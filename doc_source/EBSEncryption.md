# Amazon EBS encryption<a name="EBSEncryption"></a>

Use Amazon EBS encryption as a straight\-forward encryption solution for your EBS resources associated with your EC2 instances\. With Amazon EBS encryption, you aren't required to build, maintain, and secure your own key management infrastructure\. Amazon EBS encryption uses AWS KMS keys when creating encrypted volumes and snapshots\.

Encryption operations occur on the servers that host EC2 instances, ensuring the security of both data\-at\-rest and data\-in\-transit between an instance and its attached EBS storage\.

You can attach both encrypted and unencrypted volumes to an instance simultaneously\.

**Topics**
+ [How EBS encryption works](#how-ebs-encryption-works)
+ [Requirements](#ebs-encryption-requirements)
+ [Default KMS key for EBS encryption](#EBSEncryption_key_mgmt)
+ [Encryption by default](#encryption-by-default)
+ [Encrypt EBS resources](#encryption-parameters)
+ [Encryption scenarios](#encryption-examples)
+ [Set encryption defaults using the API and CLI](#encryption-by-default-api)

## How EBS encryption works<a name="how-ebs-encryption-works"></a>

You can encrypt both the boot and data volumes of an EC2 instance\.

When you create an encrypted EBS volume and attach it to a supported instance type, the following types of data are encrypted:
+ Data at rest inside the volume
+ All data moving between the volume and the instance
+ All snapshots created from the volume
+ All volumes created from those snapshots

EBS encrypts your volume with a data key using the industry\-standard AES\-256 algorithm\. Your data key is stored on disk with your encrypted data, but not before EBS encrypts it with your KMS key\. Your data key never appears on disk in plaintext\. The same data key is shared by snapshots of the volume and any subsequent volumes created from those snapshots\. For more information, see [Data keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) in the *AWS Key Management Service Developer Guide*\.

Amazon EC2 works with AWS KMS to encrypt and decrypt your EBS volumes in slightly different ways depending on whether the snapshot from which you create an encrypted volume is encrypted or unencrypted\.

### How EBS encryption works when the snapshot is encrypted<a name="how-ebs-encryption-works-encrypted-snapshot"></a>

When you create an encrypted volume from an encrypted snapshot that you own, Amazon EC2 works with AWS KMS to encrypt and decrypt your EBS volumes as follows:

1. Amazon EC2 sends a [GenerateDataKeyWithoutPlaintext](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html) request to AWS KMS, specifying the KMS key that you chose for volume encryption\.

1. AWS KMS generates a new data key, encrypts it under the KMS key that you chose for volume encryption, and sends the encrypted data key to Amazon EBS to be stored with the volume metadata\.

1. When you attach the encrypted volume to an instance, Amazon EC2 sends a [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) request to AWS KMS so that it can decrypt the data key\.

1. AWS KMS decrypts the encrypted data key and sends the decrypted data key to Amazon EC2\.

1. Amazon EC2 uses the plaintext data key in hypervisor memory to encrypt disk I/O to the volume\. The plaintext data key persists in memory as long as the volume is attached to the instance\.

### How EBS encryption works when the snapshot is unencrypted<a name="how-ebs-encryption-works-unencrypted-snapshot"></a>

When you create an encrypted volume from unencrypted snapshot, Amazon EC2 works with AWS KMS to encrypt and decrypt your EBS volumes as follows:

1. Amazon EC2 sends a [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) request to AWS KMS, so that it can encrypt the volume that is created from the snapshot\.

1. Amazon EC2 sends a [GenerateDataKeyWithoutPlaintext](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html) request to AWS KMS, specifying the KMS key that you chose for volume encryption\.

1. AWS KMS generates a new data key, encrypts it under the KMS key that you chose for volume encryption, and sends the encrypted data key to Amazon EBS to be stored with the volume metadata\.

1. Amazon EC2 sends a [Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) request to AWS KMS to get the encryption key to encrypt the volume data\.

1. When you attach the encrypted volume to an instance, Amazon EC2 sends a [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) request to AWS KMS, so that it can decrypt the data key\.

1. When you attach the encrypted volume to an instance, Amazon EC2 sends a [Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) request to AWS KMS, specifying the encrypted data key\.

1. AWS KMS decrypts the encrypted data key and sends the decrypted data key to Amazon EC2\.

1. Amazon EC2 uses the plaintext data key in hypervisor memory to encrypt disk I/O to the volume\. The plaintext data key persists in memory as long as the volume is attached to the instance\.

For more information, see [How Amazon Elastic Block Store \(Amazon EBS\) uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html) and [Amazon EC2 example two](https://docs.aws.amazon.com/kms/latest/developerguide/ct-ec2two.html) in the *AWS Key Management Service Developer Guide*\.

## Requirements<a name="ebs-encryption-requirements"></a>

Before you begin, verify that the following requirements are met\.

### Supported volume types<a name="ebs-encryption-volume-types"></a>

Encryption is supported by all EBS volume types\. You can expect the same IOPS performance on encrypted volumes as on unencrypted volumes, with a minimal effect on latency\. You can access encrypted volumes the same way that you access unencrypted volumes\. Encryption and decryption are handled transparently, and they require no additional action from you or your applications\.

### Supported instance types<a name="EBSEncryption_supported_instances"></a>

Amazon EBS encryption is available on all [current generation](instance-types.md#current-gen-instances) instance types and the following [previous generation](instance-types.md#previous-gen-instances) instance types: A1, C3, `cr1.8xlarge`, G2, I2, M3, and R3\.

### Permissions for IAM users<a name="ebs-encryption-permissions"></a>

When you configure a KMS key as the default key for EBS encryption, the default KMS key policy allows any IAM user with access to the required KMS actions to use this KMS key to encrypt or decrypt EBS resources\. You must grant IAM users permission to call the following actions in order to use EBS encryption:
+ `kms:CreateGrant`
+ `kms:Decrypt`
+ `kms:DescribeKey`
+ `kms:GenerateDataKeyWithoutPlainText`
+ `kms:ReEncrypt`

To follow the principle of least privilege, do not allow full access to `kms:CreateGrant`\. Instead, allow the user to create grants on the KMS key only when the grant is created on the user's behalf by an AWS service, as shown in the following example\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "kms:CreateGrant",
            "Resource": [
                "arn:aws:kms:us-east-2:123456789012:key/abcd1234-a123-456d-a12b-a123b4cd56ef"
            ],
            "Condition": {
                "Bool": {
                    "kms:GrantIsForAWSResource": true
                }
            }
        }
    ]
}
```

For more information, see [Allows access to the AWS account and enables IAM policies](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-root-enable-iam) in the **Default key policy** section in the *AWS Key Management Service Developer Guide*\.

## Default KMS key for EBS encryption<a name="EBSEncryption_key_mgmt"></a>

Amazon EBS automatically creates a unique AWS managed key in each Region where you store AWS resources\. This KMS key has the alias `alias/aws/ebs`\. By default, Amazon EBS uses this KMS key for encryption\. Alternatively, you can specify a symmetric customer managed key that you created as the default KMS key for EBS encryption\. Using your own KMS key gives you more flexibility, including the ability to create, rotate, and disable KMS keys\. 

**Important**  
Amazon EBS does not support asymmetric KMS keys\. For more information, see [Using symmetric and asymmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

------
#### [ New console ]

**To configure the default KMS key for EBS encryption for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. From the navigation pane, select **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account Attributes**, **EBS encryption**\.

1. Choose **Manage**\.

1. For **Default encryption key**, choose a symmetric customer managed key\.

1. Choose **Update EBS encryption**\.

------
#### [ Old console ]

**To configure the default KMS key for EBS encryption for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. From the navigation pane, select **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account Attributes**, **Settings**\.

1. Choose **Change the default key** and then choose an available KMS key\.

1. Choose **Save settings**\.

------

## Encryption by default<a name="encryption-by-default"></a>

You can configure your AWS account to enforce the encryption of the new EBS volumes and snapshot copies that you create\. For example, Amazon EBS encrypts the EBS volumes created when you launch an instance and the snapshots that you copy from an unencrypted snapshot\. For examples of transitioning from unencrypted to encrypted EBS resources, see [Encrypt unencrypted resources](#encrypt-unencrypted)\.

Encryption by default has no effect on existing EBS volumes or snapshots\.

**Considerations**
+ Encryption by default is a Region\-specific setting\. If you enable it for a Region, you cannot disable it for individual volumes or snapshots in that Region\.
+ When you enable encryption by default, you can launch an instance only if the instance type supports EBS encryption\. For more information, see [Supported instance types](#EBSEncryption_supported_instances)\.
+ If you copy a snapshot and encrypt it to a new KMS key, a complete \(non\-incremental\) copy is created\. This results in additional storage costs\.
+ When migrating servers using AWS Server Migration Service \(SMS\), do not turn on encryption by default\. If encryption by default is already on and you are experiencing delta replication failures, turn off encryption by default\. Instead, enable AMI encryption when you create the replication job\.

------
#### [ New console ]

**To enable encryption by default for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. From the navigation pane, select **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account Attributes**, **EBS encryption**\.

1. Choose **Manage**\.

1. Select **Enable**\. You keep the AWS managed key with the alias `alias/aws/ebs` created on your behalf as the default encryption key, or choose a symmetric customer managed key\.

1. Choose **Update EBS encryption**\.

------
#### [ Old console ]

**To enable encryption by default for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. From the navigation pane, select **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account Attributes**, **Settings**\.

1. Under **EBS Storage**, select **Always encrypt new EBS volumes**\.

1. Choose **Save settings**\.

------

You cannot change the KMS key that is associated with an existing snapshot or encrypted volume\. However, you can associate a different KMS key during a snapshot copy operation so that the resulting copied snapshot is encrypted by the new KMS key\.

## Encrypt EBS resources<a name="encryption-parameters"></a>

You encrypt EBS volumes by enabling encryption, either using [encryption by default](#encryption-by-default) or by enabling encryption when you create a volume that you want to encrypt\.

When you encrypt a volume, you can specify the symmetric KMS key to use to encrypt the volume\. If you do not specify a KMS key, the KMS key that is used for encryption depends on the encryption state of the source snapshot and its ownership\. For more information, see the [encryption outcomes table](#ebs-volume-encryption-outcomes)\.

**Note**  
If you are using the API or AWS CLI to specify a KMS key, be aware that AWS authenticates the KMS key asynchronously\. If you specify a KMS key ID, an alias, or an ARN that is not valid, the action can appear to complete, but it eventually fails\.

You cannot change the KMS key that is associated with an existing snapshot or volume\. However, you can associate a different KMS key during a snapshot copy operation so that the resulting copied snapshot is encrypted by the new KMS key\.

### Encrypt an empty volume on creation<a name="new-encrypted-volumes"></a>

When you create a new, empty EBS volume, you can encrypt it by enabling encryption for the specific volume creation operation\. If you enabled EBS encryption by default, the volume is automatically encrypted using your default KMS key for EBS encryption\. Alternatively, you can specify a different symmetric KMS key for the specific volume creation operation\. The volume is encrypted by the time it is first available, so your data is always secured\. For detailed procedures, see [Create an Amazon EBS volume](ebs-creating-volume.md)\.

By default, the KMS key that you selected when creating a volume encrypts the snapshots that you make from the volume and the volumes that you restore from those encrypted snapshots\. You cannot remove encryption from an encrypted volume or snapshot, which means that a volume restored from an encrypted snapshot, or a copy of an encrypted snapshot, is always encrypted\.

Public snapshots of encrypted volumes are not supported, but you can share an encrypted snapshot with specific accounts\. For detailed directions, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

### Encrypt unencrypted resources<a name="encrypt-unencrypted"></a>

Although there is no direct way to encrypt an existing unencrypted volume or snapshot, you can encrypt them by creating either a volume or a snapshot\. If you enabled encryption by default, Amazon EBS encrypts the resulting new volume or snapshot using your default KMS key for EBS encryption\. Even if you have not enabled encryption by default, you can enable encryption when you create an individual volume or snapshot\. Whether you enable encryption by default or in individual creation operations, you can override the default KMS key for EBS encryption and select a symmetric customer managed key\. For more information, see [Create an Amazon EBS volume](ebs-creating-volume.md) and [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.

To encrypt the snapshot copy to a customer managed key, you must both enable encryption and specify the KMS key, as shown in [Copy an unencrypted snapshot \(encryption by default not enabled\)](#snapshot-account-off)\. 

**Important**  
Amazon EBS does not support asymmetric KMS keys\. For more information, see [Using Symmetric and Asymmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

You can also apply new encryption states when launching an instance from an EBS\-backed AMI\. This is because EBS\-backed AMIs include snapshots of EBS volumes that can be encrypted as described\. For more information, see [Use encryption with EBS\-backed AMIs](AMIEncryption.md)\.

## Encryption scenarios<a name="encryption-examples"></a>

When you create an encrypted EBS resource, it is encrypted by your account's default KMS key for EBS encryption unless you specify a different customer managed key in the volume creation parameters or the block device mapping for the AMI or instance\. For more information, see [Default KMS key for EBS encryption](#EBSEncryption_key_mgmt)\.

The following examples illustrate how you can manage the encryption state of your volumes and snapshots\. For a full list of encryption cases, see the [encryption outcomes table](#ebs-volume-encryption-outcomes)\.

**Topics**
+ [Restore an unencrypted volume \(encryption by default not enabled\)](#volume-account-off)
+ [Restore an unencrypted volume \(encryption by default enabled\)](#volume-account-on)
+ [Copy an unencrypted snapshot \(encryption by default not enabled\)](#snapshot-account-off)
+ [Copy an unencrypted snapshot \(encryption by default enabled\)](#snapshot-account-on)
+ [Re\-encrypt an encrypted volume](#reencrypt-volume)
+ [Re\-encrypt an encrypted snapshot](#reencrypt-snapshot)
+ [Migrate data between encrypted and unencrypted volumes](#migrate-data-encrypted-unencrypted)
+ [Encryption outcomes](#ebs-volume-encryption-outcomes)

### Restore an unencrypted volume \(encryption by default not enabled\)<a name="volume-account-off"></a>

Without encryption by default enabled, a volume restored from an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting volume by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. The following diagram illustrates the process\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-off.png)

If you leave out the `KmsKeyId` parameter, the resulting volume is encrypted using your default KMS key for EBS encryption\. You must specify a KMS key ID to encrypt the volume to a different KMS key\.

For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

### Restore an unencrypted volume \(encryption by default enabled\)<a name="volume-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for volumes restored from unencrypted snapshots, and no encryption parameters are required for your default KMS key to be used\. The following diagram shows this simple default case:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-on.png)

If you want to encrypt the restored volume to a symmetric customer managed key, you must supply both the `Encrypted` and `KmsKeyId` parameters as shown in [Restore an unencrypted volume \(encryption by default not enabled\)](#volume-account-off)\.

### Copy an unencrypted snapshot \(encryption by default not enabled\)<a name="snapshot-account-off"></a>

Without encryption by default enabled, a copy of an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting snapshot by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. If you omit `KmsKeyId`, the resulting snapshot is encrypted by your default KMS key\. You must specify a KMS key ID to encrypt the volume to a different symmetric KMS key\.

The following diagram illustrates the process\.

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-off.png)

You can encrypt an EBS volume by copying an unencrypted snapshot to an encrypted snapshot and then creating a volume from the encrypted snapshot\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.

### Copy an unencrypted snapshot \(encryption by default enabled\)<a name="snapshot-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for copies of unencrypted snapshots, and no encryption parameters are required if your default KMS key is used\. The following diagram illustrates this default case:

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-on.png)

### Re\-encrypt an encrypted volume<a name="reencrypt-volume"></a>

When the `CreateVolume` action operates on an encrypted snapshot, you have the option of re\-encrypting it with a different KMS key\. The following diagram illustrates the process\. In this example, you own two KMS keys, KMS key A and KMS key B\. The source snapshot is encrypted by KMS key A\. During volume creation, with the KMS key ID of KMS key B specified as a parameter, the source data is automatically decrypted, then re\-encrypted by KMS key B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new KMS key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-reencrypt.png)

For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

### Re\-encrypt an encrypted snapshot<a name="reencrypt-snapshot"></a>

The ability to encrypt a snapshot during copying allows you to apply a new symmetric KMS key to an already\-encrypted snapshot that you own\. Volumes restored from the resulting copy are only accessible using the new KMS key\. The following diagram illustrates the process\. In this example, you own two KMS keys, KMS key A and KMS key B\. The source snapshot is encrypted by KMS key A\. During copy, with the KMS key ID of KMS key B specified as a parameter, the source data is automatically re\-encrypted by KMS key B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new KMS key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snap-reencrypt.png)

In a related scenario, you can choose to apply new encryption parameters to a copy of a snapshot that has been shared with you\. By default, the copy is encrypted with a KMS key shared by the snapshot's owner\. However, we recommend that you create a copy of the shared snapshot using a different KMS key that you control\. This protects your access to the volume if the original KMS key is compromised, or if the owner revokes the KMS key for any reason\. For more information, see [Encryption and snapshot copying](ebs-copy-snapshot.md#creating-encrypted-snapshots)\.

### Migrate data between encrypted and unencrypted volumes<a name="migrate-data-encrypted-unencrypted"></a>

When you have access to both an encrypted and unencrypted volume, you can freely transfer data between them\. EC2 carries out the encryption and decryption operations transparently\.

For example, use the rsync command to copy the data\. In the following command, the source data is located in `/mnt/source` and the destination volume is mounted at `/mnt/destination`\.

```
[ec2-user ~]$ sudo rsync -avh --progress /mnt/source/ /mnt/destination/
```

### Encryption outcomes<a name="ebs-volume-encryption-outcomes"></a>



The following table describes the encryption outcome for each possible combination of settings\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)

\* This is the default CMK used for EBS encryption for the AWS account and Region\. By default this is a unique AWS managed CMK for EBS, or you can specify a customer managed CMK\. For more information, see [Default KMS key for EBS encryption](#EBSEncryption_key_mgmt)\.

\*\* This is a customer managed CMK specified for the volume at launch time\. This CMK is used instead of the default CMK for the AWS account and Region\.

## Set encryption defaults using the API and CLI<a name="encryption-by-default-api"></a>

You can manage encryption by default and the default KMS key using the following API actions and CLI commands\.


| API action  | CLI command | Description | 
| --- | --- | --- | 
|  [DisableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DisableEbsEncryptionByDefault.html)  | [disable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-ebs-encryption-by-default.html) |  Disables encryption by default\.  | 
|  [EnableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableEbsEncryptionByDefault.html)  | [enable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-ebs-encryption-by-default.html) |  Enables encryption by default\.  | 
|  [GetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsDefaultKmsKeyId.html)  | [get\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-default-kms-key-id.html) |  Describes the default KMS key\.  | 
|  [GetEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsEncryptionByDefault.html)  | [get\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-encryption-by-default.html) |  Indicates whether encryption by default is enabled\.  | 
|  [ModifyEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyEbsDefaultKmsKeyId.html)  | [modify\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-ebs-default-kms-key-id.html) |  Changes the default KMS key used to encrypt EBS volumes\.  | 
|  [ResetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ResetEbsDefaultKmsKeyId.html)  | [reset\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/reset-ebs-default-kms-key-id.html) |  Resets the AWS managed key as the default KMS key used to encrypt EBS volumes\.  | 
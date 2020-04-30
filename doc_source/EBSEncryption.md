# Amazon EBS encryption<a name="EBSEncryption"></a>

Amazon EBS encryption offers a straight\-forward encryption solution for your EBS resources that doesn't require you to build, maintain, and secure your own key management infrastructure\. It uses AWS Key Management Service \(AWS KMS\) customer master keys \(CMK\) when creating encrypted volumes and snapshots\.

Encryption operations occur on the servers that host EC2 instances, ensuring the security of both data\-at\-rest and data\-in\-transit between an instance and its attached EBS storage\.

**Topics**
+ [How EBS encryption works](#how-ebs-encryption-works)
+ [Requirements](#ebs-encryption-requirements)
+ [Default key for EBS encryption](#EBSEncryption_key_mgmt)
+ [Encryption by default](#encryption-by-default)
+ [Encrypting EBS resources](#encryption-parameters)
+ [Encryption scenarios](#encryption-examples)
+ [Setting encryption defaults using the API and CLI](#encryption-by-default-api)

## How EBS encryption works<a name="how-ebs-encryption-works"></a>

You can encrypt both the boot and data volumes of an EC2 instance\. When you create an encrypted EBS volume and attach it to a supported instance type, the following types of data are encrypted:
+ Data at rest inside the volume
+ All data moving between the volume and the instance
+ All snapshots created from the volume
+ All volumes created from those snapshots

EBS encrypts your volume with a data key using the industry\-standard AES\-256 algorithm\. Your data key is stored on\-disk with your encrypted data, but not before EBS encrypts it with your CMK\. Your data key never appears on disk in plaintext\. The same data key is shared by snapshots of the volume and any subsequent volumes created from those snapshots\. For more information, see [Data Keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) in the *AWS Key Management Service Developer Guide*\.

Amazon EBS works with AWS KMS to encrypt and decrypt your EBS volumes as follows:

1. Amazon EBS sends a [CreateGrant](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateGrant.html) request to AWS KMS, so that it can decrypt the data key\.

1. Amazon EBS sends a [GenerateDataKeyWithoutPlaintext](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKeyWithoutPlaintext.html) request to AWS KMS, specifying the CMK to use to encrypt the volume\.

1. AWS KMS generates a new data key, encrypts it under the specified CMK, and sends the encrypted data key to Amazon EBS to be stored with the volume metadata\.

1. When you attach an encrypted volume to an instance, Amazon EC2 sends a [Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html) request to AWS KMS, specifying the encrypted data key\.

1. AWS KMS decrypts the encrypted data key and sends the decrypted data key to Amazon EC2\.

1. Amazon EC2 uses the plaintext data key in hypervisor memory to encrypt disk I/O to the volume\. The plaintext data key persists in memory as long as the volume is attached to the instance\.

For more information, see [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html) and [AWS KMS Log File Entries](https://docs.aws.amazon.com/kms/latest/developerguide/ct-ec2two.html) in the *AWS Key Management Service Developer Guide*\.

## Requirements<a name="ebs-encryption-requirements"></a>

Before you begin, verify that the following requirements are met\.

### Supported volume types<a name="ebs-encryption-volume-types"></a>

Encryption is supported by all EBS volume types\. You can expect the same IOPS performance on encrypted volumes as on unencrypted volumes, with a minimal effect on latency\. You can access encrypted volumes the same way that you access unencrypted volumes\. Encryption and decryption are handled transparently, and they require no additional action from you or your applications\.

### Supported instance types<a name="EBSEncryption_supported_instances"></a>

Amazon EBS encryption is available on the instance types listed below\. You can attach both encrypted and unencrypted volumes to these instance types simultaneously\.
+ General purpose: A1, M3, M4, M5, M5a, M5ad, M5d, M5dn, M5n, T2, T3, and T3a
+ Compute optimized: C3, C4, C5, C5d, C5n
+ Memory optimized: `cr1.8xlarge`, R3, R4, R5, R5a, R5ad, R5d, R5dn, R5n, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, `u-24tb1.metal`, X1, X1e, and z1d
+ Storage optimized: D2, `h1.2xlarge`, `h1.4xlarge`, I2, I3, and I3en
+ Accelerated computing: F1, G2, G3, G4, Inf1, P2, and P3

### Permissions for IAM users<a name="ebs-encryption-permissions"></a>

When you configure a CMK as the default key for EBS encryption, the default key policy allows any IAM user with access to the required KMS actions to use this key to encrypt or decrypt EBS resources\. You must grant IAM users permission to call the following actions in order to use EBS encryption:
+ `kms:CreateGrant`
+ `kms:Decrypt`
+ `kms:DescribeKey`
+ `kms:GenerateDataKeyWithoutPlainText`
+ `kms:ReEncrypt`

To follow the principal of least privilege, do not allow full access to `kms:CreateGrant`\. Instead, allow the user to create grants on the CMK only when the grant is created on the user's behalf by an AWS service, as shown in the following example:

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

For more information, see [Default Key Policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-default-allow-root-enable-iam) in the *AWS Key Management Service Developer Guide*\.

## Default key for EBS encryption<a name="EBSEncryption_key_mgmt"></a>

Amazon EBS automatically creates a unique AWS managed CMK in each Region where you store AWS resources\. This key has the alias `alias/aws/ebs`\. By default, Amazon EBS uses this key for encryption\. Alternatively, you can specify a symmetric customer managed CMK that you created as the default key for EBS encryption\. Using your own CMK gives you more flexibility, including the ability to create, rotate, and disable keys\. 

**Important**  
Amazon EBS does not support asymmetric CMKs\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

**To configure the default key for EBS encryption for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. Choose **Account Attributes**, **Settings**\.

1. Choose **Change the default key** and then choose an available key\.

1. Choose **Update**\.

## Encryption by default<a name="encryption-by-default"></a>

You can configure your AWS account to enforce the encryption of the new EBS volumes and snapshot copies that you create\. For example, Amazon EBS encrypts the EBS volumes created when you launch an instance and the snapshots that you copy from an unencrypted snapshot\. For examples of transitioning from unencrypted to encrypted EBS resources, see [Encrypting unencrypted resources](#encrypt-unencrypted)\.

Encryption by default has no effect on existing EBS volumes or snapshots\.

**Considerations**
+ Encryption by default is a Region\-specific setting\. If you enable it for a Region, you cannot disable it for individual volumes or snapshots in that Region\.
+ When you enable encryption by default, you can launch an instance only if the instance type supports EBS encryption\. For more information, see [Supported instance types](#EBSEncryption_supported_instances)\.
+ When migrating servers using AWS Server Migration Service \(SMS\), do not turn on encryption by default\. If encryption by default is already on and you are experiencing delta replication failures, turn off encryption by default\. Instead, enable AMI encryption when you create the replication job\.

**To enable encryption by default for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. From the navigation pane, select **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account Attributes**, **Settings**\.

1. Under **EBS Storage**, select **Always encrypt new EBS volumes**\.

1. Choose **Update**\.

You cannot change the CMK that is associated with an existing snapshot or encrypted volume\. However, you can associate a different CMK during a snapshot copy operation so that the resulting copied snapshot is encrypted by the new CMK\.

## Encrypting EBS resources<a name="encryption-parameters"></a>

You encrypt EBS volumes by enabling encryption, either using [encryption by default](#encryption-by-default) or by enabling encryption when you create a volume that you want to encrypt\.

When you encrypt a volume, you can specify the symmetric CMK to use to encrypt the volume\. If you do not specify a CMK, the key that is used for encryption depends on the encryption state of the source snapshot and its ownership\. For more information, see the [encryption outcomes table](#ebs-volume-encryption-outcomes)\.

You cannot change the CMK that is associated with an existing snapshot or volume\. However, you can associate a different CMK during a snapshot copy operation so that the resulting copied snapshot is encrypted by the new CMK\.

### Encrypting an empty volume on creation<a name="new-encrypted-volumes"></a>

When you create a new, empty EBS volume, you can encrypt it by enabling encryption for the specific volume creation operation\. If you enabled EBS encryption by default, the volume is automatically encrypted\. By default, the volume is encrypted to your default key for EBS encryption\. Alternatively, you can specify a different symmetric CMK for the specific volume creation operation\. The volume is encrypted by the time it is first available, so your data is always secured\. For detailed procedures, see [Creating an Amazon EBS volume](ebs-creating-volume.md)\.

By default, the CMK that you selected when creating a volume encrypts the snapshots that you make from the volume and the volumes that you restore from those encrypted snapshots\. You cannot remove encryption from an encrypted volume or snapshot, which means that a volume restored from an encrypted snapshot, or a copy of an encrypted snapshot, is always encrypted\.

Public snapshots of encrypted volumes are not supported, but you can share an encrypted snapshot with specific accounts\. For detailed directions, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

### Encrypting unencrypted resources<a name="encrypt-unencrypted"></a>

Although there is no direct way to encrypt an existing unencrypted volume or snapshot, you can encrypt them by creating either a volume or a snapshot\. If you enabled encryption by default, Amazon EBS encrypts the resulting new volume or snapshot using your default key for EBS encryption\. Even if you have not enabled encryption by default, you can enable encryption when you create an individual volume or snapshot\. Whether you enable encryption by default or in individual creation operations, you can override the default key for EBS encryption and select a symmetric customer managed CMK\. For more information, see [Creating an Amazon EBS volume](ebs-creating-volume.md) and [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

To encrypt the snapshot copy to a customer managed CMK, you must both enable encryption and specify the key, as shown in [Copy an unencrypted snapshot \(encryption by default not enabled\)](#snapshot-account-off)\. 

**Important**  
Amazon EBS does not support asymmetric CMKs\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

You can also apply new encryption states when launching an instance from an EBS\-backed AMI\. This is because EBS\-backed AMIs include snapshots of EBS volumes that can be encrypted as described\. For more information, see [Using encryption with EBS\-backed AMIs](AMIEncryption.md)\.

## Encryption scenarios<a name="encryption-examples"></a>

When you create an encrypted EBS resource, it is encrypted by your account's default key for EBS encryption unless you specify a different customer managed CMK in the volume creation parameters or the block device mapping for the AMI or instance\. For more information, see [Default key for EBS encryption](#EBSEncryption_key_mgmt)\.

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

If you leave out the `KmsKeyId` parameter, the resulting volume is encrypted using your default key for EBS encryption\. You must specify a key ID to encrypt the volume to a different CMK\.

For more information, see [Restoring an Amazon EBS volume from a snapshot](ebs-restoring-volume.md)\.

### Restore an unencrypted volume \(encryption by default enabled\)<a name="volume-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for volumes restored from unencrypted snapshots, and no encryption parameters are required for your default CMK to be used\. The following diagram shows this simple default case:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-on.png)

If you want to encrypt the restored volume to a symmetric customer managed CMK, you must supply both the `Encrypted` and `KmsKeyId` parameters as shown in [Restore an unencrypted volume \(encryption by default not enabled\)](#volume-account-off)\.

### Copy an unencrypted snapshot \(encryption by default not enabled\)<a name="snapshot-account-off"></a>

Without encryption by default enabled, a copy of an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting snapshot by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. If you omit `KmsKeyId`, the resulting snapshot is encrypted by your default CMK\. You must specify a key ID to encrypt the volume to a different symmetric CMK\.

The following diagram illustrates the process\.

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-off.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

You can encrypt an EBS volume by copying an unencrypted snapshot to an encrypted snapshot and then creating a volume from the encrypted snapshot\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

### Copy an unencrypted snapshot \(encryption by default enabled\)<a name="snapshot-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for copies of unencrypted snapshots, and no encryption parameters are required if your default CMK is used\. The following diagram illustrates this default case:

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-on.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

### Re\-encrypt an encrypted volume<a name="reencrypt-volume"></a>

When the `CreateVolume` action operates on an encrypted snapshot, you have the option of re\-encrypting it with a different CMK\. The following diagram illustrates the process\. In this example, you own two CMKs, CMK A and CMK B\. The source snapshot is encrypted by CMK A\. During volume creation, with the key ID of CMK B specified as a parameter, the source data is automatically decrypted, then re\-encrypted by CMK B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-reencrypt.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

For more information, see [Restoring an Amazon EBS volume from a snapshot](ebs-restoring-volume.md)\.

### Re\-encrypt an encrypted snapshot<a name="reencrypt-snapshot"></a>

The ability to encrypt a snapshot during copying allows you to apply a new symmetric CMK to an already\-encrypted snapshot that you own\. Volumes restored from the resulting copy are only accessible using the new CMK\. The following diagram illustrates the process\. In this example, you own two CMKs, CMK A and CMK B\. The source snapshot is encrypted by CMK A\. During copy, with the key ID of CMK B specified as a parameter, the source data is automatically re\-encrypted by CMK B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snap-reencrypt.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

In a related scenario, you can choose to apply new encryption parameters to a copy of a snapshot that has been shared with you\. By default, the copy is encrypted with a CMK shared by the snapshot's owner\. However, we recommend that you create a copy of the shared snapshot using a different CMK that you control\. This protects your access to the volume if the original CMK is compromised, or if the owner revokes the CMK for any reason\. For more information, see [Encryption and Snapshot Copying](ebs-copy-snapshot.md#creating-encrypted-snapshots)\.

### Migrate data between encrypted and unencrypted volumes<a name="migrate-data-encrypted-unencrypted"></a>

When you have access to both an encrypted and unencrypted volume, you can freely transfer data between them\. EC2 carries out the encryption and decryption operations transparently\.

For example, use the rsync command to copy the data\. In the following command, the source data is located in `/mnt/source` and the destination volume is mounted at `/mnt/destination`\.

```
[ec2-user ~]$ sudo rsync -avh --progress /mnt/source/ /mnt/destination/
```

### Encryption outcomes<a name="ebs-volume-encryption-outcomes"></a>

The following table describes the encryption outcome for each possible combination of settings\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)

\* This is the default CMK used for EBS encryption for the AWS account and Region\. By default this is a unique AWS managed CMK for EBS, or you can specify a customer managed CMK\. For more information, see [Default key for EBS encryption](#EBSEncryption_key_mgmt)\.

\*\* This is a customer managed CMK specified for the volume at launch time\. This CMK is used instead of the default CMK for the AWS account and Region\.

## Setting encryption defaults using the API and CLI<a name="encryption-by-default-api"></a>

You can manage encryption by default and the default customer master key \(CMK\) using the following API actions and CLI commands\.


| API action  | CLI command | Description | 
| --- | --- | --- | 
|  [DisableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DisableEbsEncryptionByDefault.html)  | [disable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-ebs-encryption-by-default.html) |  Disables encryption by default\.  | 
|  [EnableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableEbsEncryptionByDefault.html)  | [enable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-ebs-encryption-by-default.html) |  Enables encryption by default\.  | 
|  [GetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsDefaultKmsKeyId.html)  | [get\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-default-kms-key-id.html) |  Describes the default CMK\.  | 
|  [GetEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsEncryptionByDefault.html)  | [get\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-encryption-by-default.html) |  Indicates whether encryption by default is enabled\.  | 
|  [ModifyEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyEbsDefaultKmsKeyId.html)  | [modify\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-ebs-default-kms-key-id.html) |  Changes the default CMK used to encrypt EBS volumes\.  | 
|  [ResetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ResetEbsDefaultKmsKeyId.html)  | [reset\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/reset-ebs-default-kms-key-id.html) |  Resets the AWS managed default CMK as the default CMK used to encrypt EBS volumes\.  | 
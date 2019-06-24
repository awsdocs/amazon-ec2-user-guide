# Amazon EBS Encryption<a name="EBSEncryption"></a>

Amazon EBS encryption offers a straight\-forward encryption solution for your EBS volumes that doesn't require you to build, maintain, and secure your own key management infrastructure\. It uses AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\) when creating encrypted volumes and snapshots\. For more information, see [Customer Master Keys \(CMK\)](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) in the *AWS Key Management Service Developer Guide*\.

When you create an encrypted EBS volume and attach it to a supported instance type, the following types of data are encrypted:
+ Data at rest inside the volume
+ All data moving between the volume and the instance
+ All snapshots created from the volume
+ All volumes created from those snapshots

You can encrypt both the boot and data volumes of an EC2 instance\.

Encryption operations occur on the servers that host EC2 instances, ensuring the security of both data\-at\-rest and data\-in\-transit between an instance and its attached EBS storage\.

Public snapshots of encrypted volumes are not supported, but you can share an encrypted snapshot with specific accounts\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

**Topics**
+ [Supported Volume Types](#ebs-encryption-volume-types)
+ [Supported Instance Types](#EBSEncryption_supported_instances)
+ [Encryption by Default](#encryption-by-default)
+ [Encryption Key Management](#EBSEncryption_key_mgmt)
+ [Encryption Parameters for EBS Volumes](#encryption-parameters)
+ [Examples of Encryption Outcomes](#encryption-examples)
+ [Setting Encryption and Key Defaults Using the API and CLI](#encryption-by-default-api)

## Supported Volume Types<a name="ebs-encryption-volume-types"></a>

Encryption is supported by all EBS volume types \(General Purpose SSD \[`gp2`\], Provisioned IOPS SSD \[`io1`\], Throughput Optimized HDD \[`st1`\], Cold HDD \[`sc1`\], and Magnetic \[`standard`\]\)\. You can expect the same IOPS performance on encrypted volumes as on unencrypted volumes, with a minimal effect on latency\. You can access encrypted volumes the same way that you access unencrypted volumes\. Encryption and decryption are handled transparently and they require no additional action from you or your applications\.

## Supported Instance Types<a name="EBSEncryption_supported_instances"></a>

Amazon EBS encryption is available on the instance types listed below\. You can attach both encrypted and unencrypted volumes to these instance types simultaneously\.
+ General purpose: A1, M3, M4, M5, M5a, M5ad, M5d, T2, T3, and T3a
+ Compute optimized: C3, C4, C5, C5d, and C5n
+ Memory optimized: `cr1.8xlarge`, R3, R4, R5, R5a, R5ad, R5d, X1, X1e, and z1d
+ Storage optimized: D2, `h1.2xlarge`, `h1.4xlarge`, I2, and I3
+ Accelerated computing: F1, G2, G3, P2, and P3
+ Bare metal: `c5.metal`, `i3.metal`, `m5.metal`, `m5d.metal`, `r5.metal`, `r5d.metal`, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and `z1d.metal`

## Encryption by Default<a name="encryption-by-default"></a>

You can configure your AWS account to enforce the encryption of your EBS volumes and snapshots\. Activating encryption by default has two effects:
+ AWS encrypts new EBS volumes on launch\.
+ AWS encrypts new copies of unencrypted snapshots\.

Encryption by default is a Region\-specific setting\. If you enable it for a Region, you cannot disable it for individual volumes or snapshots in that Region\.

Newly created EBS resources are encrypted by your account's default customer master key \(CMK\) unless you specify a customer managed CMK in the EC2 settings or at launch\. For more information, see [Encryption Key Management](#EBSEncryption_key_mgmt)\.

Encryption by default has no effect on existing EBS volumes or snapshots, but when you copy unencrypted snapshots, or restore unencrypted volumes, the resulting snapshots or volumes are encrypted\. For examples of transitioning from unencrypted to encrypted EBS resources, see [Encrypting Unencrypted Resources](#encrypt-unencrypted)\.

When you enable encryption by default, you can launch an Amazon EC2 instance *only* if the instance type supports EBS encryption\. For more information, see [Supported Instance Types](#EBSEncryption_supported_instances)\.

**To enable encryption by default for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. Choose **Account Attributes**, **Settings**\.

1. Under **EBS Storage**, select **Always encrypt new EBS volumes**\.

1. Choose **Update**\.

## Encryption Key Management<a name="EBSEncryption_key_mgmt"></a>

Amazon EBS creates a unique AWS managed CMK, with the alias `alias/aws/ebs`, automatically in each Region where you store AWS resources\. By default, Amazon EBS uses this key for encryption\. Alternatively, you can specify a customer managed CMK that you created as the default key for encryption\.

**Note**  
Creating your own CMK gives you more flexibility, including the ability to create, rotate, and disable keys to define access controls\.

You cannot change the CMK that is associated with an existing snapshot or encrypted volume\. However, you can associate a different CMK during a snapshot copy operation so that the resulting copied snapshot is encrypted by the new CMK\.

EBS encrypts your volume with a data key using the industry\-standard AES\-256 algorithm\. Your data key is stored on\-disk with your encrypted data, but not before EBS encrypts it with your CMK; it never appears on disk in plaintext\. The same data key is shared by snapshots of the volume and any subsequent volumes created from those snapshots\. For more information, see [Data Keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) in the *AWS Key Management Service Developer Guide*\.

**Prerequisite**  
When you configure a CMK as the default for EBS encryption, you must also give your users access to a KMS key policy that allows the CMK to be used to launch instances, create volumes, copy snapshots, and copy images\. These permissions include the following: `GenerateDataKeyWithoutPlainText`, `Reencrypt*`, `CreateGrant`, `DescribeKey`, and `Decrypt`\. For more information, see [Authentication and Access Control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html) and [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html)\.

**To configure the default CMK for EBS encryption for a Region**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region\.

1. Choose **Account Attributes**, **Settings**\.

1. Choose **Change the default key** and then choose an available key\.

1. Choose **Update**\.

For more information about key management and key access permissions, see [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html) and [Authentication and Access Control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html) in the *AWS Key Management Service Developer Guide*\.

## Encryption Parameters for EBS Volumes<a name="encryption-parameters"></a>

You apply encryption to EBS volumes by setting the `Encrypted` parameter to `true`\. \(The `Encrypted` parameter is optional if [encryption by default](#encryption-by-default) is enabled\)\.

Optionally, you can use `KmsKeyId` to specify a custom key to use to encrypt the volume\. \(The `Encrypted` parameter must also be set to `true`, even if encryption by default is enabled\.\) If `KmsKeyId` is not specified, the key that is used for encryption depends on the encryption state of the source snapshot and its ownership\.

The following table describes the encryption outcome for each possible combination of settings\.


**Encryption Outcomes**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)

\* This is the default CMK used for EBS encryption for the AWS account and Region\. By default this is a unique AWS managed CMK for EBS, or you can specify a customer managed CMK\. For more information, see [Encryption Key Management](#EBSEncryption_key_mgmt)\.

\*\* This is a customer managed CMK specified for the volume at launch time\. This CMK is used instead of the default CMK for the AWS account and Region\.

### Creating New Empty Volumes with Encryption<a name="new-encrypted-volumes"></a>

When you create a new, empty EBS volume, you can encrypt it to your default CMK by setting the `Encrypted` flag\. To encrypt the volume to a customer managed CMK, you must provide a value for `KmsKeyId` as well\. The volume is encrypted from the time it is first available, so your data is always secured\. For detailed procedures, see [Creating an Amazon EBS Volume](ebs-creating-volume.md)\.

By default, the same CMK that you selected when creating the volume encrypts the snapshots that you make from it and the volumes that you restore from those snapshots\. You cannot remove encryption from an encrypted volume or snapshot, which means that a volume restored from an encrypted snapshot, or a copy of an encrypted snapshot, is *always* encrypted\. 

### Encrypting Unencrypted Resources<a name="encrypt-unencrypted"></a>

Although there is no direct way to encrypt an existing unencrypted volume or snapshot, you can encrypt existing unencrypted data by using either the `CreateVolume` or `CopySnapshot` action\. If you have enabled encryption by default, AWS enforces encryption of the resulting new volume or snapshot using your default CMK\. Even if you have not enabled encryption by default, you can supply encryption parameters with `CreateVolume` or `CopySnapshot` to encrypt resources individually\. In either case, you can override encryption defaults to apply a customer managed CMK\. All of the actions shown can be performed with the EC2 console, AWS CLI, or AWS API\. For more information, see [Creating an Amazon EBS Volume](ebs-creating-volume.md) and [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\. 

To encrypt the snapshot copy to a customer managed CMK, you must supply both the `Encrypted` and `KmsKeyId` parameters as shown in [Copy an Unencrypted Snapshot \(Encryption by Default Not Enabled\)](#snapshot-account-off)\.

You can also apply new encryption states when launching an instance from an EBS\-backed AMI\. This is because EBS\-backed AMIs include snapshots of EBS volumes that can be manipulated as described\. For more information about encryption options while launching an instance from an EBS\-backed AMI, see [Using Encryption with EBS\-Backed AMIs](AMIEncryption.md)\.

## Examples of Encryption Outcomes<a name="encryption-examples"></a>

The following examples illustrate how these actions and the encryption parameters can be used to manage the encryption of your volumes and snapshots\. For a full list of encryption cases, see the [encryption outcomes table](#encryption-parameters)\.

**Topics**
+ [Restore an Unencrypted Volume \(Encryption by Default Not Enabled\)](#volume-account-off)
+ [Restore an Unencrypted Volume \(Encryption by Default Enabled\)](#volume-account-on)
+ [Copy an Unencrypted Snapshot \(Encryption by Default Not Enabled\)](#snapshot-account-off)
+ [Copy an Unencrypted Snapshot \(Encryption by Default Enabled\)](#snapshot-account-on)
+ [Re\-Encrypt an Encrypted Volume](#reencrypt-volume)
+ [Re\-Encrypt an Encrypted Snapshot](#reencrypt-snapshot)
+ [Migrate Data between Encrypted and Unencrypted Volumes](#migrate-data-encrypted-unencrypted)

### Restore an Unencrypted Volume \(Encryption by Default Not Enabled\)<a name="volume-account-off"></a>

Without encryption by default enabled, a volume restored from an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting volume by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. The following diagram illustrates the process\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-off.png)

If you leave out the `KmsKeyId` parameter, the resulting volume is encrypted your default CMK\. You must supply a key ID to encrypt the volume to a different CMK\.

For more information, see [Restoring an Amazon EBS Volume from a Snapshot](ebs-restoring-volume.md)\.

### Restore an Unencrypted Volume \(Encryption by Default Enabled\)<a name="volume-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for volumes restored from unencrypted snapshots, and no encryption parameters are required for your default CMK to be used\. The following diagram shows this simple default case:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-on.png)

If you want to encrypt the restored volume to a customer managed CMK, you must supply both the `Encrypted` and `KmsKeyId` parameters as shown in [Restore an Unencrypted Volume \(Encryption by Default Not Enabled\)](#volume-account-off)\.

### Copy an Unencrypted Snapshot \(Encryption by Default Not Enabled\)<a name="snapshot-account-off"></a>

Without encryption by default enabled, a copy of an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting snapshot by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. If you omit `KmsKeyId`, the resulting snapshot is encrypted by your default CMK\. You must specify a key ID to encrypt the volume to a different CMK\.

The following diagram illustrates the process\.

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-off.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

You can encrypt an EBS volume by copying an unexpected snapshot to an encrypted snapshot and then creating a volume from the encrypted snapshot\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

### Copy an Unencrypted Snapshot \(Encryption by Default Enabled\)<a name="snapshot-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for copies of unencrypted snapshots, and no encryption parameters are required if your default CMK is used\. The following diagram illustrates this default case:

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-on.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

### Re\-Encrypt an Encrypted Volume<a name="reencrypt-volume"></a>

When the `CreateVolume` action operates on an encrypted snapshot, you have the option of re\-encrypting it with a different CMK\. The following diagram illustrates the process\. You own two CMKs, CMK A and CMK B\. The source snapshot is encrypted by CMK A\. During volume creation, with the key ID of CMK B supplied as a parameter, the source data is automatically decrypted, then re\-encrypted by CMK B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-reencrypt.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

For more information, see [Restoring an Amazon EBS Volume from a Snapshot](ebs-restoring-volume.md)\.

### Re\-Encrypt an Encrypted Snapshot<a name="reencrypt-snapshot"></a>

The ability to encrypt a snapshot during copying allows you to apply a new CMK to an already\-encrypted snapshot that you own\. Volumes restored from the resulting copy are only accessible using the new CMK\. The following diagram illustrates the process\. You own two CMKs, CMK A and CMK B\. The source snapshot is encrypted by CMK A\. During copy, with the key ID of CMK B supplied as a parameter, the source data is automatically re\-encrypted by CMK B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snap-reencrypt.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

In a related scenario, you can choose to apply new encryption parameters to a copy of a snapshot that has been shared with you\. By default, the copy is encrypted with a CMK shared by the snapshot's owner\. However, we recommend that you create a copy of the shared snapshot using a different CMK that you control\. This protects your access to the volume if the original CMK is compromised, or if the owner revokes the CMK for any reason\. For more information, see [Encryption and Snapshot Copying](ebs-copy-snapshot.md#creating-encrypted-snapshots)\.

### Migrate Data between Encrypted and Unencrypted Volumes<a name="migrate-data-encrypted-unencrypted"></a>

When you have access to both an encrypted and unencrypted volume, you can freely transfer data between them\. EC2 carries out the encryption and decryption operations transparently\.

For example, use the rsync command to copy the data\. In the following command, the source data is located in `/mnt/source` and the destination volume is mounted at `/mnt/destination`\.

```
[ec2-user ~]$ sudo rsync -avh --progress /mnt/source/ /mnt/destination/
```

## Setting Encryption and Key Defaults Using the API and CLI<a name="encryption-by-default-api"></a>

You can manage encryption by default and the default customer master key \(CMK\) using the following API actions and CLI commands\.


| API action  | CLI command | Description | 
| --- | --- | --- | 
|  [DisableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DisableEbsEncryptionByDefault.html)  | [disable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-ebs-encryption-by-default.html) |  Disables encryption by default\.  | 
|  [EnableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableEbsEncryptionByDefault.html)  | [enable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-ebs-encryption-by-default.html) |  Enables encryption by default\.  | 
|  [GetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsDefaultKmsKeyId.html)  | [get\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-default-kms-key-id.html) |  Describes the default CMK\.  | 
|  [GetEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsEncryptionByDefault.html)  | [get\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-encryption-by-default.html) |  Indicates whether encryption by default is enabled\.  | 
|  [ModifyEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyEbsDefaultKmsKeyId.html)  | [modify\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-ebs-default-kms-key-id.html) |  Changes the default CMK used to encrypt EBS volumes\.  | 
|  [ResetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ResetEbsDefaultKmsKeyId.html)  | [reset\-ebs\-default\-kms\-key\-id](https://docs.aws.amazon.com/cli/latest/reference/ec2/reset-ebs-default-kms-key-id.html) |  Resets the AWS managed default CMK as the default CMK used to encrypt EBS volumes\.  | 
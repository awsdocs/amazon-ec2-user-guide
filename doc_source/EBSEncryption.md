# Amazon EBS Encryption<a name="EBSEncryption"></a>

Amazon EBS encryption offers a straight\-forward encryption solution for your EBS volumes that doesn't require you to build, maintain, and secure your own key management infrastructure\. When you create an encrypted EBS volume and attach it to a supported instance type, the following types of data are encrypted:
+ Data at rest inside the volume
+ All data moving between the volume and the instance
+ All snapshots created from the volume
+ All volumes created from those snapshots

Encryption operations occur on the servers that host EC2 instances, ensuring the security of both data\-at\-rest and data\-in\-transit between an instance and its attached EBS storage\. 

You can encrypt both the boot and data volumes of an EC2 instance\.

Encryption is supported by all EBS volume types \(General Purpose SSD \[`gp2`\], Provisioned IOPS SSD \[`io1`\], Throughput Optimized HDD \[`st1`\], Cold HDD \[`sc1`\], and Magnetic \[`standard`\]\)\. You can expect the same IOPS performance on encrypted volumes as on unencrypted volumes, with a minimal effect on latency\. You can access encrypted volumes the same way that you access unencrypted volumes\. Encryption and decryption are handled transparently and they require no additional action from you or your applications\.

Amazon EBS encryption is available only on certain instance types\. You can attach both encrypted and unencrypted volumes to a supported instance type\. For more information, see [Supported Instance Types](#EBSEncryption_supported_instances)\.

Public snapshots of encrypted volumes are not supported, but you can share an encrypted snapshot with specific accounts\. For more information about sharing encrypted snapshots, see [Sharing an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modifying-snapshot-permissions.html)\.

**Topics**
+ [Encryption by Default](#encryption-by-default)
+ [Encryption Key Management](#EBSEncryption_key_mgmt)
+ [Setting Encryption and Key Defaults Using the API and CLI](#encryption-by-default-api)
+ [Supported Instance Types](#EBSEncryption_supported_instances)
+ [Using Encryption Parameters with EBS Volumes](#encryption-parameters)
+ [Amazon EBS Encryption and CloudWatch Events](#ebs-cw-crypto)

## Encryption by Default<a name="encryption-by-default"></a>

You can configure your AWS account to enforce the encryption of your EBS volumes and snapshots\. Activating encryption by default has two effects:
+ AWS encrypts new EBS volumes on launch\.
+ AWS encrypts new copies of unencrypted snapshots\.

Encryption by default is a Region\-specific setting\. If you enable it for a Region, you cannot disable it for individual volumes or snapshots in that Region\.

Newly created EBS resources are encrypted to your account's default customer master key \(CMK\) unless you specify a custom CMK in the EC2 settings or at launch\. For more information, see [Encryption Key Management](#EBSEncryption_key_mgmt)\.

Encryption by default has no effect on existing EBS volumes or snapshots, but when you copy unencrypted snapshots, or restore unencrypted volumes, the resulting snapshots or volumes are encrypted\. For examples of transitioning from unencrypted to encrypted EBS resources, see [Encrypting Unencrypted Resources](#encrypt-unencrypted)\.

When you enable encryption by default, you can launch an Amazon EC2 instance *only* if the instance type supports EBS encryption\. For more information, see [Supported Instance Types](#EBSEncryption_supported_instances)\.

**To configure encryption by default**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Under **Account Attributes**, choose **Settings**\.

1. Under **EBS Storage**, select **Encryption** to activate encryption by default\.

1. Choose **Update**\.

You can reconfigure this setting at any time\.

## Encryption Key Management<a name="EBSEncryption_key_mgmt"></a>

Amazon EBS encryption uses AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\) when creating encrypted volumes and any snapshots created from them\. A unique AWS\-managed CMK is created for you automatically in each Region where you store AWS resources\. This key is used for Amazon EBS encryption unless you specify a customer\-managed CMK that you created separately using AWS KMS\. You can specify this customer\-managed CMK when you perform a specific encryption action, or you can set it as your default CMK in the Amazon EC2 console under **Settings**, or using the Amazon EC2 API or CLI\.

**Note**  
Creating your own CMK gives you more flexibility, including the ability to create, rotate, and disable keys to define access controls\. For more information, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.

You cannot change the CMK that is associated with an existing snapshot or encrypted volume\. However, you can associate a different CMK during a snapshot copy operation so that the resulting copied snapshot uses the new CMK\.

EBS encrypts your volume with a data key using the industry\-standard AES\-256 algorithm\. Your data key is stored on\-disk with your encrypted data, but not before EBS encrypts it with your CMK—it never appears there in plaintext\. The same data key is shared by snapshots of the volume and any subsequent volumes created from those snapshots\.

**To configure a customer\-managed CMK as your default for encryption operations**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Under **Account Attributes**, choose **Settings**\.

1. Select **Use Customer managed CMK for Default encryption** to set a custom CMK in place of your account's AWS\-managed default CMK\. In the dialog that opens, choose an available key from the **Master Key** menu or paste the full ARN of any key that you have access to\. For more information, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.
**Important**  
If you configure a custom CMK as your account default, you must also grant your users access to a KMS key policy that allows the CMK to be used for launching instances, creating volumes, copying snapshots, and copying images\. These permissions include `GenerateDataKeyWithoutPlainText`, `Reencrypt*`, `CreateGrant`, `DescribeKey`, and `Decrypt`\. For more information, see [Authentication and Access Control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html) and [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html)\.
**Note**  
Changing the default CMK affects only the current AWS Region for your account\.

1. Choose **Update**\.

You can reconfigure this setting at any time\.

For more information about key management and key access permissions, see [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html) and [Authentication and Access Control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html) in the *AWS Key Management Service Developer Guide*\.

## Setting Encryption and Key Defaults Using the API and CLI<a name="encryption-by-default-api"></a>

In addition to managing encryption by default and the default CMK selection from the console, you can perform these tasks using API/SDK actions and CLI commands\. The following table lists and defines the actions and commands, and provides links to full documentation\.


| API action  | CLI command | Description | 
| --- | --- | --- | 
|  [DisableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DisableEbsEncryptionByDefault.html)  | [disable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-ebs-encryption-by-default.html) |  Disables default encryption\.  | 
|  [EnableEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_EnableEbsEncryptionByDefault.html)  | [enable\-ebs\-encryption\-by\-default](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-ebs-encryption-by-default.html) |  Enables default encryption\.  | 
|  [GetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsDefaultKmsKeyId.html)  | [get\-ebs\-default\-kmskeyid](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-default-kmskeyid.html) |  Describes the default customer master key \(CMK\)\.  | 
|  [GetEbsEncryptionByDefault](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_GetEbsEncryptionByDefault.html)  | [get\-ebs\-defaul\-kmskeyid](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-ebs-defaul-kmskeyid.html) |  Indicates whether default EBS encryption is enabled\.  | 
|  [ModifyEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyEbsDefaultKmsKeyId.html)  | [modify\-ebs\-default\-kmskeyid](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-ebs-default-kmskeyid.html) |  Changes the default customer master key \(CMK\) that your account uses to encrypt EBS volumes\.  | 
|  [ResetEbsDefaultKmsKeyId](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ResetEbsDefaultKmsKeyId.html)  | [reset\-ebs\-default\-kmskeyid](https://docs.aws.amazon.com/cli/latest/reference/ec2/reset-ebs-default-kmskeyid.html) |  Resets the account's default customer master key \(CMK\) to the account's AWS\-managed default CMK\.  | 

## Supported Instance Types<a name="EBSEncryption_supported_instances"></a>

Amazon EBS encryption is available on the instance types listed below\. You can attach both encrypted and unencrypted volumes to these instance types simultaneously\.
+ General purpose: A1, M3, M4, M5, M5a, M5ad, M5d, T2, T3, and T3a
+ Compute optimized: C3, C4, C5, C5d, and C5n
+ Memory optimized: `cr1.8xlarge`, R3, R4, R5, R5a, R5ad, R5d, X1, X1e, and z1d
+ Storage optimized: D2, `h1.2xlarge`, `h1.4xlarge`, I2, and I3
+ Accelerated computing: F1, G2, G3, P2, and P3
+ Bare metal: `i3.metal`, `m5.metal`, `m5d.metal`, `r5.metal`, `r5d.metal`, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and `z1d.metal`

For more information about these instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Using Encryption Parameters with EBS Volumes<a name="encryption-parameters"></a>

You apply encryption to EBS volumes by setting the `Encrypted` parameter to `true`\. \(The `Encrypted` parameter is optional if [encryption by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/encryption-by-default.html) is enabled\)\.

Optionally, you can use `KmsKeyId` to specify a custom key to use to encrypt the volume\. \(The `Encrypted` parameter must also be set to `true`, even if encryption by default is enabled\.\) If `KmsKeyId` is not specified, the key that is used for encryption depends on the encryption state of the source snapshot and its ownership\. The following table describes the encryption outcome for each possible combination of settings\.


**Encryption Outcomes**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)

\* This is the default CMK for the AWS account and region\. This may be either the AWS\-managed default or a customer\-managed default that you have specified\. For more information, see [Encryption Key Management](#EBSEncryption_key_mgmt)\.

\*\* This is a customer\-managed CMK specified for the volume at launch time\. This CMK overrides the default CMK that is set for the AWS account and region\.

### Creating New Empty Volumes with Encryption<a name="new-encrypted-volumes"></a>

When you create a new, empty EBS volume, you can encrypt it to your default CMK by setting the `Encrypted` flag\. To encrypt the volume to a custom CMK, you must provide a value for `KmsKeyId` as well\. The volume is encrypted from the time it is first available, so your data is always secured\. For detailed procedures, see [Creating an Amazon EBS Volume](ebs-creating-volume.md)\.

By default, the same CMK that you selected when creating the volume encrypts the snapshots that you make from it and the volumes that you restore from those snapshots\. You cannot remove encryption from an encrypted volume or snapshot, which means that a volume restored from an encrypted snapshot, or a copy of an encrypted snapshot, is *always* encrypted\. 

### Encrypting Unencrypted Resources<a name="encrypt-unencrypted"></a>

Although there is no direct way to encrypt an existing unencrypted volume or snapshot, you can encrypt existing unencrypted data by using either the `CreateVolume` or `CopySnapshot` action\. If you have enabled encryption by default, AWS enforces encryption of the resulting new volume or snapshot using your default CMK\. Even if you have not enabled encryption by default, you can supply encryption parameters with `CreateVolume` or `CopySnapshot` to encrypt resources individually\. In either case, you can override encryption defaults to apply a custom CMK\. All of the actions shown can be performed with the EC2 console, AWS CLI, or AWS API\. For more information, see [Creating an Amazon EBS Volume](ebs-creating-volume.md) and [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\. 

The following examples illustrate how these actions and the encryption parameters can be used to manage the encryption of your volumes and snapshots\. For a full list of encryption cases, see the [encryption outcomes table](#encryption-parameters)\.

#### **Restore an Unencrypted Volume \(Encryption by Default *Not Enabled*\)**<a name="volume-account-off"></a>

Without encryption by default enabled, a volume restored from an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting volume by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. The following diagram illustrates the process\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-off.png)

If you leave out the `KmsKeyId` parameter, the resulting volume is encrypted to your default CMK\. You must supply a key ID to encrypt the volume to a different CMK\.

For more information, see [Restoring an Amazon EBS Volume from a Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-restoring-volume.html)\.

#### Restore an Unencrypted Volume \(Encryption by Default *Enabled*\)<a name="volume-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for volumes restored from unencrypted snapshots, and no encryption parameters are required for your default CMK to be used\. The following diagram shows this simple default case:

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-encrypt-account-on.png)

If you want to encrypt the restored volume to a custom CMK, you must supply both the `Encrypted` and `KmsKeyId` parameters as shown in [**Restore an Unencrypted Volume \(Encryption by Default *Not Enabled*\)**Copy an Unencrypted Snapshot \(Encryption by Default *Not Enabled*\) ](#volume-account-off)\.

#### Copy an Unencrypted Snapshot \(Encryption by Default *Not Enabled*\)<a name="snapshot-account-off"></a>

Without encryption by default enabled, a copy of an unencrypted snapshot is unencrypted by default\. However, you can encrypt the resulting snapshot by setting the `Encrypted` parameter and, optionally, the `KmsKeyId` parameter\. The following diagram illustrates the process\.

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-off.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

If you leave out the `KmsKeyId` parameter, the resulting snapshot is encrypted to your default CMK\. You must supply a key ID to encrypt the volume to a different CMK\.

**To encrypt a volume's data by means of snapshot copying**

1. Copy the snapshot while applying encryption parameters\. If you leave out the `KmsKeyId` parameter, the resulting snapshot is encrypted to your default CMK\. You can optionally include a key ID to encrypt the snapshot to a different CMK\.

1. Restore the encrypted snapshot to a new volume, which is also encrypted\. 

For more information, see [Copying an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)\.

#### Copy an Unencrypted Snapshot \(Encryption by Default *Enabled*\)<a name="snapshot-account-on"></a>

When you have enabled encryption by default, encryption is mandatory for copies of unencrypted snapshots, and no encryption parameters are required if your default CMK is used\. The following diagram shows this simple default case:

![\[Create an encrypted snapshot from an unencrypted snapshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot-encrypt-account-on.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

If you want to encrypt the snapshot copy to a custom CMK, you must supply both the `Encrypted` and `KmsKeyId` parameters as shown in [](#snapshot-account-off)\.

### Re\-Encrypting Resources to a New CMK<a name="reencrypt-resources"></a>

This section provides examples of re\-encrypting an encrypted EBS resource to a different CMK\.

#### Re\-Encrypt an Encrypted Volume<a name="reencrypt-volume"></a>

When the `CreateVolume` action operates on an encrypted snapshot, you have the option of re\-encrypting it with a different CMK\. The following diagram illustrates the process\. You own two CMKs, CMK A and CMK B\. The source snapshot is encrypted to CMK A\. During volume creation, with the key ID of CMK B supplied as a parameter, the source data is automatically decrypted, then re\-encrypted to CMK B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/volume-reencrypt.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

For more information, see [Restoring an Amazon EBS Volume from a Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-restoring-volume.html)\.

#### Re\-Encrypt an Encrypted Snapshot<a name="reencrypt-snap"></a>

The ability to encrypt a snapshot during copying allows you to apply a new CMK to an already\-encrypted snapshot that you own\. Volumes restored from the resulting copy are only accessible using the new CMK\. The following diagram illustrates the process\. You own two CMKs, CMK A and CMK B\. The source snapshot is encrypted to CMK A\. During copy, with the key ID of CMK B supplied as a parameter, the source data is automatically re\-encrypted to CMK B\.

![\[Copy an encrypted snapshot and encrypt the copy to a new key.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snap-reencrypt.png)

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

In a related scenario, you can choose to apply new encryption parameters to a copy of a snapshot that has been shared with you\. By default, the copy is encrypted with a CMK shared by the snapshot's owner\. However, we recommend that you create a copy of the shared snapshot using a different CMK that you control\. This protects your access to the volume if the original CMK is compromised, or if the owner revokes the CMK for any reason\. 

The following procedure demonstrates how to create a copy of a shared snapshot to a customer\-managed CMK that you own\. It assumes that you have previously created a customer\-managed CMK, and that you have a snapshot that has been encrypted to your default CMK\. For more information, see [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.

**To copy a snapshot that you own to a new customer\-managed CMK using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Snapshots** page, select your snapshot, then choose **Actions**, **Copy**\.

1. In the **Copy Snapshot** window, supply the complete ARN for your customer\-managed CMK \(in the form arn:aws:kms:*us\-east\-1*:*012345678910*:key/*abcd1234\-a123\-456a\-a12b\-a123b4cd56ef*\) in the **Master Key** field, or choose it from the menu\. Choose **Copy**\. 

The resulting copy of the snapshot—and all volumes restored from it—are encrypted to your customer\-managed CMK\. 

The following procedure demonstrates how to make a copy of a shared encrypted snapshot to a new CMK that you own\. For this to work, you also need access permissions to both the shared encrypted snapshot and to the CMK to which it was originally encrypted\. 

**To copy a shared snapshot to a CMK that you own using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Snapshots** page, select the shared encrypted snapshot, then choose **Actions**, **Copy**\.

1. In the **Copy Snapshot** window, supply the complete ARN for a CMK that you own \(in the form arn:aws:kms:*us\-east\-1*:*012345678910*:key/*abcd1234\-a123\-456a\-a12b\-a123b4cd56ef*\) in the **Master Key** field, or choose it from the menu\. Choose **Copy**\. 

The resulting copy of the snapshot—and all volumes restored from it—are encrypted to the CMK that you supplied\. Changes to the original shared snapshot, its encryption status, or the shared CMK have no effect on your copy\.

For more information, see [Copying an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)\.

**Note**  
You can also apply new encryption states when launching an instance from an EBS\-backed AMI\. This is because EBS\-backed AMIs include snapshots of EBS volumes that can be manipulated as described\. For more information about encryption options while launching an instance from an EBS\-backed AMI, see [Using Encryption with EBS\-Backed AMIs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AMIEncryption.html) \.

### Migrate Data between Encrypted and Unencrypted Volumes<a name="migrate-data-encrypted-unencrypted"></a>

When you have access to both an encrypted and unencrypted volume, you can freely transfer data between them\. EC2 carries out the encryption and decryption operations transparently\.<a name="procedure_migrating_data_encryption"></a>

**To migrate data between encrypted and unencrypted volumes**

1. Create your destination volume \(encrypted or unencrypted, depending on your need\) by following the procedures in [Creating an Amazon EBS Volume](ebs-creating-volume.md)\. 

1. Attach the destination volume to the instance that hosts the data that you want to migrate\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. Make the destination volume available by following the procedures in [Making an Amazon EBS Volume Available for Use on Linux](ebs-using-volumes.md)\. For Linux instances, you can create a mount point at `/mnt/destination` and mount the destination volume there\.

1. Copy the data from your source directory to the destination volume\. It may be most convenient to use a bulk\-copy utility for this\. 

   **Linux**

   Use the rsync command as follows to copy the data from your source to the destination volume\. In this example, the source data is located in `/mnt/source` and the destination volume is mounted at `/mnt/destination`\.

   ```
   [ec2-user ~]$ sudo rsync -avh --progress /mnt/source/ /mnt/destination/
   ```

   **Windows**

   At a command prompt, use the robocopy command to copy the data from your source to the destination volume\. In this example, the source data is located in `D:\` and the destination volume is mounted at `E:\`\.

   ```
   PS C:\> robocopy D:\<sourcefolder> E:\<destinationfolder> /e /copyall /eta
   ```
**Note**  
We recommend explicitly naming folders rather than copying the entire volume in order to avoid potential problems with hidden folders\.

## Amazon EBS Encryption and CloudWatch Events<a name="ebs-cw-crypto"></a>

Amazon EBS supports Amazon CloudWatch Events for certain encryption\-related scenarios\. For more information, see [Amazon CloudWatch Events for Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-cloud-watch-events.html#attach-fail-key)\.
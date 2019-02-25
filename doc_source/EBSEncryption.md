# Amazon EBS Encryption<a name="EBSEncryption"></a>

Amazon EBS encryption offers a simple encryption solution for your EBS volumes without the need to build, maintain, and secure your own key management infrastructure\. When you create an encrypted EBS volume and attach it to a supported instance type, the following types of data are encrypted:
+ Data at rest inside the volume
+ All data moving between the volume and the instance
+ All snapshots created from the volume
+ All volumes created from those snapshots

Encryption operations occur on the servers that host EC2 instances, ensuring the security of both data\-at\-rest and data\-in\-transit between an instance and its attached EBS storage\. 

Encryption is supported by all EBS volume types \(General Purpose SSD \[`gp2`\], Provisioned IOPS SSD \[`io1`\], Throughput Optimized HDD \[`st1`\], Cold HDD \[`sc1`\], and Magnetic \[`standard`\]\)\. You can expect the same IOPS performance on encrypted volumes as on unencrypted volumes, with a minimal effect on latency\. You can access encrypted volumes the same way that you access unencrypted volumes\. Encryption and decryption are handled transparently and they require no additional action from you or your applications\.

Public snapshots of encrypted volumes are not supported, but you can share an encrypted snapshot with specific accounts\. For more information about sharing encrypted snapshots, see [Sharing an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modifying-snapshot-permissions.html)\.

Amazon EBS encryption is only available on certain instance types\. You can attach both encrypted and unencrypted volumes to a supported instance type\. For more information, see [Supported Instance Types](#EBSEncryption_supported_instances)\.

**Topics**
+ [Encryption Key Management](#EBSEncryption_key_mgmt)
+ [Supported Instance Types](#EBSEncryption_supported_instances)
+ [Changing the Encryption State of Your Data](#EBSEncryption_considerations)
+ [Amazon EBS Encryption and CloudWatch Events](#ebs-cw-crypto)

## Encryption Key Management<a name="EBSEncryption_key_mgmt"></a>

Amazon EBS encryption uses AWS Key Management Service \(AWS KMS\) customer master keys \(CMKs\) when creating encrypted volumes and any snapshots created from them\. A unique AWS\-managed CMK is created for you automatically in each region where you store AWS assets\. This key is used for Amazon EBS encryption unless you specify a customer\-managed CMK that you created separately using AWS KMS\. 

**Note**  
Creating your own CMK gives you more flexibility, including the ability to create, rotate, and disable keys to define access controls\. For more information, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.

You cannot change the CMK that is associated with an existing snapshot or encrypted volume\. However, you can associate a different CMK during a snapshot copy operation so that the resulting copied snapshot uses the new CMK\.

EBS encrypts your volume with a data key using the industry\-standard AES\-256 algorithm\. Your data key is stored on\-disk with your encrypted data, but not before EBS encrypts it with your CMK—it never appears there in plaintext\. The same data key is shared by snapshots of the volume and any subsequent volumes created from those snapshots\.

For more information about key management and key access permissions, see [How Amazon Elastic Block Store \(Amazon EBS\) Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-ebs.html) and [Authentication and Access Control for AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html) in the *AWS Key Management Service Developer Guide*\.

## Supported Instance Types<a name="EBSEncryption_supported_instances"></a>

Amazon EBS encryption is available on the instance types listed below\. You can attach both encrypted and unencrypted volumes to these instance types simultaneously\.
+ General purpose: A1, M3, M4, M5, M5d, T2, and T3
+ Compute optimized: C3, C4, C5, C5d, and C5n
+ Memory optimized: `cr1.8xlarge`, R3, R4, R5, R5d, X1, X1e, and z1d
+ Storage optimized: D2, `h1.2xlarge`, `h1.4xlarge`, I2, and I3
+ Accelerated computing: F1, G2, G3, P2, and P3
+ Bare metal: `i3.metal`, `m5.metal`, `m5d.metal`, `r5.metal`, `r5d.metal`, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and `z1d.metal`

For more information about these instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Changing the Encryption State of Your Data<a name="EBSEncryption_considerations"></a>

There is no direct way to encrypt an existing unencrypted volume, or to remove encryption from an encrypted volume\. However, you can migrate data between encrypted and unencrypted volumes\. You can also apply a new encryption status while copying a snapshot:
+ While copying an unencrypted snapshot of an unencrypted volume, you can encrypt the copy\. Volumes restored from this encrypted copy are also encrypted\.
+ While copying an encrypted snapshot of an encrypted volume, you can associate the copy with a different CMK\. Volumes restored from the encrypted copy are only accessible using the newly applied CMK\.

You cannot remove encryption from an encrypted snapshot\.

### Migrate Data between Encrypted and Unencrypted Volumes<a name="migrate-data-encrypted-unencrypted"></a>

When you have access to both an encrypted and unencrypted volume, you can freely transfer data between them\. EC2 carries out the encryption and decryption operations transparently\.<a name="procedure_migrating_data_encryption"></a>

**To migrate data between encrypted and unencrypted volumes**

1. Create your destination volume \(encrypted or unencrypted, depending on your need\) by following the procedures in [Creating an Amazon EBS Volume](ebs-creating-volume.md)\. 

1. Attach the destination volume to the instance that hosts the data to migrate\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

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

### Apply Encryption While Copying a Snapshot<a name="apply-encryption-copy-snapshot"></a>

Because you can apply encryption to a snapshot while copying it, another path to encrypting your data is the following procedure\.

**To encrypt a volume's data by means of snapshot copying**

1. Create a snapshot of your unencrypted EBS volume\. This snapshot is also unencrypted\.

1. Copy the snapshot while applying encryption parameters\. The resulting target snapshot is encrypted\.

1. Restore the encrypted snapshot to a new volume, which is also encrypted\. 

For more information, see [Copying an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)\.

### Encrypt a Snapshot to a New CMK<a name="re-encrypt_snapshot"></a>

The ability to encrypt a snapshot during copying also allows you to apply a new CMK to an already\-encrypted snapshot that you own\. Volumes restored from the resulting copy are only accessible using the new CMK\. 

**Note**  
If you copy a snapshot to a new CMK, a complete \(non\-incremental\) copy will always be created, resulting in additional storage costs\. 

In a related scenario, you may choose to apply new encryption parameters to a copy of a snapshot that has been shared with you\. Before you can restore a volume from a shared encrypted snapshot, you must create your own copy of it\. By default, the copy is encrypted with a CMK shared by the snapshot's owner\. However, we recommend that you create a copy of the shared snapshot using a different CMK that you control\. This protects your access to the volume if the original CMK is compromised, or if the owner revokes the CMK for any reason\. 

The following procedure demonstrates how to create a copy of a shared snapshot to a customer\-managed CMK that you own\.

**To copy a snapshot that you own to a new custom CMK using the console**

1. Create a customer\-managed CMK\. For more information, see [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.

1. Create an EBS volume encrypted to \(for this example\) your AWS\-managed CMK\. 

1. Create a snapshot of your encrypted EBS volume\. This snapshot is also encrypted to your AWS\-managed CMK\.

1. On the **Snapshots** page, choose **Actions**, **Copy**\.

1. In the **Copy Snapshot** window, supply the complete ARN for your customer\-managed CMK \(in the form arn:aws:kms:*us\-east\-1*:*012345678910*:key/*abcd1234\-a123\-456a\-a12b\-a123b4cd56ef*\) in the **Master Key** field, or choose it from the menu\. Choose **Copy**\. 

The resulting copy of the snapshot—and all volumes restored from it—are encrypted to your customer\-managed CMK\. 

The following procedure demonstrates how to make a copy of a shared encrypted snapshot to a new CMK that you own\. For this to work, you also need access permissions to both the shared encrypted snapshot and to the CMK to which it was originally encrypted\. 

**To copy a shared snapshot to a CMK that you own using the console**

1. Select the shared encrypted snapshot on the **Snapshots** page and choose **Actions**, **Copy**\.

1. In the **Copy Snapshot** window, supply the complete ARN for a CMK that you own \(in the form arn:aws:kms:*us\-east\-1*:*012345678910*:key/*abcd1234\-a123\-456a\-a12b\-a123b4cd56ef*\) in the **Master Key** field, or choose it from the menu\. Choose **Copy**\. 

The resulting copy of the snapshot—and all volumes restored from it—are encrypted to the CMK that you supplied\. Changes to the original shared snapshot, its encryption status, or the shared CMK have no effect on your copy\.

For more information, see [Copying an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)\.

## Amazon EBS Encryption and CloudWatch Events<a name="ebs-cw-crypto"></a>

Amazon EBS supports Amazon CloudWatch Events for certain encryption\-related scenarios\. For more information, see [Amazon CloudWatch Events for Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-cloud-watch-events.html#attach-fail-key)\.
# Copying an Amazon EBS Snapshot<a name="ebs-copy-snapshot"></a>

With Amazon EBS, you can create point\-in\-time snapshots of volumes, which we store for you in Amazon S3\. After you create a snapshot and it has finished copying to Amazon S3 \(when the snapshot status is `completed`\), you can copy it from one AWS Region to another, or within the same Region\. Amazon S3 server\-side encryption \(256\-bit AES\) protects a snapshot's data in transit during a copy operation\. The snapshot copy receives an ID that is different from the ID of the original snapshot\.

To copy multi\-volume snapshots to another AWS Region, retrieve the snapshots using the tag you applied to the multi\-volume snapshots group when you created it\. Then individually copy the snapshots to another Region\.

For information about copying an Amazon RDS snapshot, see [Copying a DB Snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html) in the *Amazon RDS User Guide*\.

If you would like another account to be able to copy your snapshot, you must either modify the snapshot permissions to allow access to that account or make the snapshot public so that all AWS accounts can copy it\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

For pricing information about copying snapshots across AWS Regions and accounts, see [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing/)\. Note that snapshot copy operations within a single account and Region do not copy any actual data and therefore are cost\-free as long as the encryption status of the snapshot copy does not change\.

**Note**  
If you copy a snapshot to a new Region, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

**Use Cases**
+ Geographic expansion: Launch your applications in a new AWS Region\.
+ Migration: Move an application to a new Region, to enable better availability and to minimize cost\.
+ Disaster recovery: Back up your data and logs across different geographical locations at regular intervals\. In case of disaster, you can restore your applications using point\-in\-time backups stored in the secondary Region\. This minimizes data loss and recovery time\.
+ Encryption: Encrypt a previously unencrypted snapshot, change the key with which the snapshot is encrypted, or, for encrypted snapshots that have been shared with you, create a copy that you own in order to restore a volume from it\.
+ Data retention and auditing requirements: Copy your encrypted EBS snapshots from one AWS account to another to preserve data logs or other files for auditing or data retention\. Using a different account helps prevent accidental snapshot deletions, and protects you if your main AWS account is compromised\.

**Prerequisites**
+ You can copy any accessible snapshots that have a `completed` status, including shared snapshots and snapshots that you have created\.
+ You can copy AWS Marketplace, VM Import/Export, and AWS Storage Gateway snapshots, but you must verify that the snapshot is supported in the destination Region\.

**Limits**
+ Each account can have up to five concurrent snapshot copy requests to a single destination Region\.
+ User\-defined tags are not copied from the source snapshot to the new snapshot\. After the copy operation is complete, you can apply user\-defined tags to the new snapshot\. For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.
+ Snapshots created by the `CopySnapshot` action have an arbitrary volume ID that should not be used for any purpose\.

## Incremental Copying Across Regions<a name="ebs-incremental-copy"></a>

The first snapshot copy to another Region is always a full copy\. For unencrypted snapshots, each subsequent snapshot copy of the same volume is incremental, meaning that AWS copies only the data that changed since your last snapshot copy to the same destination Region\. This allows for faster copying and lower storage costs\.

In the case of encrypted snapshots, you must encrypt to the same CMK that was used for previous copies to get incremental copies\. The following examples illustrate how this works: 
+ If you copy an unencrypted snapshot from the US East \(N\. Virginia\) Region to the US West \(Oregon\) Region, the first snapshot copy is a full copy and subsequent snapshot copies of the same volume transferred between the same Regions are incremental\.
+ If you copy an encrypted snapshot from the US East \(N\. Virginia\) Region to the US West \(Oregon\) Region, the first snapshot copy of the volume is a full copy\.
  + If you encrypt to the same CMK in a subsequent snapshot copy for the same volume between the same Regions, the copy is incremental\.
  + If you encrypt to a different CMK in a subsequent snapshot copy for the same volume between the same Regions, the copy is a new full copy of the snapshot\.

For more information, see [Re\-Encrypt an Encrypted Snapshot](EBSEncryption.md#reencrypt-snapshot)\.

## Encryption and Snapshot Copying<a name="creating-encrypted-snapshots"></a>

When you copy a snapshot, you can encrypt the copy or you can specify a CMK different from the original one, and the resulting copied snapshot uses the new CMK\. However, changing the encryption status of a snapshot during a copy operation results in a full \(not incremental\) copy, which might incur greater data transfer and storage charges\. 

To copy an encrypted snapshot shared from another AWS account, you must have permissions to use the snapshot and the customer master key \(CMK\) that was used to encrypt the snapshot\. When using an encrypted snapshot that was shared with you, we recommend that you re\-encrypt the snapshot by copying it using a CMK that you own\. This protects you if the original CMK is compromised, or if the owner revokes it, which could cause you to lose access to any encrypted volumes that you created using the snapshot\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

You apply encryption to EBS snapshot copies by setting the `Encrypted` parameter to `true`\. \(The `Encrypted` parameter is optional if [encryption by default](EBSEncryption.md#encryption-by-default) is enabled\)\.

Optionally, you can use `KmsKeyId` to specify a custom key to use to encrypt the snapshot copy\. \(The `Encrypted` parameter must also be set to `true`, even if encryption by default is enabled\.\) If `KmsKeyId` is not specified, the key that is used for encryption depends on the encryption state of the source snapshot and its ownership\.

The following table describes the encryption outcome for each possible combination of settings\.


**Encryption outcomes: Copying a snapshot**  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)

\* This is the default CMK used for EBS encryption for the AWS account and Region\. By default this is a unique AWS managed CMK for EBS, or you can specify a customer managed CMK\. For more information, see [Default Key for EBS Encryption](EBSEncryption.md#EBSEncryption_key_mgmt)\.

\*\* This is a customer managed CMK specified for the copy action\. This CMK is used instead of the default CMK for the AWS account and Region\.

## Copy a Snapshot<a name="ebs-snapshot-copy"></a>

Use the following procedure to copy a snapshot using the Amazon EC2 console\.

**To copy a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot to copy, and then choose **Copy** from the **Actions** list\.

1. In the **Copy Snapshot** dialog box, update the following as necessary:
   + **Destination region**: Select the Region where you want to write the copy of the snapshot\.
   + **Description**: By default, the description includes information about the source snapshot so that you can identify a copy from the original\. You can change this description as necessary\.
   + **Encryption**: If the source snapshot is not encrypted, you can choose to encrypt the copy\. If you have enabled [encryption by default](EBSEncryption.md#encryption-by-default), the **Encryption** option is set and cannot be unset from the snapshot console\. If the **Encryption** option is set, you can choose to encrypt it to a customer managed CMK by selecting one in the field, described below\.

     You cannot strip encryption from an encrypted snapshot\.
**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.
   + **Master Key**: The customer master key \(CMK\) to be used to encrypt this snapshot\. The default key for your account is displayed initially, but you can optionally select from the master keys in your account or type/paste the ARN of a key from a different account\. You can create new master encryption keys in the IAM console [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\. 

1. Choose **Copy**\.

1. In the **Copy Snapshot** confirmation dialog box, choose **Snapshots** to go to the **Snapshots** page in the Region specified, or choose **Close**\.

   To view the progress of the copy process, switch to the destination Region, and then refresh the **Snapshots** page\. Copies in progress are listed at the top of the page\.

**To check for failure**  
If you attempt to copy an encrypted snapshot without having permissions to use the encryption key, the operation fails silently\. The error state is not displayed in the console until you refresh the page\. You can also check the state of the snapshot from the command line, as in the following example\.

```
aws ec2 describe-snapshots --snapshot-id snap-0123abcd
```

If the copy failed because of insufficient key permissions, you see the following message: "StateMessage": "Given key ID is not accessible"\.

When copying an encrypted snapshot, you must have `DescribeKey` permissions on the default CMK\. Explicitly denying these permissions results in copy failure\. For information about managing CMK keys, see [Controlling Access to Customer Master Keys](https://docs.aws.amazon.com/kms/latest/developerguide/control-access.html)\.

**To copy a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [copy\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-snapshot.html) \(AWS CLI\)
+ [Copy\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Copy-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)
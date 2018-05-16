# Copying an Amazon EBS Snapshot<a name="ebs-copy-snapshot"></a>

With Amazon EBS, you can create point\-in\-time snapshots of volumes, which we store for you in Amazon S3\. After you've created a snapshot and it has finished copying to Amazon S3 \(when the snapshot status is `completed`\), you can copy it from one AWS region to another, or within the same region\. Amazon S3 server\-side encryption \(256\-bit AES\) protects a snapshot's data in\-transit during a copy operation\. The snapshot copy receives an ID that is different than the ID of the original snapshot\.

For information about copying an Amazon RDS snapshot, see [Copying a DB Snapshot](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html) in the *Amazon Relational Database Service User Guide*\.

If you would like another account to be able to copy your snapshot, you must either modify the snapshot permissions to allow access to that account or make the snapshot public so that all AWS accounts may copy it\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

For pricing information about copying snapshots across regions and accounts, see [Amazon EBS Pricing](https://aws.amazon.com/ebs/pricing/)\. Note that snapshot copy operations within a single account and region do not copy any actual data and therefore are cost\-free as long as the encryption status of the snapshot copy does not change\. Copying a snapshot to a new region does incur new storage costs\. 

**Use Cases**
+ Geographic expansion: Launch your applications in a new region\.
+ Migration: Move an application to a new region, to enable better availability and to minimize cost\.
+ Disaster recovery: Back up your data and logs across different geographical locations at regular intervals\. In case of disaster, you can restore your applications using point\-in\-time backups stored in the secondary region\. This minimizes data loss and recovery time\.
+ Encryption: Encrypt a previously unencrypted snapshot, change the key with which the snapshot is encrypted, or, for encrypted snapshots that have been shared with you, create a copy that you own in order to restore a volume from it\.
+ Data retention and auditing requirements: Copy your encrypted EBS snapshots from one AWS account to another to preserve data logs or other files for auditing or data retention\. Using a different account helps prevent accidental snapshot deletions, and protects you if your main AWS account is compromised\.

**Prerequisites**
+ You can copy any accessible snapshots that have a `completed` status, including shared snapshots and snapshots that you've created\.
+ You can copy AWS Marketplace, VM Import/Export, and AWS Storage Gateway snapshots, but you must verify that the snapshot is supported in the destination region\.

**Limits**
+ Each account can have up to 5 concurrent snapshot copy requests to a single destination region\.
+ User\-defined tags are not copied from the source snapshot to the new snapshot\. After the copy operation is complete, you can apply user\-defined tags to the new snapshot\. For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.
+ Snapshots created by the CopySnapshot action have an arbitrary volume ID that should not be used for any purpose\.

## Incremental Copying Across Regions<a name="ebs-incremental-copy"></a>

The first snapshot copy to another region is always a full copy\. For unencrypted snapshots, each subsequent snapshot copy of the same volume is incremental, meaning that AWS copies only the data that changed since your last snapshot copy to the same destination region\. This allows for faster copying and lower storage costs\.

In the case of encrypted snapshots, you must encrypt to the same CMK that was used for previous copies to get incremental copies\. The following examples illustrate how this works: 
+ If you copy an unencrypted snapshot from the US East \(N\. Virginia\) region to the US West \(Oregon\) region, the first snapshot copy is a full copy and subsequent snapshot copies of the same volume transferred between the same regions are incremental\.
+ If you copy an encrypted snapshot from the US East \(N\. Virginia\) region to the US West \(Oregon\) region, the first snapshot copy of the volume is a full copy\.
  + If you encrypt to the same CMK in a subsequent snapshot copy for the same volume between the same regions, the copy is incremental\.
  + If you encrypt to a different CMK in a subsequent snapshot copy for the same volume between the same regions, the copy is a new full copy of the snapshot\.

For more information, see [Encrypt a Snapshot Under a New CMK](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html#re-encrypt_snapshot)\.

## Encrypted Snapshots<a name="ebs-encrypt-snapshot-copy"></a>

When you copy a snapshot, you can choose to encrypt the copy \(if the original snapshot was not encrypted\) or you can specify a CMK different from the original one, and the resulting copied snapshot uses the new CMK\. However, changing the encryption status of a snapshot or using a non\-default EBS CMK during a copy operation always results in a full \(not incremental\) copy, which may incur greater data transfer and storage charges\. 

To copy an encrypted snapshot from another account, you must have permissions to use the snapshot and you must have permissions to use the customer master key \(CMK\) that was used to encrypt the original snapshot\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

When copying an encrypted snapshot that was shared with you, you should consider re\-encrypting the snapshot during the copy process with a different key that you control\. This protects you if the original key is compromised, or if the owner revokes the key for any reason, which could cause you to lose access to the volume you created\.

## Copy a Snapshot<a name="ebs-snapshot-copy"></a>

Use the following procedure to copy a snapshot using the Amazon EC2 console\.

**To copy a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot to copy, and then choose **Copy** from the **Actions** list\.

1. In the **Copy Snapshot** dialog box, update the following as necessary:
   + **Destination region**: Select the region where you want to write the copy of the snapshot\.
   + **Description**: By default, the description includes information about the source snapshot so that you can identify a copy from the original\. You can change this description as necessary\.
   + **Encryption**: If the source snapshot is not encrypted, you can choose to encrypt the copy\. *You cannot strip encryption from an encrypted snapshot*\.
   + **Master Key**: The customer master key \(CMK\) that to be used to encrypt this snapshot\. You can select from master keys in your account or type/paste the ARN of a key from a different account\. You can create a new master encryption key in the IAM console\. 

1. Choose **Copy**\.

1. In the **Copy Snapshot** confirmation dialog box, choose **Snapshots** to go to the **Snapshots** page in the region specified, or choose **Close**\.

   To view the progress of the copy process, switch to the destination region, and then refresh the **Snapshots** page\. Copies in progress are listed at the top of the page\.

**To check for failure**  
If you attempt to copy an encrypted snapshot without having permissions to use the encryption key, the operation fails silently\. The error state is not displayed in the console until you refresh the page\. You can also check the state of the snapshot from the command line\. For example:

```
aws ec2 describe-snapshots --snapshot-id snap-0123abcd
```

If the copy failed because of insufficient key permissions, you see the following message: "StateMessage": "Given key ID is not accessible"\.

When copying an encrypted snapshot, you must have `DescribeKey` permissions on the default CMK\. Explicitly denying these permissions results in copy failure\. For information about managing CMK keys, see [Controlling Access to Customer Master Keys](http://docs.aws.amazon.com/kms/latest/developerguide/control-access.html)\.

**To copy a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [copy\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/ec2/copy-snapshot.html) \(AWS CLI\)
+ [Copy\-EC2Snapshot](http://docs.aws.amazon.com/powershell/latest/reference/items/Copy-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)
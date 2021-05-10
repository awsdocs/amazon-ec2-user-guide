# Copy an Amazon EBS snapshot<a name="ebs-copy-snapshot"></a>

With Amazon EBS, you can create point\-in\-time snapshots of volumes, which we store for you in Amazon S3\. After you create a snapshot and it has finished copying to Amazon S3 \(when the snapshot status is `completed`\), you can copy it from one AWS Region to another, or within the same Region\. Amazon S3 server\-side encryption \(256\-bit AES\) protects a snapshot's data in transit during a copy operation\. The snapshot copy receives an ID that is different from the ID of the original snapshot\.

To copy multi\-volume snapshots to another AWS Region, retrieve the snapshots using the tag you applied to the multi\-volume snapshots group when you created it\. Then individually copy the snapshots to another Region\.

If you would like another account to be able to copy your snapshot, you must either modify the snapshot permissions to allow access to that account or make the snapshot public so that all AWS accounts can copy it\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

For information about copying an Amazon RDS snapshot, see [Copying a DB Snapshot](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CopySnapshot.html) in the *Amazon RDS User Guide*\.

**Use cases**
+ Geographic expansion: Launch your applications in a new AWS Region\.
+ Migration: Move an application to a new Region, to enable better availability and to minimize cost\.
+ Disaster recovery: Back up your data and logs across different geographical locations at regular intervals\. In case of disaster, you can restore your applications using point\-in\-time backups stored in the secondary Region\. This minimizes data loss and recovery time\.
+ Encryption: Encrypt a previously unencrypted snapshot, change the key with which the snapshot is encrypted, or create a copy that you own in order to create a volume from it \(for encrypted snapshots that have been shared with you\)\.
+ Data retention and auditing requirements: Copy your encrypted EBS snapshots from one AWS account to another to preserve data logs or other files for auditing or data retention\. Using a different account helps prevent accidental snapshot deletions, and protects you if your main AWS account is compromised\.

**Prerequisites**
+ You can copy any accessible snapshots that have a `completed` status, including shared snapshots and snapshots that you have created\.
+ You can copy AWS Marketplace, VM Import/Export, and AWS Storage Gateway snapshots, but you must verify that the snapshot is supported in the destination Region\.

**Considerations**
+ Each account can have up to twenty concurrent snapshot copy requests to a single destination Region\.
+ User\-defined tags are not copied from the source snapshot to the new snapshot\. You can add user\-defined tags during or after the copy operation\. For more information, see [Tag your Amazon EC2 resources](Using_Tags.md)\.
+ Snapshots created by a snapshot copy operation have an arbitrary volume ID that should not be used for any purpose\.
+ Resource\-level permissions specified for the snapshot copy operation apply only to the new snapshot\. You cannot specify resource\-level permissions for the source snapshot\. For an example, see [Example: Copying snapshots](ExamplePolicies_EC2.md#iam-copy-snapshot)\.

**Pricing**
+ For pricing information about copying snapshots across AWS Regions and accounts, see [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing/)\.
+ Snapshot copy operations within a single account and Region do not copy any actual data and therefore are cost\-free as long as the encryption status of the snapshot copy does not change\.
+ If you copy a snapshot and encrypt it to a new KMS key, a complete \(non\-incremental\) copy is created\. This results in additional storage costs\.
+ If you copy a snapshot to a new Region, a complete \(non\-incremental\) copy is created\. This results in additional storage costs\. Subsequent copies of the same snapshot are incremental\.

## Incremental snapshot copying<a name="ebs-incremental-copy"></a>

Whether a snapshot copy is incremental is determined by the most recently completed snapshot copy\. When you copy a snapshot across Regions or accounts, the copy is an incremental copy if the following conditions are met:
+ The snapshot was copied to the destination Region or account previously\.
+ The most recent snapshot copy still exists in the destination Region or account\.
+ All copies of the snapshot in the destination Region or account are either unencrypted or were encrypted using the same KMS key\.

If the most recent snapshot copy was deleted, the next copy is a full copy, not an incremental copy\. If a copy is still pending when you start a another copy, the second copy starts only after the first copy finishes\.

We recommend that you tag your snapshots with the volume ID and creation time so that you can keep track of the most recent snapshot copy of a volume in the destination Region or account\.

To see whether your snapshot copies are incremental, check the [copySnapshot](ebs-cloud-watch-events.md#copy-snapshot-complete) CloudWatch event\.

## Encryption and snapshot copying<a name="creating-encrypted-snapshots"></a>

When you copy a snapshot, you can encrypt the copy or you can specify a KMS key that is different than the original, and the resulting copied snapshot uses the new KMS key\. However, changing the encryption status of a snapshot during a copy operation results in a full \(not incremental\) copy, which might incur greater data transfer and storage charges\. 

To copy an encrypted snapshot shared from another AWS account, you must have permissions to use the snapshot and the customer master key \(CMK\) that was used to encrypt the snapshot\. When using an encrypted snapshot that was shared with you, we recommend that you re\-encrypt the snapshot by copying it using a KMS key that you own\. This protects you if the original KMS key is compromised, or if the owner revokes it, which could cause you to lose access to any encrypted volumes that you created using the snapshot\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

You apply encryption to EBS snapshot copies by setting the `Encrypted` parameter to `true`\. \(The `Encrypted` parameter is optional if [encryption by default](EBSEncryption.md#encryption-by-default) is enabled\)\.

Optionally, you can use `KmsKeyId` to specify a custom key to use to encrypt the snapshot copy\. \(The `Encrypted` parameter must also be set to `true`, even if encryption by default is enabled\.\) If `KmsKeyId` is not specified, the key that is used for encryption depends on the encryption state of the source snapshot and its ownership\.

The following tables describe the encryption outcome for each possible combination of settings\.

**Topics**
+ [Encryption outcomes: Copying snapshots that you own](#own-snapshots)
+ [Encryption outcomes: Copying snapshots that are shared with you](#shared-snapshots)

### Encryption outcomes: Copying snapshots that you own<a name="own-snapshots"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)

\*\* This is a customer managed key specified for the copy action\. This customer managed key is used instead of the default customer managed key for the AWS account and Region\.

### Encryption outcomes: Copying snapshots that are shared with you<a name="shared-snapshots"></a>

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-copy-snapshot.html)

\*\* This is a customer managed key specified for the copy action\. This customer managed key is used instead of the default customer managed key for the AWS account and Region\.

## Copy a snapshot<a name="ebs-snapshot-copy"></a>

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
   + **Master Key**: The customer master key \(CMK\) to be used to encrypt this snapshot\. The default key for your account is displayed initially, but you can optionally select from the master keys in your account or type/paste the ARN of a key from a different account\. You can create new master encryption keys in the [AWS KMS console](https://console.aws.amazon.com/kms)\. 

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

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [copy\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-snapshot.html) \(AWS CLI\)
+ [Copy\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Copy-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)
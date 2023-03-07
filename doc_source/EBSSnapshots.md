# Amazon EBS snapshots<a name="EBSSnapshots"></a>

You can back up the data on your Amazon EBS volumes to Amazon S3 by taking point\-in\-time snapshots\. Snapshots are *incremental* backups, which means that only the blocks on the device that have changed after your most recent snapshot are saved\. This minimizes the time required to create the snapshot and saves on storage costs by not duplicating data\. Each snapshot contains all of the information that is needed to restore your data \(from the moment when the snapshot was taken\) to a new EBS volume\. 

When you create an EBS volume based on a snapshot, the new volume begins as an exact replica of the original volume that was used to create the snapshot\. The replicated volume loads data in the background so that you can begin using it immediately\. If you access data that hasn't been loaded yet, the volume immediately downloads the requested data from Amazon S3, and then continues loading the rest of the volume's data in the background\. For more information, see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\.

When you delete a snapshot, only the data unique to that snapshot is removed\. For more information, see [Delete an Amazon EBS snapshot](ebs-deleting-snapshot.md)\.

**Snapshot events**  
You can track the status of your EBS snapshots through CloudWatch Events\. For more information, see [EBS snapshot events](ebs-cloud-watch-events.md#snapshot-events)\.

**Multi\-volume snapshots**  
Snapshots can be used to create a backup of critical workloads, such as a large database or a file system that spans across multiple EBS volumes\. Multi\-volume snapshots allow you to take exact point\-in\-time, data coordinated, and crash\-consistent snapshots across multiple EBS volumes attached to an EC2 instance\. You are no longer required to stop your instance or to coordinate between volumes to ensure crash consistency, because snapshots are automatically taken across multiple EBS volumes\. For more information, see the steps for creating a multi\-volume EBS snapshot under [Create Amazon EBS snapshots](ebs-creating-snapshot.md) \.

**Snapshot pricing**  
Charges for your snapshots are based on the amount of data stored\. Because snapshots are incremental, deleting a snapshot might not reduce your data storage costs\. Data referenced exclusively by a snapshot is removed when that snapshot is deleted, but data referenced by other snapshots is preserved\. For more information, see [Amazon Elastic Block Store Volumes and Snapshots](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/checklistforunwantedcharges.html#checkebsvolumes) in the *AWS Billing User Guide*\.

**Topics**
+ [How snapshots work](#how_snapshots_work)
+ [Copy and share snapshots](#copy-and-share)
+ [Encryption support for snapshots](#encryption-support)
+ [Create Amazon EBS snapshots](ebs-creating-snapshot.md)
+ [Delete an Amazon EBS snapshot](ebs-deleting-snapshot.md)
+ [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)
+ [Archive Amazon EBS snapshots](snapshot-archive.md)
+ [View Amazon EBS snapshot information](ebs-describing-snapshots.md)
+ [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)
+ [Recover snapshots from the Recycle Bin](recycle-bin-working-with-snaps.md)
+ [Amazon EBS local snapshots on Outposts](snapshots-outposts.md)
+ [Use EBS direct APIs to access the contents of an EBS snapshot](ebs-accessing-snapshot.md)
+ [Automate the snapshot lifecycle](automating-snapshots.md)

## How snapshots work<a name="how_snapshots_work"></a>

The first snapshot that you create from a volume is always a *full snapshot*\. It includes all of the data blocks written to the volume at the time of creating the snapshot\. Subsequent snapshots of the same volume are *incremental snapshots*\. They include only changed and new data blocks written to the volume since the last snapshot was created

The size of a full snapshot is determined by the size of the data being backed up, not the size of the source volume\. Similarly, the storage costs associated with a full snapshot is determined by the size of the snapshot, not the size of the source volume\. For example, you create the first snapshot of a `200 GiB` Amazon EBS volume that contains only `50 GiB` of data\. This results in a full snapshot that is `50 GiB` in size, and you are billed for `50 GiB` snapshot storage\.

Similarly, the size and storage costs of an incremental snapshot are determined by the size of any data that was written to the volume since the previous snapshot was created\. Continuing this example, if you create a second snapshot of the `200 GiB` volume after changing `20 GiB` of data and adding `10 GiB` of data, the incremental snapshot is `30 GiB` in size\. You are then billed for that additional `30 GiB` snapshot storage\.

For more information about snapshot pricing, see [ Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

**Important**  
When you archive an incremental snapshot, it is converted to a full snapshot that includes all of the blocks written to the volume at the time that the snapshot was created\. It is then moved to the Amazon EBS Snapshots Archive tier\. Snapshots in the archive tier are billed at a different rate from snapshots in the standard tier\. For more information, see [Pricing and billing](snapshot-archive.md#snapshot-archive-pricing)\.

The following sections show how an EBS snapshot captures the state of a volume at a point in time, and how subsequent snapshots of a changing volume create a history of those changes\.

 **Multiple snapshots of the same volume** 

The diagram in this section shows Volume 1, which is `15 GiB` in size, at three points in time\. A snapshot is taken of each of these three volume states\. The diagram specifically shows the following:
+ In **State 1**, the volume has `10 GiB` of data\. **Snap A** is the first snapshot taken of the volume\. **Snap A** is a full snapshot and the entire `10 GiB` of data is backed up\.
+ In **State 2**, the volume still contains `10 GiB` of data, but only `4 GiB` have changed after **Snap A** was taken\. **Snap B** is an incremental snapshot\. It needs to back up only the `4 GiB` that changed\. The other `6 GiB` of unchanged data, which are already backed up in **Snap A**, are *referenced* by **Snap B** rather than being backed up again\. This is indicated by the dashed arrow\.
+ In **State 3**, `2 GiB` of data have been added to the volume, for a total of `12 GiB`, after **Snap B** was taken\. **Snap C** is an incremental snapshot\. It needs to back up only the `2 GiB` that were added after **Snap B** was taken\. As shown by the dashed arrows, **Snap C** also references the `4 GiB` of data stored in **Snap B**, and the `6 GiB` of data stored in **Snap A**\.
+ The total storage required for the three snapshots is `16 GiB` total\. This accounts for 10 GiB for Snap A, 4 GiB for Snap B, and 2 GiB for Snap C\.

![\[Snapshots capturing an initial volume state and two subsequent states after data has been changed.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot_1a.png)

 **Incremental snapshots of different volumes** 

The diagram in this section shows how incremental snapshots can be taken from different volumes\.

1. **Vol 1**, which is `14 GiB` in size, has `10 GiB` of data\. Because **Snap A** is the first snapshot taken of the volume, it is a full snapshot and the entire `10 GiB` of data is backed up\.

1. **Vol 2** is created from **Snap A**, so it is an exact replica of **Vol 1** at the time the snapshot was taken\.

1. Over time, `4 GiB` of data is added to **Vol 2** and the total size of its data is `14 GiB`\.

1. **Snap B** is taken from **Vol 2**\. For **Snap B**, only the `4 GiB` of data that was added after the volume was created from **Snap A** is backed up\. The other `10 GiB` of unchanged data, which is already stored in **Snap A**, is referenced by **Snap B** instead of being backed up again\.

   **Snap B** is an incremental snapshot of **Snap A**, even though it was created from a different volume\.

**Important**  
The diagram assumes that you own **Vol 1** and **Snap A**, and that **Vol 2** is encrypted with the same KMS key as Vol 1\. If **Vol 1** was owned by another AWS account and that account took **Snap A** and shared it with you, then **Snap B** would be a full snapshot\. Or, if **Vol 2** was encrypted with a different KMS key than **Vol 1**, then **Snap B** would be a full snapshot\.

![\[Snapshots capturing an initial volume state and two subsequent states after data has been changed.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot_1c.png)

For more information about how data is managed when you delete a snapshot, see [Delete an Amazon EBS snapshot](ebs-deleting-snapshot.md)\.

## Copy and share snapshots<a name="copy-and-share"></a>

You can share a snapshot across AWS accounts by modifying its access permissions\. You can make copies of your own snapshots as well as snapshots that have been shared with you\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

A snapshot is constrained to the AWS Region where it was created\. After you create a snapshot of an EBS volume, you can use it to create new volumes in the same Region\. For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\. You can also copy snapshots across Regions, making it possible to use multiple Regions for geographical expansion, data center migration, and disaster recovery\. You can copy any accessible snapshot that has a `completed` status\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.

## Encryption support for snapshots<a name="encryption-support"></a>

EBS snapshots fully support EBS encryption\.
+ Snapshots of encrypted volumes are automatically encrypted\.
+ Volumes that you create from encrypted snapshots are automatically encrypted\.
+ Volumes that you create from an unencrypted snapshot that you own or have access to can be encrypted on\-the\-fly\.
+ When you copy an unencrypted snapshot that you own, you can encrypt it during the copy process\.
+ When you copy an encrypted snapshot that you own or have access to, you can reencrypt it with a different key during the copy process\.
+ The first snapshot you take of an encrypted volume that has been created from an unencrypted snapshot is always a full snapshot\.
+ The first snapshot you take of a reencrypted volume, which has a different CMK compared to the source snapshot, is always a full snapshot\.

Complete documentation of possible snapshot encryption scenarios is provided in [Create Amazon EBS snapshots](ebs-creating-snapshot.md) and in [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.

For more information, see [Amazon EBS encryption](EBSEncryption.md)\.
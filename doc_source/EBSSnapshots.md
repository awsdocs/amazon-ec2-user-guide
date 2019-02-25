# Amazon EBS Snapshots<a name="EBSSnapshots"></a>

You can back up the data on your Amazon EBS volumes to Amazon S3 by taking point\-in\-time snapshots\. Snapshots are *incremental* backups, which means that only the blocks on the device that have changed after your most recent snapshot are saved\. This minimizes the time required to create the snapshot and saves on storage costs by not duplicating data\. When you delete a snapshot, only the data unique to that snapshot is removed\. Each snapshot contains all of the information needed to restore your data \(from the moment when the snapshot was taken\) to a new EBS volume\. 

When you create an EBS volume based on a snapshot, the new volume begins as an exact replica of the original volume that was used to create the snapshot\. The replicated volume loads data lazily in the background so that you can begin using it immediately\. If you access data that hasn't been loaded yet, the volume immediately downloads the requested data from Amazon S3, and then continues loading the rest of the volume's data in the background\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

You can track the status of your EBS snapshots through CloudWatch Events\. For more information, see [Amazon CloudWatch Events for Amazon EBS](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-cloud-watch-events.html)\.

**Topics**
+ [How Incremental Snapshots Work](#how_snapshots_work)
+ [Copying and Sharing Snapshots](#copy-and-share)
+ [Encryption Support for Snapshots](#encryption-support)
+ [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)
+ [Deleting an Amazon EBS Snapshot](ebs-deleting-snapshot.md)
+ [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)
+ [Viewing Amazon EBS Snapshot Information](ebs-describing-snapshots.md)
+ [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)
+ [Automating the Amazon EBS Snapshot Lifecycle](snapshot-lifecycle.md)

## How Incremental Snapshots Work<a name="how_snapshots_work"></a>

This section provides illustrations of how an EBS snapshot captures the state of a volume at a point in time, and also how successive snapshots of a changing volume create a history of those changes\.

In the diagram below, Volume 1 is shown at three points in time\. A snapshot is taken of each of these three volume states\. 
+ In State 1, the volume has 10 GiB of data\. Because Snap A is the first snapshot taken of the volume, the entire 10 GiB of data must be copied\.
+ In State 2, the volume still contains 10 GiB of data, but 4 GiB have changed\. Snap B needs to copy and store only the 4 GiB that changed after Snap A was taken\. The other 6 GiB of unchanged data, which are already copied and stored in Snap A, are *referenced* by Snap B rather than \(again\) copied\. This is indicated by the dashed arrow\.
+ In State 3, 2 GiB of data have been added to the volume, for a total of 12 GiB\. Snap C needs to copy the 2 GiB that were added after Snap B was taken\. As shown by the dashed arrows, Snap C also references the 4 GiB of data stored in Snap B, and the 6 GiB of data stored in Snap A\. 
+ The total storage required for the three snapshots is 12 GiB\.

 **Relations among Multiple Snapshots of a Volume** 

![\[Snapshots capturing an initial volume state and two subsequent states after data has been changed.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot_1a.png)

For more information about how data is managed when you delete a snapshot, see [Deleting an Amazon EBS Snapshot](ebs-deleting-snapshot.md)\.

## Copying and Sharing Snapshots<a name="copy-and-share"></a>

You can share a snapshot across AWS accounts by modifying its access permissions\. You can make copies of your own snapshots as well as snapshots that have been shared with you\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

A snapshot is constrained to the Region where it was created\. After you create a snapshot of an EBS volume, you can use it to create new volumes in the same Region\. For more information, see [Restoring an Amazon EBS Volume from a Snapshot](ebs-restoring-volume.md)\. You can also copy snapshots across regions, making it possible to use multiple regions for geographical expansion, data center migration, and disaster recovery\. You can copy any accessible snapshot that has a `completed` status\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

## Encryption Support for Snapshots<a name="encryption-support"></a>

EBS snapshots broadly support EBS encryption\.
+ Snapshots of encrypted volumes are automatically encrypted\.
+ Volumes that are created from encrypted snapshots are automatically encrypted\.
+ When you copy an unencrypted snapshot that you own, you can encrypt it during the copy process\.
+ When you copy an encrypted snapshot that you own, you can reencrypt it with a different key during the copy process\.

For more information, see [Amazon EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)\.
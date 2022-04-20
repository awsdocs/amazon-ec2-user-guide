# Delete an Amazon EBS snapshot<a name="ebs-deleting-snapshot"></a>

After you no longer need an Amazon EBS snapshot of a volume, you can delete it\. Deleting a snapshot has no effect on the volume\. Deleting a volume has no effect on the snapshots made from it\.

## Incremental snapshot deletion<a name="ebs-deleting-snapshot-incremental"></a>

If you make periodic snapshots of a volume, the snapshots are *incremental*\. This means that only the blocks on the device that have changed after your most recent snapshot are saved in the new snapshot\. Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to retain only the most recent snapshot in order to create volumes\.

If data was present on a volume held in an earlier snapshot or series of snapshots, and that data is subsequently deleted from the volume later on, the data is still considered to be unique data of the earlier snapshots\. Unique data is only deleted from the sequence of snapshots if all snapshots that reference the unique data are deleted\. 

When you delete a snapshot, only the data that is referenced exclusively by that snapshot is removed\. Unique data is only deleted if all of the snapshots that reference it are deleted\. Deleting previous snapshots of a volume does not affect your ability to create volumes from later snapshots of that volume\.

Deleting a snapshot might not reduce your organization's data storage costs\. Other snapshots might reference that snapshot's data, and referenced data is always preserved\. If you delete a snapshot containing data being used by a later snapshot, costs associated with the referenced data are allocated to the later snapshot\. For more information about how snapshots store data, see [How incremental snapshots work](EBSSnapshots.md#how_snapshots_work) and the following example\.

In the following diagram, Volume 1 is shown at three points in time\. A snapshot has captured each of the first two states, and in the third, a snapshot has been deleted\. 
+ In State 1, the volume has 10 GiB of data\. Because Snap A is the first snapshot taken of the volume, the entire 10 GiB of data must be copied\.
+ In State 2, the volume still contains 10 GiB of data, but 4 GiB have changed\. Snap B needs to copy and store only the 4 GiB that changed after Snap A was taken\. The other 6 GiB of unchanged data, which are already copied and stored in Snap A, are referenced by Snap B rather than \(again\) copied\. This is indicated by the dashed arrow\.
+ In state 3, the volume has not changed since State 2, but Snapshot A has been deleted\. The 6 GiB of data stored in Snapshot A that were referenced by Snapshot B have now been moved to Snapshot B, as shown by the heavy arrow\. As a result, you are still charged for storing 10 GiB of data; 6 GiB of unchanged data preserved from Snap A and 4 GiB of changed data from Snap B\.

**Deleting a snapshot with some of its data referenced by another snapshot**

![\[Snap A contains 6 GiB of referenced data. When Snap A is deleted, that data is merged into Snap B.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot_1b.png)

## Considerations<a name="ebs-delete-snapshot-considerations"></a>

The following considerations apply to deleting snapshots:
+ You can't delete a snapshot of the root device of an EBS volume used by a registered AMI\. You must first deregister the AMI before you can delete the snapshot\. For more information, see [Deregister your AMI](deregister-ami.md)\.
+ You can't delete a snapshot that is managed by the AWS Backup service using Amazon EC2\. Instead, use AWS Backup to delete the corresponding recovery points in the backup vault\.
+ You can create, retain, and delete snapshots manually, or you can use Amazon Data Lifecycle Manager to manage your snapshots for you\. For more information, see [Amazon Data Lifecycle Manager](snapshot-lifecycle.md)\.
+ Although you can delete a snapshot that is still in progress, the snapshot must complete before the deletion takes effect\. This might take a long time\. If you are also at your concurrent snapshot limit, and you attempt to take an additional snapshot, you might get a `ConcurrentSnapshotLimitExceeded` error\. For more information, see the [Service Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#limits_ebs) for Amazon EBS in the *Amazon Web Services General Reference*\.
+ If you delete a snapshot that matches an Recycle Bin retention rule, the snapshot is retained in the Recycle Bin instead of being immediately deleted\. For more information, see [Recycle Bin](recycle-bin.md)\.

## Delete a snapshot<a name="ebs-delete-snapshot"></a>

To delete a snapshot, use one of the following methods\. 

------
#### [ New console ]

**To delete a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot to delete, and then choose **Actions**, **Delete snapshot**\.

1. Choose **Delete**\.

------
#### [ Old console ]

**To delete a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** in the navigation pane\. 

1. Select a snapshot and then choose **Delete** from the **Actions** list\.

1. Choose **Yes, Delete**\.

------
#### [ AWS CLI ]

**To delete a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-snapshot.html) \(AWS CLI\)
+ [Remove\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)

------

## Delete a multi\-volume snapshot<a name="ebs-delete-snapshot-multi-volume"></a>

To delete multi\-volume snapshots, retrieve all of the snapshots for your multi\-volume snapshot set using the tag you applied to the set when you created the snapshots\. Then, delete the snapshots individually\.

You will not be prevented from deleting individual snapshots in the multi\-volume snapshot set\. If you delete a snapshot while it is in the `pending state`, only that snapshot is deleted\. The other snapshots in the multi\-volume snapshot set still complete successfully\.
# Deleting an Amazon EBS Snapshot<a name="ebs-deleting-snapshot"></a>

When you delete a snapshot, only the data referenced exclusively by that snapshot is removed\. Deleting previous snapshots of a volume does not affect your ability to restore volumes from later snapshots of that volume\. 

Deleting a snapshot of a volume has no effect on the volume\. Deleting a volume has no effect on the snapshots made from it\.

If you make periodic snapshots of a volume, the snapshots are *incremental*\. This means that only the blocks on the device that have changed after your last snapshot are saved in the new snapshot\. Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to retain only the most recent snapshot in order to restore the volume\.

Deleting a snapshot might not reduce your organization's data storage costs\. Other snapshots might reference that snapshot's data, and referenced data is always preserved\. If you delete a snapshot containing data being used by a later snapshot, costs associated with the referenced data are allocated to the later snapshot\. For more information about how snapshots store data, see [How Incremental Snapshots Work](EBSSnapshots.md#how_snapshots_work) and the example below\.

To delete multi\-volume snapshots, retrieve all of the snapshots for your multi\-volume group using the tag you applied to the group when you created the snapshots\. Then, delete the snapshots individually\. You will not be prevented from deleting individual snapshots in the multi\-volume snapshots group\.

In the following diagram, Volume 1 is shown at three points in time\. A snapshot has captured each of the first two states, and in the third, a snapshot has been deleted\. 
+ In State 1, the volume has 10 GiB of data\. Because Snap A is the first snapshot taken of the volume, the entire 10 GiB of data must be copied\.
+ In State 2, the volume still contains 10 GiB of data, but 4 GiB have changed\. Snap B needs to copy and store only the 4 GiB that changed after Snap A was taken\. The other 6 GiB of unchanged data, which are already copied and stored in Snap A, are referenced by Snap B rather than \(again\) copied\. This is indicated by the dashed arrow\.
+ In state 3, the volume has not changed since State 2, but Snapshot A has been deleted\. The 6 GiB of data stored in Snapshot A that were referenced by Snapshot B have now been moved to Snapshot B, as shown by the heavy arrow\. As a result, you are still charged for storing 10 GiB of data; 6 GiB of unchanged data preserved from Snap A and 4 GiB of changed data from Snap B\.

 **Example 1: Deleting a Snapshot with Some of its Data Referenced by Another Snapshot**

![\[Snap A contains 6 GiB of referenced data. When Snap A is deleted, that data is merged into Snap B.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/snapshot_1b.png)

Note that you can't delete a snapshot of the root device of an EBS volume used by a registered AMI\. You must first deregister the AMI before you can delete the snapshot\. For more information, see [Deregistering Your Linux AMI](deregister-ami.md)\.

**To delete a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** in the navigation pane\. 

1. Select a snapshot and then choose **Delete** from the **Actions** list\.

1. Choose **Yes, Delete**\. 

**To delete a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-snapshot.html) \(AWS CLI\)
+ [Remove\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)

**Note**  
Although you can delete a snapshot that is still in progress, the snapshot must complete before the deletion takes effect\. This may take a long time\. If you are also at your concurrent snapshot limit \(five snapshots in progress\), and you attempt to take an additional snapshot, you may get the `ConcurrentSnapshotLimitExceeded` error\.
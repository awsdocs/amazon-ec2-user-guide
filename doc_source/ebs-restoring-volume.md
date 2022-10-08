# Replace a volume using a previous snapshot<a name="ebs-restoring-volume"></a>

Amazon EBS snapshots are the preferred backup tool on Amazon EC2 because of their speed, convenience, and cost\. When creating a volume from a snapshot, you recreate its state at a specific point in time with the data saved up to that specific point intact\. By attaching a volume created from a snapshot to an instance, you can duplicate data across Regions, create test environments, replace a damaged or corrupted production volume in its entirety, or retrieve specific files and directories and transfer them to another attached volume\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

You can use one of the following procedure to replace an Amazon EBS volume with another volume created from a previous snapshot of that volume\. You must detach the current volume and then attach the new volume\.

**Note**  
Amazon EBS volumes can only be attached to instances in the same Availability Zone\.

------
#### [ Console ]

**To replace a volume**

1. Create a volume from the snapshot and write down the ID of the new volume\. For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

1. On the Instances page, select the instance on which to replace the volume and write down the instance ID\.

   With the instance still selected, choose the **Storage** tab\. In the **Block devices** section, find the volume to replace and write down the device name for the volume, for example `/dev/sda1`\.

   Choose the volume ID\.

1. On the Volumes screen, select the volume and choose **Actions**, **Detach volume**, **Detach**\.

1. Select the new volume that you created in step 1 and choose **Actions**, **Attach volume**\.

   For **Instance** and **Device name**, enter the instance ID and device name that you wrote down in Step 2, and then choose **Attach volume**\.

1. Connect to your instance and mount the volume\. For more information, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.

------
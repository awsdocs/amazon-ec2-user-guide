# Replace an Amazon EBS volume using a previous snapshot<a name="ebs-restoring-volume"></a>

Amazon EBS snapshots are the preferred backup tool on Amazon EC2 due to their speed, convenience, and cost\. When creating a volume from a snapshot, you recreate its state at a specific point in the past with all data intact\. By attaching a volume created from a snapshot to an instance, you can duplicate data across Regions, create test environments, replace a damaged or corrupted production volume in its entirety, or retrieve specific files and directories and transfer them to another attached volume\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

You can use the following procedure to replace an EBS volume with another volume created from a previous snapshot of that volume\. You must detach the current volume and then attach the new volume\.

Note that EBS volumes can only be attached to EC2 instances in the same Availability Zone\.

**To replace a volume**

1. Create a volume from the snapshot and write down the ID of the new volume\. For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

1. On the volumes page, select the check box for the volume to replace\. On the **Description** tab, find **Attachment information** and write down the device name of the volume \(for example, `/dev/sda1` or `/dev/xvda` for a root volume, or `/dev/sdb` or `xvdb`\) and the ID of the instance\. 

1. \(Optional\) Before you can detach the root volume of an instance, you must stop the instance\. If you are not replacing the root volume, you can continue to the next step without stopping the instance\. Otherwise, to stop the instance, from **Attachment information**, hover over the instance ID, right\-click, and open the instance in a new browser tab\. Choose **Instance state**, **Stop instance**\. Leave the tab with the instances page open and return to the browser tab with the volumes page\.

1. With the volume still selected, choose **Actions**, **Detach Volume**\. When prompted for confirmation, choose **Yes, Detach**\. Clear the check box for this volume\.

1. Select the check box for the new volume that you created in step 1\. Choose **Actions**, **Attach Volume**\. Enter the instance ID and device name that you wrote down in step 2, and then choose **Attach**\.

1. \(Optional\) If you stopped the instance, you must restart it\. Return to the browser tab with the instances page and choose **Instance state**, **Start instance**\.

1. Connect to your instance and mount the volume\. For more information, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.
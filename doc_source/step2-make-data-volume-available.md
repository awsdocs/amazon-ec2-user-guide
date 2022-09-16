# Step 2: Make the data volume available for use<a name="step2-make-data-volume-available"></a>

The Amazon EC2 instance launched in [Step 1](step1-launch-instance-with-ebs-volume.md) has two EBS volumes—a root device and an added storage device \(the data volume\)\. Use the following procedure to make the data volume available for use\. In this tutorial, you use the XFS file system\.

**Note**  
The instance type \(`t3.micro`\) used in this tutorial is built on the [Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances), which exposes EBS volumes as NVMe block devices\.

For more information about making a volume available, see [Make an Amazon EBS volume available for use on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html)\.

**To format and mount the data volume**

1. In the Amazon EC2 console, in the navigation pane, choose **Instances**, and select `tutorial-volumes`\.

1.  To connect to your instance, choose **Connect**, ensure **EC2 Instance Connect** is selected, and then choose **Connect**\.

1. In the terminal window, use the following command to view your available disk devices and their mount points\. The output of **lsblk** removes the `/dev/` prefix from full device paths\.

   ```
   [ec2-user ~]$ sudo lsblk -f
   ```

   The following example output shows that there are two devices attached to the instance—the root device `/dev/nvme0n1` and the data volume `/dev/nvme1n1`\. In this example, you can see under the column `FSTYPE` that the data volume does not have a file system\.   
![\[Display of file systems on an EC2 instance storage volume using EC2 Instance Connect.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/filesystem-on-data-volume.png)

1. To create a file system, use the following command with the data volume name \(`/dev/nvme1n1`\)\.

   ```
   [ec2-user ~]$ sudo mkfs -t xfs /dev/nvme1n1
   ```

1. To create a mount point directory for the data volume, use the following command\. The mount point is where the volume is located in the file system tree and where you read and write files to after you mount the volume\. The following example creates a directory named `/data`\.

   ```
   [ec2-user ~]$ sudo mkdir /data
   ```

1. To mount the data volume at the directory you just created, use the following command with the data volume name \(`/dev/nvme1n1`\) and the mount point directory name \(`/data`\)\.

   ```
   [ec2-user ~]$ sudo mount /dev/nvme1n1 /data
   ```

1. The mount point is not automatically saved after rebooting your instance\. To automatically mount the data volume after reboot, see [Automatically mount an attached volume after reboot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html)\.

1. To verify that you have formatted and mounted your data volume, run the following command again\.

   ```
   [ec2-user ~]$ lsblk -f
   ```

   The following example output shows that the data volume `/dev/nvme1n1` has an XFS file system and is mounted at the `/data` mount point\.  
![\[Display of file systems confirming data volume has a file system and is mounted.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/filesystem-formatted-mounted.png)
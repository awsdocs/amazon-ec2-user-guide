# Step 4: Extend the file system<a name="step4-extend-file-system"></a>

Once the data volume enters the `optimizing` state, you can use file system\-specific commands to extend the file system to the new, larger size\.

For more information about extending the file system, see [Extend a Linux file system after resizing a volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)\.

**To extend the file system**

1. In the Amazon EC2 console, on the EC2 Dashboard, choose **Instances**, and select `tutorial-volumes`\.

1. To connect to your instance, choose **Connect**, ensure **EC2 Instance Connect** is selected, and then choose **Connect**\.

1.  In the terminal window, use the following command to get the size of the file system\.

   ```
   [ec2-user ~]$ df -hT
   ```

   The following example output shows that the file system size of the data volume `/dev/nvme1n1` is 8 GB\. In the previous procedure, you increased the data volume size to 16 GB\. Now you need to extend the file system to take advantage of the added storage\.  
![\[Display of file systems showing data volume file system size has not been increased.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/extend-file-system-disk-space.png)

1. Use the following command to extend the XFS file system that is mounted on the `/data` mount point\.

   ```
   [ec2-user ~]$ sudo xfs_growfs -d /data
   ```

1. Use the following command again to verify that the file system has been extended\.

   ```
   [ec2-user ~]$ df -hT
   ```

   The following example output shows that the file system size of `/dev/nvme1n1` is the same as the data volume size\.  
![\[Display of file systems showing data volume file system size has been increased.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/file-system-extended-disk-space.png)
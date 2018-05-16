# Modifying an EBS Volume from the Console<a name="console-modify"></a>

The following procedure shows how to apply available volume modifications from the Amazon EC2 console\.<a name="console-modify-size"></a>

**To modify an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Volumes**, select the volume to modify, and then choose **Actions**, **Modify Volume**\.

1. The **Modify Volume** window displays the volume ID and the volume's current configuration, including type, size, and IOPS\. You can change any or all of these settings in a single action\. Set new configuration values as follows:
   + To modify the type, choose a value for **Volume Type**\.
   + To modify the size, enter an allowed integer value for **Size**\.
   + If you chose **Provisioned IOPS \(IO1\)** as your volume type, enter an allowed integer value for **IOPS**\.

1. After you have specified all of the modifications to apply, choose **Modify**, **Yes**\. 

1. Modifying volume size has no practical effect until you also extend the volume's file system to make use of the new storage capacity\. For more information, see [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.
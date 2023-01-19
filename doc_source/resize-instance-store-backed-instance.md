# Change the instance type of an instance store\-backed instance<a name="resize-instance-store-backed-instance"></a>

An instance store\-backed instance is an instance that has an instance store root volume\. You can't change the instance type of an instance that has an instance store root volume\. Instead, you must create an AMI from your instance, launch a new instance from this AMI and select the instance type that you want, and then migrate your application to the new instance\. Note that the instance type that you want must be compatible with the AMI you create\. For information about how compatibility is determined, see [Compatibility for changing the instance type](resize-limitations.md)\.

To migrate your application to a new instance, do the following:
+ Back up the data on your original instance\.
+ Create an AMI from your original instance\.
+ Launch a new instance from this AMI and select the instance type that you want\.
+ Install your application on the new instance\.
+ If your original instance has an Elastic IP address, and you want to ensure that your users can continue uninterrupted to use the applications on your new instance, you must associate the Elastic IP address with your new instance\. For more information, see [Elastic IP address](elastic-ip-addresses-eip.md)\.

**To change the instance type of an instance store\-backed instance**

1. Back up data that you need to keep, as follows:
   + For data on your instance store volumes, back up the data to persistent storage\.
   + For data on your EBS volumes, [take a snapshot of the volumes](ebs-creating-snapshot.md) or [detach the volume from the instance](ebs-detaching-volume.md) so that you can attach it to the new instance later\.

1. Create an AMI from your instance by satisfying the prerequisites and following the procedures in [Create an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. When you are finished creating an AMI from your instance, return to this procedure\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**\. From the filter lists, choose **Owned by me**, and select the image that you created in Step 2\. Notice that **AMI name** is the name that you specified when you registered the image and **Source** is your Amazon S3 bucket\.
**Note**  
If you do not see the AMI that you created in Step 2, make sure that you have selected the Region in which you created your AMI\.

1. With the AMI selected, choose **Launch instance from image**\. When you configure the instance, do the following:

   1. Select the new instance type that you want\. If the instance type that you want isn't available, then it's not compatible with the configuration of the AMI that you created\. For more information, see [Compatibility for changing the instance type](resize-limitations.md)\.

   1. If you're using an Elastic IP address, select the VPC that the original instance is currently running in\.

   1. If you want to allow the same traffic to reach the new instance, select the security group that is associated with the original instance\.

   1. When you're done configuring your new instance, complete the steps to select a key pair and launch your instance\. It can take a few minutes for the instance to enter the `running` state\.

1. If required, [attach any new EBS volumes](ebs-attaching-volume.md) based on the snapshots that you created, or any EBS volumes that you detached from the original instance, to the new instance\.

1. Install your application and any required software on the new instance\.

1. If you are using an Elastic IP address, assign it to the new instance as follows:

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Select the Elastic IP address that is associated with the original instance and choose **Actions**, **Disassociate Elastic IP address**\. When prompted for confirmation, choose **Disassociate**\.

   1. With the Elastic IP address still selected, choose **Actions**, **Associate Elastic IP address**\.

   1. For **Resource type**, choose **Instance**\. 

   1. For **Instance**, choose the new instance with which to associate the Elastic IP address\.

   1. \(Optional\) For **Private IP address**, specify a private IP address with which to associate the Elastic IP address\.

   1. Choose **Associate**\.

1. \(Optional\) You can terminate the original instance if it's no longer needed\. Select the instance, verify that you are about to terminate the original instance and not the new instance \(for example, check the name or launch time\), and then choose **Instance state**, **Terminate instance**\.
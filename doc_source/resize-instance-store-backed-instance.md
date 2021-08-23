# Migrate an instance store\-backed instance<a name="resize-instance-store-backed-instance"></a>

You can't change the instance type of an instance store\-back instance\. Instead, you must migrate your application to a new instance that is configured with the instance type that you need\. To migrate your application to a new instance, you must create an image from your original instance, and then launch a new instance from this image with the instance type that you need\. To ensure that your users can continue to use the applications that you're hosting on your instance uninterrupted, you must take any Elastic IP address that you've associated with your original instance and associate it with the new instance\. Then you can terminate the original instance\.

------
#### [ New console ]

**To migrate an instance store\-backed instance**

1. Back up any data on your instance store volumes that you need to keep to persistent storage\. To migrate data on your EBS volumes that you need to keep, take a snapshot of the volumes \(see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\) or detach the volume from the instance so that you can attach it to the new instance later \(see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\)\.

1. Create an AMI from your instance store\-backed instance by satisfying the prerequisites and following the procedures in [Create an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. When you are finished creating an AMI from your instance, return to this procedure\.

1. Open the Amazon EC2 console and in the navigation pane, choose **AMIs**\. From the filter lists, choose **Owned by me**, and select the image that you created in the previous step\. Notice that **AMI Name** is the name that you specified when you registered the image and **Source** is your Amazon S3 bucket\.
**Note**  
If you do not see the AMI that you created in the previous step, make sure that you have selected the Region in which you created your AMI\.

1. Choose **Launch**\. When you specify options for the instance, be sure to select the new instance type that you want\. If the instance type that you want can't be selected, then it is not compatible with configuration of the AMI that you created \(for example, because of virtualization type\)\. You can also specify any EBS volumes that you detached from the original instance\.

   It can take a few minutes for the instance to enter the `running` state\.

1. \(Optional\) You can terminate the instance that you started with, if it's no longer needed\. Select the instance and verify that you are about to terminate the original instance, not the new instance \(for example, check the name or launch time\)\. Choose **Instance state**, **Terminate instance**\.

------
#### [ Old console ]

**To migrate an instance store\-backed instance**

1. Back up any data on your instance store volumes that you need to keep to persistent storage\. To migrate data on your EBS volumes that you need to keep, take a snapshot of the volumes \(see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\) or detach the volume from the instance so that you can attach it to the new instance later \(see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\)\.

1. Create an AMI from your instance store\-backed instance by satisfying the prerequisites and following the procedures in [Create an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. When you are finished creating an AMI from your instance, return to this procedure\.

1. Open the Amazon EC2 console and in the navigation pane, choose **AMIs**\. From the filter lists, choose **Owned by me**, and choose the image that you created in the previous step\. Notice that **AMI Name** is the name that you specified when you registered the image and **Source** is your Amazon S3 bucket\.
**Note**  
If you do not see the AMI that you created in the previous step, make sure that you have selected the Region in which you created your AMI\.

1. Choose **Launch**\. When you specify options for the instance, be sure to select the new instance type that you want\. If the instance type that you want can't be selected, then it is not compatible with configuration of the AMI that you created \(for example, because of virtualization type\)\. You can also specify any EBS volumes that you detached from the original instance\.

   It can take a few minutes for the instance to enter the `running` state\.

1. \(Optional\) You can terminate the instance that you started with, if it's no longer needed\. Select the instance and verify that you are about to terminate the original instance, not the new instance \(for example, check the name or launch time\)\. Choose **Actions**, **Instance State**, **Terminate**\.

------
# Tutorial: Increase the size of an Amazon EBS volume on an EC2 instance<a name="modify-ebs-volume-on-instance"></a>

This tutorial shows you how to launch an Amazon EC2 instance and add an Amazon Elastic Block Store \(Amazon EBS\) volume using the EC2 launch instance wizard\. The added volume must be formatted with a file system and mounted to be available for use\. Suppose, after using this configuration for six months, your added volume is full\. This tutorial shows you how to double the size of the added volume, and extend the file system\.

This tutorial uses an [Amazon EC2 T3 instance](http://aws.amazon.com/ec2/instance-types/t3/) which is a low cost general purpose instance type based on the [Nitro System](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#ec2-nitro-instances)\.

Consider the following as you complete the steps in this tutorial:
+ **Two volumes** – After you launch your Amazon EC2 instance you have two volumes—a root volume and an added volume\. To differentiate between these volumes, the added volume will be called a data volume in this tutorial\.
+ **Data volume availability** – To make the data volume available, you need to format and mount the data volume\.
+ **File system** – To take advantage of the increased size of the data volume, the file system needs to be extended\.

**Time to complete the tutorial**  
1 hour

**Costs**  
By completing this tutorial, you might incur costs for the AWS resources that you create\. You can use Amazon EC2 under the [free tier](http://aws.amazon.com/free) provided your AWS account is less than 12 months old and you configure your resources according to the free tier requirements\. After you complete the tutorial, you can delete any resources you do not need\. For the steps to delete unneeded resources, see [Step 5: Clean up unneeded resources](step5-clean-up.md)

**Topics**
+ [Step 1: Launch an EC2 instance with an added EBS volume](step1-launch-instance-with-ebs-volume.md)
+ [Step 2: Make the data volume available for use](step2-make-data-volume-available.md)
+ [Step 3: Increase the size of the data volume](step3-increase-size-of-data-volume.md)
+ [Step 4: Extend the file system](step4-extend-file-system.md)
+ [Step 5: Clean up unneeded resources](step5-clean-up.md)
# Best Practices for Amazon EC2<a name="ec2-best-practices"></a>

This checklist is intended to help you get the maximum benefit from and satisfaction with Amazon EC2\.

**Security and Network**

+ Manage access to AWS resources and APIs using identity federation, IAM users, and IAM roles\. Establish credential management policies and procedures for creating, distributing, rotating, and revoking AWS access credentials\. For more information, see [IAM Best Practices](http://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html) in the *IAM User Guide*\.

+ Implement the least permissive rules for your security group\. For more information, see [Security Group Rules](using-network-security.md#security-group-rules)\.

+ Regularly patch, update, and secure the operating system and applications on your instance\. For more information about updating Amazon Linux, see [Managing Software on Your Linux Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html)\. For more information about updating your Windows instance, see [Updating Your Windows Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#update-windows-instance) in the *Amazon EC2 User Guide for Windows Instances*\.

+ Launch your instances into a VPC instead of EC2\-Classic\. Note that if you created your AWS account after 2013\-12\-04, we automatically launch your instances into a VPC\. For more information about the benefits, see [Amazon EC2 and Amazon Virtual Private Cloud](using-vpc.md)\.

**Storage**

+ Understand the implications of the root device type for data persistence, backup, and recovery\. For more information, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\.

+ Use separate Amazon EBS volumes for the operating system versus your data\. Ensure that the volume with your data persists after instance termination\. For more information, see [Preserving Amazon EBS Volumes on Instance Termination](terminating-instances.md#preserving-volumes-on-termination)\.

+ Use the instance store available for your instance to store temporary data\. Remember that the data stored in instance store is deleted when you stop or terminate your instance\. If you use instance store for database storage, ensure that you have a cluster with a replication factor that ensures fault tolerance\.

**Resource Management**

+ Use instance metadata and custom resource tags to track and identify your AWS resources\. For more information, see [Instance Metadata and User Data](ec2-instance-metadata.md) and [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.

+ View your current limits for Amazon EC2\. Plan to request any limit increases in advance of the time that you'll need them\. For more information, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.

**Backup and Recovery**

+ Regularly back up your EBS volumes using Amazon EBS snapshots, and create an Amazon Machine Image \(AMI\) from your instance to save the configuration as a template for launching future instances\.

+ Deploy critical components of your application across multiple Availability Zones, and replicate your data appropriately\.

+ Design your applications to handle dynamic IP addressing when your instance restarts\. For more information, see [Amazon EC2 Instance IP Addressing](using-instance-addressing.md)\.

+ Monitor and respond to events\. For more information, see [Monitoring Amazon EC2](monitoring_ec2.md)\.

+ Ensure that you are prepared to handle failover\. For a basic solution, you can manually attach a network interface or Elastic IP address to a replacement instance\. For more information, see [Elastic Network Interfaces](using-eni.md)\. For an automated solution, you can use Auto Scaling\. For more information, see the [Amazon EC2 Auto Scaling User Guide](http://docs.aws.amazon.com/autoscaling/latest/userguide/)\.

+ Regularly test the process of recovering your instances and Amazon EBS volumes if they fail\.
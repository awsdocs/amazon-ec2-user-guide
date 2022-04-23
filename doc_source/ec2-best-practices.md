# Best practices for Amazon EC2<a name="ec2-best-practices"></a>

To ensure the maximum benefit from Amazon EC2, we recommend that you perform the following best practices\.

**Security**
+ Manage access to AWS resources and APIs using identity federation, IAM users, and IAM roles\. Establish credential management policies and procedures for creating, distributing, rotating, and revoking AWS access credentials\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/IAMBestPractices.html) in the *IAM User Guide*\.
+ Implement the least permissive rules for your security group\. For more information, see [Security group rules](security-group-rules.md)\.
+ Regularly patch, update, and secure the operating system and applications on your instance\. For more information about updating Amazon Linux 2 or the Amazon Linux AMI, see [Manage software on your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-software.html) in the *Amazon EC2 User Guide for Linux Instances*\.
+ Use Amazon Inspector to automatically discover and scan Amazon EC2 instances for software vulnerabilities and unintended network exposure\. For more information, see the [Amazon Inspector User Guide](https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html)\.

**Storage**
+ Understand the implications of the root device type for data persistence, backup, and recovery\. For more information, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.
+ Use separate Amazon EBS volumes for the operating system versus your data\. Ensure that the volume with your data persists after instance termination\. For more information, see [Preserve Amazon EBS volumes on instance termination](terminating-instances.md#preserving-volumes-on-termination)\.
+ Use the instance store available for your instance to store temporary data\. Remember that the data stored in instance store is deleted when you stop, hibernate, or terminate your instance\. If you use instance store for database storage, ensure that you have a cluster with a replication factor that ensures fault tolerance\.
+ Encrypt EBS volumes and snapshots\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

**Resource management**
+ Use instance metadata and custom resource tags to track and identify your AWS resources\. For more information, see [Instance metadata and user data](ec2-instance-metadata.md) and [Tag your Amazon EC2 resources](Using_Tags.md)\.
+ View your current limits for Amazon EC2\. Plan to request any limit increases in advance of the time that you'll need them\. For more information, see [Amazon EC2 service quotas](ec2-resource-limits.md)\.
+ Use AWS Trusted Advisor to inspect your AWS environment, and then make recommendations when opportunities exist to save money, improve system availability and performance, or help close security gaps\. For more information, see [AWS Trusted Advisor](https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html) in the *AWS Support User Guide*\.

**Backup and recovery**
+ Regularly back up your EBS volumes using [Amazon EBS snapshots](EBSSnapshots.md), and create an [Amazon Machine Image \(AMI\)](AMIs.md) from your instance to save the configuration as a template for launching future instances\. For more information on AWS services that help achieve this use case, see [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html) and [Amazon Data Lifecycle Manager](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshot-lifecycle.html)\.
+ Deploy critical components of your application across multiple Availability Zones, and replicate your data appropriately\.
+ Design your applications to handle dynamic IP addressing when your instance restarts\. For more information, see [Amazon EC2 instance IP addressing](using-instance-addressing.md)\.
+ Monitor and respond to events\. For more information, see [Monitor Amazon EC2](monitoring_ec2.md)\.
+ Ensure that you are prepared to handle failover\. For a basic solution, you can manually attach a network interface or Elastic IP address to a replacement instance\. For more information, see [Elastic network interfaces](using-eni.md)\. For an automated solution, you can use Amazon EC2 Auto Scaling\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/)\.
+ Regularly test the process of recovering your instances and Amazon EBS volumes to ensure data and services are restored successfully\.

**Networking**
+ Set the time\-to\-live \(TTL\) value for your applications to 255, for IPv4 and IPv6\. If you use a smaller value, there is a risk that the TTL will expire while application traffic is in transit, causing reachability issues for your instances\.
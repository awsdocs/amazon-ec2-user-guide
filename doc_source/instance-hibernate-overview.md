# Overview of hibernation<a name="instance-hibernate-overview"></a>

The following diagram shows a basic overview of the hibernation process\.

![\[Overview of the hibernation flow\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/hibernation-flow.png)

When you hibernate a running instance, the following happens:
+ When you initiate hibernation, the instance moves to the `stopping` state\. Amazon EC2 signals the operating system to perform hibernation \(suspend\-to\-disk\)\. The hibernation freezes all of the processes, saves the contents of the RAM to the EBS root volume, and then performs a regular shutdown\.
+ After the shutdown is complete, the instance moves to the `stopped` state\.
+ Any EBS volumes remain attached to the instance, and their data persists, including the saved contents of the RAM\.
+ Any Amazon EC2 instance store volumes remain attached to the instance, but the data on the instance store volumes is lost\.
+ In most cases, the instance is migrated to a new underlying host computer when it's started\. This is also what happens when you stop and start an instance\.
+ When you start the instance, the instance boots up and the operating system reads in the contents of the RAM from the EBS root volume, before unfreezing processes to resume its state\.
+ The instance retains its private IPv4 addresses and any IPv6 addresses\. When you start the instance, the instance continues to retain its private IPv4 addresses and any IPv6 addresses\.
+ Amazon EC2 releases the public IPv4 address\. When you start the instance, Amazon EC2 assigns a new public IPv4 address to the instance\.
+ The instance retains its associated Elastic IP addresses\. You're charged for any Elastic IP addresses that are associated with a hibernated instance\. With EC2\-Classic, an Elastic IP address is disassociated from your instance when you hibernate it\. For more information, see [EC2\-Classic](ec2-classic-platform.md)\.
+ When you hibernate a ClassicLink instance, it's unlinked from the VPC to which it was linked\. You must link the instance to the VPC again after starting it\. For more information, see [ClassicLink](vpc-classiclink.md)\.

For information about how hibernation differs from reboot, stop, and terminate, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.
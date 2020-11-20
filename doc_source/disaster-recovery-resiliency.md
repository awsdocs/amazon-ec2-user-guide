# Resilience in Amazon EC2<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. Regions provide multiple physically separated and isolated Availability Zones, which are connected through low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\.

If you need to replicate your data or applications over greater geographic distances, use AWS Local Zones\. An AWS Local Zone is an extension of an AWS Region in geographic proximity to your users\. Local Zones have their own connections to the internet and support AWS Direct Connect\. Like all AWS Regions, AWS Local Zones are completely isolated from other AWS Zones\.

If you need to replicate your data or applications in an AWS Local Zone, AWS recommends that you use one of the following zones as the failover zone:
+ Another Local Zone
+ An Availability Zone in the Region that is not the parent zone\. You can use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command to view the parent zone\.

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

In addition to the AWS global infrastructure, Amazon EC2 offers the following features to support your data resiliency:
+ Copying AMIs across Regions
+ Copying EBS snapshots across Regions
+ Automating EBS\-backed AMIs using Amazon Data Lifecycle Manager
+ Automating EBS snapshots using Amazon Data Lifecycle Manager
+ Maintaining the health and availability of your fleet using Amazon EC2 Auto Scaling
+ Distributing incoming traffic across multiple instances in a single Availability Zone or multiple Availability Zones using Elastic Load Balancing
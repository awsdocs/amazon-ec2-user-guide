# Burstable Performance Instances<a name="burstable-performance-instances"></a>

Burstable performance instances are designed to provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload\. Burstable performance instances are well suited for a wide range of general\-purpose applications\. Examples include microservices, low\-latency interactive applications, small and medium databases, virtual desktops, development, build, and stage environments, code repositories, and product prototypes\.

Burstable performance instances include the T2 and T3 instance types, which are the only instance types that use credits for CPU usage\. 

For more information about T2 and T3 instance pricing and additional hardware details, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

If your account is less than 12 months old, you can use a `t2.micro` instance for free within certain usage limits\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

**Topics**
+ [Hardware Specifications](#t2-instances-hardware)
+ [Burstable Performance Instance Requirements](#t2-instance-limits)
+ [Best Practices](#t2-best-practices)
+ [CPU Credits and Baseline Performance](t2-credits-baseline-concepts.md)
+ [Unlimited Mode](t2-unlimited.md)
+ [Standard Mode](t2-std.md)
+ [Working with T2 and T3 Instances](t-how-to.md)
+ [Monitoring Your CPU Credits](t2-instances-monitoring-cpu-credits.md)

## Hardware Specifications<a name="t2-instances-hardware"></a>

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Burstable Performance Instance Requirements<a name="t2-instance-limits"></a>

The following are the requirements for T2 and T3 instances:
+ You must launch your T2 and T3 instances into a virtual private cloud \(VPC\); they are not supported on the EC2\-Classic platform\. You cannot change the instance type of an existing instance in EC2\-Classic to a T2 or T3 instance type\. For more information about EC2\-Classic and EC2\-VPC, see [Supported Platforms](ec2-supported-platforms.md)\.
+ You must launch a T2 or a T3 instance using an HVM AMI with ENA and NVMe drivers\. For more information, see [Release Notes](general-purpose-instances.md#general-purpose-instances-limits)\.
+ You must launch your T2 or T3 instances using an Amazon EBS volume as the root device\. For more information, see [Amazon EC2 Root Device Volume](RootDeviceStorage.md)\.
+ T2 and T3 instances are available as On\-Demand Instances, Reserved Instances, and Spot Instances, but not as Scheduled Instances or Dedicated Instances\. They are also not supported on a Dedicated Host\. For more information, see [Instance Purchasing Options](instance-purchasing-options.md)\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. By default, you can run up to 20 T2 and 20 T3 instances simultaneously\. If you need more instances, request them using the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
+ Ensure that the T2 or T3 instance size that you choose passes the minimum memory requirements of your operating system and applications\. Operating systems with graphical user interfaces that consume significant memory and CPU resources \(for example, Windows\) may require a `t2.micro`, or larger, instance size for many use cases\. As the memory and CPU requirements of your workload grows over time, you can scale to larger T2 or T3 instance sizes, or other EC2 instance types\.

## Best Practices<a name="t2-best-practices"></a>

Follow these best practices to get the maximum benefit from and satisfaction with T2 and T3 instances\.
+ **Use a recommended AMI** — Use an AMI that provides the required drivers\. For more information, see [Release Notes](general-purpose-instances.md#general-purpose-instances-limits)\.
+ **Turn on instance recovery** — Create a CloudWatch alarm that monitors an EC2 instance and automatically recovers it if it becomes impaired for any reason\. For more information, see [Adding Recover Actions to Amazon CloudWatch Alarms](UsingAlarmActions.md#AddingRecoverActions)\.
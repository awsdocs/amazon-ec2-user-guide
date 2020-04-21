# Burstable performance instances<a name="burstable-performance-instances"></a>

Burstable performance instances, which are T3, T3a, and T2 instances, are designed to provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload\. Burstable performance instances are well suited for a wide range of general\-purpose applications\. Examples include microservices, low\-latency interactive applications, small and medium databases, virtual desktops, development, build, and stage environments, code repositories, and product prototypes\.

Burstable performance instances are the only instance types that use credits for CPU usage\. For more information about instance pricing and additional hardware details, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

If your account is less than 12 months old, you can use a `t2.micro` instance for free within certain usage limits\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

**Topics**
+ [Burstable performance instance requirements](#burstable-performance-instances-limits)
+ [Best practices](#burstable-performance-instances-best-practices)
+ [CPU credits and baseline performance for burstable performance instances](burstable-credits-baseline-concepts.md)
+ [Unlimited mode for burstable performance instances](burstable-performance-instances-unlimited-mode.md)
+ [Standard mode for burstable performance instances](burstable-performance-instances-standard-mode.md)
+ [Working with burstable performance instances](burstable-performance-instances-how-to.md)
+ [Monitoring your CPU credits](burstable-performance-instances-monitoring-cpu-credits.md)

## Burstable performance instance requirements<a name="burstable-performance-instances-limits"></a>

The following are the requirements for these instances:
+ These instances are available as On\-Demand Instances, Reserved Instances, Dedicated Instances, and Spot Instances, but not as Scheduled Instances\. They are also not supported on a Dedicated Host\. For more information, see [Instance Purchasing Options](instance-purchasing-options.md)\.
+ Ensure that the instance size you choose passes the minimum memory requirements of your operating system and applications\. Operating systems with graphical user interfaces that consume significant memory and CPU resources \(for example, Windows\) might require a `t2.micro` or larger instance size for many use cases\. As the memory and CPU requirements of your workload grow over time, you can scale to larger instance sizes of the same instance type, or another instance type\.
+ For additional requirements, see [General Purpose Instances Release Notes](general-purpose-instances.md#general-purpose-instances-limits)\.

## Best practices<a name="burstable-performance-instances-best-practices"></a>

Follow these best practices to get the maximum benefit from burstable performance instances\.
+ **Use a recommended AMI** – Use an AMI that provides the required drivers\. For more information, see [Release notes](general-purpose-instances.md#general-purpose-instances-limits)\.
+ **Turn on instance recovery** – Create a CloudWatch alarm that monitors an EC2 instance and automatically recovers it if it becomes impaired for any reason\. For more information, see [Adding Recover Actions to Amazon CloudWatch Alarms](UsingAlarmActions.md#AddingRecoverActions)\.
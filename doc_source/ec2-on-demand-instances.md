# On\-Demand Instances<a name="ec2-on-demand-instances"></a>

An On\-Demand Instance is an instance that you use *on demand*\. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it\.

There is no long\-term commitment required when you purchase On\-Demand Instances\. You pay only for the seconds that your On\-Demand Instances are in the `running` state\. The price per second for a running On\-Demand Instance is fixed, and is listed on the [On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand) page\.

We recommend that you use On\-Demand Instances for applications with short\-term, irregular workloads that cannot be interrupted\.

For significant discounts compared to On\-Demand Instances, you can use [Spot Instances](using-spot-instances.md) or [Reserved Instances](ec2-reserved-instances.md)\.

## Working With On\-Demand Instances<a name="working-with-on-demand-instances"></a>

**Tasks**
+ [Launch Your Instance](LaunchingAndUsingInstances.md)
+ [Stop and Start Your Instance](Stop_Start.md)
+ [Hibernate Your Instance](Hibernate.md)
+ [Reboot Your Instance](ec2-instance-reboot.md)
+ [Instance Retirement](instance-retirement.md)
+ [Terminate Your Instance](terminating-instances.md)
+ [Recover Your Instance](ec2-instance-recover.md)
+ [Identify EC2 Linux Instances](identify_ec2_instances.md)

If you're new to Amazon EC2, see [How to Get Started with Amazon EC2](concepts.md#how-to-get-started)\.

## On\-Demand Instance Limits<a name="ec2-on-demand-instances-limits"></a>

There is a limit to the number of running On\-Demand Instances per AWS account per Region\. Limits vary depending on instance type and purchasing option\. For more information, see [How many instances can I run in Amazon EC2?](http://aws.amazon.com/ec2/faqs/#how-many-instances-ec2)

You can view and manage your On\-Demand Instance limits from the [Limits page](https://console.aws.amazon.com/ec2/#Limits) in the Amazon EC2 console and from the Amazon EC2 [AWS services page](https://console.aws.amazon.com/servicequotas/#!/services/ec2/quotas) in the Service Quotas console\. For more information, see [Amazon EC2 Service Limits](ec2-resource-limits.md) in the *Amazon EC2 User Guide for Linux Instances* and [Viewing a Service Quota](https://docs.aws.amazon.com/servicequotas/latest/userguide/gs-request-quota.html) in the *Service Quotas User Guide*\.

**Note**  
From November 8, 2019, all AWS accounts will see On\-Demand Instance limits in number of virtual central processing units \(vCPUs\)\. With vCPU\-based limits, your limits will be managed in terms of the number of vCPUs that your running On\-Demand Instances are using, regardless of the instance type\. The current count\-based instance limits will not be available or supported after November 8, 2019\. For more information, see [EC2 On\-Demand Instance limits](http://aws.amazon.com/ec2/faqs/#EC2_On-Demand_Instance_limits)\.
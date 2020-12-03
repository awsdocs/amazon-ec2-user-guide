# Scheduled Reserved Instances<a name="ec2-scheduled-instances"></a>

**Important**  
We do not have any capacity for purchasing Scheduled Reserved Instances or any plans to make it available in the future\. To reserve capacity, use [On\-Demand Capacity Reservations](ec2-capacity-reservations.md)\. For discounted rates, use [Savings Plans](https://docs.aws.amazon.com/savingsplans/latest/userguide/)\.

Scheduled Reserved Instances \(Scheduled Instances\) enable you to purchase capacity reservations that recur on a daily, weekly, or monthly basis, with a specified start time and duration, for a one\-year term\. You reserve the capacity in advance, so that you know it is available when you need it\. You pay for the time that the instances are scheduled, even if you do not use them\.

Scheduled Instances are a good choice for workloads that do not run continuously, but do run on a regular schedule\. For example, you can use Scheduled Instances for an application that runs during business hours or for batch processing that runs at the end of the week\.

If you require a capacity reservation on a continuous basis, Reserved Instances might meet your needs and decrease costs\. For more information, see [Reserved Instances](ec2-reserved-instances.md)\. If you are flexible about when your instances run, Spot Instances might meet your needs and decrease costs\. For more information, see [Spot Instances](using-spot-instances.md)\.

**Topics**
+ [How Scheduled Instances work](#how-scheduled-instances-work)
+ [Service\-linked roles for Scheduled Instances](#service-linked-roles-scheduled-instances)
+ [Scheduled Instance limits](#scheduled-instances-limits)

## How Scheduled Instances work<a name="how-scheduled-instances-work"></a>

Amazon EC2 sets aside pools of EC2 instances in each Availability Zone for use as Scheduled Instances\. Each pool supports a specific combination of instance type, operating system, and network\.

To get started, you must search for an available schedule\. You can search across multiple pools or a single pool\. After you locate a suitable schedule, purchase it\.

You must launch your Scheduled Instances during their scheduled time periods, using a launch configuration that matches the following attributes of the schedule that you purchased: instance type, Availability Zone, network, and platform\. When you do so, Amazon EC2 launches EC2 instances on your behalf, based on the specified launch specification\. Amazon EC2 must ensure that the EC2 instances have terminated by the end of the current scheduled time period so that the capacity is available for any other Scheduled Instances it is reserved for\. Therefore, Amazon EC2 terminates the EC2 instances three minutes before the end of the current scheduled time period\.

You can't stop or reboot Scheduled Instances, but you can terminate them manually as needed\. If you terminate a Scheduled Instance before its current scheduled time period ends, you can launch it again after a few minutes\. Otherwise, you must wait until the next scheduled time period\.

The following diagram illustrates the lifecycle of a Scheduled Instance\.

![\[The Scheduled Instance lifecycle\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Scheduled_instances_lifecycle.png)

## Service\-linked roles for Scheduled Instances<a name="service-linked-roles-scheduled-instances"></a>

Amazon EC2 creates a service\-linked role when you purchase a Scheduled Instance\. A service\-linked role includes all the permissions that Amazon EC2 requires to call other AWS services on your behalf\. For more information, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2ScheduledInstances** to complete the following actions:
+ `ec2:TerminateInstances` \- Terminate Scheduled Instances after their schedules complete
+ `ec2:CreateTags` \- Add system tags to Scheduled Instances

If you purchased Scheduled Instances before October 2017, when Amazon EC2 began supporting this service\-linked role, Amazon EC2 created the **AWSServiceRoleForEC2ScheduledInstances** role in your AWS account\. For more information, see [A New Role Appeared in My Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared) in the *IAM User Guide*\.

If you no longer need to use Scheduled Instances, we recommend that you delete the **AWSServiceRoleForEC2ScheduledInstances** role\. After this role is deleted from your account, Amazon EC2 will create the role again if you purchase Scheduled Instances\.

## Scheduled Instance limits<a name="scheduled-instances-limits"></a>

Scheduled Instances are subject to the following limits:
+ The following are the only supported instance types: C3, C4, M4, and R3\.
+ The required term is 365 days \(one year\)\.
+ The minimum required utilization is 1,200 hours per year\.
+ You can purchase a Scheduled Instance up to three months in advance\.
+ They are available in the following Regions: US East \(N\. Virginia\), US West \(Oregon\), and Europe \(Ireland\)\.
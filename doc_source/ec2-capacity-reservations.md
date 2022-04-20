# On\-Demand Capacity Reservations<a name="ec2-capacity-reservations"></a>

On\-Demand Capacity Reservations enable you to reserve compute capacity for your Amazon EC2 instances in a specific Availability Zone for any duration\. This gives you the ability to create and manage Capacity Reservations independently from the billing discounts offered by Savings Plans or Regional Reserved Instances\.

By creating Capacity Reservations, you ensure that you always have access to EC2 capacity when you need it, for as long as you need it\. You can create Capacity Reservations at any time, without entering into a one\-year or three\-year term commitment, and the capacity is available immediately\. Billing starts as soon as the capacity is provisioned and the Capacity Reservation enters the active state\. When you no longer need it, cancel the Capacity Reservation to stop incurring charges\.

When you create a Capacity Reservation, you specify:
+ The Availability Zone in which to reserve the capacity
+ The number of instances for which to reserve capacity
+ The instance attributes, including the instance type, tenancy, and platform/OS

Capacity Reservations can only be used by instances that match their attributes\. By default, they are automatically used by running instances that match the attributes\. If you don't have any running instances that match the attributes of the Capacity Reservation, it remains unused until you launch an instance with matching attributes\.

In addition, you can use Savings Plans and Regional Reserved Instances with your Capacity Reservations to benefit from billing discounts\. AWS automatically applies your discount when the attributes of a Capacity Reservation match the attributes of a Savings Plan or  Regional Reserved Instance\. For more information, see [Billing discounts](capacity-reservations-pricing-billing.md#capacity-reservations-discounts)\.

**Topics**
+ [Differences between Capacity Reservations, Reserved Instances, and Savings Plans](#capacity-reservations-differences)
+ [Supported platforms](#capacity-reservations-platforms)
+ [Quotas](#capacity-reservations-limits)
+ [Limitations](#capacity-reservations-limitations)
+ [Capacity Reservation pricing and billing](capacity-reservations-pricing-billing.md)
+ [Work with Capacity Reservations](capacity-reservations-using.md)
+ [Work with Capacity Reservation groups](create-cr-group.md)
+ [Capacity Reservations in cluster placement groups](cr-cpg.md)
+ [Capacity Reservations in Local Zones](capacity-reservations-localzones.md)
+ [Capacity Reservations in Wavelength Zones](capacity-reservations-wavelengthzones.md)
+ [Capacity Reservations on AWS Outposts](capacity-reservations-outposts.md)
+ [Work with shared Capacity Reservations](capacity-reservation-sharing.md)
+ [Capacity Reservation Fleets](cr-fleets.md)
+ [CloudWatch metrics for On\-Demand Capacity Reservations](capacity-reservation-cw-metrics.md)

## Differences between Capacity Reservations, Reserved Instances, and Savings Plans<a name="capacity-reservations-differences"></a>

The following table highlights key differences between Capacity Reservations, Reserved Instances, and Savings Plans:


|  | Capacity Reservations | Zonal Reserved Instances | Regional Reserved Instances | Savings Plans | 
| --- | --- | --- | --- | --- | 
| Term | No commitment required\. Can be created and canceled as needed\. | Requires a fixed one\-year or three\-year commitment | 
| Capacity benefit | Capacity reserved in a specific Availability Zone\. | No capacity reserved\. | 
| Billing discount | No billing discount\. † | Provides a billing discount\. | 
| Instance Limits | Your On\-Demand Instance limits per Region apply\. | Default is 20 per Availability Zone\. You can request a limit increase\. | Default is 20 per Region\. You can request a limit increase\. | No limit\. | 

† You can combine Capacity Reservations with Savings Plans or Regional Reserved Instances to receive a discount\.

For more information, see the following:
+ [Reserved Instances](ec2-reserved-instances.md)
+ [Savings Plans User Guide](https://docs.aws.amazon.com/savingsplans/latest/userguide/)

## Supported platforms<a name="capacity-reservations-platforms"></a>

You must create the Capacity Reservation with the correct platform to ensure that it properly matches with your instances\. Capacity Reservations support the following platforms:
+ Linux/UNIX
+ Linux with SQL Server Standard
+ Linux with SQL Server Web
+ Linux with SQL Server Enterprise
+ SUSE Linux
+ Red Hat Enterprise Linux
+ RHEL with SQL Server Standard
+ RHEL with SQL Server Enterprise
+ RHEL with SQL Server Web
+ RHEL with HA
+ RHEL with HA and SQL Server Standard
+ RHEL with HA and SQL Server Enterprise

When you purchase a Capacity Reservation, you must specify the *platform* that represents the operating system for your instance\.
+ For SUSE Linux and RHEL distributions, excluding BYOL, you must choose the specific platform\. For example, the **SUSE Linux** or **Red Hat Enterprise Linux** platform\.
+ For all other Linux distributions \(including Ubuntu\), choose the **Linux/UNIX** platform\.
+ If you bring your existing RHEL subscription \(BYOL\), you must choose the **Linux/UNIX** platform\.

 For more information about the supported Windows platforms, see [ Supported platforms](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-capacity-reservations.html#capacity-reservations-platforms) in the *Amazon EC2 User Guide for Windows Instances*\. 

## Quotas<a name="capacity-reservations-limits"></a>

The number of instances for which you are allowed to reserve capacity is based on your account's On\-Demand Instance quota\. You can reserve capacity for as many instances as that quota allows, minus the number of instances that are already running\.

## Limitations<a name="capacity-reservations-limitations"></a>

Before you create Capacity Reservations, take note of the following limitations and restrictions\.
+ Active and unused Capacity Reservations count toward your On\-Demand Instance limits\.
+ Capacity Reservations are not transferable from one AWS account to another\. However, you can share Capacity Reservations with other AWS accounts\. For more information, see [Work with shared Capacity Reservations](capacity-reservation-sharing.md)\.
+ Zonal Reserved Instance billing discounts do not apply to Capacity Reservations\.
+ Capacity Reservations can be created in cluster placement groups\. Spread and partition placement groups are not supported\.
+ Capacity Reservations can't be used with Dedicated Hosts\.
+ Capacity Reservations do not ensure that a hibernated instance can resume after you try to start it\.
# On\-Demand Capacity Reservations<a name="ec2-capacity-reservations"></a>

On\-Demand Capacity Reservations enable you to reserve capacity for your Amazon EC2 instances in a specific Availability Zone for any duration\. This gives you the ability to create and manage capacity reservations independently from the billing discounts offered by Savings Plans or regional Reserved Instances\. By creating Capacity Reservations, you ensure that you always have access to EC2 capacity when you need it, for as long as you need it\. You can create Capacity Reservations at any time, without entering into a one\-year or three\-year term commitment, and the capacity is available immediately\. When you no longer need the reservation, cancel the Capacity Reservation to stop incurring charges for it\.

When you create a Capacity Reservation, you specify:
+ The Availability Zone in which to reserve the capacity
+ The number of instances for which to reserve capacity
+ The instance attributes, including the instance type, tenancy, and platform/OS

Capacity Reservations can only be used by instances that match their attributes\. By default, they are automatically used by running instances that match the attributes\. If you don't have any running instances that match the attributes of the Capacity Reservation, it remains unused until you launch an instance with matching attributes\.

In addition, you can use Savings Plans and regional Reserved Instances with your Capacity Reservations to benefit from billing discounts\. AWS automatically applies your discount when the attributes of a Capacity Reservation match the attributes of a Savings Plan or regional Reserved Instance\. For more information, see [Billing Discounts](capacity-reservations-pricing-billing.md#capacity-reservations-discounts)\.

**Topics**
+ [Differences Between Capacity Reservations, Reserved Instances, and Savings Plans](#capacity-reservations-differences)
+ [Capacity Reservation Limits](#capacity-reservations-limits)
+ [Capacity Reservation Limitations and Restrictions](#capacity-reservations-limitations)
+ [Capacity Reservation Pricing and Billing](capacity-reservations-pricing-billing.md)
+ [Working with Capacity Reservations](capacity-reservations-using.md)
+ [Working with Shared Capacity Reservations](capacity-reservation-sharing.md)

## Differences Between Capacity Reservations, Reserved Instances, and Savings Plans<a name="capacity-reservations-differences"></a>

The following table highlights key differences between Capacity Reservations, Reserved Instances, and Savings Plans:


****  

|  | Capacity Reservations | Zonal Reserved Instances | Regional Reserved Instances | Savings Plans | 
| --- | --- | --- | --- | --- | 
| Term | No commitment required\. Can be created and canceled as needed\. | Require fixed one\-year or three\-year commitment | 
| Capacity benefit | Capacity reserved in a specific Availability Zone\. | Do not reserve capacity in an Availability Zone\. | 
| Billing discount | No billing discount\. Instances launched into a Capacity Reservation are charged at their standard On\-Demand rates\. However, you can use Savings Plans or regional Reserved Instances with Capacity Reservations to get a billing discount\. Zonal Reserved Instances do not apply to Capacity Reservations\. | Provide billing discounts | 
| Instance Limits | Limited to your On\-Demand Instance limits per Region\. | Limited to 20 per Availability Zone\. A limit increase can be requested\. | Limited to 20 per Region\. A limit increase can be requested\. | No limits\. | 

For more information, see the following:
+ [Reserved Instances](ec2-reserved-instances.md)
+ [AWS Savings Plans User Guide](https://docs.aws.amazon.com/savingsplans/latest/userguide/)

## Capacity Reservation Limits<a name="capacity-reservations-limits"></a>

The number of instances for which you are allowed to reserve capacity is based on your account's On\-Demand Instance limit\. You can reserve capacity for as many instances as that limit allows, minus the number of instances that are already running\.

## Capacity Reservation Limitations and Restrictions<a name="capacity-reservations-limitations"></a>

Before you create Capacity Reservations, take note of the following limitations and restrictions\.
+ Active and unused Capacity Reservations count toward your On\-Demand Instance limits
+ Capacity Reservations are not transferable from one AWS account to another\. However, you can share Capacity Reservations with other AWS accounts\. For more information, see [Working with Shared Capacity Reservations](capacity-reservation-sharing.md)\.
+ Zonal Reserved Instance billing discounts do not apply to Capacity Reservations
+ Capacity Reservations can't be created in placement groups
+ Capacity Reservations can't be used with Dedicated Hosts
+ Capacity Reservations can't be used with Bring Your Own License \(BYOL\)
+ Capacity Reservations can't be used with Local Zones
# On\-Demand Capacity Reservations<a name="ec2-capacity-reservations"></a>

On\-Demand Capacity Reservations enable you to reserve capacity for your Amazon EC2 instances in a specific Availability Zone for any duration\. This gives you the ability to create and manage capacity reservations independently from the billing discounts offered by Reserved Instances \(RI\)\. By creating Capacity Reservations, you ensure that you always have access to EC2 capacity when you need it, for as long as you need it\. Capacity Reservations can be created at any time, without entering into a one\-year or three\-year term commitment, and the capacity is available immediately\. When you no longer need the reservation, cancel the Capacity Reservation to stop incurring charges for it\.

When you create a Capacity Reservation, you specify the Availability Zone in which you want to reserve the capacity, the number of instances for which you want to reserve capacity, and the instance attributes, including the instance type, tenancy, and platform/OS\. Capacity Reservations can only be used by instances that match their attributes\. By default, they are automatically used by running instances that match the attributes\. If you don't have any running instances that match the attributes of the Capacity Reservation, it remains unused until you launch an instance with matching attributes\.

In addition, you can use your Regional RIs with your Capacity Reservations to benefit from billing discounts\. This gives you the flexibility to selectively add capacity reservations and still get the Regional RI discounts for that usage\. AWS automatically applies your RI discount when the attributes of a Capacity Reservation match the attributes of an active Regional RI\.

**Topics**
+ [Differences between Capacity Reservations and RIs](#capacity-reservations-differences)
+ [Capacity Reservation Limits](#capacity-reservations-limits)
+ [Capacity Reservation Limitations and Restrictions](#capacity-reservations-limitations)
+ [Capacity Reservation Pricing and Billing](capacity-reservations-pricing-biling.md)
+ [Working with Capacity Reservations](capacity-reservations-using.md)

## Differences between Capacity Reservations and RIs<a name="capacity-reservations-differences"></a>

The following table highlights some key differences between Capacity Reservations and RIs:


****  

|  | Capacity Reservations | Zonal RIs | Regional RIs | 
| --- | --- | --- | --- | 
| Term | No commitment required\. Can be created and cancelled as needed\. | Require fixed one\-year or three\-year commitment\. | 
| Capacity benefit | Reserves capacity in a specific Availability Zone\. | Reserves capacity in a specific Availability Zone\. | Do not reserve capacity in an Availability Zone\. | 
| Billing discount | No billing discount\. Instances launched into a Capacity Reservation are charged at their standard On\-Demand rates\. However, Regional RIs can be used with Capacity Reservations to get a billing discount\. | Provide billing discounts | 
| Instance Limits | Limited to your On\-Demand Instance limits per Region | Limited to 20 per Availability Zone | Limited to 20 per Region | 

## Capacity Reservation Limits<a name="capacity-reservations-limits"></a>

The number of instances for which you are allowed to reserve capacity is based on your account's On\-Demand Instance limit\. You can reserve capacity for as many instances as that limit allows, minus the number of instances that are already running\.

## Capacity Reservation Limitations and Restrictions<a name="capacity-reservations-limitations"></a>

Before you create Capacity Reservations, take note of the following limitations and restrictions\.
+ Active and unused Capacity Reservations count towards your On\-Demand Instance limits
+ Capacity Reservations can't be shared across AWS accounts
+ Capacity Reservations are not transferable from one AWS account to another
+ Zonal RI billing discounts do not apply to Capacity Reservations
+ Capacity Reservations can't be created in Placement Groups
+ Capacity Reservations can't be used with Dedicated Hosts
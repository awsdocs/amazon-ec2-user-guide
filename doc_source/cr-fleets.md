# Capacity Reservation Fleets<a name="cr-fleets"></a>

An *On\-Demand Capacity Reservation Fleet* is a group of Capacity Reservations\.

A Capacity Reservation Fleet request contains all of the configuration information that's needed to launch a Capacity Reservation Fleet\. Using a single request, you can reserve large amounts of Amazon EC2 capacity for your workload across multiple instance types, up to a target capacity that you specify\.

After you create a Capacity Reservation Fleet, you can manage the Capacity Reservations in the fleet collectively by modifying or canceling the Capacity Reservation Fleet\.

**Topics**
+ [How Capacity Reservation Fleets work](#how-it-works)
+ [Considerations](#considerations)
+ [Pricing](#pricing)
+ [Capacity Reservation Fleet concepts](crfleet-concepts.md)
+ [Work with Capacity Reservation Fleets](work-with-cr-fleets.md)
+ [Example configurations](example-configs.md)
+ [Using Service\-Linked Roles](using-service-linked-roles.md)

## How Capacity Reservation Fleets work<a name="how-it-works"></a>

When you create a Capacity Reservation Fleet, the Fleet attempts to create individual Capacity Reservations to meet the total target capacity that you specified in the Fleet request\.

The number of instances for which the Fleet reserves capacity depends on the [*total target capacity*](crfleet-concepts.md#target-capacity) and the [*instance type weights*](crfleet-concepts.md#instance-weight) that you specify\. The instance type for which it reserves capacity depends on the [*allocation strategy*](crfleet-concepts.md#allocation-strategy) and [*instance type priorities*](crfleet-concepts.md#instance-priority) that you use\.

If there is insufficient capacity at the time the Fleet is created, and it is unable to immediately meet its total target capacity, the Fleet asynchronously attempts to create Capacity Reservations until it has reserved the requested amount of capacity\.

When the Fleet reaches its total target capacity, it attempts to maintain that capacity\. If a Capacity Reservation in the Fleet is cancelled, the Fleet automatically creates one or more Capacity Reservations, depending on your Fleet configuration, to replace the lost capacity and to maintain its total target capacity\.

The Capacity Reservations in the Fleet can't be managed individually\. They must be managed collectively by modifying the Fleet\. When you modify a Fleet, the Capacity Reservations in the Fleet are automatically updated to reflect the changes\.

Currently, Capacity Reservation Fleets support the `open` instance matching criteria, and all Capacity Reservations launched by a Fleet automatically use this instance matching criteria\. With this criteria, new instances and existing instances that have matching attributes \(instance type, platform, and Availability Zone\) automatically run in the Capacity Reservations created by a Fleet\. Capacity Reservation Fleets do not support `target` instance matching criteria\.

## Considerations<a name="considerations"></a>

Keep the following in mind when working with Capacity Reservation Fleets:
+ A Capacity Reservation Fleet can be created, modified, viewed, and cancelled using the AWS CLI and AWS API\.
+ The Capacity Reservations in a Fleet can't be managed individually\. They must be managed collectively by modifying or cancelling the Fleet\.
+ A Capacity Reservation Fleet can't span across Regions\.
+ A Capacity Reservation Fleet can't span across Availability Zones\.
+ Capacity Reservations created by a Capacity Reservation Fleet are automatically tagged with the following AWS generated tag:
  + Key — `aws:ec2-capacity-reservation-fleet`
  + Value — `fleet_id`

  You can use this tag to identify Capacity Reservations that were created by a Capacity Reservation Fleet\.

## Pricing<a name="pricing"></a>

There are no additional charges for using Capacity Reservation Fleets\. You are billed for the individual Capacity Reservations that are created by your Capacity Reservation Fleets\. For more information about how Capacity Reservations are billed, see [Capacity Reservation pricing and billing](capacity-reservations-pricing-billing.md)\.
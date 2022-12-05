# Configure EC2 Fleet for On\-Demand backup<a name="ec2-fleet-on-demand-backup"></a>

If you have urgent, unpredictable scaling needs, such as a news website that must scale during a major news event or game launch, we recommend that you specify alternative instance types for your On\-Demand Instances, in the event that your preferred option does not have sufficient available capacity\. For example, you might prefer `c5.2xlarge` On\-Demand Instances, but if there is insufficient available capacity, you'd be willing to use some `c4.2xlarge` instances during peak load\. In this case, EC2 Fleet attempts to fulfill all of your target capacity using `c5.2xlarge` instances, but if there is insufficient capacity, it automatically launches `c4.2xlarge` instances to fulfill the target capacity\.

**Topics**
+ [Prioritize instance types for On\-Demand capacity](#ec2-fleet-on-demand-priority)
+ [Use Capacity Reservations for On\-Demand Instances](#ec2-fleet-on-demand-capacity-reservations)

## Prioritize instance types for On\-Demand capacity<a name="ec2-fleet-on-demand-priority"></a>

When EC2 Fleet attempts to fulfill your On\-Demand capacity, it defaults to launching the lowest priced instance type first\. If `AllocationStrategy` is set to `prioritized`, EC2 Fleet uses priority to determine which instance type to use first in fulfilling On\-Demand capacity\. The priority is assigned to the launch template override, and the highest priority is launched first\.

**Example: Prioritize instance types**

In this example, you configure three launch template overrides, each with a different instance type\.

The On\-Demand price for the instance types range in price\. The following are the instance types used in this example, listed in order of price, starting with the least expensive instance type:
+ `m4.large` – least expensive
+ `m5.large`
+ `m5a.large`

If you do not use priority to determine the order, the fleet fulfills the On\-Demand capacity by starting with the least expensive instance type\.

However, say you have unused `m5.large` Reserved Instances that you want to use first\. You can set the launch template override priority so that the instance types are used in the order of priority, as follows:
+ `m5.large` – priority 1
+ `m4.large` – priority 2
+ `m5a.large` – priority 3

## Use Capacity Reservations for On\-Demand Instances<a name="ec2-fleet-on-demand-capacity-reservations"></a>

With On\-Demand Capacity Reservations, you can reserve compute capacity for your On\-Demand Instances in a specified Availability Zone for any duration\. You can configure an EC2 Fleet to use the Capacity Reservations first when launching On\-Demand Instances\.

Capacity Reservations are configured as either `open` or `targeted`\. EC2 Fleet can launch On\-Demand Instances into either `open` or `targeted` Capacity Reservations, as follows:
+ If a Capacity Reservation is `open`, On\-Demand Instances that have matching attributes automatically run in the reserved capacity\.
+ If a Capacity Reservation is `targeted`, On\-Demand Instances must specifically target it to run in the reserved capacity\. This is useful for using up specific Capacity Reservations or for controlling when to use specific Capacity Reservations\. 

If you use `targeted` Capacity Reservations in your EC2 Fleet, there must be enough Capacity Reservations to fulfil the target On\-Demand capacity, otherwise the launch fails\. To avoid a launch fail, rather add the `targeted` Capacity Reservations to a resource group, and then target the resource group\. The resource group doesn't need to have enough Capacity Reservations; if it runs out of Capacity Reservations before the target On\-Demand capacity is fulfilled, the fleet can launch the remaining target capacity into regular On\-Demand capacity\.

**To use Capacity Reservations with EC2 Fleet**

1. Configure the fleet as type `instant`\. You can't use Capacity Reservations for fleets of other types\.

1. Configure the usage strategy for Capacity Reservations as `use-capacity-reservations-first`\.

1. In the launch template, for **Capacity reservation**, choose either **Open** or **Target by group**\. If you choose **Target by group**, specify the Capacity Reservations resource group ID\.

When the fleet attempts to fulfil the On\-Demand capacity, if it finds that multiple instance pools have unused matching Capacity Reservations, it determines the pools in which to launch the On\-Demand Instances based on the On\-Demand allocation strategy \(`lowest-price` or `prioritized`\)\.

For examples of how to configure a fleet to use Capacity Reservations to fulfil On\-Demand capacity, see [EC2 Fleet example configurations](ec2-fleet-examples.md), specifically Examples 5 through 7\.

For information about configuring Capacity Reservations, see [On\-Demand Capacity Reservations](ec2-capacity-reservations.md) and the [On\-Demand Capacity Reservation FAQs](http://aws.amazon.com/ec2/faqs/#On-Demand_Capacity_Reservation)\.
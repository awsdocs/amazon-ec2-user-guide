# Configure EC2 Fleet for On\-Demand backup<a name="ec2-fleet-on-demand-backup"></a>

If you have urgent, unpredictable scaling needs, such as a news website that must scale during a major news event or game launch, we recommend that you specify alternative instance types for your On\-Demand Instances, in the event that your preferred option does not have sufficient available capacity\. For example, you might prefer `c5.2xlarge` On\-Demand Instances, but if there is insufficient available capacity, you'd be willing to use some `c4.2xlarge` instances during peak load\. In this case, EC2 Fleet attempts to fulfill all of your target capacity using `c5.2xlarge` instances, but if there is insufficient capacity, it automatically launches `c4.2xlarge` instances to fulfill the target capacity\.

## Prioritize instance types for On\-Demand capacity<a name="ec2-fleet-on-demand-priority"></a>

When EC2 Fleet attempts to fulfill your On\-Demand capacity, it defaults to launching the lowest\-priced instance type first\. If `AllocationStrategy` is set to `prioritized`, EC2 Fleet uses priority to determine which instance type to use first in fulfilling On\-Demand capacity\. The priority is assigned to the launch template override, and the highest priority is launched first\. 

For example, you have configured three launch template overrides, each with a different instance type: `c3.large`, `c4.large`, and `c5.large`\. The On\-Demand price for `c5.large` is less than the price for `c4.large`\. `c3.large` is the cheapest\. If you do not use priority to determine the order, the fleet fulfills On\-Demand capacity by starting with `c3.large`, and then `c5.large`\. Because you often have unused Reserved Instances for `c4.large`, you can set the launch template override priority so that the order is `c4.large`, `c3.large`, and then `c5.large`\.

## Use Capacity Reservations for On\-Demand Instances<a name="ec2-fleet-on-demand-capacity-reservations"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. You can use this setting in conjunction with the allocation strategy for On\-Demand Instances \(`lowest-price` or `prioritized`\)\.

When unused Capacity Reservations are used to fulfil On\-Demand capacity:
+ The fleet uses unused Capacity Reservations to fulfill On\-Demand capacity up to the target On\-Demand capacity\.
+ If multiple instance pools have unused Capacity Reservations, the On\-Demand allocation strategy \(`lowest-price` or `prioritized`\) is applied\.
+ If the number of unused Capacity Reservations is less than the On\-Demand target capacity, the remaining On\-Demand target capacity is launched according to the On\-Demand allocation strategy \(`lowest-price` or `prioritized`\)\.

You can only use unused On\-Demand Capacity Reservations for fleets of type `instant`\.

For examples of how to configure a fleet to use Capacity Reservations to fulfil On\-Demand capacity, see [EC2 Fleet example configurations](ec2-fleet-examples.md)\. For more information, see [On\-Demand Capacity Reservations](ec2-capacity-reservations.md) and the [On\-Demand Capacity Reservation FAQs](http://aws.amazon.com/ec2/faqs/#On-Demand_Capacity_Reservation)\.
# On\-Demand in Spot Fleet<a name="on-demand-in-spot"></a>

To ensure that you always have instance capacity, you can include a request for On\-Demand capacity in your Spot Fleet request\. In your Spot Fleet request, you specify your desired target capacity and how much of that capacity must be On\-Demand\. The balance comprises Spot capacity, which is launched if there is available Amazon EC2 capacity and availability\. For example, if in your Spot Fleet request you specify the target capacity as 10 and the On\-Demand capacity as 8, Amazon EC2 launches 8 capacity units as On\-Demand, and 2 capacity units \(10\-8=2\) as Spot\.

## Prioritize instance types for On\-Demand capacity<a name="spot-fleet-on-demand-priority"></a>

When Spot Fleet attempts to fulfill your On\-Demand capacity, it defaults to launching the lowest priced instance type first\. If `OnDemandAllocationStrategy` is set to `prioritized`, Spot Fleet uses priority to determine which instance type to use first in fulfilling On\-Demand capacity\.

The priority is assigned to the launch template override, and the highest priority is launched first\.

**Example: Prioritize instance types**

In this example, you configure three launch template overrides, each with a different instance type\.

The On\-Demand price for the instance types range in price\. The following are the instance types used in this example, listed in order of price, starting with the cheapest instance type:
+ `m4.large` – cheapest
+ `m5.large`
+ `m5a.large`

If you do not use priority to determine the order, the fleet fulfills the On\-Demand capacity by starting with the cheapest instance type\.

However, say you have unused `m5.large` Reserved Instances that you want to use first\. You can set the launch template override priority so that the instance types are used in the order of priority, as follows:
+ `m5.large` – priority 1
+ `m4.large` – priority 2
+ `m5a.large` – priority 3
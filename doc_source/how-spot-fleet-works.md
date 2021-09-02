# Spot Fleet configuration strategies<a name="how-spot-fleet-works"></a>

A *Spot Fleet* is a collection, or fleet, of Spot Instances, and optionally On\-Demand Instances\. 

The Spot Fleet attempts to launch the number of Spot Instances and On\-Demand Instances to meet the target capacity that you specified in the Spot Fleet request\. The request for Spot Instances is fulfilled if there is available capacity and the maximum price you specified in the request exceeds the current Spot price\. The Spot Fleet also attempts to maintain its target capacity fleet if your Spot Instances are interrupted\.

You can also set a maximum amount per hour that you’re willing to pay for your fleet, and Spot Fleet launches instances until it reaches the maximum amount\. When the maximum amount you're willing to pay is reached, the fleet stops launching instances even if it hasn’t met the target capacity\.

A *Spot capacity pool* is a set of unused EC2 instances with the same instance type \(for example, `m5.large`\), operating system, Availability Zone, and network platform\. When you make a Spot Fleet request, you can include multiple launch specifications, that vary by instance type, AMI, Availability Zone, or subnet\. The Spot Fleet selects the Spot capacity pools that are used to fulfill the request, based on the launch specifications included in your Spot Fleet request, and the configuration of the Spot Fleet request\. The Spot Instances come from the selected pools\.

**Topics**
+ [Plan a Spot Fleet request](plan-spot-fleet.md)
+ [Allocation strategy for Spot Instances](spot-fleet-allocation-strategy.md)
+ [On\-Demand in Spot Fleet](on-demand-in-spot.md)
+ [Capacity Rebalancing](spot-fleet-capacity-rebalance.md)
+ [Spot price overrides](spot-price-overrides.md)
+ [Control spending](spot-fleet-control-spending.md)
+ [Spot Fleet instance weighting](spot-instance-weighting.md)
# EC2 Fleet configuration strategies<a name="ec2-fleet-configuration-strategies"></a>

An *EC2 Fleet* is a group of On\-Demand Instances and Spot Instances\. 

The EC2 Fleet attempts to launch the number of instances that are required to meet the target capacity that you specify in the fleet request\. The fleet can comprise only On\-Demand Instances, only Spot Instances, or a combination of both On\-Demand Instances and Spot Instances\. The request for Spot Instances is fulfilled if there is available capacity and the maximum price per hour for your request exceeds the Spot price\. The fleet also attempts to maintain its target capacity if your Spot Instances are interrupted\.

You can also set a maximum amount per hour that you’re willing to pay for your fleet, and EC2 Fleet launches instances until it reaches the maximum amount\. When the maximum amount you're willing to pay is reached, the fleet stops launching instances even if it hasn’t met the target capacity\.

A *Spot capacity pool* is a set of unused EC2 instances with the same instance type and Availability Zone\. When you create an EC2 Fleet, you can include multiple launch specifications, which vary by instance type, Availability Zone, subnet, and maximum price\. The fleet selects the Spot capacity pools that are used to fulfill the request, based on the launch specifications included in your request, and the configuration of the request\. The Spot Instances come from the selected pools\.

An EC2 Fleet enables you to provision large amounts of EC2 capacity that makes sense for your application based on number of cores or instances, or amount of memory\. For example, you can specify an EC2 Fleet to launch a target capacity of 200 instances, of which 130 are On\-Demand Instances and the rest are Spot Instances\.

Use the appropriate configuration strategies to create an EC2 Fleet that meets your needs\.

**Topics**
+ [Plan an EC2 Fleet](plan-ec2-fleet.md)
+ [Allocation strategies for Spot Instances](ec2-fleet-allocation-strategy.md)
+ [Configure EC2 Fleet for On\-Demand backup](ec2-fleet-on-demand-backup.md)
+ [Capacity Rebalancing](ec2-fleet-capacity-rebalance.md)
+ [Maximum price overrides](ec2-fleet-price-overrides.md)
+ [Control spending](ec2-fleet-control-spending.md)
+ [EC2 Fleet instance weighting](ec2-fleet-instance-weighting.md)
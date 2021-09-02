# Spot Fleet instance weighting<a name="spot-instance-weighting"></a>

When you request a fleet of Spot Instances, you can define the capacity units that each instance type would contribute to your application's performance, and adjust your maximum price for each Spot capacity pool accordingly using *instance weighting*\.

By default, the price that you specify is *per instance hour*\. When you use the instance weighting feature, the price that you specify is *per unit hour*\. You can calculate your price per unit hour by dividing your price for an instance type by the number of units that it represents\. Spot Fleet calculates the number of Spot Instances to launch by dividing the target capacity by the instance weight\. If the result isn't an integer, the Spot Fleet rounds it up to the next integer, so that the size of your fleet is not below its target capacity\. Spot Fleet can select any pool that you specify in your launch specification, even if the capacity of the instances launched exceeds the requested target capacity\.

The following tables provide examples of calculations to determine the price per unit for a Spot Fleet request with a target capacity of 10\.


| Instance type | Instance weight | Price per instance hour | Price per unit hour | Number of instances launched | 
| --- | --- | --- | --- | --- | 
| r3\.xlarge | 2 | $0\.05 |  \.025 \(\.05 divided by 2\)  |  5 \(10 divided by 2\)  | 


| Instance type | Instance weight | Price per instance hour | Price per unit hour | Number of instances launched | 
| --- | --- | --- | --- | --- | 
| r3\.8xlarge | 8 | $0\.10 |  \.0125 \(\.10 divided by 8\)  |  2 \(10 divided by 8, result rounded up\)  | 

Use Spot Fleet instance weighting as follows to provision the target capacity that you want in the pools with the lowest price per unit at the time of fulfillment:

1. Set the target capacity for your Spot Fleet either in instances \(the default\) or in the units of your choice, such as virtual CPUs, memory, storage, or throughput\.

1. Set the price per unit\.

1. For each launch configuration, specify the weight, which is the number of units that the instance type represents toward the target capacity\.

**Instance weighting example**  
Consider a Spot Fleet request with the following configuration:
+ A target capacity of 24
+ A launch specification with an instance type `r3.2xlarge` and a weight of 6
+ A launch specification with an instance type `c3.xlarge` and a weight of 5

The weights represent the number of units that instance type represents toward the target capacity\. If the first launch specification provides the lowest price per unit \(price for `r3.2xlarge` per instance hour divided by 6\), the Spot Fleet would launch four of these instances \(24 divided by 6\)\.

If the second launch specification provides the lowest price per unit \(price for `c3.xlarge` per instance hour divided by 5\), the Spot Fleet would launch five of these instances \(24 divided by 5, result rounded up\)\.

**Instance weighting and allocation strategy**  
Consider a Spot Fleet request with the following configuration:
+ A target capacity of 30
+ A launch specification with an instance type `c3.2xlarge` and a weight of 8
+ A launch specification with an instance type `m3.xlarge` and a weight of 8
+ A launch specification with an instance type `r3.xlarge` and a weight of 8

The Spot Fleet would launch four instances \(30 divided by 8, result rounded up\)\. With the `lowestPrice` strategy, all four instances come from the pool that provides the lowest price per unit\. With the `diversified` strategy, the Spot Fleet launches one instance in each of the three pools, and the fourth instance in whichever pool provides the lowest price per unit\.
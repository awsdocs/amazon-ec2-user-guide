# EC2 Fleet instance weighting<a name="ec2-fleet-instance-weighting"></a>

When you create an EC2 Fleet, you can define the capacity units that each instance type would contribute to your application's performance\. You can then adjust your maximum price for each launch specification by using *instance weighting*\.

By default, the price that you specify is *per instance hour*\. When you use the instance weighting feature, the price that you specify is *per unit hour*\. You can calculate your price per unit hour by dividing your price for an instance type by the number of units that it represents\. EC2 Fleet calculates the number of instances to launch by dividing the target capacity by the instance weight\. If the result isn't an integer, the fleet rounds it up to the next integer, so that the size of your fleet is not below its target capacity\. The fleet can select any pool that you specify in your launch specification, even if the capacity of the instances launched exceeds the requested target capacity\.

The following table includes examples of calculations to determine the price per unit for an EC2 Fleet with a target capacity of 10\.


| Instance type | Instance weight | Target capacity | Number of instances launched | Price per instance hour | Price per unit hour | 
| --- | --- | --- | --- | --- | --- | 
| r3\.xlarge |  2  | 10 |  5 \(10 divided by 2\)  |  $0\.05  |  $0\.025 \(\.05 divided by 2\)  | 
| r3\.8xlarge |  8  | 10 |  2 \(10 divided by 8, result rounded up\)  |  $0\.10  |  $0\.0125 \(\.10 divided by 8\)  | 

Use EC2 Fleet instance weighting as follows to provision the target capacity that you want in the pools with the lowest price per unit at the time of fulfillment:

1. Set the target capacity for your EC2 Fleet either in instances \(the default\) or in the units of your choice, such as virtual CPUs, memory, storage, or throughput\.

1. Set the price per unit\.

1. For each launch specification, specify the weight, which is the number of units that the instance type represents toward the target capacity\.

**Instance weighting example**  
Consider an EC2 Fleet request with the following configuration:
+ A target capacity of 24
+ A launch specification with an instance type `r3.2xlarge` and a weight of 6
+ A launch specification with an instance type `c3.xlarge` and a weight of 5

The weights represent the number of units that instance type represents toward the target capacity\. If the first launch specification provides the lowest price per unit \(price for `r3.2xlarge` per instance hour divided by 6\), the EC2 Fleet would launch four of these instances \(24 divided by 6\)\.

If the second launch specification provides the lowest price per unit \(price for `c3.xlarge` per instance hour divided by 5\), the EC2 Fleet would launch five of these instances \(24 divided by 5, result rounded up\)\.

**Instance weighting and allocation strategy**  
Consider an EC2 Fleet request with the following configuration:
+ A target capacity of 30 Spot Instances
+ A launch specification with an instance type `c3.2xlarge` and a weight of 8
+ A launch specification with an instance type `m3.xlarge` and a weight of 8
+ A launch specification with an instance type `r3.xlarge` and a weight of 8

The EC2 Fleet would launch four instances \(30 divided by 8, result rounded up\)\. With the `lowest-price` strategy, all four instances come from the pool that provides the lowest price per unit\. With the `diversified` strategy, the fleet launches one instance in each of the three pools, and the fourth instance in whichever of the three pools provides the lowest price per unit\.
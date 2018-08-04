# How Spot Fleet Works<a name="spot-fleet"></a>

A *Spot Fleet* is a collection, or fleet, of Spot Instances, and optionally On\-Demand Instances\. 

The Spot Fleet attempts to launch the number of Spot Instances and On\-Demand Instances to meet the target capacity that you specified in the Spot Fleet request\. The request for Spot Instances is fulfilled if the Spot price exceeds the current Spot price and there is available capacity\. The Spot Fleet also attempts to maintain its target capacity fleet if your Spot Instances are interrupted due to a change in Spot prices or available capacity\.

A *Spot Instance pool* is a set of unused EC2 instances with the same instance type, operating system, Availability Zone, and network platform \(EC2\-Classic or EC2\-VPC\)\. When you make a Spot Fleet request, you can include multiple launch specifications, that vary by instance type, AMI, Availability Zone, or subnet\. The Spot Fleet selects the Spot Instance pools that are used to fulfill the request, based on the launch specifications included in your Spot Fleet request, and the configuration of the Spot Fleet request\. The Spot Instances come from the selected pools\.

**Topics**
+ [On\-Demand in Spot Fleet](#on-demand-in-spot)
+ [Allocation Strategy for Spot Instances](#spot-fleet-allocation-strategy)
+ [Spot Price Overrides](#spot-price-overrides)
+ [Spot Fleet Instance Weighting](#spot-instance-weighting)
+ [Walkthrough: Using Spot Fleet with Instance Weighting](#instance-weighting-walkthrough)

## On\-Demand in Spot Fleet<a name="on-demand-in-spot"></a>

To ensure that you always have instance capacity, you can include a request for On\-Demand capacity in your Spot Fleet request\. In your Spot Fleet request, you specify your desired target capacity and how much of that capacity must be On\-Demand\. The balance comprises Spot capacity, which is launched if there is available EC2 capacity and availability\. For example, if in your Spot Fleet request you specify target capacity as 10 and On\-Demand capacity as 8, Amazon EC2 launches 8 capacity units as On\-Demand, and 2 capacity units \(10\-8=2\) as Spot\.

### Prioritizing Instance Types for On\-Demand Capacity<a name="spot-fleet-on-demand-priority"></a>

When Spot Fleet attempts to fulfill your On\-Demand capacity, it defaults to launching the lowest\-priced instance type first\. If `OnDemandAllocationStrategy` is set to `prioritized`, Spot Fleet uses priority to determine which instance type to use first in fulfilling On\-Demand capacity\. The priority is assigned to the launch template override, and the highest priority is launched first\.

For example, you have configured three launch template overrides, each with a different instance type: `c3.large`, `c4.large`, and `c5.large`\. The On\-Demand price for `c5.large` is less than for `c4.large`\. `c3.large` is the cheapest\. If you do not use priority to determine the order, the fleet fulfills On\-Demand capacity by starting with `c3.large`, and then `c5.large`\. Because you often have unused Reserved Instances for `c4.large`, you can set the launch template override priority so that the order is `c4.large`, `c3.large`, and then `c5.large`\.

## Allocation Strategy for Spot Instances<a name="spot-fleet-allocation-strategy"></a>

The allocation strategy for the Spot Instances in your Spot Fleet determines how it fulfills your Spot Fleet request from the possible Spot Instance pools represented by its launch specifications\. The following are the allocation strategies that you can specify in your Spot Fleet request:

`lowestPrice`  
The Spot Instances come from the pool with the lowest price\. This is the default strategy\.

`diversified`  
The Spot Instances are distributed across all pools\.

`InstancePoolsToUseCount`  
The Spot Instances are distributed across the number of Spot pools that you specify\. This parameter is valid only when used in combination with `lowestPrice`\.

### Maintaining Target Capacity<a name="maintain-fleet-capacity"></a>

After Spot Instances are terminated due to a change in the Spot price or available capacity of a Spot Instance pool, a Spot Fleet of type `maintain` launches replacement Spot Instances\. If the allocation strategy is `lowestPrice`, the fleet launches replacement instances in the pool where the Spot price is currently the lowest\. If the allocation strategy is `diversified`, the fleet distributes the replacement Spot Instances across the remaining pools\. If the allocation strategy is `lowestPrice` in combination with `InstancePoolsToUseCount`, the fleet selects the Spot pools with the lowest price and launches Spot Instances across the number of Spot pools that you specify\.

### Configuring Spot Fleet for Cost Optimization<a name="spot-fleet-strategy-cost-optimization"></a>

To optimize the costs for your use of Spot Instances, specify the `lowestPrice` allocation strategy so that Spot Fleet automatically deploys the cheapest combination of instance types and Availability Zones based on the current Spot price\.

For On\-Demand Instance target capacity, Spot Fleet always selects the cheapest instance type based on the public On\-Demand price, while continuing to follow the allocation strategy \(either `lowestPrice` or `diversified`\) for Spot Instances\.

### Configuring Spot Fleet for Cost Optimization and Diversification<a name="spot-fleet-strategy-cost-optimization-and-diversified"></a>

To create a fleet of Spot Instances that is both cheap and diversified, use the `lowestPrice` allocation strategy in combination with `InstancePoolsToUseCount`\. Spot Fleet automatically deploys the cheapest combination of instance types and Availability Zones based on the current Spot price across the number of Spot pools that you specify\. This combination can be used to avoid the most expensive Spot Instances\.

### Choosing an Appropriate Allocation Strategy<a name="allocation-use-cases"></a>

You can optimize your Spot Fleets based on your use case\.

If your fleet is small or runs for a short time, the probability that your Spot Instances will be interrupted is low, even with all the instances in a single Spot Instance pool\. Therefore, the `lowestPrice` strategy is likely to meet your needs while providing the lowest cost\.

If your fleet is large or runs for a long time, you can improve the availability of your fleet by distributing the Spot Instances across multiple pools\. For example, if your Spot Fleet request specifies 10 pools and a target capacity of 100 instances, the fleet launches 10 Spot Instances in each pool\. If the Spot price for one pool exceeds your maximum price for this pool, only 10% of your fleet is affected\. Using this strategy also makes your fleet less sensitive to increases in the Spot price in any one pool over time\.

With the `diversified` strategy, the Spot Fleet does not launch Spot Instances into any pools with a Spot price that is equal to or higher than the [On\-Demand price](https://aws.amazon.com/ec2/pricing/)\.

To create a cheap and diversified fleet, use the `lowestPrice` strategy in combination with `InstancePoolsToUseCount`\. You can use a low or high number of Spot pools across which to allocate your Spot Instances\. For example, if you run batch processing, we recommend specifying a low number of Spot pools \(for example, `InstancePoolsToUseCount=2`\) to ensure that your queue always has compute capacity while maximizing savings\. If you run a web service, we recommend specifying a high number of Spot pools \(for example, `InstancePoolsToUseCount=10`\) to minimize the impact if a Spot Instance pool becomes temporarily unavailable\.

## Spot Price Overrides<a name="spot-price-overrides"></a>

Each Spot Fleet request can include a global maximum price, or use the default \(the On\-Demand price\)\. Spot Fleet uses this as the default maximum price for each of its launch specifications\.

You can optionally specify a maximum price in one or more launch specifications\. This price is specific to the launch specification\. If a launch specification includes a specific price, the Spot Fleet uses this maximum price, overriding the global maximum price\. Any other launch specifications that do not include a specific maximum price still use the global maximum price\.

## Spot Fleet Instance Weighting<a name="spot-instance-weighting"></a>

When you request a fleet of Spot Instances, you can define the capacity units that each instance type would contribute to your application's performance, and adjust your maximum price for each Spot Instance pool accordingly using *instance weighting*\.

By default, the price that you specify is *per instance hour*\. When you use the instance weighting feature, the price that you specify is *per unit hour*\. You can calculate your price per unit hour by dividing your price for an instance type by the number of units that it represents\. Spot Fleet calculates the number of Spot Instances to launch by dividing the target capacity by the instance weight\. If the result isn't an integer, the Spot Fleet rounds it up to the next integer, so that the size of your fleet is not below its target capacity\. Spot Fleet can select any pool that you specify in your launch specification, even if the capacity of the instances launched exceeds the requested target capacity\.

The following tables provide examples of calculations to determine the price per unit for a Spot Fleet request with a target capacity of 10\.


| Instance type | Instance weight | Price per instance hour | Price per unit hour | Number of instances launched | 
| --- | --- | --- | --- | --- | 
| `r3.xlarge` | 2 | $0\.05 |  \.025 \(\.05 divided by 2\)  |  5 \(10 divided by 2\)  | 


| Instance type | Instance weight | Price per instance hour | Price per unit hour | Number of instances launched | 
| --- | --- | --- | --- | --- | 
| `r3.8xlarge` | 8 | $0\.10 |  \.0125 \(\.10 divided by 8\)  |  2 \(10 divided by 8, result rounded up\)  | 

Use Spot Fleet instance weighting as follows to provision the target capacity that you want in the pools with the lowest price per unit at the time of fulfillment:

1. Set the target capacity for your Spot Fleet either in instances \(the default\) or in the units of your choice, such as virtual CPUs, memory, storage, or throughput\.

1. Set the price per unit\.

1. For each launch configuration, specify the weight, which is the number of units that the instance type represents toward the target capacity\.

**Instance Weighting Example**  
Consider a Spot Fleet request with the following configuration:
+ A target capacity of 24
+ A launch specification with an instance type `r3.2xlarge` and a weight of 6
+ A launch specification with an instance type `c3.xlarge` and a weight of 5

The weights represent the number of units that instance type represents toward the target capacity\. If the first launch specification provides the lowest price per unit \(price for `r3.2xlarge` per instance hour divided by 6\), the Spot Fleet would launch four of these instances \(24 divided by 6\)\.

If the second launch specification provides the lowest price per unit \(price for `c3.xlarge` per instance hour divided by 5\), the Spot Fleet would launch five of these instances \(24 divided by 5, result rounded up\)\.

**Instance Weighting and Allocation Strategy**  
Consider a Spot Fleet request with the following configuration:
+ A target capacity of 30
+ A launch specification with an instance type `c3.2xlarge` and a weight of 8
+ A launch specification with an instance type `m3.xlarge` and a weight of 8
+ A launch specification with an instance type `r3.xlarge` and a weight of 8

The Spot Fleet would launch four instances \(30 divided by 8, result rounded up\)\. With the `lowestPrice` strategy, all four instances come from the pool that provides the lowest price per unit\. With the `diversified` strategy, the Spot Fleet launches 1 instance in each of the three pools, and the fourth instance in whichever of the three pools provides the lowest price per unit\.

## Walkthrough: Using Spot Fleet with Instance Weighting<a name="instance-weighting-walkthrough"></a>

This walkthrough uses a fictitious company called Example Corp to illustrate the process of requesting a Spot Fleet using instance weighting\.

### Objective<a name="instance-weighting-walkthrough-objective"></a>

Example Corp, a pharmaceutical company, wants to leverage the computational power of Amazon EC2 for screening chemical compounds that might be used to fight cancer\.

### Planning<a name="instance-weighting-walkthrough-planning"></a>

Example Corp first reviews [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices)\. Next, Example Corp determines the following requirements for their Spot Fleet\.

**Instance Types**  
Example Corp has a compute\- and memory\-intensive application that performs best with at least 60 GB of memory and eight virtual CPUs \(vCPUs\)\. They want to maximize these resources for the application at the lowest possible price\. Example Corp decides that any of the following EC2 instance types would meet their needs:


| Instance type | Memory \(GiB\) | vCPUs | 
| --- | --- | --- | 
| r3\.2xlarge | 61 | 8 | 
| r3\.4xlarge | 122 | 16 | 
| r3\.8xlarge | 244 | 32 | 

**Target Capacity in Units**  
With instance weighting, target capacity can equal a number of instances \(the default\) or a combination of factors such as cores \(vCPUs\), memory \(GiBs\), and storage \(GBs\)\. By considering the base for their application \(60 GB of RAM and eight vCPUs\) as 1 unit, Example Corp decides that 20 times this amount would meet their needs\. So the company sets the target capacity of their Spot Fleet request to 20\.

**Instance Weights**  
After determining the target capacity, Example Corp calculates instance weights\. To calculate the instance weight for each instance type, they determine the units of each instance type that are required to reach the target capacity as follows:
+ r3\.2xlarge \(61\.0 GB, 8 vCPUs\) = 1 unit of 20
+ r3\.4xlarge \(122\.0 GB, 16 vCPUs\) = 2 units of 20
+ r3\.8xlarge \(244\.0 GB, 32 vCPUs\) = 4 units of 20

Therefore, Example Corp assigns instance weights of 1, 2, and 4 to the respective launch configurations in their Spot Fleet request\.

**Price Per Unit Hour**  
Example Corp uses the [On\-Demand price](https://aws.amazon.com/ec2/pricing/) per instance hour as a starting point for their price\. They could also use recent Spot prices, or a combination of the two\. To calculate the price per unit hour, they divide their starting price per instance hour by the weight\. For example:


| Instance type | On\-Demand price | Instance weight | Price per unit hour | 
| --- | --- | --- | --- | 
| r3\.2xLarge | $0\.7 | 1 | $0\.7 | 
| r3\.4xLarge | $1\.4 | 2 | $0\.7 | 
| r3\.8xLarge | $2\.8 | 4 | $0\.7 | 

Example Corp could use a global price per unit hour of $0\.7 and be competitive for all three instance types\. They could also use a global price per unit hour of $0\.7 and a specific price per unit hour of $0\.9 in the `r3.8xlarge` launch specification\.

### Verifying Permissions<a name="instance-weighting-walkthrough-permissions"></a>

Before creating a Spot Fleet request, Example Corp verifies that it has an IAM role with the required permissions\. For more information, see [Spot Fleet Prerequisites](spot-fleet-requests.md#spot-fleet-prerequisites)\.

### Creating the Request<a name="instance-weighting-walkthrough-request"></a>

Example Corp creates a file, `config.json`, with the following configuration for its Spot Fleet request:

```
{
  "SpotPrice": "0.70",
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
    {
      "ImageId": "ami-1a2b3c4d",
      "InstanceType": "r3.2xlarge",
      "SubnetId": "subnet-482e4972",
      "WeightedCapacity": 1
    },
    {
      "ImageId": "ami-1a2b3c4d",
      "InstanceType": "r3.4xlarge",
      "SubnetId": "subnet-482e4972",
      "WeightedCapacity": 2
    }
    {
      "ImageId": "ami-1a2b3c4d",
      "InstanceType": "r3.8xlarge",
      "SubnetId": "subnet-482e4972",
      "SpotPrice": "0.90",
      "WeightedCapacity": 4
    }
  ]
}
```

Example Corp creates the Spot Fleet request using the following [request\-spot\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command:

```
aws ec2 request-spot-fleet --spot-fleet-request-config file://config.json
```

For more information, see [Spot Fleet Requests](spot-fleet-requests.md)\.

### Fulfillment<a name="instance-weighting-walkthrough-fulfillment"></a>

The allocation strategy determines which Spot Instance pools your Spot Instances come from\.

With the `lowestPrice` strategy \(which is the default strategy\), the Spot Instances come from the pool with the lowest price per unit at the time of fulfillment\. To provide 20 units of capacity, the Spot Fleet launches either 20 `r3.2xlarge` instances \(20 divided by 1\), 10 `r3.4xlarge` instances \(20 divided by 2\), or 5 `r3.8xlarge` instances \(20 divided by 4\)\.

If Example Corp used the `diversified` strategy, the Spot Instances would come from all three pools\. The Spot Fleet would launch 6 `r3.2xlarge` instances \(which provide 6 units\), 3 `r3.4xlarge` instances \(which provide 6 units\), and 2 `r3.8xlarge` instances \(which provide 8 units\), for a total of 20 units\.
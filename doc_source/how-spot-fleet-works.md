# How Spot Fleet works<a name="how-spot-fleet-works"></a>

A *Spot Fleet* is a collection, or fleet, of Spot Instances, and optionally On\-Demand Instances\. 

The Spot Fleet attempts to launch the number of Spot Instances and On\-Demand Instances to meet the target capacity that you specified in the Spot Fleet request\. The request for Spot Instances is fulfilled if there is available capacity and the maximum price you specified in the request exceeds the current Spot price\. The Spot Fleet also attempts to maintain its target capacity fleet if your Spot Instances are interrupted\.

You can also set a maximum amount per hour that you’re willing to pay for your fleet, and Spot Fleet launches instances until it reaches the maximum amount\. When the maximum amount you're willing to pay is reached, the fleet stops launching instances even if it hasn’t met the target capacity\.

A *Spot capacity pool* is a set of unused EC2 instances with the same instance type \(for example, `m5.large`\), operating system, Availability Zone, and network platform\. When you make a Spot Fleet request, you can include multiple launch specifications, that vary by instance type, AMI, Availability Zone, or subnet\. The Spot Fleet selects the Spot capacity pools that are used to fulfill the request, based on the launch specifications included in your Spot Fleet request, and the configuration of the Spot Fleet request\. The Spot Instances come from the selected pools\.

**Topics**
+ [On\-Demand in Spot Fleet](#on-demand-in-spot)
+ [Allocation strategy for Spot Instances](#spot-fleet-allocation-strategy)
+ [Capacity Rebalancing](#spot-fleet-capacity-rebalance)
+ [Spot price overrides](#spot-price-overrides)
+ [Control spending](#spot-fleet-control-spending)
+ [Spot Fleet instance weighting](#spot-instance-weighting)
+ [Walkthrough: Using Spot Fleet with instance weighting](#instance-weighting-walkthrough)

## On\-Demand in Spot Fleet<a name="on-demand-in-spot"></a>

To ensure that you always have instance capacity, you can include a request for On\-Demand capacity in your Spot Fleet request\. In your Spot Fleet request, you specify your desired target capacity and how much of that capacity must be On\-Demand\. The balance comprises Spot capacity, which is launched if there is available Amazon EC2 capacity and availability\. For example, if in your Spot Fleet request you specify target capacity as 10 and On\-Demand capacity as 8, Amazon EC2 launches 8 capacity units as On\-Demand, and 2 capacity units \(10\-8=2\) as Spot\.

### Prioritize instance types for On\-Demand capacity<a name="spot-fleet-on-demand-priority"></a>

When Spot Fleet attempts to fulfill your On\-Demand capacity, it defaults to launching the lowest\-priced instance type first\. If `OnDemandAllocationStrategy` is set to `prioritized`, Spot Fleet uses priority to determine which instance type to use first in fulfilling On\-Demand capacity\. The priority is assigned to the launch template override, and the highest priority is launched first\.

For example, you have configured three launch template overrides, each with a different instance type: `c3.large`, `c4.large`, and `c5.large`\. The On\-Demand price for `c5.large` is less than for `c4.large`\. `c3.large` is the cheapest\. If you do not use priority to determine the order, the fleet fulfills On\-Demand capacity by starting with `c3.large`, and then `c5.large`\. Because you often have unused Reserved Instances for `c4.large`, you can set the launch template override priority so that the order is `c4.large`, `c3.large`, and then `c5.large`\.

## Allocation strategy for Spot Instances<a name="spot-fleet-allocation-strategy"></a>

The allocation strategy for the Spot Instances in your Spot Fleet determines how it fulfills your Spot Fleet request from the possible Spot capacity pools represented by its launch specifications\. The following are the allocation strategies that you can specify in your Spot Fleet request:

`lowestPrice`  
The Spot Instances come from the pool with the lowest price\. This is the default strategy\.

`diversified`  
The Spot Instances are distributed across all pools\.

`capacityOptimized`  
The Spot Instances come from the pools with optimal capacity for the number of instances that are launching\. You can optionally set a priority for each instance type in your fleet using `capacityOptimizedPrioritized`\. Spot Fleet optimizes for capacity first, but honors instance type priorities on a best\-effort basis\.   
With Spot Instances, pricing changes slowly over time based on long\-term trends in supply and demand, but capacity fluctuates in real time\. The `capacityOptimized` strategy automatically launches Spot Instances into the most available pools by looking at real\-time capacity data and predicting which are the most available\. This works well for workloads such as big data and analytics, image and media rendering, machine learning, and high performance computing that may have a higher cost of interruption associated with restarting work and checkpointing\. By offering the possibility of fewer interruptions, the `capacityOptimized` strategy can lower the overall cost of your workload\.  
Alternatively, you can use the `capacityOptimizedPrioritized` allocation strategy with a priority parameter to order instance types from highest to lowest priority\. You can set the same priority for different instance types\. Spot Fleet will optimize for capacity first, but will honor instance type priorities on a best\-effort basis \(for example, if honoring the priorities will not significantly affect Spot Fleet's ability to provision optimal capacity\)\. This is a good option for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set the priority for `capacityOptimizedPrioritized`, the same priority is also applied to your On\-Demand Instances if the On\-Demand `AllocationStrategy` is set to `prioritized`\.

`InstancePoolsToUseCount`  
The Spot Instances are distributed across the number of Spot pools that you specify\. This parameter is valid only when used in combination with `lowestPrice`\.

### Maintain target capacity<a name="maintain-fleet-capacity"></a>

After Spot Instances are terminated due to a change in the Spot price or available capacity of a Spot capacity pool, a Spot Fleet of type `maintain` launches replacement Spot Instances\. If the allocation strategy is `lowestPrice`, the fleet launches replacement instances in the pool where the Spot price is currently the lowest\. If the allocation strategy is `diversified`, the fleet distributes the replacement Spot Instances across the remaining pools\. If the allocation strategy is `lowestPrice` in combination with `InstancePoolsToUseCount`, the fleet selects the Spot pools with the lowest price and launches Spot Instances across the number of Spot pools that you specify\.

### Choose an appropriate allocation strategy<a name="allocation-use-cases"></a>

You can optimize your Spot Fleets based on your use case\.

If your fleet runs workloads that may have a higher cost of interruption associated with restarting work and checkpointing, then use the `capacityOptimized` strategy\. This strategy offers the possibility of fewer interruptions, which can lower the overall cost of your workload\. This is the recommended strategy\. Use the `capacityOptimizedPrioritized` strategy for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\. 

If your fleet is small or runs for a short time, the probability that your Spot Instances may be interrupted is low, even with all the instances in a single Spot capacity pool\. Therefore, the `lowestPrice` strategy is likely to meet your needs while providing the lowest cost\.

If your fleet is large or runs for a long time, you can improve the availability of your fleet by distributing the Spot Instances across multiple pools\. For example, if your Spot Fleet request specifies 10 pools and a target capacity of 100 instances, the fleet launches 10 Spot Instances in each pool\. If the Spot price for one pool exceeds your maximum price for this pool, only 10% of your fleet is affected\. Using this strategy also makes your fleet less sensitive to increases in the Spot price in any one pool over time\. With the `diversified` strategy, the Spot Fleet does not launch Spot Instances into any pools with a Spot price that is equal to or higher than the [On\-Demand price](https://aws.amazon.com/ec2/pricing/)\.

To create a cheap and diversified fleet, use the `lowestPrice` strategy in combination with `InstancePoolsToUseCount`\. You can use a low or high number of Spot pools across which to allocate your Spot Instances\. For example, if you run batch processing, we recommend specifying a low number of Spot pools \(for example, `InstancePoolsToUseCount=2`\) to ensure that your queue always has compute capacity while maximizing savings\. If you run a web service, we recommend specifying a high number of Spot pools \(for example, `InstancePoolsToUseCount=10`\) to minimize the impact if a Spot capacity pool becomes temporarily unavailable\.

### Configure Spot Fleet for cost optimization<a name="spot-fleet-strategy-cost-optimization"></a>

To optimize the costs for your use of Spot Instances, specify the `lowestPrice` allocation strategy so that Spot Fleet automatically deploys the least expensive combination of instance types and Availability Zones based on the current Spot price\.

For On\-Demand Instance target capacity, Spot Fleet always selects the least expensive instance type based on the public On\-Demand price, while continuing to follow the allocation strategy \(either `lowestPrice`, `capacityOptimized`, or `diversified`\) for Spot Instances\.

### Configure Spot Fleet for cost optimization and diversification<a name="spot-fleet-strategy-cost-optimization-and-diversified"></a>

To create a fleet of Spot Instances that is both cheap and diversified, use the `lowestPrice` allocation strategy in combination with `InstancePoolsToUseCount`\. Spot Fleet automatically deploys the cheapest combination of instance types and Availability Zones based on the current Spot price across the number of Spot pools that you specify\. This combination can be used to avoid the most expensive Spot Instances\.

### Configure Spot Fleet for capacity optimization<a name="spot-fleet-strategy-capacity-optimized"></a>

To launch Spot Instances into the most\-available Spot capacity pools, use the `capacityOptimized` allocation strategy\. For an example configuration, see [Example 9: Launch Spot Instances in a capacity\-optimized fleet](spot-fleet-examples.md#fleet-config9)\.

You can also express your pool priorities by using the `capacityOptimizedPrioritized` allocation strategy and then setting the order of instance types to use from highest to lowest priority\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set priorities for `capacityOptimizedPrioritized`, the same priorities are also applied to your On\-Demand Instances if the `OnDemandAllocationStrategy` is set to `prioritized`\. For an example configuration, see [Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities](spot-fleet-examples.md#fleet-config10)\.

## Capacity Rebalancing<a name="spot-fleet-capacity-rebalance"></a>

You can configure Spot Fleet to launch a replacement Spot Instance when Amazon EC2 emits a rebalance recommendation to notify you that a Spot Instance is at an elevated risk of interruption\. Capacity Rebalancing helps you maintain workload availability by proactively augmenting your fleet with a new Spot Instance before a running instance is interrupted by Amazon EC2\. For more information, see [EC2 instance rebalance recommendations](rebalance-recommendations.md)\.

To configure Spot Fleet to launch a replacement Spot Instance, you can use the Amazon EC2 console or the AWS CLI\.
+ Amazon EC2 console: You must select the **Capacity rebalance** check box when you create the Spot Fleet\. For more information, see step 6\.d\. in [Create a Spot Fleet request using defined parameters \(console\)](spot-fleet-requests.md#create-spot-fleet-advanced)\.
+ AWS CLI: Use the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command and the relevant parameters in the `SpotMaintenanceStrategies` structure\. For more information, see the [example launch configuration](spot-fleet-examples.md#fleet-config8)\.

**Limitations**
+ Only available for fleets of type `maintain`\.
+ When the fleet is running, you can't modify the Capacity Rebalancing setting\. To change the Capacity Rebalancing setting, you must delete the fleet and create a new fleet\.

**Considerations**

If you configure a Spot Fleet for Capacity Rebalancing, consider the following:

**Spot Fleet can launch new replacement Spot Instances until fulfilled capacity is double target capacity**  
When a Spot Fleet is configured for Capacity Rebalancing, the fleet attempts to launch a new replacement Spot Instance for every Spot Instance that receives a rebalance recommendation\. After a Spot Instance receives a rebalance recommendation, it is no longer counted as part of the fulfilled capacity, and Spot Fleet does not automatically terminate the instance\. This gives you the opportunity to perform [rebalancing actions](rebalance-recommendations.md#rebalancing-actions) on the instance\. Thereafter, you can terminate the instance, or you can leave it running\.  
If your fleet reaches double its target capacity, it stops launching new replacement instances even if the replacement instances themselves receive a rebalance recommendation\.  
For example, you create a Spot Fleet with a target capacity of 100 Spot Instances\. All the Spot Instances receive a rebalance recommendation, which causes Spot Fleet to launch 100 replacement Spot Instances\. This raises the number of fulfilled Spot Instances to 200, which is double the target capacity\. Some of the replacement instances receive a rebalance recommendation, but no more replacement instances are launched because the fleet cannot exceed double its target capacity\.   
Note that you are charged for all of the instances while they are running\.

**We recommend that you manually terminate Spot Instances that receive a rebalance recommendation**  
If you configure your Spot Fleet for Capacity Rebalancing, we recommend that you monitor the rebalance recommendation signal that is received by the Spot Instances in the fleet\. By monitoring the signal, you can quickly perform [rebalancing actions](rebalance-recommendations.md#rebalancing-actions) on the affected instances before Amazon EC2 interrupts them, and then you can manually terminate them\. If you do not terminate the instances, you continue paying for them while they are running\. Spot Fleet does not automatically terminate the instances that receive a rebalance recommendation\.  
You can set up notifications using Amazon EventBridge or instance metadata\. For more information, see [Monitor rebalance recommendation signals](rebalance-recommendations.md#monitor-rebalance-recommendations)\.

**Spot Fleet does not count instances that receive a rebalance recommendation when calculating fulfilled capacity during scale in or out**  
If your Spot Fleet is configured for Capacity Rebalancing, and you change the target capacity to either scale in or scale out, the fleet does not count the instances that are marked for rebalance as part of the fulfilled capacity, as follows:  
+ Scale in – If you decrease your desired target capacity, the fleet terminates instances that are not marked for rebalance until the desired capacity is reached\. The instances that are marked for rebalance are not counted towards the fulfilled capacity\.

  For example, you create a Spot Fleet with a target capacity of 100 Spot Instances\. 10 instances receive a rebalance recommendation, so the fleet launches 10 new replacement instances, resulting in a fulfilled capacity of 110 instances\. You then reduce the target capacity to 50 \(scale in\), but the fulfilled capacity is actually 60 instances because the 10 instances that are marked for rebalance are not terminated by the fleet\. You need to manually terminate these instances, or you can leave them running\.
+ Scale out – If you increase your desired target capacity, the fleet launches new instances until the desired capacity is reached\. The instances that are marked for rebalance are not counted towards the fulfilled capacity\. 

  For example, you create a Spot Fleet with a target capacity of 100 Spot Instances\. 10 instances receive a rebalance recommendation, so the fleet launches 10 new replacement instances, resulting in a fulfilled capacity of 110 instances\. You then increase the target capacity to 200 \(scale out\), but the fullfilled capacity is actually 210 instances because the 10 instances that are marked for rebalance are not counted by the fleet as part of the target capacity\. You need to manually terminate these instances, or you can leave them running\.

**Provide as many Spot capacity pools in the request as possible**  
Configure your Spot Fleet to use multiple instance types and Availability Zones\. This provides the flexibility to launch Spot Instances in various Spot capacity pools\. For more information, see [Be flexible about instance types and Availability Zones](spot-best-practices.md#be-instance-type-flexible)\.

**Configure your Spot Fleet to use the most optimal Spot capacity pools**  
Use the `capacity-optimized` allocation strategy to ensure that replacement Spot Instances are launched in the most optimal Spot capacity pools\. For more information, see [Use the capacity optimized allocation strategy](spot-best-practices.md#use-capacity-optimized-allocation-strategy)\. 

## Spot price overrides<a name="spot-price-overrides"></a>

Each Spot Fleet request can include a global maximum price, or use the default \(the On\-Demand price\)\. Spot Fleet uses this as the default maximum price for each of its launch specifications\.

You can optionally specify a maximum price in one or more launch specifications\. This price is specific to the launch specification\. If a launch specification includes a specific price, the Spot Fleet uses this maximum price, overriding the global maximum price\. Any other launch specifications that do not include a specific maximum price still use the global maximum price\.

## Control spending<a name="spot-fleet-control-spending"></a>

Spot Fleet stops launching instances when it has either reached the target capacity or the maximum amount you’re willing to pay\. To control the amount you pay per hour for your fleet, you can specify the `SpotMaxTotalPrice` for Spot Instances and the `OnDemandMaxTotalPrice` for On\-Demand Instances\. When the maximum total price is reached, Spot Fleet stops launching instances even if it hasn’t met the target capacity\.

The following examples show two different scenarios\. In the first, Spot Fleet stops launching instances when it has met the target capacity\. In the second, Spot Fleet stops launching instances when it has reached the maximum amount you’re willing to pay\.

**Example: Stop launching instances when target capacity is reached**

Given a request for `m4.large` On\-Demand Instances, where:
+ On\-Demand Price: $0\.10 per hour
+ `OnDemandTargetCapacity`: 10
+ `OnDemandMaxTotalPrice`: $1\.50

Spot Fleet launches 10 On\-Demand Instances because the total of $1\.00 \(10 instances x $0\.10\) does not exceed the `OnDemandMaxTotalPrice` of $1\.50\.

**Example: Stop launching instances when maximum total price is reached**

Given a request for `m4.large` On\-Demand Instances, where:
+ On\-Demand Price: $0\.10 per hour
+ `OnDemandTargetCapacity`: 10
+ `OnDemandMaxTotalPrice`: $0\.80

If Spot Fleet launches the On\-Demand target capacity \(10 On\-Demand Instances\), the total cost per hour would be $1\.00\. This is more than the amount \($0\.80\) specified for `OnDemandMaxTotalPrice`\. To prevent spending more than you're willing to pay, Spot Fleet launches only 8 On\-Demand Instances \(below the On\-Demand target capacity\) because launching more would exceed the `OnDemandMaxTotalPrice`\.

## Spot Fleet instance weighting<a name="spot-instance-weighting"></a>

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

## Walkthrough: Using Spot Fleet with instance weighting<a name="instance-weighting-walkthrough"></a>

This walkthrough uses a fictitious company called Example Corp to illustrate the process of requesting a Spot Fleet using instance weighting\.

### Objective<a name="instance-weighting-walkthrough-objective"></a>

Example Corp, a pharmaceutical company, wants to leverage the computational power of Amazon EC2 for screening chemical compounds that might be used to fight cancer\.

### Planning<a name="instance-weighting-walkthrough-planning"></a>

Example Corp first reviews [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices)\. Next, Example Corp determines the following requirements for their Spot Fleet\.

**Instance types**  
Example Corp has a compute\- and memory\-intensive application that performs best with at least 60 GB of memory and eight virtual CPUs \(vCPUs\)\. They want to maximize these resources for the application at the lowest possible price\. Example Corp decides that any of the following EC2 instance types would meet their needs:


| Instance type | Memory \(GiB\) | vCPUs | 
| --- | --- | --- | 
| r3\.2xlarge | 61 | 8 | 
| r3\.4xlarge | 122 | 16 | 
| r3\.8xlarge | 244 | 32 | 

**Target capacity in units**  
With instance weighting, target capacity can equal a number of instances \(the default\) or a combination of factors such as cores \(vCPUs\), memory \(GiBs\), and storage \(GBs\)\. By considering the base for their application \(60 GB of RAM and eight vCPUs\) as 1 unit, Example Corp decides that 20 times this amount would meet their needs\. So the company sets the target capacity of their Spot Fleet request to 20\.

**Instance weights**  
After determining the target capacity, Example Corp calculates instance weights\. To calculate the instance weight for each instance type, they determine the units of each instance type that are required to reach the target capacity as follows:
+ r3\.2xlarge \(61\.0 GB, 8 vCPUs\) = 1 unit of 20
+ r3\.4xlarge \(122\.0 GB, 16 vCPUs\) = 2 units of 20
+ r3\.8xlarge \(244\.0 GB, 32 vCPUs\) = 4 units of 20

Therefore, Example Corp assigns instance weights of 1, 2, and 4 to the respective launch configurations in their Spot Fleet request\.

**Price per unit hour**  
Example Corp uses the [On\-Demand price](https://aws.amazon.com/ec2/pricing/) per instance hour as a starting point for their price\. They could also use recent Spot prices, or a combination of the two\. To calculate the price per unit hour, they divide their starting price per instance hour by the weight\. For example:


| Instance type | On\-Demand price | Instance weight | Price per unit hour | 
| --- | --- | --- | --- | 
| r3\.2xLarge | $0\.7 | 1 | $0\.7 | 
| r3\.4xLarge | $1\.4 | 2 | $0\.7 | 
| r3\.8xLarge | $2\.8 | 4 | $0\.7 | 

Example Corp could use a global price per unit hour of $0\.7 and be competitive for all three instance types\. They could also use a global price per unit hour of $0\.7 and a specific price per unit hour of $0\.9 in the `r3.8xlarge` launch specification\.

### Verify permissions<a name="instance-weighting-walkthrough-permissions"></a>

Before creating a Spot Fleet request, Example Corp verifies that it has an IAM role with the required permissions\. For more information, see [Spot Fleet permissions](spot-fleet-requests.md#spot-fleet-prerequisites)\.

### Create the request<a name="instance-weighting-walkthrough-request"></a>

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
    },
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

Example Corp creates the Spot Fleet request using the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command\.

```
aws ec2 request-spot-fleet --spot-fleet-request-config file://config.json
```

For more information, see [Spot Fleet requests](spot-fleet-requests.md)\.

### Fulfillment<a name="instance-weighting-walkthrough-fulfillment"></a>

The allocation strategy determines which Spot capacity pools your Spot Instances come from\.

With the `lowestPrice` strategy \(which is the default strategy\), the Spot Instances come from the pool with the lowest price per unit at the time of fulfillment\. To provide 20 units of capacity, the Spot Fleet launches either 20 `r3.2xlarge` instances \(20 divided by 1\), 10 `r3.4xlarge` instances \(20 divided by 2\), or 5 `r3.8xlarge` instances \(20 divided by 4\)\.

If Example Corp used the `diversified` strategy, the Spot Instances would come from all three pools\. The Spot Fleet would launch 6 `r3.2xlarge` instances \(which provide 6 units\), 3 `r3.4xlarge` instances \(which provide 6 units\), and 2 `r3.8xlarge` instances \(which provide 8 units\), for a total of 20 units\.
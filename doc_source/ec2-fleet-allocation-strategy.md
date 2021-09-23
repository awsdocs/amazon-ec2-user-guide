# Allocation strategies for Spot Instances<a name="ec2-fleet-allocation-strategy"></a>

The allocation strategy for your EC2 Fleet determines how it fulfills your request for Spot Instances from the possible Spot capacity pools represented by its launch specifications\. The following are the allocation strategies that you can specify in your fleet:

`lowest-price`  
The Spot Instances come from the Spot capacity pool with the lowest price\. This is the default strategy\.

`diversified`  
The Spot Instances are distributed across all Spot capacity pools\.

`capacity-optimized`  
The Spot Instances come from the Spot capacity pool with optimal capacity for the number of instances that are launching\. You can optionally set a priority for each instance type in your fleet using `capacity-optimized-prioritized`\. EC2 Fleet optimizes for capacity first, but honors instance type priorities on a best\-effort basis\.  
With Spot Instances, pricing changes slowly over time based on long\-term trends in supply and demand, but capacity fluctuates in real time\. The `capacity-optimized` strategy automatically launches Spot Instances into the most available pools by looking at real\-time capacity data and predicting which are the most available\. This works well for workloads such as big data and analytics, image and media rendering, machine learning, and high performance computing that may have a higher cost of interruption associated with restarting work and checkpointing\. By offering the possibility of fewer interruptions, the `capacity-optimized` strategy can lower the overall cost of your workload\.  
Alternatively, you can use the `capacity-optimized-prioritized` allocation strategy with a priority parameter to order instance types from highest to lowest priority\. You can set the same priority for different instance types\. EC2 Fleet will optimize for capacity first, but will honor instance type priorities on a best\-effort basis \(for example, if honoring the priorities will not significantly affect EC2 Fleet's ability to provision optimal capacity\)\. This is a good option for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set the priority for `capacity-optimized-prioritized`, the same priority is also applied to your On\-Demand Instances if the On\-Demand `AllocationStrategy` is set to `prioritized`\.

`InstancePoolsToUseCount`  
The Spot Instances are distributed across the number of Spot capacity pools that you specify\. This parameter is valid only when used in combination with `lowest-price`\.

## Maintaining target capacity<a name="ec2-fleet-maintain-fleet-capacity"></a>

After Spot Instances are terminated due to a change in the Spot price or available capacity of a Spot capacity pool, an EC2 Fleet of type `maintain` launches replacement Spot Instances\. If the allocation strategy is `lowest-price`, the fleet launches replacement instances in the pool where the Spot price is currently the lowest\. If the allocation strategy is `lowest-price` in combination with `InstancePoolsToUseCount`, the fleet selects the Spot capacity pools with the lowest price and launches Spot Instances across the number of Spot capacity pools that you specify\. If the allocation strategy is `capacity-optimized`, the fleet launches replacement instances in the pool that has the most available Spot Instance capacity\. If the allocation strategy is `diversified`, the fleet distributes the replacement Spot Instances across the remaining pools\.

## Choose the appropriate allocation strategy<a name="ec2-fleet-allocation-use-cases"></a>

You can optimize your fleet based on your use case\.

If your fleet runs workloads that may have a higher cost of interruption associated with restarting work and checkpointing, then use the `capacity-optimized` strategy\. This strategy offers the possibility of fewer interruptions, which can lower the overall cost of your workload\. Use the `capacity-optimized-prioritized` strategy for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\.

If your fleet is small or runs for a short time, the probability that your Spot Instances will be interrupted is low, even with all of the instances in a single Spot capacity pool\. Therefore, the `lowest-price` strategy is likely to meet your needs while providing the lowest cost\.

If your fleet is large or runs for a long time, you can improve the availability of your fleet by distributing the Spot Instances across multiple pools using the `diversified` strategy\. For example, if your EC2 Fleet specifies 10 pools and a target capacity of 100 instances, the fleet launches 10 Spot Instances in each pool\. If the Spot price for one pool exceeds your maximum price for this pool, only 10% of your fleet is affected\. Using this strategy also makes your fleet less sensitive to increases in the Spot price in any one pool over time\. With the `diversified` strategy, the EC2 Fleet does not launch Spot Instances into any pools with a Spot price that is equal to or higher than the [On\-Demand price](https://aws.amazon.com/ec2/pricing/)\.

To create a cheap and diversified fleet, use the `lowest-price` strategy in combination with `InstancePoolsToUseCount`\. You can use a low or high number of Spot capacity pools across which to allocate your Spot Instances\. For example, if you run batch processing, we recommend specifying a low number of Spot capacity pools \(for example, `InstancePoolsToUseCount=2`\) to ensure that your queue always has compute capacity while maximizing savings\. If you run a web service, we recommend specifying a high number of Spot capacity pools \(for example, `InstancePoolsToUseCount=10`\) to minimize the impact if a Spot capacity pool becomes temporarily unavailable\.

## Configure EC2 Fleet for cost optimization<a name="ec2-fleet-strategy-cost-optimization"></a>

To optimize the costs for your use of Spot Instances, specify the `lowest-price` allocation strategy so that EC2 Fleet automatically deploys the least expensive combination of instance types and Availability Zones based on the current Spot price\.

For On\-Demand Instance target capacity, EC2 Fleet always selects the cheapest instance type based on the public On\-Demand price, while continuing to follow the allocation strategy \(either `lowest-price`, `capacity-optimized`, or `diversified`\) for Spot Instances\.

## Configure EC2 Fleet for cost optimization and diversification<a name="ec2-fleet-strategy-cost-optimization-and-diversified"></a>

To create a fleet of Spot Instances that is both cheap and diversified, use the `lowest-price` allocation strategy in combination with `InstancePoolsToUseCount`\. EC2 Fleet automatically deploys the least expensive combination of instance types and Availability Zones based on the current Spot price across the number of Spot capacity pools that you specify\. This combination can be used to avoid the most expensive Spot Instances\.

For example, if your target capacity is 10 Spot Instances, and you specify 2 Spot capacity pools \(for `InstancePoolsToUseCount`\), EC2 Fleet will draw on the two cheapest pools to fulfill your Spot capacity\.

Note that EC2 Fleet attempts to draw Spot Instances from the number of pools that you specify on a best effort basis\. If a pool runs out of Spot capacity before fulfilling your target capacity, EC2 Fleet will continue to fulfill your request by drawing from the next cheapest pool\. To ensure that your target capacity is met, you might receive Spot Instances from more than the number of pools that you specified\. Similarly, if most of the pools have no Spot capacity, you might receive your full target capacity from fewer than the number of pools that you specified\.

## Configure EC2 Fleet for capacity optimization<a name="ec2-fleet-strategy-capacity-optimized"></a>

To launch Spot Instances into the most\-available Spot capacity pools, use the `capacity-optimized` allocation strategy\. For an example configuration, see [Example 9: Launch Spot Instances in a capacity\-optimized fleet](ec2-fleet-examples.md#ec2-fleet-config10)\.

You can also express your pool priorities by using the `capacity-optimized-prioritized` allocation strategy and then setting the order of instance types to use from highest to lowest priority\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set priorities for `capacity-optimized-prioritized`, the same priorities are also applied to your On\-Demand Instances if the On\-Demand `AllocationStrategy` is set to `prioritized`\. For an example configuration, see [Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities](ec2-fleet-examples.md#ec2-fleet-config11)\.
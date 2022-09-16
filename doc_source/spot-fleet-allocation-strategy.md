# Allocation strategies for Spot Instances<a name="spot-fleet-allocation-strategy"></a>

Your launch configuration determines all the possible Spot capacity pools \(instance types and Availability Zones\) from which Spot Fleet can launch Spot Instances\. However, when launching instances, Spot Fleet uses the allocation strategy that you specify to pick the specific pools from all your possible pools\.

You can specify one of the following allocation strategies:

`lowestPrice`  
The Spot Instances come from the lowest\-price pool that has available capacity\. This is the default strategy\.  
If the cheapest pool doesn't have available capacity, the Spot Instances come from the next cheapest pool that has available capacity\.  
If a pool runs out of capacity before fulfilling your desired capacity, Spot Fleet will continue to fulfill your request by drawing from the next cheapest pool\. To ensure that your desired capacity is met, you might receive Spot Instances from several pools\.

`diversified`  
The Spot Instances are distributed across all pools\.

`capacityOptimized`  
The Spot Instances come from the pools with optimal capacity for the number of instances that are launching\. You can optionally set a priority for each instance type in your fleet using `capacityOptimizedPrioritized`\. Spot Fleet optimizes for capacity first, but honors instance type priorities on a best\-effort basis\.   
With Spot Instances, pricing changes slowly over time based on long\-term trends in supply and demand, but capacity fluctuates in real time\. The `capacityOptimized` strategy automatically launches Spot Instances into the most available pools by looking at real\-time capacity data and predicting which are the most available\. This works well for workloads such as big data and analytics, image and media rendering, machine learning, and high performance computing that may have a higher cost of interruption associated with restarting work and checkpointing\. By offering the possibility of fewer interruptions, the `capacityOptimized` strategy can lower the overall cost of your workload\.  
Alternatively, you can use the `capacityOptimizedPrioritized` allocation strategy with a priority parameter to order instance types from highest to lowest priority\. You can set the same priority for different instance types\. Spot Fleet will optimize for capacity first, but will honor instance type priorities on a best\-effort basis \(for example, if honoring the priorities will not significantly affect Spot Fleet's ability to provision optimal capacity\)\. This is a good option for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set the priority for `capacityOptimizedPrioritized`, the same priority is also applied to your On\-Demand Instances if the On\-Demand `AllocationStrategy` is set to `prioritized`\.

`InstancePoolsToUseCount`  
The number of Spot pools across which to allocate your target Spot capacity\. Valid only when the allocation strategy is set to `lowest-price`\. Spot Fleet selects the cheapest Spot pools and evenly allocates your target Spot capacity across the number of Spot pools that you specify\.  
Note that Spot Fleet attempts to draw Spot Instances from the number of pools that you specify on a best effort basis\. If a pool runs out of Spot capacity before fulfilling your target capacity, Spot Fleet will continue to fulfill your request by drawing from the next cheapest pool\. To ensure that your target capacity is met, you might receive Spot Instances from more than the number of pools that you specified\. Similarly, if most of the pools have no Spot capacity, you might receive your full target capacity from fewer than the number of pools that you specified\.

## Maintain target capacity<a name="maintain-fleet-capacity"></a>

After Spot Instances are terminated due to a change in the Spot price or available capacity of a Spot capacity pool, a Spot Fleet of type `maintain` launches replacement Spot Instances\. If the allocation strategy is `lowestPrice`, the fleet launches replacement instances in the pool where the Spot price is currently the lowest\. If the allocation strategy is `diversified`, the fleet distributes the replacement Spot Instances across the remaining pools\. If the allocation strategy is `lowestPrice` in combination with `InstancePoolsToUseCount`, the fleet selects the Spot pools with the lowest price and launches Spot Instances across the number of Spot pools that you specify\.

## Choose an appropriate allocation strategy<a name="allocation-use-cases"></a>

You can optimize your Spot Fleets based on your use case\.

If your fleet runs workloads that may have a higher cost of interruption associated with restarting work and checkpointing, then use the `capacityOptimized` strategy\. This strategy offers the possibility of fewer interruptions, which can lower the overall cost of your workload\. This is the recommended strategy\. Use the `capacityOptimizedPrioritized` strategy for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\. 

If your fleet is small or runs for a short time, the probability that your Spot Instances may be interrupted is low, even with all the instances in a single Spot capacity pool\. Therefore, the `lowestPrice` strategy is likely to meet your needs while providing the lowest cost\.

If your fleet is large or runs for a long time, you can improve the availability of your fleet by distributing the Spot Instances across multiple pools\. For example, if your Spot Fleet request specifies 10 pools and a target capacity of 100 instances, the fleet launches 10 Spot Instances in each pool\. If the Spot price for one pool exceeds your maximum price for this pool, only 10% of your fleet is affected\. Using this strategy also makes your fleet less sensitive to increases in the Spot price in any one pool over time\. With the `diversified` strategy, the Spot Fleet does not launch Spot Instances into any pools with a Spot price that is equal to or higher than the [On\-Demand price](https://aws.amazon.com/ec2/pricing/)\.

To create a cheap and diversified fleet, use the `lowestPrice` strategy in combination with `InstancePoolsToUseCount`\. You can use a low or high number of Spot pools across which to allocate your Spot Instances\. For example, if you run batch processing, we recommend specifying a low number of Spot pools \(for example, `InstancePoolsToUseCount=2`\) to ensure that your queue always has compute capacity while maximizing savings\. If you run a web service, we recommend specifying a high number of Spot pools \(for example, `InstancePoolsToUseCount=10`\) to minimize the impact if a Spot capacity pool becomes temporarily unavailable\.

## Configure Spot Fleet for cost optimization<a name="spot-fleet-strategy-cost-optimization"></a>

To optimize the costs for your use of Spot Instances, specify the `lowestPrice` allocation strategy so that Spot Fleet automatically deploys the least expensive combination of instance types and Availability Zones based on the current Spot price\.

For On\-Demand Instance target capacity, Spot Fleet always selects the least expensive instance type based on the public On\-Demand price, while continuing to follow the allocation strategy \(either `lowestPrice`, `capacityOptimized`, or `diversified`\) for Spot Instances\.

## Configure Spot Fleet for cost optimization and diversification<a name="spot-fleet-strategy-cost-optimization-and-diversified"></a>

To create a fleet of Spot Instances that is both cheap and diversified, use the `lowestPrice` allocation strategy in combination with `InstancePoolsToUseCount`\. Spot Fleet automatically deploys the cheapest combination of instance types and Availability Zones based on the current Spot price across the number of Spot pools that you specify\. This combination can be used to avoid the most expensive Spot Instances\.

For example, if your target capacity is 10 Spot Instances, and you specify 2 Spot capacity pools \(for `InstancePoolsToUseCount`\), Spot Fleet will draw on the two cheapest pools to fulfill your Spot capacity\.

Note that Spot Fleet attempts to draw Spot Instances from the number of pools that you specify on a best effort basis\. If a pool runs out of Spot capacity before fulfilling your target capacity, Spot Fleet will continue to fulfill your request by drawing from the next cheapest pool\. To ensure that your target capacity is met, you might receive Spot Instances from more than the number of pools that you specified\. Similarly, if most of the pools have no Spot capacity, you might receive your full target capacity from fewer than the number of pools that you specified\.

## Configure Spot Fleet for capacity optimization<a name="spot-fleet-strategy-capacity-optimized"></a>

To launch Spot Instances into the most\-available Spot capacity pools, use the `capacityOptimized` allocation strategy\. For an example configuration, see [Example 9: Launch Spot Instances in a capacity\-optimized fleet](spot-fleet-examples.md#fleet-config9)\.

You can also express your pool priorities by using the `capacityOptimizedPrioritized` allocation strategy and then setting the order of instance types to use from highest to lowest priority\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set priorities for `capacityOptimizedPrioritized`, the same priorities are also applied to your On\-Demand Instances if the `OnDemandAllocationStrategy` is set to `prioritized`\. For an example configuration, see [Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities](spot-fleet-examples.md#fleet-config10)\.
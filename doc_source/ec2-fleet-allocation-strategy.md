# Allocation strategies for Spot Instances<a name="ec2-fleet-allocation-strategy"></a>

Your launch configuration determines all the possible Spot capacity pools \(instance types and Availability Zones\) from which EC2 Fleet can launch Spot Instances\. However, when launching instances, EC2 Fleet uses the allocation strategy that you specify to pick the specific pools from all your possible pools\.

You can specify one of the following allocation strategies:

`price-capacity-optimized` \(recommended\)  
EC2 Fleet identifies the pools with the highest capacity availability for the number of instances that are launching\. This means that we will request Spot Instances from the pools that we believe have the lowest chance of interruption in the near term\. EC2 Fleet then requests Spot Instances from the lowest priced of these pools\.  
The `price-capacity-optimized` allocation strategy is the best choice for most Spot workloads, such as stateless containerized applications, microservices, web applications, data and analytics jobs, and batch processing\.

`capacity-optimized`  
EC2 Fleet identifies the pools with the highest capacity availability for the number of instances that are launching\. This means that we will request Spot Instances from the pools that we believe have the lowest chance of interruption in the near term\. You can optionally set a priority for each instance type in your fleet using `capacity-optimized-prioritized`\. EC2 Fleet optimizes for capacity first, but honors instance type priorities on a best\-effort basis\.  
With Spot Instances, pricing changes slowly over time based on long\-term trends in supply and demand, but capacity fluctuates in real time\. The `capacity-optimized` strategy automatically launches Spot Instances into the most available pools by looking at real\-time capacity data and predicting which are the most available\. This works well for workloads that may have a higher cost of interruption associated with restarting work, such as long Continuous Integration \(CI\), image and media rendering, Deep Learning, and High Performance Compute \(HPC\) workloads that may have a higher cost of interruption associated with restarting work\. By offering the possibility of fewer interruptions, the `capacity-optimized` strategy can lower the overall cost of your workload\.  
Alternatively, you can use the `capacity-optimized-prioritized` allocation strategy with a priority parameter to order instance types from highest to lowest priority\. You can set the same priority for different instance types\. EC2 Fleet will optimize for capacity first, but will honor instance type priorities on a best\-effort basis \(for example, if honoring the priorities will not significantly affect EC2 Fleet's ability to provision optimal capacity\)\. This is a good option for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\. Using priorities is supported only if your fleet uses a launch template\. Note that when you set the priority for `capacity-optimized-prioritized`, the same priority is also applied to your On\-Demand Instances if the On\-Demand `AllocationStrategy` is set to `prioritized`\.

`diversified`  
The Spot Instances are distributed across all Spot capacity pools\.

`lowest-price`  
The Spot Instances come from the lowest priced pool that has available capacity\. This is the default strategy\. However, we recommend that you override the default by specifying the `price-capacity-optimized` allocation strategy\.  
If the lowest priced pool doesn't have available capacity, the Spot Instances come from the next lowest priced pool that has available capacity\.  
If a pool runs out of capacity before fulfilling your desired capacity, EC2 Fleet will continue to fulfill your request by drawing from the next lowest priced pool\. To ensure that your desired capacity is met, you might receive Spot Instances from several pools\.  
Because this strategy only considers instance price and not capacity availability, it might lead to high interruption rates\.

`InstancePoolsToUseCount`  
The number of Spot pools across which to allocate your target Spot capacity\. Valid only when the allocation strategy is set to `lowest-price`\. EC2 Fleet selects the lowest priced Spot pools and evenly allocates your target Spot capacity across the number of Spot pools that you specify\.  
Note that EC2 Fleet attempts to draw Spot Instances from the number of pools that you specify on a best effort basis\. If a pool runs out of Spot capacity before fulfilling your target capacity, EC2 Fleet will continue to fulfill your request by drawing from the next lowest priced pool\. To ensure that your target capacity is met, you might receive Spot Instances from more than the number of pools that you specified\. Similarly, if most of the pools have no Spot capacity, you might receive your full target capacity from fewer than the number of pools that you specified\.

## Choose the appropriate allocation strategy<a name="ec2-fleet-allocation-use-cases"></a>

You can optimize your fleet for your use case by choosing the appropriate Spot allocation strategy\. For On\-Demand Instance target capacity, EC2 Fleet always selects the least expensive instance type based on the public On\-Demand price, while following the allocation strategy—either `price-capacity-optimized`, `capacity-optimized`, `diversified`, or `lowest-price`—for Spot Instances\.

### Balance lowest price and capacity availability<a name="ec2-fleet-strategy-balance-price-and-capacity-availability"></a>

To balance the trade\-offs between the lowest priced Spot capacity pools and the Spot capacity pools with the highest capacity availability, we recommend that you use the `price-capacity-optimized` allocation strategy\. This strategy makes decisions about which pools to request Spot Instances from based on both the price of the pools and the capacity availability of Spot Instances in those pools\. This means that we will request Spot Instances from the pools that we believe have the lowest chance of interruption in the near term, while still taking price into consideration\.

If your fleet runs resilient and stateless workloads, including containerized applications, microservices, web applications, data and analytics jobs, and batch processing, then use the `price-capacity-optimized` allocation strategy for optimal cost savings and capacity availability\.

If your fleet runs workloads that might have a higher cost of interruption associated with restarting work, then you should implement checkpointing so that applications can restart from that point if they're interrupted\. By using checkpointing, you make the `price-capacity-optimized` allocation strategy a good fit for these workloads because it allocates capacity from the lowest priced pools that also offer a low Spot Instance interruption rate\.

For an example configuration that uses the `price-capacity-optimized` allocation strategy, see [Example 11: Launch Spot Instances in a price\-capacity\-optimized fleet](ec2-fleet-examples.md#ec2-fleet-config11)\.

### When workloads have a high cost of interruption<a name="ec2-fleet-strategy-capacity-optimized"></a>

You can optionally use the `capacity-optimized` strategy if you run workloads that either use similarly priced instance types, or where the cost of interruption is so significant that any cost saving is inadequate in comparison to a marginal increase in interruptions\. This strategy allocates capacity from the most available Spot capacity pools that offer the possibility of fewer interruptions, which can lower the overall cost of your workload\. For an example configuration that uses the `capacity-optimized` allocation strategy, see [Example 9: Launch Spot Instances in a capacity\-optimized fleet](ec2-fleet-examples.md#ec2-fleet-config9)\.

When the possibility of interruptions must be minimized but the preference for certain instance types matters, you can express your pool priorities by using the `capacity-optimized-prioritized` allocation strategy and then setting the order of instance types to use from highest to lowest priority\. For an example configuration, see [Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities](ec2-fleet-examples.md#ec2-fleet-config10)\.

Note that using priorities is supported only if your fleet uses a launch template\. Also note that when you set priorities for `capacity-optimized-prioritized`, the same priorities are also applied to your On\-Demand Instances if the On\-Demand `AllocationStrategy` is set to `prioritized`\. 

### When your workload is time flexible and capacity availability is not a factor<a name="ec2-fleet-strategy-time-flexible-workload"></a>

If your fleet is small or runs for a short time, you can use `price-capacity-optimized` to maximize cost savings while still considering capacity availability\.

If your workload is time flexible and capacity availability is not a factor, you can optionally use the `lowest-price` allocation strategy to maximize cost savings\. Note, however, that because the `lowest-price` allocation strategy only considers instance price and not capacity availability, it might lead to high Spot Instance interruption rates\.

### When your fleet is large or runs for a long time<a name="ec2-fleet-strategy-large-workload-long-time"></a>

If your fleet is large or runs for a long time, you can improve the availability of your fleet by distributing the Spot Instances across multiple pools using the `diversified` strategy\. For example, if your EC2 Fleet specifies 10 pools and a target capacity of 100 instances, the fleet launches 10 Spot Instances in each pool\. If the Spot price for one pool exceeds your maximum price for this pool, only 10% of your fleet is affected\. Using this strategy also makes your fleet less sensitive to increases in the Spot price in any one pool over time\. With the `diversified` strategy, the EC2 Fleet does not launch Spot Instances into any pools with a Spot price that is equal to or higher than the [On\-Demand price](https://aws.amazon.com/ec2/pricing/)\.

To create an inexpensive and diversified fleet, use the `lowest-price` strategy in combination with `InstancePoolsToUseCount`\. For example, if your target capacity is 10 Spot Instances, and you specify 2 Spot capacity pools \(for `InstancePoolsToUseCount`\), EC2 Fleet will draw on the two lowest priced pools to fulfill your Spot capacity\.

You can use a low or high number of Spot capacity pools across which to allocate your Spot Instances\. For example, if you run batch processing, we recommend specifying a low number of Spot capacity pools \(for example, `InstancePoolsToUseCount=2`\) to ensure that your queue always has compute capacity while maximizing savings\. If you run a web service, we recommend specifying a high number of Spot capacity pools \(for example, `InstancePoolsToUseCount=10`\) to minimize the impact if a Spot capacity pool becomes temporarily unavailable\.

Note that EC2 Fleet attempts to draw Spot Instances from the number of pools that you specify on a best effort basis\. If a pool runs out of Spot capacity before fulfilling your target capacity, EC2 Fleet will continue to fulfill your request by drawing from the next lowest priced pool\. To ensure that your target capacity is met, you might receive Spot Instances from more than the number of pools that you specified\. Similarly, if most of the pools have no Spot capacity, you might receive your full target capacity from fewer than the number of pools that you specified\.

## Maintain target capacity<a name="ec2-fleet-maintain-fleet-capacity"></a>

After Spot Instances are terminated due to a change in the Spot price or available capacity of a Spot capacity pool, an EC2 Fleet of type `maintain` launches replacement Spot Instances\. The allocation strategy determines the pools from which the replacement instances are launched, as follows:
+ If the allocation strategy is `price-capacity-optimized`, the fleet launches replacement instances in the pools that have the most Spot Instance capacity availability while also taking price into consideration and identifying lowest priced pools with high capacity availability\.
+ If the allocation strategy is `capacity-optimized`, the fleet launches replacement instances in the pools that have the most Spot Instance capacity availability\.
+ If the allocation strategy is `diversified`, the fleet distributes the replacement Spot Instances across the remaining pools\.
+ If the allocation strategy is `lowest-price`, the fleet launches replacement instances in the pool where the Spot price is currently the lowest\.
+ If the allocation strategy is `lowest-price` in combination with `InstancePoolsToUseCount`, the fleet selects the Spot capacity pools with the lowest price and launches Spot Instances across the number of Spot capacity pools that you specify\.
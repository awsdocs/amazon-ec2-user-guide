# Capacity Rebalancing<a name="ec2-fleet-capacity-rebalance"></a>

You can configure EC2 Fleet to launch a replacement Spot Instance when Amazon EC2 emits a rebalance recommendation to notify you that a Spot Instance is at an elevated risk of interruption\. Capacity Rebalancing helps you maintain workload availability by proactively augmenting your fleet with a new Spot Instance before a running instance is interrupted by Amazon EC2\. For more information, see [EC2 instance rebalance recommendations](rebalance-recommendations.md)\.

To configure EC2 Fleet to launch a replacement Spot Instance, use the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) \(AWS CLI\) command and the relevant parameters in the `MaintenanceStrategies` structure\. For more information, see the [example launch configuration](ec2-fleet-examples.md#ec2-fleet-config9)\.

## Limitations<a name="ec2-fleet-capacity-rebalance-limitations"></a>
+ Capacity Rebalancing is available only for fleets of type `maintain`\.
+ When the fleet is running, you can't modify the Capacity Rebalancing setting\. To change the Capacity Rebalancing setting, you must delete the fleet and create a new fleet\.

## Configuration options<a name="ec2-fleet-capacity-rebalance-config-options"></a>

The `ReplacementStrategy` for EC2 Fleet supports the following two values:

`launch-before-terminate`  
EC2 Fleet terminates the Spot Instances that receive a rebalance notification after new replacement Spot Instances are launched\. When you specify `launch-before-terminate`, you must also specify a value for `termination-delay`\. After the new replacement instances are launched, EC2 Fleet waits for the duration of the `termination-delay`, and then terminates the old instances\. For `termination-delay`, the minimum is 120 seconds \(2 minutes\), and the maximum is 7200 seconds \(2 hours\)\.  
We recommend that you use `launch-before-terminate` only if you can predict how long your instance shutdown procedures will take to complete\. This will ensure that the old instances are terminated only after the shutdown procedures are completed\. Note that Amazon EC2 can interrupt the old instances with a two\-minute warning before the `termination-delay`\.  
We strongly recommend against using the `lowest-price` allocation strategy in combination with `launch-before-terminate` to avoid having replacement Spot Instances that are also at an elevated risk of interruption\.

`launch`  
EC2 Fleet launches replacement Spot Instances when a rebalance notification is emitted for existing Spot Instances\. EC2 Fleet does not terminate the instances that receive a rebalance notification\. You can terminate the old instances, or you can leave them running\. You are charged for all instances while they are running\.

## Considerations<a name="ec2-fleet-capacity-rebalance-considerations"></a>

If you configure an EC2 Fleet for Capacity Rebalancing, consider the following:

**Provide as many Spot capacity pools in the request as possible**  
Configure your EC2 Fleet to use multiple instance types and Availability Zones\. This provides the flexibility to launch Spot Instances in various Spot capacity pools\. For more information, see [Be flexible about instance types and Availability Zones](spot-best-practices.md#be-instance-type-flexible)\.

**Avoid an elevated risk of interruption of replacement Spot Instances**  
Your replacement Spot Instances may be at an elevated risk of interruption if you use the `lowest-price` allocation strategy\. This is because Amazon EC2 will always launch instances in the lowest priced pool that has available capacity at that moment, even if your replacement Spot Instances are likely to be interrupted soon after being launched\. To avoid an elevated risk of interruption, we strongly recommend against using the `lowest-price` allocation strategy, and instead recommend the `capacity-optimized` or `capacity-optimized-prioritized` allocation strategy\. These strategies ensure that replacement Spot Instances are launched in the most optimal Spot capacity pools, and are therefore less likely to be interrupted in the near future\. For more information, see [Use the price and capacity optimized allocation strategy](spot-best-practices.md#use-capacity-optimized-allocation-strategy)\. 

**Capacity Rebalancing does not increase your Spot Instance interruption rate**  
When you enable Capacity Rebalancing, it does not increase your [Spot Instance interruption rate](spot-interruptions.md) \(the number of Spot Instances that are reclaimed when Amazon EC2 needs the capacity back\)\. However, if Capacity Rebalancing detects an instance is at risk of interruption, EC2 Fleet will immediately attempt to launch a new instance\. The result is that more instances might be replaced than if you’d waited for EC2 Fleet to launch a new instance after the at\-risk instance was interrupted\.  
While you might replace more instances with Capacity Rebalancing enabled, you benefit from being proactive rather than reactive by having more time to take action before your instances are interrupted\. With a [Spot Instance interruption notice](spot-instance-termination-notices.md), you typically only have up to two minutes to gracefully shut down your instance\. With Capacity Rebalancing launching a new instance in advance, you give existing processes a better chance of completing on your at\-risk instance, you can start your instance shutdown procedures, and prevent new work from being scheduled on your at\-risk instance\. You can also start preparing the newly\-launched instance to take over the application\. With Capacity Rebalancing’s proactive replacement, you benefit from graceful continuity\.  
As a theoretical example to demonstrate the risks and benefits of using Capacity Rebalancing, consider the following scenario:  
+ 2:00 PM – A rebalance recommendation is received for instance\-A, and EC2 Fleet immediately starts attempting to launch a replacement instance\-B, giving you time to start your shutdown procedures\.\*
+ 2:30 PM – A rebalance recommendation is received for instance\-B, replaced with instance\-C, giving you time to start your shutdown procedures\.\*
+ 2:32 PM – If Capacity Rebalancing wasn’t enabled, and if a Spot Instance interruption notice would've been received at 2:32 PM for instance\-A, you would only have had up to two minutes to take action, but Instance\-A would have been running up till this time\.
\* If `launch-before-terminate` is specified, EC2 Fleet will terminate the at\-risk instance after the replacement instance comes online\.

**EC2 Fleet can launch new replacement Spot Instances until fulfilled capacity is double target capacity**  
When an EC2 Fleet is configured for Capacity Rebalancing, the fleet attempts to launch a new replacement Spot Instance for every Spot Instance that receives a rebalance recommendation\. After a Spot Instance receives a rebalance recommendation, it is no longer counted as part of the fulfilled capacity\. Depending on the replacement strategy, EC2 Fleet either terminates the instance after a preconfigured termination delay, or leaves it running\. This gives you the opportunity to perform [rebalancing actions](rebalance-recommendations.md#rebalancing-actions) on the instance\.  
If your fleet reaches double its target capacity, it stops launching new replacement instances even if the replacement instances themselves receive a rebalance recommendation\.  
For example, you create an EC2 Fleet with a target capacity of 100 Spot Instances\. All of the Spot Instances receive a rebalance recommendation, which causes EC2 Fleet to launch 100 replacement Spot Instances\. This raises the number of fulfilled Spot Instances to 200, which is double the target capacity\. Some of the replacement instances receive a rebalance recommendation, but no more replacement instances are launched because the fleet cannot exceed double its target capacity\.   
Note that you are charged for all of the instances while they are running\.

**We recommend that you configure EC2 Fleet to terminate Spot Instances that receive a rebalance recommendation**  
If you configure your EC2 Fleet for Capacity Rebalancing, we recommend that you choose `launch-before-terminate` with an appropriate termination delay only if you can predict how long your instance shutdown procedures will take to complete\. This will ensure that the old instances are terminated only after the shutdown procedures are completed\.  
If you choose to terminate the instances that are recommended for rebalance yourself, we recommend that you monitor the rebalance recommendation signal that is received by the Spot Instances in the fleet\. By monitoring the signal, you can quickly perform [rebalancing actions](rebalance-recommendations.md#rebalancing-actions) on the affected instances before Amazon EC2 interrupts them, and then you can manually terminate them\. If you do not terminate the instances, you continue paying for them while they are running\. EC2 Fleet does not automatically terminate the instances that receive a rebalance recommendation\.  
You can set up notifications using Amazon EventBridge or instance metadata\. For more information, see [Monitor rebalance recommendation signals](rebalance-recommendations.md#monitor-rebalance-recommendations)\.

**EC2 Fleet does not count instances that receive a rebalance recommendation when calculating fulfilled capacity during scale in or out**  
If your EC2 Fleet is configured for Capacity Rebalancing, and you change the target capacity to either scale in or scale out, the fleet does not count the instances that are marked for rebalance as part of the fulfilled capacity, as follows:  
+ Scale in – If you decrease your desired target capacity, the fleet terminates instances that are not marked for rebalance until the desired capacity is reached\. The instances that are marked for rebalance are not counted towards the fulfilled capacity\.

  For example, you create an EC2 Fleet with a target capacity of 100 Spot Instances\. 10 instances receive a rebalance recommendation, so the fleet launches 10 new replacement instances, resulting in a fulfilled capacity of 110 instances\. You then reduce the target capacity to 50 \(scale in\), but the fulfilled capacity is actually 60 instances because the 10 instances that are marked for rebalance are not terminated by the fleet\. You need to manually terminate these instances, or you can leave them running\.
+ Scale out – If you increase your desired target capacity, the fleet launches new instances until the desired capacity is reached\. The instances that are marked for rebalance are not counted towards the fulfilled capacity\. 

  For example, you create an EC2 Fleet with a target capacity of 100 Spot Instances\. 10 instances receive a rebalance recommendation, so the fleet launches 10 new replacement instances, resulting in a fulfilled capacity of 110 instances\. You then increase the target capacity to 200 \(scale out\), but the fulfilled capacity is actually 210 instances because the 10 instances that are marked for rebalance are not counted by the fleet as part of the target capacity\. You need to manually terminate these instances, or you can leave them running\.
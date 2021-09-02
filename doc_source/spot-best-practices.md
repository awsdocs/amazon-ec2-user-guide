# Best practices for EC2 Spot<a name="spot-best-practices"></a>

Amazon EC2 Spot Instances are spare EC2 compute capacity in the AWS Cloud that are available to you at savings of up to 90% off compared to On\-Demand prices\. The only difference between On\-Demand Instances and Spot Instances is that Spot Instances can be interrupted by Amazon EC2, with two minutes of notification, when Amazon EC2 needs the capacity back\.

Spot Instances are recommended for stateless, fault\-tolerant, flexible applications\. For example, Spot Instances work well for big data, containerized workloads, CI/CD, stateless web servers, high performance computing \(HPC\), and rendering workloads\.

While running, Spot Instances are exactly the same as On\-Demand Instances\. However, Spot does not guarantee that you can keep your running instances long enough to finish your workloads\. Spot also does not guarantee that you can get immediate availability of the instances that you are looking for, or that you can always get the aggregate capacity that you requested\. Moreover, Spot Instance interruptions and capacity can change over time because Spot Instance availability varies based on supply and demand, and past performance isn’t a guarantee of future results\.

Spot Instances are not suitable for workloads that are inflexible, stateful, fault\-intolerant, or tightly coupled between instance nodes\. It's also not recommended for workloads that are intolerant of occasional periods when the target capacity is not completely available\. We strongly warn against using Spot Instances for these workloads or attempting to fail\-over to On\-Demand Instances to handle interruptions\.

Regardless of whether you're an experienced Spot user or new to Spot Instances, if you are currently experiencing issues with Spot Instance interruptions or availability, we recommend that you follow these best practices to have the best experience using the Spot service\.

**Topics**
+ [Prepare individual instances for interruptions](#prep-instances-for-interruptions)
+ [Be flexible about instance types and Availability Zones](#be-instance-type-flexible)
+ [Use EC2 Auto Scaling groups or Spot Fleet to manage your aggregate capacity](#use-sf-asg-for-aggregate-capacity)
+ [Use the capacity optimized allocation strategy](#use-capacity-optimized-allocation-strategy)
+ [Use proactive capacity rebalancing](#use-capacity-rebalancing)
+ [Use integrated AWS services to manage your Spot Instances](#use-integrated-aws-services)

## Prepare individual instances for interruptions<a name="prep-instances-for-interruptions"></a>

The best way for you to gracefully handle Spot Instance interruptions is to architect your application to be fault\-tolerant\. To accomplish this, you can take advantage of EC2 instance rebalance recommendations and Spot Instance interruption notices\.

An EC2 Instance rebalance recommendation is a new signal that notifies you when a Spot Instance is at elevated risk of interruption\. The signal gives you the opportunity to proactively manage the Spot Instance in advance of the two\-minute Spot Instance interruption notice\. You can decide to rebalance your workload to new or existing Spot Instances that are not at an elevated risk of interruption\. We've made it easy for you to use this new signal by using the Capacity Rebalancing feature in Auto Scaling groups and Spot Fleet\. For more information, see [Use proactive capacity rebalancing](#use-capacity-rebalancing)\.

A Spot Instance interruption notice is a warning that is issued two minutes before Amazon EC2 interrupts a Spot Instance\. If your workload is "time\-flexible," you can configure your Spot Instances to be stopped or hibernated, instead of being terminated, when they are interrupted\. Amazon EC2 automatically stops or hibernates your Spot Instances on interruption, and automatically resumes the instances when we have available capacity\.

We recommend that you create a rule in [Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/index.html) that captures the rebalance recommendations and interruption notifications, and then triggers a checkpoint for the progress of your workload or gracefully handles the interruption\. For more information, see [Monitor rebalance recommendation signals](rebalance-recommendations.md#monitor-rebalance-recommendations)\. For a detailed example that walks you through how to create and use event rules, see [Taking Advantage of Amazon EC2 Spot Instance Interruption Notices](http://aws.amazon.com/blogs/compute/taking-advantage-of-amazon-ec2-spot-instance-interruption-notices/)\.

For more information, see [EC2 instance rebalance recommendations](rebalance-recommendations.md) and [Spot Instance interruptions](spot-interruptions.md)\.

## Be flexible about instance types and Availability Zones<a name="be-instance-type-flexible"></a>

A Spot capacity pool is a set of unused EC2 instances with the same instance type \(for example, m5\.large\) and Availability Zone \(for example, us\-east\-1a\)\. You should be flexible about which instance types you request and in which Availability Zones you can deploy your workload\. This gives Spot a better chance to find and allocate your required amount of compute capacity\. For example, don't just ask for `c5.large` if you'd be willing to use larges from the c4, m5, and m4 families\.

Depending on your specific needs, you can evaluate which instance types you can be flexible across to fulfill your compute requirements\. If a workload can be vertically scaled, you should include larger instance types \(more vCPUs and memory\) in your requests\. If you can only scale horizontally, you should include older generation instance types because they are less in demand from On\-Demand customers\.

A good rule of thumb is to be flexible across at least 10 instance types for each workload\. In addition, make sure that all Availability Zones are configured for use in your VPC and selected for your workload\.

## Use EC2 Auto Scaling groups or Spot Fleet to manage your aggregate capacity<a name="use-sf-asg-for-aggregate-capacity"></a>

Spot enables you to think in terms of aggregate capacity—in units that include vCPUs, memory, storage, or network throughput—rather than thinking in terms of individual instances\. Auto Scaling groups and Spot Fleet enable you to launch and maintain a target capacity, and to automatically request resources to replace any that are disrupted or manually terminated\. When you configure an Auto Scaling group or a Spot Fleet, you need only specify the instance types and target capacity based on your application needs\. For more information, see [Auto Scaling groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) in the *Amazon EC2 Auto Scaling User Guide* and [Create a Spot Fleet request](work-with-spot-fleets.md#create-spot-fleet) in this user guide\.

## Use the capacity optimized allocation strategy<a name="use-capacity-optimized-allocation-strategy"></a>

Allocation strategies in Auto Scaling groups help you to provision your target capacity without the need to manually look for the Spot capacity pools with spare capacity\. We recommend using the `capacity optimized` strategy because this strategy automatically provisions instances from the most\-available Spot capacity pools\. You can also take advantage of the `capacity optimized` allocation strategy in Spot Fleet\. Because your Spot Instance capacity is sourced from pools with optimal capacity, this decreases the possibility that your Spot Instances are reclaimed\. For more information about allocation strategies, see [Spot Instances](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-purchase-options.html#asg-spot-strategy) in the *Amazon EC2 Auto Scaling User Guide* and [Configure Spot Fleet for capacity optimization](spot-fleet-allocation-strategy.md#spot-fleet-strategy-capacity-optimized) in this user guide\.

## Use proactive capacity rebalancing<a name="use-capacity-rebalancing"></a>

Capacity Rebalancing helps you maintain workload availability by proactively augmenting your fleet with a new Spot Instance before a running Spot Instance receives the two\-minute Spot Instance interruption notice\. When Capacity Rebalancing is enabled, Auto Scaling or Spot Fleet attempts to proactively replace Spot Instances that have received a rebalance recommendation, providing the opportunity to rebalance your workload to new Spot Instances that are not at elevated risk of interruption\.

Capacity Rebalancing complements the capacity optimized allocation strategy \(which is designed to help find the most optimal spare capacity\) and the mixed instances policy \(which is designed to enhance availability by deploying instances across multiple instance types running in multiple Availability Zones\)\.

For more information, see [Capacity Rebalancing](spot-fleet-capacity-rebalance.md)\.

## Use integrated AWS services to manage your Spot Instances<a name="use-integrated-aws-services"></a>

Other AWS services integrate with Spot to reduce overall compute costs without the need to manage the individual instances or fleets\. We recommend that you consider the following solutions for your applicable workloads: Amazon EMR, Amazon ECS, AWS Batch, Amazon EKS, SageMaker, AWS Elastic Beanstalk, and Amazon GameLift\. To learn more about Spot best practices with these services, see the [Amazon EC2 Spot Instances Workshops Website](https://ec2spotworkshops.com/)\.
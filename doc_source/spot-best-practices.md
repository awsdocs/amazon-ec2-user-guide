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
+ [Use integrated AWS services to manage your Spot Instances](#use-integrated-aws-services)

## Prepare individual instances for interruptions<a name="prep-instances-for-interruptions"></a>

The best way for you to gracefully handle Spot Instance interruptions is to architect your application to be fault\-tolerant\. To accomplish this, you can take advantage of Spot Instance interruption notices\. A Spot Instance interruption notice is a warning that is issued two minutes before Amazon EC2 interrupts a Spot Instance\. We recommend that you create a rule in Amazon EventBridge that captures the interruption notification, and then triggers a checkpoint for the progress of your workload or gracefully handles the interruption\. For a detailed example that walks you through how to create and use Event rules, see [Taking Advantage of Amazon EC2 Spot Instance Interruption Notices](http://aws.amazon.com/blogs/compute/taking-advantage-of-amazon-ec2-spot-instance-interruption-notices/)\.

If your workload is "time\-flexible," you can also configure your Spot Instances to be stopped or hibernated when they are interrupted\. Amazon EC2 automatically stops or hibernates your Spot Instances on interruption, and automatically resumes the instances when we have available capacity\.

For more information, see [Spot Instance interruptions](spot-interruptions.md)\.

## Be flexible about instance types and Availability Zones<a name="be-instance-type-flexible"></a>

A Spot Instance pool is a set of unused EC2 instances with the same instance type \(for example, m5\.large\) and Availability Zone \(for example, us\-east\-1a\)\. You should be flexible about which instance types you request and in which Availability Zones you can deploy your workload\. This gives Spot a better chance to find and allocate your required amount of compute capacity\. For example, don't just ask for `c5.large` if you'd be willing to use larges from the c4, m5, and m4 families\.

Depending on your specific needs, you can evaluate which instance types you can be flexible across to fulfill your compute requirements\. If a workload can be vertically scaled, you should include larger instance types \(more vCPUs and memory\) in your requests\. If you can only scale horizontally, you should include older generation instance types because they are less in demand from On\-Demand customers\.

A good rule of thumb is to flexible across at least 10 instance types for each workload\. In addition, make sure that all Availability Zones are configured for use in your VPC and selected for your workload\.

## Use EC2 Auto Scaling groups or Spot Fleet to manage your aggregate capacity<a name="use-sf-asg-for-aggregate-capacity"></a>

Spot enables you to think in terms of aggregate capacity—in units that include vCPUs, memory, storage, or network throughput—rather than thinking in terms of individual instances\. Auto Scaling groups and Spot Fleet enable you to launch and maintain a target capacity, and to automatically request resources to replace any that are disrupted or manually terminated\. When you configure an Auto Scaling group or a Spot Fleet, you need only specify the instance types and target capacity based on your application needs\. For more information, see [Auto Scaling Groups](https://docs.aws.amazon.com/autoscaling/ec2/userguide/AutoScalingGroup.html) in the *Amazon EC2 Auto Scaling User Guide* and [Creating a Spot Fleet request](spot-fleet-requests.md#create-spot-fleet) in this user guide\.

## Use the capacity optimized allocation strategy<a name="use-capacity-optimized-allocation-strategy"></a>

Allocation strategies in Auto Scaling groups help you to provision your target capacity without the need to manually look for the Spot Instance pools with spare capacity\. We recommend using the `capacity optimized` strategy because this strategy automatically provisions instances from the most\-available Spot Instance pools\. You can also take advantage of the `capacity optimized` allocation strategy in Spot Fleet\. Because your Spot Instance capacity is sourced from pools with optimal capacity, this decreases the possibility that your Spot Instances are reclaimed\. For more information about allocation strategies, see [Spot Instances](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-purchase-options.html#asg-spot-strategy) in the *Amazon EC2 Auto Scaling User Guide* and [Configuring Spot Fleet for capacity optimization](spot-fleet.md#spot-fleet-strategy-capacity-optimized) in this user guide\.

## Use integrated AWS services to manage your Spot Instances<a name="use-integrated-aws-services"></a>

Other AWS services integrate with Spot to reduce overall compute costs without the need to manage the individual instances or fleets\. We recommend that you consider the following solutions for your applicable workloads: Amazon EMR, Amazon ECS, AWS Batch, Amazon EKS, Amazon SageMaker, AWS Elastic Beanstalk, and Amazon GameLift\. To learn more about Spot best practices with these services, see the [Amazon EC2 Spot Instances Workshops Website](https://ec2spotworkshops.com/)\.
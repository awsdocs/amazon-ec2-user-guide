# T1 Micro Instances<a name="concepts_micro_instances"></a>

T1 Micro instances \(`t1.micro`\) provide a small amount of consistent CPU resources and allow you to increase CPU capacity in short bursts when additional cycles are available\. They are well suited for lower throughput applications and websites that require additional compute cycles periodically\.

**Note**  
The `t1.micro` is a previous generation instance and it has been replaced by the `t2.micro`, which has a much better performance profile\. We recommend using the `t2.micro` instance type instead of the `t1.micro`\. For more information, see [Burstable Performance Instances](burstable-performance-instances.md)\.

The `t1.micro` instance is available as an Amazon EBS\-backed instance only\.

This documentation describes how `t1.micro` instances work so that you can understand how to apply them\. It's not our intent to specify exact behavior, but to give you visibility into the instance's behavior so you can understand its performance\.

**Topics**
+ [Hardware Specifications](#storage_instances_hardware_micro)
+ [Optimal Application of T1 Micro Instances](#optimal-application-of-micro-instances)
+ [Available CPU Resources During Spikes](#available-cpu-resources-during-spikes)
+ [When the Instance Uses Its Allotted Resources](#when-instance-uses-allotted-resources)
+ [Comparison with the m1\.small Instance Type](#comparison-with-the-m1.small-instance-type)
+ [AMI Optimization for Micro Instances](#ami-optimization-for-micro-instances)

## Hardware Specifications<a name="storage_instances_hardware_micro"></a>

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Optimal Application of T1 Micro Instances<a name="optimal-application-of-micro-instances"></a>

A `t1.micro` instance provides spiky CPU resources for workloads that have a CPU usage profile similar to what is shown in the following figure\.

![\[Good fit\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Good_Fit.png)

The instance is designed to operate with its CPU usage at essentially only two levels: the normal low background level, and then at brief spiked levels much higher than the background level\. We allow the instance to operate at up to 2 EC2 compute units \(ECUs\) \(one ECU provides the equivalent CPU capacity of a 1\.0\-1\.2 GHz 2007 Opteron or 2007 Xeon processor\)\. The ratio between the maximum level and the background level is designed to be large\. We designed `t1.micro` instances to support tens of requests per minute on your application\. However, actual performance can vary significantly depending on the amount of CPU resources required for each request on your application\.

Your application might have a different CPU usage profile than that described in the preceding section\. The following figure shows the profile for an application that isn't appropriate for a `t1.micro` instance\. The application requires continuous data\-crunching CPU resources for each request, resulting in plateaus of CPU usage that the `t1.micro` instance isn't designed to handle\.

![\[Bad fit: Plateaus\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Bad_Fit_Wide.png)

The following figure shows another profile that isn't appropriate for a `t1.micro` instance\. Here the spikes in CPU use are brief, but they occur too frequently to be serviced by a micro instance\.

![\[Bad fit: Too frequent\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Bad_Fit_Frequent.png)

The following figure shows another profile that isn't appropriate for a `t1.micro` instance\. Here the spikes aren't too frequent, but the background level between spikes is too high to be serviced by a `t1.micro` instance\.

![\[Bad fit: Background too high\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Bad_Fit_Background.png)

In each of the preceding cases of workloads not appropriate for a `t1.micro` instance, we recommend that you consider using a different instance type\. For more information about instance types, see [Instance Types](instance-types.md)\.

## Available CPU Resources During Spikes<a name="available-cpu-resources-during-spikes"></a>

When your instance *bursts* to accommodate a spike in demand for compute resources, it uses unused resources on the host\. The amount available depends on how much contention there is when the spike occurs\. The instance is never left with zero CPU resources, whether other instances on the host are spiking or not\.

## When the Instance Uses Its Allotted Resources<a name="when-instance-uses-allotted-resources"></a>

We expect your application to consume only a certain amount of CPU resources in a period of time\. If the application consumes more than your instance's allotted CPU resources, we temporarily limit the instance so it operates at a low CPU level\. If your instance continues to use all of its allotted resources, its performance will degrade\. We will increase the time that we limit its CPU level, thus increasing the time before the instance is allowed to burst again\. 

If you enable CloudWatch monitoring for your `t1.micro` instance, you can use the "Avg CPU Utilization" graph in the AWS Management Console to determine whether your instance is regularly using all its allotted CPU resources\. We recommend that you look at the maximum value reached during each given period\. If the maximum value is 100%, we recommend that you use Amazon EC2 Auto Scaling to scale out \(with additional `t1.micro` instances and a load balancer\), or move to a larger instance type\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/)\.

Consider the three suboptimal profiles from the preceding section and what it might look like when the instance consumes its allotted resources and we limit its CPU level\. If an instance consumes its allotted resources, we restrict it to the low background level\. The following figure shows long plateaus of data\-crunching CPU usage\. The CPU hits the maximum allowed level and stays there until the instance's allotted resources are consumed for the period\. At that point, we limit the instance to operate at the low background level, and it operates there until we allow it to burst above that level again\. The instance again stays there until the allotted resources are consumed and we limit it again \(not seen on the graph\)\.

![\[Bad fit: Too wide; limited\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Bad_Fit_Wide_Throttled.png)

The following figure shows requests that are too frequent\. The instance uses its allotted resources after only a few requests and so we limit it\. After we lift the restriction, the instance maxes out its CPU usage trying to keep up with the requests, and we limit it again\.

![\[Bad fit: Too frequent; limited\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Bad_Fit_Frequent_Throttled.png)

The following figure shows a background level that is too high\. Notice that the instance doesn't have to be operating at the maximum CPU level for us to limit it\. We limit the instance when it's operating above the normal background level and has consumed its allotted resources for the given period\. In this case \(as in the preceding one\), the instance can't keep up with the work, and we limit it again\.

![\[Bad fit: Background too high; limited\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Bad_Fit_Background_Throttled.png)

## Comparison with the m1\.small Instance Type<a name="comparison-with-the-m1.small-instance-type"></a>

The `t1.micro` instance provides different levels of CPU resources at different times \(up to 2 ECUs\)\. By comparison, the `m1.small` instance type provides 1 ECU at all times\. The following figure illustrates the difference\.

![\[General comparison with m1.small\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_General_Comparison_to_m1small.png)

Let's compare the CPU usage of a `t1.micro` instance with an `m1.small` instance for the various scenarios we've discussed in the preceding sections\. The following figure that follows shows an optimal scenario for a `t1.micro` instance \(the left graph\) and how it might look for an `m1.small` instance \(the right graph\)\. In this case, we don't need to limit the `t1.micro` instance\. The processing time on the `m1.small` instance would be longer for each spike in CPU demand compared to the `t1.micro` instance\.

![\[Comparison with m1.small: optimal situation\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Comparison_Optimal.png)

The following figure shows the scenario with the data\-crunching requests that used up the allotted resources on the `t1.micro` instance, and how they might look with the `m1.small` instance\.

![\[Comparison with m1.small: too wide plateaus\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Comparison_Too_Wide.png)

The following figure shows the frequent requests that used up the allotted resources on the `t1.micro` instance, and how they might look on the `m1.small` instance\.

![\[Comparison with m1.small: too frequent\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Comparison_Frequent.png)

The following figure shows the situation where the background level used up the allotted resources on the `t1.micro` instance, and how it might look on the `m1.small` instance\.

![\[Comparison with m1.small: background too high\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Micro_Comparison_Background.png)

## AMI Optimization for Micro Instances<a name="ami-optimization-for-micro-instances"></a>

We recommend that you follow these best practices when optimizing an AMI for the `t1.micro` instance type:
+ Design the AMI to run on 600 MB of RAM
+ Limit the number of recurring processes that use CPU time \(for example, cron jobs, daemons\)

You can optimize performance using swap space and virtual memory \(for example, by setting up swap space in a separate partition from the root file system\)\.
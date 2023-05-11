# Dedicated Instances<a name="dedicated-instance"></a>

By default, EC2 instances run on shared tenancy hardware\. Dedicated Instances are EC2 instances that run on hardware that's dedicated to a single customer\. Dedicated Instances that belong to different AWS accounts are physically isolated at a hardware level, even if those accounts are linked to a single payer account\. However, Dedicated Instances might share hardware with other instances from the same AWS account that are not Dedicated Instances\.

A *Dedicated Host* is also a physical server that's dedicated for your use\. With a Dedicated Host, you have visibility and control over how instances are placed on the server\. For more information, see [Dedicated Hosts](dedicated-hosts-overview.md)\.

**Topics**
+ [Dedicated Instance basics](#dedicated-howitworks)
+ [Supported features](#features)
+ [Differences between Dedicated Instances and Dedicated Hosts](#dh-di-diffs)
+ [Dedicated Instances limitations](#dedicated-limits)
+ [Pricing for Dedicated Instances](#dedicated-instance-pricing)
+ [Work with Dedicated Instances](dedicated-usage-overview.md)

## Dedicated Instance basics<a name="dedicated-howitworks"></a>

A VPC can have a tenancy of either `default` or `dedicated`\. By default, your VPCs have `default` tenancy and instances launched into a `default` tenancy VPC have `default` tenancy\. To launch Dedicated Instances, do the following:
+ Create a VPC with a tenancy of `dedicated`, so that all instances in the VPC run as Dedicated Instances\. For more information, see [Create a VPC with a dedicated instance tenancy](dedicated-usage-overview.md#creatingdedicatedvpc)\.
+ Create a VPC with a tenancy of `default` and manually specify a tenancy of `dedicated` for the instances to run as Dedicated Instances\. For more information, see [Launch Dedicated Instances into a VPC](dedicated-usage-overview.md#dedicatedinstancesintovpc)\.

## Supported features<a name="features"></a>

Dedicated Instances support the following features and AWS service integrations:

**Topics**
+ [Reserved Instances](#dedicatedreservedinstances)
+ [Automatic scaling](#dedicated-instance-autoscaling)
+ [Automatic recovery](#dedicated-instance-recovery)
+ [Dedicated Spot Instances](#dedicated-instance-spot)
+ [Burstable performance instances](#dedicated-instance-burstable)

### Reserved Instances<a name="dedicatedreservedinstances"></a>

To guarantee that sufficient capacity is available to launch Dedicated Instances, you can purchase Dedicated Reserved Instances or Capacity Reservations\. For more information, see [Reserved Instances](ec2-reserved-instances.md) and [On\-Demand Capacity Reservations](ec2-capacity-reservations.md)\. 

When you purchase a Dedicated Reserved Instance, you are purchasing the capacity to launch a Dedicated Instance into a VPC at a much reduced usage fee; the price break in the usage charge applies only if you launch an instance with dedicated tenancy\. When you purchase a Reserved Instance with default tenancy, it applies only to a running instance with `default` tenancy; it does not apply to a running instance with `dedicated` tenancy\.

You can't use the modification process to change the tenancy of a Reserved Instance after you've purchased it\. However, you can exchange a Convertible Reserved Instance for a new Convertible Reserved Instance with a different tenancy\.

### Automatic scaling<a name="dedicated-instance-autoscaling"></a>

You can use Amazon EC2 Auto Scaling to launch Dedicated Instances\. For more information, see [Launching Auto Scaling Instances in a VPC](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-in-vpc.html) in the *Amazon EC2 Auto Scaling User Guide*\.

### Automatic recovery<a name="dedicated-instance-recovery"></a>

You can configure automatic recovery for a Dedicated Instance if it becomes impaired due to an underlying hardware failure or a problem that requires AWS involvement to repair\. For more information, see [Recover your instance](ec2-instance-recover.md)\.

### Dedicated Spot Instances<a name="dedicated-instance-spot"></a>

You can run a Dedicated Spot Instance by specifying a tenancy of `dedicated` when you create a Spot Instance request\. For more information, see [Specify a tenancy for your Spot Instances](spot-requests.md#spot-instance-tenancy)\.

### Burstable performance instances<a name="dedicated-instance-burstable"></a>

You can leverage the benefits of running on dedicated tenancy hardware with [Burstable performance instances](burstable-performance-instances.md)\. T3 Dedicated Instances launch in unlimited mode by default, and they provide a baseline level of CPU performance with the ability to burst to a higher CPU level when required by your workload\. The T3 baseline performance and ability to burst are governed by CPU credits\. Because of the burstable nature of the T3 instance types, we recommend that you monitor how your T3 instances use the CPU resources of the dedicated hardware for the best performance\. T3 Dedicated Instances are intended for customers with diverse workloads that display random CPU behavior, but that ideally have average CPU usage at or below the baseline usages\. For more information, see [Key concepts and definitions for burstable performance instances](burstable-credits-baseline-concepts.md)\.

Amazon EC2 has systems in place to identify and correct variability in performance\. However, it is still possible to experience short\-term variability if you launch multiple T3 Dedicated Instances that have correlated CPU usage patterns\. For these more demanding or correlated workloads, we recommend using M5 or M5a Dedicated Instances rather than T3 Dedicated Instances\.

## Differences between Dedicated Instances and Dedicated Hosts<a name="dh-di-diffs"></a>

Dedicated Instances and Dedicated Hosts can both be used to launch Amazon EC2 instances onto physical servers that are dedicated for your use\.

There are no performance, security, or physical differences between Dedicated Instances and instances on Dedicated Hosts\. However, there are some differences between the two\. The following table highlights some of the key differences between Dedicated Instances and Dedicated Hosts:


|  | Dedicated Host | Dedicated Instance | 
| --- | --- | --- | 
| Billing | Per\-host billing | Per\-instance billing | 
| Visibility of sockets, cores, and host ID | Provides visibility of the number of sockets and physical cores | No visibility | 
| Host and instance affinity | Allows you to consistently deploy your instances to the same physical server over time | Not supported | 
| Targeted instance placement | Provides additional visibility and control over how instances are placed on a physical server | Not supported | 
| Automatic instance recovery | Supported\. For more information, see [Host recovery](dedicated-hosts-recovery.md)\. | Supported | 
| Bring Your Own License \(BYOL\) | Supported | Partial support \* | 
| Capacity Reservations | Not supported | Supported | 

\* Microsoft SQL Server with License Mobility through Software Assurance, and Windows Virtual Desktop Access \(VDA\) licenses can be used with Dedicated Instance\.

For more information about Dedicated Hosts, see [Dedicated Hosts](dedicated-hosts-overview.md)\.

## Dedicated Instances limitations<a name="dedicated-limits"></a>

Keep the following in mind when using Dedicated Instances:
+ Some AWS services or their features are not supported with a VPC with the instance tenancy set to `dedicated`\. Refer to the respective service's documentation to confirm if there are any limitations\.
+ Some instance types can't be launched into a VPC with the instance tenancy set to `dedicated`\. For more information about supported instance types, see [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/)\.
+ When you launch a Dedicated Instance backed by Amazon EBS, the EBS volume doesn't run on single\-tenant hardware\.

## Pricing for Dedicated Instances<a name="dedicated-instance-pricing"></a>

Pricing for Dedicated Instances is different from pricing for On\-Demand Instances\. For more information, see the [Amazon EC2 Dedicated Instances product page](https://aws.amazon.com/dedicated-instances)\.
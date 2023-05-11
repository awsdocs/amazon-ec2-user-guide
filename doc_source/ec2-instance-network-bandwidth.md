# Amazon EC2 instance network bandwidth<a name="ec2-instance-network-bandwidth"></a>

Instance bandwidth specifications apply to both inbound and outbound traffic for the instance\. For example, if an instance specifies up to 10 Gbps of bandwidth, that means is has up to 10 Gbps of bandwidth for inbound traffic, and 10 Gbps for outbound traffic\. The network bandwidth that's available to an EC2 instance depends on several factors, as follows\.

**Multi\-flow traffic**

Bandwidth for aggregate multi\-flow traffic available to an instance depends on the destination of the traffic\.
+ Within the Region – Traffic can utilize the full network bandwidth available to the instance\.
+ To other Regions, an internet gateway, Direct Connect, or local gateways \(LGW\) – Traffic can utilize up to 50% of the network bandwidth available to a [current generation instance](instance-types.md#current-gen-instances) with a minimum of 32 vCPUs\. Bandwidth for a current generation instance with less than 32 vCPUs is limited to 5 Gbps\.

**Single\-flow traffic**  
Baseline bandwidth for single\-flow \(5\-tuple\) traffic is limited to 5 Gbps when instances are not in the same [cluster placement group](placement-groups.md#placement-groups-cluster)\. To reduce latency and increase single\-flow bandwidth, try one of the following:
+ Use a cluster placement group to achieve up to 10 Gbps bandwidth for instances within the same placement group\.
+ Set up multiple paths between two endpoints to achieve higher bandwidth with Multipath TCP \(MPTCP\)\.
+ Configure ENA Express for eligible instances within the same subnet to achieve up to 25 Gbps between those instances\.

## Available instance bandwidth<a name="available-instance-bandwidth"></a>

The available network bandwidth of an instance depends on the number of vCPUs that it has\. For example, an `m5.8xlarge` instance has 32 vCPUs and 10 Gbps network bandwidth, and an `m5.16xlarge` instance has 64 vCPUs and 20 Gbps network bandwidth\. However, instances might not achieve this bandwidth; for example, if they exceed network allowances at the instance level, such as packet per second or number of tracked connections\. How much of the available bandwidth the traffic can utilize depends on the number of vCPUs and the destination\. For example, an `m5.16xlarge` instance has 64 vCPUs, so traffic to another instance in the Region can utilize the full bandwidth available \(20 Gbps\)\. However, traffic to another instance in a different Region can utilize only 50% of the bandwidth available \(10 Gbps\)\.

Typically, instances with 16 vCPUs or fewer \(size `4xlarge` and smaller\) are documented as having "up to" a specified bandwidth; for example, "up to 10 Gbps"\. These instances have a baseline bandwidth\. To meet additional demand, they can use a network I/O credit mechanism to burst beyond their baseline bandwidth\. Instances can use burst bandwidth for a limited time, typically from 5 to 60 minutes, depending on the instance size\.

An instance receives the maximum number of network I/O credits at launch\. If the instance exhausts its network I/O credits, it returns to its baseline bandwidth\. A running instance earns network I/O credits whenever it uses less network bandwidth than its baseline bandwidth\. A stopped instance does not earn network I/O credits\. Instance burst is on a best effort basis, even when the instance has credits available, as burst bandwidth is a shared resource\.

There are separate network I/O credit buckets for inbound and outbound traffic\.

**Base and burst network performance**

The following documentation describes the network performance for all instances, plus the baseline network bandwidth available for instances that can use burst bandwidth\.
+ [General purpose instances](general-purpose-instances.md#general-purpose-network-performance)
+ [Compute optimized instances](compute-optimized-instances.md)
+ [Memory optimized instances](memory-optimized-instances.md)
+ [Storage optimized instances](storage-optimized-instances.md)
+ [Accelerated computing instances](accelerated-computing-instances.md#gpu-network-performance)

**To view network performance using the AWS CLI**  
You can use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) AWS CLI command to display information about an instance type\. The following example displays network performance information for all C5 instances\.

```
aws ec2 describe-instance-types --filters "Name=instance-type,Values=c5.*" --query "InstanceTypes[].[InstanceType, NetworkInfo.NetworkPerformance]" --output table
-------------------------------------
|       DescribeInstanceTypes       |
+--------------+--------------------+
|  c5.4xlarge  |  Up to 10 Gigabit  |
|  c5.xlarge   |  Up to 10 Gigabit  |
|  c5.12xlarge |  12 Gigabit        |
|  c5.24xlarge |  25 Gigabit        |
|  c5.9xlarge  |  10 Gigabit        |
|  c5.2xlarge  |  Up to 10 Gigabit  |
|  c5.large    |  Up to 10 Gigabit  |
|  c5.metal    |  25 Gigabit        |
|  c5.18xlarge |  25 Gigabit        |
+--------------+--------------------+
```

## Monitor instance bandwidth<a name="monitor-instance-bandwidth"></a>

You can use CloudWatch metrics to monitor instance network bandwidth and the packets sent and received\. You can use the network performance metrics provided by the Elastic Network Adapter \(ENA\) driver to monitor when traffic exceeds the network allowances that Amazon EC2 defines at the instance level\.

You can configure whether Amazon EC2 sends metric data for the instance to CloudWatch using one\-minute periods or five\-minute periods\. It is possible that the network performance metrics would show that an allowance was exceeded and packets were dropped while the CloudWatch instance metrics do not\. This can happen when the instance has a short spike in demand for network resources \(known as a microburst\), but the CloudWatch metrics are not granular enough to reflect these microsecond spikes\.

**Learn more**
+ [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)
+ [Network performance metrics](monitoring-network-performance-ena.md)
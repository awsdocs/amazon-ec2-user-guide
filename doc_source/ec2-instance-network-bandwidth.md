# Amazon EC2 instance network bandwidth<a name="ec2-instance-network-bandwidth"></a>

The network bandwidth available to an EC2 instance depends on the destination of the traffic\. The following bandwidth quotas apply to aggregate multi\-flow bandwidth on a per\-instance basis\.

**Inside the Region**  
Traffic between an instance and a target destination in the same Region can utilize the full network bandwidth available to the instance\.

**Outside the Region**  
Traffic between an instance and a target destination in a different Region has an aggregate bandwidth quota of 5 Gbps\.

Single flow \(5\-tuple\) bandwidth is limited to 5 Gbps, regardless of the direction of traffic\. For use cases that require low latency and high single flow bandwidth, use a [cluster placement group](placement-groups.md#placement-groups-cluster) to achieve bandwidth up to 10 Gbps for instances in the same placement group\.

## Monitor instance bandwidth<a name="monitor-instance-bandwidth"></a>

You can use CloudWatch metrics to monitor instance network bandwidth and the packets sent and received\. You can use the network performance metrics provided by the Elastic Network Adapter \(ENA\) driver to monitor when traffic exceeds the network allowances that Amazon EC2 defines at the instance level\.

You can configure whether Amazon EC2 sends metric data for the instance to CloudWatch using one\-minute periods or five\-minute periods\. It is possible that the network performance metrics would show that an allowance was exceeded and packets were dropped while the CloudWatch instance metrics do not\. This can happen when the instance has a short spike in demand for network resources \(known as a microburst\), but the CloudWatch metrics are not granular enough to reflect these microsecond spikes\.

**Learn more**
+ [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)
+ [Network performance metrics](monitoring-network-performance-ena.md)

## Available instance bandwidth<a name="available-instance-bandwidth"></a>

The following documentation describes the network bandwidth available by instance type\. Instances might not achieve this bandwidth, for example, if they exceed network allowances at the instance level, such as packet per second or number of tracked connections\.
+ [General purpose instances](general-purpose-instances.md#general-purpose-network-performance)
+ [Compute optimized instances](compute-optimized-instances.md)
+ [Memory optimized instances](memory-optimized-instances.md)
+ [Storage optimized instances](storage-optimized-instances.md)
+ [Accelerated computing instances](accelerated-computing-instances.md#gpu-network-performance)

You can use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) AWS CLI command to display information about an instance type, such as its network performance\. The following example displays this information for all C5 instances\.

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
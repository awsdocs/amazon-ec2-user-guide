# Monitoring network performance for your Linux instance<a name="monitoring-network-performance-ena"></a>

The Elastic Network Adapter \(ENA\) driver publishes network performance metrics from the instances where they are enabled\. You can use these metrics to troubleshoot instance performance issues, choose the right instance size for a workload, plan scaling activities proactively, and benchmark applications to determine whether they maximize the performance available on an instance\.

Amazon EC2 defines network maximums at the instance level to ensure a high\-quality networking experience, including consistent network performance across instance sizes\. AWS provides maximums for the following for each instance:
+ **Bandwidth capability** — Each EC2 instance has a maximum bandwidth for aggregate inbound and outbound traffic, based on instance type and size\. Some instances use a network I/O credit mechanism to allocate network bandwidth based on average bandwidth utilization\. Amazon EC2 also has maximum bandwidth for traffic to AWS Direct Connect and the internet\.
+ **Packet\-per\-second \(PPS\) performance** — Each EC2 instance has a maximum PPS performance, based on instance type and size\.
+ **Connections tracked** — The security group tracks each connection established to ensure that return packets are delivered as expected\. There is a maximum number of connections that can be tracked per instance\.
+ **Link\-local service access** — Amazon EC2 provides a maximum PPS per network interface for traffic to services such as the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.

When the network traffic for an instance exceeds a maximum, AWS shapes the traffic that exceeds the maximum by queueing and then dropping network packets\. You can monitor when traffic exceeds a maximum using the network performance metrics\. These metrics inform you of impact to network traffic, and possible network performance issues, in real time\.

**Topics**
+ [Requirements](#network-performance-metrics-requirements)
+ [Metrics for the ENA driver](#network-performance-metrics)
+ [View the network performance metrics for your instance](#view-network-performance-metrics)

## Requirements<a name="network-performance-metrics-requirements"></a>
+ Install ENA driver version 2\.2\.10 or later\. To verify the installed version, use the ethtool command\. In the following example, the version meets the minimum requirement\.

  ```
  [ec2-user ~]$ ethtool -i eth0 | grep version
  version: 2.2.10
  ```

  To upgrade your ENA driver, see [Enhanced networking](enhanced-networking-ena.md)\.
+ To import these metrics to Amazon CloudWatch, install the CloudWatch agent\. For more information, see [Collect network performance metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-network-performance.html) in the *Amazon CloudWatch User Guide*\.

## Metrics for the ENA driver<a name="network-performance-metrics"></a>

The ENA driver delivers the following metrics to the instance in real time\. They provide the cumulative number of packets shaped on each network interface since the last driver reset\.


| Metric | Description | 
| --- | --- | 
| bw\_in\_allowance\_exceeded |  The number of packets shaped because the inbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| bw\_out\_allowance\_exceeded |  The number of packets shaped because the outbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| conntrack\_allowance\_exceeded |  The number of packets shaped because connection tracking exceeded the maximum for the instance and new connections could not be established\. This can result in packet loss for traffic to or from the instance\.  | 
| linklocal\_allowance\_exceeded |  The number of packets shaped because the PPS of the traffic to local proxy services exceeded the maximum for the network interface\. This impacts traffic to the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.  | 
| pps\_allowance\_exceeded |  The number of packets shaped because the bidirectional PPS exceeded the maximum for the instance\.  | 

## View the network performance metrics for your instance<a name="view-network-performance-metrics"></a>

You can publish metrics to your favorite tools to visualize the metric data\. For example, you can publish the metrics to Amazon CloudWatch using the CloudWatch agent\. The agent enables you to select individual metrics and control publication\.

You can also use the ethtool to retrieve the metrics for each network interface, such as eth0, as follows\.

```
[ec2-user ~]$ ethtool -S eth0
bw_in_allowance_exceeded: 0
bw_out_allowance_exceeded: 0
pps_allowance_exceeded: 0
conntrack_allowance_exceeded: 0
linklocal_allowance_exceeded: 0
```

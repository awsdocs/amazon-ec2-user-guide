# Monitor network performance for your EC2 instance<a name="monitoring-network-performance-ena"></a>

The Elastic Network Adapter \(ENA\) driver publishes network performance metrics from the instances where they are enabled\. You can use these metrics to troubleshoot instance performance issues, choose the right instance size for a workload, plan scaling activities proactively, and benchmark applications to determine whether they maximize the performance available on an instance\.

Amazon EC2 defines network maximums at the instance level to ensure a high\-quality networking experience, including consistent network performance across instance sizes\. AWS provides maximums for the following for each instance:
+ **Bandwidth capability** – Each EC2 instance has a maximum bandwidth for aggregate inbound and outbound traffic, based on instance type and size\. Some instances use a network I/O credit mechanism to allocate network bandwidth based on average bandwidth utilization\. Amazon EC2 also has maximum bandwidth for traffic to AWS Direct Connect and the internet\. For more information, see [Amazon EC2 instance network bandwidth](ec2-instance-network-bandwidth.md)\.
+ **Packet\-per\-second \(PPS\) performance** – Each EC2 instance has a maximum PPS performance, based on instance type and size\.
+ **Connections tracked** – The security group tracks each connection established to ensure that return packets are delivered as expected\. There is a maximum number of connections that can be tracked per instance\. For more information, see [Security group connection tracking](security-group-connection-tracking.md)
+ **Link\-local service access** – Amazon EC2 provides a maximum PPS per network interface for traffic to services such as the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.

When the network traffic for an instance exceeds a maximum, AWS shapes the traffic that exceeds the maximum by queueing and then dropping network packets\. You can monitor when traffic exceeds a maximum using the network performance metrics\. These metrics inform you, in real time, of impact to network traffic and possible network performance issues\.

**Topics**
+ [Requirements](#network-performance-metrics-requirements)
+ [Metrics for the ENA driver](#network-performance-metrics)
+ [View the network performance metrics for your Linux instance](#view-network-performance-metrics)
+ [Metrics for ENA Express](#network-performance-metrics-ena-express)
+ [Network performance metrics with the DPDK driver for ENA](#network-performance-metrics-dpdk)
+ [Metrics on instances running FreeBSD](#network-performance-metrics-freebsd)

## Requirements<a name="network-performance-metrics-requirements"></a>

The following requirements apply to Linux instances\.
+ Install ENA driver version 2\.2\.10 or later\. To verify the installed version, use the ethtool command\. In the following example, the version meets the minimum requirement\.

  ```
  [ec2-user ~]$ ethtool -i eth0 | grep version
  version: 2.2.10
  ```

  To upgrade your ENA driver, see [Enhanced networking](enhanced-networking-ena.md)\.
+ To import these metrics to Amazon CloudWatch, install the CloudWatch agent\. For more information, see [Collect network performance metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-network-performance.html) in the Amazon CloudWatch User Guide\.
+ In order to support `conntrack_allowance_available` metric, install ENA driver version 2\.8\.1\.

## Metrics for the ENA driver<a name="network-performance-metrics"></a>

The ENA driver delivers the following metrics to the instance in real time\. They provide the cumulative number of packets queued or dropped on each network interface since the last driver reset\.

The following metrics are available on Linux instances, FreeBSD instances, and DPDK environments\.


| Metric | Description | 
| --- | --- | 
| bw\_in\_allowance\_exceeded |  The number of packets queued or dropped because the inbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| bw\_out\_allowance\_exceeded |  The number of packets queued or dropped because the outbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| conntrack\_allowance\_exceeded |  The number of packets dropped because connection tracking exceeded the maximum for the instance and new connections could not be established\. This can result in packet loss for traffic to or from the instance\.  | 
| conntrack\_allowance\_available\* | The number of tracked connections that can be established by the instance before hitting the Connections Tracked allowance of that instance type\. | 
| linklocal\_allowance\_exceeded |  The number of packets dropped because the PPS of the traffic to local proxy services exceeded the maximum for the network interface\. This impacts traffic to the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.  | 
| pps\_allowance\_exceeded |  The number of packets queued or dropped because the bidirectional PPS exceeded the maximum for the instance\.  | 

**\*** conntrack\_allowance\_available metric is supported on Nitro based instance types\. Currently, these [Nitro System](instance-types.md#ec2-nitro-instances) instances do not publish the conntrack utilization metric: C6a, C6gn, C6i, C6id, C6in, Hpc6a, I4i, Im4gn, Is4gen, M6a, M6i, M6in, M6idn, R6i, R6id, R6idn, R6in, Trn1, X2idn, X2iedn, X2iezn, c7gn, p4d, p4de\. Support for these instances is coming soon\.

## View the network performance metrics for your Linux instance<a name="view-network-performance-metrics"></a>

You can publish metrics to your favorite tools to visualize the metric data\. For example, you can publish the metrics to Amazon CloudWatch using the CloudWatch agent\. The agent enables you to select individual metrics and control publication\.

You can also use the ethtool to retrieve the metrics for each network interface, such as eth0, as follows\.

```
[ec2-user ~]$ ethtool -S eth0
     bw_in_allowance_exceeded: 0
     bw_out_allowance_exceeded: 0
     pps_allowance_exceeded: 0
     conntrack_allowance_exceeded: 0
     linklocal_allowance_exceeded: 0
     conntrack_allowance_available: 136812
```

## Metrics for ENA Express<a name="network-performance-metrics-ena-express"></a>

ENA Express is powered by AWS Scalable Reliable Datagram \(SRD\) technology\. SRD is a high performance network transport protocol that uses dynamic routing to increase throughput and minimize tail latency\. You can use ENA Express metrics to help ensure that your instances take full advantage of the performance improvements that SRD technology provides, for example:
+ Evaluate your resources to ensure that they have sufficient capacity to establish more SRD connections\.
+ Identify where there are potential issues that prevent eligible outgoing packets from using SRD\.
+ Calculate the percentage of outgoing traffic that uses SRD for the instance\.
+ Calculate the percentage of incoming traffic that uses SRD for the instance\.

The following ENA Express metrics are available using the ethtool command for Linux based instances\.
+ `ena_srd_mode` – Describes which ENA Express features are enabled\. Values are as follows:
  + `0` = ENA Express off, UDP off
  + `1` = ENA Express on, UDP off
  + `2` = ENA Express off, UDP on
**Note**  
This only happens when ENA Express was originally enabled, and UDP was configured to use it\. The prior value is retained for UDP traffic\.
  + `3` = ENA Express on, UDP on
+ `ena_srd_eligible_tx_pkts` – The number of network packets sent within a given time period that meet SRD requirements for eligibility, as follows:
  + Both sending and receiving instance types are supported\. See the [Supported instance types for ENA Express](ena-express.md#ena-express-supported-instance-types) table for more information\.
  + Both sending and receiving instances must have ENA Express configured\.
  + The sending and receiving instances must run in the same subnet\.
  + The network path between the instances must not include middleware boxes\. ENA Express doesn't currently support middleware boxes\.
**Note**  
The ENA Express eligibility metric covers source and destination requirements, and the network between the two endpoints\. Eligible packets can still be disqualified after they’ve already been counted\. For example, if an eligible packet is over the maximum transmission unit \(MTU\) limit, it falls back to standard ENA transmission, though the packet is still reflected as eligible in the counter\.
+ `ena_srd_tx_pkts` – The number of SRD packets transmitted within a given time period\.
+ `ena_srd_rx_pkts` – The number of SRD packets received within a given time period\.
+ `ena_srd_resource_utilization` – The percentage of the maximum allowed memory utilization for concurrent SRD connections that the instance has consumed\.

To see a list of metrics that are filtered for ENA Express, run the following ethtool command for your network interface \(shown here as `eth0`\):

```
[ec2-user ~]$ ethtool -S eth0 | grep ena_srd
NIC statistics:
	ena_srd_mode: 0
	ena_srd_tx_pkts: 0
	pena_srd_eligible_tx_pkts: 0
	ena_srd_rx_pkts: 0
	ena_srd_resource_utilization: 0
```

**Egress traffic \(outgoing packets\)**  
To ensure that your egress traffic uses SRD as expected, compare the number of SRD eligible packets \(`ena_srd_eligible_tx_pkts`\) with the number of SRD packets sent \(`ena_srd_tx_pkts`\) over a given time period\.

Significant differences between the number of eligible packets and the number of SRD packets sent are often caused by resource utilization issues\. When the network card attached to the instance has used up its maximum resources, or if packets are over the MTU limit, eligible packets are not able to transmit via SRD, and must fall back to standard ENA transmission\. Packets can also fall into this gap during live migrations or live droplet updates\. Additional troubleshooting is required to determine the root cause\.

**Note**  
You can ignore occasional minor differences between the number of eligible packets and the number of SRD packets\. This can happen when your instance establishes a connection to another instance for SRD traffic, for example\.

To find out what percentage of your total egress traffic over a given time period uses SRD, compare the number of SRD packets sent \(`ena_srd_tx_pkts`\) to the total number of packets sent for the instance \(`NetworkPacketOut`\) during that time\.

**Ingress traffic \(incoming packets\)**  
To find out what percentage of your ingress traffic uses SRD, compare the number of SRD packets received \(`ena_srd_rx_pkts`\) over a given time period to the total number of packets received for the instance \(`NetworkPacketIn`\) during that time\.

**Resource utilization**  
Resource utilization is based on the number of concurrent SRD connections a single instance can hold at a given time\. The resource utilization metric \(`ena_srd_resource_utilization`\) keeps track of your current utilization for the instance\. As utilization approaches 100%, you can expect to see performance issues\. ENA Express falls back from SRD to standard ENA transmission, and the possibility of dropped packets increases\. High resource utilization is a sign that it’s time to scale the instance out to improve network performance\.

**Note**  
When the network traffic for an instance exceeds a maximum, AWS shapes the traffic that exceeds the maximum by queueing and then dropping network packets\.

**Persistence**  
Egress and ingress metrics accrue while ENA Express is enabled for the instance\. Metrics stop accruing if ENA Express is deactivated, but persist as long as the instance is still running\. Metrics reset if the instance reboots or is terminated, or if the network interface is detached from the instance\.

## Network performance metrics with the DPDK driver for ENA<a name="network-performance-metrics-dpdk"></a>

The ENA driver version 2\.2\.0 and later supports network metrics reporting\. DPDK 20\.11 includes the ENA driver 2\.2\.0 and is the first DPDK version to support this feature\.

You can use an example application to view DPDK statistics\. To start an interactive version of the example application, run the following command\.

```
./app/dpdk-testpmd -- -i
```

Within this interactive session, you can enter a command to retrieve extended statistics for a port\. The following example command retrieves the statistics for port 0\.

```
show port xstats 0
```

The following is an example of an interactive session with the DPDK example application\.

```
[root@ip-192.0.2.0 build]# ./app/dpdk-testpmd -- -i
        EAL: Detected 4 lcore(s)
        EAL: Detected 1 NUMA nodes
        EAL: Multi-process socket /var/run/dpdk/rte/mp_socket
        EAL: Selected IOVA mode 'PA'
        EAL: Probing VFIO support...
        EAL:   Invalid NUMA socket, default to 0
        EAL:   Invalid NUMA socket, default to 0
        EAL: Probe PCI driver: net_ena (1d0f:ec20) device: 0000:00:06.0
(socket 0)
        EAL: No legacy callbacks, legacy socket not created
        Interactive-mode selected
    
        Port 0: link state change event
        testpmd: create a new mbuf pool <mb_pool_0>: n=171456,
size=2176, socket=0
        testpmd: preferred mempool ops selected: ring_mp_mc
    
        Warning! port-topology=paired and odd forward ports number, the
last port will pair with itself.
    
        Configuring Port 0 (socket 0)
        Port 0: 02:C7:17:A2:60:B1
        Checking link statuses...
        Done
        Error during enabling promiscuous mode for port 0: Operation
not supported - ignore
        testpmd> show port xstats 0
        ###### NIC extended statistics for port 0
        rx_good_packets: 0
        tx_good_packets: 0
        rx_good_bytes: 0
        tx_good_bytes: 0
        rx_missed_errors: 0
        rx_errors: 0
        tx_errors: 0
        rx_mbuf_allocation_errors: 0
        rx_q0_packets: 0
        rx_q0_bytes: 0
        rx_q0_errors: 0
        tx_q0_packets: 0
        tx_q0_bytes: 0
        wd_expired: 0
        dev_start: 1
        dev_stop: 0
        tx_drops: 0
        bw_in_allowance_exceeded: 0
        bw_out_allowance_exceeded: 0
        pps_allowance_exceeded: 0
        conntrack_allowance_exceeded: 0
        linklocal_allowance_exceeded: 0
        rx_q0_cnt: 0
        rx_q0_bytes: 0
        rx_q0_refill_partial: 0
        rx_q0_bad_csum: 0
        rx_q0_mbuf_alloc_fail: 0
        rx_q0_bad_desc_num: 0
        rx_q0_bad_req_id: 0
        tx_q0_cnt: 0
        tx_q0_bytes: 0
        tx_q0_prepare_ctx_err: 0
        tx_q0_linearize: 0
        tx_q0_linearize_failed: 0
        tx_q0_tx_poll: 0
        tx_q0_doorbells: 0
        tx_q0_bad_req_id: 0
        tx_q0_available_desc: 1023
        testpmd>
```

For more information about the example application and using it to retrieve extended statistics\. see [Testpmd Application User Guide](https://doc.dpdk.org/guides/testpmd_app_ug/) in the DPDK documentation\.

## Metrics on instances running FreeBSD<a name="network-performance-metrics-freebsd"></a>

Starting with version 2\.3\.0, the ENA FreeBSD driver supports collecting network performance metrics on instances running FreeBSD\. To enable the collection of FreeBSD metrics, enter the following command and set *interval* to a value between 1 and 3600\. This specifies how often, in seconds, to collect FreeBSD metrics\.

```
sysctl dev.ena.network_interface.eni_metrics.sample_interval=interval
```

For example, the following command sets the driver to collect FreeBSD metrics on network interface 1 every 10 seconds:

```
sysctl dev.ena.1.eni_metrics.sample_interval=10
```

To turn off the collection of FreeBSD metrics, you can run the preceding command and specify `0` as the *interval*\.

Once you are collecting FreeBSD metrics, you can retrieve the latest set of collected metrics by running the following command\.

```
sysctl dev.ena.network_interface.en1_metrics
```
# Monitor network performance for your instance<a name="monitoring-network-performance-ena"></a>

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
+ [View the network performance metrics for your Linux instance](#view-network-performance-metrics)
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
+ To import these metrics to Amazon CloudWatch, install the CloudWatch agent\. For more information, see [Collect network performance metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-network-performance.html) in the *Amazon CloudWatch User Guide*\.

## Metrics for the ENA driver<a name="network-performance-metrics"></a>

The ENA driver delivers the following metrics to the instance in real time\. They provide the cumulative number of packets shaped on each network interface since the last driver reset\.

The following metrics are available on Linux instances, FreeBSD instances, and DPDK environments\.


| Metric | Description | 
| --- | --- | 
| bw\_in\_allowance\_exceeded |  The number of packets shaped because the inbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| bw\_out\_allowance\_exceeded |  The number of packets shaped because the outbound aggregate bandwidth exceeded the maximum for the instance\.  | 
| conntrack\_allowance\_exceeded |  The number of packets shaped because connection tracking exceeded the maximum for the instance and new connections could not be established\. This can result in packet loss for traffic to or from the instance\.  | 
| linklocal\_allowance\_exceeded |  The number of packets shaped because the PPS of the traffic to local proxy services exceeded the maximum for the network interface\. This impacts traffic to the DNS service, the Instance Metadata Service, and the Amazon Time Sync Service\.  | 
| pps\_allowance\_exceeded |  The number of packets shaped because the bidirectional PPS exceeded the maximum for the instance\.  | 

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
```

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

Starting with version 2\.3\.0, the ENA FreeBSD driver supports collecting network performance metrics on instances running FreeBSD\. To enable the collection of FreeBSD metrics, enter the following commmand and set *interval* to a value between 1 and 3600\. This specifies how often, in seconds, to collect FreeBSD metrics\.

```
sysctl dev.ena.network_interface.eni_metrics.sample_interval=interval
```

For example, the following command sets the driver to collect FreeBSD metrics on network interface 1 every 10 seconds:

```
sysctl dev.ena.1.eni_metrics.sample_interval=10
```

To turn off the collection of FreeBSD metrics, you can run the preceding command and specify 0 as the *interval*\.

Once you are collecting FreeBSD metrics, you can retrieve the latest set of collected metrics by running the following command\.

```
sysctl dev.ena.network_interface.en1_metrics
```
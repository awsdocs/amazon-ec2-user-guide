# Storage optimized instances<a name="storage-optimized-instances"></a>

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage\. They are optimized to deliver tens of thousands of low\-latency, random I/O operations per second \(IOPS\) to applications\. For more information, including the technology used, see the [Amazon EC2 Instance Type Details](http://aws.amazon.com/ec2/instance-types/) page\.<a name="d2-instances"></a>

**D2 instances**

These instances are well suited for the following:
+ Massive parallel processing \(MPP\) data warehouse
+ MapReduce and Hadoop distributed computing
+ Log or data processing applications<a name="d3-instances"></a>

**D3 and D3en instances**

These instances offer scale out of instance storage and are well suited for the following:
+ Distributed file systems for Hadoop workloads
+ File storage workloads such as GPFC and BeeFS
+ Large data lakes for HPC workloads<a name="h1-instances"></a>

**H1 instances**

These instances are well suited for the following:
+ Data\-intensive workloads such as MapReduce and distributed file systems
+ Applications requiring sequential access to large amounts of data on direct\-attached instance storage
+ Applications that require high\-throughput access to large quantities of data<a name="i3-instances"></a>

**I3 and I3en instances**

These instances are well suited for the following:
+ High frequency online transaction processing \(OLTP\) systems
+ Relational databases
+ NoSQL databases
+ Cache for in\-memory databases \(for example, Redis\)
+ Data warehousing applications
+ Distributed file systems

Bare metal instances provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 I3 Instances](https://aws.amazon.com/ec2/instance-types/i3)\.
<a name="i4i-instances"></a>
**I4i instances**  
These instances are well suited for I/O intensive workloads that require small to medium sized data sets on local storage, such as transactional databases and NoSQL databases\.

For more information, see [Amazon EC2 I4i Instances](https://aws.amazon.com/ec2/instance-types/i4i)\.<a name="im4gn-instances"></a>

**Im4gn instances**

These instances are well suited for workloads that require high random I/O performance at a low latency, such as the following:
+ Relational databases
+ NoSQL databases
+ Search
+ Distributed file systems

For more information, see [Amazon EC2 Im4gn and Is4gen Instances](https://aws.amazon.com/ec2/instance-types/i4g)\.<a name="is4gen-instances"></a>

**Is4gen instances**

These instances are well suited for workloads that require high random I/O performance at a low latency, such as the following:
+ NoSQL databases
+ Indexing
+ Streaming
+ Caching
+ Warm storage

For more information, see [Amazon EC2 Im4gn and Is4gen Instances](https://aws.amazon.com/ec2/instance-types/i4g)\.

**Topics**
+ [Hardware specifications](#storage-instances-hardware)
+ [Instance performance](#storage-performance)
+ [Network performance](#storage-network-performance)
+ [Amazon EBS I/O performance](#storage-ebs-perf)
+ [Instance store volume I/O performance](#storage-instances-diskperf)
+ [Support for vCPUs](#d2-instances-cpu-support)
+ [Release notes](#storage-instance-release-notes)

## Hardware specifications<a name="storage-instances-hardware"></a>

The following is a summary of the hardware specifications for storage optimized instances\. A virtual central processing unit \(vCPU\) represents a portion of the physical CPU assigned to a virtual machine \(VM\)\. For x86 instances, there are two vCPUs per core\. For Graviton instances, there is one vCPU per core\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| d2\.xlarge | 4 | 30\.50 | 
| d2\.2xlarge | 8 | 61\.00 | 
| d2\.4xlarge | 16 | 122\.00 | 
| d2\.8xlarge | 36 | 244\.00 | 
| d3\.xlarge | 4 | 32\.00 | 
| d3\.2xlarge | 8 | 64\.00 | 
| d3\.4xlarge | 16 | 128\.00 | 
| d3\.8xlarge | 32 | 256\.00 | 
| d3en\.xlarge | 4 | 16\.00 | 
| d3en\.2xlarge | 8 | 32\.00 | 
| d3en\.4xlarge | 16 | 64\.00 | 
| d3en\.6xlarge | 24 | 96\.00 | 
| d3en\.8xlarge | 32 | 128\.00 | 
| d3en\.12xlarge | 48 | 192\.00 | 
| h1\.2xlarge | 8 | 32\.00 | 
| h1\.4xlarge | 16 | 64\.00 | 
| h1\.8xlarge | 32 | 128\.00 | 
| h1\.16xlarge | 64 | 256\.00 | 
| hs1\.8xlarge | 16 | 117\.00 | 
| i2\.xlarge | 4 | 30\.50 | 
| i2\.2xlarge | 8 | 61\.00 | 
| i2\.4xlarge | 16 | 122\.00 | 
| i2\.8xlarge | 32 | 244\.00 | 
| i3\.large | 2 | 15\.25 | 
| i3\.xlarge | 4 | 30\.50 | 
| i3\.2xlarge | 8 | 61\.00 | 
| i3\.4xlarge | 16 | 122\.00 | 
| i3\.8xlarge | 32 | 244\.00 | 
| i3\.16xlarge | 64 | 488\.00 | 
| i3\.metal | 72 | 512\.00 | 
| i3en\.large | 2 | 16\.00 | 
| i3en\.xlarge | 4 | 32\.00 | 
| i3en\.2xlarge | 8 | 64\.00 | 
| i3en\.3xlarge | 12 | 96\.00 | 
| i3en\.6xlarge | 24 | 192\.00 | 
| i3en\.12xlarge | 48 | 384\.00 | 
| i3en\.24xlarge | 96 | 768\.00 | 
| i3en\.metal | 96 | 768\.00 | 
| i4i\.large | 2 | 16\.00 | 
| i4i\.xlarge | 4 | 32\.00 | 
| i4i\.2xlarge | 8 | 64\.00 | 
| i4i\.4xlarge | 16 | 128\.00 | 
| i4i\.8xlarge | 32 | 256\.00 | 
| i4i\.16xlarge | 64 | 512\.00 | 
| i4i\.32xlarge | 128 | 1024\.00 | 
| i4i\.metal | 128 | 1024\.00 | 
| im4gn\.large | 2 | 8\.00 | 
| im4gn\.xlarge | 4 | 16\.00 | 
| im4gn\.2xlarge | 8 | 32\.00 | 
| im4gn\.4xlarge | 16 | 64\.00 | 
| im4gn\.8xlarge | 32 | 128\.00 | 
| im4gn\.16xlarge | 64 | 256\.00 | 
| is4gen\.medium | 1 | 6\.00 | 
| is4gen\.large | 2 | 12\.00 | 
| is4gen\.xlarge | 4 | 24\.00 | 
| is4gen\.2xlarge | 8 | 48\.00 | 
| is4gen\.4xlarge | 16 | 96\.00 | 
| is4gen\.8xlarge | 32 | 192\.00 | 

The storage optimized instances use the following processors\.

**AWS Graviton processors**
+ **AWS Graviton2**: Im4gn, Is4gen

**Intel processors**
+ **Intel Xeon Scalable processors \(Haswell E5\-2676 v3\)**: D2
+ **Intel Xeon Scalable processors \(Broadwell E5\-2686 v4\)**: H1, I3
+ **Intel Xeon Scalable processors \(Skylake 8175M or Cascade Lake 8259CL\)**: I3en
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8259CL\)**: D3, D3en
+ **3rd generation Intel Xeon Scalable processors \(Ice Lake 8375C\)**: I4i

For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance performance<a name="storage-performance"></a>

To ensure the best disk throughput performance from your instance on Linux, we recommend that you use the most recent version of Amazon Linux 2 or the Amazon Linux AMI\.

For instances with NVMe instance store volumes, you must use a Linux AMI with kernel version 4\.4 or later\. Otherwise, your instance will not achieve the maximum IOPS performance available\.

D2 instances provide the best disk performance when you use a Linux kernel that supports persistent grants, an extension to the Xen block ring protocol that significantly improves disk throughput and scalability\. For more information about persistent grants, see [this article](https://blog.xenproject.org/2012/11/23/improving-block-protocol-scalability-with-persistent-grants/) in the Xen Project Blog\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some storage optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some storage optimized instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="storage-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for storage optimized instances that support enhanced networking\.


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | Enhanced networking features | 
| --- | --- | --- | --- | 
| d2\.xlarge † | 1\.25 | 2\.8 | Not supported | 
| d2\.2xlarge † | 2\.5 | 10\.0 | Not supported | 
| d2\.4xlarge † | 5\.0 | 10\.0 | Not supported | 
| d2\.8xlarge † | 5\.0 | 10\.0 | Not supported | 
| d3\.xlarge † | 3\.0 | 15\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3\.2xlarge † | 6\.0 | 15\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3\.4xlarge † | 12\.5 | 15\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3\.8xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3en\.xlarge † | 6\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3en\.2xlarge † | 12\.5 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3en\.4xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3en\.6xlarge | 40\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3en\.8xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| d3en\.12xlarge | 75\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| h1\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| h1\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| h1\.8xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| h1\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i2\.xlarge † | 0\.7 | 2\.8 | Not supported | 
| i2\.2xlarge † | 1\.0 | 10\.0 | Not supported | 
| i2\.4xlarge † | 2\.0 | 10\.0 | Not supported | 
| i2\.8xlarge † | 5\.0 | 10\.0 | Not supported | 
| i3\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3\.8xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3\.metal | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3en\.large † | 2\.1 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3en\.xlarge † | 4\.2 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3en\.2xlarge † | 8\.4 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3en\.3xlarge † | 12\.5 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3en\.6xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i3en\.12xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| i3en\.24xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| i3en\.metal | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| i4i\.large † | 0\.781 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i4i\.xlarge † | 1\.875 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i4i\.2xlarge † | 4\.687 | 12\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i4i\.4xlarge † | 9\.375 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i4i\.8xlarge | 18\.75 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i4i\.16xlarge | 37\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| i4i\.32xlarge | 75\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| i4i\.metal | 75\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| im4gn\.large † | 3\.125 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| im4gn\.xlarge † | 6\.25 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| im4gn\.2xlarge † | 12\.5 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| im4gn\.4xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| im4gn\.8xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| im4gn\.16xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| is4gen\.medium † | 1\.562 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| is4gen\.large † | 3\.125 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| is4gen\.xlarge † | 6\.25 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| is4gen\.2xlarge † | 12\.5 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| is4gen\.4xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| is4gen\.8xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.

## Amazon EBS I/O performance<a name="storage-ebs-perf"></a>

Amazon EBS optimized instances use an optimized configuration stack and provide additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your Amazon EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## Instance store volume I/O performance<a name="storage-instances-diskperf"></a>

The primary data storage for D2, D3, and D3en instances is HDD instance store volumes\. The primary data storage for I3 and I3en instances is non\-volatile memory express \(NVMe\) SSD instance store volumes\.

Instance store volumes persist only for the life of the instance\. When you stop, hibernate, or terminate an instance, the applications and data in its instance store volumes are erased\. We recommend that you regularly back up or replicate important data in your instance store volumes\. For more information, see [Amazon EC2 instance store](InstanceStorage.md) and [SSD instance store volumes](ssd-instance-store.md)\.

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| d2\.xlarge | 144000 | 115200 | 
| d2\.2xlarge | 268800 | 230400 | 
| d2\.4xlarge | 499200 | 460800 | 
| d2\.8xlarge | 998400 | 921600 | 
| d3\.xlarge | 450000 | 450000 | 
| d3\.2xlarge | 1800000 | 1800000 | 
| d3\.4xlarge | 7200000 | 7200000 | 
| d3\.8xlarge | 28800000 | 28800000 | 
| d3en\.xlarge | 200000 | 200000 | 
| d3en\.2xlarge | 800000 | 800000 | 
| d3en\.4xlarge | 3200000 | 3200000 | 
| d3en\.6xlarge | 7200000 | 7200000 | 
| d3en\.8xlarge | 12800000 | 12800000 | 
| d3en\.12xlarge | 28800000 | 28800000 | 
| h1\.2xlarge | 2100 | 1350 | 
| h1\.4xlarge | 3300 | 2800 | 
| h1\.8xlarge | 6400 | 5200 | 
| h1\.16xlarge | 12800 | 10400 | 
| i2\.xlarge | 118400 | 113600 | 
| i2\.2xlarge | 256800 | 227200 | 
| i2\.4xlarge | 513600 | 454400 | 
| i2\.8xlarge | 947200 | 908800 | 
| i3\.large | 124800 | 49600 | 
| i3\.xlarge | 248000 | 99200 | 
| i3\.2xlarge | 492800 | 198400 | 
| i3\.4xlarge | 982400 | 396800 | 
| i3\.8xlarge | 1964800 | 793600 | 
| i3\.16xlarge | 3929600 | 1587200 | 
| i3\.metal | 3300000 | 1440000 | 
| i3en\.large | 42500 | 32500 | 
| i3en\.xlarge | 85000 | 65000 | 
| i3en\.2xlarge | 170000 | 130000 | 
| i3en\.3xlarge | 250000 | 200000 | 
| i3en\.6xlarge | 500000 | 400000 | 
| i3en\.12xlarge | 1000000 | 800000 | 
| i3en\.24xlarge | 2000000 | 1600000 | 
| i3en\.metal | 2000000 | 1600000 | 
| i4i\.large | 50000 | 27500 | 
| i4i\.xlarge | 100000 | 55000 | 
| i4i\.2xlarge | 200000 | 110000 | 
| i4i\.4xlarge | 400000 | 220000 | 
| i4i\.8xlarge | 800000 | 440000 | 
| i4i\.16xlarge | 1600000 | 880000 | 
| i4i\.32xlarge | 3200000 | 1760000 | 
| i4i\.metal | 3200000 | 1760000 | 
| im4gn\.large | 31250 | 25000 | 
| im4gn\.xlarge | 62500 | 50000 | 
| im4gn\.2xlarge | 125000 | 100000 | 
| im4gn\.4xlarge | 250000 | 200000 | 
| im4gn\.8xlarge | 500000 | 400000 | 
| im4gn\.16xlarge | 1000000 | 800000 | 
| is4gen\.medium | 31250 | 25000 | 
| is4gen\.large | 62500 | 50000 | 
| is4gen\.xlarge | 125000 | 100000 | 
| is4gen\.2xlarge | 250000 | 200000 | 
| is4gen\.4xlarge | 500000 | 400000 | 
| is4gen\.8xlarge | 1000000 | 800000 | 

As you fill your SSD\-based instance store volumes, the I/O performance that you get decreases\. This is due to the extra work that the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

For a comparison of the volume size across all instance types that support instance store volumes, see the [Available instance store volumes](InstanceStorage.md#instance-store-volumes) table\.

## Support for vCPUs<a name="d2-instances-cpu-support"></a>

The `d2.8xlarge` instance type provides 36 vCPUs, which might cause launch issues in some Linux operating systems that have a vCPU limit of 32\. We strongly recommend that you use the latest AMIs when you launch `d2.8xlarge` instances\.

The following Linux AMIs support launching `d2.8xlarge` instances with 36 vCPUs:
+ Amazon Linux 2 \(HVM\)
+ Amazon Linux AMI 2018\.03 \(HVM\)
+ Ubuntu Server 14\.04 LTS \(HVM\) or later
+ Red Hat Enterprise Linux 7\.1 \(HVM\)
+ SUSE Linux Enterprise Server 12 \(HVM\)

If you must use a different AMI for your application, and your `d2.8xlarge` instance launch does not complete successfully \(for example, if your instance status changes to `stopped` during launch with a `Client.InstanceInitiatedShutdown` state transition reason\), modify your instance as described in the following procedure to support more than 32 vCPUs so that you can use the `d2.8xlarge` instance type\.

**To update an instance to support more than 32 vCPUs**

1. Launch a D2 instance using your AMI, choosing any D2 instance type other than `d2.8xlarge`\.

1. Update the kernel to the latest version by following your operating system\-specific instructions\. For example, for RHEL 6, use the following command:

   ```
   sudo yum update -y kernel
   ```

1. Stop the instance\.

1. \(Optional\) Create an AMI from the instance that you can use to launch any additional `d2.8xlarge` instances that you need in the future\.

1. Change the instance type of your stopped instance to `d2.8xlarge` \(choose **Actions**, **Instance settings**, **Change instance type**, and then follow the directions\)\.

1. Start the instance\. If the instance launches properly, you are done\. If the instance still does not boot properly, proceed to the next step\.

1. \(Optional\) If the instance still does not boot properly, the kernel on your instance may not support more than 32 vCPUs\. However, you may be able to boot the instance if you limit the vCPUs\.

   1. Change the instance type of your stopped instance to any D2 instance type other than `d2.8xlarge` \(choose **Actions**, **Instance settings**, **Change instance type**, and then follow the directions\)\.

   1. Add the `maxcpus=32` option to your boot kernel parameters by following your operating system\-specific instructions\. For example, for RHEL 6, edit the `/boot/grub/menu.lst` file and add the following option to the most recent and active `kernel` entry:

      ```
      default=0
      timeout=1
      splashimage=(hd0,0)/boot/grub/splash.xpm.gz
      hiddenmenu
      title Red Hat Enterprise Linux Server (2.6.32-504.3.3.el6.x86_64)
      root (hd0,0)
      kernel /boot/vmlinuz-2.6.32-504.3.3.el6.x86_64 maxcpus=32 console=ttyS0 ro root=UUID=9996863e-b964-47d3-a33b-3920974fdbd9 rd_NO_LUKS  KEYBOARDTYPE=pc KEYTABLE=us LANG=en_US.UTF-8 xen_blkfront.sda_is_xvda=1 console=ttyS0,115200n8 console=tty0 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_NO_LVM rd_NO_DM
      initrd /boot/initramfs-2.6.32-504.3.3.el6.x86_64.img
      ```

   1. Stop the instance\.

   1. \(Optional\) Create an AMI from the instance that you can use to launch any additional `d2.8xlarge` instances that you need in the future\.

   1. Change the instance type of your stopped instance to `d2.8xlarge` \(choose **Actions**, **Instance Settings**, **Change Instance Type**, and then follow the directions\)\.

   1. Start the instance\.

## Release notes<a name="storage-instance-release-notes"></a>
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) have the following requirements:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The following Linux AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2018\.03
  + Ubuntu 14\.04 or later with `linux-aws` kernel
**Note**  
AWS Graviton\-based instance types require Ubuntu 18\.04 or later with `linux-aws` kernel
  + Red Hat Enterprise Linux 7\.4 or later
  + SUSE Linux Enterprise Server 12 SP2 or later
  + CentOS 7\.4\.1708 or later
  + FreeBSD 11\.1 or later
  + Debian GNU/Linux 9 or later
+ Instances with an AWS Graviton processors have the following requirements:
  + Use an AMI for the 64\-bit Arm architecture\.
  + Support booting through UEFI with ACPI tables and support ACPI hot\-plug of PCI devices\.

  The following AMIs meet these requirements:
  + Amazon Linux 2 \(64\-bit Arm\)
  + Ubuntu 16\.04 or later \(64\-bit Arm\)
  + Red Hat Enterprise Linux 8\.0 or later \(64\-bit Arm\)
  + SUSE Linux Enterprise Server 15 or later \(64\-bit Arm\)
  + Debian 10 or later \(64\-bit Arm\)
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ With FreeBSD AMIs, bare metal instances take nearly an hour to boot and I/O to the local NVMe storage does not complete\. As a workaround, add the following line to `/boot/loader.conf` and reboot:

  ```
  hw.nvme.per_cpu_io_queues="0"
  ```
+ The `d2.8xlarge` instance type has 36 vCPUs, which might cause launch issues in some Linux operating systems that have a vCPU limit of 32\. For more information, see [Support for vCPUs](#d2-instances-cpu-support)\.
+ The `d3.8xlarge` and `d3en.12xlarge` instances support a maximum of three attachments, including the root volume\. If you exceed the attachment limit when you add a network interface or EBS volume, this causes attachment issues on your instance\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.
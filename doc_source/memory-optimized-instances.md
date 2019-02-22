# Memory Optimized Instances<a name="memory-optimized-instances"></a>

Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory\.

**R4, R5, R5a, and R5d Instances**

These instances are well suited for the following applications:
+ High\-performance, relational \(MySQL\) and NoSQL \(MongoDB, Cassandra\) databases\.
+ Distributed web scale cache stores that provide in\-memory caching of key\-value type data \(Memcached and Redis\)\.
+ In\-memory databases using optimized data storage formats and analytics for business intelligence \(for example, SAP HANA\)\.
+ Applications performing real\-time processing of big unstructured data \(financial services, Hadoop/Spark clusters\)\.
+ High\-performance computing \(HPC\) and Electronic Design Automation \(EDA\) applications\.

For more information, see [Amazon EC2 R5 Instances](https://aws.amazon.com/ec2/instance-types/r5)\.

**High memory instances**  
High memory instances \(`u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal`\) offer 6 TiB, 9 TiB, and 12 TiB of memory per instance\. These instances are designed to run large in\-memory databases, including production installations of SAP HANA\. They offer bare metal performance with direct access to host hardware\.

**X1 Instances**

These instances are well suited for the following applications:
+ In\-memory databases such as SAP HANA, including SAP\-certified support for Business Suite S/4HANA, Business Suite on HANA \(SoH\), Business Warehouse on HANA \(BW\), and Data Mart Solutions on HANA\. For more information, see [SAP HANA on the AWS Cloud](https://aws.amazon.com/sap/solutions/saphana/)\.
+ Big\-data processing engines such as Apache Spark or Presto\.
+ High\-performance computing \(HPC\) applications\.

For more information, see [Amazon EC2 X1 Instances](https://aws.amazon.com/ec2/instance-types/x1)\.

**X1e Instances**

These instances are well suited for the following applications:
+ High\-performance databases\.
+ In\-memory databases such as SAP HANA\. For more information, see [SAP HANA on the AWS Cloud](https://aws.amazon.com/sap/solutions/saphana/)\.
+ Memory\-intensive enterprise applications\.

For more information, see [Amazon EC2 X1e Instances](https://aws.amazon.com/ec2/instance-types/x1e)\.

**z1d Instances**

These instances deliver both high compute and high memory and are well\-suited for the following applications:
+ Electronic Design Automation \(EDA\)
+ Relational database workloads

For more information, see [Amazon EC2 z1d Instances](https://aws.amazon.com/ec2/instance-types/z1d)\.

**Topics**
+ [Hardware Specifications](#memory-instances-hardware)
+ [Memory Performance](#memory-perf)
+ [Instance Performance](#memory-compute-perf)
+ [Network Performance](#memory-network-perf)
+ [SSD I/O Performance](#r5d-z1d-instances-ssd-perf)
+ [Instance Features](#memory-instances-features)
+ [Support for vCPUs](#high-cpu-support)
+ [Release Notes](#memory-instance-limits)

## Hardware Specifications<a name="memory-instances-hardware"></a>

The following is a summary of the hardware specifications for memory optimized instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| r4\.large | 2 | 15\.25 | 
| r4\.xlarge | 4 | 30\.5 | 
| r4\.2xlarge | 8 | 61 | 
| r4\.4xlarge | 16 | 122 | 
| r4\.8xlarge | 32 | 244 | 
| r4\.16xlarge | 64 | 488 | 
| r5\.large | 2 | 16 | 
| r5\.xlarge | 4 | 32 | 
| r5\.2xlarge | 8 | 64 | 
| r5\.4xlarge | 16 | 128 | 
| r5\.12xlarge | 48 | 384 | 
| r5\.24xlarge | 96 | 768 | 
| r5a\.large | 2 | 16 | 
| r5a\.xlarge | 4 | 32 | 
| r5a\.2xlarge | 8 | 64 | 
| r5a\.4xlarge | 16 | 128 | 
| r5a\.12xlarge | 48 | 384 | 
| r5a\.24xlarge | 96 | 768 | 
| r5d\.large | 2 | 16 | 
| r5d\.xlarge | 4 | 32 | 
| r5d\.2xlarge | 8 | 64 | 
| r5d\.4xlarge | 16 | 128 | 
| r5d\.12xlarge | 48 | 384 | 
| r5d\.24xlarge | 96 | 768 | 
| u\-6tb1\.metal | 448 \* | 6,144 | 
| u\-9tb1\.metal | 448 \* | 9,216 | 
| u\-12tb1\.metal | 448 \* | 12,288 | 
| x1\.16xlarge | 64 | 976 | 
| x1\.32xlarge | 128 | 1,952 | 
| x1e\.xlarge | 4 | 122 | 
| x1e\.2xlarge | 8 | 244 | 
| x1e\.4xlarge | 16 | 488 | 
| x1e\.8xlarge | 32 | 976 | 
| x1e\.16xlarge | 64 | 1,952 | 
| x1e\.32xlarge | 128 | 3,904 | 
| z1d\.large | 2 | 16 | 
| z1d\.xlarge | 4 | 32 | 
| z1d\.2xlarge | 8 | 64 | 
| z1d\.3xlarge | 12 | 96 | 
| z1d\.6xlarge | 24 | 192 | 
| z1d\.12xlarge | 48 | 384 | 

\* Each logical processor is a hyperthread on 224 cores\.

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Memory Performance<a name="memory-perf"></a>

X1 instances include Intel Scalable M​​emory Buffers, providing 300 GiB/s of sustainable memory\-read bandwidth and 140 GiB/s of sustainable memory\-write bandwidth\.

For more information about how much RAM can be enabled for memory optimized instances, see [Hardware Specifications](#memory-instances-hardware)\.

Memory optimized instances have high memory and require 64\-bit HVM AMIs to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on memory optimized instances\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

## Instance Performance<a name="memory-compute-perf"></a>

R4 instances feature up to 64 vCPUs and are powered by two AWS\-customized Intel XEON processors based on E5\-2686v4 that feature high\-memory bandwidth and larger L3 caches to boost the performance of in\-memory applications\.

X1e and X1 instances feature up to 128 vCPUs and are powered by four Intel Xeon E7\-8880 v3 processors that feature high\-memory bandwidth and larger L3 caches to boost the performance of in\-memory applications\.

High memory instances \(`u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal`\) are the first instances to be powered by an eight\-socket platform with the latest generation Intel Xeon Platinum 8176M \(Skylake\) processors that are optimized for mission\-critical enterprise workloads\.

Memory optimized instances enable increased cryptographic performance through the latest Intel AES\-NI feature, support Intel Transactional Synchronization Extensions \(TSX\) to boost the performance of in\-memory transactional data processing, and support Advanced Vector Extensions 2 \(Intel AVX2\) processor instructions to expand most integer commands to 256 bits\.

Some memory optimized instances provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(measured by CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="memory-network-perf"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance sizes that use the ENA and are documented with network performance of "Up to 10 Gbps" or "Up to 25 Gbps" use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network bandwidth is below their baseline limits, and can use these credits when they perform network data transfers\.

The following is a summary of network performance for memory optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `r4.4xlarge` and smaller \| `r5.4xlarge` and smaller \| `r5a.4xlarge` and smaller \| `r5d.4xlarge` and smaller \| `x1e.8large` and smaller \| `z1d.3xlarge` and smaller  |  Up to 10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `r4.8xlarge` \| `r5.12xlarge` \| `r5a.12xlarge` \| `r5d.12xlarge` \| `x1.16xlarge` \| `x1e.16xlarge` \| `z1d.6xlarge`  |  10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `r5a.24xlarge`  |  20 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `r4.16xlarge` \| `r5.24xlarge` \| `r5d.24xlarge` \| `u-6tb1.metal` \| `u-9tb1.metal` \| `u-12tb1.metal` \| `x1.32xlarge` \| `x1e.32xlarge` \| `z1d.12xlarge`  |  25 Gbps  | [ENA](enhanced-networking-ena.md) | 

## SSD I/O Performance<a name="r5d-z1d-instances-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
|  `r5d.large` \*  |  30,000  |  15,000  | 
|  `r5d.xlarge` \*  |  59,000  |  29,000  | 
|  `r5d.2xlarge` \*  |  117,000  |  57,000  | 
|  `r5d.4xlarge` \*  |  234,000  |  114,000  | 
|  `r5d.12xlarge`  |  700,000  |  340,000  | 
|  `r5d.24xlarge`  |  1,400,000  |  680,000  | 
|  `z1d.large` \*  |  30,000  |  15,000  | 
|  `z1d.xlarge` \*  |  59,000  |  29,000  | 
|  `z1d.2xlarge` \*  |  117,000  |  57,000  | 
|  `z1d.3xlarge` \*  |  175,000  |  75,000  | 
|  `z1d.6xlarge`  |  350,000  |  170,000  | 
|  `z1d.12xlarge`  |  700,000  |  340,000  | 

\* For these instances, you can get up to the specified performance\.

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance Features<a name="memory-instances-features"></a>

The following is a summary of features for memory optimized instances\.


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| R4 | Yes | No | No | Yes | 
| R5 | Yes | Yes | No | Yes | 
| R5a | Yes | Yes | No | Yes | 
| R5d | No | Yes | NVME \* | Yes | 
| `u-6tb1.metal` | Yes | Yes | No | No | 
| `u-9tb1.metal` | Yes | Yes | No | No | 
| `u-12tb1.metal` | Yes | Yes | No | No | 
| X1 | No | No | SSD | Yes | 
| X1e | No | No | SSD | Yes | 
| z1d | No | Yes | NVME \* | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe](nvme-ebs-volumes.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)

## Support for vCPUs<a name="high-cpu-support"></a>

Memory optimized instances provide a high number of vCPUs, which can cause launch issues with operating systems that have a lower vCPU limit\. We strongly recommend that you use the latest AMIs when you launch memory optimized instances\.

The following AMIs support launching memory optimized instances:
+ Amazon Linux 2 \(HVM\)
+ Amazon Linux AMI 2016\.03 \(HVM\) or later
+ Ubuntu Server 14\.04 LTS \(HVM\)
+ Red Hat Enterprise Linux 7\.1 \(HVM\)
+ SUSE Linux Enterprise Server 12 SP1 \(HVM\)
+ Windows Server 2019
+ Windows Server 2016
+ Windows Server 2012 R2
+ Windows Server 2012
+ Windows Server 2008 R2 64\-bit
+ Windows Server 2008 SP2 64\-bit

## Release Notes<a name="memory-instance-limits"></a>
+ R5 and R5d instances feature a 3\.1 GHz Intel Xeon Platinum 8000 series processor\.
+ R5a instances feature a 2\.5 GHz AMD EPYC 7000 series processor\.
+ The following are requirements for high memory, R5, R5a, R5d, and z1d instances:
  + NVMe drivers must be installed\. EBS volumes are exposed as [NVMe block devices](nvme-ebs-volumes.md)\.
  + Elastic Network Adapter \([ENA](enhanced-networking-ena.md)\) drivers must be installed\.

  The following AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux 2014\.03 or later
  + Ubuntu 14\.04 or later
  + SUSE Linux Enterprise Server 12 or later
  + Red Hat Enterprise Linux 7\.4 or later
  + CentOS 7 or later
  + Windows Server 2008 R2 or later
+ R5, R5a, and R5d instances support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. Every instance has at least one network interface attachment\. For example, if you have no additional network interface attachments on an EBS\-only instance, you could attach 27 EBS volumes to that instance\.
+ You can't launch X1 instances using a Windows Server 2008 SP2 64\-bit AMI, except for `x1.16xlarge` instances\.
+ You can't launch X1e instances using a Windows Server 2008 SP2 64\-bit AMI\.
+ With earlier versions of the Windows Server 2008 R2 64\-bit AMI, you can't launch `r4.large` and `r4.4xlarge` instances\. If you experience this issue, update to the latest version of this AMI\.
+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
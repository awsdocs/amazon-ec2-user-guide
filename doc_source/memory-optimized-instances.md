# Memory Optimized Instances<a name="memory-optimized-instances"></a>

Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory\.

**R4 Instances**

R4 instances are well suited for the following applications:
+ High\-performance, relational \(MySQL\) and NoSQL \(MongoDB, Cassandra\) databases\.
+ Distributed web scale cache stores that provide in\-memory caching of key\-value type data \(Memcached and Redis\)\.
+ In\-memory databases using optimized data storage formats and analytics for business intelligence \(for example, SAP HANA\)\.
+ Applications performing real\-time processing of big unstructured data \(financial services, Hadoop/Spark clusters\)\.
+ High\-performance computing \(HPC\) and Electronic Design Automation \(EDA\) applications\.

**X1 Instances**

X1 instances are well suited for the following applications:
+ In\-memory databases such as SAP HANA, including SAP\-certified support for Business Suite S/4HANA, Business Suite on HANA \(SoH\), Business Warehouse on HANA \(BW\), and Data Mart Solutions on HANA\. For more information, see [SAP HANA on the AWS Cloud](https://aws.amazon.com/sap/solutions/saphana/)\.
+ Big\-data processing engines such as Apache Spark or Presto\.
+ High\-performance computing \(HPC\) applications\.

**X1e Instances**

X1e instances are well suited for the following applications:
+ High\-performance databases\.
+ In\-memory databases such as SAP HANA\. For more information, see [SAP HANA on the AWS Cloud](https://aws.amazon.com/sap/solutions/saphana/)\.
+ Memory\-intensive enterprise applications\.

**Topics**
+ [Hardware Specifications](#memory-instances-hardware)
+ [Memory Performance](#memory-perf)
+ [Instance Performance](#memory-compute-perf)
+ [Network Performance](#memory-network-perf)
+ [Instance Features](#memory-instances-features)
+ [Support for vCPUs](#high-cpu-support)
+ [Release Notes](#memory-instance-limits)

## Hardware Specifications<a name="memory-instances-hardware"></a>

The following is a summary of the hardware specifications for memory optimized instances\.


****  

| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| r4\.large | 2 | 15\.25 | 
| r4\.xlarge | 4 | 30\.5 | 
| r4\.2xlarge | 8 | 61 | 
| r4\.4xlarge | 16 | 122 | 
| r4\.8xlarge | 32 | 244 | 
| r4\.16xlarge | 64 | 488 | 
| x1\.16xlarge | 64 | 976 | 
| x1\.32xlarge | 128 | 1,952 | 
| x1e\.xlarge | 4 | 122 | 
| x1e\.2xlarge | 8 | 244 | 
| x1e\.4xlarge | 16 | 488 | 
| x1e\.8xlarge | 32 | 976 | 
| x1e\.16xlarge | 64 | 1,952 | 
| x1e\.32xlarge | 128 | 3,904 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Memory Performance<a name="memory-perf"></a>

X1 instances include Intel Scalable M​​emory Buffers, providing 300 GiB/s of sustainable memory\-read bandwidth and 140 GiB/s of sustainable memory\-write bandwidth\.

For more information about how much RAM can be enabled for memory optimized instances, see [Hardware Specifications](#memory-instances-hardware)\.

Memory optimized instances have high\-memory and require 64\-bit HVM AMIs to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

## Instance Performance<a name="memory-compute-perf"></a>

R4 instances feature up to 64 vCPUs and are powered by two AWS\-customized Intel XEON processors based on E5\-2686v4 that feature high\-memory bandwidth and larger L3 caches to boost the performance of in\-memory applications\.

X1e and X1 instances feature up to 128 vCPUs and are powered by four Intel Xeon E7\-8880 v3 processors that feature high\-memory bandwidth and larger L3 caches to boost the performance of in\-memory applications\.

Memory optimized instances enable increased cryptographic performance through the latest Intel AES\-NI feature, support Intel Transactional Synchronization Extensions \(TSX\) to boost the performance of in\-memory transactional data processing, and support Advanced Vector Extensions 2 \(Intel AVX2\) processor instructions to expand most integer commands to 256 bits\.

Some memory optimized instances provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(measured by CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="memory-network-perf"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance types that use the ENA and support up to 10 Gbps of throughput use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network throughput is below their baseline limits, and can use these credits when they perform network data transfers\. For workloads that require access to 10 Gbps of bandwidth or more on a sustained basis, we recommend using instance types that support 10 Gbps or 25 Gbps network speeds\.

The following is a summary of network performance for memory optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `r4.4xlarge` and smaller \| `x1e.8large` and smaller   |  Up to 10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `r4.8xlarge` \| `x1.16xlarge` \| `x1e.16xlarge`  |  10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `r4.16xlarge` \| `x1.32xlarge` \| `x1e.32xlarge`  |  25 Gbps  | [ENA](enhanced-networking-ena.md) | 

## Instance Features<a name="memory-instances-features"></a>

The following is a summary of features for memory optimized instances\.


|  | VPC only | EBS only | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| R4 | Yes | Yes |  | Yes | 
| X1 | Yes |  | SSD | Yes | 
| X1e | Yes |  | SSD | Yes | 

For more information, see the following:
+ [Instance Types Available Only in a VPC](using-vpc.md#vpc-only-instance-types)
+ [Amazon EBS–Optimized Instances](EBSOptimized.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)
+ [Enhanced Networking on Linux](enhanced-networking.md)

## Support for vCPUs<a name="high-cpu-support"></a>

Memory optimized instances provide a high number of vCPUs, which can cause launch issues with operating systems that have a lower vCPU limit\. We strongly recommend that you use the latest AMIs when you launch memory optimized instances\.

The following AMIs support launching memory optimized instances:
+ Amazon Linux 2 \(HVM\)
+ Amazon Linux AMI 2016\.03 \(HVM\) or later
+ Ubuntu Server 14\.04 LTS \(HVM\)
+ Red Hat Enterprise Linux 7\.1 \(HVM\)
+ SUSE Linux Enterprise Server 12 SP1 \(HVM\)
+ Windows Server 2016
+ Windows Server 2012 R2
+ Windows Server 2012
+ Windows Server 2008 R2 64\-bit
+ Windows Server 2008 SP2 64\-bit

## Release Notes<a name="memory-instance-limits"></a>
+ You can't launch X1 instances using a Windows Server 2008 SP2 64\-bit AMI, except for `x1.16xlarge` instances\.
+ You can't launch X1e instances using a Windows Server 2008 SP2 64\-bit AMI\.
+ With earlier versions of the Windows Server 2008 R2 64\-bit AMI, you can't launch `r4.large` and `r4.4xlarge` instances\. If you experience this issue, update to the latest version of this AMI\.
+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
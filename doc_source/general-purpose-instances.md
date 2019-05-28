# General Purpose Instances<a name="general-purpose-instances"></a>

General purpose instances provide a balance of compute, memory, and networking resources, and can be used for a variety of workloads\.

**A1 Instances**

A1 instances are ideally suited for scale\-out workloads that are supported by the Arm ecosystem\. These instances are well\-suited for the following applications:
+ Web servers
+ Containerized microservices
+ Caching fleets
+ Distributed data stores
+ Applications that require the Arm instruction set

For more information, see [Amazon EC2 A1 Instances](https://aws.amazon.com/ec2/instance-types/a1)\.

**M5, M5a, M5ad, and M5d Instances**

These instances provide an ideal cloud infrastructure, offering a balance of compute, memory, and networking resources for a broad range of applications that are deployed in the cloud\. M5 instances are well\-suited for the following applications:
+ Web and application servers
+ Small and medium databases
+ Gaming servers
+ Caching fleets
+ Running backend servers for SAP, Microsoft SharePoint, cluster computing, and other enterprise applications

`m5.metal` and `m5d.metal` instances provide your applications with direct access to physical resources of the host server, such as processors and memory\. These instances are well suited for the following:
+ Workloads that require access to low\-level hardware features \(for example, Intel VT\) that are not available or fully supported in virtualized environments
+ Applications that require a non\-virtualized environment for licensing or support

For more information, see [Amazon EC2 M5 Instances](https://aws.amazon.com/ec2/instance-types/m5)\.

**T2, T3, and T3a Instances**

These instances provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload\. An Unlimited instance can sustain high CPU performance for any period of time whenever required\. For more information, see [Burstable Performance Instances](burstable-performance-instances.md)\. These instances are well\-suited for the following applications:
+ Websites and web applications
+ Code repositories
+ Development, build, test, and staging environments
+ Microservices

For more information, see [Amazon EC2 T2 Instances](https://aws.amazon.com/ec2/instance-types/t2) and [Amazon EC2 T3 Instances](https://aws.amazon.com/ec2/instance-types/t3)\.

**Topics**
+ [Hardware Specifications](#general-purpose-hardware)
+ [Instance Performance](#general-purpose-performance)
+ [Network Performance](#general-purpose-network-performance)
+ [SSD I/O Performance](#general-purpose-ssd-perf)
+ [Instance Features](#general-purpose-features)
+ [Release Notes](#general-purpose-instances-limits)
+ [Burstable Performance Instances](burstable-performance-instances.md)

## Hardware Specifications<a name="general-purpose-hardware"></a>

The following is a summary of the hardware specifications for general purpose instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| a1\.medium | 1 | 2 | 
| a1\.large | 2 | 4 | 
| a1\.xlarge | 4 | 8 | 
| a1\.2xlarge | 8 | 16 | 
| a1\.4xlarge | 16 | 32 | 
| m4\.large | 2 | 8 | 
| m4\.xlarge | 4 | 16 | 
| m4\.2xlarge | 8 | 32 | 
| m4\.4xlarge | 16 | 64 | 
| m4\.10xlarge | 40 | 160 | 
| m4\.16xlarge | 64 | 256 | 
| m5\.large | 2 | 8 | 
| m5\.xlarge | 4 | 16 | 
| m5\.2xlarge | 8 | 32 | 
| m5\.4xlarge | 16 | 64 | 
| m5\.12xlarge | 48 | 192 | 
| m5\.24xlarge | 96 | 384 | 
| m5\.metal | 96 | 384 | 
| m5a\.large | 2 | 8 | 
| m5a\.xlarge | 4 | 16 | 
| m5a\.2xlarge | 8 | 32 | 
| m5a\.4xlarge | 16 | 64 | 
| m5a\.12xlarge | 48 | 192 | 
| m5a\.24xlarge | 96 | 384 | 
| m5ad\.large | 2 | 8 | 
| m5ad\.xlarge | 4 | 16 | 
| m5ad\.2xlarge | 8 | 32 | 
| m5ad\.4xlarge | 16 | 64 | 
| m5ad\.12xlarge | 48 | 192 | 
| m5ad\.24xlarge | 96 | 384 | 
| m5d\.large | 2 | 8 | 
| m5d\.xlarge | 4 | 16 | 
| m5d\.2xlarge | 8 | 32 | 
| m5d\.4xlarge | 16 | 64 | 
| m5d\.12xlarge | 48 | 192 | 
| m5d\.24xlarge | 96 | 384 | 
| m5d\.metal | 96 | 384 | 
| t2\.nano | 1 | 0\.5 | 
| t2\.micro | 1 | 1 | 
| t2\.small | 1 | 2 | 
| t2\.medium | 2 | 4 | 
| t2\.large | 2 | 8 | 
| t2\.xlarge | 4 | 16 | 
| t2\.2xlarge | 8 | 32 | 
| t3\.nano | 2 | 0\.5 | 
| t3\.micro | 2 | 1 | 
| t3\.small | 2 | 2 | 
| t3\.medium | 2 | 4 | 
| t3\.large | 2 | 8 | 
| t3\.xlarge | 4 | 16 | 
| t3\.2xlarge | 8 | 32 | 
| t3a\.nano | 2 | 0\.5 | 
| t3a\.micro | 2 | 1 | 
| t3a\.small | 2 | 2 | 
| t3a\.medium | 2 | 4 | 
| t3a\.large | 2 | 8 | 
| t3a\.xlarge | 4 | 16 | 
| t3a\.2xlarge | 8 | 32 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Instance Performance<a name="general-purpose-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some general purpose instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

Some general purpose instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="general-purpose-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance sizes that use the ENA and are documented with network performance of "Up to 10 Gbps" or "Up to 25 Gbps" use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network bandwidth is below their baseline limits, and can use these credits when they perform network data transfers\.

The following is a summary of network performance for general purpose instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| t2\.nano, t2\.micro, t2\.small, t2\.medium, t2\.large, t2\.xlarge, t2\.2xlarge | Up to 1 Gbps |  | 
| t3\.nano, t3\.micro, t3\.small, t3\.medium, t3\.large, t3\.xlarge, t3\.2xlarge, t3a\.nano, t3a\.micro, t3a\.small, t3a\.medium, t3a\.large, t3a\.xlarge, t3a\.2xlarge | Up to 5 Gbps | [ENA](enhanced-networking-ena.md) | 
|  `m4.large`  |  Moderate  |  [Intel 82599 VF](sriov-networking.md)  | 
|  `m4.xlarge`, `m4.2xlarge`, `m4.4xlarge`  |  High  |  [Intel 82599 VF](sriov-networking.md)  | 
|  `a1.medium`, `a1.large`, `a1.xlarge`, `a1.2xlarge`, `a1.4xlarge`, `m5.large`, `m5.xlarge`, `m5.2xlarge`, `m5.4xlarge`, `m5a.large`, `m5a.xlarge`, `m5a.2xlarge`, `m5a.4xlarge`, `m5ad.large`, `m5ad.xlarge`, `m5ad.2xlarge`, `m5ad.4xlarge`, `m5d.large`, `m5d.xlarge`, `m5d.2xlarge`, `m5d.4xlarge`  |  Up to 10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `m4.10xlarge`  |  10 Gbps  |  [Intel 82599 VF](sriov-networking.md)  | 
|  `m5.12xlarge`, `m5a.12xlarge`, `m5ad.12xlarge`, `m5d.12xlarge`  |  10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `m5a.24xlarge`, `m5ad.24xlarge`  |  20 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `m4.16xlarge`, `m5.24xlarge`, `m5.metal`, `m5d.24xlarge`, `m5d.metal`  |  25 Gbps  | [ENA](enhanced-networking-ena.md) | 

## SSD I/O Performance<a name="general-purpose-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
|  `m5ad.large` \*  |  30,000  |  15,000  | 
|  `m5ad.xlarge` \*  |  59,000  |  29,000  | 
|  `m5ad.2xlarge` \*  |  117,000  |  57,000  | 
|  `m5ad.4xlarge` \*  |  234,000  |  114,000  | 
|  `m5ad.12xlarge`  |  700,000  |  340,000  | 
|  `m5ad.24xlarge`  |  1,400,000  |  680,000  | 
|  `m5d.large` \*  |  30,000  |  15,000  | 
|  `m5d.xlarge` \*  |  59,000  |  29,000  | 
|  `m5d.2xlarge` \*  |  117,000  |  57,000  | 
|  `m5d.4xlarge` \*  |  234,000  |  114,000  | 
|  `m5d.12xlarge`  |  700,000  |  340,000  | 
|  `m5d.24xlarge`  |  1,400,000  |  680,000  | 
|  `m5d.metal`  |  1,400,000  |  680,000  | 

\* For these instances, you can get up to the specified performance\.

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance Features<a name="general-purpose-features"></a>

The following is a summary of features for general purpose instances:


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| A1 | Yes | Yes | No | Yes | 
| M4 | Yes | No | No | Yes | 
| M5 | Yes | Yes | No | Yes | 
| M5a | Yes | Yes | No | Yes | 
| M5ad | No | Yes | NVMe \* | Yes | 
| M5d | No | Yes | NVMe \* | Yes | 
| T2 | Yes | No | No | No | 
| T3 | Yes | Yes | No | No | 
| T3a | Yes | Yes | No | No | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Linux Instances](nvme-ebs-volumes.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)

## Release Notes<a name="general-purpose-instances-limits"></a>
+ M5, M5d, and T3 instances feature a 3\.1 GHz Intel Xeon Platinum 8000 series processor\.
+ M5a, M5ad, and T3a instances feature a 2\.5 GHz AMD EPYC 7000 series processor\.
+ A1 instances feature a 2\.3 GHz AWS Graviton processor based on 64\-bit Arm architecture\.
+ M4, M5, M5a, M5ad, M5d, `t2.large` and larger, and `t3.large` and larger, and `t3a.large` and larger instance types require 64\-bit HVM AMIs\. They have high\-memory, and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
+ A1 instances have the following requirements:
  + Must have the NVMe drivers installed\. EBS volumes are exposed as [NVMe block devices](nvme-ebs-volumes.md)\.
  + Must have the Elastic Network Adapter \([ENA](enhanced-networking-ena.md)\) drivers installed\.
  + Must use an AMI for the 64\-bit Arm architecture\.
  + Must support booting through UEFI with ACPI tables and support ACPI hot\-plug of PCI devices\.

  The following AMIs meet these requirements:
  + Amazon Linux 2 \(64\-bit Arm\)
  + Ubuntu 16\.04 or later \(64\-bit Arm\)
  + Red Hat Enterprise Linux 7\.6 or later \(64\-bit Arm\)
  + SUSE Linux Enterprise Server 15 or later \(64\-bit Arm\)
+ M5, M5a, M5ad, M5d, T3, and T3a instances have the following requirements:
  + NVMe drivers must be installed\. EBS volumes are exposed as [NVMe block devices](nvme-ebs-volumes.md)\.
  + Elastic Network Adapter \([ENA](enhanced-networking-ena.md)\) drivers must be installed\.

  The following AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2018\.03
  + Ubuntu 14\.04 or later
  + Red Hat Enterprise Linux 7\.4 or later
  + SUSE Linux Enterprise Server 12 SP2 or later
  + CentOS 7 or later
  + FreeBSD 11\.1 or later
+ A1, M5, M5a, M5ad, M5d, T3, and T3a instances support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. Every instance has at least one network interface attachment\. For example, if you have no additional network interface attachments on an EBS\-only instance, you could attach 27 EBS volumes to that instance\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ A1, M5, M5a, M5ad, M5d, T3, and T3a instances should have system\-logind or acpid installed to support clean shutdown through API requests\.
+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
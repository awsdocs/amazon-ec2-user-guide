# Compute optimized instances<a name="compute-optimized-instances"></a>

Compute optimized instances are ideal for compute\-bound applications that benefit from high\-performance processors\.

**C5 and C5n instances**

These instances are well suited for the following:
+ Batch processing workloads
+ Media transcoding
+ High\-performance web servers
+ High\-performance computing \(HPC\)
+ Scientific modeling
+ Dedicated gaming servers and ad serving engines
+ Machine learning inference and other compute\-intensive applications

Bare metal instances, such as `c5.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 C5 Instances](https://aws.amazon.com/ec2/instance-types/c5)\.

**C6g, C6gd, and C6gn instances**

These instances are powered by AWS Graviton2 processors and are ideal for running advanced, compute\-intensive workloads, such as the following:
+ High\-performance computing \(HPC\)
+ Batch processing
+ Ad serving
+ Video encoding
+ Gaming servers
+ Scientific modeling
+ Distributed analytics
+ CPU\-based machine learning inference

Bare metal instances, such as `c6g.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 C6g Instances](https://aws.amazon.com/ec2/instance-types/c6)\.

**C6i instances**

These instances are ideal for running advanced, compute\-intensive workloads, such as the following:
+ High\-performance computing \(HPC\)
+ Batch processing
+ Ad serving
+ Video encoding
+ Distributed analytics
+ Highly scalable multiplayer gaming

For more information, see [Amazon EC2 C6i Instances](http://aws.amazon.com/ec2/instance-types/c6i)\.

**Topics**
+ [Hardware specifications](#compute-instances-hardware)
+ [Instance performance](#compute-performance)
+ [Network performance](#compute-network-performance)
+ [SSD I/O performance](#compute-ssd-perf)
+ [Instance features](#compute-instances-features)
+ [Release notes](#compute-instance-release-notes)

## Hardware specifications<a name="compute-instances-hardware"></a>

The following is a summary of the hardware specifications for compute optimized instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| c4\.large | 2 | 3\.75 | 
| c4\.xlarge | 4 | 7\.5 | 
| c4\.2xlarge | 8 | 15 | 
| c4\.4xlarge | 16 | 30 | 
| c4\.8xlarge | 36 | 60 | 
| c5\.large | 2 | 4 | 
| c5\.xlarge | 4 | 8 | 
| c5\.2xlarge | 8 | 16 | 
| c5\.4xlarge | 16 | 32 | 
| c5\.9xlarge | 36 | 72 | 
| c5\.12xlarge | 48 | 96 | 
| c5\.18xlarge | 72 | 144 | 
| c5\.24xlarge | 96 | 192 | 
| c5\.metal | 96 | 192 | 
| c5a\.large | 2 | 4 | 
| c5a\.xlarge | 4 | 8 | 
| c5a\.2xlarge | 8 | 16 | 
| c5a\.4xlarge | 16 | 32 | 
| c5a\.8xlarge | 32 | 64 | 
| c5a\.12xlarge | 48 | 96 | 
| c5a\.16xlarge | 64 | 128 | 
| c5a\.24xlarge | 96 | 192 | 
| c5ad\.large | 2 | 4 | 
| c5ad\.xlarge | 4 | 8 | 
| c5ad\.2xlarge | 8 | 16 | 
| c5ad\.4xlarge | 16 | 32 | 
| c5ad\.8xlarge | 32 | 64 | 
| c5ad\.12xlarge | 48 | 96 | 
| c5ad\.16xlarge | 64 | 128 | 
| c5ad\.24xlarge | 96 | 192 | 
| c5d\.large | 2 | 4 | 
| c5d\.xlarge | 4 | 8 | 
| c5d\.2xlarge | 8 | 16 | 
| c5d\.4xlarge | 16 | 32 | 
| c5d\.9xlarge | 36 | 72 | 
| c5d\.12xlarge | 48 | 96 | 
| c5d\.18xlarge | 72 | 144 | 
| c5d\.24xlarge | 96 | 192 | 
| c5d\.metal | 96 | 192 | 
| c5n\.large | 2 | 5\.25 | 
| c5n\.xlarge | 4 | 10\.5 | 
| c5n\.2xlarge | 8 | 21 | 
| c5n\.4xlarge | 16 | 42 | 
| c5n\.9xlarge | 36 | 96 | 
| c5n\.18xlarge | 72 | 192 | 
| c5n\.metal | 72 | 192 | 
| c6g\.medium | 1 | 2 | 
| c6g\.large | 2 | 4 | 
| c6g\.xlarge | 4 | 8 | 
| c6g\.2xlarge | 8 | 16 | 
| c6g\.4xlarge | 16 | 32 | 
| c6g\.8xlarge | 32 | 64 | 
| c6g\.12xlarge | 48 | 96 | 
| c6g\.16xlarge | 64 | 128 | 
| c6g\.metal | 64 | 128 | 
| c6gd\.medium | 1 | 2 | 
| c6gd\.large | 2 | 4 | 
| c6gd\.xlarge | 4 | 8 | 
| c6gd\.2xlarge | 8 | 16 | 
| c6gd\.4xlarge | 16 | 32 | 
| c6gd\.8xlarge | 32 | 64 | 
| c6gd\.12xlarge | 48 | 96 | 
| c6gd\.16xlarge | 64 | 128 | 
| c6gd\.metal | 64 | 128 | 
| c6gn\.medium | 1 | 2 | 
| c6gn\.large | 2 | 4 | 
| c6gn\.xlarge | 4 | 8 | 
| c6gn\.2xlarge | 8 | 16 | 
| c6gn\.4xlarge | 16 | 32 | 
| c6gn\.8xlarge | 32 | 64 | 
| c6gn\.12xlarge | 48 | 96 | 
| c6gn\.16xlarge | 64 | 128 | 
| c6i\.large | 2 | 4 | 
| c6i\.xlarge | 4 | 8 | 
| c6i\.2xlarge | 8 | 16 | 
| c6i\.4xlarge | 16 | 32 | 
| c6i\.8xlarge | 32 | 64 | 
| c6i\.12xlarge | 48 | 96 | 
| c6i\.16xlarge | 64 | 128 | 
| c6i\.24xlarge | 96 | 192 | 
| c6i\.32xlarge | 128 | 256 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimize CPU options](instance-optimize-cpu.md)\.

## Instance performance<a name="compute-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some compute optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some compute optimized instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="compute-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for compute optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| c4\.large | Moderate | [Intel 82599 VF](sriov-networking.md) | 
| c4\.xlarge \| c4\.2xlarge \| c4\.4xlarge | High | [Intel 82599 VF](sriov-networking.md) | 
| c5\.4xlarge and smaller \| c5a\.4xlarge and smaller \| c5ad\.4xlarge and smaller \| c5d\.4xlarge and smaller \| c6g\.4xlarge and smaller \| c6gd\.4xlarge and smaller | Up to 10 Gbps † | [ENA](enhanced-networking-ena.md) | 
| c4\.8xlarge | 10 Gbps | [Intel 82599 VF](sriov-networking.md) | 
| c5\.9xlarge \| c5a\.8xlarge \| c5ad\.8xlarge \| c5d\.9xlarge | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5\.12xlarge \| c5a\.12xlarge \| c5ad\.12xlarge \| c5d\.12xlarge  \| c6g\.8xlarge \| c6gd\.8xlarge | 12 Gbps | [ENA](enhanced-networking-ena.md) | 
|  c6i\.4xlarge and smaller  | Up to 12\.5 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  c6i\.8xlarge  | 12\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
|  c6i\.12xlarge  | 18\.75 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5a\.16xlarge \| c5a\.24xlarge \| c5ad\.16xlarge \| c5ad\.24xlarge \| c6g\.12xlarge \| c6gd\.12xlarge | 20 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5n\.4xlarge and smaller  \| c6gn\.4xlarge and smaller | Up to 25 Gbps † | [ENA](enhanced-networking-ena.md) | 
| c5\.18xlarge \| c5\.24xlarge \| c5\.metal \| c5d\.18xlarge \| c5d\.24xlarge \| c5d\.metal  \| c6g\.16xlarge \| c6g\.metal  \| c6gd\.16xlarge \| c6gd\.metal  \| c6gn\.4xlarge \| c6i\.16xlarge  | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
|  c6i\.24xlarge  | 37\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5n\.9xlarge \| c6gn\.8xlarge \| c6i\.32xlarge  | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
| c6gn\.12xlarge | 75 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5n\.18xlarge \| c5n\.metal  \| c6gn\.16xlarge  | 100 Gbps | [ENA](enhanced-networking-ena.md), [EFA](efa.md) | 

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.<a name="baseline-bandwidth"></a>


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | 
| --- | --- | --- | 
| c5\.large | \.75 | 10 | 
| c5\.xlarge | 1\.25 | 10 | 
| c5\.2xlarge | 2\.5 | 10 | 
| c5\.4xlarge | 5 | 10 | 
| c5a\.large | \.75 | 10 | 
| c5a\.xlarge | 1\.25 | 10 | 
| c5a\.2xlarge | 2\.5 | 10 | 
| c5a\.4xlarge | 5 | 10 | 
| c5ad\.large | \.75 | 10 | 
| c5ad\.xlarge | 1\.25 | 10 | 
| c5ad\.2xlarge | 2\.5 | 10 | 
| c5ad\.4xlarge | 5 | 10 | 
| c5d\.large | \.75 | 10 | 
| c5d\.xlarge | 1\.25 | 10 | 
| c5d\.2xlarge | 2\.5 | 10 | 
| c5d\.4xlarge | 5 | 10 | 
| c5n\.large | 3 | 25 | 
| c5n\.xlarge | 5 | 25 | 
| c5n\.2xlarge | 10 | 25 | 
| c5n\.4xlarge | 15 | 25 | 
| c6g\.medium | \.5 | 10 | 
| c6g\.large | \.75 | 10 | 
| c6g\.xlarge | 1\.25 | 10 | 
| c6g\.2xlarge | 2\.5 | 10 | 
| c6g\.4xlarge | 5 | 10 | 
| c6gd\.medium | \.5 | 10 | 
| c6gd\.large | \.75 | 10 | 
| c6gd\.xlarge | 1\.25 | 10 | 
| c6gd\.2xlarge | 2\.5 | 10 | 
| c6gd\.4xlarge | 5 | 10 | 
| c6gn\.medium | 1\.6 | 25 | 
| c6gn\.large | 3 | 25 | 
| c6gn\.xlarge | 6\.3 | 25 | 
| c6gn\.2xlarge | 12\.5 | 25 | 
| c6gn\.4xlarge | 15 | 25 | 
| c6i\.large | \.781 | 12\.5 | 
| c6i\.xlarge | 1\.562 | 12\.5 | 
| c6i\.2xlarge | 3\.125 | 12\.5 | 
| c6i\.4xlarge | 6\.25 | 12\.5 | 

## SSD I/O performance<a name="compute-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| c5ad\.large | 16,283 | 7,105 | 
| c5ad\.xlarge | 32,566 | 14,211 | 
| c5ad\.2xlarge | 65,132 | 28,421 | 
| c5ad\.4xlarge | 130,263 | 56,842 | 
| c5ad\.8xlarge | 260,526 | 113,684 | 
| c5ad\.12xlarge | 412,500 | 180,000 | 
| c5ad\.16xlarge | 521,053 | 227,368 | 
| c5ad\.24xlarge | 825,000 | 360,000 | 
| c5d\.large \* | 20,000 | 9,000 | 
| c5d\.xlarge \* | 40,000 | 18,000 | 
| c5d\.2xlarge \* | 80,000 | 37,000 | 
| c5d\.4xlarge \* | 175,000 | 75,000 | 
| c5d\.9xlarge | 350,000 | 170,000 | 
| c5d\.12xlarge | 700,000 | 340,000 | 
| c5d\.18xlarge | 700,000 | 340,000 | 
| c5d\.24xlarge | 1,400,000 | 680,000 | 
| c5d\.metal | 1,400,000 | 680,000 | 
| c6gd\.medium | 13,438 | 5,625 | 
| c6gd\.large | 26,875 | 11,250 | 
| c6gd\.xlarge | 53,750 | 22,500 | 
| c6gd\.2xlarge | 107,500 | 45,000 | 
| c6gd\.4xlarge | 215,000 | 90,000 | 
| c6gd\.8xlarge | 430,000 | 180,000 | 
| c6gd\.12xlarge | 645,000 | 270,000 | 
| c6gd\.16xlarge | 860,000 | 360,000 | 
| c6gd\.metal | 860,000 | 360,000 | 

\* For these instances, you can get up to the specified performance\.

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance features<a name="compute-instances-features"></a>

The following is a summary of features for compute optimized instances:


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| C4 | Yes | No | No | Yes | 
| C5 | Yes | Yes | No | Yes | 
| C5a | Yes | Yes | No | Yes | 
| C5ad | No | Yes | NVMe \* | Yes | 
| C5d | No | Yes | NVMe \* | Yes | 
| C5n | Yes | Yes | No | Yes | 
| C6g | Yes | Yes | No | Yes | 
| C6gd | No | Yes | NVMe \* | Yes | 
| C6gn | Yes | Yes | No | Yes | 
| C6i | Yes | Yes | No | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Linux instances](nvme-ebs-volumes.md)
+ [Amazon EC2 instance store](InstanceStorage.md)
+ [Placement groups](placement-groups.md)

## Release notes<a name="compute-instance-release-notes"></a>
+ C5 and C5d instances feature a 3\.1 GHz Intel Xeon Platinum 8000 series processor from either the first generation \(Skylake\-SP\) or second generation \(Cascade Lake\)\.
+ C5a and C5ad instances feature a second\-generation AMD EPYC processor \(Rome\) running at frequencies as high as 3\.3\. GHz\.
+ C6g, C6gd, and C6gn instances feature an AWS Graviton2 processor based on 64\-bit Arm architecture\.
+ C4 instances and instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) require 64\-bit EBS\-backed HVM AMIs\. They have high\-memory and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
+ Instances built on the Nitro System have the following requirements:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The following Linux AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2018\.03
  + Ubuntu 14\.04 \(with `linux-aws` kernel\) or later
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
+ To get the best performance from your C6i instances, ensure that they have ENA driver version 2\.2\.9 or later\. Using an ENA driver earlier than version 1\.2 with these instances causes network interface attachment failures\. The following AMIs have a compatible ENA driver\.
  + Amazon Linux 2 with kernel 4\.14\.186
  + Ubuntu 20\.04 with kernel 5\.4\.0\-1025\-aws
  + Red Hat Enterprise Linux 8\.3 with kernel 4\.18\.0\-240\.1\.1\.el8\_3\.ARCH
  + SUSE Linux Enterprise Server 15 SP2 with kernel 5\.3\.18\-24\.15\.1
+ Instances built on the Nitro System instances support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. For more information, see [Nitro System volume limits](volume_limits.md#instance-type-volume-limits)\.
+ To get the best performance from your C6gn instances, ensure that they have ENA driver version 2\.2\.9 or later\. Using an ENA driver earlier than version 1\.2 with these instances causes network interface attachment failures\. The following AMIs have a compatible ENA driver\.
  + Amazon Linux 2 with kernel 4\.14\.186
  + Ubuntu 20\.04 with kernel 5\.4\.0\-1025\-aws
  + Red Hat Enterprise Linux 8\.3 with kernel 4\.18\.0\-240\.1\.1\.el8\_3\.ARCH
  + SUSE Linux Enterprise Server 15 SP2 with kernel 5\.3\.18\-24\.15\.1
+ [Traffic Mirroring](https://docs.aws.amazon.com/vpc/latest/mirroring/) is not supported on C6gn instances\.
+ To launch AMIs for all Linux distributions on C6gn instances, use AMIs with the latest version and run an update for the latest driver\. For earlier versions, download the latest driver from [GitHub](https://github.com/amzn/amzn-drivers/tree/master/kernel/linux/ena)\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ Instances built on the Nitro System should have acpid installed to support clean shutdown through API requests\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.
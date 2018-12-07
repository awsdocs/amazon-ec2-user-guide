# Compute Optimized Instances<a name="compute-optimized-instances"></a>

Compute optimized instances are ideal for compute\-bound applications that benefit from high\-performance processors\. They are well suited for the following applications:
+ Batch processing workloads
+ Media transcoding
+ High\-performance web servers
+ High\-performance computing \(HPC\)
+ Scientific modeling
+ Dedicated gaming servers and ad serving engines
+ Machine learning inference and other compute\-intensive applications

For more information, see [Amazon EC2 C5 Instances](https://aws.amazon.com/ec2/instance-types/c5)\.

**Topics**
+ [Hardware Specifications](#compute-instances-hardware)
+ [Instance Performance](#compute-performance)
+ [Network Performance](#compute-network-performance)
+ [SSD I/O Performance](#compute-ssd-perf)
+ [Instance Features](#compute-instances-features)
+ [Release Notes](#compute-instance-limits)

## Hardware Specifications<a name="compute-instances-hardware"></a>

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
| c5\.18xlarge | 72 | 144 | 
| c5d\.large | 2 | 4 | 
| c5d\.xlarge | 4 | 8 | 
| c5d\.2xlarge | 8 | 16 | 
| c5d\.4xlarge | 16 | 32 | 
| c5d\.9xlarge | 36 | 72 | 
| c5d\.18xlarge | 72 | 144 | 
| c5n\.large | 2 | 5\.25 | 
| c5n\.xlarge | 4 | 10\.5 | 
| c5n\.2xlarge | 8 | 21 | 
| c5n\.4xlarge | 16 | 42 | 
| c5n\.9xlarge | 36 | 96 | 
| c5n\.18xlarge | 72 | 192 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Instance Performance<a name="compute-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some compute optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

Some compute optimized instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="compute-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance sizes that use the ENA and are documented with network performance of "Up to 10 Gbps" or "Up to 25 Gbps" use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network bandwidth is below their baseline limits, and can use these credits when they perform network data transfers\.

The following is a summary of network performance for compute optimized instances that support enhanced networking\.


****  

| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `c5.4xlarge` and smaller \| `c5d.4xlarge` and smaller  | Up to 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5\.9xlarge \| c5d\.9xlarge | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5n\.4xlarge and smaller | Up to 25 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5\.18xlarge \| c5d\.18xlarge | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5n\.9xlarge | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5n\.18xlarge | 100 Gbps | [ENA](enhanced-networking-ena.md) | 
| c4\.large | Moderate | [Intel 82599 VF](sriov-networking.md) | 
| c4\.xlarge \| c4\.2xlarge \| c4\.4xlarge | High | [Intel 82599 VF](sriov-networking.md) | 
| c4\.8xlarge | 10 Gbps | [Intel 82599 VF](sriov-networking.md) | 

## SSD I/O Performance<a name="compute-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
|  `c5d.large` \*  |  20,000  |  9,000  | 
|  `c5d.xlarge` \*  |  40,000  |  18,000  | 
|  `c5d.2xlarge` \*  |  80,000  |  37,000  | 
|  `c5d.4xlarge` \*  |  175,000  |  75,000  | 
|  `c5d.9xlarge`  |  350,000  |  170,000  | 
|  `c5d.18xlarge`  |  700,000  |  340,000  | 

\* For these instances, you can get up to the specified performance\.

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance Features<a name="compute-instances-features"></a>

The following is a summary of features for compute optimized instances:


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| C4 | Yes |  |  | Yes | 
| C5 | Yes | Yes |  | Yes | 
| C5d |  | Yes | NVMe \* | Yes | 
| C5n | Yes | Yes |  | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe](nvme-ebs-volumes.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)

## Release Notes<a name="compute-instance-limits"></a>
+ C4, C5, C5d, and C5n instances require 64\-bit EBS\-backed HVM AMIs\. They have high\-memory and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
+ C5, C5d, and C5n instances have the following requirements: 
  + Must have the NVMe drivers installed\. EBS volumes are exposed as [NVMe block devices](nvme-ebs-volumes.md)\.
  + Must have the Elastic Network Adapter \([ENA](enhanced-networking-ena.md)\) drivers installed\.

  The following AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2014\.03 or later
  + Ubuntu 14\.04 or later
  + SUSE Linux Enterprise Server 12 or later
  + Red Hat Enterprise Linux 7\.4 or later
  + CentOS 7 or later
  + FreeBSD 11\.1\-RELEASE
  + Windows Server 2008 R2 or later
+ C5, C5d, and C5n instances support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. Every instance has at least one network interface attachment\.
+ C5, C5d, and C5n instances should have acpid installed to support clean shutdown through API requests\.
+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
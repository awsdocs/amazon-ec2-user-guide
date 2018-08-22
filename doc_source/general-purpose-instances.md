# General Purpose Instances<a name="general-purpose-instances"></a>

General purpose instances provide a balance of compute, memory, and networking resources, and can be used for a variety of workloads\.

**M5 Instances**

M5 instances are the latest generation in Amazon EC2's General purpose instance family\. M5 instances give you an ideal cloud infrastructure, offering a balance of compute, memory, and networking resources for a broad range of applications that are deployed in the cloud\. M5 instances are well\-suited for the following applications:
+ Web and application servers
+ Small and medium databases
+ Gaming servers
+ Caching fleets
+ Running backend servers for SAP, Microsoft SharePoint, cluster computing, and other enterprise applications

**Note**: M5 instances require EBS\-backed AMIs with the NVMe and Elastic Network Adapter \(ENA\) drivers installed\. For more information, see the [Release Notes](#general-purpose-instances-limits)\.

**T3 Instances**

T3 instances provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload\. A T3 Unlimited instance can sustain high CPU performance for any period of time whenever required\. For more information, see [Burstable Performance Instances](burstable-performance-instances.md)\. T3 instances are well\-suited for the following applications:
+ Websites and web applications
+ Code repositories
+ Development, build, test, and staging environments
+ Microservices

**T2 Instances**

T2 instances provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload\. A T2 Unlimited instance can sustain high CPU performance for any period of time whenever required\. For more information, see [Burstable Performance Instances](burstable-performance-instances.md)\. T2 instances are well\-suited for the following applications:
+ Websites and web applications
+ Code repositories
+ Development, build, test, and staging environments
+ Microservices

**Topics**
+ [Hardware Specifications](#general-purpose-hardware)
+ [Instance Performance](#general-purpose-performance)
+ [Network Performance](#general-purpose-network-performance)
+ [Instance Features](#general-purpose-features)
+ [Release Notes](#general-purpose-instances-limits)

## Hardware Specifications<a name="general-purpose-hardware"></a>

The following is a summary of the hardware specifications for General purpose instances\.


****  

| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
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
| m5d\.large | 2 | 8 | 
| m5d\.xlarge | 4 | 16 | 
| m5d\.2xlarge | 8 | 32 | 
| m5d\.4xlarge | 16 | 64 | 
| m5d\.12xlarge | 48 | 192 | 
| m5d\.24xlarge | 96 | 384 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Instance Performance<a name="general-purpose-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. M4, M5, and M5d instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

The `m4.10xlarge` and `m4.16xlarge` instance types provide the ability to control processor C\-states and P\-states on Linux\. The `m5.12xlarge`, `m5.24xlarge`, `m5d.12xlarge`, and `m5d.24xlarge` instance types provide the ability to control processor C\-states\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="general-purpose-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance types that use the ENA and support up to 10 Gbps of throughput use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network throughput is below their baseline limits, and can use these credits when they perform network data transfers\. For workloads that require access to 10 Gbps of bandwidth or more on a sustained basis, we recommend using instance types that support 10 Gbps or 25 Gbps network speeds\.

The following is a summary of network performance for General purpose instances that support enhanced networking\.


****  

| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| t2\.nano, t2\.micro, t2\.small, t2\.medium, t2\.large, t2\.xlarge, t2\.2xlarge | Up to 1 Gbps | [ENA](enhanced-networking-ena.md) | 
| t3\.nano, t3\.micro, t3\.small, t3\.medium, t3\.large, t3\.xlarge, t3\.2xlarge | Up to 5 Gbps | [ENA](enhanced-networking-ena.md) | 
|  `m4.large`  |  Moderate  |  [Intel 82599 VF](sriov-networking.md)  | 
|  `m4.xlarge`, `m4.2xlarge`, `m4.4xlarge`  |  High  |  [Intel 82599 VF](sriov-networking.md)  | 
|  `m4.10xlarge`  |  10 Gbps  |  [Intel 82599 VF](sriov-networking.md)  | 
|  `m4.16xlarge`  |  25 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `m5.large`, `m5.xlarge`, `m5.2xlarge`, `m5.4xlarge`, `m5d.large`, `m5d.xlarge`, `m5d.2xlarge`, `m5d.4xlarge`  |  Up to 10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `m5.12xlarge`, `m5d.12xlarge`  |  10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `m5.24xlarge`, `m5d.24xlarge`  |  25 Gbps  | [ENA](enhanced-networking-ena.md) | 

## Instance Features<a name="general-purpose-features"></a>

The following is a summary of features for General purpose instances:


|  | VPC only | EBS only | Placement group | 
| --- | --- | --- | --- | 
|  T2  |  Yes  |  Yes  |   | 
|  T3  |  Yes  |  Yes  |   | 
|  M4  |  Yes  |  Yes  |  Yes  | 
|  M5  |  Yes  |  Yes  |  Yes  | 
|  M5d  |  Yes  |  |  Yes  | 

For more information, see the following:
+ [Instance Types Available Only in a VPC](using-vpc.md#vpc-only-instance-types)
+ [Amazon EBS–Optimized Instances](EBSOptimized.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)
+ [Enhanced Networking on Linux](enhanced-networking.md)

## Release Notes<a name="general-purpose-instances-limits"></a>
+ M4, M5, M5d, `t2.large` and larger, and `t3.large` and larger instance types require 64\-bit HVM AMIs\. They have high\-memory, and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
+ M5, M5d, and T3 instances have the following requirements:
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
+ M5, M5d, and T3 instances support a maximum of 27 EBS volumes plus elastic network interface attachments\. For example, `m5.2xlarge` instances support four network interfaces\. Every instance has at least one network interface\. If you have a `m5.2xlarge` instance with three additional elastic network interface attachments, you can attach 24 EBS volumes to that instance\.
+ M5 and M5d instances should have acpid installed to support clean shutdown through API requests\.
+ [ClassicLink](vpc-classiclink.md) is not supported for M5 and M5d instances—you cannot use ClassicLink to link your EC2\-Classic instances to these instances in your VPC\.
+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
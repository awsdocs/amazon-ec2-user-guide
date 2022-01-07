# General purpose instances<a name="general-purpose-instances"></a>

General purpose instances provide a balance of compute, memory, and networking resources, and can be used for a wide range of workloads\.

**M5 and M5a instances**

These instances provide an ideal cloud infrastructure, offering a balance of compute, memory, and networking resources for a broad range of applications that are deployed in the cloud\. They are well\-suited for the following:
+ Small and midsize databases
+ Data processing tasks that require additional memory
+ Caching fleets
+ Backend servers for SAP, Microsoft SharePoint, cluster computing, and other enterprise applications

For more information, see [Amazon EC2 M5 Instances](https://aws.amazon.com/ec2/instance-types/m5)\.

Bare metal instances, such as `m5.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

**M5zn**

These instances are ideal for applications that benefit from extremely high single\-thread performance, high throughput, and low latency networking\. They are well\-suited for the following:
+ Gaming
+ High performance computing
+ Simulation modeling

For more information, see [Amazon EC2 M5 Instances](https://aws.amazon.com/ec2/instance-types/m5)\.

Bare metal instances, such as `m5zn.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

**M6g and M6gd instances**

These instances are powered by AWS Graviton2 processors and deliver balanced compute, memory, and networking for a broad range a general purpose workloads\. They are well suited for the following:
+ Application servers
+ Microservices
+ Gaming servers
+ Midsize data stores
+ Caching fleets

Bare metal instances, such as `m6g.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 M6g Instances](https://aws.amazon.com/ec2/instance-types/m6)\.

**M6i instances**

These instances are well suited for general\-purpose workloads such as the following:
+ Application servers and web servers
+ Microservices
+ High performance computing
+ App development
+ Small and midsize databases
+ Caching fleets

Bare metal instances, such as `m6i.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 M6i Instances](http://aws.amazon.com/ec2/instance-types/m6i)\.

**Mac1 instances**  
These instances are powered by Apple Mac mini computers\. They provide up to 10 Gbps of network bandwidth and 8 Gbps EBS bandwidth through high\-speed Thunderbolt 3 connections\. They are well suited to develop, build, test, and sign applications for Apple devices, such as iPhone, iPad, iPod, Mac, Apple Watch, and Apple TV\.

For more information, see [Amazon EC2 Mac instances](ec2-mac-instances.md)\.

**T2, T3, T3a, and T4g instances**

These instances provide a baseline level of CPU performance with the ability to burst to a higher level when required by your workload\. An Unlimited instance can sustain high CPU performance for any period of time whenever required\. For more information, see [Burstable performance instances](burstable-performance-instances.md)\. They are well\-suited for the following:
+ Websites and web applications
+ Code repositories
+ Development, build, test, and staging environments
+ Microservices

For more information, see [Amazon EC2 T2 Instances](http://aws.amazon.com/ec2/instance-types/t2/), [Amazon EC2 T3 Instances](http://aws.amazon.com/ec2/instance-types/t3/), and [Amazon EC2 T4g Instances](http://aws.amazon.com/ec2/instance-types/t4/)\.

**Topics**
+ [Hardware specifications](#general-purpose-hardware)
+ [Instance performance](#general-purpose-performance)
+ [Network performance](#general-purpose-network-performance)
+ [SSD I/O performance](#general-purpose-ssd-perf)
+ [Instance features](#general-purpose-features)
+ [Release notes](#general-purpose-instances-release-notes)
+ [Burstable performance instances](burstable-performance-instances.md)
+ [Amazon EC2 Mac instances](ec2-mac-instances.md)

## Hardware specifications<a name="general-purpose-hardware"></a>

The following is a summary of the hardware specifications for general purpose instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
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
| m5\.8xlarge | 32 | 128 | 
| m5\.12xlarge | 48 | 192 | 
| m5\.16xlarge | 64 | 256 | 
| m5\.24xlarge | 96 | 384 | 
| m5\.metal | 96 | 384 | 
| m5a\.large | 2 | 8 | 
| m5a\.xlarge | 4 | 16 | 
| m5a\.2xlarge | 8 | 32 | 
| m5a\.4xlarge | 16 | 64 | 
| m5a\.8xlarge | 32 | 128 | 
| m5a\.12xlarge | 48 | 192 | 
| m5a\.16xlarge | 64 | 256 | 
| m5a\.24xlarge | 96 | 384 | 
| m5ad\.large | 2 | 8 | 
| m5ad\.xlarge | 4 | 16 | 
| m5ad\.2xlarge | 8 | 32 | 
| m5ad\.4xlarge | 16 | 64 | 
| m5ad\.8xlarge | 32 | 128 | 
| m5ad\.12xlarge | 48 | 192 | 
| m5ad\.16xlarge | 64 | 256 | 
| m5ad\.24xlarge | 96 | 384 | 
| m5d\.large | 2 | 8 | 
| m5d\.xlarge | 4 | 16 | 
| m5d\.2xlarge | 8 | 32 | 
| m5d\.4xlarge | 16 | 64 | 
| m5d\.8xlarge | 32 | 128 | 
| m5d\.12xlarge | 48 | 192 | 
| m5d\.16xlarge | 64 | 256 | 
| m5d\.24xlarge | 96 | 384 | 
| m5d\.metal | 96 | 384 | 
| m5dn\.large | 2 | 8 | 
| m5dn\.xlarge | 4 | 16 | 
| m5dn\.2xlarge | 8 | 32 | 
| m5dn\.4xlarge | 16 | 64 | 
| m5dn\.8xlarge | 32 | 128 | 
| m5dn\.12xlarge | 48 | 192 | 
| m5dn\.16xlarge | 64 | 256 | 
| m5dn\.24xlarge | 96 | 384 | 
| m5dn\.metal | 96 | 384 | 
| m5n\.large | 2 | 8 | 
| m5n\.xlarge | 4 | 16 | 
| m5n\.2xlarge | 8 | 32 | 
| m5n\.4xlarge | 16 | 64 | 
| m5n\.8xlarge | 32 | 128 | 
| m5n\.12xlarge | 48 | 192 | 
| m5n\.16xlarge | 64 | 256 | 
| m5n\.24xlarge | 96 | 384 | 
| m5n\.metal | 96 | 384 | 
| m5zn\.large | 2 | 8 | 
| m5zn\.xlarge | 4 | 16 | 
| m5zn\.2xlarge | 8 | 32 | 
| m5zn\.3xlarge | 12 | 48 | 
| m5zn\.6xlarge | 24 | 96 | 
| m5zn\.12xlarge | 48 | 192 | 
| m5zn\.metal | 48 | 192 | 
| m6a\.large | 2 | 8 | 
| m6a\.xlarge | 4 | 16 | 
| m6a\.2xlarge | 8 | 32 | 
| m6a\.4xlarge | 16 | 64 | 
| m6a\.8xlarge | 32 | 128 | 
| m6a\.12xlarge | 48 | 192 | 
| m6a\.16xlarge | 64 | 256 | 
| m6a\.24xlarge | 96 | 256 | 
| m6a\.32xlarge | 128 | 256 | 
| m6a\.48xlarge | 192 | 256 | 
| m6g\.medium | 1 | 4 | 
| m6g\.large | 2 | 8 | 
| m6g\.xlarge | 4 | 16 | 
| m6g\.2xlarge | 8 | 32 | 
| m6g\.4xlarge | 16 | 64 | 
| m6g\.8xlarge | 32 | 128 | 
| m6g\.12xlarge | 48 | 192 | 
| m6g\.16xlarge | 64 | 256 | 
| m6g\.metal | 64 | 256 | 
| m6gd\.medium | 1 | 4 | 
| m6gd\.large | 2 | 8 | 
| m6gd\.xlarge | 4 | 16 | 
| m6gd\.2xlarge | 8 | 32 | 
| m6gd\.4xlarge | 16 | 64 | 
| m6gd\.8xlarge | 32 | 128 | 
| m6gd\.12xlarge | 48 | 192 | 
| m6gd\.16xlarge | 64 | 256 | 
| m6gd\.metal | 64 | 256 | 
| m6i\.large | 2 | 8 | 
| m6i\.xlarge | 4 | 16 | 
| m6i\.2xlarge | 8 | 32 | 
| m6i\.4xlarge | 16 | 64 | 
| m6i\.8xlarge | 32 | 128 | 
| m6i\.12xlarge | 48 | 192 | 
| m6i\.16xlarge | 64 | 256 | 
| m6i\.24xlarge | 96 | 384 | 
| m6i\.32xlarge | 128 | 512 | 
| m6i\.metal | 128 | 512 | 
| mac1\.metal | 12 | 32 | 
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
| t4g\.nano | 2 | 0\.5 | 
| t4g\.micro | 2 | 1 | 
| t4g\.small | 2 | 2 | 
| t4g\.medium | 2 | 4 | 
| t4g\.large | 2 | 8 | 
| t4g\.xlarge | 4 | 16 | 
| t4g\.2xlarge | 8 | 32 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimize CPU options](instance-optimize-cpu.md)\.

## Instance performance<a name="general-purpose-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some general purpose instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some general purpose instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="general-purpose-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for general purpose instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| T2 | Up to 1 Gbps | Not supported | 
| T3 \| T3a \| T4g | Up to 5 Gbps † | [ENA](enhanced-networking-ena.md) | 
| m4\.large | Moderate | [Intel 82599 VF](sriov-networking.md) | 
|  m4\.xlarge \| m4\.2xlarge \| m4\.4xlarge  | High | [Intel 82599 VF](sriov-networking.md) | 
|  m5\.4xlarge and smaller \| m5a\.8xlarge and smaller \| m5ad\.8xlarge and smaller \| m5d\.4xlarge and smaller \| m6g\.4xlarge and smaller \| m6gd\.4xlarge and smaller  | Up to 10 Gbps † | [ENA](enhanced-networking-ena.md) | 
| m4\.10xlarge | 10 Gbps | [Intel 82599 VF](sriov-networking.md) | 
|  m5\.8xlarge \| m5a\.12xlarge \| m5ad\.12xlarge \| m5d\.8xlarge \| m5d\.12xlarge \| mac1\.metal  | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| m5\.12xlarge \| m5a\.16xlarge \| m5ad\.16xlarge  \| m6g\.8xlarge \| m6gd\.8xlarge  | 12 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m6a\.4xlarge and smaller \| m6i\.4xlarge and smaller  | Up to 12\.5 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  m6a\.8xlarge \| m6i\.8xlarge  | 12\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m6a\.12xlarge \| m6i\.12xlarge  | 18\.75 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m5\.16xlarge \| m5a\.24xlarge \| m5ad\.24xlarge \| m5d\.16xlarge  \| m6g\.12xlarge \| m6gd\.12xlarge  | 20 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m5dn\.4xlarge and smaller \| m5n\.4xlarge and smaller \| m5zn\.3xlarge and smaller  | Up to 25 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  m4\.16xlarge \| m5\.24xlarge \| m5\.metal \| m5d\.24xlarge \| m5d\.metal \| m5dn\.8xlarge \| m5n\.8xlarge \| m6a\.16xlarge  \| m6g\.16xlarge \| m6g\.metal \| m6gd\.16xlarge \| m6gd\.metal \| m6i\.16xlarge  | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m6a\.24xlarge \| m6i\.24xlarge  | 37\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m5dn\.12xlarge \| m5n\.12xlarge \| m5zn\.6xlarge \| m6a\.32xlarge \| m6a\.48xlarge \| m6i\.32xlarge \| m6i\.metal  | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m5dn\.16xlarge \| m5n\.16xlarge  | 75 Gbps | [ENA](enhanced-networking-ena.md) | 
|  m5dn\.24xlarge \| m5dn\.metal \| m5n\.24xlarge \| m5n\.metal \| m5zn\.12xlarge \| m5zn\.metal  | 100 Gbps | [ENA](enhanced-networking-ena.md), [EFA](efa.md) | 

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.<a name="baseline-bandwidth"></a>


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | 
| --- | --- | --- | 
| m5\.large | \.75 | 10 | 
| m5\.xlarge | 1\.25 | 10 | 
| m5\.2xlarge | 2\.5 | 10 | 
| m5\.4xlarge | 5 | 10 | 
| m5a\.large | \.75 | 10 | 
| m5a\.xlarge | 1\.25 | 10 | 
| m5a\.2xlarge | 2\.5 | 10 | 
| m5a\.4xlarge | 5 | 10 | 
| m5ad\.large | \.75 | 10 | 
| m5ad\.xlarge | 1\.25 | 10 | 
| m5ad\.2xlarge | 2\.5 | 10 | 
| m5ad\.4xlarge | 5 | 10 | 
| m5d\.large | \.75 | 10 | 
| m5d\.xlarge | 1\.25 | 10 | 
| m5d\.2xlarge | 2\.5 | 10 | 
| m5d\.4xlarge | 5 | 10 | 
| m5dn\.large | 2\.1 | 25 | 
| m5dn\.xlarge | 4\.1 | 25 | 
| m5dn\.2xlarge | 8\.125 | 25 | 
| m5dn\.4xlarge | 16\.25 | 25 | 
| m5n\.large | 2\.1 | 25 | 
| m5n\.xlarge | 4\.1 | 25 | 
| m5n\.2xlarge | 8\.125 | 25 | 
| m5n\.4xlarge | 16\.25 | 25 | 
| m5zn\.large | 3 | 25 | 
| m5zn\.xlarge | 5 | 25 | 
| m5zn\.2xlarge | 10 | 25 | 
| m5zn\.3xlarge | 15 | 25 | 
| m6a\.large | \.781 | 12\.5 | 
| m6a\.xlarge | 1\.562 | 12\.5 | 
| m6a\.2xlarge | 3\.125 | 12\.5 | 
| m6a\.4xlarge | 6\.25 | 12\.5 | 
| m6g\.medium | \.5 | 10 | 
| m6g\.large | \.75 | 10 | 
| m6g\.xlarge | 1\.25 | 10 | 
| m6g\.2xlarge | 2\.5 | 10 | 
| m6g\.4xlarge | 5 | 10 | 
| m6gd\.medium | \.5 | 10 | 
| m6gd\.large | \.75 | 10 | 
| m6gd\.xlarge | 1\.25 | 10 | 
| m6gd\.2xlarge | 2\.5 | 10 | 
| m6gd\.4xlarge | 5 | 10 | 
| m6i\.large | \.781 | 12\.5 | 
| m6i\.xlarge | 1\.562 | 12\.5 | 
| m6i\.2xlarge | 3\.125 | 12\.5 | 
| m6i\.4xlarge | 6\.25 | 12\.5 | 
| t3\.nano | \.032 | 5 | 
| t3\.micro | \.064 | 5 | 
| t3\.small | \.128 | 5 | 
| t3\.medium | \.256 | 5 | 
| t3\.large | \.512 | 5 | 
| t3\.xlarge | 1\.024 | 5 | 
| t3\.2xlarge | 2\.048 | 5 | 
| t3a\.nano | \.032 | 5 | 
| t3a\.micro | \.064 | 5 | 
| t3a\.small | \.128 | 5 | 
| t3a\.medium | \.256 | 5 | 
| t3a\.large | \.512 | 5 | 
| t3a\.xlarge | 1\.024 | 5 | 
| t3a\.2xlarge | 2\.048 | 5 | 
| t4g\.nano | \.032 | 5 | 
| t4g\.micro | \.064 | 5 | 
| t4g\.small | \.128 | 5 | 
| t4g\.medium | \.256 | 5 | 
| t4g\.large | \.512 | 5 | 
| t4g\.xlarge | 1\.024 | 5 | 
| t4g\.2xlarge | 2\.048 | 5 | 

## SSD I/O performance<a name="general-purpose-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| m5ad\.large | 30,000 | 15,000 | 
| m5ad\.xlarge | 59,000 | 29,000 | 
| m5ad\.2xlarge | 117,000 | 57,000 | 
| m5ad\.4xlarge | 234,000 | 114,000 | 
| m5ad\.8xlarge | 466,666 | 233,333 | 
| m5ad\.12xlarge | 700,000 | 340,000 | 
| m5ad\.16xlarge  | 933,333 | 466,666 | 
| m5ad\.24xlarge | 1,400,000 | 680,000 | 
| m5d\.large | 30,000 | 15,000 | 
| m5d\.xlarge | 59,000 | 29,000 | 
| m5d\.2xlarge | 117,000 | 57,000 | 
| m5d\.4xlarge | 234,000 | 114,000 | 
| m5d\.8xlarge | 466,666 | 233,333 | 
| m5d\.12xlarge  | 700,000 | 340,000 | 
| m5d\.16xlarge | 933,333 | 466,666 | 
| m5d\.24xlarge | 1,400,000 | 680,000 | 
| m5d\.metal | 1,400,000 | 680,000 | 
| m5dn\.large | 30,000 | 15,000 | 
| m5dn\.xlarge | 59,000 | 29,000 | 
| m5dn\.2xlarge  | 117,000 | 57,000 | 
| m5dn\.4xlarge | 234,000 | 114,000 | 
| m5dn\.8xlarge | 466,666 | 233,333 | 
| m5dn\.12xlarge | 700,000 | 340,000 | 
| m5dn\.16xlarge | 933,333 | 466,666 | 
| m5dn\.24xlarge  | 1,400,000 | 680,000 | 
| m5dn\.metal  | 1,400,000 | 680,000 | 
| m6gd\.medium | 13,438 | 5,625 | 
| m6gd\.large | 26,875 | 11,250 | 
| m6gd\.xlarge | 53,750 | 22,500 | 
| m6gd\.2xlarge | 107,500 | 45,000 | 
| m6gd\.4xlarge | 215,000 | 90,000 | 
| m6gd\.8xlarge | 430,000 | 180,000 | 
| m6gd\.12xlarge | 645,000 | 270,000 | 
| m6gd\.16xlarge | 860,000 | 360,000 | 
| m6gd\.metal | 860,000 | 360,000 | 

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance features<a name="general-purpose-features"></a>

The following is a summary of features for general purpose instances:


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| M4 | Yes | No | No | Yes | 
| M5 | Yes | Yes | No | Yes | 
| M5a | Yes | Yes | No | Yes | 
| M5ad | No | Yes | NVMe \* | Yes | 
| M5d | No | Yes | NVMe \* | Yes | 
| M5dn | No | Yes | NVMe \* | Yes | 
| M5n | Yes | Yes | No | Yes | 
| M5zn | Yes | Yes | No | Yes | 
| M6a | Yes | Yes | No | Yes | 
| M6g | Yes | Yes | No | Yes | 
| M6gd | No | Yes | NVMe \* | Yes | 
| M6i | Yes | Yes | No | Yes | 
| Mac1 | Yes | Yes | No | No | 
| T2 | Yes | No | No | No | 
| T3 | Yes | Yes | No | No | 
| T3a | Yes | Yes | No | No | 
| T4g | Yes | Yes | No | No | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Linux instances](nvme-ebs-volumes.md)
+ [Amazon EC2 instance store](InstanceStorage.md)
+ [Placement groups](placement-groups.md)

## Release notes<a name="general-purpose-instances-release-notes"></a>
+ M5, M5d, and T3 instances feature a 3\.1 GHz Intel Xeon Platinum 8000 series processor from either the first generation \(Skylake\-SP\) or second generation \(Cascade Lake\)\.
+ M5a, M5ad, and T3a instances feature a 2\.5 GHz AMD EPYC 7000 series processor\.
+ M5zn instances are powered by Intel Cascade Lake CPUs that deliver all\-core turbo frequency of up to 4\.5 GHz and up to 100 Gbps network bandwidth\.
+ M6g and M6gd instances feature an AWS Graviton2 processor based on 64\-bit Arm architecture\.
+ M6i instances feature third generation Intel Xeon Scalable processors \(Ice Lake\) and support the Intel Advanced Vector Extensions 512 \(Intel AVX\-512\) instruction set\.
+ Mac1 instances feature a 3\.2 GHz Intel eighth\-generation \(Coffee Lake\) Core i7 processor\.
+ T4g instances feature an AWS Graviton2 processor based on 64\-bit Arm architecture\.
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances), M4, `t2.large` and larger, `t3.large` and larger, and `t3a.large` and larger instance types require 64\-bit HVM AMIs\. They have high\-memory, and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) have the following requirements:
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
+ Instances with an [AWS Graviton Processor](https://aws.amazon.com/ec2/graviton) have the following requirements:
  + Use an AMI for the 64\-bit Arm architecture\.
  + Support booting through UEFI with ACPI tables and support ACPI hot\-plug of PCI devices\.

  The following Linux AMIs meet these requirements:
  + Amazon Linux 2 \(64\-bit Arm\)
  + Ubuntu 16\.04 or later \(64\-bit Arm\)
  + Red Hat Enterprise Linux 8\.0 or later \(64\-bit Arm\)
  + SUSE Linux Enterprise Server 15 or later \(64\-bit Arm\)
  + Debian 10 or later \(64\-bit Arm\)
+ To get the best performance from your M6i instances, ensure that they have ENA driver version 2\.2\.9 or later\. Using an ENA driver earlier than version 1\.2 with these instances causes network interface attachment failures\. The following AMIs have a compatible ENA driver\.
  + Amazon Linux 2 with kernel 4\.14\.186
  + Ubuntu 20\.04 with kernel 5\.4\.0\-1025\-aws
  + Red Hat Enterprise Linux 8\.3 with kernel 4\.18\.0\-240\.1\.1\.el8\_3\.ARCH
  + SUSE Linux Enterprise Server 15 SP2 with kernel 5\.3\.18\-24\.15\.1
+ Amazon EC2 Mac instances support macOS Mojave \(version 10\.14\), macOS Catalina \(version 10\.15\), and macOS Big Sur \(version 11\)\.
+ Instances built on the Nitro System support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. For more information, see [Nitro System volume limits](volume_limits.md#instance-type-volume-limits)\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ Instances built on the Nitro System should have system\-logind or acpid installed to support clean shutdown through API requests\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.
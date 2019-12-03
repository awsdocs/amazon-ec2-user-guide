# Instance Types<a name="instance-types"></a>

When you launch an instance, the *instance type* that you specify determines the hardware of the host computer used for your instance\. Each instance type offers different compute, memory, and storage capabilities and are grouped in instance families based on these capabilities\. Select an instance type based on the requirements of the application or software that you plan to run on your instance\.

Amazon EC2 provides each instance with a consistent and predictable amount of CPU capacity, regardless of its underlying hardware\.

Amazon EC2 dedicates some resources of the host computer, such as CPU, memory, and instance storage, to a particular instance\. Amazon EC2 shares other resources of the host computer, such as the network and the disk subsystem, among instances\. If each instance on a host computer tries to use as much of one of these shared resources as possible, each receives an equal share of that resource\. However, when a resource is underused, an instance can consume a higher share of that resource while it's available\.

Each instance type provides higher or lower minimum performance from a shared resource\. For example, instance types with high I/O performance have a larger allocation of shared resources\. Allocating a larger share of shared resources also reduces the variance of I/O performance\. For most applications, moderate I/O performance is more than enough\. However, for applications that require greater or more consistent I/O performance, consider an instance type with higher I/O performance\.

**Topics**
+ [Available Instance Types](#AvailableInstanceTypes)
+ [Hardware Specifications](#instance-hardware-specs)
+ [AMI Virtualization Types](#instance-virtualization-type)
+ [Nitro\-based Instances](#ec2-nitro-instances)
+ [Networking and Storage Features](#instance-networking-storage)
+ [Instance Limits](#instance-type-limits)
+ [General Purpose Instances](general-purpose-instances.md)
+ [Compute Optimized Instances](compute-optimized-instances.md)
+ [Memory Optimized Instances](memory-optimized-instances.md)
+ [Storage Optimized Instances](storage-optimized-instances.md)
+ [Linux Accelerated Computing Instances](accelerated-computing-instances.md)
+ [Finding an Instance Type](instance-discovery.md)
+ [Changing the Instance Type](ec2-instance-resize.md)
+ [Getting Recommendations for an Instance Type](ec2-instance-recommendations.md)

## Available Instance Types<a name="AvailableInstanceTypes"></a>

Amazon EC2 provides the instance types listed in the following tables\.

### Current Generation Instances<a name="current-gen-instances"></a>

For the best performance, we recommend that you use the current generation instance types when you launch new instances\.

For more information about the current generation instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.


| Instance Family | Current Generation Instance Types | 
| --- | --- | 
| General purpose | `a1.medium` \| `a1.large` \| `a1.xlarge` \| `a1.2xlarge` \| `a1.4xlarge` \| `a1.metal` \| `m4.large` \| `m4.xlarge` \| `m4.2xlarge` \| `m4.4xlarge` \| `m4.10xlarge` \| `m4.16xlarge` \| `m5.large` \| `m5.xlarge` \| `m5.2xlarge` \| `m5.4xlarge` \| `m5.8xlarge` \| `m5.12xlarge` \| `m5.16xlarge` \| `m5.24xlarge` \| `m5.metal` \| `m5a.large` \| `m5a.xlarge` \| `m5a.2xlarge` \| `m5a.4xlarge` \| `m5a.8xlarge` \| `m5a.12xlarge` \| `m5a.16xlarge` \| `m5a.24xlarge` \| `m5ad.large` \| `m5ad.xlarge` \| `m5ad.2xlarge` \| `m5ad.4xlarge` \| `m5ad.12xlarge` \| `m5ad.24xlarge` \| `m5d.large` \| `m5d.xlarge` \| `m5d.2xlarge` \| `m5d.4xlarge` \| `m5d.8xlarge` \| `m5d.12xlarge` \| `m5d.16xlarge` \| `m5d.24xlarge` \| `m5d.metal` \| `m5dn.large` \| `m5dn.xlarge` \| `m5dn.2xlarge` \| `m5dn.4xlarge` \| `m5dn.8xlarge` \| `m5dn.12xlarge` \| `m5dn.16xlarge` \| `m5dn.24xlarge` \| `m5n.large` \| `m5n.xlarge` \| `m5n.2xlarge` \| `m5n.4xlarge` \| `m5n.8xlarge` \| `m5n.12xlarge` \| `m5n.16xlarge` \| `m5n.24xlarge` \| `t2.nano` \| `t2.micro` \| `t2.small` \| `t2.medium` \| `t2.large` \| `t2.xlarge` \| `t2.2xlarge` \| `t3.nano` \| `t3.micro` \| `t3.small` \| `t3.medium` \| `t3.large` \| `t3.xlarge` \| `t3.2xlarge` \| `t3a.nano` \| `t3a.micro` \| `t3a.small` \| `t3a.medium` \| `t3a.large` \| `t3a.xlarge` \| `t3a.2xlarge` | 
| Compute optimized | `c4.large` \| `c4.xlarge` \| `c4.2xlarge` \| `c4.4xlarge` \| `c4.8xlarge` \| `c5.large` \| `c5.xlarge` \| `c5.2xlarge` \| `c5.4xlarge` \| `c5.9xlarge` \| `c5.12xlarge` \| `c5.18xlarge` \| `c5.24xlarge` \| `c5.metal` \| `c5d.large` \| `c5d.xlarge` \| `c5d.2xlarge` \| `c5d.4xlarge` \| `c5d.9xlarge` \| `c5d.12xlarge` \| `c5d.18xlarge` \| `c5d.24xlarge` \| `c5d.metal` \| `c5n.large` \| `c5n.xlarge` \| `c5n.2xlarge` \| `c5n.4xlarge` \| `c5n.9xlarge` \| `c5n.18xlarge` \| `c5n.metal`  | 
| Memory optimized | `r4.large` \| `r4.xlarge` \| `r4.2xlarge` \| `r4.4xlarge` \| `r4.8xlarge` \| `r4.16xlarge` \| `r5.large` \| `r5.xlarge` \| `r5.2xlarge` \| `r5.4xlarge` \| `r5.8xlarge` \| `r5.12xlarge` \| `r5.16xlarge` \| `r5.24xlarge` \| `r5.metal` \| `r5a.large` \| `r5a.xlarge` \| `r5a.2xlarge` \| `r5a.4xlarge` \| `r5a.8xlarge` \| `r5a.12xlarge` \| `r5a.16xlarge` \| `r5a.24xlarge` \| `r5ad.large` \| `r5ad.xlarge` \| `r5ad.2xlarge` \| `r5ad.4xlarge` \| `r5ad.12xlarge` \| `r5ad.24xlarge` \| `r5d.large` \| `r5d.xlarge` \| `r5d.2xlarge` \| `r5d.4xlarge` \| `r5d.8xlarge` \| `r5d.12xlarge` \| `r5d.16xlarge` \| `r5d.24xlarge` \| `r5d.metal` \| `r5dn.large` \| `r5dn.xlarge` \| `r5dn.2xlarge` \| `r5dn.4xlarge` \| `r5dn.8xlarge` \| `r5dn.12xlarge` \| `r5dn.16xlarge` \| `r5dn.24xlarge` \| `r5n.large` \| `r5n.xlarge` \| `r5n.2xlarge` \| `r5n.4xlarge` \| `r5n.8xlarge` \| `r5n.12xlarge` \| `r5n.16xlarge` \| `r5n.24xlarge` \| `u-6tb1.metal` \| `u-9tb1.metal` \| `u-12tb1.metal` \| `u-18tb1.metal` \| `u-24tb1.metal` \| `x1.16xlarge` \| `x1.32xlarge` \| `x1e.xlarge` \| `x1e.2xlarge` \| `x1e.4xlarge` \| `x1e.8xlarge` \| `x1e.16xlarge` \| `x1e.32xlarge` \| `z1d.large` \| `z1d.xlarge` \| `z1d.2xlarge` \| `z1d.3xlarge` \| `z1d.6xlarge` \| `z1d.12xlarge` \| `z1d.metal` | 
| Storage optimized | `d2.xlarge` \| `d2.2xlarge` \| `d2.4xlarge` \| `d2.8xlarge` \| `h1.2xlarge` \| `h1.4xlarge` \| `h1.8xlarge` \| `h1.16xlarge` \| `i3.large` \| `i3.xlarge` \| `i3.2xlarge` \| `i3.4xlarge` \| `i3.8xlarge` \| `i3.16xlarge` \| `i3.metal` \| `i3en.large` \| `i3en.xlarge` \| `i3en.2xlarge` \| `i3en.3xlarge` \| `i3en.6xlarge` \| `i3en.12xlarge` \| `i3en.24xlarge` \| `i3en.metal` | 
| Accelerated computing | `f1.2xlarge` \| `f1.4xlarge` \| `f1.16xlarge` \| `g3s.xlarge` \| `g3.4xlarge` \| `g3.8xlarge` \| `g3.16xlarge` \| `g4dn.xlarge` \| `g4dn.2xlarge` \| `g4dn.4xlarge` \| `g4dn.8xlarge` \| `g4dn.12xlarge` \| `g4dn.16xlarge` \| `p2.xlarge` \| `p2.8xlarge` \| `p2.16xlarge` \| `p3.2xlarge` \| `p3.8xlarge` \| `p3.16xlarge` \| `p3dn.24xlarge` \| `inf1.xlarge` \| `inf1.2xlarge` \| `inf1.6xlarge` \| `inf1.24xlarge` | 

### Previous Generation Instances<a name="previous-gen-instances"></a>

Amazon Web Services offers previous generation instances for users who have optimized their applications around these instances and have yet to upgrade\. We encourage you to use the latest generation of instances to get the best performance, but we continue to support these previous generation instances\. If you are currently using a previous generation instance, you can see which current generation instance would be a suitable upgrade\. For more information, see [Previous Generation Instances](https://aws.amazon.com/ec2/previous-generation/)\.


| Instance Family | Previous Generation Instance Types | 
| --- | --- | 
| General purpose | `m1.small` \| `m1.medium` \| `m1.large` \| `m1.xlarge` \| `m3.medium` \| `m3.large` \| `m3.xlarge` \| `m3.2xlarge` \| `t1.micro` | 
| Compute optimized | `c1.medium` \| `c1.xlarge` \| `cc2.8xlarge` \| `c3.large` \| `c3.xlarge` \| `c3.2xlarge` \| `c3.4xlarge` \| `c3.8xlarge` | 
| Memory optimized | `m2.xlarge` \| `m2.2xlarge` \| `m2.4xlarge` \| `cr1.8xlarge` \| `r3.large` \| `r3.xlarge` \| `r3.2xlarge` \| `r3.4xlarge` \| `r3.8xlarge` | 
| Storage optimized | `hs1.8xlarge` \| `i2.xlarge` \| `i2.2xlarge` \| `i2.4xlarge` \| `i2.8xlarge` | 
| Accelerated computing | `g2.2xlarge` \| `g2.8xlarge` | 

## Hardware Specifications<a name="instance-hardware-specs"></a>

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

To determine which instance type best meets your needs, we recommend that you launch an instance and use your own benchmark application\. Because you pay by the instance second, it's convenient and inexpensive to test multiple instance types before making a decision\.

If your needs change, even after you make a decision, you can resize your instance later\. For more information, see [Changing the Instance Type](ec2-instance-resize.md)\.

**Note**  
Amazon EC2 instances typically run on 64\-bit virtual Intel processors as specified in the instance type product pages\. For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. However, confusion may result from industry naming conventions for 64\-bit CPUs\. Chip manufacturer Advanced Micro Devices \(AMD\) introduced the first commercially successful 64\-bit architecture based on the Intel x86 instruction set\. Consequently, the architecture is widely referred to as AMD64 regardless of the chip manufacturer\. Windows and several Linux distributions follow this practice\. This explains why the internal system information on an Ubuntu or Windows EC2 instance displays the CPU architecture as AMD64 even though the instances are running on Intel hardware\.

## AMI Virtualization Types<a name="instance-virtualization-type"></a>

<a name="virtualization"></a>The virtualization type of your instance is determined by the AMI that you use to launch it\. Current generation instance types support hardware virtual machine \(HVM\) only\. Some previous generation instance types support paravirtual \(PV\) and some AWS regions support PV instances\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

For best performance, we recommend that you use an HVM AMI\. In addition, HVM AMIs are required to take advantage of enhanced networking\. HVM virtualization uses hardware\-assist technology provided by the AWS platform\. With HVM virtualization, the guest VM runs as if it were on a native hardware platform, except that it still uses PV network and storage drivers for improved performance\.

## Nitro\-based Instances<a name="ec2-nitro-instances"></a>

The Nitro system is a collection of AWS\-built hardware and software components that enable high performance, high availability, and high security\. In addition, the Nitro system provides bare metal capabilities that eliminate virtualization overhead and support workloads that require full access to host hardware\.

**Nitro Components**

The following components are part of the Nitro system:
+ Nitro hypervisor \- A lightweight hypervisor that manages memory and CPU allocation and delivers performance that is indistinguishable from bare metal for most workloads\.
+ Nitro card
  + Local NVMe storage volumes
  + Networking hardware support
  + Management
  + Monitoring
  + Security
+ Nitro security chip, integrated into the motherboard

**Instance Types**

The following instances are based on the Nitro system:
+ A1, C5, C5d, C5n, G4, I3en, Inf1, M5, M5a, M5ad, M5d, M5dn, M5n, `p3dn.24xlarge`, R5, R5a, R5ad, R5d, R5dn, R5n, T3, T3a, and z1d
+ Bare metal: `a1.metal`, `c5.metal`, `c5d.metal`, `c5n.metal`, `i3.metal`, `i3en.metal`, `m5.metal`, `m5d.metal`, `r5.metal`, `r5d.metal`, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, `u-24tb1.metal`, and `z1d.metal`

**Resources**

For more information, see the following videos:
+ [AWS re:Invent 2017: The Amazon EC2 Nitro System Architecture](https://www.youtube.com/watch?v=02EbskIXCOc)
+ [AWS re:Invent 2017: Amazon EC2 Bare Metal Instances](https://www.youtube.com/watch?v=o9_4uGvbvnk)
+ [The Nitro Project: Next\-Generation EC2 Infrastructure](https://www.slideshare.net/AmazonWebServices/the-nitro-project-nextgeneration-ec2-infrastructure-aws-online-tech-talks)

## Networking and Storage Features<a name="instance-networking-storage"></a>

When you select an instance type, this determines the networking and storage features that are available\.

**Networking features**
+ IPv6 is supported on all current generation instance types and the C3, R3, and I2 previous generation instance types\.
+ To maximize the networking and bandwidth performance of your instance type, you can do the following:
  + Launch supported instance types into a cluster placement group to optimize your instances for high performance computing \(HPC\) applications\. Instances in a common cluster placement group can benefit from high\-bandwidth, low\-latency networking\. For more information, see [Placement Groups](placement-groups.md)\.
  + Enable enhanced networking for supported current generation instance types to get significantly higher packet per second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\. 
+ Current generation instance types that are enabled for enhanced networking have the following networking performance attributes:
  + Traffic within the same region over private IPv4 or IPv6 can support 5 Gbps for single\-flow traffic and up to 25 Gbps for multi\-flow traffic \(depending on the instance type\)\.
  + Traffic to and from Amazon S3 buckets within the same region over the public IP address space or through a VPC endpoint can use all available instance aggregate bandwidth\.
+ The maximum supported MTU varies across instance types\. All Amazon EC2 instance types support standard Ethernet V2 1500 MTU frames\. All current generation instances support 9001 MTU, or jumbo frames, and some previous generation instances support them as well\. For more information, see [Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance](network_mtu.md)\.

**Storage features**
+ Some instance types support EBS volumes and instance store volumes, while other instance types support only EBS volumes\. Some instance types that support instance store volumes use solid state drives \(SSD\) to deliver very high random I/O performance\. Some instance types support NVMe instance store volumes\. Some instance types support NVMe EBS volumes\. For more information, see [Amazon EBS and NVMe on Linux Instances](nvme-ebs-volumes.md) and [NVMe SSD Volumes](ssd-instance-store.md#nvme-ssd-volumes)\.
+ To obtain additional, dedicated capacity for Amazon EBS I/O, you can launch some instance types as EBS–optimized instances\. Some instance types are EBS–optimized by default\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

### Summary of Networking and Storage Features<a name="instance-type-summary-table"></a>

The following table summarizes the networking and storage features supported by current generation instance types\.


|  | EBS only | NVMe EBS | Instance store | Placement group | Enhanced networking | 
| --- | --- | --- | --- | --- | --- | 
| A1 | Yes | Yes | No | Yes | ENA | 
|  C4  |  Yes  | No | No |  Yes  | Intel 82599 VF | 
|  C5  |  Yes  | Yes | No |  Yes  | ENA | 
|  C5d  | No | Yes | NVMe \* |  Yes  | ENA | 
|  C5n  |  Yes  | Yes | No |  Yes  | ENA | 
|  D2  | No | No |  HDD  |  Yes  | Intel 82599 VF | 
|  F1  | No | No |  NVMe \*  |  Yes  | ENA | 
|  G3  | Yes | No | No |  Yes  | ENA | 
| G4 | No | Yes | NVMe \* | Yes | ENA | 
|  HS1  | No | No |  HDD \*  |  Yes  | ENA | 
|  I3  | No | No |  NVMe \*  |  Yes  | ENA | 
|  I3en  | No | Yes |  NVMe \*  |  Yes  | ENA | 
|  Inf1  | Yes | Yes |  No  |  Yes  | ENA | 
|  M4  |  Yes  | No | No |  Yes  |  `m4.16xlarge`: ENA All other sizes: Intel 82599 VF  | 
|  M5  |  Yes  | Yes | No |  Yes  | ENA | 
|  M5a  |  Yes  | Yes | No |  Yes  | ENA | 
| M5ad | No | Yes | NVMe \* | Yes | ENA | 
|  M5d  | No | Yes | NVMe \* |  Yes  | ENA | 
|  M5dn  | No | Yes | NVMe \* |  Yes  | ENA | 
|  M5n  |  Yes  | Yes | No |  Yes  | ENA | 
|  P2  |  Yes  | No | No |  Yes  | ENA | 
| P3 |  Yes  |  No  |  No  |  Yes  | ENA | 
| P3dn |  No  |  Yes  |  NVMe \*  |  Yes  | ENA | 
|  R4  |  Yes  | No | No |  Yes  | ENA | 
|  R5  |  Yes  | Yes | No |  Yes  | ENA | 
|  R5a  |  Yes  | Yes | No |  Yes  | ENA | 
|  R5ad  | No | Yes | NVMe \* |  Yes  | ENA | 
|  R5d  | No | Yes | NVMe \* |  Yes  | ENA | 
|  R5dn  | No | Yes | NVMe \* |  Yes  | ENA | 
|  R5n  |  Yes  | Yes | No |  Yes  | ENA | 
| T2 | Yes | No | No | No | No | 
| T3 | Yes | Yes | No | No | ENA | 
| T3a | Yes | Yes | No | No | ENA | 
| u\-xtb1\.metal | Yes | Yes | No | No | ENA | 
|  X1  | No | No |  SSD  |  Yes  | ENA | 
| X1e | No | No | SSD \* | Yes | ENA | 
| z1d | No | Yes | NVMe \* | Yes | ENA | 

\* The root device volume must be an Amazon EBS volume\.

The following table summarizes the networking and storage features supported by previous generation instance types\.


|  | Instance store | Placement group | Enhanced networking | 
| --- | --- | --- | --- | 
|  C3  |  SSD  |  Yes  |  Intel 82599 VF  | 
|  G2  |  SSD  |  Yes  | No | 
|  I2  |  SSD  |  Yes  |  Intel 82599 VF  | 
|  M3  |  SSD  | No | No | 
|  R3  |  SSD  |  Yes  |  Intel 82599 VF  | 

## Instance Limits<a name="instance-type-limits"></a>

There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\.

For more information about the default limits, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)

For more information about viewing your current limits or requesting an increase in your current limits, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.
# Instance Types<a name="instance-types"></a>

When you launch an instance, the *instance type* that you specify determines the hardware of the host computer used for your instance\. Each instance type offers different compute, memory, and storage capabilities and are grouped in instance families based on these capabilities\. Select an instance type based on the requirements of the application or software that you plan to run on your instance\.

Amazon EC2 provides each instance with a consistent and predictable amount of CPU capacity, regardless of its underlying hardware\.

Amazon EC2 dedicates some resources of the host computer, such as CPU, memory, and instance storage, to a particular instance\. Amazon EC2 shares other resources of the host computer, such as the network and the disk subsystem, among instances\. If each instance on a host computer tries to use as much of one of these shared resources as possible, each receives an equal share of that resource\. However, when a resource is underused, an instance can consume a higher share of that resource while it's available\.

Each instance type provides higher or lower minimum performance from a shared resource\. For example, instance types with high I/O performance have a larger allocation of shared resources\. Allocating a larger share of shared resources also reduces the variance of I/O performance\. For most applications, moderate I/O performance is more than enough\. However, for applications that require greater or more consistent I/O performance, consider an instance type with higher I/O performance\.

**Contents**

+ [Available Instance Types](#AvailableInstanceTypes)

+ [Hardware Specifications](#instance-hardware-specs)

+ [Virtualization Types](#instance-virtualization-type)

+ [Networking and Storage Features](#instance-networking-storage)

+ [Instance Limits](#instance-type-limits)

## Available Instance Types<a name="AvailableInstanceTypes"></a>

Amazon EC2 provides the instance types listed in the following tables\.

### Current Generation Instances<a name="current-gen-instances"></a>

For the best performance, we recommend that you use the current generation instance types when you launch new instances\. 

For more information about the current generation instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.


| Instance Family | Current Generation Instance Types | 
| --- | --- | 
| General purpose | `t2.nano` | `t2.micro` | `t2.small` | `t2.medium` | `t2.large` | `t2.xlarge` | `t2.2xlarge` | `m4.large` | `m4.xlarge` | `m4.2xlarge` | `m4.4xlarge` | `m4.10xlarge` | `m4.16xlarge` | `m5.large` | `m5.xlarge` | `m5.2xlarge` | `m5.4xlarge` | `m5.12xlarge` | `m5.24xlarge`  | 
| Compute optimized | `c4.large` | `c4.xlarge` | `c4.2xlarge` | `c4.4xlarge` | `c4.8xlarge` | `c5.large` | `c5.xlarge` | `c5.2xlarge` | `c5.4xlarge` | `c5.9xlarge` | `c5.18xlarge` | 
| Memory optimized | `r4.large` | `r4.xlarge` | `r4.2xlarge` | `r4.4xlarge` | `r4.8xlarge` | `r4.16xlarge` | `x1.16xlarge` | `x1.32xlarge` | `x1e.xlarge` | `x1e.2xlarge` | `x1e.4xlarge` | `x1e.8xlarge` | `x1e.16xlarge` | `x1e.32xlarge` | 
| Storage optimized | `d2.xlarge` | `d2.2xlarge` | `d2.4xlarge` | `d2.8xlarge` | `h1.2xlarge` | `h1.4xlarge` | `h1.8xlarge` | `h1.16xlarge` | `i3.large` | `i3.xlarge` | `i3.2xlarge` | `i3.4xlarge` | `i3.8xlarge` | `i3.16xlarge` | 
| Accelerated computing | `f1.2xlarge` | `f1.16xlarge` | `g3.4xlarge` | `g3.8xlarge` | `g3.16xlarge` | `p2.xlarge` | `p2.8xlarge` | `p2.16xlarge` | `p3.2xlarge` | `p3.8xlarge` | `p3.16xlarge` | 

### Previous Generation Instances<a name="previous-gen-instances"></a>

Amazon Web Services offers previous generation instances for users who have optimized their applications around these instances and have yet to upgrade\. We encourage you to use the latest generation of instances to get the best performance, but we continue to support these previous generation instances\. If you are currently using a previous generation instance, you can see which current generation instance would be a suitable upgrade\. For more information, see [Previous Generation Instances](https://aws.amazon.com/ec2/previous-generation/)\.


| Instance Family | Previous Generation Instance Types | 
| --- | --- | 
| General purpose | `m1.small` | `m1.medium` | `m1.large` | `m1.xlarge` | `m3.medium` | `m3.large` | `m3.xlarge` | `m3.2xlarge` | 
| Compute optimized | `c1.medium` | `c1.xlarge` | `cc2.8xlarge` | `c3.large` | `c3.xlarge` | `c3.2xlarge` | `c3.4xlarge` | `c3.8xlarge` | 
| Memory optimized | `m2.xlarge` | `m2.2xlarge` | `m2.4xlarge` | `cr1.8xlarge` | `r3.large` | `r3.xlarge` | `r3.2xlarge` | `r3.4xlarge` | `r3.8xlarge` | 
| Storage optimized | `hs1.8xlarge` | `i2.xlarge` | `i2.2xlarge` | `i2.4xlarge` | `i2.8xlarge` | 
|  GPU optimized  | `g2.2xlarge` | `g2.8xlarge` | 
| Micro | `t1.micro` | 

## Hardware Specifications<a name="instance-hardware-specs"></a>

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

To determine which instance type best meets your needs, we recommend that you launch an instance and use your own benchmark application\. Because you pay by the instance second, it's convenient and inexpensive to test multiple instance types before making a decision\.

If your needs change, even after you make a decision, you can resize your instance later\. For more information, see [Resizing Your Instance](ec2-instance-resize.md)\.

**Note**  
Amazon EC2 instances run on 64\-bit virtual Intel processors as specified in the instance type product pages\. For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. However, confusion may result from industry naming conventions for 64\-bit CPUs\. Chip manufacturer Advanced Micro Devices \(AMD\) introduced the first commercially successful 64\-bit architecture based on the Intel x86 instruction set\. Consequently, the architecture is widely referred to as AMD64 regardless of the chip manufacturer\. Windows and several Linux distributions follow this practice\. This explains why the internal system information on an Ubuntu or Windows EC2 instance displays the CPU architecture as AMD64 even though the instances are running on Intel hardware\.

## Virtualization Types<a name="instance-virtualization-type"></a>

<a name="virtualization"></a>The virtualization type of your instance is determined by the AMI that you use to launch it\. Current generation instance types support hardware virtual machine \(HVM\) only\. Some previous generation instance types support paravirtual \(PV\)\. 

For best performance, we recommend that you use an HVM AMI\. In addition, HVM AMIs are required to take advantage of enhanced networking\. HVM virtualization uses hardware\-assist technology provided by the AWS platform\. With HVM virtualization, the guest VM runs as if it were on a native hardware platform, except that it still uses PV network and storage drivers for improved performance\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

## Networking and Storage Features<a name="instance-networking-storage"></a>

When you select an instance type, this determines the networking and storage features that are available\.

**Networking features**

+ Some instance types are not available in EC2\-Classic, so you must launch them in a VPC\. By launching an instance in a VPC, you can leverage features that are not available in EC2\-Classic, such as enhanced networking, assigning multiple private IPv4 addresses to an instance, assigning IPv6 addresses to an instance, and changing the security groups assigned to an instance\. For more information, see [Instance Types Available Only in a VPC](using-vpc.md#vpc-only-instance-types)\.

+ IPv6 is supported on all current generation instance types and the C3, R3, and I2 previous generation instance types\.

+ To maximize the networking and bandwidth performance of your instance type, you can do the following:

  + Launch supported instance types into a cluster placement group to optimize your instances for high performance computing \(HPC\) applications\. Instances in a common cluster placement group can benefit from high\-bandwidth, low\-latency networking\. For more information, see [Placement Groups](placement-groups.md)\.

  + Enable enhanced networking for supported current generation instance types to get significantly higher packet per second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\. 

+ Current generation instance types that are enabled for enhanced networking have the following networking performance attributes:

  + Traffic within the same AWS Region over private IPv4 or IPv6 can support 5 Gbps for single\-flow traffic and up to 25 Gbps for multi\-flow traffic \(depending on the instance type\)\.

  + Traffic to and from Amazon S3 buckets within the same AWS Region over the public IP address space or through a VPC endpoint can use all available instance aggregate bandwidth\.

+ The maximum supported MTU varies across instance types\. All Amazon EC2 instance types support standard Ethernet V2 1500 MTU frames\. All current generation instances support 9001 MTU, or jumbo frames, and some previous generation instances support them as well\. For more information, see [Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance](network_mtu.md)\.

**Storage features**

+ Some instance types support EBS volumes and instance store volumes, while other instance types support only EBS volumes\. Some instances that support instance store volumes use solid state drives \(SSD\) to deliver very high random I/O performance\. For more information, see [Storage](Storage.md)\.

+ To obtain additional, dedicated capacity for Amazon EBS I/O, you can launch some instance types as EBS–optimized instances\. Some instance types are EBS–optimized by default\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

The following table summarizes the networking and storage features supported by the current generation instance types\.


|  | VPC only | EBS only | Instance store | Placement group | Enhanced networking | 
| --- | --- | --- | --- | --- | --- | 
|  C4  |  Yes  |  Yes  |  |  Yes  |  Intel 82599 VF  | 
|  C5  |  Yes  |  Yes  |  |  Yes  |  ENA  | 
|  F1  |  Yes  |  |  NVMe \*  |  Yes  |  ENA  | 
|  P2  |  Yes  |  Yes  |  |  Yes  |  ENA  | 
|  P3  |  Yes  |  Yes  |  |  Yes  |  ENA  | 
|  G3  | Yes | Yes |  |  Yes  | ENA | 
|  H1  | Yes |  |  HDD  |  Yes  |  ENA  | 
|  I3  |  Yes  |  |  NVMe \*  |  Yes  |  ENA  | 
|  D2  |  |  |  HDD  |  Yes  |  Intel 82599 VF  | 
|  M4  |  Yes  |  Yes  |  |  Yes  |  `m4.16xlarge`: ENA All other sizes: Intel 82599 VF  | 
|  M5  |  Yes  |  Yes  |  |  Yes  |  ENA  | 
|  T2  |  Yes  |  Yes  |  |  |  | 
|  R4  |  Yes  |  Yes  |  |  Yes  |  ENA  | 
|  X1  |  Yes  |  |  SSD  |  Yes  |  ENA  | 
|  X1e  |  Yes  |  |  SSD  |  Yes  |   | 

\* The root device volume must be an Amazon EBS volume\.

## Instance Limits<a name="instance-type-limits"></a>

There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\.

For more information about the default limits, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)

For more information about viewing your current limits or requesting an increase in your current limits, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.
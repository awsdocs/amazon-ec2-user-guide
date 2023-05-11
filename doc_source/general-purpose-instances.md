# General purpose instances<a name="general-purpose-instances"></a>

General purpose instances provide a balance of compute, memory, and networking resources, and can be used for a wide range of workloads\.

**M5 and M5a instances**

These instances provide an ideal cloud infrastructure, offering a balance of compute, memory, and networking resources for a broad range of applications that are deployed in the cloud\. They are well\-suited for the following:
+ Small and midsize databases
+ Data processing tasks that require additional memory
+ Caching fleets
+ Backend servers for SAP, Microsoft SharePoint, cluster computing, and other enterprise applications

For more information, see [Amazon EC2 M5 Instances](https://aws.amazon.com/ec2/instance-types/m5)\.

Bare metal instances, such as `m5.metal`, `m5n.metal`, and `m5zn.metal `provide your applications with direct access to physical resources of the host server, such as processors and memory\.

**M5zn**

These instances are ideal for applications that benefit from extremely high single\-thread performance, high throughput, and low latency networking\. They are well\-suited for the following:
+ Gaming
+ High performance computing
+ Simulation modeling

For more information, see [Amazon EC2 M5 Instances](https://aws.amazon.com/ec2/instance-types/m5)\.

**M6g and M6gd instances**

These instances are powered by AWS Graviton2 processors and deliver balanced compute, memory, and networking for a broad range a general purpose workloads\. They are well suited for the following:
+ Application servers
+ Microservices
+ Gaming servers
+ Midsize data stores
+ Caching fleets

Bare metal instances such as `m6g.metal` provide your applications with direct access to physical resources of the host server, such as processors and memory\. For more information, see [Amazon EC2 M6g Instances](https://aws.amazon.com/ec2/instance-types/m6)\.

**M6i and M6id instances**

These instances are well suited for general\-purpose workloads such as the following:
+ Application servers and web servers
+ Microservices
+ High performance computing
+ App development
+ Small and midsize databases
+ Caching fleets

Bare metal instances such as `m6i.metal` provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 M6i Instances](http://aws.amazon.com/ec2/instance-types/m6i)\.

**M6in and M6idn instances**

These instances are well suited for network\-intensive workloads such as the following:
+ High\-performance file systems
+ Distributed web scale in\-memory caches
+ Caching fleets
+ Real\-time big data analytics
+ Telco applications such as 5G User Plane Function \(UPF\)

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
+ [Amazon EBS I/O performance](#general-purpose-ebs-perf)
+ [SSD\-based instance store volume I/O performance](#general-purpose-ssd-perf)
+ [Release notes](#general-purpose-instances-release-notes)
+ [Burstable performance instances](burstable-performance-instances.md)

## Hardware specifications<a name="general-purpose-hardware"></a>

The following is a summary of the hardware specifications for general purpose instances\. A virtual central processing unit \(vCPU\) represents a portion of the physical CPU assigned to a virtual machine \(VM\)\. For x86 instances, there are two vCPUs per core\. For Graviton instances, there is one vCPU per core\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| a1\.medium | 1 | 2\.00 | 
| a1\.large | 2 | 4\.00 | 
| a1\.xlarge | 4 | 8\.00 | 
| a1\.2xlarge | 8 | 16\.00 | 
| a1\.4xlarge | 16 | 32\.00 | 
| a1\.metal | 16 | 32\.00 | 
| m1\.small | 1 | 1\.70 | 
| m1\.medium | 1 | 3\.70 | 
| m1\.large | 2 | 7\.50 | 
| m1\.xlarge | 4 | 15\.00 | 
| m2\.xlarge | 2 | 17\.10 | 
| m2\.2xlarge | 4 | 34\.20 | 
| m2\.4xlarge | 8 | 68\.40 | 
| m3\.medium | 1 | 3\.75 | 
| m3\.large | 2 | 7\.50 | 
| m3\.xlarge | 4 | 15\.00 | 
| m3\.2xlarge | 8 | 30\.00 | 
| m4\.large | 2 | 8\.00 | 
| m4\.xlarge | 4 | 16\.00 | 
| m4\.2xlarge | 8 | 32\.00 | 
| m4\.4xlarge | 16 | 64\.00 | 
| m4\.10xlarge | 40 | 160\.00 | 
| m4\.16xlarge | 64 | 256\.00 | 
| m5\.large | 2 | 8\.00 | 
| m5\.xlarge | 4 | 16\.00 | 
| m5\.2xlarge | 8 | 32\.00 | 
| m5\.4xlarge | 16 | 64\.00 | 
| m5\.8xlarge | 32 | 128\.00 | 
| m5\.12xlarge | 48 | 192\.00 | 
| m5\.16xlarge | 64 | 256\.00 | 
| m5\.24xlarge | 96 | 384\.00 | 
| m5\.metal | 96 | 384\.00 | 
| m5a\.large | 2 | 8\.00 | 
| m5a\.xlarge | 4 | 16\.00 | 
| m5a\.2xlarge | 8 | 32\.00 | 
| m5a\.4xlarge | 16 | 64\.00 | 
| m5a\.8xlarge | 32 | 128\.00 | 
| m5a\.12xlarge | 48 | 192\.00 | 
| m5a\.16xlarge | 64 | 256\.00 | 
| m5a\.24xlarge | 96 | 384\.00 | 
| m5ad\.large | 2 | 8\.00 | 
| m5ad\.xlarge | 4 | 16\.00 | 
| m5ad\.2xlarge | 8 | 32\.00 | 
| m5ad\.4xlarge | 16 | 64\.00 | 
| m5ad\.8xlarge | 32 | 128\.00 | 
| m5ad\.12xlarge | 48 | 192\.00 | 
| m5ad\.16xlarge | 64 | 256\.00 | 
| m5ad\.24xlarge | 96 | 384\.00 | 
| m5d\.large | 2 | 8\.00 | 
| m5d\.xlarge | 4 | 16\.00 | 
| m5d\.2xlarge | 8 | 32\.00 | 
| m5d\.4xlarge | 16 | 64\.00 | 
| m5d\.8xlarge | 32 | 128\.00 | 
| m5d\.12xlarge | 48 | 192\.00 | 
| m5d\.16xlarge | 64 | 256\.00 | 
| m5d\.24xlarge | 96 | 384\.00 | 
| m5d\.metal | 96 | 384\.00 | 
| m5dn\.large | 2 | 8\.00 | 
| m5dn\.xlarge | 4 | 16\.00 | 
| m5dn\.2xlarge | 8 | 32\.00 | 
| m5dn\.4xlarge | 16 | 64\.00 | 
| m5dn\.8xlarge | 32 | 128\.00 | 
| m5dn\.12xlarge | 48 | 192\.00 | 
| m5dn\.16xlarge | 64 | 256\.00 | 
| m5dn\.24xlarge | 96 | 384\.00 | 
| m5dn\.metal | 96 | 384\.00 | 
| m5n\.large | 2 | 8\.00 | 
| m5n\.xlarge | 4 | 16\.00 | 
| m5n\.2xlarge | 8 | 32\.00 | 
| m5n\.4xlarge | 16 | 64\.00 | 
| m5n\.8xlarge | 32 | 128\.00 | 
| m5n\.12xlarge | 48 | 192\.00 | 
| m5n\.16xlarge | 64 | 256\.00 | 
| m5n\.24xlarge | 96 | 384\.00 | 
| m5n\.metal | 96 | 384\.00 | 
| m5zn\.large | 2 | 8\.00 | 
| m5zn\.xlarge | 4 | 16\.00 | 
| m5zn\.2xlarge | 8 | 32\.00 | 
| m5zn\.3xlarge | 12 | 48\.00 | 
| m5zn\.6xlarge | 24 | 96\.00 | 
| m5zn\.12xlarge | 48 | 192\.00 | 
| m5zn\.metal | 48 | 192\.00 | 
| m6a\.large | 2 | 8\.00 | 
| m6a\.xlarge | 4 | 16\.00 | 
| m6a\.2xlarge | 8 | 32\.00 | 
| m6a\.4xlarge | 16 | 64\.00 | 
| m6a\.8xlarge | 32 | 128\.00 | 
| m6a\.12xlarge | 48 | 192\.00 | 
| m6a\.16xlarge | 64 | 256\.00 | 
| m6a\.24xlarge | 96 | 384\.00 | 
| m6a\.32xlarge | 128 | 512\.00 | 
| m6a\.48xlarge | 192 | 768\.00 | 
| m6a\.metal | 192 | 768\.00 | 
| m6g\.medium | 1 | 4\.00 | 
| m6g\.large | 2 | 8\.00 | 
| m6g\.xlarge | 4 | 16\.00 | 
| m6g\.2xlarge | 8 | 32\.00 | 
| m6g\.4xlarge | 16 | 64\.00 | 
| m6g\.8xlarge | 32 | 128\.00 | 
| m6g\.12xlarge | 48 | 192\.00 | 
| m6g\.16xlarge | 64 | 256\.00 | 
| m6g\.metal | 64 | 256\.00 | 
| m6gd\.medium | 1 | 4\.00 | 
| m6gd\.large | 2 | 8\.00 | 
| m6gd\.xlarge | 4 | 16\.00 | 
| m6gd\.2xlarge | 8 | 32\.00 | 
| m6gd\.4xlarge | 16 | 64\.00 | 
| m6gd\.8xlarge | 32 | 128\.00 | 
| m6gd\.12xlarge | 48 | 192\.00 | 
| m6gd\.16xlarge | 64 | 256\.00 | 
| m6gd\.metal | 64 | 256\.00 | 
| m6i\.large | 2 | 8\.00 | 
| m6i\.xlarge | 4 | 16\.00 | 
| m6i\.2xlarge | 8 | 32\.00 | 
| m6i\.4xlarge | 16 | 64\.00 | 
| m6i\.8xlarge | 32 | 128\.00 | 
| m6i\.12xlarge | 48 | 192\.00 | 
| m6i\.16xlarge | 64 | 256\.00 | 
| m6i\.24xlarge | 96 | 384\.00 | 
| m6i\.32xlarge | 128 | 512\.00 | 
| m6i\.metal | 128 | 512\.00 | 
| m6id\.large | 2 | 8\.00 | 
| m6id\.xlarge | 4 | 16\.00 | 
| m6id\.2xlarge | 8 | 32\.00 | 
| m6id\.4xlarge | 16 | 64\.00 | 
| m6id\.8xlarge | 32 | 128\.00 | 
| m6id\.12xlarge | 48 | 192\.00 | 
| m6id\.16xlarge | 64 | 256\.00 | 
| m6id\.24xlarge | 96 | 384\.00 | 
| m6id\.32xlarge | 128 | 512\.00 | 
| m6id\.metal | 128 | 512\.00 | 
| m6idn\.large | 2 | 8\.00 | 
| m6idn\.xlarge | 4 | 16\.00 | 
| m6idn\.2xlarge | 8 | 32\.00 | 
| m6idn\.4xlarge | 16 | 64\.00 | 
| m6idn\.8xlarge | 32 | 128\.00 | 
| m6idn\.12xlarge | 48 | 192\.00 | 
| m6idn\.16xlarge | 64 | 256\.00 | 
| m6idn\.24xlarge | 96 | 384\.00 | 
| m6idn\.32xlarge | 128 | 512\.00 | 
| m6idn\.metal | 128 | 512\.00 | 
| m6in\.large | 2 | 8\.00 | 
| m6in\.xlarge | 4 | 16\.00 | 
| m6in\.2xlarge | 8 | 32\.00 | 
| m6in\.4xlarge | 16 | 64\.00 | 
| m6in\.8xlarge | 32 | 128\.00 | 
| m6in\.12xlarge | 48 | 192\.00 | 
| m6in\.16xlarge | 64 | 256\.00 | 
| m6in\.24xlarge | 96 | 384\.00 | 
| m6in\.32xlarge | 128 | 512\.00 | 
| m6in\.metal | 128 | 512\.00 | 
| m7g\.medium | 1 | 4\.00 | 
| m7g\.large | 2 | 8\.00 | 
| m7g\.xlarge | 4 | 16\.00 | 
| m7g\.2xlarge | 8 | 32\.00 | 
| m7g\.4xlarge | 16 | 64\.00 | 
| m7g\.8xlarge | 32 | 128\.00 | 
| m7g\.12xlarge | 48 | 192\.00 | 
| m7g\.16xlarge | 64 | 256\.00 | 
| m7g\.metal | 64 | 256\.00 | 
| mac1\.metal | 12 | 32\.00 | 
| mac2\.metal | 8 | 16\.00 | 
| t1\.micro | 1 | 0\.61 | 
| t2\.nano | 1 | 0\.50 | 
| t2\.micro | 1 | 1\.00 | 
| t2\.small | 1 | 2\.00 | 
| t2\.medium | 2 | 4\.00 | 
| t2\.large | 2 | 8\.00 | 
| t2\.xlarge | 4 | 16\.00 | 
| t2\.2xlarge | 8 | 32\.00 | 
| t3\.nano | 2 | 0\.50 | 
| t3\.micro | 2 | 1\.00 | 
| t3\.small | 2 | 2\.00 | 
| t3\.medium | 2 | 4\.00 | 
| t3\.large | 2 | 8\.00 | 
| t3\.xlarge | 4 | 16\.00 | 
| t3\.2xlarge | 8 | 32\.00 | 
| t3a\.nano | 2 | 0\.50 | 
| t3a\.micro | 2 | 1\.00 | 
| t3a\.small | 2 | 2\.00 | 
| t3a\.medium | 2 | 4\.00 | 
| t3a\.large | 2 | 8\.00 | 
| t3a\.xlarge | 4 | 16\.00 | 
| t3a\.2xlarge | 8 | 32\.00 | 
| t4g\.nano | 2 | 0\.50 | 
| t4g\.micro | 2 | 1\.00 | 
| t4g\.small | 2 | 2\.00 | 
| t4g\.medium | 2 | 4\.00 | 
| t4g\.large | 2 | 8\.00 | 
| t4g\.xlarge | 4 | 16\.00 | 
| t4g\.2xlarge | 8 | 32\.00 | 

The general purpose instances use the following processors\.

**AWS Graviton processors**
+ **AWS Graviton2**: M6g, M6gd, T4g

**AMD processors**
+ **AMD EPYC 7000 series processors \(AMD EPYC 7571\)**: M5a, M5ad, T3a
+ **3rd generation AMD EPYC processors \(AMD EPYC 7R13\)**: M6a

**Intel processors**
+ **Intel Xeon Scalable processors \(Haswell E5\-2676 v3 or Broadwell E5\-2686 v4\)**: M4, T2
+ **Intel Xeon Scalable processors \(Skylake 8175M or Cascade Lake 8259CL\)**: M5, M5d, T3
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8259CL\)**: M5n
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8252C\)**: M5zn
+ **3rd generation Intel Xeon Scalable processors \(Ice Lake 8375C\)**: M6i, M6id

For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance performance<a name="general-purpose-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some general purpose instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some general purpose instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="general-purpose-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for general purpose instances that support enhanced networking\.

**Note**  
Instance types indicated with a **†** have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.


| Instance type | Network performance | Enhanced networking features | 
| --- | --- | --- | 
| a1\.medium † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| a1\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| a1\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| a1\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| a1\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| a1\.metal † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m1\.small † | Low | Not supported | 
| m1\.medium † | Moderate | Not supported | 
| m1\.large † | Moderate | Not supported | 
| m1\.xlarge † | High | Not supported | 
| m2\.xlarge † | Moderate | Not supported | 
| m2\.2xlarge † | Moderate | Not supported | 
| m2\.4xlarge † | High | Not supported | 
| m3\.medium † | Moderate | Not supported | 
| m3\.large † | Moderate | Not supported | 
| m3\.xlarge † | High | Not supported | 
| m3\.2xlarge † | High | Not supported | 
| m4\.large † | Moderate | Not supported | 
| m4\.xlarge † | High | Not supported | 
| m4\.2xlarge † | High | Not supported | 
| m4\.4xlarge † | High | Not supported | 
| m4\.10xlarge † | 10 Gigabit | Not supported | 
| m4\.16xlarge † | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.8xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.12xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.16xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.24xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.8xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.12xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.16xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5a\.24xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.8xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.12xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.16xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5ad\.24xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.8xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.12xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.16xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.24xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5d\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.4xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.8xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.12xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.16xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5dn\.24xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m5dn\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m5n\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.4xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.8xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.12xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.16xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5n\.24xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m5n\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m5zn\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5zn\.xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5zn\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5zn\.3xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5zn\.6xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m5zn\.12xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m5zn\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6a\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.2xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.4xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.8xlarge | 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.12xlarge | 18\.75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.24xlarge | 37\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.32xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6a\.48xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6a\.metal | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6g\.medium † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.8xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.12xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6g\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.medium † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.8xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.12xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6gd\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.2xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.4xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.8xlarge | 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.12xlarge | 18\.75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.24xlarge | 37\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6i\.32xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6i\.metal | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6id\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.2xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.4xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.8xlarge | 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.12xlarge | 18\.75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.24xlarge | 37\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6id\.32xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6id\.metal | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6idn\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.xlarge † | Up to 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.2xlarge † | Up to 40 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.4xlarge † | Up to 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.8xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.12xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.16xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.24xlarge | 150 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6idn\.32xlarge | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6idn\.metal | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6in\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.xlarge † | Up to 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.2xlarge † | Up to 40 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.4xlarge † | Up to 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.8xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.12xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.16xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.24xlarge | 150 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m6in\.32xlarge | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m6in\.metal | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m7g\.medium † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.2xlarge † | Up to 15 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.4xlarge † | Up to 15 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.8xlarge | 15 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.12xlarge | 22\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| m7g\.16xlarge | 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| m7g\.metal | 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| mac1\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| mac2\.metal | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t1\.micro † | Very Low | Not supported | 
| t2\.nano † | Low to Moderate | Not supported | 
| t2\.micro † | Low to Moderate | Not supported | 
| t2\.small † | Low to Moderate | Not supported | 
| t2\.medium † | Low to Moderate | Not supported | 
| t2\.large † | Low to Moderate | Not supported | 
| t2\.xlarge † | Moderate | Not supported | 
| t2\.2xlarge † | Moderate | Not supported | 
| t3\.nano † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3\.micro † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3\.small † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3\.medium † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3\.large † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3\.xlarge † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3\.2xlarge † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.nano † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.micro † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.small † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.medium † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.large † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.xlarge † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t3a\.2xlarge † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.nano † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.micro † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.small † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.medium † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.large † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.xlarge † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| t4g\.2xlarge † | Up to 5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 

For `32xlarge` and `metal` instance types that support 200 Gbps, at least 2 ENIs, each attached to a different network card, are required on the instance to achieve 200 Gbps throughput\. Each ENI attached to a network card can achieve a max of 170 Gbps\.

The following table shows the baseline and burst bandwidth for instance types that use the network I/O credit mechanism to burst beyond their baseline bandwidth\.


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | 
| --- | --- | --- | 
| a1\.medium | 0\.5 | 10\.0 | 
| a1\.large | 0\.75 | 10\.0 | 
| a1\.xlarge | 1\.25 | 10\.0 | 
| a1\.2xlarge | 2\.5 | 10\.0 | 
| a1\.4xlarge | 5\.0 | 10\.0 | 
| a1\.metal | 5\.0 | 10\.0 | 
| m1\.small | 0\.3 | 1\.2 | 
| m1\.medium | 0\.3 | 1\.2 | 
| m1\.large | 0\.7 | 2\.8 | 
| m1\.xlarge | 1\.0 | 10\.0 | 
| m2\.xlarge | 0\.3 | 1\.2 | 
| m2\.2xlarge | 0\.7 | 2\.8 | 
| m2\.4xlarge | 1\.0 | 10\.0 | 
| m3\.medium | 0\.3 | 1\.2 | 
| m3\.large | 0\.7 | 2\.8 | 
| m3\.xlarge | 1\.0 | 10\.0 | 
| m3\.2xlarge | 1\.0 | 10\.0 | 
| m4\.large | 0\.45 | 1\.2 | 
| m4\.xlarge | 0\.75 | 2\.8 | 
| m4\.2xlarge | 1\.0 | 10\.0 | 
| m4\.4xlarge | 2\.0 | 10\.0 | 
| m4\.10xlarge | 5\.0 | 10\.0 | 
| m4\.16xlarge | 5\.0 | 10\.0 | 
| m5\.large | 0\.75 | 10\.0 | 
| m5\.xlarge | 1\.25 | 10\.0 | 
| m5\.2xlarge | 2\.5 | 10\.0 | 
| m5\.4xlarge | 5\.0 | 10\.0 | 
| m5a\.large | 0\.75 | 10\.0 | 
| m5a\.xlarge | 1\.25 | 10\.0 | 
| m5a\.2xlarge | 2\.5 | 10\.0 | 
| m5a\.4xlarge | 5\.0 | 10\.0 | 
| m5a\.8xlarge | 7\.5 | 10\.0 | 
| m5ad\.large | 0\.75 | 10\.0 | 
| m5ad\.xlarge | 1\.25 | 10\.0 | 
| m5ad\.2xlarge | 2\.5 | 10\.0 | 
| m5ad\.4xlarge | 5\.0 | 10\.0 | 
| m5ad\.8xlarge | 7\.5 | 10\.0 | 
| m5d\.large | 0\.75 | 10\.0 | 
| m5d\.xlarge | 1\.25 | 10\.0 | 
| m5d\.2xlarge | 2\.5 | 10\.0 | 
| m5d\.4xlarge | 5\.0 | 10\.0 | 
| m5dn\.large | 2\.1 | 25\.0 | 
| m5dn\.xlarge | 4\.1 | 25\.0 | 
| m5dn\.2xlarge | 8\.125 | 25\.0 | 
| m5dn\.4xlarge | 16\.25 | 25\.0 | 
| m5n\.large | 2\.1 | 25\.0 | 
| m5n\.xlarge | 4\.1 | 25\.0 | 
| m5n\.2xlarge | 8\.125 | 25\.0 | 
| m5n\.4xlarge | 16\.25 | 25\.0 | 
| m5zn\.large | 3\.0 | 25\.0 | 
| m5zn\.xlarge | 5\.0 | 25\.0 | 
| m5zn\.2xlarge | 10\.0 | 25\.0 | 
| m5zn\.3xlarge | 15\.0 | 25\.0 | 
| m6a\.large | 0\.781 | 12\.5 | 
| m6a\.xlarge | 1\.562 | 12\.5 | 
| m6a\.2xlarge | 3\.125 | 12\.5 | 
| m6a\.4xlarge | 6\.25 | 12\.5 | 
| m6g\.medium | 0\.5 | 10\.0 | 
| m6g\.large | 0\.75 | 10\.0 | 
| m6g\.xlarge | 1\.25 | 10\.0 | 
| m6g\.2xlarge | 2\.5 | 10\.0 | 
| m6g\.4xlarge | 5\.0 | 10\.0 | 
| m6gd\.medium | 0\.5 | 10\.0 | 
| m6gd\.large | 0\.75 | 10\.0 | 
| m6gd\.xlarge | 1\.25 | 10\.0 | 
| m6gd\.2xlarge | 2\.5 | 10\.0 | 
| m6gd\.4xlarge | 5\.0 | 10\.0 | 
| m6i\.large | 0\.781 | 12\.5 | 
| m6i\.xlarge | 1\.562 | 12\.5 | 
| m6i\.2xlarge | 3\.125 | 12\.5 | 
| m6i\.4xlarge | 6\.25 | 12\.5 | 
| m6id\.large | 0\.781 | 12\.5 | 
| m6id\.xlarge | 1\.562 | 12\.5 | 
| m6id\.2xlarge | 3\.125 | 12\.5 | 
| m6id\.4xlarge | 6\.25 | 12\.5 | 
| m6idn\.large | 3\.125 | 25\.0 | 
| m6idn\.xlarge | 6\.25 | 30\.0 | 
| m6idn\.2xlarge | 12\.5 | 40\.0 | 
| m6idn\.4xlarge | 25\.0 | 50\.0 | 
| m6in\.large | 3\.125 | 25\.0 | 
| m6in\.xlarge | 6\.25 | 30\.0 | 
| m6in\.2xlarge | 12\.5 | 40\.0 | 
| m6in\.4xlarge | 25\.0 | 50\.0 | 
| m7g\.medium | 0\.52 | 12\.5 | 
| m7g\.large | 0\.937 | 12\.5 | 
| m7g\.xlarge | 1\.876 | 12\.5 | 
| m7g\.2xlarge | 3\.75 | 15\.0 | 
| m7g\.4xlarge | 7\.5 | 15\.0 | 
| t1\.micro | 0\.07 | 0\.28 | 
| t2\.nano | 0\.032 | 0\.512 | 
| t2\.micro | 0\.064 | 1\.024 | 
| t2\.small | 0\.128 | 1\.024 | 
| t2\.medium | 0\.256 | 1\.024 | 
| t2\.large | 0\.512 | 1\.024 | 
| t2\.xlarge | 0\.75 | 1\.024 | 
| t2\.2xlarge | 1\.0 | 1\.024 | 
| t3\.nano | 0\.032 | 5\.0 | 
| t3\.micro | 0\.064 | 5\.0 | 
| t3\.small | 0\.128 | 5\.0 | 
| t3\.medium | 0\.256 | 5\.0 | 
| t3\.large | 0\.512 | 5\.0 | 
| t3\.xlarge | 1\.024 | 5\.0 | 
| t3\.2xlarge | 2\.048 | 5\.0 | 
| t3a\.nano | 0\.032 | 5\.0 | 
| t3a\.micro | 0\.064 | 5\.0 | 
| t3a\.small | 0\.128 | 5\.0 | 
| t3a\.medium | 0\.256 | 5\.0 | 
| t3a\.large | 0\.512 | 5\.0 | 
| t3a\.xlarge | 1\.024 | 5\.0 | 
| t3a\.2xlarge | 2\.048 | 5\.0 | 
| t4g\.nano | 0\.032 | 5\.0 | 
| t4g\.micro | 0\.064 | 5\.0 | 
| t4g\.small | 0\.128 | 5\.0 | 
| t4g\.medium | 0\.256 | 5\.0 | 
| t4g\.large | 0\.512 | 5\.0 | 
| t4g\.xlarge | 1\.024 | 5\.0 | 
| t4g\.2xlarge | 2\.048 | 5\.0 | 

## Amazon EBS I/O performance<a name="general-purpose-ebs-perf"></a>

Amazon EBS optimized instances use an optimized configuration stack and provide additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your Amazon EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## SSD\-based instance store volume I/O performance<a name="general-purpose-ssd-perf"></a>

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
| m6id\.large | 33,542 | 16,771 | 
| m6id\.xlarge | 67,083 | 33,542 | 
| m6id\.2xlarge | 134,167 | 67,084 | 
| m6id\.4xlarge | 268,333 | 134,167 | 
| m6id\.8xlarge | 536,666 | 268,334 | 
| m6id\.12xlarge | 804,999 | 402,501 | 
| m6id\.16xlarge | 1,073,332 | 536,668 | 
| m6id\.24xlarge | 1,609,998 | 805,002 | 
| m6id\.32xlarge | 2,146,664 | 1,073,336 | 
| m6id\.metal | 2,146,664 | 1,073,336 | 
| m6idn\.large | 33,542 | 16,771 | 
| m6idn\.xlarge | 67,083 | 33,542 | 
| m6idn\.2xlarge | 134,167 | 67,084 | 
| m6idn\.4xlarge | 268,333 | 134,167 | 
| m6idn\.8xlarge | 536,666 | 268,334 | 
| m6idn\.12xlarge | 804,999 | 402,501 | 
| m6idn\.16xlarge | 1,073,332 | 536,668 | 
| m6idn\.24xlarge | 1,609,998 | 805,002 | 
| m6idn\.32xlarge | 2,146,664 | 1,073,336 | 
| m6idn\.metal | 2,146,664 | 1,073,336 | 

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Release notes<a name="general-purpose-instances-release-notes"></a>
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances), M4, `t2.large` and larger, `t3.large` and larger, and `t3a.large` and larger instance types require 64\-bit HVM AMIs\. They have high\-memory, and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
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
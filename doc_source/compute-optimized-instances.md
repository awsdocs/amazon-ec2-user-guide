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

For more information, see [Amazon EC2 C5 Instances](http://aws.amazon.com/ec2/instance-types/c5)\.

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

For more information, see [Amazon EC2 C6g Instances](http://aws.amazon.com/ec2/instance-types/c6g/)\.

**C6i and C6id instances**

These instances are ideal for running advanced, compute\-intensive workloads, such as the following:
+ High\-performance computing \(HPC\)
+ Batch processing
+ Ad serving
+ Video encoding
+ Distributed analytics
+ Highly scalable multiplayer gaming

**C6in instances**

These instances are well suited for compute\-intensive workloads such as the following:
+ Distributed computing applications
+ Network virtual appliances
+ Data analytics
+ High Performance Computing \(HPC\)
+ CPU\-based AI/ML

For more information, see [Amazon EC2 C6i Instances](http://aws.amazon.com/ec2/instance-types/c6i/)\.

**Hpc6a instances**

These instances are ideal for running high performance computing \(HPC\) workloads, such as the following:
+ Molecular dynamics
+ Computational chemistry
+ Computational fluid dynamics
+ Weather forecasting
+ Materials simulation
+ Crash simulations
+ Astrophysics

For more information, see [Amazon EC2 Hpc6a Instances](http://aws.amazon.com/ec2/instance-types/hpc6)\.

**Topics**
+ [Hardware specifications](#compute-instances-hardware)
+ [Instance performance](#compute-performance)
+ [Network performance](#compute-network-performance)
+ [Amazon EBS I/O performance](#compute-ebs-perf)
+ [SSD\-based instance store volume I/O performance](#compute-ssd-perf)
+ [Release notes](#compute-instance-release-notes)

## Hardware specifications<a name="compute-instances-hardware"></a>

The following is a summary of the hardware specifications for compute optimized instances\. A virtual central processing unit \(vCPU\) represents a portion of the physical CPU assigned to a virtual machine \(VM\)\. For x86 instances, there are two vCPUs per core\. For Graviton instances, there is one vCPU per core\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| c1\.medium | 2 | 1\.70 | 
| c1\.xlarge | 8 | 7\.00 | 
| c3\.large | 2 | 3\.75 | 
| c3\.xlarge | 4 | 7\.50 | 
| c3\.2xlarge | 8 | 15\.00 | 
| c3\.4xlarge | 16 | 30\.00 | 
| c3\.8xlarge | 32 | 60\.00 | 
| c4\.large | 2 | 3\.75 | 
| c4\.xlarge | 4 | 7\.50 | 
| c4\.2xlarge | 8 | 15\.00 | 
| c4\.4xlarge | 16 | 30\.00 | 
| c4\.8xlarge | 36 | 60\.00 | 
| c5\.large | 2 | 4\.00 | 
| c5\.xlarge | 4 | 8\.00 | 
| c5\.2xlarge | 8 | 16\.00 | 
| c5\.4xlarge | 16 | 32\.00 | 
| c5\.9xlarge | 36 | 72\.00 | 
| c5\.12xlarge | 48 | 96\.00 | 
| c5\.18xlarge | 72 | 144\.00 | 
| c5\.24xlarge | 96 | 192\.00 | 
| c5\.metal | 96 | 192\.00 | 
| c5a\.large | 2 | 4\.00 | 
| c5a\.xlarge | 4 | 8\.00 | 
| c5a\.2xlarge | 8 | 16\.00 | 
| c5a\.4xlarge | 16 | 32\.00 | 
| c5a\.8xlarge | 32 | 64\.00 | 
| c5a\.12xlarge | 48 | 96\.00 | 
| c5a\.16xlarge | 64 | 128\.00 | 
| c5a\.24xlarge | 96 | 192\.00 | 
| c5ad\.large | 2 | 4\.00 | 
| c5ad\.xlarge | 4 | 8\.00 | 
| c5ad\.2xlarge | 8 | 16\.00 | 
| c5ad\.4xlarge | 16 | 32\.00 | 
| c5ad\.8xlarge | 32 | 64\.00 | 
| c5ad\.12xlarge | 48 | 96\.00 | 
| c5ad\.16xlarge | 64 | 128\.00 | 
| c5ad\.24xlarge | 96 | 192\.00 | 
| c5d\.large | 2 | 4\.00 | 
| c5d\.xlarge | 4 | 8\.00 | 
| c5d\.2xlarge | 8 | 16\.00 | 
| c5d\.4xlarge | 16 | 32\.00 | 
| c5d\.9xlarge | 36 | 72\.00 | 
| c5d\.12xlarge | 48 | 96\.00 | 
| c5d\.18xlarge | 72 | 144\.00 | 
| c5d\.24xlarge | 96 | 192\.00 | 
| c5d\.metal | 96 | 192\.00 | 
| c5n\.large | 2 | 5\.25 | 
| c5n\.xlarge | 4 | 10\.50 | 
| c5n\.2xlarge | 8 | 21\.00 | 
| c5n\.4xlarge | 16 | 42\.00 | 
| c5n\.9xlarge | 36 | 96\.00 | 
| c5n\.18xlarge | 72 | 192\.00 | 
| c5n\.metal | 72 | 192\.00 | 
| c6a\.large | 2 | 4\.00 | 
| c6a\.xlarge | 4 | 8\.00 | 
| c6a\.2xlarge | 8 | 16\.00 | 
| c6a\.4xlarge | 16 | 32\.00 | 
| c6a\.8xlarge | 32 | 64\.00 | 
| c6a\.12xlarge | 48 | 96\.00 | 
| c6a\.16xlarge | 64 | 128\.00 | 
| c6a\.24xlarge | 96 | 192\.00 | 
| c6a\.32xlarge | 128 | 256\.00 | 
| c6a\.48xlarge | 192 | 384\.00 | 
| c6a\.metal | 192 | 384\.00 | 
| c6g\.medium | 1 | 2\.00 | 
| c6g\.large | 2 | 4\.00 | 
| c6g\.xlarge | 4 | 8\.00 | 
| c6g\.2xlarge | 8 | 16\.00 | 
| c6g\.4xlarge | 16 | 32\.00 | 
| c6g\.8xlarge | 32 | 64\.00 | 
| c6g\.12xlarge | 48 | 96\.00 | 
| c6g\.16xlarge | 64 | 128\.00 | 
| c6g\.metal | 64 | 128\.00 | 
| c6gd\.medium | 1 | 2\.00 | 
| c6gd\.large | 2 | 4\.00 | 
| c6gd\.xlarge | 4 | 8\.00 | 
| c6gd\.2xlarge | 8 | 16\.00 | 
| c6gd\.4xlarge | 16 | 32\.00 | 
| c6gd\.8xlarge | 32 | 64\.00 | 
| c6gd\.12xlarge | 48 | 96\.00 | 
| c6gd\.16xlarge | 64 | 128\.00 | 
| c6gd\.metal | 64 | 128\.00 | 
| c6gn\.medium | 1 | 2\.00 | 
| c6gn\.large | 2 | 4\.00 | 
| c6gn\.xlarge | 4 | 8\.00 | 
| c6gn\.2xlarge | 8 | 16\.00 | 
| c6gn\.4xlarge | 16 | 32\.00 | 
| c6gn\.8xlarge | 32 | 64\.00 | 
| c6gn\.12xlarge | 48 | 96\.00 | 
| c6gn\.16xlarge | 64 | 128\.00 | 
| c6i\.large | 2 | 4\.00 | 
| c6i\.xlarge | 4 | 8\.00 | 
| c6i\.2xlarge | 8 | 16\.00 | 
| c6i\.4xlarge | 16 | 32\.00 | 
| c6i\.8xlarge | 32 | 64\.00 | 
| c6i\.12xlarge | 48 | 96\.00 | 
| c6i\.16xlarge | 64 | 128\.00 | 
| c6i\.24xlarge | 96 | 192\.00 | 
| c6i\.32xlarge | 128 | 256\.00 | 
| c6i\.metal | 128 | 256\.00 | 
| c6id\.large | 2 | 4\.00 | 
| c6id\.xlarge | 4 | 8\.00 | 
| c6id\.2xlarge | 8 | 16\.00 | 
| c6id\.4xlarge | 16 | 32\.00 | 
| c6id\.8xlarge | 32 | 64\.00 | 
| c6id\.12xlarge | 48 | 96\.00 | 
| c6id\.16xlarge | 64 | 128\.00 | 
| c6id\.24xlarge | 96 | 192\.00 | 
| c6id\.32xlarge | 128 | 256\.00 | 
| c6id\.metal | 128 | 256\.00 | 
| c6in\.large | 2 | 4\.00 | 
| c6in\.xlarge | 4 | 8\.00 | 
| c6in\.2xlarge | 8 | 16\.00 | 
| c6in\.4xlarge | 16 | 32\.00 | 
| c6in\.8xlarge | 32 | 64\.00 | 
| c6in\.12xlarge | 48 | 96\.00 | 
| c6in\.16xlarge | 64 | 128\.00 | 
| c6in\.24xlarge | 96 | 192\.00 | 
| c6in\.32xlarge | 128 | 256\.00 | 
| c7g\.medium | 1 | 2\.00 | 
| c7g\.large | 2 | 4\.00 | 
| c7g\.xlarge | 4 | 8\.00 | 
| c7g\.2xlarge | 8 | 16\.00 | 
| c7g\.4xlarge | 16 | 32\.00 | 
| c7g\.8xlarge | 32 | 64\.00 | 
| c7g\.12xlarge | 48 | 96\.00 | 
| c7g\.16xlarge | 64 | 128\.00 | 
| c7g\.metal | 64 | 128\.00 | 
| cc2\.8xlarge | 32 | 60\.50 | 
| hpc6a\.48xlarge | 96 | 384\.00 | 

The compute optimized instances use the following processors\.

**AWS Graviton processors**
+ **AWS Graviton2**: C6g, C6gd, C6gn
+ **AWS Graviton3**: C7g

**AMD processors**
+ **2nd generation AMD EPYC processors \(AMD EPYC 7R32\)**: C5a, C5ad
+ **3rd generation AMD EPYC processors \(AMD EPYC 7R13\)**: C6a, Hpc6a

**Intel processors**
+ **Intel Xeon Scalable processors \(Haswell E5\-2666 v3\)**: C4
+ **Intel Xeon Scalable processors \(Skylake 8124\)**: C5n
+ **Intel Xeon Scalable processors \(Skylake 8124M or Cascade Lake 8223CL\)**: Smaller C5 and C5d
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8275CL\)**: Larger C5 and C5d
+ **3rd generation Intel Xeon Scalable processors \(Ice Lake 8375C\)**: C6i, C6id

For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance performance<a name="compute-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some compute optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some compute optimized instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="compute-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for compute optimized instances that support enhanced networking\.


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | Enhanced networking features | 
| --- | --- | --- | --- | 
| c1\.medium † | 0\.3 | 1\.2 | Not supported | 
| c1\.xlarge † | 1\.0 | 10\.0 | Not supported | 
| c3\.large † | 0\.5 | 1\.2 | Not supported | 
| c3\.xlarge † | 0\.7 | 10\.0 | Not supported | 
| c3\.2xlarge † | 1\.0 | 10\.0 | Not supported | 
| c3\.4xlarge † | 2\.0 | 10\.0 | Not supported | 
| c3\.8xlarge † | 5\.0 | 10\.0 | Not supported | 
| c4\.large † | 0\.625 | 1\.2 | Not supported | 
| c4\.xlarge † | 1\.25 | 2\.8 | Not supported | 
| c4\.2xlarge † | 2\.5 | 10\.0 | Not supported | 
| c4\.4xlarge † | 5\.0 | 10\.0 | Not supported | 
| c4\.8xlarge † | 5\.0 | 10\.0 | Not supported | 
| c5\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.9xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.12xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.18xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.24xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5\.metal | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.8xlarge | 10\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.12xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.16xlarge | 20\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5a\.24xlarge | 20\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.8xlarge | 10\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.12xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.16xlarge | 20\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5ad\.24xlarge | 20\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.9xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.12xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.18xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.24xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5d\.metal | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5n\.large † | 3\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5n\.xlarge † | 5\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5n\.2xlarge † | 10\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5n\.4xlarge † | 15\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c5n\.9xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c5n\.18xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c5n\.metal | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6a\.large † | 0\.781 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.xlarge † | 1\.562 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.2xlarge † | 3\.125 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.4xlarge † | 6\.25 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.8xlarge | 12\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.12xlarge | 18\.75 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.24xlarge | 37\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.32xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6a\.48xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6a\.metal | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6g\.medium † | 0\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.8xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.12xlarge | 20\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6g\.metal | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.medium † | 0\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.large † | 0\.75 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.8xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.12xlarge | 20\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gd\.metal | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.medium † | 1\.6 | 16\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.large † | 3\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.xlarge † | 6\.3 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.2xlarge † | 12\.5 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.4xlarge † | 15\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.8xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.12xlarge | 75\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6gn\.16xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6i\.large † | 0\.781 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.xlarge † | 1\.562 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.2xlarge † | 3\.125 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.4xlarge † | 6\.25 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.8xlarge | 12\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.12xlarge | 18\.75 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.24xlarge | 37\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6i\.32xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6i\.metal | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6id\.large † | 0\.781 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.xlarge † | 1\.562 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.2xlarge † | 3\.125 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.4xlarge † | 6\.25 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.8xlarge | 12\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.12xlarge | 18\.75 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.24xlarge | 37\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6id\.32xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6id\.metal | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c6in\.large † | 3\.125 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.xlarge † | 6\.25 | 30\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.2xlarge † | 12\.5 | 40\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.4xlarge † | 25\.0 | 50\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.8xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.12xlarge | 75\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.16xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.24xlarge | 150\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c6in\.32xlarge | 400\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c7g\.medium † | 0\.52 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.large † | 0\.937 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.xlarge † | 1\.876 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.2xlarge † | 3\.75 | 15\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.4xlarge † | 7\.5 | 15\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.8xlarge | 15\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.12xlarge | 22\.5 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| c7g\.16xlarge | 30\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| c7g\.metal | 30\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| hpc6a\.48xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.

## Amazon EBS I/O performance<a name="compute-ebs-perf"></a>

Amazon EBS optimized instances use an optimized configuration stack and provide additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your Amazon EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## SSD\-based instance store volume I/O performance<a name="compute-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


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
| c5d\.large | 20,000 | 9,000 | 
| c5d\.xlarge | 40,000 | 18,000 | 
| c5d\.2xlarge | 80,000 | 37,000 | 
| c5d\.4xlarge | 175,000 | 75,000 | 
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
| c6id\.large | 33,542 | 16,771 | 
| c6id\.xlarge | 67,083 | 33,542 | 
| c6id\.2xlarge | 134,167 | 67,084 | 
| c6id\.4xlarge | 268,333 | 134,167 | 
| c6id\.8xlarge | 536,666 | 268,334 | 
| c6id\.12xlarge | 804,999 | 402,501 | 
| c6id\.16xlarge | 1,073,332 | 536,668 | 
| c6id\.24xlarge | 1,609,998 | 805,002 | 
| c6id\.32xlarge | 2,146,664 | 1,073,336 | 
| c6id\.metal | 2,146,664 | 1,073,336 | 

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Release notes<a name="compute-instance-release-notes"></a>
+ C4 instances and instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) require 64\-bit EBS\-backed HVM AMIs\. They have high\-memory and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.
+ C7g instances are powered by the latest generation AWS Graviton3 processors\. They offer up to 25% better performance over the sixth generation AWS Graviton2\-based C6g instances\. C7g instances are the first in the cloud to feature DDR5 memory, which provides 50% higher memory bandwidth compared to DDR4 memory to enable high\-speed access to data in memory\.
  + C7g instances limit the aggregate execution rate of high power instructions such as floating point multiply accumulates across cores in an instance\. Future instance types focused on other workload segments may not have this restriction
+ Instances built on the Nitro System have the following requirements:
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
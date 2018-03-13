# Compute Optimized Instances<a name="compute-optimized-instances"></a>

Compute optimized instances are ideal for compute\-bound applications that benefit from high\-performance processors\. They are well suited for the following applications:

+ Batch processing workloads

+ Media transcoding

+ High\-performance web servers

+ High\-performance computing \(HPC\)

+ Scientific modeling

+ Dedicated gaming servers and ad serving engines

+ Machine learning inference and other compute\-intensive applications


+ [Hardware Specifications](#compute-instances-hardware)
+ [Instance Performance](#compute-performance)
+ [Network Performance](#compute-network-performance)
+ [Instance Features](#compute-instances-features)
+ [Release Notes](#compute-instance-limits)

## Hardware Specifications<a name="compute-instances-hardware"></a>

The following is a summary of the hardware specifications for Compute optimized instances\.


****  

| Instance type | vCPUs | Memory \(GiB\) | 
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

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance Performance<a name="compute-performance"></a>

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. C4 and C5 instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

The `c4.8xlarge` instance type provides the ability to control processor C\-states and P\-states on Linux\. The `c5.9xlarge` and `c5.18xlarge` instance types provide the ability to control processor C\-states\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="compute-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance types that use the ENA and support up to 10 Gbps of throughput use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network throughput is below their baseline limits, and can use these credits when they perform network data transfers\. For workloads that require access to 10 Gbps of bandwidth or more on a sustained basis, we recommend using instance types that support 10 Gbps or 25 Gbps network speeds\.

The following is a summary of network performance for Compute optimized instances that support enhanced networking\.


****  

| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| c5\.4xlarge and smaller | Up to 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5\.9xlarge | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| c5\.18xlarge | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
| c4\.large | Moderate | [Intel 82599 VF](sriov-networking.md) | 
| c4\.xlarge, c4\.2xlarge, c4\.4xlarge | High | [Intel 82599 VF](sriov-networking.md) | 
| c4\.8xlarge | 10 Gbps | [Intel 82599 VF](sriov-networking.md) | 

## Instance Features<a name="compute-instances-features"></a>

The following is a summary of features for Compute optimized instances:


|  | VPC only | EBS only | Placement group | 
| --- | --- | --- | --- | 
|  C4  |  Yes  |  Yes  |  Yes  | 
|  C5  |  Yes  |  Yes  |  Yes  | 

For more information, see the following:

+ [Instance Types Available Only in a VPC](using-vpc.md#vpc-only-instance-types)

+ [Amazon EBS–Optimized Instances](EBSOptimized.md)

+ [Amazon EC2 Instance Store](InstanceStorage.md)

+ [Placement Groups](placement-groups.md)

+ [Enhanced Networking on Linux](enhanced-networking.md)

## Release Notes<a name="compute-instance-limits"></a>

+ C4 and C5 instances require 64\-bit EBS\-backed HVM AMIs\. They have high\-memory \(up to 144 GiB of RAM\), and require a 64\-bit operating system to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on high\-memory instance types\. In addition, you must use an HVM AMI to take advantage of enhanced networking\.

+ C5 instances have the following requirements: 

  + Must have the NVMe drivers installed\. EBS volumes are exposed as [NVMe block devices](nvme-ebs-volumes.md)\.

  + Must have the Elastic Network Adapter \([ENA](enhanced-networking-ena.md)\) drivers installed\.

  The following AMIs meet these requirements:

  + Amazon Linux 2014\.03 or later

  + Ubuntu 14\.04 or later

  + SUSE Linux Enterprise Server 12 or later

  + Red Hat Enterprise Linux 7\.4 or later

  + CentOS 7 or later

  + FreeBSD 11\.1\-RELEASE

  + Windows Server 2008 R2 or later

+ C5 instances support a maximum of 27 EBS volumes plus elastic network interface attachments\. For example, `c5.2xlarge` instances support four network interfaces\. Every instance has at least one network interface\. If you have a `c5.2xlarge` instance with three additional elastic network interface attachments, you can attach 24 EBS volumes to that instance\.

+ C5 instances should have acpid installed to support clean shutdown through API requests\.

+ ClassicLink is not supported for C5 instances—you cannot use ClassicLink to link your EC2\-Classic instances to C5 instances in your VPC\.

+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.

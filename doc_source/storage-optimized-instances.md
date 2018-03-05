# Storage Optimized Instances<a name="storage-optimized-instances"></a>

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage\. They are optimized to deliver tens of thousands of low\-latency, random I/O operations per second \(IOPS\) to applications\.<a name="d2-instances"></a>

**D2 Instances**

D2 instances are well suited for the following applications:

+ Massive parallel processing \(MPP\) data warehouse

+ MapReduce and Hadoop distributed computing

+ Log or data processing applications<a name="h1-instances"></a>

**H1 Instances**

H1 instances are well suited for the following applications:

+ Data\-intensive workloads such as MapReduce and distributed file systems

+ Applications requiring sequential access to large amounts of data on direct\-attached instance storage

+ Applications that require high\-throughput access to large quantities of data<a name="i3-instances"></a>

**I3 Instances**

I3 instances are well suited for the following applications:

+ High frequency online transaction processing \(OLTP\) systems

+ Relational databases

+ NoSQL databases

+ Cache for in\-memory databases \(for example, Redis\)

+ Data warehousing applications

+ Low latency Ad\-Tech serving applications


+ [Hardware Specifications](#storage-instances-hardware)
+ [Instance Performance](#storage-performance)
+ [Network Performance](#storage-network-performance)
+ [SSD I/O Performance](#i2-instances-diskperf)
+ [Instance Features](#storage-instances-features)
+ [Support for vCPUs](#d2-instances-cpu-support)
+ [Release Notes](#storage-instance-limits)

## Hardware Specifications<a name="storage-instances-hardware"></a>

The primary data storage for D2 instances is HDD instance store volumes\. The primary data storage for I3 instances is non\-volatile memory express \(NVMe\) SSD instance store volumes\.

Instance store volumes persist only for the life of the instance\. When you stop or terminate an instance, the applications and data in its instance store volumes are erased\. We recommend that you regularly back up or replicate important data in your instance store volumes\. For more information, see [Amazon EC2 Instance Store](InstanceStorage.md) and [SSD Instance Store Volumes](ssd-instance-store.md)\.

The following is a summary of the hardware specifications for Storage optimized instances\.


****  

| Instance type | vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| d2\.xlarge | 4 | 30\.5 | 
| d2\.2xlarge | 8 | 61 | 
| d2\.4xlarge | 16 | 122 | 
| d2\.8xlarge | 36 | 244 | 
| h1\.2xlarge | 8 | 32 | 
| h1\.4xlarge | 16 | 64 | 
| h1\.8xlarge | 32 | 128 | 
| h1\.16xlarge | 64 | 256 | 
| i3\.large | 2 | 15\.25 | 
| i3\.xlarge | 4 | 30\.5 | 
| i3\.2xlarge | 8 | 61 | 
| i3\.4xlarge | 16 | 122 | 
| i3\.8xlarge | 32 | 244 | 
| i3\.16xlarge | 64 | 488 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance Performance<a name="storage-performance"></a>

To ensure the best disk throughput performance from your instance on Linux, we recommend that you use the most recent version of the Amazon Linux AMI\.

For instances with NVMe instance store volumes, you must use a Linux AMI with kernel version 4\.4 or later\. Otherwise, your instance will not achieve the maximum IOPS performance available\.

D2 instances provide the best disk performance when you use a Linux kernel that supports persistent grants, an extension to the Xen block ring protocol that significantly improves disk throughput and scalability\. For more information about persistent grants, see [this article](https://blog.xenproject.org/2012/11/23/improving-block-protocol-scalability-with-persistent-grants/) in the Xen Project Blog\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. D2 and H1 instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

The `h1.16xlarge`, `h1.8xlarge`, `d2.8xlarge`, and `i3.16xlarge` instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="storage-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance types that use the ENA and support up to 10 Gbps of throughput use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network throughput is below their baseline limits, and can use these credits when they perform network data transfers\. For workloads that require access to 10 Gbps of bandwidth or more on a sustained basis, we recommend using instance types that support 10 Gbps or 25 Gbps network speeds\.

The following is a summary of network performance for Storage optimized instances that support enhanced networking\.


****  

| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `i3.4xlarge` and smaller  |  Up to 10 Gbps, use network I/O credit mechanism  | [ENA](enhanced-networking-ena.md) | 
|  `i3.8xlarge`, `h1.8xlarge`  |  10 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `i3.16xlarge`, `h1.16xlarge`  |  25 Gbps  | [ENA](enhanced-networking-ena.md) | 
|  `d2.xlarge`  |  Moderate  | [Intel 82599 VF](sriov-networking.md) | 
| d2\.2xlarge, d2\.4xlarge |  High  | [Intel 82599 VF](sriov-networking.md) | 
| d2\.8xlarge |  10 Gbps  | [Intel 82599 VF](sriov-networking.md) | 

## SSD I/O Performance<a name="i2-instances-diskperf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
|  `i3.large` \*  |  100,125  |  35,000  | 
|  `i3.xlarge` \*  |  206,250  |  70,000  | 
|  `i3.2xlarge`  |  412,500  |  180,000  | 
|  `i3.4xlarge`  |  825,000  |  360,000  | 
|  `i3.8xlarge`  |  1\.65 million  |  720,000  | 
|  `i3.16xlarge`  |  3\.3 million  |  1\.4 million  | 

\* For `i3.large` and `i3.xlarge` instances, you can get up to the specified performance\.

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance Features<a name="storage-instances-features"></a>

The following is a summary of features for Storage optimized instances:


|  | VPC only | SSD volumes | Placement group | Enhanced networking | 
| --- | --- | --- | --- | --- | 
|  D2  |   |   |  Yes  |  [Intel 82599 VF](sriov-networking.md)  | 
|  H1  |  Yes  |   |  Yes  |  [ENA](enhanced-networking-ena.md)  | 
|  I3  |  Yes  |  NVMe  |  Yes  |  [ENA](enhanced-networking-ena.md)  | 

For more information, see the following:

+ [Instance Types Available Only in a VPC](using-vpc.md#vpc-only-instance-types)

+ [Amazon EBS–Optimized Instances](EBSOptimized.md)

+ [Amazon EC2 Instance Store](InstanceStorage.md)

+ [Placement Groups](placement-groups.md)

+ [Enhanced Networking on Linux](enhanced-networking.md)

## Support for vCPUs<a name="d2-instances-cpu-support"></a>

The `d2.8xlarge` instance type provides 36 vCPUs, which might cause launch issues in some Linux operating systems that have a vCPU limit of 32\. We strongly recommend that you use the latest AMIs when you launch `d2.8xlarge` instances\.

The following Linux AMIs support launching `d2.8xlarge` instances with 36 vCPUs:

+ Amazon Linux AMI 2017\.09 \(HVM\)

+ Ubuntu Server 14\.04 LTS \(HVM\)

+ Red Hat Enterprise Linux 7\.1 \(HVM\)

+ SUSE Linux Enterprise Server 12 \(HVM\)

If you must use a different AMI for your application, and your `d2.8xlarge` instance launch does not complete successfully \(for example, if your instance status changes to `stopped` during launch with a `Client.InstanceInitiatedShutdown` state transition reason\), modify your instance as described in the following procedure to support more than 32 vCPUs so that you can use the `d2.8xlarge` instance type\.

**To update an instance to support more than 32 vCPUs**

1. Launch a D2 instance using your AMI, choosing any D2 instance type other than `d2.8xlarge`\.

1. Update the kernel to the latest version by following your operating system\-specific instructions\. For example, for RHEL 6, use the following command:

   ```
   sudo yum update -y kernel
   ```

1. Stop the instance\.

1. \(Optional\) Create an AMI from the instance that you can use to launch any additional `d2.8xlarge` instances that you need in the future\.

1. Change the instance type of your stopped instance to `d2.8xlarge` \(choose **Actions**, **Instance Settings**, **Change Instance Type**, and then follow the directions\)\.

1. Start the instance\. If the instance launches properly, you are done\. If the instance still does not boot properly, proceed to the next step\.

1. \(Optional\) If the instance still does not boot properly, the kernel on your instance may not support more than 32 vCPUs\. However, you may be able to boot the instance if you limit the vCPUs\.

   1. Change the instance type of your stopped instance to any D2 instance type other than `d2.8xlarge` \(choose **Actions**, **Instance Settings**, **Change Instance Type**, and then follow the directions\)\.

   1. Add the `maxcpus=32` option to your boot kernel parameters by following your operating system\-specific instructions\. For example, for RHEL 6, edit the `/boot/grub/menu.lst` file and add the following option to the most recent and active `kernel` entry:

      ```
      default=0
      timeout=1
      splashimage=(hd0,0)/boot/grub/splash.xpm.gz
      hiddenmenu
      title Red Hat Enterprise Linux Server (2.6.32-504.3.3.el6.x86_64)
      root (hd0,0)
      kernel /boot/vmlinuz-2.6.32-504.3.3.el6.x86_64 maxcpus=32 console=ttyS0 ro root=UUID=9996863e-b964-47d3-a33b-3920974fdbd9 rd_NO_LUKS  KEYBOARDTYPE=pc KEYTABLE=us LANG=en_US.UTF-8 xen_blkfront.sda_is_xvda=1 console=ttyS0,115200n8 console=tty0 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_NO_LVM rd_NO_DM
      initrd /boot/initramfs-2.6.32-504.3.3.el6.x86_64.img
      ```

   1. Stop the instance\.

   1. \(Optional\) Create an AMI from the instance that you can use to launch any additional `d2.8xlarge` instances that you need in the future\.

   1. Change the instance type of your stopped instance to `d2.8xlarge` \(choose **Actions**, **Instance Settings**, **Change Instance Type**, and then follow the directions\)\.

   1. Start the instance\.

## Release Notes<a name="storage-instance-limits"></a>

+ You must launch Storage optimized instances using an HVM AMI\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

+ You must launch I3 instances using an Amazon EBS\-backed AMI\.

+ The `d2.8xlarge` instance type has 36 vCPUs, which might cause launch issues in some Linux operating systems that have a vCPU limit of 32\. For more information, see [Support for vCPUs](#d2-instances-cpu-support)\.

+ There is a limit on the total number of instances that you can launch in a region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2)\. To request a limit increase, use the [Amazon EC2 Instance Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
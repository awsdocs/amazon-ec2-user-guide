# Storage optimized instances<a name="storage-optimized-instances"></a>

Storage optimized instances are designed for workloads that require high, sequential read and write access to very large data sets on local storage\. They are optimized to deliver tens of thousands of low\-latency, random I/O operations per second \(IOPS\) to applications\.<a name="d2-instances"></a>

**D2 instances**

These instances are well suited for the following:
+ Massive parallel processing \(MPP\) data warehouse
+ MapReduce and Hadoop distributed computing
+ Log or data processing applications<a name="d3-instances"></a>

**D3 and D3en instances**

These instances offer scale out of instance storage and are well suited for the following:
+ Distributed file systems for Hadoop workloads
+ File storage workloads such as GPFC and BeeFS
+ Large data lakes for HPC workloads<a name="h1-instances"></a>

**H1 instances**

These instances are well suited for the following:
+ Data\-intensive workloads such as MapReduce and distributed file systems
+ Applications requiring sequential access to large amounts of data on direct\-attached instance storage
+ Applications that require high\-throughput access to large quantities of data<a name="i3-instances"></a>

**I3 and I3en instances**

These instances are well suited for the following:
+ High frequency online transaction processing \(OLTP\) systems
+ Relational databases
+ NoSQL databases
+ Cache for in\-memory databases \(for example, Redis\)
+ Data warehousing applications
+ Distributed file systems

Bare metal instances provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 I3 Instances](https://aws.amazon.com/ec2/instance-types/i3)\.

**Topics**
+ [Hardware specifications](#storage-instances-hardware)
+ [Instance performance](#storage-performance)
+ [Network performance](#storage-network-performance)
+ [SSD I/O performance](#i2-instances-diskperf)
+ [Instance features](#storage-instances-features)
+ [Support for vCPUs](#d2-instances-cpu-support)
+ [Release notes](#storage-instance-release-notes)

## Hardware specifications<a name="storage-instances-hardware"></a>

The primary data storage for D2, D3, and D3en instances is HDD instance store volumes\. The primary data storage for I3 and I3en instances is non\-volatile memory express \(NVMe\) SSD instance store volumes\.

Instance store volumes persist only for the life of the instance\. When you stop, hibernate, or terminate an instance, the applications and data in its instance store volumes are erased\. We recommend that you regularly back up or replicate important data in your instance store volumes\. For more information, see [Amazon EC2 instance store](InstanceStorage.md) and [SSD instance store volumes](ssd-instance-store.md)\.

The following is a summary of the hardware specifications for storage optimized instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| d2\.xlarge | 4 | 30\.5 | 
| d2\.2xlarge | 8 | 61 | 
| d2\.4xlarge | 16 | 122 | 
| d2\.8xlarge | 36 | 244 | 
| d3\.xlarge | 4 | 32 | 
| d3\.2xlarge | 8 | 64 | 
| d3\.4xlarge | 16 | 128 | 
| d3\.8xlarge | 32 | 256 | 
| d3en\.large | 2 | 8 | 
| d3en\.xlarge | 4 | 16 | 
| d3en\.2xlarge | 8 | 32 | 
| d3en\.4xlarge | 16 | 64 | 
| d3en\.6xlarge | 24 | 96 | 
| d3en\.8xlarge | 32 | 128 | 
| d3en\.12xlarge | 48 | 192 | 
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
| i3\.metal | 72 | 512 | 
| i3en\.large | 2 | 16 | 
| i3en\.xlarge | 4 | 32 | 
| i3en\.2xlarge | 8 | 64 | 
| i3en\.3xlarge | 12 | 96 | 
| i3en\.6xlarge | 24 | 192 | 
| i3en\.12xlarge | 48 | 384 | 
| i3en\.24xlarge | 96 | 768 | 
| i3en\.metal | 96 | 768 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU options](instance-optimize-cpu.md)\.

## Instance performance<a name="storage-performance"></a>

To ensure the best disk throughput performance from your instance on Linux, we recommend that you use the most recent version of Amazon Linux 2 or the Amazon Linux AMI\.

For instances with NVMe instance store volumes, you must use a Linux AMI with kernel version 4\.4 or later\. Otherwise, your instance will not achieve the maximum IOPS performance available\.

D2 instances provide the best disk performance when you use a Linux kernel that supports persistent grants, an extension to the Xen block ring protocol that significantly improves disk throughput and scalability\. For more information about persistent grants, see [this article](https://blog.xenproject.org/2012/11/23/improving-block-protocol-scalability-with-persistent-grants/) in the Xen Project Blog\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some storage optimized instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some storage optimized instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="storage-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for storage optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
| d2\.xlarge | Moderate | [Intel 82599 VF](sriov-networking.md) | 
| d2\.2xlarge \| d2\.4xlarge | High | [Intel 82599 VF](sriov-networking.md) | 
| d3\.xlarge | Up to 5 Gbps † | [ENA](enhanced-networking-ena.md) | 
| d3\.2xlarge | 5 Gbps | [ENA](enhanced-networking-ena.md) | 
| i3\.4xlarge and smaller | Up to 10 Gbps † | [ENA](enhanced-networking-ena.md) | 
| d2\.8xlarge | 10 Gbps | [Intel 82599 VF](sriov-networking.md) | 
| d3\.4xlarge \| i3\.8xlarge \| h1\.8xlarge | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3en\.2xlarge and smaller \| i3en\.3xlarge and smaller | Up to 25 Gbps † | [ENA](enhanced-networking-ena.md) | 
| d3\.8xlarge \| d3en\.4xlarge \| i3\.16xlarge \| i3\.metal \| i3en\.6xlarge \| h1\.16xlarge  | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3en\.6xlarge | 40 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3\.8xlarge \| d3en\.8xlarge \| i3en\.12xlarge | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
| d3en\.12xlarge | 75 Gbps | [ENA](enhanced-networking-ena.md) | 
| i3en\.24xlarge \| i3en\.metal | 100 Gbps | [ENA](enhanced-networking-ena.md) | 

† These instances use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. They accrue credits when their bandwidth is below their baseline bandwidth, and can use these credits when they perform network data transfers\. For more information, open a support case and ask about baseline bandwidth for the specific instance types that you are interested in\.

## SSD I/O performance<a name="i2-instances-diskperf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you get the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| i3\.large \* |  100,125  |  35,000  | 
| i3\.xlarge \* |  206,250  |  70,000  | 
| i3\.2xlarge |  412,500  |  180,000  | 
| i3\.4xlarge |  825,000  |  360,000  | 
| i3\.8xlarge |  1\.65 million  |  720,000  | 
| i3\.16xlarge |  3\.3 million  |  1\.4 million  | 
| i3\.metal |  3\.3 million  |  1\.4 million  | 
| i3en\.large \* | 42,500 | 32,500 | 
| i3en\.xlarge \* | 85,000 | 65,000 | 
| i3en\.2xlarge \* | 170,000 | 130,000 | 
| i3en\.3xlarge | 250,000 | 200,000 | 
| i3en\.6xlarge | 500,000 | 400,000 | 
| i3en\.12xlarge | 1 million | 800,000 | 
| i3en\.24xlarge | 2 million | 1\.6 million | 
| i3en\.metal | 2 million | 1\.6 million | 

\* For these instances, you can get up to the specified performance\.

As you fill your SSD\-based instance store volumes, the I/O performance that you get decreases\. This is due to the extra work that the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance features<a name="storage-instances-features"></a>

The following is a summary of features for storage optimized instances:


|  | EBS only | Instance store | Placement group | 
| --- | --- | --- | --- | 
| D2 | No | HDD  | Yes | 
| D3 | No | HDD \*  | Yes | 
| D3en | No | HDD \*  | Yes | 
| H1 | No | HDD \* | Yes | 
| I3 | No | NVMe \* | Yes | 
| I3en | No | NVMe \* | Yes | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Linux instances](nvme-ebs-volumes.md)
+ [Amazon EC2 instance store](InstanceStorage.md)
+ [Placement groups](placement-groups.md)

## Support for vCPUs<a name="d2-instances-cpu-support"></a>

The `d2.8xlarge` instance type provides 36 vCPUs, which might cause launch issues in some Linux operating systems that have a vCPU limit of 32\. We strongly recommend that you use the latest AMIs when you launch `d2.8xlarge` instances\.

The following Linux AMIs support launching `d2.8xlarge` instances with 36 vCPUs:
+ Amazon Linux 2 \(HVM\)
+ Amazon Linux AMI 2018\.03 \(HVM\)
+ Ubuntu Server 14\.04 LTS \(HVM\) or later
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

1. Change the instance type of your stopped instance to `d2.8xlarge` \(choose **Actions**, **Instance settings**, **Change instance type**, and then follow the directions\)\.

1. Start the instance\. If the instance launches properly, you are done\. If the instance still does not boot properly, proceed to the next step\.

1. \(Optional\) If the instance still does not boot properly, the kernel on your instance may not support more than 32 vCPUs\. However, you may be able to boot the instance if you limit the vCPUs\.

   1. Change the instance type of your stopped instance to any D2 instance type other than `d2.8xlarge` \(choose **Actions**, **Instance settings**, **Change instance type**, and then follow the directions\)\.

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

## Release notes<a name="storage-instance-release-notes"></a>
+ You must launch storage optimized instances using an HVM AMI\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.
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
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ With FreeBSD AMIs, bare metal instances take nearly an hour to boot and I/O to the local NVMe storage does not complete\. As a workaround, add the following line to `/boot/loader.conf` and reboot:

  ```
  hw.nvme.per_cpu_io_queues="0"
  ```
+ The `d2.8xlarge` instance type has 36 vCPUs, which might cause launch issues in some Linux operating systems that have a vCPU limit of 32\. For more information, see [Support for vCPUs](#d2-instances-cpu-support)\.
+ The `d3.8xlarge` and `d3en.12xlarge` instances support a maximum of three attachments, including the root volume\. If you exceed the attachment limit when you add a network interface or EBS volume, this causes attachment issues on your instance\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.
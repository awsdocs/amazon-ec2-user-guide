# Instance Volume Limits<a name="volume_limits"></a>

The maximum number of volumes that your instance can have depends on the operating system and instance type\. When considering how many volumes to add to your instance, you should consider whether you need increased I/O bandwidth or increased storage capacity\.

**Topics**
+ [Linux\-Specific Volume Limits](#linux-specific-volume-limits)
+ [Windows\-Specific Volume Limits](#windows-specific-volume-limits)
+ [Instance Type Limits](#instance-type-volume-limits)
+ [Bandwidth versus Capacity](#storage-bandwidth)

## Linux\-Specific Volume Limits<a name="linux-specific-volume-limits"></a>

Attaching more than 40 volumes can cause boot failures\. Note that this number includes the root volume, plus any attached instance store volumes and EBS volumes\. If you experience boot problems on an instance with a large number of volumes, stop the instance, detach any volumes that are not essential to the boot process, and then reattach the volumes after the instance is running\.

**Important**  
Attaching more than 40 volumes to a Linux instance is supported on a best effort basis only and is not guaranteed\.

## Windows\-Specific Volume Limits<a name="windows-specific-volume-limits"></a>

The following table shows the volume limits for Windows instances based on the driver used\. Note that these numbers include the root volume, plus any attached instance store volumes and EBS volumes\.

**Important**  
Attaching more than the following volumes to a Windows instance is supported on a best effort basis only and is not guaranteed\.


| Driver | Volume Limit | 
| --- | --- | 
|  AWS PV  |  26  | 
|  Citrix PV  |  26  | 
|  Red Hat PV  |  17  | 

We do not recommend that you give a Windows instance more than 26 volumes with AWS PV or Citrix PV drivers, as it is likely to cause performance issues\.

To determine which PV drivers your instance is using, or to upgrade your Windows instance from Red Hat to Citrix PV drivers, see [Upgrading PV Drivers on Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Upgrading_PV_drivers.html)\.

For more information about how device names related to volumes, see [Mapping Disks to Volumes on Your Windows EC2 Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-volumes.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Instance Type Limits<a name="instance-type-volume-limits"></a>

A1, C5, C5d, C5n, G4, I3en, Inf1, M5, M5a, M5ad, M5d, M5dn, M5n, `p3dn.24xlarge`, R5, R5a, R5ad, R5d, R5dn, R5n, T3, T3a, and z1d instances support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. Every instance has at least one network interface attachment\. NVMe instance store volumes are automatically attached\. For example, if you have no additional network interface attachments on an EBS\-only instance, you can attach up to 27 EBS volumes to it\. If you have one additional network interface on an instance with 2 NVMe instance store volumes, you can attach 24 EBS volumes to it\. For more information, see [Elastic Network Interfaces](using-eni.md) and [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

`c5.metal`, `c5d.metal`, `i3.metal`, `m5.metal`, `m5d.metal`, `r5.metal`, `r5d.metal`, and `z1d.metal` instances support a maximum of 31 EBS volumes\.

`inf1.xlarge` and `inf1.2xlarge` instances support a maximum of 26 EBS volumes\. `inf1.6xlarge` instances support a maximum of 23 volumes\. `inf1.24xlarge` instances support a maximum of 11 EBS volumes\.

`u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` instances support a maximum of 13 EBS volumes\. `u-18tb1.metal` and `u-24tb1.metal` instances support a maximum of 19 EBS volumes\.

## Bandwidth versus Capacity<a name="storage-bandwidth"></a>

For consistent and predictable bandwidth use cases, use EBS\-optimized or 10 Gigabit network connectivity instances and General Purpose SSD or Provisioned IOPS SSD volumes\. Follow the guidance in [Amazon EBS–Optimized Instances](ebs-optimized.md) to match the IOPS you have provisioned for your volumes to the bandwidth available from your instances for maximum performance\. For RAID configurations, many administrators find that arrays larger than 8 volumes have diminished performance returns due to increased I/O overhead\. Test your individual application performance and tune it as required\.
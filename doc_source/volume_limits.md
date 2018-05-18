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

To determine which PV drivers your instance is using, or to upgrade your Windows instance from Red Hat to Citrix PV drivers, see [Upgrading PV Drivers on Your Windows Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Upgrading_PV_drivers.html)\.

For more information about how device names related to volumes, see [Mapping Disks to Volumes on Your Windows EC2 Instance](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-volumes.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Instance Type Limits<a name="instance-type-volume-limits"></a>

C5 and M5 instances support a maximum of 28 attachments, and every instance has at least one network interface attachment\. If you have no additional network interface attachments on a C5 or M5 instance, you could attach 27 EBS volumes\. For more information, see [Elastic Network Interfaces](using-eni.md)\.

## Bandwidth versus Capacity<a name="storage-bandwidth"></a>

For consistent and predictable bandwidth use cases, use EBS\-optimized or 10 Gigabit network connectivity instances and General Purpose SSD or Provisioned IOPS SSD volumes\. Follow the guidance in [Amazon EC2 Instance Configuration](ebs-ec2-config.md) to match the IOPS you have provisioned for your volumes to the bandwidth available from your instances for maximum performance\. For RAID configurations, many administrators find that arrays larger than 8 volumes have diminished performance returns due to increased I/O overhead\. Test your individual application performance and tune it as required\.
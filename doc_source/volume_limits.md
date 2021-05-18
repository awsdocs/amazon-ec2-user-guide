# Instance volume limits<a name="volume_limits"></a>

The maximum number of volumes that your instance can have depends on the operating system and instance type\. When considering how many volumes to add to your instance, you should consider whether you need increased I/O bandwidth or increased storage capacity\.

**Topics**
+ [Nitro System volume limits](#instance-type-volume-limits)
+ [Linux\-specific volume limits](#linux-specific-volume-limits)
+ [Bandwidth versus capacity](#storage-bandwidth)

## Nitro System volume limits<a name="instance-type-volume-limits"></a>

Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) support a maximum number of attachments, which are shared between network interfaces, EBS volumes, and NVMe instance store volumes\. Every instance has at least one network interface attachment\. NVMe instance store volumes are automatically attached\. For more information, see [Elastic network interfaces](using-eni.md) and [Instance store volumes](InstanceStorage.md#instance-store-volumes)\.

Most of these instances support a maximum of 28 attachments\. For example, if you have no additional network interface attachments on an EBS\-only instance, you can attach up to 27 EBS volumes to it\. If you have one additional network interface on an instance with 2 NVMe instance store volumes, you can attach 24 EBS volumes to it\.

For other instances, the following limits apply:
+ `d3.8xlarge` and `d3en.12xlarge` instances support a maximum of 3 EBS volumes\.
+ `inf1.xlarge` and `inf1.2xlarge` instances support a maximum of 26 EBS volumes\.
+ `inf1.6xlarge` instances support a maximum of 23 EBS volumes\.
+ `inf1.24xlarge` instances support a maximum of 11 EBS volumes\.
+ `mac1.metal` instances support a maximum of 16 EBS volumes\.
+ Most bare metal instances support a maximum of 31 EBS volumes\.
+ High memory virtualized instances support a maximum of 19 EBS volumes\.
+ The `u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` high memory bare metal instances support a maximum of 19 EBS volumes if launched after March 12, 2020 and a maximum of 14 EBS volumes otherwise\. To attach more than 14 EBS volumes to an instance launched before March 12, 2020, contact your account team to upgrade the instance at no additional cost\.
+ The `u-18tb1.metal` and `u-24tb1.metal` high memory bare metal instances support a maximum of 19 EBS volumes\.

## Linux\-specific volume limits<a name="linux-specific-volume-limits"></a>

Attaching more than 40 volumes can cause boot failures\. This number includes the root volume, plus any attached instance store volumes and EBS volumes\. If you experience boot problems on an instance with a large number of volumes, stop the instance, detach any volumes that are not essential to the boot process, and then reattach the volumes after the instance is running\.

**Important**  
Attaching more than 40 volumes to a Linux instance is supported on a best effort basis only and is not guaranteed\.

## Bandwidth versus capacity<a name="storage-bandwidth"></a>

For consistent and predictable bandwidth use cases, use EBS\-optimized or 10 Gigabit network connectivity instances and General Purpose SSD or Provisioned IOPS SSD volumes\. Follow the guidance in [Amazon EBS–optimized instances](ebs-optimized.md) to match the IOPS you have provisioned for your volumes to the bandwidth available from your instances for maximum performance\. For RAID configurations, many administrators find that arrays larger than 8 volumes have diminished performance returns due to increased I/O overhead\. Test your individual application performance and tune it as required\.
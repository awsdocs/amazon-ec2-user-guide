# Requirements when modifying volumes<a name="modify-volume-requirements"></a>

The following requirements and limitations apply when you modify an Amazon EBS volume\. To learn more about the general requirements for EBS volumes, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

**Topics**
+ [Supported instance types](#instance-support)
+ [Requirements for Linux volumes](#linux-volumes)
+ [Limitations](#elastic-volumes-limitations)

## Supported instance types<a name="instance-support"></a>

Elastic Volumes are supported on the following instances:
+ All [current\-generation instances](instance-types.md#AvailableInstanceTypes)
+ The following previous\-generation instances: C1, C3, CC2, CR1, G2, I2, M1, M3, and R3

If your instance type does not support Elastic Volumes, see [Modify an EBS volume if Elastic Volumes is not supported](requesting-ebs-volume-modifications.md#modify-volume-stop-start)\.

## Requirements for Linux volumes<a name="linux-volumes"></a>

Linux AMIs require a GUID partition table \(GPT\) and GRUB 2 for boot volumes that are 2 TiB \(2,048 GiB\) or larger\. Many Linux AMIs today still use the MBR partitioning scheme, which only supports boot volume sizes up to 2 TiB\. If your instance does not boot with a boot volume larger than 2 TiB, the AMI you are using may be limited to a boot volume size of less than 2 TiB\. Non\-boot volumes do not have this limitation on Linux instances\. For requirements affecting Windows volumes, see [ Requirements for Windows volumes](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/modify-volume-requirements.html#window-volumes) in the *Amazon EC2 User Guide for Windows Instances*\.

Before attempting to resize a boot volume beyond 2 TiB, you can determine whether the volume is using MBR or GPT partitioning by running the following command on your instance:

```
[ec2-user ~]$ sudo gdisk -l /dev/xvda
```

An Amazon Linux instance with GPT partitioning returns the following information:

```
GPT fdisk (gdisk) version 0.8.10
  
  Partition table scan:
    MBR: protective
    BSD: not present
    APM: not present
    GPT: present
  
  Found valid GPT with protective MBR; using GPT.
```

A SUSE instance with MBR partitioning returns the following information:

```
GPT fdisk (gdisk) version 0.8.8
  
  Partition table scan:
    MBR: MBR only
    BSD: not present
    APM: not present
    GPT: not present
```

## Limitations<a name="elastic-volumes-limitations"></a>
+ There are limits to the maximum aggregated storage that can be requested across volume modifications\. For more information, see [Amazon EBS service quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#limits_ebs) in the *Amazon Web Services General Reference*\.
+ After modifying a volume, you must wait at least six hours and ensure that the volume is in the `in-use` or `available` state before you can modify the same volume\. This is sometimes referred to as a cooldown period\.
+ If the volume was attached before November 3, 2016 23:40 UTC, you must initialize Elastic Volumes support\. For more information, see [Initializing Elastic Volumes Support](requesting-ebs-volume-modifications.md#initialize-modification-support)\.
+ If you encounter an error message while attempting to modify an EBS volume, or if you are modifying an EBS volume attached to a previous\-generation instance type, take one of the following steps:
  + For a non\-root volume, detach the volume from the instance, apply the modifications, and then re\-attach the volume\.
  + For a root volume, stop the instance, apply the modifications, and then restart the instance\.
+ Modification time is increased for volumes that are not fully initialized\. For more information see [Initialize Amazon EBS volumes](ebs-initialize.md)\.
+ The new volume size can't exceed the supported capacity of its file system and partitioning scheme\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.
+ If you modify the volume type of a volume, the size and performance must be within the limits of the target volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)
+ You can't decrease the size of an EBS volume\. However, you can create a smaller volume and then migrate your data to it using an application\-level tool such as rsync\.
+ After provisioning over 32,000 IOPS on an existing `io1` or `io2` volume, you might need to detach and re\-attach the volume, or restart the instance to see the full performance improvements\.
+ For `io2` volumes, you can't increase the size beyond `16` TiB or the IOPS beyond `64,000` while the volume is attached to an instance type that does not support `io2` Block Express volumes\. Currently, only C7g, R5b, X2idn, and X2iedn instances support `io2` Block Express volumes volumes\. For more information, see [`io2` Block Express volumes](ebs-volume-types.md#io2-block-express)
+ You can't modify the size or provisioned IOPS of an `io2` volume that is attached to an C7g, R5b, X2idn, and X2iedn instance\.
+ You can't modify the volume type of Multi\-Attach enabled `io2` volumes\.
+ You can't modify the volume type, size, or Provisioned IOPS of Multi\-Attach enabled `io1` volumes\.
+ A `gp2` volume that is attached to an instance as a root volume can't be modified to an `st1` or `sc1` volume\. If detached and modified to `st1` or `sc1`, it can't be re\-attached to an instance as the root volume\.
+ While `m3.medium` instances fully support volume modification, `m3.large`, `m3.xlarge`, and `m3.2xlarge` instances might not support all volume modification features\.
# Requirements when modifying volumes<a name="modify-volume-requirements"></a>

The following requirements and limitations apply when you modify an Amazon EBS volume\. To learn more about the general requirements for EBS volumes, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

## Supported instance types<a name="instance-support"></a>

Elastic Volumes are supported on the following instances:
+ All [current\-generation instances](instance-types.md#AvailableInstanceTypes)
+ The following previous\-generation instances: C1, C3, CC2, CR1, G2, I2, M1, M3, and R3

If your instance type does not support Elastic Volumes, see [Modifying an EBS volume if Elastic Volumes is not supported](requesting-ebs-volume-modifications.md#modify-volume-stop-start)\.

## Requirements for Linux volumes<a name="linux-volumes"></a>

Linux AMIs require a GUID partition table \(GPT\) and GRUB 2 for boot volumes that are 2 TiB \(2,048 GiB\) or larger\. Many Linux AMIs today still use the MBR partitioning scheme, which only supports boot volume sizes up to 2 TiB\. If your instance does not boot with a boot volume larger than 2 TiB, the AMI you are using may be limited to a boot volume size of less than 2 TiB\. Non\-boot volumes do not have this limitation on Linux instances\. For requirements affecting Windows volumes, see [Requirements for Windows Volumes](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/volume_constraints.html) in the *Amazon EC2 User Guide for Windows Instances*\.

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
+ Elastic Volume operations are not supported on Multi\-Attach enabled Amazon EBS volumes\.
+ The new volume size cannot exceed the supported volume capacity\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.
+ If the volume was attached before November 3, 2016 23:40 UTC, you must initialize Elastic Volumes support\. For more information, see [Initializing Elastic Volumes Support](requesting-ebs-volume-modifications.md#initialize-modification-support)\.
+ If you are using an unsupported previous\-generation instance type, or if you encounter an error while attempting a volume modification, see [Modifying an EBS volume if Elastic Volumes is not supported](requesting-ebs-volume-modifications.md#modify-volume-stop-start)\.
+ A `gp2` volume that is attached to an instance as a root volume cannot be modified to an `st1` or `sc1` volume\. If detached and modified to `st1` or `sc1`, it cannot be attached to an instance as the root volume\.
+ A `gp2` volume cannot be modified to an `st1` or `sc1` volume if the requested volume size is below the minimum size for `st1` and `sc1` volumes\.
+ In some cases, you must detach the volume or stop the instance for modification to proceed\. If you encounter an error message while attempting to modify an EBS volume, or if you are modifying an EBS volume attached to a previous\-generation instance type, take one of the following steps:
  + For a non\-root volume, detach the volume from the instance, apply the modifications, and then re\-attach the volume\.
  + For a root \(boot\) volume, stop the instance, apply the modifications, and then restart the instance\.
+ After provisioning over 32,000 IOPS on an existing `io1` or `io2` volume, you may need to do one of the following to see the full performance improvements:
  + Detach and attach the volume\.
  + Restart the instance\.
+ Decreasing the size of an EBS volume is not supported\. However, you can create a smaller volume and then migrate your data to it using an application\-level tool such as rsync\.
+ Modification time is increased if you modify a volume that has not been fully initialized\. For more information see [Initializing Amazon EBS volumes](ebs-initialize.md)\.
+ After modifying a volume, wait at least six hours and ensure that the volume is in the `in-use` or `available` state before making additional modifications to the same volume\.
+ While `m3.medium` instances fully support volume modification, `m3.large`, `m3.xlarge`, and `m3.2xlarge` instances might not support all volume modification features\.
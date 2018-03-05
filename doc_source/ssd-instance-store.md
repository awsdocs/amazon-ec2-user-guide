# SSD Instance Store Volumes<a name="ssd-instance-store"></a>

The following instances support instance store volumes that use solid state drives \(SSD\) to deliver high random I/O performance: C3, F1, G2, I2, I3, M3, R3, and X1\. For more information about the instance store volumes support by each instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

To ensure the best IOPS performance from your SSD instance store volumes on Linux, we recommend that you use the most recent version of the [Amazon Linux AMI](https://aws.amazon.com/amazon-linux-ami/), or another Linux AMI with a kernel version of 3\.8 or later\. If you do not use a Linux AMI with a kernel version of 3\.8 or later, your instance will not achieve the maximum IOPS performance available for these instance types\.

Like other instance store volumes, you must map the SSD instance store volumes for your instance when you launch it\. The data on an SSD instance volume persists only for the life of its associated instance\. For more information, see [Add Instance Store Volumes to Your EC2 Instance](add-instance-store-volumes.md)\.

## NVMe SSD Volumes<a name="nvme-ssd-volumes"></a>

I3 and F1 instances offer non\-volatile memory express \(NVMe\) SSD instance store volumes\. To access the NVMe volumes, you must use an operating system that supports NVMe\. The following are the recommended operating systems:

+ The current Amazon Linux AMI

+ Ubuntu version 16\.04 or latest LTS release provided by AWS\. 

  We should be recommending: latest LTS \(16\.04\) or latest regular release\.\.\.which will change every 6 months and those releases are only supported for 9 months as opposed to 5 years for LTS releases\. 

  If you are using a different version, we recommend that you turn off memory hot add\.

+ Red Hat Enterprise Linux version 7\.4

+ CentOS 7 version 1708\_11

+ Windows Server 2016, Windows Server 2012 R2, or Windows Server 2008 R2\. Windows Server 2012 and Windows Server 2008 are not supported\.

SUSE Linux Enterprise Server 12 is not recommended at this time due to pending kernel updates\.

After you connect to your instance, you can list the NVMe devices using the lspci command\. The following is example output for an `i3.8xlarge` instance, which supports four NVMe devices\.

```
[ec2-user ~]$ lspci
00:00.0 Host bridge: Intel Corporation 440FX - 82441FX PMC [Natoma] (rev 02)
00:01.0 ISA bridge: Intel Corporation 82371SB PIIX3 ISA [Natoma/Triton II]
00:01.1 IDE interface: Intel Corporation 82371SB PIIX3 IDE [Natoma/Triton II]
00:01.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 01)
00:02.0 VGA compatible controller: Cirrus Logic GD 5446
00:03.0 Ethernet controller: Device 1d0f:ec20
00:17.0 Non-Volatile memory controller: Device 1d0f:cd01
00:18.0 Non-Volatile memory controller: Device 1d0f:cd01
00:19.0 Non-Volatile memory controller: Device 1d0f:cd01
00:1a.0 Non-Volatile memory controller: Device 1d0f:cd01
00:1f.0 Unassigned class [ff80]: XenSource, Inc. Xen Platform Device (rev 01)
```

If you are using a supported operating system but you do not see the NVMe devices, verify that the NVMe module is loaded using the following `lsmod` command\.

```
[ec2-user ~]$ lsmod | grep nvme
nvme          48813  0
```

The NVMe volumes are compliant with the NVMe 1\.0e specification\. You can use the NVMe commands with your NVMe volumes\. With the Amazon Linux AMI, you can install the `nvme-cli` package from the repo using the yum install command\. With other supported versions of Linux, you can download the `nvme-cli` package if it's not available in the image\.

## Instance Store Volume TRIM Support<a name="InstanceStoreTrimSupport"></a>

The following instances support SSD volumes with TRIM: F1, I2, I3, and R3\.

Instance store volumes that support TRIM are fully trimmed before they are allocated to your instance\. These volumes are not formatted with a file system when an instance launches, so you must format them before they can be mounted and used\. For faster access to these volumes, you should skip the TRIM operation when you format them\.

With instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller when you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. On Linux, you can use the `fstrim` command to enable periodic TRIM\. On Windows, you can use the `fsutil behavior set DisableDeleteNotify 1` command\. For more information, see the documentation for your operating system\.
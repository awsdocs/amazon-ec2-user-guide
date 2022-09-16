# Amazon EBS and NVMe on Linux instances<a name="nvme-ebs-volumes"></a>

EBS volumes are exposed as NVMe block devices on instances built on the [Nitro System](instance-types.md#ec2-nitro-instances)\. The device names are `/dev/nvme0n1`, `/dev/nvme1n1`, and so on\. The device names that you specify in a block device mapping are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\. The block device driver can assign NVMe device names in a different order than you specified for the volumes in the block device mapping\. 

The EBS performance guarantees stated in [Amazon EBS Product Details](http://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

For information about EBS volumes and NVME on Windows instances, see the [https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/nvme-ebs-volumes.html](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/nvme-ebs-volumes.html)\.

**Topics**
+ [Install or upgrade the NVMe driver](#install-nvme-driver)
+ [Identify the EBS device](#identify-nvme-ebs-device)
+ [Work with NVMe EBS volumes](#using-nvme-ebs-volumes)
+ [I/O operation timeout](#timeout-nvme-ebs-volumes)
+ [`Abort` command](#abort-command)

## Install or upgrade the NVMe driver<a name="install-nvme-driver"></a>

To access NVMe volumes, the NVMe drivers must be installed\. Instances can support NVMe EBS volumes, NVMe instance store volumes, both types of NVMe volumes, or no NVMe volumes\. For more information, see [Summary of networking and storage features](instance-types.md#instance-type-summary-table)\.

The following AMIs include the required NVMe drivers:
+ Amazon Linux 2
+ Amazon Linux AMI 2018\.03
+ Ubuntu 14\.04 \(with `linux-aws` kernel\) or later
+ Red Hat Enterprise Linux 7\.4 or later
+ SUSE Linux Enterprise Server 12 SP2 or later
+ CentOS 7\.4\.1708 or later
+ FreeBSD 11\.1 or later
+ Debian GNU/Linux 9 or later

For more information about NVMe drivers on Windows instances, see [Amazon EBS and NVMe on Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/nvme-ebs-volumes.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**To confirm that your instance has the NVMe driver**  
You can confirm that your instance has the NVMe driver using the following command\.
+ Amazon Linux, RHEL, CentOS, and SUSE Linux Enterprise Server

  ```
  $ modinfo nvme
  ```

  If the instance has the NVMe driver, the command returns information about the driver\.
+ Amazon Linux 2 and Ubuntu

  ```
  $ ls /sys/module/ | grep nvme
  ```

  If the instance has the NVMe driver, the command returns the installed drivers\.

**To update the NVMe driver**

If your instance has the NVMe driver, you can update the driver to the latest version using the following procedure\.

1. Connect to your instance\.

1. Update your package cache to get necessary package updates as follows\.
   + For Amazon Linux 2, Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     [ec2-user ~]$ sudo yum update -y
     ```
   + For Ubuntu and Debian:

     ```
     [ec2-user ~]$ sudo apt-get update -y
     ```

1. Ubuntu 16\.04 and later include the `linux-aws` package, which contains the NVMe and ENA drivers required by Nitro\-based instances\. Upgrade the `linux-aws` package to receive the latest version as follows:

   ```
   [ec2-user ~]$ sudo apt-get install --only-upgrade -y linux-aws
   ```

   For Ubuntu 14\.04, you can install the latest `linux-aws` package as follows:

   ```
   [ec2-user ~]$ sudo apt-get install linux-aws
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

## Identify the EBS device<a name="identify-nvme-ebs-device"></a>

EBS uses single\-root I/O virtualization \(SR\-IOV\) to provide volume attachments on Nitro\-based instances using the NVMe specification\. These devices rely on standard NVMe drivers on the operating system\. These drivers typically discover attached devices by scanning the PCI bus during instance boot, and create device nodes based on the order in which the devices respond, not on how the devices are specified in the block device mapping\. In Linux, NVMe device names follow the pattern `/dev/nvme<x>n<y>`, where <x> is the enumeration order, and, for EBS, <y> is 1\. Occasionally, devices can respond to discovery in a different order in subsequent instance starts, which causes the device name to change\. Additionally, the device name assigned by the block device driver can be different from the name specified in the block device mapping\.

We recommend that you use stable identifiers for your EBS volumes within your instance, such as one of the following:
+ For Nitro\-based instances, the block device mappings that are specified in the Amazon EC2 console when you are attaching an EBS volume or during `AttachVolume` or `RunInstances` API calls are captured in the vendor\-specific data field of the NVMe controller identification\. With Amazon Linux AMIs later than version 2017\.09\.01, we provide a `udev` rule that reads this data and creates a symbolic link to the block\-device mapping\.
+ The EBS volume ID and the mount point are stable between instance state changes\. The NVMe device name can change depending on the order in which the devices respond during instance boot\. We recommend using the EBS volume ID and the mount point for consistent device identification\.
+ NVMe EBS volumes have the EBS volume ID set as the serial number in the device identification\. Use the `lsblk -o +SERIAL` command to list the serial number\.
+ The NVMe device name format can vary depending on whether the EBS volume was attached during or after the instance launch\. NVMe device names for volumes attached after instance launch include the `/dev/` prefix, while NVMe device names for volumes attached during instance launch do not include the `/dev/` prefix\. If you are using an Amazon Linux or FreeBSD AMI, use the `sudo ebsnvme-id /dev/nvme0n1 -u` command for a consistent NVMe device name\. For other distributions, use the `sudo ebsnvme-id /dev/nvme0n1 -u` command to determine the NVMe device name\.
+ When a device is formatted, a UUID is generated that persists for the life of the filesystem\. A device label can be specified at the same time\. For more information, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md) and [Boot from the wrong volume](instance-booting-from-wrong-volume.md)\.

**Amazon Linux AMIs**  
With Amazon Linux AMI 2017\.09\.01 or later \(including Amazon Linux 2\), you can run the ebsnvme\-id command as follows to map the NVMe device name to a volume ID and device name:

The following example shows the command and output for a volume attached during instance launch\. Note that the NVMe device name does not include the `/dev/` prefix\.

```
[ec2-user ~]$ sudo /sbin/ebsnvme-id /dev/nvme0n1
Volume ID: vol-01324f611e2463981
sda
```

The following example shows the command and output for a volume attached after instance launch\. Note that the NVMe device name includes the `/dev/` prefix\.

```
[ec2-user ~]$ sudo /sbin/ebsnvme-id /dev/nvme1n1
Volume ID: vol-064784f1011136656
/dev/sdf
```

Amazon Linux also creates a symbolic link from the device name in the block device mapping \(for example, `/dev/sdf`\), to the NVMe device name\.

**FreeBSD AMIs**  
Starting with FreeBSD 12\.2\-RELEASE, you can run the ebsnvme\-id command as shown above\. Pass either the name of the NVMe device \(for example, `nvme0`\) or the disk device \(for example, `nvd0` or `nda0`\)\. FreeBSD also creates symbolic links to the disk devices \(for example, `/dev/aws/disk/ebs/`*volume\_id*\)\.

**Other Linux AMIs**  
With a kernel version of 4\.2 or later, you can run the nvme id\-ctrl command as follows to map an NVMe device to a volume ID\. First, install the NVMe command line package, `nvme-cli`, using the package management tools for your Linux distribution\. For download and installation instructions for other distributions, refer to the documentation specific to your distribution\.

The following example gets the volume ID and NVMe device name for a volume that was attached during instance launch\. Note that the NVMe device name does not include the `/dev/` prefix\. The device name is available through the NVMe controller vendor\-specific extension \(bytes 384:4095 of the controller identification\):

```
[ec2-user ~]$ sudo nvme id-ctrl -v /dev/nvme0n1
NVME Identify Controller:
vid     : 0x1d0f
ssvid   : 0x1d0f
sn      : vol01234567890abcdef
mn      : Amazon Elastic Block Store
...
0000: 2f 64 65 76 2f 73 64 6a 20 20 20 20 20 20 20 20 "sda..."
```

The following example gets the volume ID and NVMe device name for a volume that was attached after instance launch\. Note that the NVMe device name includes the `/dev/` prefix\.

```
[ec2-user ~]$ sudo nvme id-ctrl -v /dev/nvme1n1
NVME Identify Controller:
vid     : 0x1d0f
ssvid   : 0x1d0f
sn      : volabcdef01234567890
mn      : Amazon Elastic Block Store
...
0000: 2f 64 65 76 2f 73 64 6a 20 20 20 20 20 20 20 20 "/dev/sdf..."
```

The lsblk command lists available devices and their mount points \(if applicable\)\. This helps you determine the correct device name to use\. In this example, `/dev/nvme0n1p1` is mounted as the root device and `/dev/nvme1n1` is attached but not mounted\.

```
[ec2-user ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme1n1       259:3   0  100G  0 disk
nvme0n1       259:0   0    8G  0 disk
  nvme0n1p1   259:1   0    8G  0 part /
  nvme0n1p128 259:2   0    1M  0 part
```

## Work with NVMe EBS volumes<a name="using-nvme-ebs-volumes"></a>

To format and mount an NVMe EBS volume, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.

If you are using Linux kernel 4\.2 or later, any change you make to the volume size of an NVMe EBS volume is automatically reflected in the instance\. For older Linux kernels, you might need to detach and attach the EBS volume or reboot the instance for the size change to be reflected\. With Linux kernel 3\.19 or later, you can use the hdparm command as follows to force a rescan of the NVMe device:

```
[ec2-user ~]$ sudo hdparm -z /dev/nvme1n1
```

When you detach an NVMe EBS volume, the instance does not have an opportunity to flush the file system caches or metadata before detaching the volume\. Therefore, before you detach an NVMe EBS volume, you should first sync and unmount it\. If the volume fails to detach, you can attempt a `force-detach` command as described in [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\.

## I/O operation timeout<a name="timeout-nvme-ebs-volumes"></a>

EBS volumes attached to Nitro\-based instances use the default NVMe driver provided by the operating system\. Most operating systems specify a timeout for I/O operations submitted to NVMe devices\. The default timeout is 30 seconds and can be changed using the `nvme_core.io_timeout` boot parameter\. For most Linux kernels earlier than version 4\.6, this parameter is `nvme.io_timeout`\.

If I/O latency exceeds the value of this timeout parameter, the Linux NVMe driver fails the I/O and returns an error to the filesystem or application\. Depending on the I/O operation, your filesystem or application can retry the error\. In some cases, your filesystem might be remounted as read\-only\.

For an experience similar to EBS volumes attached to Xen instances, we recommend setting `nvme_core.io_timeout` to the highest value possible\. For current kernels, the maximum is 4294967295, while for earlier kernels the maximum is 255\. Depending on the version of Linux, the timeout might already be set to the supported maximum value\. For example, the timeout is set to 4294967295 by default for Amazon Linux AMI 2017\.09\.01 and later\.

You can verify the maximum value for your Linux distribution by writing a value higher than the suggested maximum to `/sys/module/nvme_core/parameters/io_timeout` and checking for the Numerical result out of range error when attempting to save the file\.

## `Abort` command<a name="abort-command"></a>

The `Abort` command is an NVMe Admin command that is issued to abort a specific command that was previously submitted to the controller\. This command is typically issued by the device driver to storage devices that have exceeded the I/O operation timeout threshold\. Amazon EC2 instance types that support the `Abort` command by default will abort a specific command that was previously submitted to the controller of the attached Amazon EBS device to which an `Abort` command is issued\.

The following instance types support the `Abort` command for all attached Amazon EBS volumes by default: `R5b`, `R6i`, `M6i`, `M6a`, `C6gn`, `C6i`, `X2gd`, `X2iezn`, `Im4gn`, `Is4gen`\.

Other instance types take no action when `Abort` commands are issued to attached Amazon EBS volumes\.

Amazon EBS devices with NVMe device version `1.4` or higher support the `Abort` command\.

For more information, see section **5\.1 Abort command** of the [ NVM Express Base Specification](https://nvmexpress.org/wp-content/uploads/NVM-Express-1_4-2019.06.10-Ratified.pdf)\.
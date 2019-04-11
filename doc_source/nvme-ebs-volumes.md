# Amazon EBS and NVMe<a name="nvme-ebs-volumes"></a>

EBS volumes are exposed as NVMe block devices on [Nitro\-based instances](instance-types.md#ec2-nitro-instances)\. The device names are `/dev/nvme0n1`, `/dev/nvme1n1`, and so on\. The device names that you specify in a block device mapping are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\. The block device driver can assign NVMe device names in a different order than you specified for the volumes in the block device mapping\.

**Note**  
The EBS performance guarantees stated in [Amazon EBS Product Details](https://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

The following Nitro\-based instances support NVMe instance store volumes: C5d, I3, F1, M5ad, M5d, `p3dn.24xlarge`, R5ad, R5d, and z1d\. For more information, see [NVMe SSD Volumes](ssd-instance-store.md#nvme-ssd-volumes)\.

**Topics**
+ [Install or Upgrade the NVMe Driver](#install-nvme-driver)
+ [Identifying the EBS Device](#identify-nvme-ebs-device)
+ [Working with NVMe EBS Volumes](#using-nvme-ebs-volumes)
+ [I/O Operation Timeout](#timeout-nvme-ebs-volumes)

## Install or Upgrade the NVMe Driver<a name="install-nvme-driver"></a>

The following AMIs include the required NVMe drivers:
+ Amazon Linux 2
+ Amazon Linux AMI 2018\.03
+ Ubuntu 14\.04 or later
+ Red Hat Enterprise Linux 7\.4 or later
+ SUSE Linux Enterprise Server 12 or later
+ CentOS 7 or later
+ FreeBSD 11\.1 or later

If you are using an AMI that does not include the NVMe driver, you can install the driver on your instance using the following procedure\.

**To install the NVMe driver**

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
   [ec2-user ~]$ sudo apt-get upgrade -y linux-aws
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

## Identifying the EBS Device<a name="identify-nvme-ebs-device"></a>

EBS uses single\-root I/O virtualization \(SR\-IOV\) to provide volume attachments on Nitro\-based instances using the NVMe specification\. These devices rely on standard NVMe drivers on the operating system\. These drivers typically discover attached devices by scanning the PCI bus during instance boot, and create device nodes based on the order in which the devices respond, not on how the devices are specified in the block device mapping\. In Linux, NVMe device names follow the pattern `/dev/nvme<x>n<y>`, where <x> is the enumeration order, and, for EBS, <y> is 1\. Occasionally, devices can respond to discovery in a different order in subsequent instance starts, which causes the device name to change\.

We recommend that you use stable identifiers for your EBS volumes within your instance, such as one of the following:
+ For Nitro\-based instances, the block device mappings that are specified in the Amazon EC2 console when you are attaching an EBS volume or during `AttachVolume` or `RunInstances` API calls are captured in the vendor\-specific data field of the NVMe controller identification\. With Amazon Linux AMIs later than version 2017\.09\.01, we provide a `udev` rule that reads this data and creates a symbolic link to the block\-device mapping\.
+ NVMe\-attached EBS volumes have the EBS volume ID set as the serial number in the device identification\.
+ When a device is formatted, a UUID is generated that persists for the life of the filesystem\. A device label can be specified at the same time\. For more information, see [Making an Amazon EBS Volume Available for Use on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html) and [Booting from the Wrong Volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-booting-from-wrong-volume.html)\.

**Amazon Linux AMIs**  
With Amazon Linux AMI 2017\.09\.01 or later \(including Amazon Linux 2\), you can run the ebsnvme\-id command as follows to map the NVMe device name to a volume ID and device name:

```
[ec2-user ~]$ sudo /sbin/ebsnvme-id /dev/nvme1n1
Volume ID: vol-01324f611e2463981
/dev/sdf
```

Amazon Linux also creates a symbolic link from the device name in the block device mapping \(for example, `/dev/sdf`\), to the NVMe device name\.

**Other Linux AMIs**  
With a kernel version of 4\.2 or later, you can run the nvme id\-ctrl command as follows to map an NVMe device to a volume ID\. First, install the NVMe command line package, `nvme-cli`, using the package management tools for your Linux distribution\.

The following example gets the volume ID and device name\. The device name is available through the NVMe controller vendor\-specific extension \(bytes 384:4095 of the controller identification\):

```
[ec2-user ~]$ sudo nvme id-ctrl -v /dev/nvme1n1
NVME Identify Controller:
vid     : 0x1d0f
ssvid   : 0x1d0f
sn      : vol01234567890abcdef
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

## Working with NVMe EBS Volumes<a name="using-nvme-ebs-volumes"></a>

To format and mount an NVMe EBS volume, see [Making an Amazon EBS Volume Available for Use on Linux](ebs-using-volumes.md)\.

If you are using Linux kernel 4\.2 or later, any change you make to the volume size of an NVMe EBS volume is automatically reflected in the instance\. For older Linux kernels, you might need to detach and attach the EBS volume or reboot the instance for the size change to be reflected\. With Linux kernel 3\.19 or later, you can use the hdparm command as follows to force a rescan of the NVMe device:

```
[ec2-user ~]$ sudo hdparm -z /dev/nvme1n1
```

When you detach an NVMe EBS volume, the instance does not have an opportunity to flush the file system caches or metadata before detaching the volume\. Therefore, before you detach an NVMe EBS volume, you should first sync and unmount it\. If the volume fails to detach, you can attempt a `force-detach` command as described in [Detaching an Amazon EBS Volume from an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-detaching-volume.html)\.

## I/O Operation Timeout<a name="timeout-nvme-ebs-volumes"></a>

EBS volumes attached to Nitro\-based instances use the default NVMe driver provided by the operating system\. Most operating systems specify a timeout for I/O operations submitted to NVMe devices\. The default timeout is 30 seconds and can be changed using the `nvme_core.io_timeout` boot parameter \(or the `nvme.io_timeout` boot parameter for Linux kernels before version 4\.6\)\. For testing purposes, you can also dynamically update the timeout by writing to `/sys/module/nvme_core/parameters/io_timeout` using your preferred text editor\. If I/O latency exceeds the value of this parameter, the Linux NVMe driver fails the I/O and return an error to the filesystem or application\. Depending on the I/O operation, your filesystem or application can retry the error\. In some cases, your filesystem may be remounted as read\-only\.

For an experience similar to EBS volumes attached to Xen instances, we recommend setting `nvme.io_timeout` to the highest value possible\. For current kernels, the maximum is 4294967295, while for earlier kernels the maximum is 255\. Depending on the version of Linux, the timeout might already be set to the supported maximum value\. For example, the timeout is set to 4294967295 by default for Amazon Linux AMI 2017\.09\.01 and later\.

You can verify the maximum value for your Linux distribution by writing a value higher than the suggested maximum to `/sys/module/nvme_core/parameters/io_timeout` and checking for the `Numerical result out of range` error when attempting to save the file\.
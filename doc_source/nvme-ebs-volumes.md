# Amazon EBS and NVMe<a name="nvme-ebs-volumes"></a>

EBS volumes are exposed as NVMe block devices on [Nitro\-based instances](instance-types.md#ec2-nitro-instances)\. The device names are `/dev/nvme0n1`, `/dev/nvme1n1`, and so on\. The device names that you specify in a block device mapping are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\. The block device driver can assign NVMe device names in a different order than you specified for the volumes in the block device mapping\.

**Note**  
The EBS performance guarantees stated in [Amazon EBS Product Details](https://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

Some of these instance types also support NVMe instance store volumes\. For more information, see [NVMe SSD Volumes](ssd-instance-store.md#nvme-ssd-volumes)\.

## Identifying the EBS Device<a name="identify-nvme-ebs-device"></a>

**Amazon Linux AMIs**  
EBS uses single\-root I/O virtualization \(SR\-IOV\) to provide volume attachments on Nitro\-based instances following the NVMe specification\. These devices rely on standard drivers provided by the operating system\. 

NVMe drivers typically discover attached devices by scanning the PCI bus during instance boot and create device nodes based on the order in which the devices respond\. This differs from the block\-device mapping that is performed during `RunInstances` or `AttachVolume` API call\. In Linux, NVMe devices names follow the pattern: `/dev/nvme<x>n<y>`, where `x` is the enumeration order, and, for EBS, `y` is 1\. Devices may respond to discovery in a different order in subsequent instance starts, causing the device name to change\.

We recommend that you track your EBS volumes with one of the following stable identifiers:
+ For Nitro\-based instances, the block device mappings that are specified in the Amazon EC2 console when you are attaching an EBS volume or during `AttachVolume` or `RunInstances` API calls are captured in the vendor\-specific data field of the NVMe controller identification\. Amazon Linux AMIs later than version 2017\.09\.01 provide a `udev` rule that reads this data and creates a symbolic link to the block\-device mapping\.
+ NVMe\-attached EBS volumes have the EBS volume ID set as the serial number in the device identification\.
+ When a device is formatted, a UUID is generated that persists for the life of the filesystem\. A device label can be specified at the same time\. For more information, see [Making an Amazon EBS Volume Available for Use on Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html) and [Booting from the Wrong Volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-booting-from-wrong-volume.html)\.

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

If you are using Linux kernel 4\.2 or later, any change you make to the volume size of an NVMe EBS volume is automatically reflected in the instance\. For older Linux kernels, you might need to detach and attach the EBS volume or reboot the instance for the size change to be reflected\. With Linux kernel 3\.19 or later, you can use the hdparm command as follows to force a rescan of the NVMe device:

```
[ec2-user ~]$ sudo hdparm -z /dev/nvme1n1
```

Before you detach an NVMe EBS volume, you should sync and unmount it\. When you detach an NVMe EBS volume, the force option is implicitly enabled\. Therefore, the instance does not have an opportunity to flush file system caches or metadata before detaching the volume\.

## I/O Operation Timeout<a name="timeout-nvme-ebs-volumes"></a>

EBS volumes attached to Nitro\-based system instances use the default NVMe driver provided by the operating system\. Most operating systems specify a timeout for I/O operations submitted to NVMe devices\. The default timeout is 30 seconds and can be changed using the `nvme_core.io_timeout` boot parameter \(or the `nvme.io_timeout` boot parameter for Linux kernels before version 4\.6\)\. If I/O latency exceeds the value of this parameter, the Linux NVMe driver fails the I/O and return an error to the filesystem or application\. Depending on the I/O operation, your filesystem or application may retry the error\. In some cases, your filesystem may be remounted as read\-only\. 

For an experience similar to EBS volumes attached to Xen instances, we recommend setting this timeout to the highest value possible\. For current kernels, the maximum is 4294967295, while for earlier kernels the maximum is 255\.The `nvme.io_timeout` boot parameter is already set to the maximum value for the following Linux distributions: 
+ Amazon Linux AMI 2017\.09\.01 or later
+ Canonical 4\.4\.0\-1041 or later
+ SLES 12 SP2 \(4\.4 kernel\) or later
+ RHEL 7\.5 \(3\.10\.0\-862 kernel\) or later
# Amazon EBS and NVMe<a name="nvme-ebs-volumes"></a>

With the following instances, EBS volumes are exposed as NVMe block devices: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, and z1d\. The device names are `/dev/nvme0n1`, `/dev/nvme1n1`, and so on\. The device names that you specify in a block device mapping are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\.

**Note**  
The EBS performance guarantees stated in [Amazon EBS Product Details](https://aws.amazon.com/ebs/details/) are valid regardless of the block\-device interface\.

## Identifying the EBS Device<a name="identify-nvme-ebs-device"></a>

**Amazon Linux AMIs**  
With Amazon Linux AMI 2017\.09\.01 or later \(including Amazon Linux 2\), you can run the ebsnvme\-id command as follows to map the NVMe device name to a volume ID and device name\.

```
[ec2-user ~]$ sudo /sbin/ebsnvme-id /dev/nvme1n1
Volume ID: vol-01324f611e2463981
/dev/sdf
```

Amazon Linux also creates a symbolic link from the device name in the block device mapping \(for example, `/dev/sdf`\), to the NVMe device name\.

**Other Linux AMIs**  
With a kernel version of 4\.2 or later, you can run the nvme id\-ctrl command as follows to map an NVMe device to a volume ID\. First, install the NVMe command line package, `nvme-cli`, using the package management tools for your Linux distribution\.

The following example gets the volume ID and device name\. The device name is available through the NVMe controller vendor specific extension \(bytes 384:4095 of the controller identification\)\.

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

NVMe EBS volumes use the default NVMe driver provided by the operating system\. Most operating systems specify a timeout for I/O operations submitted to NVMe devices\. The default timeout is 30 seconds and can be changed using the `nvme_core.io_timeout` boot parameter \(or the `nvme.io_timeout` boot parameter for Linux kernels prior to version 4\.6\)\. For an experience similar to EBS volumes attached to Xen instances, we recommend setting this to the highest value possible\. For Amazon Linux AMI 2017\.09\.01 \(or greater\), and for Linux kernels with version 4\.15 or greater, the maximum is 4294967295\. Prior to Linux 4\.15, the maximum is 255 seconds\. If you are using a current version of the Amazon Linux AMI, we have already increased the timeout\.

With Linux kernel 4\.14 and later, you can also configure the number of times that I/O operations can be retried\. The default is five retries\. You can configure a different value using the `nvme_core.max_retries` kernel boot parameter, or at runtime using the following command:

```
[ec2-user ~]$ echo 10 > /sys/module/nvme_core/parameters/max_retries
```
# Extending a Linux File System After Resizing a Volume<a name="recognize-expanded-volume-linux"></a>

After you increase the size of an EBS volume, you must use file system–specific commands to extend the file system to the larger size\. You can resize the file system as soon as the volume enters the `optimizing` state\.

**Important**  
Before extending a file system that contains valuable data, it is a best practice to create a snapshot of the volume,in case you need to roll back your changes\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

For information about extending a Windows file system, see [Extending a Windows File System after Resizing a Volume](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/recognize-expanded-volume-windows.html) in the *Amazon EC2 User Guide for Windows Instances*\.

For the following tasks, suppose that you have resized the boot volume of an instance from 8 GB to 16 GB and an additional volume from 8 GB to 30 GB\.

**Topics**
+ [Identifying the File System for a Volume](#identify-linux-file-system)
+ [Extending a Partition \(If Needed\)](#extend-linux-volume-partition)
+ [Extending the File System](#extend-linux-file-system)

## Identifying the File System for a Volume<a name="identify-linux-file-system"></a>

To verify the file system in use for each volume on your instance, [connect to your instance](AccessingInstances.md) and run the file \-s command\.

**Example Example: File Systems on a Nitro\-based Instance**  
The following example shows a [Nitro\-based instance](instance-types.md#ec2-nitro-instances) that has a boot volume with an XFS file system and an additional volume with an XFS file system\.  

```
[ec2-user ~]$ sudo file -s /dev/nvme?n*
/dev/nvme0n1:     x86 boot sector ...
/dev/nvme0n1p1:   SGI XFS filesystem data ...
/dev/nvme0n1p128: data
/dev/nvme1n1:     SGI XFS filesystem data ...
```

**Example Example: File Systems on a T2 Instance**  
The following example shows a T2 instance that has a boot volume with an ext4 file system and an additional volume with an XFS file system\.  

```
[ec2-user ~]$ sudo file -s /dev/xvd*
/dev/xvda:  DOS/MBR boot sector ..
/dev/xvda1: Linux rev 1.0 ext4 filesystem data ...
/dev/xvdf:  SGI XFS filesystem data ...
```

## Extending a Partition \(If Needed\)<a name="extend-linux-volume-partition"></a>

Your EBS volume might have a partition that contains the file system and data\. Increasing the size of a volume does not increase the size of the partition\. Before you extend the file system on a resized volume, check whether the volume has a partition that must be extended to the new size of the volume\.

Use the lsblk command to display information about the block devices attached to your instance\. If a resized volume has a partition and the partition does not reflect the new size of the volume, use the growpart command to extend the partition\. For information about extending an LVM partition, refer to the documentation for LVM\.

**Example Example: Partitions on a Nitro\-based Instance**  
The following example shows the volumes on a Nitro\-based instance:  

```
[ec2-user ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme1n1       259:0    0  30G  0 disk /data
nvme0n1       259:1    0  16G  0 disk
└─nvme0n1p1   259:2    0   8G  0 part /
└─nvme0n1p128 259:3    0   1M  0 part
```
+ The root volume, `/dev/nvme0n1`, has a partition, `/dev/nvme0n1p1`\. While the size of the root volume reflects the new size, 16 GB, the size of the partition reflects the original size, 8 GB, and must be extended before you can extend the file system\.
+ The volume `/dev/nvme1n1` has no partitions\. The size of the volume reflects the new size, 30 GB\.
To extend the partition on the root volume, use the following growpart command\. Notice that there is a space between the device name and the partition number\.  

```
[ec2-user ~]$ sudo growpart /dev/nvme0n1 1
```
You can verify that the partition reflects the increased volume size by using the lsblk command again\.  

```
[ec2-user ~]$ lsblk
NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
nvme1n1       259:0    0  30G  0 disk /data
nvme0n1       259:1    0  16G  0 disk
└─nvme0n1p1   259:2    0  16G  0 part /
└─nvme0n1p128 259:3    0   1M  0 part
```

**Example Example: Partitions on a T2 Instance**  
The following example shows the volumes on a T2 instance:  

```
[ec2-user ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  16G  0 disk
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  30G  0 disk
└─xvdf1 202:81   0   8G  0 part /data
```
+ The root volume, `/dev/xvda`, has a partition, `/dev/xvda1`\. While the size of the volume is 16 GB, the size of the partition is still 8 GB and must be extended\.
+ The volume `/dev/xvdf` has a partition, `/dev/xvdf1`\. While the size of the volume is 30G, the size of the partition is still 8 GB and must be extended\.
To extend the partition on each volume, use the following growpart commands\. Note that there is a space between the device name and the partition number\.  

```
[ec2-user ~]$ sudo growpart /dev/xvda 1
[ec2-user ~]$ sudo growpart /dev/xvdf 1
```
You can verify that the partitions reflect the increased volume size by using the lsblk command again\.  

```
[ec2-user ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  16G  0 disk
└─xvda1 202:1    0  16G  0 part /
xvdf    202:80   0  30G  0 disk
└─xvdf1 202:81   0  30G  0 part /data
```

## Extending the File System<a name="extend-linux-file-system"></a>

Use a file system\-specific command to resize each file system to the new volume capacity\. For a file system other than the examples shown here, refer to the documentation for the file system for instructions\.

**Example Example: Extend an ext2, ext3, or ext4 file system**  
Use the df \-h command to verify the size of the file system for each volume\. In this example, both `/dev/xvda1` and `/dev/xvdf` reflect the original size of the volumes, 8 GB\.  

```
[ec2-user ~]$ df -h
Filesystem       Size  Used Avail Use% Mounted on
/dev/xvda1       8.0G  1.9G  6.2G  24% /
/dev/xvdf1       8.0G   45M  8.0G   1% /data
...
```
Use the resize2fs command to extend the file system on each volume\.  

```
[ec2-user ~]$ sudo resize2fs /dev/xvda1
[ec2-user ~]$ sudo resize2fs /dev/xvdf1
```
You can verify that each file system reflects the increased volume size by using the df \-h command again\.  

```
[ec2-user ~]$ df -h
Filesystem       Size  Used Avail Use% Mounted on
/dev/xvda1        16G  1.9G  6.2G  12% /
/dev/xvdf1        30G   45M  8.0G   1% /data
...
```

**Example Example: Extend an XFS file system**  
Use the df \-h command to verify the size of the file system for each volume\. In this example, each file system reflects the original volume size, 8 GB\.  

```
[ec2-user ~]$ df -h
Filesystem       Size  Used Avail Use% Mounted on
/dev/nvme0n1p1   8.0G  1.6G  6.5G  20% /
/dev/nvme1n1     8.0G   33M  8.0G   1% /data
...
```
To extend the XFS file system, install the XFS tools as follows, if they are not already installed\.  

```
[ec2-user ~]$ sudo yum install xfsprogs
```
Use the xfs\_growfs command to extend the file system on each volume\. In this example, `/` and `/data` are the volume mount points shown in the output for df \-h\.  

```
[ec2-user ~]$ sudo xfs_growfs -d /
[ec2-user ~]$ sudo xfs_growfs -d /data
```
You can verify that each file system reflects the increased volume size by using the df \-h command again\.  

```
[ec2-user ~]$ df -h
Filesystem       Size  Used Avail Use% Mounted on
/dev/nvme0n1p1    16G  1.6G   15G  10% /
/dev/nvme1n1      30G   33M   30G   1% /data
...
```
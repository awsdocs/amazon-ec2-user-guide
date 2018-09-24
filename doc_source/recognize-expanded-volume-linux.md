# Extending a Linux File System after Resizing the Volume<a name="recognize-expanded-volume-linux"></a>

Use a file system–specific command to resize the file system to the larger size of the new volume\. These commands work even if the volume to extend is the root volume\. For ext2, ext3, and ext4 file systems, this command is resize2fs\. For XFS file systems, this command is xfs\_growfs\. For other file systems, refer to the specific documentation for those file systems for instructions on extending them\.

If you are unsure of which file system you are using, you can use the file \-s command to list the file system data for a device\. The following example shows a Linux ext4 file system and an SGI XFS file system\.

```
[ec2-user ~]$ sudo file -s /dev/xvd*
/dev/xvda1: Linux rev 1.0 ext4 filesystem data ...
/dev/xvdf:  SGI XFS filesystem data ...
```

**Note**  
If the volume you are extending has been partitioned, you need to increase the size of the partition before you can resize the file system\. You can also allocate additional partitions at this time\. For more information, see [Expanding a Linux Partition](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/expand-linux-partition.html)\.

You can begin resizing the file system as soon as the volume enters the `Optimizing` state\.

**Important**  
Before extending a file system that contains valuable data, it is a best practice to create a snapshot of the volume that contains it in case you need to roll back your changes\. For information about EBS snapshots, see [Creating an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html)\.

For information about extending a Windows file system, see [Extending a Windows File System after Resizing the Volume](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/recognize-expanded-volume-windows.html) in the *Amazon EC2 User Guide for Windows Instances*\.<a name="procedure_partition_check"></a>

**To check if your volume partition needs resizing**
+ Use the lsblk command to list the block devices attached to your instance\. The example below shows three volumes: `/dev/xvda`, `/dev/xvdb`, and `/dev/xvdf`\.

  ```
  [ec2-user ~]$ lsblk
  NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
  xvda    202:0    0  30G  0 disk
  └─xvda1 202:1    0  30G  0 part /
  xvdb    202:16   0  30G  0 disk /mnt
  xvdf    202:80   0  35G  0 disk
  └─xvdf1 202:81   0   8G  0 part
  ```

  The root volume, `/dev/xvda1`, is a partition on `/dev/xvda`\. Notice that they are both 30 GiB in size\. In this case, the partition occupies all of the room on the device, so it does not need resizing\.

  The volume `/dev/xvdb` is not partitioned at all, so it does not need resizing\.

  However, `/dev/xvdf1` is an 8\-GiB partition on a 35\-GiB device and there are no other partitions on the volume\. In this case, the partition must be resized in order to use the remaining space on the volume\. After you resize the partition, you can extend the file system to occupy all of the space on the partition\.

**To resize a Linux partition**

1. Log in to your Linux instance using an SSH client\. For more information about connecting to a Linux instance, see [Connecting to Your Linux Instance Using SSH](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)\.

1. Use the df \-h command to report the existing disk space usage on the file system\. In this new example, `/dev/xvda1` block device has already been expanded to 35 GiB, but the operating system still sees only an original 8 GiB ext4 file system\. Similarly, the `/dev/xvdf` block device has been expanded to 35 GiB, but the operating system still only sees an original 1 GiB XFS file system\.

   ```
   [ec2-user ~]$ df -h
   Filesystem            Size  Used Avail Use% Mounted on
   /dev/xvda1            8.0G  943M  6.9G  12% /
   tmpfs                 1.9G     0  1.9G   0% /dev/shm
   /dev/xvdf            1014M   33M  982M   4% /mnt
   ```

1. Expand the modified partition using growpart \(and note the unusual syntax of separating the device name from the partition number\):

   ```
   $sudo growpart /dev/xvdf 1
   CHANGED: disk=/dev/xvdf partition=1: start=4096 old: size=16773086,end=16777182 new: size=73396190,end=73400286
   ```

   A look at the lsblk output confirms that the partition `/dev/xvdf1` now fills the available space on the volume `/dev/xvdf`:

   ```
   [ec2-user ~]$ lsblk
   NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   ...
   xvdf    202:80   0  35G  0 disk
   └─xvdf1 202:81   0  35G  0 part
   ```

**To extend a Linux file system**

1. Use a file system\-specific command to resize each file system to the new volume capacity\. 

   For a Linux ext2, ext3, or ext4 file system, use the following command, substituting the device name to extend:

   ```
   [ec2-user ~]$ sudo resize2fs /dev/xvdf1
   resize2fs 1.42.3 (14-May-2012)
   old_desc_blocks = 1, new_desc_blocks = 3
   The filesystem on /dev/xvdf1 is now 9174523 blocks long.
   ```

   For an XFS file system, first install the XFS userspace tools:

   ```
   [ec2-user ~]$ sudo yum install xfsprogs
   ```

   Then use the following command, substituting the mount point of the file system \(XFS file systems must be mounted to resize them\):

   ```
   [ec2-user ~]$ sudo xfs_growfs -d /mnt
   meta-data=/dev/xvdf              isize=256    agcount=4, agsize=65536 blks
            =                       sectsz=512   attr=2
   data     =                       bsize=4096   blocks=262144, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 262144 to 26214400
   ```
**Note**  
If you receive an xfsctl failed: Cannot allocate memory error, you may need to update the Linux kernel on your instance\. For more information, refer to your specific operating system documentation\.  
If you receive a The filesystem is already *nnnnnnn* blocks long\. Nothing to do\! error, see [Expanding a Linux Partition](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/expand-linux-partition.html)\.

1. Use the df \-h command to report the existing file system disk space usage, in this example showing 70 GiB on the ext4 file system and 100 GiB on the XFS file system:

   ```
   # df -h
   Filesystem            Size  Used Avail Use% Mounted on
   /dev/xvda1             70G  951M   69G   2% /
   tmpfs                 1.9G     0  1.9G   0% /dev/shm
   /dev/xvdf             100G   45M  100G   1% /mnt
   ```

**Tip**  
If the increased available space on your volume remains invisible to the system, try re\-initializing the volume as described in [Initializing Amazon EBS Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-initialize.html)\.
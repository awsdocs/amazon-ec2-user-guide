# Instance Store Swap Volumes<a name="instance-store-swap-volumes"></a>

Swap space in Linux can be used when a system requires more memory than it has been physically allocated\. When swap space is enabled, Linux systems can swap infrequently used memory pages from physical memory to swap space \(either a dedicated partition or a swap file in an existing file system\) and free up that space for memory pages that require high\-speed access\.

**Note**  
Using swap space for memory paging is not as fast or efficient as using RAM\. If your workload is regularly paging memory into swap space, you should consider migrating to a larger instance type with more RAM\. For more information, see [Resizing Your Instance](ec2-instance-resize.md)\.

The `c1.medium` and `m1.small` instance types have a limited amount of physical memory to work with, and they are given a 900 MiB swap volume at launch time to act as virtual memory for Linux AMIs\. Although the Linux kernel sees this swap space as a partition on the root device, it is actually a separate instance store volume, regardless of your root device type\.

Amazon Linux AMIs automatically enable and use this swap space, but your AMI may require some additional steps to recognize and use this swap space\. To see if your instance is using swap space, you can use the swapon \-s command\.

```
[ec2-user ~]$ swapon -s
Filename                                Type            Size    Used    Priority
/dev/xvda3                              partition       917500  0       -1
```

The above instance has a 900 MiB swap volume attached and enabled\. If you don't see a swap volume listed with this command, you may need to enable swap space for the device\. Check your available disks using the lsblk command\.

```
[ec2-user ~]$ lsblk
NAME  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
xvda1 202:1    0    8G  0 disk /
xvda3 202:3    0  896M  0 disk
```

Here, the swap volume `xvda3` is available to the instance, but it is not enabled \(notice that the `MOUNTPOINT` field is empty\)\. You can enable the swap volume with the swapon command\.

**Note**  
You must prepend `/dev/` to the device name listed by lsblk\. Your device may be named differently, such as `sda3`, `sde3`, or `xvde3`\. Use the device name for your system in the command below\.

```
[ec2-user ~]$ sudo swapon /dev/xvda3
```

Now the swap space should show up in lsblk and swapon \-s output\.

```
[ec2-user ~]$ lsblk
NAME  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
xvda1 202:1    0    8G  0 disk /
xvda3 202:3    0  896M  0 disk [SWAP]
[ec2-user ~]$ swapon -s
Filename                                Type            Size    Used    Priority
/dev/xvda3                              partition       917500  0       -1
```

You also need to edit your `/etc/fstab` file so that this swap space is automatically enabled at every system boot\.

```
[ec2-user ~]$ sudo vim /etc/fstab
```

Append the following line to your `/etc/fstab` file \(using the swap device name for your system\):

```
/dev/xvda3       none    swap    sw  0       0
```

**To use an instance store volume as swap space**

Any instance store volume can be used as swap space\. For example, the `m3.medium` instance type includes a 4 GB SSD instance store volume that is appropriate for swap space\. If your instance store volume is much larger \(for example, 350 GB\), you may consider partitioning the volume with a smaller swap partition of 4\-8 GB and the rest for a data volume\.
**Note**  
This procedure applies only to instance types that support instance storage\. For a list of supported instance types, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\.

1. <a name="step_swap_start"></a>List the block devices attached to your instance to get the device name for your instance store volume\.

   ```
   [ec2-user ~]$ lsblk -p
   NAME       MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   /dev/xvdb  202:16   0   4G  0 disk /media/ephemeral0
   /dev/xvda1 202:1    0   8G  0 disk /
   ```

   In this example, the instance store volume is `/dev/xdvb`\. Because this is an Amazon Linux instance, the instance store volume is formatted and mounted at `/media/ephemeral0`; not all Linux operating systems do this automatically\.

1. \(Optional\) If your instance store volume is mounted \(it lists a `MOUNTPOINT` in the lsblk command output\), unmount it with the following command\.

   ```
   [ec2-user ~]$ sudo umount /dev/xvdb
   ```

1. <a name="step_mkswap"></a>Set up a Linux swap area on the device with the mkswap command\.

   ```
   [ec2-user ~]$ sudo mkswap /dev/xvdb
   mkswap: /dev/xvdb: warning: wiping old ext3 signature.
   Setting up swapspace version 1, size = 4188668 KiB
   no label, UUID=b4f63d28-67ed-46f0-b5e5-6928319e620b
   ```

1. Enable the new swap space\.

   ```
   [ec2-user ~]$ sudo swapon /dev/xvdb
   ```

1. <a name="step_swap_enable"></a>Verify that the new swap space is being used\.

   ```
   [ec2-user ~]$ swapon -s
   Filename				Type		Size	Used	Priority
   /dev/xvdb                              	partition	4188668	0	-1
   ```

1. Edit your `/etc/fstab` file so that this swap space is automatically enabled at every system boot\.

   ```
   [ec2-user ~]$ sudo vim /etc/fstab
   ```

   If your `/etc/fstab` file has an entry for `/dev/xvdb` \(or `/dev/sdb`\) change it to match the line below; if it does not have an entry for this device, append the following line to your `/etc/fstab` file \(using the swap device name for your system\):

   ```
   /dev/xvdb       none    swap    sw  0       0
   ```
**Important**  
Instance store volume data is lost when an instance is stopped; this includes the instance store swap space formatting created in [Step 3](#step_mkswap)\. If you stop and restart an instance that has been configured to use instance store swap space, you must repeat [Step 1](#step_swap_start) through [Step 5](#step_swap_enable) on the new instance store volume\.
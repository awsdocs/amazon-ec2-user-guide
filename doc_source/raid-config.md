# RAID Configuration on Linux<a name="raid-config"></a>

With Amazon EBS, you can use any of the standard RAID configurations that you can use with a traditional bare metal server, as long as that particular RAID configuration is supported by the operating system for your instance\. This is because all RAID is accomplished at the software level\. For greater I/O performance than you can achieve with a single volume, RAID 0 can stripe multiple volumes together; for on\-instance redundancy, RAID 1 can mirror two volumes together\.

Amazon EBS volume data is replicated across multiple servers in an Availability Zone to prevent the loss of data from the failure of any single component\. This replication makes Amazon EBS volumes ten times more reliable than typical commodity disk drives\. For more information, see [Amazon EBS Availability and Durability](https://aws.amazon.com/ebs/details/#Amazon_EBS_Availability_and_Durability) in the Amazon EBS product detail pages\.

**Note**  
You should avoid booting from a RAID volume\. Grub is typically installed on only one device in a RAID array, and if one of the mirrored devices fails, you may be unable to boot the operating system\.

If you need to create a RAID array on a Windows instance, see [RAID Configuration on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/raid-config.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [RAID Configuration Options](#raid-config-options)
+ [Creating a RAID Array on Linux](#linux-raid)
+ [Creating Snapshots of Volumes in a RAID Array](#ebs-snapshots-raid-array)

## RAID Configuration Options<a name="raid-config-options"></a>

The following table compares the common RAID 0 and RAID 1 options\.


| Configuration | Use | Advantages | Disadvantages | 
| --- | --- | --- | --- | 
|  RAID 0  |  When I/O performance is more important than fault tolerance; for example, as in a heavily used database \(where data replication is already set up separately\)\.  |  I/O is distributed across the volumes in a stripe\. If you add a volume, you get the straight addition of throughput\.  |  Performance of the stripe is limited to the worst performing volume in the set\. Loss of a single volume results in a complete data loss for the array\.  | 
|  RAID 1  |  When fault tolerance is more important than I/O performance; for example, as in a critical application\.  |  Safer from the standpoint of data durability\.  |  Does not provide a write performance improvement; requires more Amazon EC2 to Amazon EBS bandwidth than non\-RAID configurations because the data is written to multiple volumes simultaneously\.   | 

**Important**  
RAID 5 and RAID 6 are not recommended for Amazon EBS because the parity write operations of these RAID modes consume some of the IOPS available to your volumes\. Depending on the configuration of your RAID array, these RAID modes provide 20\-30% fewer usable IOPS than a RAID 0 configuration\. Increased cost is a factor with these RAID modes as well; when using identical volume sizes and speeds, a 2\-volume RAID 0 array can outperform a 4\-volume RAID 6 array that costs twice as much\. 

Creating a RAID 0 array allows you to achieve a higher level of performance for a file system than you can provision on a single Amazon EBS volume\. A RAID 1 array offers a "mirror" of your data for extra redundancy\. Before you perform this procedure, you need to decide how large your RAID array should be and how many IOPS you want to provision\.

The resulting size of a RAID 0 array is the sum of the sizes of the volumes within it, and the bandwidth is the sum of the available bandwidth of the volumes within it\. The resulting size and bandwidth of a RAID 1 array is equal to the size and bandwidth of the volumes in the array\. For example, two 500 GiB Amazon EBS `io1` volumes with 4,000 provisioned IOPS each will create a 1000 GiB RAID 0 array with an available bandwidth of 8,000 IOPS and 1,000 MiB/s of throughput or a 500 GiB RAID 1 array with an available bandwidth of 4,000 IOPS and 500 MiB/s of throughput\.

This documentation provides basic RAID setup examples\. For more information about RAID configuration, performance, and recovery, see the Linux RAID Wiki at [https://raid\.wiki\.kernel\.org/index\.php/Linux\_Raid](https://raid.wiki.kernel.org/index.php/Linux_Raid)\.

## Creating a RAID Array on Linux<a name="linux-raid"></a>

Use the following procedure to create the RAID array\. Note that you can get directions for Windows instances from [Creating a RAID Array on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/raid-config.html#windows-raid) in the *Amazon EC2 User Guide for Windows Instances*\.

**To create a RAID array on Linux**

1. Create the Amazon EBS volumes for your array\. For more information, see [Creating an Amazon EBS Volume](ebs-creating-volume.md)\.
**Important**  
Create volumes with identical size and IOPS performance values for your array\. Make sure you do not create an array that exceeds the available bandwidth of your EC2 instance\. For more information, see [Amazon EC2 Instance Configuration](ebs-ec2-config.md)\.

1. Attach the Amazon EBS volumes to the instance that you want to host the array\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. Use the mdadm command to create a logical RAID device from the newly attached Amazon EBS volumes\. Substitute the number of volumes in your array for *number\_of\_volumes* and the device names for each volume in the array \(such as `/dev/xvdf`\) for *device\_name*\. You can also substitute *MY\_RAID* with your own unique name for the array\.
**Note**  
You can list the devices on your instance with the lsblk command to find the device names\.

   \(RAID 0 only\) To create a RAID 0 array, execute the following command \(note the `--level=0` option to stripe the array\):

   ```
   [ec2-user ~]$ sudo mdadm --create --verbose /dev/md0 --level=0 --name=MY_RAID --raid-devices=number_of_volumes device_name1 device_name2
   ```

   \(RAID 1 only\) To create a RAID 1 array, execute the following command \(note the `--level=1` option to mirror the array\):

   ```
   [ec2-user ~]$ sudo mdadm --create --verbose /dev/md0 --level=1 --name=MY_RAID --raid-devices=number_of_volumes device_name1 device_name2
   ```

1.  Allow time for the RAID array to initialize and synchronize\. You can track the progress of these operations with the following command:

   ```
   [ec2-user ~]$ sudo cat /proc/mdstat
   ```

   The following is example output:

   ```
   Personalities : [raid1] 
   md0 : active raid1 xvdg[1] xvdf[0]
         20955008 blocks super 1.2 [2/2] [UU]
         [=========>...........]  resync = 46.8% (9826112/20955008) finish=2.9min speed=63016K/sec
   ```

   In general, you can display detailed information about your RAID array with the following command:

   ```
   [ec2-user ~]$ sudo mdadm --detail /dev/md0
   ```

   The following is example output:

   ```
   /dev/md0:
           Version : 1.2
     Creation Time : Mon Jun 27 11:31:28 2016
        Raid Level : raid1
        Array Size : 20955008 (19.98 GiB 21.46 GB)
     Used Dev Size : 20955008 (19.98 GiB 21.46 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent
   
       Update Time : Mon Jun 27 11:37:02 2016
             State : clean 
   ...
   ...
   ...
   
       Number   Major   Minor   RaidDevice State
          0     202       80        0      active sync   /dev/sdf
          1     202       96        1      active sync   /dev/sdg
   ```

1. Create a file system on your RAID array, and give that file system a label to use when you mount it later\. For example, to create an ext4 file system with the label *MY\_RAID*, execute the following command:

   ```
   [ec2-user ~]$ sudo mkfs.ext4 -L MY_RAID /dev/md0
   ```

   Depending on the requirements of your application or the limitations of your operating system, you can use a different file system type, such as ext3 or XFS \(consult your file system documentation for the corresponding file system creation command\)\.

1. To ensure that the RAID array is reassembled automatically on boot, create a configuration file to contain the RAID information:

   ```
   [ec2-user ~]$ sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
   ```
**Note**  
If you are using a Linux distribution other than Amazon Linux, this file may need to be placed in different location\. For more information, consult man mdadm\.conf on your Linux system\.\.

1. Create a new ramdisk image to properly preload the block device modules for your new RAID configuration:

   ```
   [ec2-user ~]$ sudo dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
   ```

1. Create a mount point for your RAID array\.

   ```
   [ec2-user ~]$ sudo mkdir -p /mnt/raid
   ```

1. Finally, mount the RAID device on the mount point that you created:

   ```
   [ec2-user ~]$ sudo mount LABEL=MY_RAID /mnt/raid
   ```

   Your RAID device is now ready for use\.

1. \(Optional\) To mount this Amazon EBS volume on every system reboot, add an entry for the device to the `/etc/fstab` file\.

   1. Create a backup of your `/etc/fstab` file that you can use if you accidentally destroy or delete this file while you are editing it\.

      ```
      [ec2-user ~]$ sudo cp /etc/fstab /etc/fstab.orig
      ```

   1. Open the `/etc/fstab` file using your favorite text editor, such as nano or vim\.

   1. Comment out any lines starting with "`UUID=`" and, at the end of the file, add a new line for your RAID volume using the following format:

      ```
      device_label mount_point file_system_type fs_mntops fs_freq fs_passno
      ```

      The last three fields on this line are the file system mount options, the dump frequency of the file system, and the order of file system checks done at boot time\. If you don't know what these values should be, then use the values in the example below for them \(`defaults,nofail 0 2)`\. For more information about `/etc/fstab` entries, see the fstab manual page \(by entering man fstab on the command line\)\. For example, to mount the ext4 file system on the device with the label MY\_RAID at the mount point `/mnt/raid`, add the following entry to `/etc/fstab`\.
**Note**  
If you ever intend to boot your instance without this volume attached \(for example, so this volume could move back and forth between different instances\), you should add the `nofail` mount option that allows the instance to boot even if there are errors in mounting the volume\. Debian derivatives, such as Ubuntu, must also add the `nobootwait` mount option\.

      ```
      LABEL=MY_RAID       /mnt/raid   ext4    defaults,nofail        0       2
      ```

   1. After you've added the new entry to `/etc/fstab`, you need to check that your entry works\. Run the sudo mount \-a command to mount all file systems in `/etc/fstab`\.

      ```
      [ec2-user ~]$ sudo mount -a
      ```

      If the previous command does not produce an error, then your `/etc/fstab` file is OK and your file system will mount automatically at the next boot\. If the command does produce any errors, examine the errors and try to correct your `/etc/fstab`\.
**Warning**  
Errors in the `/etc/fstab` file can render a system unbootable\. Do not shut down a system that has errors in the `/etc/fstab` file\.

   1. \(Optional\) If you are unsure how to correct `/etc/fstab` errors, you can always restore your backup `/etc/fstab` file with the following command\.

      ```
      [ec2-user ~]$ sudo mv /etc/fstab.orig /etc/fstab
      ```

## Creating Snapshots of Volumes in a RAID Array<a name="ebs-snapshots-raid-array"></a>

If you want to back up the data on the EBS volumes in a RAID array using snapshots, you must ensure that the snapshots are consistent\. This is because the snapshots of these volumes are created independently\. To restore EBS volumes in a RAID array from snapshots that are out of sync would degrade the integrity of the array\.

To create a consistent set of snapshots for your RAID array, use [EBS multi\-volume snapshots](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshots.html)\. Multi\-volume snapshots allow you to take point\-in\-time, data coordinated, and crash\-consistent snapshots across multiple EBS volumes attached to an EC2 instance\. You do not have to stop your instance to coordinate between volumes to ensure consistency because snapshots are automatically taken across multiple EBS volumes\. For more information, see the steps for creating multi\-volume snapshots under [Creating Amazon EBS Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-snapshot.html)\. 
# RAID configuration on Linux<a name="raid-config"></a>

With Amazon EBS, you can use any of the standard RAID configurations that you can use with a traditional bare metal server, as long as that particular RAID configuration is supported by the operating system for your instance\. This is because all RAID is accomplished at the software level\. 

Amazon EBS volume data is replicated across multiple servers in an Availability Zone to prevent the loss of data from the failure of any single component\. This replication makes Amazon EBS volumes ten times more reliable than typical commodity disk drives\. For more information, see [Amazon EBS Availability and Durability](https://aws.amazon.com/ebs/details/#Amazon_EBS_Availability_and_Durability) in the Amazon EBS product detail pages\.

**Note**  
You should avoid booting from a RAID volume\. Grub is typically installed on only one device in a RAID array, and if one of the mirrored devices fails, you may be unable to boot the operating system\.

If you need to create a RAID array on a Windows instance, see [RAID configuration on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/raid-config.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [RAID configuration options](#raid-config-options)
+ [Create a RAID 0 array on Linux](#linux-raid)
+ [Create snapshots of volumes in a RAID array](#ebs-snapshots-raid-array)

## RAID configuration options<a name="raid-config-options"></a>

Creating a RAID 0 array allows you to achieve a higher level of performance for a file system than you can provision on a single Amazon EBS volume\. Use RAID 0 when I/O performance is of the utmost importance\. With RAID 0, I/O is distributed across the volumes in a stripe\. If you add a volume, you get the straight addition of throughput and IOPS\. However, keep in mind that performance of the stripe is limited to the worst performing volume in the set, and that the loss of a single volume in the set results in a complete data loss for the array\.

The resulting size of a RAID 0 array is the sum of the sizes of the volumes within it, and the bandwidth is the sum of the available bandwidth of the volumes within it\. For example, two 500 GiB `io1` volumes with 4,000 provisioned IOPS each create a 1000 GiB RAID 0 array with an available bandwidth of 8,000 IOPS and 1,000 MiB/s of throughput\.

**Important**  
RAID 5 and RAID 6 are not recommended for Amazon EBS because the parity write operations of these RAID modes consume some of the IOPS available to your volumes\. Depending on the configuration of your RAID array, these RAID modes provide 20\-30% fewer usable IOPS than a RAID 0 configuration\. Increased cost is a factor with these RAID modes as well; when using identical volume sizes and speeds, a 2\-volume RAID 0 array can outperform a 4\-volume RAID 6 array that costs twice as much\.  
RAID 1 is also not recommended for use with Amazon EBS\. RAID 1 requires more Amazon EC2 to Amazon EBS bandwidth than non\-RAID configurations because the data is written to multiple volumes simultaneously\. In addition, RAID 1 does not provide any write performance improvement\. 

## Create a RAID 0 array on Linux<a name="linux-raid"></a>

This documentation provides a basic RAID 0 setup example\.

Before you perform this procedure, you need to decide how large your RAID 0 array should be and how many IOPS you want to provision\.

Use the following procedure to create a RAID 0 array\. Note that you can get directions for Windows instances from [Create a RAID 0 array on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/raid-config.html#windows-raid) in the *Amazon EC2 User Guide for Windows Instances*\.

**To create a RAID 0 array on Linux**

1. Create the Amazon EBS volumes for your array\. For more information, see [Create an Amazon EBS volume](ebs-creating-volume.md)\.
**Important**  
Create volumes with identical size and IOPS performance values for your array\. Make sure you do not create an array that exceeds the available bandwidth of your EC2 instance\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

1. Attach the Amazon EBS volumes to the instance that you want to host the array\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

1. Use the mdadm command to create a logical RAID device from the newly attached Amazon EBS volumes\. Substitute the number of volumes in your array for *number\_of\_volumes* and the device names for each volume in the array \(such as `/dev/xvdf`\) for *device\_name*\. You can also substitute *MY\_RAID* with your own unique name for the array\.
**Note**  
You can list the devices on your instance with the lsblk command to find the device names\.

   To create a RAID 0 array, run the following command \(note the `--level=0` option to stripe the array\):

   ```
   [ec2-user ~]$ sudo mdadm --create --verbose /dev/md0 --level=0 --name=MY_RAID --raid-devices=number_of_volumes device_name1 device_name2
   ```
**Tip**  
If you get the `mdadm: command not found` error, use the following command to install mdadm: `sudo yum install mdadm`\.

1.  Allow time for the RAID array to initialize and synchronize\. You can track the progress of these operations with the following command:

   ```
   [ec2-user ~]$ sudo cat /proc/mdstat
   ```

   The following is example output:

   ```
   Personalities : [raid0]
   md0 : active raid0 xvdc[1] xvdb[0]
         41910272 blocks super 1.2 512k chunks
   
   unused devices: <none>
   ```

   In general, you can display detailed information about your RAID array with the following command:

   ```
   [ec2-user ~]$ sudo mdadm --detail /dev/md0
   ```

   The following is example output:

   ```
   /dev/md0:
              Version : 1.2
        Creation Time : Wed May 19 11:12:56 2021
           Raid Level : raid0
           Array Size : 41910272 (39.97 GiB 42.92 GB)
         Raid Devices : 2
        Total Devices : 2
          Persistence : Superblock is persistent
   
          Update Time : Wed May 19 11:12:56 2021
                State : clean
       Active Devices : 2
      Working Devices : 2
       Failed Devices : 0
        Spare Devices : 0
   
           Chunk Size : 512K
   
   Consistency Policy : none
   
                 Name : MY_RAID
                 UUID : 646aa723:db31bbc7:13c43daf:d5c51e0c
               Events : 0
   
       Number   Major   Minor   RaidDevice State
          0     202       16        0      active sync   /dev/sdb
          1     202       32        1      active sync   /dev/sdc
   ```

1. Create a file system on your RAID array, and give that file system a label to use when you mount it later\. For example, to create an ext4 file system with the label *MY\_RAID*, run the following command:

   ```
   [ec2-user ~]$ sudo mkfs.ext4 -L MY_RAID /dev/md0
   ```

   Depending on the requirements of your application or the limitations of your operating system, you can use a different file system type, such as ext3 or XFS \(consult your file system documentation for the corresponding file system creation command\)\.

1. To ensure that the RAID array is reassembled automatically on boot, create a configuration file to contain the RAID information:

   ```
   [ec2-user ~]$ sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
   ```
**Note**  
If you are using a Linux distribution other than Amazon Linux, you might need to modify this command\. For example, you might need to place the file in a different location, or you might need to add the `--examine` parameter\. For more information, run man mdadm\.conf on your Linux instance\.

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

## Create snapshots of volumes in a RAID array<a name="ebs-snapshots-raid-array"></a>

If you want to back up the data on the EBS volumes in a RAID array using snapshots, you must ensure that the snapshots are consistent\. This is because the snapshots of these volumes are created independently\. To restore EBS volumes in a RAID array from snapshots that are out of sync would degrade the integrity of the array\.

To create a consistent set of snapshots for your RAID array, use [EBS multi\-volume snapshots](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshots.html)\. Multi\-volume snapshots allow you to take point\-in\-time, data coordinated, and crash\-consistent snapshots across multiple EBS volumes attached to an EC2 instance\. You do not have to stop your instance to coordinate between volumes to ensure consistency because snapshots are automatically taken across multiple EBS volumes\. For more information, see the steps for creating multi\-volume snapshots under [Creating Amazon EBS snapshots](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-creating-snapshot.html)\. 
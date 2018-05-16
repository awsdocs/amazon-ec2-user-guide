# Expanding a Linux Partition<a name="expand-linux-partition"></a>

Some Amazon EC2 root volumes and volumes that are restored from snapshots contain a partition that actually holds the file system and the data\. If you think of a volume as a container, a partition is another container inside the volume, and the data resides on the partition\. Growing the volume size does not grow the partition; to take advantage of a larger volume, the partition must be expanded to the new size\.

**Note**  
Not all volumes restored from snapshots are partitioned, and this procedure may not apply to your volume\. You may just need to resize the file system on your volume to make all of the space available\. If you are not sure if your volume has a partition that needs resizing, see [To check if your volume partition needs resizing](recognize-expanded-volume-linux.md#procedure_partition_check) for more information\.

If the partition you want to expand is not the root partition, then you can simply unmount it and resize the partition from the instance itself\. If the partition to resize is the root partition for an instance, the process becomes more complicated because you cannot unmount the root partition of a running instance\. You have to perform the following procedures on another instance, which is referred to as a *secondary instance*\.

**Important**  
The following procedures were written for and tested on Amazon Linux\. Other distributions with different tool sets and tool versions may behave differently\.

**Topics**
+ [Preparing a Linux Root Partition for Expansion](#prepare-linux-root-part)
+ [Expanding a Linux Partition Using parted](#expanding-partition-parted)
+ [Expanding a Linux Partition Using gdisk](#expanding-partition-gdisk)
+ [Returning an Expanded Partition to Its Original Instance](#return-expanded-root-partition)

## Preparing a Linux Root Partition for Expansion<a name="prepare-linux-root-part"></a>

There are several steps to take to expand the root partition of an instance\. If the partition to expand is not the root partition, then this procedure is not necessary\. <a name="root_partition_prepare"></a>

**To prepare a Linux root partition for expansion**

1. If your primary instance is running, stop it\. You cannot perform the rest of this procedure on a running instance\. For more information, see [Stop and Start Your Instance](Stop_Start.md)\.

1. Check the integrity of your volume\. File\-system corruption that is picked up by the snapshot may render a restored root volume unbootable\.

1. Take a snapshot of your volume\. It can be easy to corrupt or lose your data in the following procedures\. If you have a fresh snapshot, you can always start over in case of a mistake and your data is still safe\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

1. <a name="identify_root_device_step"></a>Record the device name that the volume is attached to\. You can find this information on the **Root device** field of the instance's details pane\. The value is likely `/dev/sda1` or `/dev/xvda`\.

1. Detach the volume from the primary instance\. For more information, see [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\.

1. Attach the volume to another \(secondary\) instance in the same Availability Zone\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\. If your EBS volume is encrypted, you must use a secondary instance that supports Amazon EBS encryption; otherwise, you can use a `t2.micro` instance for this procedure\. For more information, see [Supported Instance Types](EBSEncryption.md#EBSEncryption_supported_instances)\. If you do not already have a secondary instance, you must launch one\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.
**Important**  
The secondary instance must be running when you attach the volume, and you should not reboot the secondary instance while multiple root volumes are attached; booting an instance with multiple root volumes attached could cause the instance to boot to the wrong volume\.

1. Log in to the secondary instance with SSH\. For more information, see [Connect to Your Linux Instance](AccessingInstances.md)\. Continue with the next procedure\.

## Expanding a Linux Partition Using parted<a name="expanding-partition-parted"></a>

The parted utility is a partition editing tool that is available on most Linux distributions\. It can create and edit both MBR partition tables and GPT partition tables\. Some versions of parted \(newer than version 2\.1\) have limited support for GPT partition tables and they may cause boot issues if their version of parted is used to modify boot volumes\. You can check your version of parted with the `parted --version` command\.

If you are expanding a partition that resides on a GPT partitioned device, you should choose to use the gdisk utility instead\. If you're not sure which disk label type your volume uses, you can check it with the sudo fdisk \-l command\. For more information, see [To expand a Linux partition using gdisk](#part-resize-gdisk)\.

**To expand a Linux partition using parted**

If the partition to expand is the root partition, be sure to follow the steps in [To prepare a Linux root partition for expansion](#root_partition_prepare) first\.

1. Identify the device that contains the partition that you want to expand\. Use the lsblk command to list all devices and partitions attached to the instance\.

   ```
   [ec2-user ~]$ lsblk
   NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   xvdf    202:80   0  100G  0 disk
   └─xvdf1 202:81   0    8G  0 part /mnt
   xvda1   202:1    0   30G  0 disk /
   ```

   In this example, the `xvdf` device has 100 GiB of available storage and it contains an 8\-GiB partition\.

1. <a name="partition_unmount_step_parted"></a>Unmount the partition if it is mounted\. Run the umount command with the value of `MOUNTPOINT` from the lsblk command\. In this example, the `MOUNTPOINT` value for the partition is `/mnt`\.

   ```
   [ec2-user ~]$ sudo umount /mnt
   ```

1. Take a snapshot of your volume \(unless you just took one in the previous procedure\)\. It can be easy to corrupt or lose your data in the following procedures\. If you have a fresh snapshot, you can always start over in case of a mistake and your data is still safe\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

1. Run the parted command on the device \(and not the partition on the device\)\. Remember to add the `/dev/` prefix to the name that lsblk outputs\.

   ```
   [ec2-user ~]$ sudo parted /dev/xvdf
   GNU Parted 2.1
   Using /dev/xvdf
   Welcome to GNU Parted! Type 'help' to view a list of commands.
   ```

1. Change the parted units of measure to sectors\.

   ```
   (parted) unit s
   ```

1. Run the print command to list the partitions on the device\. For certain partition table types, you might be prompted to repair the partition table for the larger volume size\. Answer 'Ignore' to any questions about fixing the existing partition table; you create a new table later\.

   ```
   (parted) print
   ```

   1. If you receive the following message, enter 'Ignore' to prevent the backup GPT location from changing\. 

      ```
      Error: The backup GPT table is not at the end of the disk, as it should be.  This might mean that another operating
      system believes the disk is smaller.  Fix, by moving the backup to the end (and removing the old backup)?
      Fix/Ignore/Cancel? Ignore
      ```

   1. If you receive the following message, enter 'Ignore' again to keep the space on the drive the same\.

      ```
      Warning: Not all of the space available to /dev/xvdf appears to be used, you can fix the GPT to use all of the
      space (an extra 46137344 blocks) or continue with the current setting?
      Fix/Ignore? Ignore
      ```

1. Examine the output for the total size of the disk, the partition table type, the number of the partition, the start point of the partition, and any flags, such as `boot`\. For `gpt` partition tables, note the name of the partition; for `msdos` partition tables, note the `Type` field \(`primary` or `extended`\)\. These values are used in the upcoming steps\.

   The following is a `gpt` partition table example\.

   ```
   Model: Xen Virtual Block Device (xvd)
   Disk /dev/xvdf: 209715200s
   Sector size (logical/physical): 512B/512B
   Partition Table: gpt
   
   Number  Start  End        Size       File system  Name                 Flags
   128     2048s  4095s      2048s                   BIOS Boot Partition  bios_grub
    1      4096s  16777182s  16773087s  ext4         Linux
   ```

   The following is an `msdos` partition table example\.

   ```
   Model: Xen Virtual Block Device (xvd)
   Disk /dev/xvdg: 104857600s
   Sector size (logical/physical): 512B/512B
   Partition Table: msdos
   
   Number  Start  End        Size       Type     File system  Flags
    1      2048s  35649535s  35647488s  primary  ext3
   ```

1. Delete the partition entry for the partition using the number \(1\) from the previous step\.

   ```
   (parted) rm 1
   ```

1. Create a new partition that extends to the end of the volume\.

   \(For the `gpt` partition table example\) Note the start point and name of partition 1 above\. For the `gpt` example, there is a start point of 4096s, and the name `Linux`\. Run the mkpart command with the start point of partition 1, the name, and 100% to use all of the available space\.

   ```
   (parted) mkpart Linux 4096s 100%
   ```

   \(For the `msdos` partition table example\) Note the start point and the partition type of partition 1 above\. For the `msdos` example, there is a start point of 2048s and a partition type of `primary`\. Run the mkpart command with a primary partition type, the start point of partition 1, and 100% to use all of the available space\.

   ```
   (parted) mkpart primary 2048s 100%
   ```

1. Run the print command again to verify your partition\.

   \(For the `gpt` partition table example\)

   ```
   (parted) print
   Model: Xen Virtual Block Device (xvd)
   Disk /dev/xvdf: 209715200s
   Sector size (logical/physical): 512B/512B
   Partition Table: gpt
   
   Number  Start  End         Size        File system  Name                 Flags
   128     2048s  4095s       2048s                    BIOS Boot Partition  bios_grub
    1      4096s  209713151s  209709056s  ext4         Linux
   ```

   \(For the `msdos` partition table example\)

   ```
   (parted) print
   Model: Xen Virtual Block Device (xvd)
   Disk /dev/xvdg: 104857600s
   Sector size (logical/physical): 512B/512B
   Partition Table: msdos
   
   Number  Start  End         Size        Type     File system  Flags
    1      2048s  104857599s  104855552s  primary  ext3
   ```

1. Check to see that any flags that were present earlier are still present for the partition that you expanded\. In some cases, the `boot` flag may be lost\. If a flag was dropped from the partition when it was expanded, add the flag with the following command, substituting your partition number and the flag name\. For example, the following command adds the `boot` flag to partition 1\.

   ```
   (parted) set 1 boot on
   ```

   You can run the print command again to verify your change\.

1. Run the quit command to exit parted\.

   ```
   (parted) quit
   ```
**Note**  
Because you removed a partition and added a partition, parted may warn that you may need to update `/etc/fstab`\. This is only required if the partition number changes\.

1. Check the file system to make sure that there are no errors \(this is required before you may extend the file system\)\. Note the file system type from the previous print commands\. Choose one of the commands below based on your file system type; if you are using a different file system, consult the documentation for that file system to determine the correct check command\.

   \(For `ext3` or `ext4` file systems\)

   ```
   [ec2-user ~]$ sudo e2fsck -f /dev/xvdf1
   e2fsck 1.42.3 (14-May-2012)
   Pass 1: Checking inodes, blocks, and sizes
   Pass 2: Checking directory structure
   Pass 3: Checking directory connectivity
   Pass 4: Checking reference counts
   Pass 5: Checking group summary information
   /: 31568/524288 files (0.4% non-contiguous), 266685/2096635 blocks
   ```

   \(For `xfs` file systems\)

   ```
   [ec2-user ~]$ sudo xfs_repair /dev/xvdf1
   Phase 1 - find and verify superblock...
   Phase 2 - using internal log
           - zero log...
           - scan filesystem freespace and inode maps...
           - found root inode chunk
   Phase 3 - for each AG...
           - scan and clear agi unlinked lists...
           - process known inodes and perform inode discovery...
           - agno = 0
           - agno = 1
           - agno = 2
           - agno = 3
           - process newly discovered inodes...
   Phase 4 - check for duplicate blocks...
           - setting up duplicate extent list...
           - check for inodes claiming duplicate blocks...
           - agno = 0
           - agno = 1
           - agno = 2
           - agno = 3
   Phase 5 - rebuild AG headers and trees...
           - reset superblock...
   Phase 6 - check inode connectivity...
           - resetting contents of realtime bitmap and summary inodes
           - traversing filesystem ...
           - traversal finished ...
           - moving disconnected inodes to lost+found ...
   Phase 7 - verify and correct link counts...
   done
   ```

1. The next steps differ depending on whether the expanded partition belongs on the current instance or if it is the root partition for another instance\. 
   + If this partition belongs on the current instance, remount the partition at the `MOUNTPOINT` identified in [Step 2](#partition_unmount_step_parted)\.

     ```
     [ec2-user ~]$ sudo mount /dev/xvdf1 /mnt
     ```

     After you have mounted the partition, extend the file system to use the newly available space by following the procedures in [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.
   + If this volume is the root partition for another instance, proceed to the procedures in [Returning an Expanded Partition to Its Original Instance](#return-expanded-root-partition)\.

## Expanding a Linux Partition Using gdisk<a name="expanding-partition-gdisk"></a>

The gdisk utility \(sometimes called GPT fdisk\) is a text\-based, menu\-driven tool for creating and editing partition tables, and it has better support for GPT partition tables than parted in some distributions\. Many common Linux distributions \(such as Amazon Linux and Ubuntu\) provide gdisk by default\. If your distribution does not provide the gdisk command, you can find out how to get it by visiting [Obtaining GPT fdisk](http://www.rodsbooks.com/gdisk/download.html); in many cases, it is much easier to launch an Amazon Linux instance to use as a secondary instance because the gdisk command is already available\.<a name="part-resize-gdisk"></a>

**To expand a Linux partition using gdisk**

If the partition to expand is the root partition, be sure to follow the steps in [To prepare a Linux root partition for expansion](#root_partition_prepare) first\.

1. Identify the device that contains the partition that you want to expand\. Use the lsblk command to list all devices and partitions attached to the instance\.

   ```
   [ec2-user ~]$ lsblk
   NAME    MAJ:MIN RM  SIZE RO MOUNTPOINT
   xvdf    202:80   0  100G  0
   xvdf1   202:81   0  9.9G  0 /mnt
   xvda1   202:1    0   30G  0 /
   ```

   In this example, the `xvdf` device has 100 GiB of available storage and it contains a 9\.9\-GiB partition\.

1. <a name="partition_unmount_step_gdisk"></a>Unmount the partition if it is mounted\. Run the umount command with the value of `MOUNTPOINT` from the lsblk command\. In this example, the `MOUNTPOINT` value for the partition is `/mnt`\.

   ```
   [ec2-user ~]$ sudo umount /mnt
   ```

1. Take a snapshot of your volume \(unless you just took one in the previous procedure\)\. It can be easy to corrupt or lose your data in the following procedures\. If you have a fresh snapshot, you can always start over in case of a mistake and your data is still safe\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

1. Run the gdisk command on the device \(and not the partition on the device\)\. Remember to add the `/dev/` prefix to the name that lsblk outputs\.

   ```
   [ec2-user ~]$ sudo gdisk /dev/xvdf
   gdisk /dev/xvdf
   GPT fdisk (gdisk) version 0.8.10
   
   Partition table scan:
     MBR: protective
     BSD: not present
     APM: not present
     GPT: present
   
   Found valid GPT with protective MBR; using GPT.
   ```

1. Run the p command to print the partition table for the device\.

1. Examine the output for the disk identifier, partition number, starting sector, code for the partition, and name of the partition\. If your volume has multiple partitions, take note of each one\.

   ```
   Command (? for help): p
   Disk /dev/xvdf: 209715200 sectors, 100.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 947F4655-F3BF-4A1F-8203-000000000000
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 20705246
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 2108 sectors (1.0 MiB)
   
   Number  Start (sector)    End (sector)  Size       Code  Name
      1            2048        20705152   9.9 GiB     EF00  lxroot
   ```

   In the preceding example, the disk identifier is `947F4655-F3BF-4A1F-8203-000000000000`, the partition number is `1`, the starting sector is `2048`, the code is `EF00`, and the name is `lxroot`\. Your values will vary\.

1. Because the existing partition table was originally created for a smaller volume, create a new partition table for the larger volume\. Run the o command to create a new, empty partition table\.

   ```
   Command (? for help): o
   This option deletes all partitions and creates a new protective MBR.
   Proceed? (Y/N): Y
   ```

1. Use the n command to create a new partition entry for each partition on the device\. 
   + If your volume has only one partition, at each prompt, enter the values that you recorded earlier\. For the last sector value, use the default value to expand to the entire volume size\.

     ```
     Command (? for help): n
     Partition number (1-128, default 1): 1
     First sector (34-209715166, default = 2048) or {+-}size{KMGTP}: 2048
     Last sector (2048-209715166, default = 209715166) or {+-}size{KMGTP}: 209715166
     Current type is 'Linux filesystem'
     Hex code or GUID (L to show codes, Enter = 8300): EF00
     Changed type of partition to 'EFI System'
     ```
   + If your volume has more than one partition, there is likely a BIOS boot partition, and a main data partition\. Create a new partition entry for the BIOS boot partition using the values that you recorded earlier\. Create another new partition entry for the main data partition using the values that you recorded earlier\. For the last sector value, use the default value to expand to the entire volume size\.

     ```
     Command (? for help): n
     Partition number (1-128, default 1): 1
     First sector (34-209715166, default = 2048) or {+-}size{KMGTP}:  2048
     Last sector (2048-209715166, default = 209715166) or {+-}size{KMGTP}: 4095
     Current type is 'Linux filesystem'
     Hex code or GUID (L to show codes, Enter = 8300): EF02
     Changed type of partition to 'BIOS boot partition'
     
     Command (? for help): n
     Partition number (2-128, default 2): 2
     First sector (34-209715166, default = 4096) or {+-}size{KMGTP}: 4096
     Last sector (4096-209715166, default = 209715166) or {+-}size{KMGTP}: 209715166
     Current type is 'Linux filesystem'
     Hex code or GUID (L to show codes, Enter = 8300): 0700
     Changed type of partition to 'Microsoft basic data'
     ```

1. Use the c command to change the name of each partition to the name of the previous partition\. If your partition did not have a name, simply type `Enter`\.

   ```
   Command (? for help): c
   Using 1
   Enter name: lxroot
   ```

1. Use the x command to enter the expert command menu\.

1. Use the g command to change the disk identifier to the original value\.

   ```
   Expert command (? for help): g
   Enter the disk's unique GUID ('R' to randomize): 947F4655-F3BF-4A1F-8203-A7B30C2A4425
   The new disk GUID is 947F4655-F3BF-4A1F-8203-A7B30C2A4425
   ```

1. Use the w command to write the changes to the device and exit\.

   ```
   Expert command (? for help): w
   
   Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
   PARTITIONS!!
   
   Do you want to proceed? (Y/N): Y
   OK; writing new GUID partition table (GPT) to /dev/xvdf.
   The operation has completed successfully.
   ```

1. Check the file system to make sure that there are no errors \(this is required before you may extend the file system\)\.

   1. Find the file system type with the following command, substituting the partition you just expanded \(this may be `/dev/xvdf2` if your volume had multiple partitions\)\.

      ```
      [ec2-user ~]$ sudo file -sL /dev/xvdf1
      ```

   1. Choose one of the commands below based on your file system type\. If you are using a different file system, consult the documentation for that file system to determine the correct check command\.

      \(For `ext3` or `ext4` file systems\)

      ```
      [ec2-user ~]$ sudo e2fsck -f /dev/xvdf1
      e2fsck 1.42.3 (14-May-2012)
      Pass 1: Checking inodes, blocks, and sizes
      Pass 2: Checking directory structure
      Pass 3: Checking directory connectivity
      Pass 4: Checking reference counts
      Pass 5: Checking group summary information
      /: 31568/524288 files (0.4% non-contiguous), 266685/2096635 blocks
      ```

      \(For `xfs` file systems\)
**Note**  
You may need to install the `xfsprogs` package to work with XFS file systems\. Use the following command to add XFS support to your Amazon Linux instance\.  

      ```
      [ec2-user ~]$ sudo yum install -y xfsprogs
      ```

      ```
      [ec2-user ~]$ sudo xfs_repair /dev/xvdf1
      Phase 1 - find and verify superblock...
      Phase 2 - using internal log
              - zero log...
              - scan filesystem freespace and inode maps...
              - found root inode chunk
      Phase 3 - for each AG...
              - scan and clear agi unlinked lists...
              - process known inodes and perform inode discovery...
              - agno = 0
              - agno = 1
              - agno = 2
              - agno = 3
              - process newly discovered inodes...
      Phase 4 - check for duplicate blocks...
              - setting up duplicate extent list...
              - check for inodes claiming duplicate blocks...
              - agno = 0
              - agno = 1
              - agno = 2
              - agno = 3
      Phase 5 - rebuild AG headers and trees...
              - reset superblock...
      Phase 6 - check inode connectivity...
              - resetting contents of realtime bitmap and summary inodes
              - traversing filesystem ...
              - traversal finished ...
              - moving disconnected inodes to lost+found ...
      Phase 7 - verify and correct link counts...
      done
      ```

1. The next steps differ depending on whether the expanded partition belongs on the current instance or if it is the root partition for another instance\. 
   + If this partition belongs on the current instance, remount the partition at the `MOUNTPOINT` identified in [Step 2](#partition_unmount_step_gdisk)\.

     ```
     [ec2-user ~]$ sudo mount /dev/xvdf1 /mnt
     ```

     After you have mounted the partition, extend the file system to use the newly available space by following the procedures in [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.
   + If this volume is the root partition for another instance, proceed to the procedures in [Returning an Expanded Partition to Its Original Instance](#return-expanded-root-partition)\.

## Returning an Expanded Partition to Its Original Instance<a name="return-expanded-root-partition"></a>

If you expanded a root partition from another instance, follow this procedure to return the volume to its original instance\.

**To return an expanded root partition to its original instance**

1. Detach the expanded partition from its secondary instance\. For more information, see [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\.

1. Reattach the volume to the primary instance using the device name that you identified in [Step 4](#identify_root_device_step) of the [preparation procedure](#root_partition_prepare)\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. Start the primary instance\. For more information, see [Stop and Start Your Instance](Stop_Start.md)\.

1. \(Optional\) If you launched a secondary instance for the sole purpose of expanding the partition, you can terminate the instance to stop incurring charges\. For more information, see [Terminate Your Instance](terminating-instances.md)\.

1. Connect to your primary instance and extend the file system to use the newly available space by following the procedures in [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.

After you are finished with this expanding the file system, you can create an AMI from the instance that you can use to launch new instances with the desired partition size\. For more information, see [Amazon Machine Images \(AMI\)](AMIs.md)\.
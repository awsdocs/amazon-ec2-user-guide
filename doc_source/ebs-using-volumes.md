# Making an Amazon EBS Volume Available for Use on Linux<a name="ebs-using-volumes"></a>

After you attach an Amazon EBS volume to your instance, it is exposed as a block device, and appears as a removable disk in Windows\. You can format the volume with any file system and then mount it\. After you make the EBS volume available for use, you can access it in the same ways that you access any other volume\. Any data written to this file system is written to the EBS volume and is transparent to applications using the device\.

You can take snapshots of your EBS volume for backup purposes or to use as a baseline when you create another volume\. For more information, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

You can get directions for volumes on a Windows instance from [Making a Volume Available for Use on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-using-volumes.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**To make an EBS volume available for use on Linux**

1. Connect to your instance using SSH\. For more information, see [Connect to Your Linux Instance](AccessingInstances.md)\.

1. Depending on the block device driver of the kernel, the device could be attached with a different name than you specified\. For example, if you specify a device name of `/dev/sdh`, your device could be renamed `/dev/xvdh` or `/dev/hdh`\. In most cases, the trailing letter remains the same\. In some versions of Red Hat Enterprise Linux \(and its variants, such as CentOS\), even the trailing letter could change \(`/dev/sda` could become `/dev/xvde`\)\. In these cases, the trailing letter of each device name is incremented the same number of times\. For example, if `/dev/sdb` is renamed `/dev/xvdf`, then `/dev/sdc` is renamed `/dev/xvdg`\. Amazon Linux creates a symbolic link for the name you specified to the renamed device\. Other operating systems could behave differently\.

   Use the lsblk command to view your available disk devices and their mount points \(if applicable\) to help you determine the correct device name to use\.

   ```
   [ec2-user ~]$ lsblk
   NAME  MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   xvdf  202:80   0  100G  0 disk
   xvda1 202:1    0    8G  0 disk /
   ```

   The output of lsblk removes the `/dev/` prefix from full device paths\. In this example, `/dev/xvda1` is mounted as the root device \(note that MOUNTPOINT is listed as `/`, the root of the Linux file system hierarchy\), and `/dev/xvdf` is attached, but it has not been mounted yet\.

   EBS volumes are exposed as NVMe block devices for the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, and z1d\. The device names that you specify are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\. For more information, see [Amazon EBS and NVMe](nvme-ebs-volumes.md)\.

1. Determine whether to create a file system on the volume\. New volumes are raw block devices, and you must create a file system on them before you can mount and use them\. Volumes that have been restored from snapshots likely have a file system on them already; if you create a new file system on top of an existing file system, the operation overwrites your data\. Use the sudo file \-s *device* command to list special information, such as file system type\.

   ```
   [ec2-user ~]$ sudo file -s /dev/xvdf
   /dev/xvdf: data
   ```

   If the output of the previous command shows simply `data` for the device, then there is no file system on the device and you must create one\. You can go on to [Step 4](#create_file_system_step)\. If you run this command on a device that contains a file system, then your output will be different\.

   ```
   [ec2-user ~]$ sudo file -s /dev/xvda1
   /dev/xvda1: Linux rev 1.0 ext4 filesystem data, UUID=1701d228-e1bd-4094-a14c-8c64d6819362 (needs journal recovery) (extents) (large files) (huge files)
   ```

   In the previous example, the device contains Linux rev 1\.0 ext4 filesystem data, so this volume does not need a file system created \(you can skip [Step 4](#create_file_system_step) if your output shows file system data\)\.

1. <a name="create_file_system_step"></a>\(Conditional\) Use the following command to create an ext4 file system on the volume\. Substitute the device name \(such as `/dev/xvdf`\) for *device\_name*\. Depending on the requirements of your application or the limitations of your operating system, you can choose a different file system type, such as ext3 or XFS\.
**Warning**  
This step assumes that you're mounting an empty volume\. If you're mounting a volume that already has data on it \(for example, a volume that was restored from a snapshot\), don't use mkfs before mounting the volume \(skip to the next step instead\)\. Otherwise, you'll format the volume and delete the existing data\.

   ```
   [ec2-user ~]$ sudo mkfs -t ext4 device_name
   ```

1. Use the following command to create a mount point directory for the volume\. The mount point is where the volume is located in the file system tree and where you read and write files to after you mount the volume\. Substitute a location for *mount\_point*, such as `/data`\.

   ```
   [ec2-user ~]$ sudo mkdir mount_point
   ```

1. Use the following command to mount the volume at the location you just created\.

   ```
   [ec2-user ~]$ sudo mount device_name mount_point
   ```

1. \(Optional\) To mount this EBS volume on every system reboot, add an entry for the device to the `/etc/fstab` file\.

   1. Create a backup of your `/etc/fstab` file that you can use if you accidentally destroy or delete this file while you are editing it\.

      ```
      [ec2-user ~]$ sudo cp /etc/fstab /etc/fstab.orig
      ```

   1. Open the `/etc/fstab` file using any text editor, such as nano or vim\.
**Note**  
You must open the file as root or by using the sudo command\.

   1. Add a new line to the end of the file for your volume using the following format:

      ```
      device_name  mount_point  file_system_type  fs_mntops  fs_freq  fs_passno  
      ```

      The last three fields on this line are the file system mount options, the dump frequency of the file system, and the order of file system checks done at boot time\. If you don't know what these values should be, then use the values in the following example for them \(`defaults,nofail 0 2)`\. For more information on `/etc/fstab` entries, see the fstab manual page \(by entering man fstab on the command line\)\. 

      You can use the system's current device name \(`/dev/sda1`, `/dev/xvda1`, etc\.\) in `/etc/fstab`, but we recommend using the device's 128\-bit universally unique identifier \(UUID\) instead\. System\-declared block\-device names may change under a variety of circumstances, but the UUID is assigned to a volume partition when it is formatted and persists throughout the partition's service life\. By using the UUID, you reduce the chances of the block\-device mapping in `/etc/fstab` leaving the system unbootable after a hardware reconfiguration\.

      To find the UUID of a device, first display the available devices:

      ```
      [ec2-user ~]$ df
      ```

      The following is example output:

      ```
      Filesystem     1K-blocks    Used Available Use% Mounted on
      /dev/xvda1       8123812 1876888   6146676  24% /
      devtmpfs          500712      56    500656   1% /dev
      tmpfs             509724       0    509724   0% /dev/shm
      ```

      Next, continuing this example, examine the output of either of two commands to find the UUID of `/dev/xvda1`:
      + **sudo file \-s */dev/xvda1***
      + **ls \-al /dev/disk/by\-uuid/**

      Assuming that you find `/dev/xvda1` to have UUID `de9a1ccd-a2dd-44f1-8be8-0123456abcdef`, you would add the following entry to `/etc/fstab` to mount an ext4 file system at mount point `/data`:

      ```
      UUID=de9a1ccd-a2dd-44f1-8be8-0123456abcdef       /data   ext4    defaults,nofail        0       2
      ```
**Note**  
If you ever intend to boot your instance without this volume attached \(for example, so this volume could move back and forth between different instances\), you should add the `nofail` mount option that allows the instance to boot even if there are errors in mounting the volume\. Debian derivatives, including Ubuntu versions earlier than 16\.04, must also add the `nobootwait` mount option\.

   1. After you've added the new entry to `/etc/fstab`, you must check that your entry works\. Run the sudo mount \-a command to mount all file systems in `/etc/fstab`\.

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

1. Review the file permissions of your new volume mount to make sure that your users and applications can write to the volume\. For more information about file permissions, see [File security](http://tldp.org/LDP/intro-linux/html/sect_03_04.html) at *The Linux Documentation Project*\.
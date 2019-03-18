# Making an Amazon EBS Volume Available for Use on Linux<a name="ebs-using-volumes"></a>

After you attach an Amazon EBS volume to your instance, it is exposed as a block device\. You can format the volume with any file system and then mount it\. After you make the EBS volume available for use, you can access it in the same ways that you access any other volume\. Any data written to this file system is written to the EBS volume and is transparent to applications using the device\.

You can take snapshots of your EBS volume for backup purposes or to use as a baseline when you create another volume\. For more information, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

You can get directions for volumes on a Windows instance from [Making a Volume Available for Use on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-using-volumes.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Format and Mount an Attached Volume<a name="ebs-format-mount-volume"></a>

Suppose that you have an EC2 instance with an EBS volume for the root device, `/dev/xvda`, and that you have just attached an empty EBS volume to the instance using `/dev/sdf`\. Use the following procedure to make the newly attached volume available for use\.

**To format and mount an EBS volume on Linux**

1. Connect to your instance using SSH\. For more information, see [Connect to Your Linux Instance](AccessingInstances.md)\.

1. The device could be attached to the instance with a different device name than you specified in the block device mapping\. For more information, see [Device Naming on Linux Instances](device_naming.md)\. Use the lsblk command to view your available disk devices and their mount points \(if applicable\) to help you determine the correct device name to use\. The output of lsblk removes the `/dev/` prefix from full device paths\.

   The following is example output for a [Nitro\-based instance](instance-types.md#ec2-nitro-instances), which exposes EBS volumes as NVMe block devices\. The root device is `/dev/nvme0n1`\. The attached volume is `/dev/nvme1n1`, which is not yet mounted\.

   ```
   [ec2-user ~]$ lsblk
   NAME          MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   nvme1n1       259:0    0  10G  0 disk
   nvme0n1       259:1    0   8G  0 disk
   -nvme0n1p1    259:2    0   8G  0 part /
   -nvme0n1p128  259:3    0   1M  0 part
   ```

   The following is example output for a T2 instance\. The root device is `/dev/xvda`\. The attached volume is `/dev/xvdf`, which is not yet mounted\.

   ```
   [ec2-user ~]$ lsblk
   NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   xvda    202:0    0    8G  0 disk
   -xvda1  202:1    0    8G  0 part /
   xvdf    202:80   0   10G  0 disk
   ```

1. Determine whether there is a file system on the volume\. New volumes are raw block devices, and you must create a file system on them before you can mount and use them\. Volumes that have been restored from snapshots likely have a file system on them already; if you create a new file system on top of an existing file system, the operation overwrites your data\.

   Use the file \-s command to get information about a device, such as its file system type\. If the output shows simply `data`, as in the following example output, there is no file system on the device and you must create one\.

   ```
   [ec2-user ~]$ sudo file -s /dev/xvdf
   /dev/xvdf: data
   ```

   If the device has a file system, the command shows information about the file system type\. For example, the following output shows a root device with the XFS file system\.

   ```
   [ec2-user ~]$ sudo file -s /dev/xvda1
   /dev/xvda1: SGI XFS filesystem data (blksz 4096, inosz 512, v2 dirs)
   ```

1. <a name="create_file_system_step"></a>\(Conditional\) If you discovered that there is a file system on the device in the previous step, skip this step\. If you have an empty volume, use the mkfs \-t command to create a file system on the volume\.
**Warning**  
Do not use this command if you're mounting a volume that already has data on it \(for example, a volume that was restored from a snapshot\)\. Otherwise, you'll format the volume and delete the existing data\.

   ```
   [ec2-user ~]$ sudo mkfs -t xfs /dev/xvdf
   ```

1. Use the mkdir command to create a mount point directory for the volume\. The mount point is where the volume is located in the file system tree and where you read and write files to after you mount the volume\. The following example creates a directory named `/data`\.

   ```
   [ec2-user ~]$ sudo mkdir /data
   ```

1. Use the following command to mount the volume at the directory you created in the previous step\.

   ```
   [ec2-user ~]$ sudo mount /dev/xvdf /data
   ```

1. Review the file permissions of your new volume mount to make sure that your users and applications can write to the volume\. For more information about file permissions, see [File security](http://tldp.org/LDP/intro-linux/html/sect_03_04.html) at *The Linux Documentation Project*\.

1. The mount point is not automatically preserved after rebooting your instance\. To automatically mount this EBS volume after reboot, see [Automatically Mount an Attached Volume After Reboot](#ebs-mount-after-reboot)\.

## Automatically Mount an Attached Volume After Reboot<a name="ebs-mount-after-reboot"></a>

To mount an attached EBS volume on every system reboot, add an entry for the device to the `/etc/fstab` file\.

You can use the device name, such as `/dev/xvdf`, in `/etc/fstab`, but we recommend using the device's 128\-bit universally unique identifier \(UUID\) instead\. Device names can change, but the UUID persists throughout the life of the partition\. By using the UUID, you reduce the chances that the system becomes unbootable after a hardware reconfiguration\. For more information, see [Identifying the EBS Device](nvme-ebs-volumes.md#identify-nvme-ebs-device)\.

**To mount an attached volume automatically after reboot**

1. \(Optional\) Create a backup of your `/etc/fstab` file that you can use if you accidentally destroy or delete this file while editing it\.

   ```
   [ec2-user ~]$ sudo cp /etc/fstab /etc/fstab.orig
   ```

1. Use the blkid command to find the UUID of the device\.

   ```
   [ec2-user ~]$ sudo blkid
   /dev/xvda1: LABEL="/" UUID="ca774df7-756d-4261-a3f1-76038323e572" TYPE="xfs" PARTLABEL="Linux" PARTUUID="02dcd367-e87c-4f2e-9a72-a3cf8f299c10"
   /dev/xvdf: UUID="aebf131c-6957-451e-8d34-ec978d9581ae" TYPE="xfs"
   ```

1. Open the `/etc/fstab` file using any text editor, such as nano or vim\.

   ```
   [ec2-user ~]$ sudo vim /etc/fstab
   ```

1. Add the following entry to `/etc/fstab` to mount the device at the specified mount point\. The fields are the UUID value returned by blkid, the mount point, the file system, and the recommended file system mount options\. For more information, see the manual page for fstab \(run man fstab\)\.

   ```
   UUID=aebf131c-6957-451e-8d34-ec978d9581ae  /data  xfs  defaults,nofail  0  2
   ```
**Note**  
If you ever boot your instance without this volume attached \(for example, after moving the volume to another instance\), the `nofail` mount option enables the instance to boot even if there are errors mounting the volume\. Debian derivatives, including Ubuntu versions earlier than 16\.04, must also add the `nobootwait` mount option\.

1. To verify that your entry works, run the following commands to unmount the device and then mount all file systems in `/etc/fstab`\. If there are no errors, the `/etc/fstab` file is OK and your file system will mount automatically after it is rebooted\.

   ```
   [ec2-user ~]$ sudo umount /data
   [ec2-user ~]$ sudo mount -a
   ```

   If you receive an error message, address the errors in the file\.
**Warning**  
Errors in the `/etc/fstab` file can render a system unbootable\. Do not shut down a system that has errors in the `/etc/fstab` file\.

   If you are unsure how to correct errors in `/etc/fstab` and you created a backup file in the first step of this procedure, you can restore from your backup file using the following command\.

   ```
   [ec2-user ~]$ sudo mv /etc/fstab.orig /etc/fstab
   ```
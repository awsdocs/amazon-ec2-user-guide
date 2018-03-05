# Initializing Amazon EBS Volumes<a name="ebs-initialize"></a>

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. However, storage blocks on volumes that were restored from snapshots must be initialized \(pulled down from Amazon S3 and written to the volume\) before you can access the block\. This preliminary action takes time and can cause a significant increase in the latency of an I/O operation the first time each block is accessed\. For most applications, amortizing this cost over the lifetime of the volume is acceptable\. Performance is restored after the data is accessed once\.

You can avoid this performance hit in a production environment by reading from all of the blocks on your volume before you use it; this process is called *initialization*\. For a new volume created from a snapshot, you should read all the blocks that have data before using the volume\.

**Important**  
While initializing `io1` volumes that were restored from snapshots, the performance of the volume may drop below 50 percent of its expected level, which causes the volume to display a `warning` state in the **I/O Performance** status check\. This is expected, and you can ignore the `warning` state on `io1` volumes while you are initializing them\. For more information, see [Monitoring Volumes with Status Checks](monitoring-volume-status.md#monitoring-volume-checks)\.

## Initializing Amazon EBS Volumes on Linux<a name="ebs-initialize-linux"></a>

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. For volumes that have been restored from snapshots, use the dd or fio utilities to read from all of the blocks on a volume\. All existing data on the volume will be preserved\.

**To initialize a volume restored from a snapshot on Linux**

1. Attach the newly\-restored volume to your Linux instance\.

1. Use the lsblk command to list the block devices on your instance\.

   ```
   [ec2-user ~]$ lsblk
   NAME  MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   xvdf  202:80   0  30G  0 disk
   xvda1 202:1    0   8G  0 disk /
   ```

   Here you can see that the new volume, `/dev/xvdf`, is attached, but not mounted \(because there is no path listed under the `MOUNTPOINT` column\)\.

1. <a name="initialize-snapshot-step"></a>Use the dd or fio utilities to read all of the blocks on the device\. The dd command is installed by default on Linux systems, but fio is considerably faster because it allows multi\-threaded reads\.
**Note**  
This step may take several minutes up to several hours, depending on your EC2 instance bandwidth, the IOPS provisioned for the volume, and the size of the volume\.

   \[dd\] The `if` \(input file\) parameter should be set to the drive you wish to initialize\. The `of` \(output file\) parameter should be set to the Linux null virtual device, `/dev/null`\. The `bs` parameter sets the block size of the read operation; for optimal performance, this should be set to 1 MB\.
**Important**  
Incorrect use of dd can easily destroy a volume's data\. Be sure to follow precisely the example command below\. Only the `if=/dev/xvdf` parameter will vary depending on the name of the device you are reading\.

   ```
   [ec2-user ~]$ sudo dd if=/dev/xvdf of=/dev/null bs=1M
   ```

   \[fio\] If you have fio installed on your system, use the following command initialize your volume\. The `--filename` \(input file\) parameter should be set to the drive you wish to initialize\.

   ```
   [ec2-user ~]$ sudo fio --filename=/dev/xvdf --rw=read --bs=128k --iodepth=32 --ioengine=libaio --direct=1 --name=volume-initialize
   ```

   To install fio on Amazon Linux, use the following command:

   ```
   sudo yum install -y fio
   ```

   To install fio on Ubuntu, use the following command:

   ```
   sudo apt-get install -y fio
   ```

   When the operation is finished, you will see a report of the read operation\. Your volume is now ready for use\. For more information, see [Making an Amazon EBS Volume Available for Use](ebs-using-volumes.md)\.
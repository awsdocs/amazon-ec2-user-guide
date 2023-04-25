# Initialize Amazon EBS volumes<a name="ebs-initialize"></a>

Empty EBS volumes receive their maximum performance the moment that they are created and do not require initialization \(formerly known as pre\-warming\)\.

For volumes that were created from snapshots, the storage blocks must be pulled down from Amazon S3 and written to the volume before you can access them\. This preliminary action takes time and can cause a significant increase in the latency of I/O operations the first time each block is accessed\. Volume performance is achieved after all blocks have been downloaded and written to the volume\.

**Important**  
While initializing Provisioned IOPS SSD volumes that were created from snapshots, the performance of the volume may drop below 50 percent of its expected level, which causes the volume to display a `warning` state in the **I/O Performance** status check\. This is expected, and you can ignore the `warning` state on Provisioned IOPS SSD volumes while you are initializing them\. For more information, see [EBS volume status checks](monitoring-volume-status.md#monitoring-volume-checks)\.

For most applications, amortizing the initialization cost over the lifetime of the volume is acceptable\. To avoid this initial performance hit in a production environment, you can use one of the following options:
+ Force the immediate initialization of the entire volume\. For more information, see [Initialize Amazon EBS volumes on Linux](#ebs-initialize-linux)\.
+ Enable fast snapshot restore on a snapshot to ensure that the EBS volumes created from it are fully\-initialized at creation and instantly deliver all of their provisioned performance\. For more information, see [Amazon EBS fast snapshot restore](ebs-fast-snapshot-restore.md)\.

## Initialize Amazon EBS volumes on Linux<a name="ebs-initialize-linux"></a>

Empty EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. For volumes that have been created from snapshots, use the dd or fio utilities to read from all of the blocks on a volume\. All existing data on the volume will be preserved\.

For information about initializing Amazon EBS volumes on Windows, see [Initializing Amazon EBS volumes on Windows](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-initialize.html#ebs-initialize-windows)\.

**To initialize a volume created from a snapshot on Linux**

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

   \[fio\] If you have fio installed on your system, use the following command to initialize your volume\. The `--filename` \(input file\) parameter should be set to the drive you wish to initialize\.

   ```
   [ec2-user ~]$ sudo fio --filename=/dev/xvdf --rw=read --bs=1M --iodepth=32 --ioengine=libaio --direct=1 --name=volume-initialize
   ```

   To install fio on Amazon Linux, use the following command:

   ```
   sudo yum install -y fio
   ```

   To install fio on Ubuntu, use the following command:

   ```
   sudo apt-get install -y fio
   ```

   When the operation is finished, you will see a report of the read operation\. Your volume is now ready for use\. For more information, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.
# Torn write prevention<a name="storage-twp"></a>

Torn write prevention is a block storage feature designed by AWS to improve the performance of your I/O\-intensive relational database workloads and reduce latency without negatively impacting data resiliency\. Relational databases that use InnoDB or XtraDB as the database engine, such as MySQL and MariaDB, will benefit from torn write prevention\.

Typically, relational databases that use pages larger than the *power fail atomicity* of the storage device use *data logging* mechanisms to protect against torn writes\. MariaDB and MySQL use a *doublewrite buffer* file to log data before writing it to data tables\. In the event of incomplete or torn writes, as a result of operating system crashes or power loss during write transactions, the database can recover the data from the doublewrite buffer\. The additional I/O overhead associated with writing to the doublewrite buffer impacts database performance and application latency, and it reduces the number transactions that can be processed per second\.

With *torn write prevention*, data is written to storage in *all\-or\-nothing* write transactions, which eliminates the need for using the doublewrite buffer\. This prevents partial, or torn, data from being written to storage in the event of operating system crashes or power loss during write transactions\. The number of transactions processed per second can be increased by up to 30 percent, and write latency can be decreased by up to 50 percent, without compromising the resiliency of your workloads\.

## Pricing<a name="twp-pricing"></a>

There are no additional costs for using torn write prevention\.

## Supported block sizes and block boundary alignments<a name="supported-block-sizes"></a>

Torn write prevention supports write operations for 4 KiB, 8 KiB, and 16 KiB blocks of data\. The data block start logical block address \(LBA\) must be aligned to the respective block boundary size of 4 KiB, 8 KiB, or 16 KiB\. For example, for 16 KiB write operations, the data block start LBA must be aligned to a block boundary size of 16 KiB\.

The following table shows support across storage and instance types\.


|   | 4 KiB blocks | 8 KiB blocks | 16 KiB blocks | 
| --- | --- | --- | --- | 
| Instance store volumes | All NVMe instance store volumes attached to [current generation instances](instance-types.md#current-gen-instances)\. | I4i instances supported by AWS Nitro SSD\. | 
| Amazon EBS volumes | All Amazon EBS volumes attached to [nitro\-based instances](instance-types.md#ec2-nitro-instances)\. | 

To confirm whether your instance and volume support torn write prevention, query to check if the instance supports torn write prevention and other details, like supported block and boundary sizes\. For more information, see [Check torn write prevention support and configuration](#twp-namespace)\.

## Requirements<a name="twp-reqs"></a>

For torn write prevention to work properly, an I/O operation must meet size, alignment, and boundary requirements, as specified in the `NTWPU`, `NTWGU`, `NTWBU` fields\. You must configure your operating system to ensure that the specific storage subsystem \(file system, LVM, RAID, etc\) does not modify I/O properties down the storage stack, including block merges, splits, or block address relocation, before being submitted to the device\.

Torn write prevent has been tested with the following configuration:
+ An instance type and storage type that supports the required block size\.
+ Amazon Linux 2 with kernel version 5\.10 or later\.
+ ext4 with `bigalloc` enabled and a cluster size of 16 KiB, and the most recent ext4 utilities \(e2fsprogs 1\.46\.5 or later\)\.
+ `O_DIRECT` file access mode to bypass Linux kernel buffer cache\.

**Note**  
You do not need to disable I/O merging for MySQL and MariaDB workloads\.

## Check torn write prevention support and configuration<a name="twp-namespace"></a>

To confirm whether your instance and volume supports torn write prevention, and to view the NVMe namespace vendor specific data that contains torn write prevention information, use the following command\.

```
$ sudo nvme id-ns -v device_name
```

**Note**  
The command returns the vendor\-specific information in hex with ASCII interpretation\. You might need to build a tool, similar to `ebsnvme-id`, into your applications that can read and parse the output\.

For example, the following command returns the NVMe namespace vendor specific data that contains torn write prevention information for `/dev/nvme1n1`\.

```
$ sudo nvme id-ns -v /dev/nvme1n1
```

If your instance and volume support torn write prevention, it returns the following AWS torn write prevention information in the NVMe namespace vendor specific data\.

**Note**  
The bytes in the following table represent the offset in bytes from the beginning of the NVMe namespace vendor specific data\.


| Bytes | Description | 
| --- | --- | 
| 0:31 | The name of the device attachment mount point, for example /dev/xvda\. You provide this during volume attachment request and it can be used by the Amazon EC2 instance to create a symlink to the NVMe block device\(nvmeXn1\)\. | 
| 32:63 | The volume ID\. For example, vol01234567890abcdef\. This field can be used to map the NVMe device to the attached volume\. | 
| 64:255 | Reserved for future use\. | 
| 256:257 | Namespace Torn Write Prevention Unit size \(NTWPU\)\. This field indicates the namespace specific size of the write operation guaranteed to be written atomically to the NVM during a power failure or error condition\. This field is specified in logical blocks represented in zero based values\. | 
| 258:259 | Namespace Torn Write Prevention Granularity size \(NTWPG\)\. This field indicates the namespace specific size increments below NTWPU of the write operation guaranteed to be written atomically to the NVM during a power failure or error condition\. That is, size should be NTWPG \* n <= NTWPU where n is positive integer\. The write operation LBA offset also must be aligned to this field\. This field is specified in logical blocks represented in zero based values\. | 
| 260:263 | Namespace Torn Write Prevention Boundary size \(NTWPB\)\. This field indicates the atomic boundary size for this namespace for the NTWPU value\. Writes to this namespace that cross atomic boundaries are not guaranteed to be written atomically to the NVM during a power failure or error condition\. A value of 0h indicates that there are no atomic boundaries for power fail or error conditions\. All other values specify a size in terms of logical blocks using the same encoding as the NTWPU field\. | 

## Configure your software stack for torn write prevention<a name="configure-twp"></a>

Torn write prevention is enabled by default on [supported instance types with supported volumes](#supported-block-sizes)\. You do not need to enabled any additional settings to enable your volume or instance for torn write prevention\.

**Note**  
There is no performance impact on workloads that do not support torn write prevention\. You do not need to make any changes for these workloads\.  
Workloads that do support torn write prevention, but are not configured to use it, continue to use the doublewrite buffer and do not receive any performance benefits\.

To configure your MySQL or MariaDB software stack to disable the doublewrite buffer and use torn write prevention, complete the following steps:

1. Configure your volume to use ext4 file system with the BigAlloc option and set the cluster size to 4 KiB, 8 KiB, or 16 KiB\. Using BigAlloc with a cluster size of 4 KiB, 8 KiB, or 16 KiB ensures that the file system allocates files that align with the respective boundary\.

   ```
   $  mkfs.ext4 -O bigalloc -C 4096|8192|16384 device_name
   ```
**Note**  
For MySQL and MariaDB, you must use `-C 16384` to match the database page size\. Setting allocation granularity to a value other than a multiple of the page size can result in allocations that might be mismatched with torn write prevention boundaries of the storage device\.

   For example:

   ```
   $  mkfs.ext4 -O bigalloc -C 16384 /dev/nvme1n1
   ```

1. Configure InnoDB to use the `0_DIRECT` flushing method and turn off InnoDB doublewrite\. Use your preferred text editor to open `/etc/my.cnf`, and update the `innodb_flush_method` and `innodb_doublewrite` parameters as follows:

   ```
   innodb_flush_method=O_DIRECT
   innodb_doublewrite=0
   ```

**Important**  
If you are using Logical Volume Manager \(LVM\) or other storage virtualization layer, make sure that the starting offsets of the volumes are aligned on 16 KiB multiples\. This is relative to the underlying NVMe storage to account for the metadata headers and superblocks used by the storage virtualization layer\. If you add an offset to the LVM physical volume, it can cause misalignment between the file system allocations and the NVMe device's offsets, which would invalidate torn write prevention\. For more information, see `--dataalignmentoffset` in the [Linux manual page](https://man7.org/linux/man-pages/man8/pvcreate.8.html)\.
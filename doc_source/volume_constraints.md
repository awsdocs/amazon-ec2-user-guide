# Constraints on Modifying EBS Volume Size<a name="volume_constraints"></a>

Modifications to the size of an Amazon EBS volume are constrained by the physics and arithmetic of block data storage, as well as by the implementation decisions of operating system and file system designers\. AWS imposes additional limits on volume size to safeguard the reliability of its services\. 

As a service, Amazon EBS abstracts the massively distributed storage of a data center into virtual hard disk drives\. To an operating system installed on an EC2 instance, an attached EBS volume appears to be a physical hard disk drive containing 512\-byte disk sectors\. The OS manages the allocation of data blocks \(or clusters\) onto those virtual sectors through its storage management utilities\. The allocation is in conformity with a volume partitioning scheme, such as master boot record \(MBR\) or GUID partition table \(GPT\), and within the capabilities of the installed file system \(ext4, NTFS, and so on\)\. 

EBS is not aware of the data contained in its virtual disk sectors; it only ensures the integrity of the sectors\. This means that AWS actions and OS actions are independent of each other\. When you are modifying volume size, be aware of the capabilities and limits of both\. For example, you can increase the size of an EBS volume to as much as 16 TiB, but whether the OS recognizes all of that capacity depends on its own design characteristics and on how the volume is partitioned\.

This section describes the most important factors that limit the usable size of an EBS volume\.

## AWS Service Limitations<a name="aws_limits"></a>

Amazon EBS currently supports a maximum volume size of 16 TiB\. 

Amazon EC2 requires Windows boot volumes to use MBR partitioning\. As discussed in [Partitioning Schemes](#partitioning), this means that boot volumes cannot be bigger than 2 TiB\. Windows data volumes are not subject to this limitation and may be GPT\-partitioned\. 

Linux boot volumes may be either MBR or GPT, and Linux GPT boot volumes are not subject to the 2\-TiB limit\.

## Partitioning Schemes<a name="partitioning"></a>

Among other impacts, the partitioning scheme determines how many logical data blocks can be uniquely addressed in a single volume\. For more information, see [Data Block Sizes](#block_size)\. Two partitioning schemes are in common use on Linux and Windows systems: master boot record \(MBR\) and GUID partition table \(GPT\)\. The important differences between the two can be summarized as follows:
+ **MBR**

  MBR uses a 32\-bit data structure to store block addresses\. This means that each data block is mapped with one of 232 possible integers\. The maximum addressable size of a volume is given by: 

  \(232 \- 1\) × Block size = Number of addressable blocks

  The block size for MBR volumes is conventionally limited to 512 bytes\. Therefore:

   \(232 \- 1\) × 512 bytes = 2 TiB \- 512 bytes

  Engineering workarounds to increase this 2\-TiB limit for MBR volumes have not met with widespread industry adoption\. Consequently, Linux and Windows never detect an MBR volume as being larger than 2 TiB even if AWS shows its size to be larger\. 
+ **GPT**

  GPT uses a 64\-bit data structure to store block addresses\. This means that each data block is mapped with one of 264 possible integers\. The maximum addressable size of a volume is given by:

  \(264 \- 1\) × Block size = Number of addressable blocks

   The block size for GPT volumes is commonly 4,096 bytes\. Therefore:

   \(264 \- 1\) × 4,096 bytes = 8 ZiB \- 4,096 bytes = 8 billion TiB \- 4,096 bytes

  Real\-world computer systems don't support anything close to this theoretical maximum\. Implemented file\-system size is currently limited to 50 TiB for ext4 and 256 TiB for NTFS—both of which exceed the 16\-TiB limit imposed by AWS\.

## Data Block Sizes<a name="block_size"></a>

Data storage on a modern hard drive is managed through logical block Addressing, an abstraction layer that allows the operating system to read and write data in logical blocks without knowing much about the underlying hardware\. The OS relies on the storage device to map the blocks to its physical sectors\. EBS advertises 512\-byte sectors to the operating system, which reads and writes data to disk using data blocks that are a multiple of the sector size\. 

The industry default size for logical data blocks is currently 4,096 bytes \(4 KiB\)\. Because certain workloads benefit from a smaller or larger block size, file systems support non\-default block sizes that can be specified during formatting\. Scenarios in which non\-default block sizes should be used are outside the scope of this topic, but the choice of block size has consequences for the storage capacity of the volume\. The following table shows storage capacity as a function of block size:


**Block Size and Resulting Volume Capacity**  

| Block size | Max\. volume size | 
| --- | --- | 
| 4 KiB \(default\) | 16 TiB | 
| 8 KiB | 32 TiB | 
| 16 KiB | 64 TiB | 
| 32 KiB | 128 TiB | 
| 64 KiB \(maximum\) | 256 TiB | 

The EBS\-imposed limit on volume size is currently equal to the maximum size enabled by 4\-KiB data blocks\.

## Summary<a name="summary"></a>

The following table summarizes the theoretical and implemented storage capacities for the most commonly used file systems on Amazon EBS\. 


**MBR vs\. GPT volume sizes for popular file systems, assuming 4,096\-byte block size**  

| Partitioning Scheme | Max\. addressable blocks  | Theoretical max\. size \(blocks × block size\) | Ext4 implemented max\. size\* | XFS implemented max\. size\*\* | NTFS implemented max\. size | Max\. supported by EBS | 
| --- | --- | --- | --- | --- | --- | --- | 
| MBR | 232 | 2 TiB | 2 TiB | 2 TiB | 2 TiB | 2 TiB | 
| GPT | 264 | 8 ZiB = 8 ×10243 TiB | 1 EiB =10242 TiB \(50 TiB certified on RHEL7\) |  500 TiB \(certified on RHEL7\)  | 256 TiB | 16 TiB | 

\* [https://ext4.wiki.kernel.org/index.php/Ext4_Howto](https://ext4.wiki.kernel.org/index.php/Ext4_Howto) and [https://access.redhat.com/solutions/1532](https://access.redhat.com/solutions/1532)

\*\* [https://access.redhat.com/solutions/1532](https://access.redhat.com/solutions/1532)

## Recommendations for Linux Volumes<a name="linux-volumes"></a>

Linux AMIs require a GUID partition table \(GPT\) and GRUB 2 for boot volumes that are 2 TiB \(2,048 GiB\) or larger\. Many Linux AMIs today still use the MBR partitioning scheme, which only supports boot\-volume sizes up to 2 TiB\. If your instance does not boot with a boot volume that is 2 TiB or larger, the AMI you are using may be limited to a 2 TiB GiB boot volume size\. Non\-boot volumes do not have this limitation on Linux instances\. For recommendations for Windows volumes, see [Recommendations for Windows Volumes](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/volume_constraints.html) in the *Amazon EC2 User Guide for Windows Instances*\.

Before attempting to resize a boot volume beyond 2 TiB, you can determine whether the volume is using MBR or GPT partitioning by running the following command on your instance:

```
[ec2-user ~]$ sudo gdisk -l /dev/xvda
```

An Amazon Linux instance with GPT partitioning returns the following information:

```
GPT fdisk (gdisk) version 0.8.10

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.
```

A SUSE instance with MBR partitioning returns the following information:

```
GPT fdisk (gdisk) version 0.8.8

Partition table scan:
  MBR: MBR only
  BSD: not present
  APM: not present
  GPT: not present
```
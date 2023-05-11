# Amazon EBS volume types<a name="ebs-volume-types"></a>

Amazon EBS provides the following volume types, which differ in performance characteristics and price, so that you can tailor your storage performance and cost to the needs of your applications\. 

**Important**  
There are several factors that can affect the performance of EBS volumes, such as instance configuration, I/O characteristics, and workload demand\. To fully use the IOPS provisioned on an EBS volume, use [EBS\-optimized instances](ebs-optimized.md)\. For more information about getting the most out of your EBS volumes, see [Amazon EBS volume performance on Linux instances](EBSPerformance.md)\.

For more information about pricing, see [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing/)\.

**Volume types**
+ [Solid state drive \(SSD\) volumes](#vol-type-ssd)
+ [Hard disk drive \(HDD\) volumes](#vol-type-hdd)
+ [Previous generation volumes](#vol-type-prev)

## Solid state drive \(SSD\) volumes<a name="vol-type-ssd"></a>

SSD\-backed volumes are optimized for transactional workloads involving frequent read/write operations with small I/O size, where the dominant performance attribute is IOPS\. SSD\-backed volume types include **General Purpose SSD** and **Provisioned IOPS SSD **\. The following is a summary of the use cases and characteristics of SSD\-backed volumes\.


|  | [General Purpose SSD volumes](general-purpose.md) | [Provisioned IOPS SSD volumes](provisioned-iops.md) | 
| --- | --- | --- | 
| Volume type | gp3 | gp2 | io2 Block Express ‡ | io2      | io1 | 
| Durability | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 99\.999% durability \(0\.001% annual failure rate\) | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 
| Use cases |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  |  Workloads that require: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  | 
| Volume size | 1 GiB \- 16 TiB  | 4 GiB \- 64 TiB | 4 GiB \- 16 TiB  | 
| Max IOPS per volume \(16 KiB I/O\) | 16,000 | 256,000 | 64,000 † | 
| Max throughput per volume | 1,000 MiB/s | 250 MiB/s \* | 4,000 MiB/s | 1,000 MiB/s † | 
| Amazon EBS Multi\-attach | Not supported | Supported | 
| Boot volume | Supported | 

\* The throughput limit is between 128 MiB/s and 250 MiB/s, depending on the volume size\. For more information, see [`gp2` volume performance](general-purpose.md#gp2-performance)\. Volumes created before **December 3, 2018** that have not been modified since creation might not reach full performance unless you [modify the volume](ebs-modify-volume.md)\.

† To achieve maximum throughput of 1,000 MiB/s, the volume must be provisioned with 64,000 IOPS and it must be attached to an [instance built on the Nitro System](instance-types.md#ec2-nitro-instances)\. `io1` volumes created before **December 6, 2017** and that have not been modified since creation, might not reach full performance unless you [modify the volume](ebs-modify-volume.md)\.

‡ `io2` Block Express volumes are supported with C6a, C6in, C7g, Inf2, M6a, M6in, M6idn, M7g, R5b, R6in, R6idn, R7g, Trn1, Trn1n, X2idn, and X2iedn instances\. `io2` volumes attached to these instances, during or after launch, automatically run on Block Express\. For more information, see [`io2` Block Express volumes](provisioned-iops.md#io2-block-express)\.

For more information about the SSD\-backed volume types, see the following:
+ [General Purpose SSD volumes](general-purpose.md)
+ [Provisioned IOPS SSD volumes](provisioned-iops.md)

## Hard disk drive \(HDD\) volumes<a name="vol-type-hdd"></a>

HDD\-backed volumes are optimized for large streaming workloads where the dominant performance attribute is throughput\. HDD volume types include ** Throughput Optimized HDD** and **Cold HDD**\. The following is a summary of the use cases and characteristics of HDD\-backed volumes\.


|  | [Throughput Optimized HDD volumes](hdd-vols.md#EBSVolumeTypes_st1) | [Cold HDD volumes](hdd-vols.md#EBSVolumeTypes_sc1) | 
| --- | --- | --- | 
| Volume type | st1 | sc1 | 
| Durability | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 
| Use cases |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  | 
| Volume size | 125 GiB \- 16 TiB | 
| Max IOPS per volume \(1 MiB I/O\) | 500 | 250 | 
| Max throughput per volume | 500 MiB/s | 250 MiB/s | 
| Amazon EBS Multi\-attach | Not supported | 
| Boot volume | Not supported | 

For more information about the Hard disk drives \(HDD\) volumes, see [Throughput Optimized HDD and Cold HDD volumes](hdd-vols.md)\.

## Previous generation volumes<a name="vol-type-prev"></a>

Magnetic \(`standard`\) volumes are previous generation volumes that are backed by magnetic drives\. They are suited for workloads with small datasets where data is accessed infrequently and performance is not of primary importance\. These volumes deliver approximately 100 IOPS on average, with burst capability of up to hundreds of IOPS, and they can range in size from 1 GiB to 1 TiB\.

**Tip**  
Magnetic is a previous generation volume type\. If you need higher performance or performance consistency than previous\-generation volumes can provide, we recommend using one of the newer volume types\.

The following table describes previous\-generation EBS volume types\.


|  | Magnetic | 
| --- | --- | 
| Volume type | standard | 
| Use cases | Workloads where data is infrequently accessed | 
| Volume size | 1 GiB\-1 TiB | 
| Max IOPS per volume | 40–200 | 
| Max throughput per volume | 40–90 MiB/s | 
| Boot volume | Supported | 

For more information, see [ Previous Generation Volumes](http://aws.amazon.com/ebs/previous-generation/)\.
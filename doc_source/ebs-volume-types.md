# Amazon EBS volume types<a name="ebs-volume-types"></a>

Amazon EBS provides the following volume types, which differ in performance characteristics and price, so that you can tailor your storage performance and cost to the needs of your applications\. 
+ **Solid state drives \(SSD\)** — Optimized for transactional workloads involving frequent read/write operations with small I/O size, where the dominant performance attribute is IOPS\. SSD\-backed volume types include:
  + [General Purpose SSD volumes](general-purpose.md)
  + [Provisioned IOPS SSD volumes](provisioned-iops.md)
+ **Hard disk drives \(HDD\)** — Optimized for large streaming workloads where the dominant performance attribute is throughput\. HDD\-backed volume types include [Throughput Optimized HDD and Cold HDD volumes](hdd-vols.md)\.
+ **Previous generation** — Hard disk drives that you can use for workloads with small datasets where data is accessed infrequently and performance is not of primary importance\. We recommend that you consider a current generation volume type instead\. For more information, see [Previous generation Magnetic volumes](EBSVolumeTypes_standard.md)\.

**Tip**  
There are several factors that can affect the performance of EBS volumes, such as instance configuration, I/O characteristics, and workload demand\. To fully use the IOPS provisioned on an EBS volume, use [EBS\-optimized instances](ebs-optimized.md)\. For more information about getting the most out of your EBS volumes, see [Amazon EBS volume performance on Linux instances](EBSPerformance.md)\.

For more information about pricing, see [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing/)\.

**Topics**
+ [General Purpose SSD volumes](general-purpose.md)
+ [Provisioned IOPS SSD volumes](provisioned-iops.md)
+ [Throughput Optimized HDD and Cold HDD volumes](hdd-vols.md)
+ [Previous generation Magnetic volumes](EBSVolumeTypes_standard.md)
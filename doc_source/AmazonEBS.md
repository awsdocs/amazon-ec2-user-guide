# Amazon Elastic Block Store \(Amazon EBS\)<a name="AmazonEBS"></a>

Amazon Elastic Block Store \(Amazon EBS\) provides block level storage volumes for use with EC2 instances\. EBS volumes behave like raw, unformatted block devices\. You can mount these volumes as devices on your instances\. EBS volumes that are attached to an instance are exposed as storage volumes that persist independently from the life of the instance\. You can create a file system on top of these volumes, or use them in any way you would use a block device \(such as a hard drive\)\. You can dynamically change the configuration of a volume attached to an instance\.

We recommend Amazon EBS for data that must be quickly accessible and requires long\-term persistence\. EBS volumes are particularly well\-suited for use as the primary storage for file systems, databases, or for any applications that require fine granular updates and access to raw, unformatted, block\-level storage\. Amazon EBS is well suited to both database\-style applications that rely on random reads and writes, and to throughput\-intensive applications that perform long, continuous reads and writes\.

With Amazon EBS, you pay only for what you use\. For more information about Amazon EBS pricing, see the Projecting Costs section of the [Amazon Elastic Block Store page](http://aws.amazon.com/ebs/)\.

**Topics**
+ [Features of Amazon EBS](#ebs-features)
+ [Amazon EBS volumes](ebs-volumes.md)
+ [Amazon EBS snapshots](EBSSnapshots.md)
+ [Amazon Data Lifecycle Manager](snapshot-lifecycle.md)
+ [Amazon EBS data services](ebs-data-services.md)
+ [Amazon EBS and NVMe on Linux instances](nvme-ebs-volumes.md)
+ [Amazon EBS–optimized instances](ebs-optimized.md)
+ [Amazon EBS volume performance on Linux instances](EBSPerformance.md)
+ [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)
+ [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)
+ [Amazon EBS quotas](ebs-resource-quotas.md)

## Features of Amazon EBS<a name="ebs-features"></a>
+ You create an EBS volume in a specific Availability Zone, and then attach it to an instance in that same Availability Zone\. To make a volume available outside of the Availability Zone, you can create a snapshot and restore that snapshot to a new volume anywhere in that Region\. You can copy snapshots to other Regions and then restore them to new volumes there, making it easier to leverage multiple AWS Regions for geographical expansion, data center migration, and disaster recovery\.
+ Amazon EBS provides the following volume types: General Purpose SSD, Provisioned IOPS SSD, Throughput Optimized HDD, and Cold HDD\. For more information, see [EBS volume types](ebs-volume-types.md)\.

  The following is a summary of performance and use cases for each volume type\.
  + General Purpose SSD volumes \(`gp2` and `gp3`\) balance price and performance for a wide variety of transactional workloads\. These volumes are ideal for use cases such as boot volumes, medium\-size single instance databases, and development and test environments\.
  + Provisioned IOPS SSD volumes \(`io1` and `io2`\) are designed to meet the needs of I/O\-intensive workloads that are sensitive to storage performance and consistency\. They provide a consistent IOPS rate that you specify when you create the volume\. This enables you to predictably scale to tens of thousands of IOPS per instance\. Additionally, io2 volumes provide the highest levels of volume durability\.
  + Throughput Optimized HDD volumes \(`st1`\) provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. These volumes are ideal for large, sequential workloads such as Amazon EMR, ETL, data warehouses, and log processing\.
  + Cold HDD volumes \(`sc1`\) provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. These volumes are ideal for large, sequential, cold\-data workloads\. If you require infrequent access to your data and are looking to save costs, these volumes provides inexpensive block storage\.
+ You can create your EBS volumes as encrypted volumes, in order to meet a wide range of data\-at\-rest encryption requirements for regulated/audited data and applications\. When you create an encrypted EBS volume and attach it to a supported instance type, data stored at rest on the volume, disk I/O, and snapshots created from the volume are all encrypted\. The encryption occurs on the servers that host EC2 instances, providing encryption of data\-in\-transit from EC2 instances to EBS storage\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.
+ You can create point\-in\-time snapshots of EBS volumes, which are persisted to Amazon S3\. Snapshots protect data for long\-term durability, and they can be used as the starting point for new EBS volumes\. The same snapshot can be used to instantiate as many volumes as you wish\. These snapshots can be copied across AWS Regions\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\. 
+ Performance metrics, such as bandwidth, throughput, latency, and average queue length, are available through the AWS Management Console\. These metrics, provided by Amazon CloudWatch, allow you to monitor the performance of your volumes to make sure that you are providing enough performance for your applications without paying for resources you don't need\. For more information, see [Amazon EBS volume performance on Linux instances](EBSPerformance.md)\.
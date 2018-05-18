# Amazon EC2 Instance Configuration<a name="ebs-ec2-config"></a>

When you plan and configure EBS volumes for your application, it is important to consider the configuration of the instances that you will attach the volumes to\. In order to get the most performance out of your EBS volumes, you should attach them to an instance with enough bandwidth to support your volumes, such as an EBS\-optimized instance or an instance with 10 Gigabit network connectivity\. This is especially important when you stripe multiple volumes together in a RAID configuration\. 

**Use EBS\-Optimized or 10 Gigabit Network Instances**  
Any performance\-sensitive workloads that require minimal variability and dedicated Amazon EC2 to Amazon EBS traffic, such as production databases or business applications, should use volumes that are attached to an EBS\-optimized instance or an instance with 10 Gigabit network connectivity\. EC2 instances that do not meet this criteria offer no guarantee of network resources\. The only way to ensure sustained reliable network bandwidth between your EC2 instance and your EBS volumes is to launch the EC2 instance as EBS\-optimized or choose an instance type with 10 Gigabit network connectivity\. To see which instance types include 10 Gigabit network connectivity, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. For information about configuring EBS\-optimized instances, see [Amazon EBS–Optimized Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html)\.

**Choose an EC2 Instance with Enough Bandwidth**  
Launching an instance that is EBS\-optimized provides you with a dedicated connection between your EC2 instance and your EBS volume\. However, it is still possible to provision EBS volumes that exceed the available bandwidth for certain instance types, especially when multiple volumes are striped in a RAID configuration\. The following table shows which instance types are available to be launched as EBS\-optimized, the dedicated throughput to instance types are available to be launched as EBS\-optimized, the dedicated bandwidth to Amazon EBS, the maximum amount of IOPS the instance can support if you are using a 16 KB I/O size, and the approximate I/O bandwidth available on that connection in MB/s\. Be sure to choose an EBS\-optimized instance that provides more dedicated EBS throughput than your application needs; otherwise, the Amazon EBS to Amazon EC2 connection will become a performance bottleneck\.

**Note**  
The table below and the following examples use 16 KB as an I/O size for explanatory purposes only; your application I/O size may vary \(Amazon EBS measures each I/O operation per second that is 256 KiB or smaller as one IOPS\)\. For more information about IOPS and the relationship between I/O size and volume throughput limits, see [I/O Characteristics and Monitoring](ebs-io-characteristics.md)\.


| Instance type | EBS\-optimized by default | Maximum bandwidth \(Mbps\) | Maximum throughput \(MB/s, 128 KB I/O\) | Maximum IOPS \(16 KB I/O\) | 
| --- | --- | --- | --- | --- | 
| c4\.large | Yes | 500 | 62\.5 | 4,000 | 
| c4\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| c4\.2xlarge | Yes | 1,000 | 125 | 8,000 | 
| c4\.4xlarge | Yes | 2,000 | 250 | 16,000 | 
| c4\.8xlarge | Yes | 4,000 | 500 | 32,000 | 
| c5\.large \* | Yes | 2,250 | 281\.25 | 16,000 | 
| c5\.xlarge \* | Yes | 2,250 | 281\.25 | 16,000 | 
| c5\.2xlarge \* | Yes | 2,250 | 281\.25 | 16,000 | 
| c5\.4xlarge | Yes | 2,250 | 281\.25 | 16,000 | 
| c5\.9xlarge | Yes | 4,500 | 562\.5 | 32,000 | 
| c5\.18xlarge | Yes | 9,000 | 1,125 | 64,000 | 
| d2\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| d2\.2xlarge | Yes | 1,000 | 125 | 8,000 | 
| d2\.4xlarge | Yes | 2,000 | 250 | 16,000 | 
| d2\.8xlarge | Yes | 4,000 | 500 | 32,000 | 
| f1\.2xlarge | Yes | 1,700 | 212\.5 | 12,000 | 
| f1\.16xlarge | Yes | 14,000 | 1,750 | 75,000 | 
| g3\.4xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| g3\.8xlarge | Yes | 7,000 | 875 | 40,000 | 
| g3\.16xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| h1\.2xlarge | Yes | 1,750 | 218\.75 | 12,000 | 
| h1\.4xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| h1\.8xlarge | Yes | 7,000 | 875 | 40,000 | 
| h1\.16xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| i3\.large | Yes | 425 | 53\.13 | 3000 | 
| i3\.xlarge | Yes | 850 | 106\.25 | 6000 | 
| i3\.2xlarge | Yes | 1,700 | 212\.5 | 12,000 | 
| i3\.4xlarge | Yes | 3,500 | 437\.5 | 16,000 | 
| i3\.8xlarge | Yes | 7,000 | 875 | 32,500 | 
| i3\.16xlarge | Yes | 14,000 | 1,750 | 65,000 | 
| m4\.large | Yes | 450 | 56\.25 | 3,600 | 
| m4\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| m4\.2xlarge | Yes | 1,000 | 125 | 8,000 | 
| m4\.4xlarge | Yes | 2,000 | 250 | 16,000 | 
| m4\.10xlarge | Yes | 4,000 | 500 | 32,000 | 
| m4\.16xlarge | Yes | 10,000 | 1,250 | 65,000 | 
| m5\.large\* | Yes | 2,120 | 265 | 16,000 | 
| m5\.xlarge\* | Yes | 2,120 | 265 | 16,000 | 
| m5\.2xlarge\* | Yes | 2,120 | 265 | 16,000 | 
| m5\.4xlarge | Yes | 2,120 | 265 | 16,000 | 
| m5\.12xlarge | Yes | 5,000 | 625 | 32,500 | 
| m5\.24xlarge | Yes | 10,000 | 1,250 | 65,000 | 
| p2\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| p2\.8xlarge | Yes | 5,000 | 625 | 32,500 | 
| p2\.16xlarge | Yes | 10,000 | 1,250 | 65,000 | 
| p3\.2xlarge | Yes | 1,750 | 218 | 10,000 | 
| p3\.8xlarge | Yes | 7,000 | 875 | 40,000 | 
| p3\.16xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| r4\.large | Yes | 425 | 53\.13 | 3,000 | 
| r4\.xlarge | Yes | 850 | 106\.25 | 6,000 | 
| r4\.2xlarge | Yes | 1,700 | 212\.5 | 12,000 | 
| r4\.4xlarge | Yes | 3,500 | 437\.5 | 18,750 | 
| r4\.8xlarge | Yes | 7,000 | 875 | 37,500 | 
| r4\.16xlarge | Yes | 14,000 | 1,750 | 75,000 | 
| x1\.16xlarge | Yes | 7,000 | 875 | 40,000 | 
| x1\.32xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| x1e\.xlarge | Yes | 500 | 62\.5 | 3,700 | 
| x1e\.2xlarge | Yes | 1,000 | 125 | 7,400 | 
| x1e\.4xlarge | Yes | 1,750 | 218\.75 | 10,000 | 
| x1e\.8xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| x1e\.16xlarge | Yes | 7,000 | 875 | 40,000 | 
| x1e\.32xlarge | Yes | 14,000 | 1,750 | 80,000 | 

\* These instance types can support maximum performance for 30 minutes at least once every 24 hours\. For example, `c5.large` instances can deliver 281 MB/s for 30 minutes at least once every 24 hours\. If you have a workload that requires sustained maximum performance for longer than 30 minutes, select an instance type according to baseline performance as shown in the table below\.

The `EBSIOBalance%` and `EBSByteBalance%` metrics can help you determine if you have rightsized your instances\. You can view these metrics in the CloudWatch console and set an alarm that will be triggered based on your thresholds\. These metrics are expressed as a percentage\. Instances with a consistently low balance percentage are candidates for upsizing\. Instances where the balance percentage never drops below 100% are candidates for downsizing\. For more information, see [Monitoring Your Instances Using CloudWatch](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-cloudwatch.html)\.


| Instance type | Baseline bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KB I/O\) | Baseline IOPS \(16 KB I/O\) | 
| --- | --- | --- | --- | 
| c5\.large | 525 | 66 | 4,000 | 
| c5\.xlarge | 800 | 100 | 6,000 | 
| c5\.2xlarge | 1,125 | 141 | 8,000 | 
| m5\.large | 480 | 60 | 3,600 | 
| m5\.xlarge | 800 | 100 | 6,000 | 
| m5\.2xlarge | 1,166 | 146 | 8,333 | 

Note that some instances with 10\-gigabit network interfaces, such as `i2.8xlarge`, `c3.8xlarge`, and `r3.8xlarge`, do not offer EBS\-optimization, and therefore do not have dedicated EBS bandwidth available and are not listed here\. However, you can use all of that bandwidth for traffic to Amazon EBS if your application isn’t pushing other network traffic that contends with Amazon EBS\. Some other 10\-gigabit network instances, such as `c4.8xlarge` and `d2.8xlarge` offer dedicated Amazon EBS bandwidth in addition to a 10\-gigabit interface which is used exclusively for network traffic\.

The `m1.large` instance has a maximum 16 KB IOPS value of 4,000, but unless this instance type is launched as EBS\-optimized, that value is an absolute best\-case scenario and is not guaranteed; to consistently achieve 4,000 16 KB IOPS, you must launch this instance as EBS\-optimized\. However, if a 4,000 IOPS `io1` volume is attached to an EBS\-optimized `m1.large` instance, the Amazon EC2 to Amazon EBS connection bandwidth limit prevents this volume from providing the 320 MB/s maximum aggregate throughput available to it\. In this case, we must use an EBS\-optimized EC2 instance that supports at least 320 MB/s of throughput, such as the `c4.8xlarge` instance type\.

Volumes of type General Purpose SSD \(`gp2`\) have a throughput limit between 128 MB/s and 160 MB/s per volume \(depending on volume size\), which pairs well with a 1,000 Mbps EBS\-optimized connection\. Instance types that offer more than 1,000 Mbps of throughput to Amazon EBS can use more than one `gp2` volume to take advantage of the available throughput\. Volumes of type Provisioned IOPS SSD \(`io1`\) have a throughput limit range of 256 KiB for each IOPS provisioned, up to a maximum of 320 MiB/s \(at 1,280 IOPS\)\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.

Instance types with 10 Gigabit network connectivity support up to 800 MB/s of throughput and 48,000 16K IOPS for unencrypted Amazon EBS volumes and up to 25,000 16K IOPS for encrypted Amazon EBS volumes\. Because the maximum `io1` value for EBS volumes is 32,000 for `io1` volumes and 10,000 for `gp2` volumes, you can use several EBS volumes simultaneously to reach the level of I/O performance available to these instance types\. For more information about which instance types include 10 Gigabit network connectivity, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

You should use EBS\-optimized instances when available to get the full performance benefits of Amazon EBS `gp2` and `io1` volumes\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.
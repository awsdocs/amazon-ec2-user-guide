# General Purpose SSD volumes<a name="general-purpose"></a>

General Purpose SSD \(gp2 and gp3\) volumes are backed by solid\-state drives \(SSDs\)\. They balance price and performance for a wide variety of transactional workloads\. These include virtual desktops, medium\-sized single instance databases, latency sensitive interactive applications, development and test environments, and boot volumes\. We recommend these volumes for most workloads\.

Amazon EBS offers two types of General Purpose SSD volumes:
+ General Purpose SSD \(`gp3`\) volumes—latest generation General Purpose SSD volume
+ General Purpose SSD \(`gp2`\) volumes

**Topics**
+ [General Purpose SSD \(gp3\) volumes](#gp3-ebs-volume-type)
+ [General Purpose SSD \(gp2\) volumes](#EBSVolumeTypes_gp2)

## General Purpose SSD \(gp3\) volumes<a name="gp3-ebs-volume-type"></a>

General Purpose SSD \(gp3\) volumes are the latest generation of General Purpose SSD volumes, and the lowest cost SSD volume offered by Amazon EBS\. This volume type helps to provide the right balance of price and performance for most applications\. It also helps you to scale volume performance independently of volume size\. This means that you can provision the required performance without needing to provision additional block storage capacity\. Additionally, gp3 volumes offer a 20 percent lower price per GiB than General Purpose SSD \(gp2\) volumes\.

gp3 volumes provide single\-digit millisecond latency and 99\.8 percent to 99\.9 percent volume durability with an annual failure rate \(AFR\) no higher than 0\.2 percent, which translates to a maximum of two volume failures per 1,000 running volumes over a one\-year period\. AWS designs gp3 volumes to deliver their provisioned performance 99 percent of the time\.

**Topics**
+ [gp3 volume performance](#gp3-performance)
+ [gp3 volume size](#gp3-sie)
+ [Migrate to gp3 from gp2](#migrate-to-gp3)

### gp3 volume performance<a name="gp3-performance"></a>

**Tip**  
gp3 volumes do not use burst performance\. They can indefinitely sustain their full provisioned IOPS and throughput performance\.

**IOPS performance**  
gp3 volumes deliver a consistent baseline IOPS performance of 3,000 IOPS, which is included with the price of storage\. You can provision additional IOPS \(up to a maximum of 16,000\) for an additional cost at a ratio of 500 IOPS per GiB of volume size\. Maximum IOPS can be provisioned for volumes 32 GiB or larger \(500 IOPS per GiB × 32 GiB = 16,000 IOPS\)\.

**Throughput performance**  
gp3 volumes deliver a consistent baseline throughput performance of 125 MiB/s, which is included with the price of storage\. You can provision additional throughput \(up to a maximum of 1,000 MiB/s\) for an additional cost at a ratio of 0\.25 MiB/s per provisioned IOPS\. Maximum throughput can be provisioned at 4,000 IOPS or higher and 8 GiB or larger \(4,000 IOPS × 0\.25 MiB/s per IOPS = 1,000 MiB/s\)\.

### gp3 volume size<a name="gp3-sie"></a>

A gp3 volume can range in size from 1 GiB to 16 TiB\.

### Migrate to gp3 from gp2<a name="migrate-to-gp3"></a>

If you are currently using gp2 volumes, you can migrate your volumes to gp3 using [Amazon EBS Elastic Volumes](ebs-modify-volume.md) operations\. You can use Amazon EBS Elastic Volumes operations to modify the volume type, IOPS, and throughput of your existing volumes without interrupting your Amazon EC2 instances\. Also, when you create a new volume, launch a new instance, or create an AMI, you can select gp3 as the volume type at that point, instead of using the default gp2 volume type\.

To find out how much you can save by migrating your gp2 volumes to gp3, use the [Amazon EBS gp2 to gp3 migration cost savings calculator](http://aws.amazon.com/ebs/resources/)\.

## General Purpose SSD \(gp2\) volumes<a name="EBSVolumeTypes_gp2"></a>

General Purpose SSD \(`gp2`\) volumes are the default Amazon EBS volume type for Amazon EC2 instances\. They offer cost\-effective storage that is ideal for a broad range of transactional workloads\. With `gp2` volumes, performance scales with volume size\.

**Tip**  
`gp3` volumes are the latest generation of General Purpose SSD volumes\. They offer more predictable performance scaling and prices that are up to 20 percent lower than `gp2` volumes\. For more information, see [General Purpose SSD \(gp3\) volumes](#gp3-ebs-volume-type)\.   
To find out how much you can save by migrating your `gp2` volumes to `gp3`, use the [Amazon EBS gp2 to gp3 migration cost savings calculator](http://aws.amazon.com/ebs/resources/)\.

`gp2` volumes provide single\-digit millisecond latency and 99\.8 percent to 99\.9 percent volume durability with an annual failure rate \(AFR\) no higher than 0\.2 percent, which translates to a maximum of two volume failures per 1,000 running volumes over a one\-year period\. AWS designs `gp2` volumes to deliver their provisioned performance 99 percent of the time\.

**Topics**
+ [`gp2` volume performance](#gp2-performance)
+ [`gp2` volume size](#gp2-size)

### `gp2` volume performance<a name="gp2-performance"></a>

**IOPS performance**  
Baseline IOPS performance scales linearly between a minimum of 100 and a maximum of 16,000 at a rate of 3 IOPS per GiB of volume size\. IOPS performance is provisioned as follows:
+ Volumes 33\.33 GiB and smaller are provisioned with the minimum of 100 IOPS\.
+ Volumes larger than 33\.33 GiB are provisioned with 3 IOPS per GiB of volume size up to the maximum of 16,000 IOPS, which is reached at 5,334 GiB \(3 X 5,334\)\.
+ Volumes 5,334 GiB and larger are provisioned with 16,000 IOPS\.

`gp2` volumes smaller than 1 TiB \(and that are provisioned with less than 3,000 IOPS\) can **burst** to 3,000 IOPS when needed for an extended period of time\. A volume's ability to burst is governed by I/O credits\. When I/O demand is greater than baseline performance, the volume **spends I/O credits** to burst to the required performance level \(up to 3,000 IOPS\)\. While bursting, I/O credits are spent at a rate of 3 I/O credits per GiB of volume size per second\. The more I/O credits a volume has accrued, the longer it can sustain its burst performance\. You can calculate **Burst duration** as follows:

```
                        (I/O credit balance)
Burst duration  =  ------------------------------
                   (Burst IOPS) - (Baseline IOPS)
```

When I/O demand drops to baseline performance level or lower, the volume starts to **earn I/O credits** at a rate of 3 I/O credits per GiB of volume size per second\. Volumes have an **I/O credit accrual limit** of 5\.4 million I/O credits, which is enough to sustain the maximum burst performance of 3,000 IOPS for at least 30 minutes\.

**Note**  
Each volume receives an initial I/O credit balance of 5\.4 million I/O credits, which provides a fast initial boot cycle for boot volumes and a good bootstrapping experience for other applications\.

The following table lists example volume sizes and the associated baseline performance of the volume, the burst duration \(when starting with 5\.4 million I/O credits\), and the time needed to refill an empty I/O credits balance\.


|  Volume size \(GiB\)  |  Baseline performance \(IOPS\)  |  Burst duration at 3,000 IOPS \(seconds\)  |  Time to refill empty credit balance \(seconds\)  | 
| --- | --- | --- | --- | 
|  1 to 33\.33  |  100  |  1,862  | 54,000 | 
|  100  |  300  |  2,000  | 18,000 | 
|  334 \(min size for max throughput\)  | 1,002 |  2,703  |  5,389  | 
|  750  |  2,250  |  7,200  | 2,400 | 
|  1,000  |  3,000  |  N/A\*  |  N/A\*  | 
|  5,334 \(min size for max IOPS\) and larger  |  16,000  |  N/A\*  |  N/A\*  | 

\* The baseline performance of the volume exceeds the maximum burst performance\.

You can monitor the I/O credit balance for a volume using the Amazon EBS `BurstBalance` metric in Amazon CloudWatch\. This metric shows the percentage of I/O credits for `gp2` remaining\. For more information, see [I/O characteristics and monitoring](ebs-io-characteristics.md)\. You can set an alarm that notifies you when the `BurstBalance` value falls to a certain level\. For more information, see [ Creating CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.

**Throughput performance**  


`gp2` volumes deliver throughput between 128 MiB/s and 250 MiB/s, depending on the volume size\. Throughput performance is provisioned as follows:
+ Volumes that are 170 GiB and smaller deliver a maximum throughput of 128 MiB/s\.
+ Volumes larger than 170 GiB but smaller than 334 GiB can burst to a maximum throughput of 250 MiB/s\.
+ Volumes that are 334 GiB and larger deliver 250 MiB/s\.

Throughput for a `gp2` volume can be calculated using the following formula, up to the throughput limit of 250 MiB/s:

```
Throughput in MiB/s = IOPS performance × I/O size in KiB
```

### `gp2` volume size<a name="gp2-size"></a>

A `gp2` volume can range in size from 1 GiB to 16 TiB\. Keep in mind that volume performance scales linearly with the volume size\.
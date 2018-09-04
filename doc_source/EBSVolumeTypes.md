# Amazon EBS Volume Types<a name="EBSVolumeTypes"></a>

Amazon EBS provides the following volume types, which differ in performance characteristics and price, so that you can tailor your storage performance and cost to the needs of your applications\. The volumes types fall into two categories:
+ SSD\-backed volumes optimized for transactional workloads involving frequent read/write operations with small I/O size, where the dominant performance attribute is IOPS
+ HDD\-backed volumes optimized for large streaming workloads where throughput \(measured in MiB/s\) is a better performance measure than IOPS

The following table describes the use cases and performance characteristics for each volume type:


|  | Solid\-State Drives \(SSD\) | Hard disk Drives \(HDD\) | 
| --- | --- | --- | 
| Volume Type | General Purpose SSD \(gp2\)\* | Provisioned IOPS SSD \(io1\) | Throughput Optimized HDD \(st1\) | Cold HDD \(sc1\) | 
| Description | General purpose SSD volume that balances price and performance for a wide variety of workloads | Highest\-performance SSD volume for mission\-critical low\-latency or high\-throughput workloads  | Low cost HDD volume designed for frequently accessed, throughput\-intensive workloads | Lowest cost HDD volume designed for less frequently accessed workloads | 
| Use Cases |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSVolumeTypes.html)  | 
| API Name | gp2 | io1 | st1 | sc1 | 
| Volume Size | 1 GiB \- 16 TiB  | 4 GiB \- 16 TiB  | 500 GiB \- 16 TiB | 500 GiB \- 16 TiB  | 
| Max\. IOPS\*\*/Volume | 10,000 | 32,000\*\*\* | 500 | 250 | 
| Max\. Throughput/Volume | 160 MiB/s\*\*\*\* | 500 MiB/s† | 500 MiB/s | 250 MiB/s | 
| Max\. IOPS/Instance | 80,000 | 80,000 | 80,000 | 80,000 | 
| Max\. Throughput/Instance†† | 1,750 MiB/s | 1,750 MiB/s | 1,750 MiB/s | 1,750 MiB/s | 
| Dominant Performance Attribute | IOPS | IOPS | MiB/s | MiB/s | 

\* Default volume type for EBS volumes created from the console is `gp2`\. Volumes created using the `CreateVolume` API without a volume\-type argument default to either `gp2` or `standard` according to region:
+ `standard`: us\-east\-1, eu\-west\-1, eu\-central\-1, us\-west\-2, us\-west\-1, sa\-east\-1, ap\-northeast\-1, ap\-northeast\-2, ap\-southeast\-1, ap\-southeast\-2, ap\-south\-1, us\-gov\-west\-1, cn\-north\-1
+ `gp2`: All other regions

\*\* `gp2`/`io1` based on 16 KiB I/O size, `st1`/`sc1` based on 1 MiB I/O size

\*\*\* `io1` volumes created in regions ap\-northeast\-3 and us\-gov\-west\-1 are subject to a 20,000 IOPS limit\.

\*\*\*\* General Purpose SSD \(gp2\) volumes have a throughput limit between 128 MiB/s and 160 MiB/s depending on volume size\. Volumes greater than 170 GiB up to 214 GiB deliver a maximum throughput of 160 MiB/s if burst credits are available\. Volumes above 214 GiB deliver 160 MiB/s irrespective of burst credits\.

† An `io1` volume created before 12/6/2017 will not achieve this throughput until modified in some way\. For more information, see [Modifying the Size, IOPS, or Type of an EBS Volume on Linux](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-modify-volume.html)\.

†† To achieve this throughput, you must have an instance that supports it\. For more information, see [Amazon EBS–Optimized Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html)\.

The following table describes previous\-generation EBS volume types\. If you need higher performance or performance consistency than previous\-generation volumes can provide, we recommend that you consider using General Purpose SSD \(`gp2`\) or other current volume types\. For more information, see [Previous Generation Volumes](https://aws.amazon.com/ebs/previous-generation/)\.


| Previous Generation Volumes | 
| --- | 
| Volume Type | EBS Magnetic | 
| Description | Previous generation HDD | 
| Use Cases | Workloads where data is infrequently accessed | 
| API Name | standard | 
| Volume Size | 1 GiB\-1 TiB | 
| Max\. IOPS/Volume | 40–200 | 
| Max\. Throughput/Volume | 40–90 MiB/s | 
| Max\. IOPS/Instance | 80,000 | 
| Max\. Throughput/Instance | 1,750 MiB/s | 
| Dominant Performance Attribute | IOPS | 

**Note**  
Linux AMIs require GPT partition tables and GRUB 2 for boot volumes 2 TiB \(2048 GiB\) or larger\. Many Linux AMIs today use the MBR partitioning scheme, which only supports up to 2047 GiB boot volumes\. If your instance does not boot with a boot volume that is 2 TiB or larger, the AMI you are using may be limited to a 2047 GiB boot volume size\. Non\-boot volumes do not have this limitation on Linux instances\.

There are several factors that can affect the performance of EBS volumes, such as instance configuration, I/O characteristics, and workload demand\. For more information about getting the most out of your EBS volumes, see [Amazon EBS Volume Performance on Linux Instances](EBSPerformance.md)\.

For more information about pricing for these volume types, see [Amazon EBS Pricing](https://aws.amazon.com/ebs/pricing/)\.

## General Purpose SSD \(`gp2`\) Volumes<a name="EBSVolumeTypes_gp2"></a>

General Purpose SSD \(`gp2`\) volumes offer cost\-effective storage that is ideal for a broad range of workloads\. These volumes deliver single\-digit millisecond latencies and the ability to burst to 3,000 IOPS for extended periods of time\. Between a minimum of 100 IOPS \(at 33\.33 GiB and below\) and a maximum of 10,000 IOPS \(at 3,334 GiB and above\), baseline performance scales linearly at 3 IOPS per GiB of volume size\. AWS designs `gp2` volumes to deliver the provisioned performance 99% of the time\. A `gp2` volume can range in size from 1 GiB to 16 TiB\.

### I/O Credits and Burst Performance<a name="IOcredit"></a>

The performance of `gp2` volumes is tied to volume size, which determines the baseline performance level of the volume and how quickly it accumulates I/O credits; larger volumes have higher baseline performance levels and accumulate I/O credits faster\. I/O credits represent the available bandwidth that your `gp2` volume can use to burst large amounts of I/O when more than the baseline performance is needed\. The more credits your volume has for I/O, the more time it can burst beyond its baseline performance level and the better it performs when more performance is needed\. The following diagram shows the burst\-bucket behavior for `gp2`\.

![\[gp2 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/gp2-burst-bucket.png)

Each volume receives an initial I/O credit balance of 5\.4 million I/O credits, which is enough to sustain the maximum burst performance of 3,000 IOPS for 30 minutes\. This initial credit balance is designed to provide a fast initial boot cycle for boot volumes and to provide a good bootstrapping experience for other applications\. Volumes earn I/O credits at the baseline performance rate of 3 IOPS per GiB of volume size\. For example, a 100 GiB `gp2` volume has a baseline performance of 300 IOPS\.

![\[Comparing baseline performance and burst IOPS\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/gp2_iops.png)

When your volume requires more than the baseline performance I/O level, it draws on I/O credits in the credit balance to burst to the required performance level, up to a maximum of 3,000 IOPS\. Volumes larger than 1,000 GiB have a baseline performance that is equal or greater than the maximum burst performance, and their I/O credit balance never depletes\. When your volume uses fewer I/O credits than it earns in a second, unused I/O credits are added to the I/O credit balance\. The maximum I/O credit balance for a volume is equal to the initial credit balance \(5\.4 million I/O credits\)\.

The following table lists several volume sizes and the associated baseline performance of the volume \(which is also the rate at which it accumulates I/O credits\), the burst duration at the 3,000 IOPS maximum \(when starting with a full credit balance\), and the time in seconds that the volume would take to refill an empty credit balance\.


|  Volume size \(GiB\)  |  Baseline performance \(IOPS\)  |  Maximum burst duration @ 3,000 IOPS \(seconds\)  |  Seconds to fill empty credit balance  | 
| --- | --- | --- | --- | 
|  1  |  100  |  1862  | 54,000 | 
|  100  |  300  |  2,000  | 18,000 | 
|  214 \(Min\. size for max\. throughput\)  |  642  |  2,290  |  8,412  | 
|  250  |  750  | 2,400 | 7,200 | 
|  500  |  1,500  |  3,600  | 3,600 | 
|  750  |  2,250  |  7,200  | 2,400 | 
|  1,000  |  3,000  |  N/A\*  |  N/A\*  | 
|  3,334 \(Min\. size for max\. IOPS\)  |  10,000  |  N/A\*  |  N/A\*  | 
|  16,384 \(16 TiB, max\. volume size\)  |  10,000  |  N/A\*  |  N/A\*  | 

\* Bursting and I/O credits are only relevant to volumes under 1,000 GiB, where burst performance exceeds baseline performance\.

The burst duration of a volume is dependent on the size of the volume, the burst IOPS required, and the credit balance when the burst begins\. This is shown in the following equation:

```
                             (Credit balance)
Burst duration  =  ------------------------------------
                   (Burst IOPS) - 3(Volume size in GiB)
```

**What happens if I empty my I/O credit balance?**  
If your `gp2` volume uses all of its I/O credit balance, the maximum IOPS performance of the volume remains at the baseline IOPS performance level \(the rate at which your volume earns credits\) and the volume's maximum throughput is reduced to the baseline IOPS multiplied by the maximum I/O size\. Throughput can never exceed 160 MiB/s\. When I/O demand drops below the baseline level and unused credits are added to the I/O credit balance, the maximum IOPS performance of the volume again exceeds the baseline\. For example, a 100 GiB `gp2` volume with an empty credit balance has a baseline performance of 300 IOPS and a throughput limit of 75 MiB/s \(300 I/O operations per second \* 256 KiB per I/O operation = 75 MiB/s\)\. The larger a volume is, the greater the baseline performance is and the faster it replenishes the credit balance\. For more information about how IOPS are measured, see [I/O Characteristics](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-io-characteristics.html)\.

If you notice that your volume performance is frequently limited to the baseline level \(due to an empty I/O credit balance\), you should consider using a larger `gp2` volume \(with a higher baseline performance level\) or switching to an `io1` volume for workloads that require sustained IOPS performance greater than 10,000 IOPS\.

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the Burst Bucket Balance for `gp2`, `st1`, and `sc1` Volumes](#monitoring_burstbucket)\.

### Throughput Performance<a name="GP2Throughput"></a>

Throughput for a `gp2` volume can be calculated using the following formula, up to the throughput limit of 160 MiB/s:

```
Throughput in MiB/s = (Volume size in GiB) × (IOPS per GiB) × (I/O size in KiB)
```

Assuming V = volume size, I = I/O size, R = I/O rate, and T = throughput, this can be simplified to: 

```
T = VIR
```

The smallest volume size that achieves the maximum throughput is given by: 

```
        T          
V  =  -----  
       I R   

           (160 MiB/s)
   =  ---------------------
      (256 KiB)(3 IOPS/GiB)


               [(160)(2^20)(Bytes)]/(s)
   =  ------------------------------------------
      (256)(2^10)(Bytes)(3 IOPS/[(2^30)(Bytes)])


      (160)(2^20)(2^30)(Bytes)
   =  ------------------------
           (256)(2^10)(3)

   =  229064920000 Bytes
   
   =  213 GiB
```

## Provisioned IOPS SSD \(`io1`\) Volumes<a name="EBSVolumeTypes_piops"></a>

Provisioned IOPS SSD \(`io1`\) volumes are designed to meet the needs of I/O\-intensive workloads, particularly database workloads, that are sensitive to storage performance and consistency\. Unlike `gp2`, which uses a bucket and credit model to calculate performance, an `io1` volume allows you to specify a consistent IOPS rate when you create the volume, and Amazon EBS delivers within 10 percent of the provisioned IOPS performance 99\.9 percent of the time over a given year\. 

An `io1` volume can range in size from 4 GiB to 16 TiB and you can provision 100 up to 32,000 IOPS per volume\. The maximum ratio of provisioned IOPS to requested volume size \(in GiB\) is 50:1\. For example, a 100 GiB volume can be provisioned with up to 5,000 IOPS\. Any volume 640 GiB in size or greater allows provisioning up to the 32,000 IOPS maximum \(50 × 640 GiB = 32,000\)\.

The throughput limit of `io1` volumes is 256 KiB/s for each IOPS provisioned, up to a maximum of 500 MiB/s \(at 32,000 IOPS\)\.

![\[Throughput limits for io1 volumes\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/io1_throughput.png)

Your per\-I/O latency experience depends on the IOPS provisioned and your workload pattern\. For the best per\-I/O latency experience, we recommend that you provision an IOPS\-to\-GiB ratio greater than 2:1\. For example, a 2,000 IOPS volume should be smaller than 1,000 GiB\.

**Note**  
Some AWS accounts created before 2012 might have access to Availability Zones in us\-west\-1 or ap\-northeast\-1 that do not support Provisioned IOPS SSD \(`io1`\) volumes\. If you are unable to create an `io1` volume \(or launch an instance with an `io1` volume in its block device mapping\) in one of these regions, try a different Availability Zone in the region\. You can verify that an Availability Zone supports `io1` volumes by creating a 4 GiB `io1` volume in that zone\.

## Throughput Optimized HDD \(`st1`\) Volumes<a name="EBSVolumeTypes_st1"></a>

Throughput Optimized HDD \(`st1`\) volumes provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. This volume type is a good fit for large, sequential workloads such as Amazon EMR, ETL, data warehouses, and log processing\. Bootable `st1` volumes are not supported\. 

Throughput Optimized HDD \(`st1`\) volumes, though similar to Cold HDD \(`sc1`\) volumes, are designed to support *frequently* accessed data\.

This volume type is optimized for workloads involving large, sequential I/O, and we recommend that customers with workloads performing small, random I/O use `gp2`\. For more information, see [**Inefficiency of Small Read/Writes on HDD**](#inefficiency)\.

### Throughput Credits and Burst Performance<a name="ST1ThroughputBurst"></a>

Like `gp2`, `st1` uses a burst\-bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

The following diagram shows the burst\-bucket behavior for `st1`\.

![\[st1 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/st1-burst-bucket.png)

Subject to throughput and throughput\-credit caps, the available throughput of an `st1` volume is expressed by the following formula:

```
(Volume size) x (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB `st1` volume, burst throughput is limited to 250 MiB/s, the bucket fills with credits at 40 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

Larger volumes scale these limits linearly, with throughput capped at a maximum of 500 MiB/s\. After the bucket is depleted, throughput is limited to the baseline rate of 40 MiB/s per TiB\. 

On volume sizes ranging from 0\.5 to 16 TiB, baseline throughput varies from 20 to a cap of 500 MiB/s, which is reached at 12\.5 TiB as follows:

```
            40 MiB/s
12.5 TiB x ---------- = 500 MiB/s
             1 TiB
```

Burst throughput varies from 125 MiB/s to a cap of 500 MiB/s, which is reached at 2 TiB as follows:

```
         250 MiB/s
2 TiB x ---------- = 500 MiB/s
          1 TiB
```

The following table states the full range of base and burst throughput values for `st1`:


| Volume Size \(TiB\) | ST1 Base Throughput \(MiB/s\) | ST1 Burst Throughput \(MiB/s\) | 
| --- | --- | --- | 
| 0\.5 | 20 | 125 | 
| 1 | 40 | 250 | 
| 2 | 80 | 500 | 
| 3 | 120 | 500 | 
| 4 | 160 | 500 | 
| 5 | 200 | 500 | 
| 6 | 240 | 500 | 
| 7 | 280 | 500 | 
| 8 | 320 | 500 | 
| 9 | 360 | 500 | 
| 10 | 400 | 500 | 
| 11 | 440 | 500 | 
| 12 | 480 | 500 | 
| 12\.5 | 500 | 500 | 
| 13 | 500 | 500 | 
| 14 | 500 | 500 | 
| 15 | 500 | 500 | 
| 16 | 500 | 500 | 

The following diagram plots the table values:

![\[Comparing st1 base and burst throughput\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/st1_base_v_burst.png)

**Note**  
When you create a snapshot of a Throughput Optimized HDD \(`st1`\) volume, performance may drop as far as the volume's baseline value while the snapshot is in progress\.

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the Burst Bucket Balance for `gp2`, `st1`, and `sc1` Volumes](#monitoring_burstbucket)\.

## Cold HDD \(`sc1`\) Volumes<a name="EBSVolumeTypes_sc1"></a>

Cold HDD \(`sc1`\) volumes provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. With a lower throughput limit than `st1`, `sc1` is a good fit ideal for large, sequential cold\-data workloads\. If you require infrequent access to your data and are looking to save costs, `sc1` provides inexpensive block storage\. Bootable `sc1` volumes are not supported\.

Cold HDD \(`sc1`\) volumes, though similar to Throughput Optimized HDD \(`st1`\) volumes, are designed to support *infrequently* accessed data\.

**Note**  
This volume type is optimized for workloads involving large, sequential I/O, and we recommend that customers with workloads performing small, random I/O use `gp2`\. For more information, see [**Inefficiency of Small Read/Writes on HDD**](#inefficiency)\.

### Throughput Credits and Burst Performance<a name="SC1ThroughputBurst"></a>

Like `gp2`, `sc1` uses a burst\-bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

![\[sc1 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/sc1-burst-bucket.png)

Subject to throughput and throughput\-credit caps, the available throughput of an `sc1` volume is expressed by the following formula:

```
(Volume size) x (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB `sc1` volume, burst throughput is limited to 80 MiB/s, the bucket fills with credits at 12 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

Larger volumes scale these limits linearly, with throughput capped at a maximum of 250 MiB/s\. After the bucket is depleted, throughput is limited to the baseline rate of 12 MiB/s per TiB\. 

On volume sizes ranging from 0\.5 to 16 TiB, baseline throughput varies from 6 MiB/s to a maximum of 192 MiB/s, which is reached at 16 TiB as follows:

```
           12 MiB/s
16 TiB x ---------- = 192 MiB/s
            1 TiB
```

Burst throughput varies from 40 MiB/s to a cap of 250 MiB/s, which is reached at 3\.125 TiB as follows:

```
             80 MiB/s
3.125 TiB x ----------- = 250 MiB/s
              1 TiB
```

The following table states the full range of base and burst throughput values for `sc1`:


| Volume Size \(TiB\) | SC1 Base Throughput \(MiB/s\) | SC1 Burst Throughput \(MiB/s\) | 
| --- | --- | --- | 
| 0\.5 | 6 | 40 | 
| 1 | 12 | 80 | 
| 2 | 24 | 160 | 
| 3 | 36 | 240 | 
| 3\.125 | 37\.5 | 250 | 
| 4 | 48 | 250 | 
| 5 | 60 | 250 | 
| 6 | 72 | 250 | 
| 7 | 84 | 250 | 
| 8 | 96 | 250 | 
| 9 | 108 | 250 | 
| 10 | 120 | 250 | 
| 11 | 132 | 250 | 
| 12 | 144 | 250 | 
| 13 | 156 | 250 | 
| 14 | 168 | 250 | 
| 15 | 180 | 250 | 
| 16 | 192 | 250 | 

The following diagram plots the table values:

![\[Comparing sc1 base and burst throughput\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/sc1_base_v_burst.png)

**Note**  
When you create a snapshot of a Cold HDD \(`sc1`\) volume, performance may drop as far as the volume's baseline value while the snapshot is in progress\.

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the Burst Bucket Balance for `gp2`, `st1`, and `sc1` Volumes](#monitoring_burstbucket)\.

## Magnetic \(`standard`\)<a name="EBSVolumeTypes_standard"></a>

Magnetic volumes are backed by magnetic drives and are suited for workloads where data is accessed infrequently, and scenarios where low\-cost storage for small volume sizes is important\. These volumes deliver approximately 100 IOPS on average, with burst capability of up to hundreds of IOPS, and they can range in size from 1 GiB to 1 TiB\.

**Note**  
Magnetic is a Previous Generation Volume\. For new applications, we recommend using one of the newer volume types\. For more information, see [Previous Generation Volumes](https://aws.amazon.com/ebs/previous-generation/)\.

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the Burst Bucket Balance for `gp2`, `st1`, and `sc1` Volumes](#monitoring_burstbucket)\.

## Performance Considerations When Using HDD Volumes<a name="EBSVolumeTypes_considerations"></a>

For optimal throughput results using HDD volumes, plan your workloads with the following considerations in mind\.

### **Throughput Optimized HDD vs\. Cold HDD**<a name="ST1vSC1"></a>

The `st1` and `sc1` bucket sizes vary according to volume size, and a full bucket contains enough tokens for a full volume scan\. However, larger `st1` and `sc1` volumes take longer for the volume scan to complete due to per\-instance and per\-volume throughput limits\. Volumes attached to smaller instances are limited to the per\-instance throughput rather than the `st1` or `sc1` throughput limits\.

Both `st1` and `sc1` are designed for performance consistency of 90% of burst throughput 99% of the time\. Non\-compliant periods are approximately uniformly distributed, targeting 99% of expected total throughput each hour\.

The following table shows ideal scan times for volumes of various size, assuming full buckets and sufficient instance throughput\.

In general, scan times are expressed by this formula:

```
 Volume size
------------- = Scan time
 Throughput
```

For example, taking the performance consistency guarantees and other optimizations into account, an `st1` customer with a 5\-TiB volume can expect to complete a full volume scan in 2\.91 to 3\.27 hours\. 

```
   5 TiB            5 TiB
----------- = ------------------- = 10,486 s = 2.91 hours (optimal) 
 500 MiB/s     0.00047684 TiB/s


               2.91 hours
2.91 hours + -------------- = 3.27 hours (minimum expected)
              (0.90)(0.99) <-- From expected performance of 90% of burst 99% of the time
```

Similarly, an `sc1` customer with a 5\-TiB volume can expect to complete a full volume scan in 5\.83 to 6\.54 hours\.

```
      5 TiB
------------------- = 20972 s = 5.83 hours (optimal) 
 0.000238418 TiB/s


  5.83 hours
-------------- = 6.54 hours (minimum expected)
 (0.90)(0.99)
```


| Volume Size \(TiB\) | ST1 Scan Time with Burst \(Hours\)\* | SC1 Scan Time with Burst \(Hours\)\* | 
| --- | --- | --- | 
| 1 | 1\.17 | 3\.64 | 
| 2 | 1\.17 | 3\.64 | 
| 3 | 1\.75 | 3\.64 | 
| 4 | 2\.33 | 4\.66 | 
| 5 | 2\.91 | 5\.83 | 
| 6 | 3\.50 | 6\.99 | 
| 7 | 4\.08 | 8\.16 | 
| 8 | 4\.66 | 9\.32 | 
| 9 | 5\.24 | 10\.49 | 
| 10 | 5\.83 | 11\.65 | 
| 11 | 6\.41 | 12\.82 | 
| 12 | 6\.99 | 13\.98 | 
| 13 | 7\.57 | 15\.15 | 
| 14 | 8\.16 | 16\.31 | 
| 15 | 8\.74 | 17\.48 | 
| 16 | 9\.32 | 18\.64 | 

 \* These scan times assume an average queue depth \(rounded to the nearest whole number\) of four or more when performing 1 MiB of sequential I/O\.

Therefore if you have a throughput\-oriented workload that needs to complete scans quickly \(up to 500 MiB/s\), or requires several full volume scans a day, use `st1`\. If you are optimizing for cost, your data is relatively infrequently accessed, and you don’t need more than 250 MiB/s of scanning performance, then use `sc1`\.

### **Inefficiency of Small Read/Writes on HDD**<a name="inefficiency"></a>

The performance model for `st1` and `sc1` volumes is optimized for sequential I/Os, favoring high\-throughput workloads, offering acceptable performance on workloads with mixed IOPS and throughput, and discouraging workloads with small, random I/O\.

For example, an I/O request of 1 MiB or less counts as a 1 MiB I/O credit\. However, if the I/Os are sequential, they are merged into 1 MiB I/O blocks and count only as a 1 MiB I/O credit\. 

### **Limitations on per\-Instance Throughput**<a name="throughput-limitations"></a>

Throughput for `st1` and `sc1` volumes is always determined by the smaller of the following:
+ Throughput limits of the volume
+ Throughput limits of the instance

As for all Amazon EBS volumes, we recommend that you select an appropriate EBS\-optimized EC2 instance in order to avoid network bottlenecks\. For more information, see [Amazon EBS\-Optimized Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html)\.

## Monitoring the Burst Bucket Balance for `gp2`, `st1`, and `sc1` Volumes<a name="monitoring_burstbucket"></a>

You can monitor the burst\-bucket level for `gp2`, `st1`, and `sc1` volumes using the EBS `BurstBalance` metric available in Amazon CloudWatch\. This metric shows the percentage of I/O credits \(for `gp2`\) or throughput credits \(for `st1` and `sc1`\) remaining in the burst bucket\. For more information about the `BurstBalance` metric and other metrics related to I/O, see [I/O Characteristics and Monitoring](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-io-characteristics.html)\. CloudWatch also allows you to set an alarm that notifies you when the `BurstBalance` value falls to a certain level\. For more information, see [Creating Amazon CloudWatch Alarms](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.
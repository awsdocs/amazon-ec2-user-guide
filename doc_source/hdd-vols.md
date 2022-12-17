# Throughput Optimized HDD and Cold HDD volumes<a name="hdd-vols"></a>

The HDD\-backed volumes provided by Amazon EBS fall into these categories:
+ Throughput Optimized HDD — A low\-cost HDD designed for frequently accessed, throughput\-intensive workloads\.
+ Cold HDD — The lowest\-cost HDD design for less frequently accessed workloads\.

**Topics**
+ [Limitations on per\-instance throughput](#throughput-limitations)
+ [Throughput Optimized HDD volumes](#EBSVolumeTypes_st1)
+ [Cold HDD volumes](#EBSVolumeTypes_sc1)
+ [Performance considerations when using HDD volumes](#EBSVolumeTypes_considerations)
+ [Monitor the burst bucket balance for volumes](#monitoring_burstbucket-hdd)

## Limitations on per\-instance throughput<a name="throughput-limitations"></a>

Throughput for `st1` and `sc1` volumes is always determined by the smaller of the following:
+ Throughput limits of the volume
+ Throughput limits of the instance

As for all Amazon EBS volumes, we recommend that you select an appropriate EBS\-optimized EC2 instance to avoid network bottlenecks\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## Throughput Optimized HDD volumes<a name="EBSVolumeTypes_st1"></a>

Throughput Optimized HDD \(`st1`\) volumes provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. This volume type is a good fit for large, sequential workloads such as Amazon EMR, ETL, data warehouses, and log processing\. Bootable `st1` volumes are not supported\. 

Throughput Optimized HDD \(`st1`\) volumes, though similar to Cold HDD \(`sc1`\) volumes, are designed to support *frequently* accessed data\.

This volume type is optimized for workloads involving large, sequential I/O, and we recommend that customers with workloads performing small, random I/O use `gp2`\. For more information, see [Inefficiency of small read/writes on HDD](#inefficiency)\.

Throughput Optimized HDD \(`st1`\) volumes attached to EBS\-optimized instances are designed to offer consistent performance, delivering at least 90 percent of the expected throughput performance 99 percent of the time in a given year\.

### Throughput credits and burst performance<a name="ST1ThroughputBurst"></a>

Like `gp2`, `st1` uses a burst bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

The following diagram shows the burst bucket behavior for `st1`\.

![\[st1 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/st1-burst-bucket.png)

Subject to throughput and throughput\-credit caps, the available throughput of an `st1` volume is expressed by the following formula:

```
(Volume size) × (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB `st1` volume, burst throughput is limited to 250 MiB/s, the bucket fills with credits at 40 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

Larger volumes scale these limits linearly, with throughput capped at a maximum of 500 MiB/s\. After the bucket is depleted, throughput is limited to the baseline rate of 40 MiB/s per TiB\. 

On volume sizes ranging from 0\.125 TiB to 16 TiB, baseline throughput varies from 5 MiB/s to a cap of 500 MiB/s, which is reached at 12\.5 TiB as follows:

```
            40 MiB/s
12.5 TiB × ---------- = 500 MiB/s
             1 TiB
```

Burst throughput varies from 31 MiB/s to a cap of 500 MiB/s, which is reached at 2 TiB as follows:

```
         250 MiB/s
2 TiB × ---------- = 500 MiB/s
          1 TiB
```

The following table states the full range of base and burst throughput values for `st1`\.


| Volume size \(TiB\) | ST1 base throughput \(MiB/s\) | ST1 burst throughput \(MiB/s\) | 
| --- | --- | --- | 
| 0\.125 | 5 | 31 | 
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

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitor the burst bucket balance for volumes](#monitoring_burstbucket-hdd)\.

## Cold HDD volumes<a name="EBSVolumeTypes_sc1"></a>

Cold HDD \(`sc1`\) volumes provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. With a lower throughput limit than `st1`, `sc1` is a good fit for large, sequential cold\-data workloads\. If you require infrequent access to your data and are looking to save costs, `sc1` provides inexpensive block storage\. Bootable `sc1` volumes are not supported\.

Cold HDD \(`sc1`\) volumes, though similar to Throughput Optimized HDD \(`st1`\) volumes, are designed to support *infrequently* accessed data\.

**Note**  
This volume type is optimized for workloads involving large, sequential I/O, and we recommend that customers with workloads performing small, random I/O use `gp2`\. For more information, see [Inefficiency of small read/writes on HDD](#inefficiency)\.

Cold HDD \(`sc1`\) volumes attached to EBS\-optimized instances are designed to offer consistent performance, delivering at least 90 percent of the expected throughput performance 99 percent of the time in a given year\.

### Throughput credits and burst performance<a name="SC1ThroughputBurst"></a>

Like `gp2`, `sc1` uses a burst bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

![\[sc1 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/sc1-burst-bucket.png)

Subject to throughput and throughput\-credit caps, the available throughput of an `sc1` volume is expressed by the following formula:

```
(Volume size) × (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB `sc1` volume, burst throughput is limited to 80 MiB/s, the bucket fills with credits at 12 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

Larger volumes scale these limits linearly, with throughput capped at a maximum of 250 MiB/s\. After the bucket is depleted, throughput is limited to the baseline rate of 12 MiB/s per TiB\. 

On volume sizes ranging from 0\.125 TiB to 16 TiB, baseline throughput varies from 1\.5 MiB/s to a maximum of 192 MiB/s, which is reached at 16 TiB as follows:

```
           12 MiB/s
16 TiB × ---------- = 192 MiB/s
            1 TiB
```

Burst throughput varies from 10 MiB/s to a cap of 250 MiB/s, which is reached at 3\.125 TiB as follows:

```
             80 MiB/s
3.125 TiB × ----------- = 250 MiB/s
              1 TiB
```

The following table states the full range of base and burst throughput values for `sc1`:


| Volume Size \(TiB\) | SC1 Base Throughput \(MiB/s\) | SC1 Burst Throughput \(MiB/s\) | 
| --- | --- | --- | 
| 0\.125 | 1\.5 | 10 | 
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

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitor the burst bucket balance for volumes](#monitoring_burstbucket-hdd)\.

## Performance considerations when using HDD volumes<a name="EBSVolumeTypes_considerations"></a>

For optimal throughput results using HDD volumes, plan your workloads with the following considerations in mind\.

### **Comparing Throughput Optimized HDD and Cold HDD**<a name="ST1vSC1"></a>

The `st1` and `sc1` bucket sizes vary according to volume size, and a full bucket contains enough tokens for a full volume scan\. However, larger `st1` and `sc1` volumes take longer for the volume scan to complete because of per\-instance and per\-volume throughput limits\. Volumes attached to smaller instances are limited to the per\-instance throughput rather than the `st1` or `sc1` throughput limits\.

Both `st1` and `sc1` are designed for performance consistency of 90 percent of burst throughput 99 percent of the time\. Non\-compliant periods are approximately uniformly distributed, targeting 99 percent of expected total throughput each hour\.

In general, scan times are expressed by this formula:

```
 Volume size
------------ = Scan time
 Throughput
```

For example, taking the performance consistency guarantees and other optimizations into account, an `st1` customer with a 5\-TiB volume can expect to complete a full volume scan in 2\.91 to 3\.27 hours\. 
+ Optimal scan time

  ```
     5 TiB            5 TiB
  ----------- = ------------------ = 10,486 seconds = 2.91 hours 
   500 MiB/s     0.00047684 TiB/s
  ```
+ Maximum scan time

  ```
    2.91 hours
  -------------- = 3.27 hours
   (0.90)(0.99) <-- From expected performance of 90% of burst 99% of the time
  ```

Similarly, an `sc1` customer with a 5\-TiB volume can expect to complete a full volume scan in 5\.83 to 6\.54 hours\.
+ Optimal scan time

  ```
     5 TiB             5 TiB
  ----------- = ------------------- = 20972 seconds = 5.83 hours 
   250 MiB/s     0.000238418 TiB/s
  ```
+ Maximum scan time

  ```
    5.83 hours
  -------------- = 6.54 hours
   (0.90)(0.99)
  ```

The following table shows ideal scan times for volumes of various size, assuming full buckets and sufficient instance throughput\.


| Volume size \(TiB\) | ST1 scan time with burst \(hours\)\* | SC1 scan time with burst \(hours\)\* | 
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

### Inefficiency of small read/writes on HDD<a name="inefficiency"></a>

The performance model for `st1` and `sc1` volumes is optimized for sequential I/Os, favoring high\-throughput workloads, offering acceptable performance on workloads with mixed IOPS and throughput, and discouraging workloads with small, random I/O\.

For example, an I/O request of 1 MiB or less counts as a 1 MiB I/O credit\. However, if the I/Os are sequential, they are merged into 1 MiB I/O blocks and count only as a 1 MiB I/O credit\. 

## Monitor the burst bucket balance for volumes<a name="monitoring_burstbucket-hdd"></a>

You can monitor the burst bucket level for `st1` and `sc1` volumes using the Amazon EBS `BurstBalance` metric available in Amazon CloudWatch\. This metric shows the throughput credits for `st1` and `sc1` remaining in the burst bucket\. For more information about the `BurstBalance` metric and other metrics related to I/O, see [I/O characteristics and monitoring](ebs-io-characteristics.md)\. CloudWatch also allows you to set an alarm that notifies you when the `BurstBalance` value falls to a certain level\. For more information, see [Creating CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.
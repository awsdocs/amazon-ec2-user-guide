# Amazon EBS volume types<a name="ebs-volume-types"></a>

Amazon EBS provides the following volume types, which differ in performance characteristics and price, so that you can tailor your storage performance and cost to the needs of your applications\. The volumes types fall into these categories:
+ [Solid state drives \(SSD\)](#solid-state-drives) — Optimized for transactional workloads involving frequent read/write operations with small I/O size, where the dominant performance attribute is IOPS\.
+ [Hard disk drives \(HDD\)](#hard-disk-drives) — Optimized for large streaming workloads where the dominant performance attribute is throughput\.
+ [Previous generation](#ebs-previous-generation-volumes) — Hard disk drives that can be used for workloads with small datasets where data is accessed infrequently and performance is not of primary importance\. We recommend that you consider a current generation volume type instead\.

There are several factors that can affect the performance of EBS volumes, such as instance configuration, I/O characteristics, and workload demand\. To fully use the IOPS provisioned on an EBS volume, use [EBS\-optimized instances](ebs-optimized.md)\. For more information about getting the most out of your EBS volumes, see [Amazon EBS volume performance on Linux instances](EBSPerformance.md)\.

For more information about pricing, see [Amazon EBS Pricing](http://aws.amazon.com/ebs/pricing/)\.

## Solid state drives \(SSD\)<a name="solid-state-drives"></a>

The SSD\-backed volumes provided by Amazon EBS fall into these categories:
+ General Purpose SSD — Provides a balance of price and performance\. We recommend these volumes for most workloads\.
+ Provisioned IOPS SSD — Provides high performance for mission\-critical, low\-latency, or high\-throughput workloads\.

The following is a summary of the use cases and characteristics of SSD\-backed volumes\. For information about the maximum IOPS and throughput per instance, see [Amazon EBS–optimized instances](ebs-optimized.md)\.


|  | General Purpose SSD | Provisioned IOPS SSD | 
| --- | --- | --- | 
| Volume type | gp3 | gp2 | io2 Block Express ‡ | io2 | io1 | 
| Durability | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 99\.999% durability \(0\.001% annual failure rate\) | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 
| Use cases |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  | Workloads that require sub\-millisecond latency, and sustained IOPS performance or more than 64,000 IOPS or 1,000 MiB/s of throughput  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  | 
| Volume size | 1 GiB \- 16 TiB  | 4 GiB \- 64 TiB | 4 GiB \- 16 TiB  | 
| Max IOPS per volume \(16 KiB I/O\) | 16,000 | 256,000 | 64,000 † | 
| Max throughput per volume | 1,000 MiB/s | 250 MiB/s \* | 4,000 MiB/s | 1,000 MiB/s † | 
| Amazon EBS Multi\-attach | Not supported | Not supported | Supported | 
| Boot volume | Supported | 

\* The throughput limit is between 128 MiB/s and 250 MiB/s, depending on the volume size\. Volumes smaller than or equal to 170 GiB deliver a maximum throughput of 128 MiB/s\. Volumes larger than 170 GiB but smaller than 334 GiB deliver a maximum throughput of 250 MiB/s if burst credits are available\. Volumes larger than or equal to 334 GiB deliver 250 MiB/s regardless of burst credits\. Older `gp2` volumes might not reach full performance unless you [modify the volume](ebs-modify-volume.md)\.

† Maximum IOPS and throughput are guaranteed only on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances) provisioned with more than 32,000 IOPS\. Other instances guarantee up to 32,000 IOPS and 500 MiB/s\. Older `io1` volumes might not reach full performance unless you [modify the volume](ebs-modify-volume.md)\.

‡ `io2` Block Express volumes are available as an opt\-in preview only\. For more information, see [`io2` Block Express volumes \(In preview\)](#io2-block-express)\.

## Hard disk drives \(HDD\)<a name="hard-disk-drives"></a>

The HDD\-backed volumes provided by Amazon EBS fall into these categories:
+ Throughput Optimized HDD — A low\-cost HDD designed for frequently accessed, throughput\-intensive workloads\.
+ Cold HDD — The lowest\-cost HDD design for less frequently accessed workloads\.

The following is a summary of the use cases and characteristics of HDD\-backed volumes\. For information about the maximum IOPS and throughput per instance, see [Amazon EBS–optimized instances](ebs-optimized.md)\.


|  | Throughput Optimized HDD | Cold HDD | 
| --- | --- | --- | 
| Volume type | st1 | sc1 | 
| Durability | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 
| Use cases |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html)  | 
| Volume size | 125 GiB \- 16 TiB | 125 GiB \- 16 TiB  | 
| Max IOPS per volume \(1 MiB I/O\) | 500 | 250 | 
| Max throughput per volume | 500 MiB/s | 250 MiB/s | 
| Amazon EBS Multi\-attach | Not supported | Not supported | 
| Boot volume | Not supported | Not supported | 

## Previous generation volume types<a name="ebs-previous-generation-volumes"></a>

The following table describes previous\-generation EBS volume types\. If you need higher performance or performance consistency than previous\-generation volumes can provide, we recommend that you consider using General Purpose SSD \(`gp2` and `gp3`\) or other current volume types\. For more information, see [Previous Generation Volumes](https://aws.amazon.com/ebs/previous-generation/)\.


|  | Magnetic | 
| --- | --- | 
| Volume type | standard | 
| Use cases | Workloads where data is infrequently accessed | 
| Volume size | 1 GiB\-1 TiB | 
| Max IOPS per volume | 40–200 | 
| Max throughput per volume | 40–90 MiB/s | 
| Boot volume | Supported | 

## General Purpose SSD volumes \(gp3\)<a name="gp3-ebs-volume-type"></a>

General Purpose SSD \(`gp3`\) volumes offer cost\-effective storage that is ideal for a broad range of workloads\. These volumes deliver a consistent baseline rate of 3,000 IOPS and 125 MiB/s, included with the price of storage\. You can provision additional IOPS \(up to 16,000\) and throughput \(up to 1,000 MiB/s\) for an additional cost\.

The maximum ratio of provisioned IOPS to provisioned volume size is 500 IOPS per GiB\. The maximum ratio of provisioned throughput to provisioned IOPS is \.25 MiB/s per IOPS\. The following volume configurations support provisioning either maximum IOPS or maximum throughput:
+ 32 GiB or larger: 500 IOPS/GiB x 32 GiB = 16,000 IOPS
+ 8 GiB or larger and 4,000 IOPS or higher: 4,000 IOPS x 0\.25 MiB/s/IOPS \- 1,000 MiB/s

## General Purpose SSD volumes \(gp2\)<a name="EBSVolumeTypes_gp2"></a>

General Purpose SSD \(`gp2`\) volumes offer cost\-effective storage that is ideal for a broad range of workloads\. These volumes deliver single\-digit millisecond latencies and the ability to burst to 3,000 IOPS for extended periods of time\. Between a minimum of 100 IOPS \(at 33\.33 GiB and below\) and a maximum of 16,000 IOPS \(at 5,334 GiB and above\), baseline performance scales linearly at 3 IOPS per GiB of volume size\. AWS designs `gp2` volumes to deliver their provisioned performance 99% of the time\. A `gp2` volume can range in size from 1 GiB to 16 TiB\.

### I/O Credits and burst performance<a name="IOcredit"></a>

The performance of `gp2` volumes is tied to volume size, which determines the baseline performance level of the volume and how quickly it accumulates I/O credits; larger volumes have higher baseline performance levels and accumulate I/O credits faster\. I/O credits represent the available bandwidth that your `gp2` volume can use to burst large amounts of I/O when more than the baseline performance is needed\. The more credits your volume has for I/O, the more time it can burst beyond its baseline performance level and the better it performs when more performance is needed\. The following diagram shows the burst\-bucket behavior for `gp2`\.

![\[gp2 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/gp2-burst-bucket.png)

Each volume receives an initial I/O credit balance of 5\.4 million I/O credits, which is enough to sustain the maximum burst performance of 3,000 IOPS for at least 30 minutes\. This initial credit balance is designed to provide a fast initial boot cycle for boot volumes and to provide a good bootstrapping experience for other applications\. Volumes earn I/O credits at the baseline performance rate of 3 IOPS per GiB of volume size\. For example, a 100 GiB `gp2` volume has a baseline performance of 300 IOPS\.

![\[Comparing baseline performance and burst IOPS\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/gp2_iops_1.png)

When your volume requires more than the baseline performance I/O level, it draws on I/O credits in the credit balance to burst to the required performance level, up to a maximum of 3,000 IOPS\. When your volume uses fewer I/O credits than it earns in a second, unused I/O credits are added to the I/O credit balance\. The maximum I/O credit balance for a volume is equal to the initial credit balance \(5\.4 million I/O credits\)\.

When the baseline performance of a volume is higher than maximum burst performance, I/O credits are never spent\. If the volume is attached to an instance built on the [Nitro System](instance-types.md#ec2-nitro-instances), the burst balance is not reported\. For other instances, the reported burst balance is 100%\.

The burst duration of a volume is dependent on the size of the volume, the burst IOPS required, and the credit balance when the burst begins\. This is shown in the following equation:

```
                             (Credit balance)
Burst duration  =  ------------------------------------
                   (Burst IOPS) - 3(Volume size in GiB)
```

The following table lists several volume sizes and the associated baseline performance of the volume \(which is also the rate at which it accumulates I/O credits\), the burst duration at the 3,000 IOPS maximum \(when starting with a full credit balance\), and the time in seconds that the volume would take to refill an empty credit balance\.


|  Volume size \(GiB\)  |  Baseline performance \(IOPS\)  |  Burst duration when driving sustained 3,000 IOPS \(second\)  |  Seconds to fill empty credit balance when driving no IO  | 
| --- | --- | --- | --- | 
|  1  |  100  |  1,802  | 54,000 | 
|  100  |  300  |  2,000  | 18,000 | 
|  250  |  750  | 2,400 | 7,200 | 
|  334 \(Min\. size for max throughput\)  | 1,002 |  2,703  |  5,389  | 
|  500  |  1,500  |  3,600  | 3,600 | 
|  750  |  2,250  |  7,200  | 2,400 | 
|  1,000  |  3,000  |  N/A\*  |  N/A\*  | 
|  5,334 \(Min\. size for max IOPS\)  |  16,000  |  N/A\*  |  N/A\*  | 
|  16,384 \(16 TiB, max volume size\)  |  16,000  |  N/A\*  |  N/A\*  | 

\* The baseline performance of the volume exceeds the maximum burst performance\.

**What happens if I empty my I/O credit balance?**  
If your `gp2` volume uses all of its I/O credit balance, the maximum IOPS performance of the volume remains at the baseline IOPS performance level \(the rate at which your volume earns credits\) and the volume's maximum throughput is reduced to the baseline IOPS multiplied by the maximum I/O size\. Throughput can never exceed 250 MiB/s\. When I/O demand drops below the baseline level and unused credits are added to the I/O credit balance, the maximum IOPS performance of the volume again exceeds the baseline\. For example, a 100 GiB `gp2` volume with an empty credit balance has a baseline performance of 300 IOPS and a throughput limit of 75 MiB/s \(300 I/O operations per second \* 256 KiB per I/O operation = 75 MiB/s\)\. The larger a volume is, the greater the baseline performance is and the faster it replenishes the credit balance\. For more information about how IOPS are measured, see [I/O characteristics and monitoring](ebs-io-characteristics.md)\.

If you notice that your volume performance is frequently limited to the baseline level \(due to an empty I/O credit balance\), you should consider switching to a `gp3` volume\.

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the burst bucket balance for volumes](#monitoring_burstbucket)\.

### Throughput performance<a name="GP2Throughput"></a>

Throughput for a `gp2` volume can be calculated using the following formula, up to the throughput limit of 250 MiB/s:

```
Throughput in MiB/s = ((Volume size in GiB) × (IOPS per GiB) × (I/O size in KiB))
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

            250 MiB/s
   =  ---------------------
      (256 KiB)(3 IOPS/GiB)


               [(250)(2^20)(Bytes)]/s
   =  ------------------------------------------
      (256)(2^10)(Bytes)([3 IOP/s]/[(2^30)(Bytes)])


      (250)(2^20)(2^30)(Bytes)
   =  ------------------------
           (256)(2^10)(3)

   =  357,913,941,333 Bytes
   
   =  333⅓ GiB (334 GiB in practice because volumes are provisioned in whole gibibytes)
```

## Provisioned IOPS SSD volumes<a name="EBSVolumeTypes_piops"></a>

Provisioned IOPS SSD \(`io1` and `io2`\) volumes are designed to meet the needs of I/O\-intensive workloads, particularly database workloads, that are sensitive to storage performance and consistency\. Provisioned IOPS SSD volumes use a consistent IOPS rate, which you specify when you create the volume, and Amazon EBS delivers the provisioned performance 99\.9 percent of the time\.

`io1` volumes are designed to provide 99\.8 to 99\.9 percent volume durability with an annual failure rate \(AFR\) no higher than 0\.2 percent, which translates to a maximum of two volume failures per 1,000 running volumes over a one\-year period\. `io2` volumes are designed to provide 99\.999 percent volume durability with an AFR no higher than 0\.001 percent, which translates to a single volume failure per 100,000 running volumes over a one\-year period\.

Provisioned IOPS SSD volumes can range in size from 4 GiB to 16 TiB\. You can provision from 100 IOPS up to 64,000 IOPS per volume on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances) and up to 32,000 on other instances\. The maximum ratio of provisioned IOPS to requested volume size \(in GiB\) is 50:1 for `io1` volumes, and 500:1 for `io2` volumes\. For example, a 100 GiB `io1` volume can be provisioned with up to 5,000 IOPS, while a 100 GiB `io2` volume can be provisioned with up to 50,000 IOPS\. On a supported instance type, the following volume sizes allow provisioning up to the 64,000 IOPS maximum:
+ `io1` volume 1,280 GiB in size or greater \(50 × 1,280 GiB = 64,000 IOPS\)
+ `io2` volume 128 GiB in size or greater \(500 × 128 GiB = 64,000 IOPS\)

Provisioned IOPS SSD volumes provisioned with up to 32,000 IOPS support a maximum I/O size of 256 KiB and yield as much as 500 MiB/s of throughput\. With the I/O size at the maximum, peak throughput is reached at 2,000 IOPS\. A volume provisioned with more than 32,000 IOPS \(up to the cap of 64,000 IOPS\) supports a maximum I/O size of 16 KiB and yields as much as 1,000 MiB/s of throughput\. The following graph illustrates these performance characteristics:

![\[Throughput limits for io1 volumes\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/io1_throughput.png)

Your per\-I/O latency experience depends on the provisioned IOPS and on your workload profile\. For the best I/O latency experience, ensure that you provision IOPS to meet the I/O profile of your workload\.

### `io2` Block Express volumes \(In preview\)<a name="io2-block-express"></a>

**Note**  
You can now opt in to the `io2` Block Express volumes preview in `us-east-1`, `us-west-2`, `us-east-2`, `eu-central-1`, `ap-southeast-1`, and `ap-northeast-1`\. After you opt in, all `io2` volumes that you create in your account in the opted\-in Regions will be `io2` Block Express volumes\. Carefully review the feature limitations that apply to the preview before opting in\.  
You can opt in to the preview only with an account that does not have any existing `io2` volumes\.

EBS Block Express is the next generation of Amazon EBS storage server architecture\. It has been built for the purpose of meeting the performance requirements of the most demanding I/O intensive applications that run on Nitro\-based Amazon EC2 instances\. 

Block Express architecture increases performance and scale\. Block Express servers communicate with Nitro\-based instances using the Scalable Reliable Datagram \(SRD\) networking protocol\. This interface is implemented in the Nitro Card dedicated for Amazon EBS I/O function on the host hardware of the instance\. It minimizes I/O delay and latency variation \(network jitter\), which provides faster and more consistent performance for your applications\. For more information, see [`io2` Block Express volumes](http://aws.amazon.com/ebs/provisioned-iops/)\.

`io2` Block Express volumes are suited for workloads that benefit from a single volume that provides sub\-millisecond latency, and supports higher IOPS, higher throughput, and larger capacity than `io2` volumes\.

**Topics**
+ [Performance](#io2-bx-perf)
+ [Limitations during preview](#io2-bx-limits)
+ [Opt in to the preview](#io2-bx-optin)
+ [Opt out of the preview](#io2-bx-optout)
+ [Quotas](#io2-bx-quotas)
+ [Pricing and billing](#io2-bx-pricing)

#### Performance<a name="io2-bx-perf"></a>

With `io2` Block Express volumes, you can provision volumes with:
+ Sub\-millisecond average latency
+ Storage capacity up to 64 TiB \(65,536 GiB\)
+ Provisioned IOPS up to 256,000, with an IOPS:GiB ratio of 1,000:1\. Maximum IOPS can be provisioned with volumes 256 GiB in size and larger \(1,000 IOPS x 256 GiB = 256,000 IOPS\)\.
+ Volume throughput up to 4,000 MiB/s\. Throughput scales proportionally up to 0\.256 MiB/s per provisioned IOPS\. Maximum throughput can be achieved at 16,000 IOPS or higher\. 

![\[Throughput limits for io2 Block Express volumes\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/io2_bx.png)

#### Limitations during preview<a name="io2-bx-limits"></a>

`io2` Block Express volumes have the following limitations during the preview:
+ `io2` Block Express volumes are supported with R5B instances only\. If you attempt to launch an unsupported instance type with `io2` Block Express volumes, the request fails\.
+ `io2` Block Express volumes are available in the following Regions only: `us-east-1`, `us-west-2`, `us-east-2`, `eu-central-1`, `ap-southeast-1`, and `ap-northeast-1`\.
+ `io2` Block Express volumes are not optimized for boot and could cause longer boot times\. We recommend that you continue to use `io1`, `gp2`, or `gp3` for boot volumes\.
+ You can't create encrypted `io2` Block Express volumes, or launch instances with encrypted `io2` Block Express volumes, from unencrypted snapshots or shared snapshots\. To create encrypted `io2` Block Express volumes, or launch instances with encrypted `io2` Block Express volumes, you must use encrypted snapshots or AMIs that you own in your account\.
+ You can't create encrypted `io2` Block Express volumes, or launch instances with encrypted `io2` Block Express volumes using the Amazon EC2 console\. You must use the Amazon EC2 API or the AWS CLI\.
+ `io2` Block Express volumes do not fully support fast snapshot restore\. While you can restore `io2` Block Express volumes from snapshots that are enabled for fast snapshot restore, you will experience higher I/O latency while the volumes are being initialized\. This is formerly known as pre\-warming\. To avoid the higher latency, we recommend that you initialize your `io2` Block Express volumes before starting your I/O operations\. For more information, see [Initializing Amazon EBS volumes](ebs-initialize.md)\.
+ `io2` Block Express volumes do not support Elastic Volume operations\. You can't change the volume type or performance after the volume has been created\.
+ `io2` Block Express volumes do not support Multi\-Attach\.

#### Opt in to the preview<a name="io2-bx-optin"></a>

To opt in to the `io2` Block Express volumes preview, visit the [ Opt in page](https://pages.awscloud.com/io2-block-express-preview.html) and complete the form\. You must specify the Regions in which to opt in\.

After you opt in, all new `io2` volumes that you create in your account in the opted\-in Regions will be `io2` Block Express volumes\. While your account is opted in, you will not be able to create `io2` volumes that are not `io2` Block Express volumes in the opted\-in Regions\. To transition your existing workloads from your existing volumes, create snapshots of the volumes and then restore them to new `io2` Block Express volumes\.

#### Opt out of the preview<a name="io2-bx-optout"></a>

To opt out of the `io2` Block Express volumes preview, visit the [ Opt in page](https://pages.awscloud.com/io2-block-express-preview.html) and complete the form\. You must specify the Regions in which to opt out\.

After you opt out, all new `io2` volumes that you create in the opted\-out Regions will be `io2` volumes, and not `io2` Block Express volumes\. `io2` volumes that you created in the opted\-out Regions before opting out will continue to be `io2` Block Express volumes\. If you created snapshots of volumes larger than 16 TiB, you will not be able to restore them to `io2` volumes in the opted\-out Regions\. Before opting out, we recommend that for `io2` Block Express volumes greater than 16 TiB you copy the data to a raid set that comprises of `io2` volumes less than 16 TiB in size and then create a backup of the raid set using multi\-volume snapshots\. For more information, see [Multi\-volume snapshots](ebs-creating-snapshot.md#ebs-create-snapshot-multi-volume)\.

#### Quotas<a name="io2-bx-quotas"></a>

`io2` Block Express volumes adhere to the same service quotas as `io2` volumes\. For more information, see [Amazon EBS quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html)\.

#### Pricing and billing<a name="io2-bx-pricing"></a>

`io2` volumes and `io2` Block Express volumes are billed at the same rate\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

Usage reports do not distinguish between `io2` Block Express volumes and `io2` volumes\. We recommend that you use tags to help you identify costs associated with `io2` Block Express volumes\.

## Throughput Optimized HDD volumes<a name="EBSVolumeTypes_st1"></a>

Throughput Optimized HDD \(`st1`\) volumes provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. This volume type is a good fit for large, sequential workloads such as Amazon EMR, ETL, data warehouses, and log processing\. Bootable `st1` volumes are not supported\. 

Throughput Optimized HDD \(`st1`\) volumes, though similar to Cold HDD \(`sc1`\) volumes, are designed to support *frequently* accessed data\.

This volume type is optimized for workloads involving large, sequential I/O, and we recommend that customers with workloads performing small, random I/O use `gp2`\. For more information, see [Inefficiency of small read/writes on HDD](#inefficiency)\.

### Throughput credits and burst performance<a name="ST1ThroughputBurst"></a>

Like `gp2`, `st1` uses a burst\-bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

The following diagram shows the burst\-bucket behavior for `st1`\.

![\[st1 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/st1-burst-bucket.png)

Subject to throughput and throughput\-credit caps, the available throughput of an `st1` volume is expressed by the following formula:

```
(Volume size) x (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB `st1` volume, burst throughput is limited to 250 MiB/s, the bucket fills with credits at 40 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

Larger volumes scale these limits linearly, with throughput capped at a maximum of 500 MiB/s\. After the bucket is depleted, throughput is limited to the baseline rate of 40 MiB/s per TiB\. 

On volume sizes ranging from 0\.125 TiB to 16 TiB, baseline throughput varies from 5 MiB/s to a cap of 500 MiB/s, which is reached at 12\.5 TiB as follows:

```
            40 MiB/s
12.5 TiB x ---------- = 500 MiB/s
             1 TiB
```

Burst throughput varies from 31 MiB/s to a cap of 500 MiB/s, which is reached at 2 TiB as follows:

```
         250 MiB/s
2 TiB x ---------- = 500 MiB/s
          1 TiB
```

The following table states the full range of base and burst throughput values for `st1`:


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

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the burst bucket balance for volumes](#monitoring_burstbucket)\.

## Cold HDD volumes<a name="EBSVolumeTypes_sc1"></a>

Cold HDD \(`sc1`\) volumes provide low\-cost magnetic storage that defines performance in terms of throughput rather than IOPS\. With a lower throughput limit than `st1`, `sc1` is a good fit for large, sequential cold\-data workloads\. If you require infrequent access to your data and are looking to save costs, `sc1` provides inexpensive block storage\. Bootable `sc1` volumes are not supported\.

Cold HDD \(`sc1`\) volumes, though similar to Throughput Optimized HDD \(`st1`\) volumes, are designed to support *infrequently* accessed data\.

**Note**  
This volume type is optimized for workloads involving large, sequential I/O, and we recommend that customers with workloads performing small, random I/O use `gp2`\. For more information, see [Inefficiency of small read/writes on HDD](#inefficiency)\.

### Throughput credits and burst performance<a name="SC1ThroughputBurst"></a>

Like `gp2`, `sc1` uses a burst\-bucket model for performance\. Volume size determines the baseline throughput of your volume, which is the rate at which the volume accumulates throughput credits\. Volume size also determines the burst throughput of your volume, which is the rate at which you can spend credits when they are available\. Larger volumes have higher baseline and burst throughput\. The more credits your volume has, the longer it can drive I/O at the burst level\.

![\[sc1 burst bucket\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/sc1-burst-bucket.png)

Subject to throughput and throughput\-credit caps, the available throughput of an `sc1` volume is expressed by the following formula:

```
(Volume size) x (Credit accumulation rate per TiB) = Throughput
```

For a 1\-TiB `sc1` volume, burst throughput is limited to 80 MiB/s, the bucket fills with credits at 12 MiB/s, and it can hold up to 1 TiB\-worth of credits\.

Larger volumes scale these limits linearly, with throughput capped at a maximum of 250 MiB/s\. After the bucket is depleted, throughput is limited to the baseline rate of 12 MiB/s per TiB\. 

On volume sizes ranging from 0\.125 TiB to 16 TiB, baseline throughput varies from 1\.5 MiB/s to a maximum of 192 MiB/s, which is reached at 16 TiB as follows:

```
           12 MiB/s
16 TiB x ---------- = 192 MiB/s
            1 TiB
```

Burst throughput varies from 10 MiB/s to a cap of 250 MiB/s, which is reached at 3\.125 TiB as follows:

```
             80 MiB/s
3.125 TiB x ----------- = 250 MiB/s
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

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the burst bucket balance for volumes](#monitoring_burstbucket)\.

## Magnetic volumes<a name="EBSVolumeTypes_standard"></a>

Magnetic volumes are backed by magnetic drives and are suited for workloads where data is accessed infrequently, and scenarios where low\-cost storage for small volume sizes is important\. These volumes deliver approximately 100 IOPS on average, with burst capability of up to hundreds of IOPS, and they can range in size from 1 GiB to 1 TiB\.

**Note**  
Magnetic is a previous generation volume type\. For new applications, we recommend using one of the newer volume types\. For more information, see [Previous Generation Volumes](http://aws.amazon.com/ebs/previous-generation/)\.

For information about using CloudWatch metrics and alarms to monitor your burst bucket balance, see [Monitoring the burst bucket balance for volumes](#monitoring_burstbucket)\.

## Performance considerations when using HDD volumes<a name="EBSVolumeTypes_considerations"></a>

For optimal throughput results using HDD volumes, plan your workloads with the following considerations in mind\.

### **Comparing Throughput Optimized HDD and Cold HDD**<a name="ST1vSC1"></a>

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

### Limitations on per\-instance throughput<a name="throughput-limitations"></a>

Throughput for `st1` and `sc1` volumes is always determined by the smaller of the following:
+ Throughput limits of the volume
+ Throughput limits of the instance

As for all Amazon EBS volumes, we recommend that you select an appropriate EBS\-optimized EC2 instance in order to avoid network bottlenecks\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## Monitoring the burst bucket balance for volumes<a name="monitoring_burstbucket"></a>

You can monitor the burst\-bucket level for `gp2`, `st1`, and `sc1` volumes using the EBS `BurstBalance` metric available in Amazon CloudWatch\. This metric shows the percentage of I/O credits \(for `gp2`\) or throughput credits \(for `st1` and `sc1`\) remaining in the burst bucket\. For more information about the `BurstBalance` metric and other metrics related to I/O, see [I/O characteristics and monitoring](ebs-io-characteristics.md)\. CloudWatch also allows you to set an alarm that notifies you when the `BurstBalance` value falls to a certain level\. For more information, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html)\.
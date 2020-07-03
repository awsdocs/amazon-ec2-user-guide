# I/O characteristics and monitoring<a name="ebs-io-characteristics"></a>

On a given volume configuration, certain I/O characteristics drive the performance behavior for your EBS volumes\. SSD\-backed volumes—General Purpose SSD \(`gp2`\) and Provisioned IOPS SSD \(`io1`\)—deliver consistent performance whether an I/O operation is random or sequential\. HDD\-backed volumes—Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\)—deliver optimal performance only when I/O operations are large and sequential\. To understand how SSD and HDD volumes will perform in your application, it is important to know the connection between demand on the volume, the quantity of IOPS available to it, the time it takes for an I/O operation to complete, and the volume's throughput limits\. 

**IOPS**  
IOPS are a unit of measure representing input/output operations per second\. The operations are measured in KiB, and the underlying drive technology determines the maximum amount of data that a volume type counts as a single I/O\. I/O size is capped at 256 KiB for SSD volumes and 1,024 KiB for HDD volumes because SSD volumes handle small or random I/O much more efficiently than HDD volumes\. 

When small I/O operations are physically contiguous, Amazon EBS attempts to merge them into a single I/O operation up to the maximum size\. For example, for SSD volumes, a single 1,024 KiB I/O operation counts as 4 operations \(1,024÷256=4\), while 8 contiguous I/O operations at 32 KiB each count as 1 operation \(8×32=256\)\. However, 8 random I/O operations at 32 KiB each count as 8 operations\. Each I/O operation under 32 KiB counts as 1 operation\.

Similarly, for HDD\-backed volumes, both a single 1,024 KiB I/O operation and 8 sequential 128 KiB operations would count as one operation\. However, 8 random 128 KiB I/O operations would count as 8 operations\.

Consequently, when you create an SSD\-backed volume supporting 3,000 IOPS \(either by provisioning an `io1` volume at 3,000 IOPS or by sizing a `gp2` volume at 1000 GiB\), and you attach it to an EBS\-optimized instance that can provide sufficient bandwidth, you can transfer up to 3,000 I/Os of data per second, with throughput determined by I/O size\. 

**Volume queue length and latency**  
The volume queue length is the number of pending I/O requests for a device\. Latency is the true end\-to\-end client time of an I/O operation, in other words, the time elapsed between sending an I/O to EBS and receiving an acknowledgement from EBS that the I/O read or write is complete\. Queue length must be correctly calibrated with I/O size and latency to avoid creating bottlenecks either on the guest operating system or on the network link to EBS\.

Optimal queue length varies for each workload, depending on your particular application's sensitivity to IOPS and latency\. If your workload is not delivering enough I/O requests to fully use the performance available to your EBS volume, then your volume might not deliver the IOPS or throughput that you have provisioned\. 

Transaction\-intensive applications are sensitive to increased I/O latency and are well\-suited for SSD\-backed `io1` and `gp2` volumes\. You can maintain high IOPS while keeping latency down by maintaining a low queue length and a high number of IOPS available to the volume\. Consistently driving more IOPS to a volume than it has available can cause increased I/O latency\. 

Throughput\-intensive applications are less sensitive to increased I/O latency, and are well\-suited for HDD\-backed `st1` and `sc1` volumes\. You can maintain high throughput to HDD\-backed volumes by maintaining a high queue length when performing large, sequential I/O\.

**I/O size and volume throughput limits**  
For SSD\-backed volumes, if your I/O size is very large, you may experience a smaller number of IOPS than you provisioned because you are hitting the throughput limit of the volume\. For example, a `gp2` volume under 1000 GiB with burst credits available has an IOPS limit of 3,000 and a volume throughput limit of 250 MiB/s\. If you are using a 256 KiB I/O size, your volume reaches its throughput limit at 1000 IOPS \(1000 x 256 KiB = 250 MiB\)\. For smaller I/O sizes \(such as 16 KiB\), this same volume can sustain 3,000 IOPS because the throughput is well below 250 MiB/s\. \(These examples assume that your volume's I/O is not hitting the throughput limits of the instance\.\) For more information about the throughput limits for each EBS volume type, see [Amazon EBS volume types](ebs-volume-types.md)\. 

For smaller I/O operations, you may see a higher\-than\-provisioned IOPS value as measured from inside your instance\. This happens when the instance operating system merges small I/O operations into a larger operation before passing them to Amazon EBS\.

If your workload uses sequential I/Os on HDD\-backed `st1` and `sc1` volumes, you may experience a higher than expected number of IOPS as measured from inside your instance\. This happens when the instance operating system merges sequential I/Os and counts them in 1,024 KiB\-sized units\. If your workload uses small or random I/Os, you may experience a lower throughput than you expect\. This is because we count each random, non\-sequential I/O toward the total IOPS count, which can cause you to hit the volume's IOPS limit sooner than expected\.

Whatever your EBS volume type, if you are not experiencing the IOPS or throughput you expect in your configuration, ensure that your EC2 instance bandwidth is not the limiting factor\. You should always use a current\-generation, EBS\-optimized instance \(or one that includes 10 Gb/s network connectivity\) for optimal performance\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\. Another possible cause for not experiencing the expected IOPS is that you are not driving enough I/O to the EBS volumes\.

**Monitor I/O characteristics using CloudWatch**  
You can monitor these I/O characteristics with each volume's [CloudWatch metrics](monitoring-volume-status.md)\. Important metrics to consider include:
+ `BurstBalance`
+ `VolumeReadBytes`
+ `VolumeWriteBytes`
+ `VolumeReadOps`
+ `VolumeWriteOps`
+ `VolumeQueueLength`

`BurstBalance` displays the burst bucket balance for `gp2`, `st1`, and `sc1` volumes as a percentage of the remaining balance\. When your burst bucket is depleted, volume I/O \(for `gp2` volumes\) or volume throughput \(for `st1` and `sc1` volumes\) is throttled to the baseline\. Check the `BurstBalance` value to determine whether your volume is being throttled for this reason\. 

HDD\-backed `st1` and `sc1` volumes are designed to perform best with workloads that take advantage of the 1,024 KiB maximum I/O size\. To determine your volume's average I/O size, divide `VolumeWriteBytes `by `VolumeWriteOps`\. The same calculation applies to read operations\. If average I/O size is below 64 KiB, increasing the size of the I/O operations sent to an `st1` or `sc1` volume should improve performance\. 

**Note**  
If average I/O size is at or near 44 KiB, you may be using an instance or kernel without support for indirect descriptors\. Any Linux kernel 3\.8 and above has this support, as well as any current\-generation instance\. 

If your I/O latency is higher than you require, check `VolumeQueueLength` to make sure your application is not trying to drive more IOPS than you have provisioned\. If your application requires a greater number of IOPS than your volume can provide, you should consider using a larger `gp2` volume with a higher base performance level or an `io1` volume with more provisioned IOPS to achieve faster latencies\.

For more information about Amazon EBS I/O characteristics, see the [Amazon EBS: Designing for Performance](https://www.youtube.com/watch?v=2wKgha8CZ_w) re:Invent presentation on this topic\.
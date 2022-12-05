# Provisioned IOPS SSD volumes<a name="provisioned-iops"></a>

Provisioned IOPS SSD volumes are backed by solid\-state drives \(SSDs\)\. They are the highest performance Amazon EBS storage volumes designed for critical, IOPS\-intensive, and throughput\-intensive workloads that require low latency\.

Amazon EBS offers three types of Provisioned IOPS SSD volumes:
+ Provisioned IOPS SSD \(`io2`\) volumes
+ Provisioned IOPS SSD \(`io2`\) Block Express volumes
+ Provisioned IOPS SSD \(`io1`\) volumes

**Topics**
+ [Provisioned IOPS SSD \(io1 and io2\) volumes](#EBSVolumeTypes_piops)
+ [`io2` Block Express volumes](#io2-block-express)

The following table provides a summary of the use cases, characteristics, and performance of Provisioned IOPS SSD volumes\. For information about the maximum IOPS and throughput per instance type, see [Amazon EBS–optimized instances](ebs-optimized.md)\.


|  | Provisioned IOPS SSD | 
| --- | --- | 
| Volume type | io2 Block Express \* | io2 | io1 | 
| Durability | 99\.999% durability \(0\.001% annual failure rate\) | 99\.999% durability \(0\.001% annual failure rate\) | 99\.8% \- 99\.9% durability \(0\.1% \- 0\.2% annual failure rate\) | 
| Use cases |  Workloads that require: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/provisioned-iops.html)  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/provisioned-iops.html)  | 
| Volume size | 4 GiB \- 64 TiB | 4 GiB \- 16 TiB  | 
| Max IOPS per volume \(16 KiB I/O\) | 256,000 | 64,000 † | 
| Max throughput per volume | 4,000 MiB/s | 1,000 MiB/s † | 
| Amazon EBS Multi\-attach | Supported | 
| Boot volume | Supported | 

\* `io2` Block Express volumes are supported with C6in, C7g, M6in, M6idn, R5b, R6in, R6idn, Trn1, X2idn, and X2iedn instances\. `io2` volumes attached to these instances, during or after launch, automatically run on Block Express\. For more information, see [`io2` Block Express volumes](#io2-block-express)\.

† To achieve maximum throughput of 1,000 MiB/s, the volume must be provisioned with 64,000 IOPS and it must be attached to an [instance built on the Nitro System](instance-types.md#ec2-nitro-instances)\. `io1` volumes created before December 6, 2017 and that have not been modified since creation, might not reach full performance unless you [modify the volume](ebs-modify-volume.md)\.

## Provisioned IOPS SSD \(io1 and io2\) volumes<a name="EBSVolumeTypes_piops"></a>

Provisioned IOPS SSD \(`io1` and `io2`\) volumes are designed to meet the needs of I/O\-intensive workloads, particularly database workloads, that are sensitive to storage performance and consistency\. Provisioned IOPS SSD volumes use a consistent IOPS rate, which you specify when you create the volume, and Amazon EBS delivers the provisioned performance 99\.9 percent of the time\.

`io1` volumes are designed to provide 99\.8 percent to 99\.9 percent volume durability with an annual failure rate \(AFR\) no higher than 0\.2 percent, which translates to a maximum of two volume failures per 1,000 running volumes over a one\-year period\. `io2` volumes are designed to provide 99\.999 percent volume durability with an AFR no higher than 0\.001 percent, which translates to a single volume failure per 100,000 running volumes over a one\-year period\.

Provisioned IOPS SSD `io1` and `io2` volumes are available for all Amazon EC2 instance types\. Provisioned IOPS SSD `io2` volumes attached to C6in, C7g, M6in, M6idn, R5b, R6in, R6idn, Trn1, X2idn, and X2iedn instances run on EBS Block Express\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express)\.

**Considerations for `io2` volumes**  

+ Keep the following in mind when **launching instances with `io2` volumes**:
  + If you launch an instance with an `io2` volume using an instance type that supports Block Express, the volume automatically runs on Block Express, regardless of the volume's size and IOPS\.
  + You can't launch an instance type that does not support [Block Express](#io2-block-express) with an `io2` volume that has a size greater than 16 TiB or IOPS greater than 64,000\.
  + You can't launch an instance with an encrypted `io2` volume that has a size greater than 16 TiB or IOPS greater than 64,000 from an unencrypted AMI or a shared encrypted AMI with Block Express\. In this case, you must first create an encrypted AMI in your account and then use that AMI to launch the instance\.
+ Keep the following in mind when **creating `io2` volumes**:
  + If you create an `io2` volume with a size greater than 16 TiB or IOPS greater than 64,000 in a Region where [Block Express](#io2-block-express) is supported, the volume automatically runs on Block Express\.
  + You can't create an `io2` volume with a size greater than 16 TiB or IOPS greater than 64,000 in a Region where [Block Express](#io2-block-express) is not supported\.
  + If you create an `io2` volume with a size of 16 TiB or less and IOPS of 64,000 or less in a Region where [Block Express](#io2-block-express) is supported, the volume does not run on Block Express\.
  + You can't create an encrypted `io2` volume that has a size greater than 16 TiB or IOPS greater than 64,000 from an unencrypted snapshot or a shared encrypted snapshot\. In this case, you must first create an encrypted snapshot in your account and then use that snapshot to create the volume\.
+ Keep the following in mind when **attaching `io2` volumes** to instances:
  + If you attach an `io2` volume to an instance that supports Block Express, the volume automatically runs on Block Express\. It can take up to 48 hours to optimize the volume for Block Express\. During this time, the volume provides `io2` latency\. After the volume has been optimized, it provides the sub\-millisecond latency supported by Block Express\.
  + You can't attach an `io2` volume with a size greater than 16 TiB or IOPS greater than 64,000 to an instance type that does not support [Block Express](#io2-block-express)\.
  + If you detach an `io2` volume with a size of 16 TiB or less and IOPS of 64,000 or less from an instance that supports Block Express and attach it to an instance type that does not support [Block Express](#io2-block-express), the volume no longer runs on Block Express and it provides `io2` latency\.
+ Keep the following in mind when **modifying `io2` volumes**:
  + You can't modify an `io2` volume and increase its size beyond 16 TiB or its IOPS beyond 64,000 while it is attached to an instance type that does not support [Block Express](#io2-block-express)\.

**Performance**  
Provisioned IOPS SSD volumes can range in size from 4 GiB to 16 TiB and you can provision from 100 IOPS up to 64,000 IOPS per volume\. You can achieve up to 64,000 IOPS only on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\. On other instance families you can achieve performance up to 32,000 IOPS\. The maximum ratio of provisioned IOPS to requested volume size \(in GiB\) is 50:1 for `io1` volumes, and 500:1 for `io2` volumes\. For example, a 100 GiB `io1` volume can be provisioned with up to 5,000 IOPS, while a 100 GiB `io2` volume can be provisioned with up to 50,000 IOPS\. On a supported instance type, the following volume sizes allow provisioning up to the 64,000 IOPS maximum:
+ `io1` volume 1,280 GiB in size or greater \(50 × 1,280 GiB = 64,000 IOPS\)
+ `io2` volume 128 GiB in size or greater \(500 × 128 GiB = 64,000 IOPS\)

Provisioned IOPS SSD volumes provisioned with up to 32,000 IOPS support a maximum I/O size of 256 KiB and yield as much as 500 MiB/s of throughput\. With the I/O size at the maximum, peak throughput is reached at 2,000 IOPS\. Volumes provisioned with more than 32,000 IOPS \(up to the maximum of 64,000 IOPS\) yield a linear increase in throughput at a rate of 16 KiB per provisioned IOPS\. For example, a volume provisioned with 48,000 IOPS can support up to 750 MiB/s of throughput \(16 KiB per provisioned IOPS × 48,000 provisioned IOPS = 750 MiB/s\)\. To achieve the maximum throughput of 1,000 MiB/s, a volume must be provisioned with 64,000 IOPS \(16 KiB per provisioned IOPS × 64,000 provisioned IOPS = 1,000 MiB/s\)\. The following graph illustrates these performance characteristics:

![\[Throughput limits for io1 volumes\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/io1_throughput.png)

Your per\-I/O latency experience depends on the provisioned IOPS and on your workload profile\. For the best I/O latency experience, ensure that you provision IOPS to meet the I/O profile of your workload\.

## `io2` Block Express volumes<a name="io2-block-express"></a>

**Note**  
`io2` Block Express volumes are supported with C6in, C7g, M6in, M6idn, R5b, R6in, R6idn, Trn1, X2idn, and X2iedn instances\.

`io2` Block Express volumes is the next generation of Amazon EBS storage server architecture\. It has been built for the purpose of meeting the performance requirements of the most demanding I/O intensive applications that run on Nitro\-based Amazon EC2 instances\. 

Block Express architecture increases performance and scale\. Block Express servers communicate with Nitro\-based instances using the Scalable Reliable Datagram \(SRD\) networking protocol\. This interface is implemented in the Nitro Card dedicated for Amazon EBS I/O function on the host hardware of the instance\. It minimizes I/O delay and latency variation \(network jitter\), which provides faster and more consistent performance for your applications\. For more information, see [`io2` Block Express volumes](http://aws.amazon.com/ebs/provisioned-iops/)\.

`io2` Block Express volumes are suited for workloads that benefit from a single volume that provides sub\-millisecond latency, and supports higher IOPS, higher throughput, and larger capacity than `io2` volumes\.

`io2` Block Express volumes support the same features as `io2` volumes, including Multi\-Attach and encryption\.

**Note**  
You can't attach a Multi\-Attach enabled `io2` volume to instance types that support Block Express and instance types that do not support Block Express at the same time\. For more information, see [io2 Block Express volumes](AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express) 

**Topics**
+ [Considerations](#io2-bx-considerations)
+ [Performance](#io2-bx-perf)
+ [Quotas](#io2-bx-quotas)
+ [Pricing and billing](#io2-bx-pricing)

### Considerations<a name="io2-bx-considerations"></a>
+ `io2` Block Express volumes are currently supported with C6in, C7g, M6in, M6idn, R5b, R6in, R6idn, Trn1, X2idn, and X2iedn instances only\.
+ `io2` Block Express volumes are currently available in all Regions where supported instances are available, including US East \(Ohio\), US East \(N\. Virginia\), US West \(Oregon\), Asia Pacific \(Mumbai\), Asia Pacific \(Seoul\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), Canada \(Central\), Europe \(Frankfurt\), Europe \(Ireland\), Europe \(London\), and Europe \(Stockholm\)\. Instance availability might vary by Availability Zone\. For more information, see [Find an Amazon EC2 instance type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-discovery.html)\.

### Performance<a name="io2-bx-perf"></a>

With `io2` Block Express volumes, you can provision volumes with:
+ Sub\-millisecond average latency
+ Storage capacity up to 64 TiB \(65,536 GiB\)
+ Provisioned IOPS up to 256,000, with an IOPS:GiB ratio of 1,000:1\. Maximum IOPS can be provisioned with volumes 256 GiB in size and larger \(1,000 IOPS × 256 GiB = 256,000 IOPS\)\.
+ Volume throughput up to 4,000 MiB/s\. Throughput scales proportionally up to 0\.256 MiB/s per provisioned IOPS\. Maximum throughput can be achieved at 16,000 IOPS or higher\. 

![\[Throughput limits for io2 Block Express volumes\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/io2_bx.png)

### Quotas<a name="io2-bx-quotas"></a>

`io2` Block Express volumes adhere to the same service quotas as `io2` volumes\. For more information, see [Amazon EBS quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html)\.

### Pricing and billing<a name="io2-bx-pricing"></a>

`io2` volumes and `io2` Block Express volumes are billed at the same rate\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

Usage reports do not distinguish between `io2` Block Express volumes and `io2` volumes\. We recommend that you use tags to help you identify costs associated with `io2` Block Express volumes\.
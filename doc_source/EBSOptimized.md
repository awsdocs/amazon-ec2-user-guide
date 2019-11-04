# Amazon EBS–Optimized Instances<a name="EBSOptimized"></a>

An Amazon EBS–optimized instance uses an optimized configuration stack and provides additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

EBS–optimized instances deliver dedicated bandwidth to Amazon EBS, with options between 425 Mib/s and 14,000 Mib/s, depending on the instance type you use\. When attached to an EBS–optimized instance, General Purpose SSD \(`gp2`\) volumes are designed to deliver their baseline and burst performance 99% of the time, and Provisioned IOPS SSD \(`io1`\) volumes are designed to deliver their provisioned performance 99\.9% of the time\. Both Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) guarantee performance consistency of 90% of burst throughput 99% of the time\. Non\-compliant periods are approximately uniformly distributed, targeting 99% of expected total throughput each hour\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.

**Topics**
+ [Supported Instance Types](#ebs-optimization-support)
+ [Enabling EBS Optimization at Launch](#enable-ebs-optimization)
+ [Enable EBS Optimization for a Running Instance](#modify-ebs-optimized-attribute)

## Supported Instance Types<a name="ebs-optimization-support"></a>

The following tables show which instance types support EBS optimization\. They include the dedicated bandwidth to Amazon EBS, the typical maximum aggregate throughput that can be achieved on that connection with a streaming read workload and 128 KiB I/O size, and the maximum IOPS the instance can support if you are using a 16 KiB I/O size\. Choose an EBS–optimized instance that provides more dedicated Amazon EBS throughput than your application needs; otherwise, the connection between Amazon EBS and Amazon EC2 can become a performance bottleneck\.

### EBS Optimized By Default<a name="current"></a>

The following table lists the instance types that support EBS optimization and EBS optimization is enabled by default\. There is no need to enable EBS optimization and no effect if you disable EBS optimization\.


| Instance type | Maximum bandwidth \(Mib/s\) | Maximum throughput \(MiB/s, 128 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | 
| a1\.medium | 3,500 | 437\.5 | 20,000 | 
| a1\.large | 3,500 | 437\.5 | 20,000 | 
| a1\.xlarge | 3,500 | 437\.5 | 20,000 | 
| a1\.2xlarge | 3,500 | 437\.5 | 20,000 | 
| a1\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| a1\.metal | 3,500 | 437\.5 | 20,000 | 
| c4\.large | 500 | 62\.5 | 4,000 | 
| c4\.xlarge | 750 | 93\.75 | 6,000 | 
| c4\.2xlarge | 1,000 | 125 | 8,000 | 
| c4\.4xlarge | 2,000 | 250 | 16,000 | 
| c4\.8xlarge | 4,000 | 500 | 32,000 | 
| c5\.large \* | 3,500 | 437\.5 | 20,000 | 
| c5\.xlarge \* | 3,500 | 437\.5 | 20,000 | 
| c5\.2xlarge \* | 3,500 | 437\.5 | 20,000 | 
| c5\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| c5\.9xlarge | 7,000 | 875 | 40,000 | 
| c5\.12xlarge | 7,000 | 875 | 40,000 | 
| c5\.18xlarge | 14,000 | 1,750 | 80,000 | 
| c5\.24xlarge | 14,000 | 1,750 | 80,000 | 
| c5\.metal | 14,000 | 1,750 | 80,000 | 
| c5d\.large \* | 3,500 | 437\.5 | 20,000 | 
| c5d\.xlarge \* | 3,500 | 437\.5 | 20,000 | 
| c5d\.2xlarge \* | 3,500 | 437\.5 | 20,000 | 
| c5d\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| c5d\.9xlarge | 7,000 | 875 | 40,000 | 
| c5d\.18xlarge | 14,000 | 1,750 | 80,000 | 
| c5n\.large \* | 3,500 | 437\.5 | 20,000 | 
| c5n\.xlarge \* | 3,500 | 437\.5 | 20,000 | 
| c5n\.2xlarge \* | 3,500 | 437\.5 | 20,000 | 
| c5n\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| c5n\.9xlarge | 7,000 | 875 | 40,000 | 
| c5n\.18xlarge | 14,000 | 1,750 | 80,000 | 
| c5n\.metal | 14,000 | 1,750 | 80,000 | 
| d2\.xlarge | 750 | 93\.75 | 6,000 | 
| d2\.2xlarge | 1,000 | 125 | 8,000 | 
| d2\.4xlarge | 2,000 | 250 | 16,000 | 
| d2\.8xlarge | 4,000 | 500 | 32,000 | 
| f1\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| f1\.4xlarge | 3,500 | 400 | 44,000 | 
| f1\.16xlarge | 14,000 | 1,750 | 75,000 | 
| g3s\.xlarge | 850 | 100 | 5,000 | 
| g3\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| g3\.8xlarge | 7,000 | 875 | 40,000 | 
| g3\.16xlarge | 14,000 | 1,750 | 80,000 | 
| g4dn\.xlarge | 3,500 | 437\.5 | 10,000 | 
| g4dn\.2xlarge | 3,500 | 437\.5 | 20,000 | 
| g4dn\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| g4dn\.8xlarge | 7,000 | 875 | 40,000 | 
| g4dn\.12xlarge | 7,000 | 875 | 40,000 | 
| g4dn\.16xlarge | 7,000 | 875 | 40,000 | 
| h1\.2xlarge | 1,750 | 218\.75 | 12,000 | 
| h1\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| h1\.8xlarge | 7,000 | 875 | 40,000 | 
| h1\.16xlarge | 14,000 | 1,750 | 80,000 | 
| i3\.large | 425 | 53\.13 | 3000 | 
| i3\.xlarge | 850 | 106\.25 | 6000 | 
| i3\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| i3\.4xlarge | 3,500 | 437\.5 | 16,000 | 
| i3\.8xlarge | 7,000 | 875 | 32,500 | 
| i3\.16xlarge | 14,000 | 1,750 | 65,000 | 
| i3\.metal | 14,000 | 1,750 | 65,000 | 
| i3en\.large \* | 3,500 | 437\.5 | 20,000 | 
| i3en\.xlarge \* | 3,500 | 437\.5 | 20,000 | 
| i3en\.2xlarge \* | 3,500 | 437\.5 | 20,000 | 
| i3en\.3xlarge \* | 3,500 | 437\.5 | 20,000 | 
| i3en\.6xlarge | 3,500 | 437\.5 | 20,000 | 
| i3en\.12xlarge | 7,000 | 875 | 40,000 | 
| i3en\.24xlarge | 14,000 | 1,750 | 80,000 | 
| i3en\.metal | 14,000 | 1,750 | 80,000 | 
| m4\.large | 450 | 56\.25 | 3,600 | 
| m4\.xlarge | 750 | 93\.75 | 6,000 | 
| m4\.2xlarge | 1,000 | 125 | 8,000 | 
| m4\.4xlarge | 2,000 | 250 | 16,000 | 
| m4\.10xlarge | 4,000 | 500 | 32,000 | 
| m4\.16xlarge | 10,000 | 1,250 | 65,000 | 
| m5\.large \* | 3,500 | 437\.5 | 18,750 | 
| m5\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| m5\.8xlarge | 5,000 | 625 | 30,000 | 
| m5\.12xlarge | 7,000 | 875 | 40,000 | 
| m5\.16xlarge | 10,000 | 1,250 | 60,000 | 
| m5\.24xlarge | 14,000 | 1,750 | 80,000 | 
| m5\.metal | 14,000 | 1,750 | 80,000 | 
| m5a\.large \* | 2,120 | 265 | 16,000 | 
| m5a\.xlarge \* | 2,120 | 265 | 16,000 | 
| m5a\.2xlarge \* | 2,120 | 265 | 16,000 | 
| m5a\.4xlarge | 2,120 | 265 | 16,000 | 
| m5a\.8xlarge | 3,500 | 437\.5 | 20,000 | 
| m5a\.12xlarge | 5,000 | 625 | 30,000 | 
| m5a\.16xlarge | 7,000 | 875 | 40,000 | 
| m5a\.24xlarge | 10,000 | 1,250 | 60,000 | 
| m5ad\.large \* | 2,120 | 265 | 16,000 | 
| m5ad\.xlarge \* | 2,120 | 265 | 16,000 | 
| m5ad\.2xlarge \* | 2,120 | 265 | 16,000 | 
| m5ad\.4xlarge | 2,120 | 265 | 16,000 | 
| m5ad\.12xlarge | 5,000 | 675 | 30,000 | 
| m5ad\.24xlarge | 10,000 | 1,250 | 60,000 | 
| m5d\.large \* | 3,500 | 437\.5 | 18,750 | 
| m5d\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5d\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5d\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| m5d\.8xlarge | 5,000 | 625 | 30,000 | 
| m5d\.12xlarge | 7,000 | 875 | 40,000 | 
| m5d\.16xlarge | 10,000 | 1,250 | 60,000 | 
| m5d\.24xlarge | 14,000 | 1,750 | 80,000 | 
| m5d\.metal | 14,000 | 1,750 | 80,000 | 
| m5dn\.large \* | 3,500 | 437\.5 | 18,750 | 
| m5dn\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5dn\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5dn\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| m5dn\.8xlarge | 5,000 | 625 | 30,000 | 
| m5dn\.12xlarge | 7,000 | 875 | 40,000 | 
| m5dn\.16xlarge | 10,000 | 1,250 | 60,000 | 
| m5dn\.24xlarge | 14,000 | 1,750 | 80,000 | 
| m5n\.large \* | 3,500 | 437\.5 | 18,750 | 
| m5n\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5n\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| m5n\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| m5n\.8xlarge | 5,000 | 625 | 30,000 | 
| m5n\.12xlarge | 7,000 | 875 | 40,000 | 
| m5n\.16xlarge | 10,000 | 1,250 | 60,000 | 
| m5n\.24xlarge | 14,000 | 1,750 | 80,000 | 
| p2\.xlarge | 750 | 93\.75 | 6,000 | 
| p2\.8xlarge | 5,000 | 625 | 32,500 | 
| p2\.16xlarge | 10,000 | 1,250 | 65,000 | 
| p3\.2xlarge | 1,750 | 218 | 10,000 | 
| p3\.8xlarge | 7,000 | 875 | 40,000 | 
| p3\.16xlarge | 14,000 | 1,750 | 80,000 | 
| p3dn\.24xlarge | 14,000 | 1,750 | 80,000 | 
| r4\.large | 425 | 53\.13 | 3,000 | 
| r4\.xlarge | 850 | 106\.25 | 6,000 | 
| r4\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| r4\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| r4\.8xlarge | 7,000 | 875 | 37,500 | 
| r4\.16xlarge | 14,000 | 1,750 | 75,000 | 
| r5\.large \* | 3,500 | 437\.5 | 18,750 | 
| r5\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| r5\.8xlarge | 5,000 | 625 | 30,000 | 
| r5\.12xlarge | 7,000 | 875 | 40,000 | 
| r5\.16xlarge | 10,000 | 1,250 | 60,000 | 
| r5\.24xlarge | 14,000 | 1,750 | 80,000 | 
| r5\.metal | 14,000 | 1,750 | 80,000 | 
| r5a\.large \* | 2,120 | 265 | 16,000 | 
| r5a\.xlarge \* | 2,120 | 265 | 16,000 | 
| r5a\.2xlarge \* | 2,120 | 265 | 16,000 | 
| r5a\.4xlarge | 2,120 | 265 | 16,000 | 
| r5a\.8xlarge | 3,500 | 437\.5 | 32,000 | 
| r5a\.12xlarge | 5,000 | 625 | 30,000 | 
| r5a\.16xlarge | 7,000 | 875 | 40,000 | 
| r5a\.24xlarge | 10,000 | 1,250 | 60,000 | 
| r5ad\.large \* | 2,210 | 265 | 16,000 | 
| r5ad\.xlarge \* | 2,210 | 265 | 16,000 | 
| r5ad\.2xlarge \* | 2,210 | 265 | 16,000 | 
| r5ad\.4xlarge | 2,210 | 265 | 16,000 | 
| r5ad\.12xlarge | 5,000 | 625 | 30,000 | 
| r5ad\.24xlarge | 10,000 | 1,250 | 60,000 | 
| r5d\.large \* | 3,500 | 437\.5 | 18,750 | 
| r5d\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5d\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5d\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| r5d\.8xlarge | 5,000 | 625 | 30,000 | 
| r5d\.12xlarge | 7,000 | 875 | 40,000 | 
| r5d\.16xlarge | 10,000 | 1,250 | 60,000 | 
| r5d\.24xlarge | 14,000 | 1,750 | 80,000 | 
| r5d\.metal | 14,000 | 1,750 | 80,000 | 
| r5dn\.large \* | 3,500 | 437\.5 | 18,750 | 
| r5dn\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5dn\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5dn\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| r5dn\.8xlarge | 5,000 | 625 | 30,000 | 
| r5dn\.12xlarge | 7,000 | 875 | 40,000 | 
| r5dn\.16xlarge | 10,000 | 1,250 | 60,000 | 
| r5dn\.24xlarge | 14,000 | 1,750 | 80,000 | 
| r5n\.large \* | 3,500 | 437\.5 | 18,750 | 
| r5n\.xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5n\.2xlarge \* | 3,500 | 437\.5 | 18,750 | 
| r5n\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| r5n\.8xlarge | 5,000 | 625 | 30,000 | 
| r5n\.12xlarge | 7,000 | 875 | 40,000 | 
| r5n\.16xlarge | 10,000 | 1,250 | 60,000 | 
| r5n\.24xlarge | 14,000 | 1,750 | 80,000 | 
| t3\.nano \* | 1,536 | 192 | 11,800 | 
| t3\.micro \* | 1,536 | 192 | 11,800 | 
| t3\.small \* | 1,536 | 192 | 11,800 | 
| t3\.medium \* | 1,536 | 192 | 11,800 | 
| t3\.large \* | 2,048 | 256 | 15,700 | 
| t3\.xlarge \* | 2,048 | 256 | 15,700 | 
| t3\.2xlarge \* | 2,048 | 256 | 15,700 | 
| t3a\.nano \* | 1,536 | 192 | 11,800 | 
| t3a\.micro \* | 1,536 | 192 | 11,800 | 
| t3a\.small \* | 1,536 | 192 | 11,800 | 
| t3a\.medium \* | 1,536 | 192 | 11,800 | 
| t3a\.large \* | 2,048 | 256 | 15,700 | 
| t3a\.xlarge \* | 2,048 | 256 | 15,700 | 
| t3a\.2xlarge \* | 2,048 | 256 | 15,700 | 
| u\-6tb1\.metal | 14,000 | 1,750 | 80,000 | 
| u\-9tb1\.metal | 14,000 | 1,750 | 80,000 | 
| u\-12tb1\.metal | 14,000 | 1,750 | 80,000 | 
| u\-18tb1\.metal | 28,000 | 3,500 | 160,000 | 
| u\-24tb1\.metal | 28,000 | 3,500 | 160,000 | 
| x1\.16xlarge | 7,000 | 875 | 40,000 | 
| x1\.32xlarge | 14,000 | 1,750 | 80,000 | 
| x1e\.xlarge | 500 | 62\.5 | 3,700 | 
| x1e\.2xlarge | 1,000 | 125 | 7,400 | 
| x1e\.4xlarge | 1,750 | 218\.75 | 10,000 | 
| x1e\.8xlarge | 3,500 | 437\.5 | 20,000 | 
| x1e\.16xlarge | 7,000 | 875 | 40,000 | 
| x1e\.32xlarge | 14,000 | 1,750 | 80,000 | 
| z1d\.large \* | 2,333 | 291 | 13,333 | 
| z1d\.xlarge \* | 2,333 | 291 | 13,333 | 
| z1d\.2xlarge | 2,333 | 292 | 13,333 | 
| z1d\.3xlarge | 3,500 | 438 | 20,000 | 
| z1d\.6xlarge | 7,000 | 875 | 40,000 | 
| z1d\.12xlarge | 14,000 | 1,750 | 80,000 | 
| z1d\.metal | 14,000 | 1,750 | 80,000 | 

\* These instance types can support maximum performance for 30 minutes at least once every 24 hours\. For example, `c5.large` instances can deliver 437\.5 Mib/s for 30 minutes at least once every 24 hours\. If you have a workload that requires sustained maximum performance for longer than 30 minutes, select an instance type according to baseline performance as shown in the following table:


| Instance type | Baseline bandwidth \(Mib/s\) | Baseline throughput \(MiB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | 
| c5\.large | 525 | 65\.625 | 4,000 | 
| c5\.xlarge | 800 | 100 | 6,000 | 
| c5\.2xlarge | 1,750 | 218\.75 | 10,000 | 
| c5d\.large | 525 | 65\.625 | 4,000 | 
| c5d\.xlarge | 800 | 100 | 6,000 | 
| c5d\.2xlarge | 1,750 | 218\.75 | 10,000 | 
| c5n\.large | 525 | 65\.625 | 4,000 | 
| c5n\.xlarge | 800 | 100 | 6,000 | 
| c5n\.2xlarge | 1,750 | 218\.75 | 10,000 | 
| i3en\.large | 425 | 53\.125 | 3,000 | 
| i3en\.xlarge | 850 | 106\.25 | 6,000 | 
| i3en\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| i3en\.3xlarge | 2,800 | 350 | 15,000 | 
| m5\.large | 480 | 60 | 3,600 | 
| m5\.xlarge | 850 | 106\.25 | 6,000 | 
| m5\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| m5a\.large | 480 | 60 | 3,600 | 
| m5a\.xlarge | 800 | 100 | 6,000 | 
| m5a\.2xlarge | 1,166 | 146 | 8,333 | 
| m5ad\.large | 480 | 60 | 3,600 | 
| m5ad\.xlarge | 800 | 100 | 6,000 | 
| m5ad\.2xlarge | 1,166 | 146 | 8,333 | 
| m5d\.large | 480 | 60 | 3,600 | 
| m5d\.xlarge | 850 | 106\.25 | 6,000 | 
| m5d\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| r5\.large | 480 | 60 | 3,600 | 
| r5\.xlarge | 850 | 106\.25 | 6,000 | 
| r5\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| r5a\.large | 480 | 60 | 3,600 | 
| r5a\.xlarge | 800 | 100 | 6,000 | 
| r5a\.2xlarge | 1,166 | 146 | 8,333 | 
| r5ad\.large | 480 | 60 | 3,600 | 
| r5ad\.xlarge | 800 | 100 | 6,000 | 
| r5ad\.2xlarge | 1,166 | 146 | 8,333 | 
| r5d\.large | 480 | 60 | 3,600 | 
| r5d\.xlarge | 850 | 106\.25 | 6,000 | 
| r5d\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| t3\.nano | 32 | 4 | 250 | 
| t3\.micro | 64 | 8 | 500 | 
| t3\.small | 128 | 16 | 1,000 | 
| t3\.medium | 256 | 32 | 2,000 | 
| t3\.large | 512 | 64 | 4,000 | 
| t3\.xlarge | 512 | 64 | 4,000 | 
| t3\.2xlarge | 512 | 64 | 4,000 | 
| t3a\.nano | 32 | 4 | 250 | 
| t3a\.micro | 64 | 8 | 500 | 
| t3a\.small | 128 | 16 | 1,000 | 
| t3a\.medium | 256 | 32 | 2,000 | 
| t3a\.large | 512 | 64 | 4,000 | 
| t3a\.xlarge | 512 | 64 | 4,000 | 
| t3a\.2xlarge | 512 | 64 | 4,000 | 
| z1d\.large | 583 | 73 | 3,333 | 
| z1d\.xlarge | 1,167 | 146 | 6,667 | 

The `EBSIOBalance%` and `EBSByteBalance%` metrics can help you determine if your instances are sized correctly\. You can view these metrics in the CloudWatch console and set an alarm that is triggered based on a threshold you specify\. These metrics are expressed as a percentage\. Instances with a consistently low balance percentage are candidates for upsizing\. Instances where the balance percentage never drops below 100% are candidates for downsizing\. For more information, see [Monitoring Your Instances Using CloudWatch](using-cloudwatch.md)\.

### EBS Optimization Supported<a name="previous"></a>

The following table lists the instance types that support EBS optimization but EBS optimization is not enabled by default\. You can enable EBS optimization when you launch these instances or after they are running\. Instances must have EBS optimization enabled to achieve the level of performance described\. When you enable EBS optimization for an instance that is not EBS\-optimized by default, you pay an additional low, hourly fee for the dedicated capacity\. For pricing information, see EBS\-optimized Instances on the [Amazon EC2 Pricing page for On\-Demand instances](http://aws.amazon.com/ec2/pricing/on-demand/)\.


| Instance type | Maximum bandwidth \(Mib/s\) | Maximum throughput \(MiB/s, 128 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | 
| c1\.xlarge | 1,000 | 125 | 8,000 | 
| c3\.xlarge | 500 | 62\.5 | 4,000 | 
| c3\.2xlarge | 1,000 | 125 | 8,000 | 
| c3\.4xlarge | 2,000 | 250 | 16,000 | 
| g2\.2xlarge | 1,000 | 125 | 8,000 | 
| i2\.xlarge | 500 | 62\.5 | 4,000 | 
| i2\.2xlarge | 1,000 | 125 | 8,000 | 
| i2\.4xlarge | 2,000 | 250 | 16,000 | 
| m1\.large | 500 | 62\.5 | 4,000 | 
| m1\.xlarge | 1,000 | 125 | 8,000 | 
| m2\.2xlarge | 500 | 62\.5 | 4,000 | 
| m2\.4xlarge | 1,000 | 125 | 8,000 | 
| m3\.xlarge | 500 | 62\.5 | 4,000 | 
| m3\.2xlarge | 1,000 | 125 | 8,000 | 
| r3\.xlarge | 500 | 62\.5 | 4,000 | 
| r3\.2xlarge | 1,000 | 125 | 8,000 | 
| r3\.4xlarge | 2,000 | 250 | 16,000 | 

The `i2.8xlarge`, `c3.8xlarge`, and `r3.8xlarge` instances do not have dedicated EBS bandwidth and therefore do not offer EBS optimization\. On these instances, network traffic and Amazon EBS traffic share the same 10\-gigabit network interface\.

## Enabling EBS Optimization at Launch<a name="enable-ebs-optimization"></a>

You can enable optimization for an instance by setting its attribute for EBS optimization\.

**To enable Amazon EBS optimization when launching an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, select an AMI\.

1. In **Step 2: Choose an Instance Type**, select an instance type that is listed as supporting Amazon EBS optimization\.

1. In **Step 3: Configure Instance Details**, complete the fields that you need and choose **Launch as EBS\-optimized instance**\. If the instance type that you selected in the previous step doesn't support Amazon EBS optimization, this option is not present\. If the instance type that you selected is Amazon EBS–optimized by default, this option is selected and you can't deselect it\.

1. Follow the directions to complete the wizard and launch your instance\.

**To enable EBS optimization when launching an instance using the command line**

You can use one of the following options with the corresponding command\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ `--ebs-optimized` with [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
+ `-EbsOptimized` with [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Enable EBS Optimization for a Running Instance<a name="modify-ebs-optimized-attribute"></a>

You can enable or disable optimization for a running instance by modifying its Amazon EBS–optimized instance attribute\.

**To enable EBS optimization for a running instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, click **Instances**, and select the instance\.

1. Click **Actions**, select **Instance State**, and then click **Stop**\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. In the confirmation dialog box, click **Yes, Stop**\. It can take a few minutes for the instance to stop\.

1. With the instance still selected, click **Actions**, select **Instance Settings**, and then click **Change Instance Type**\.

1. In the **Change Instance Type** dialog box, do one of the following:
   + If the instance type of your instance is Amazon EBS–optimized by default, **EBS\-optimized** is selected and you can't change it\. You can choose **Cancel**, because Amazon EBS optimization is already enabled for the instance\.
   + If the instance type of your instance supports Amazon EBS optimization, choose **EBS\-optimized**, **Apply**\.
   + If the instance type of your instance does not support Amazon EBS optimization, you can't choose **EBS\-optimized**\. You can select an instance type from **Instance Type** that supports Amazon EBS optimization, and then choose **EBS\-optimized**, **Apply**\.

1. Choose **Actions**, **Instance State**, **Start**\.

**To enable EBS optimization for a running instance using the command line**

You can use one of the following options with the corresponding command\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ `--ebs-optimized` with [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ `-EbsOptimized` with [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)
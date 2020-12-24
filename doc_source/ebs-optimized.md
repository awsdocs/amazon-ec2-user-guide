# Amazon EBS–optimized instances<a name="ebs-optimized"></a>

An Amazon EBS–optimized instance uses an optimized configuration stack and provides additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

EBS–optimized instances deliver dedicated bandwidth to Amazon EBS\. When attached to an EBS–optimized instance, General Purpose SSD \(`gp2` and `gp3`\) volumes are designed to deliver their baseline and burst performance 99% of the time, and Provisioned IOPS SSD \(`io1` and `io2`\) volumes are designed to deliver their provisioned performance 99\.9% of the time\. Both Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) guarantee performance consistency of 90% of burst throughput 99% of the time\. Non\-compliant periods are approximately uniformly distributed, targeting 99% of expected total throughput each hour\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

**Topics**
+ [Supported instance types](#ebs-optimization-support)
+ [Getting maximum performance](#ebs-optimization-performance)
+ [Enabling EBS optimization at launch](#enable-ebs-optimization)
+ [Enable EBS optimization for an existing instance](#modify-ebs-optimized-attribute)

## Supported instance types<a name="ebs-optimization-support"></a>

The following tables show which instance types support EBS optimization\. They include the dedicated bandwidth to Amazon EBS, the typical maximum aggregate throughput that can be achieved on that connection with a streaming read workload and 128 KiB I/O size, and the maximum IOPS the instance can support if you are using a 16 KiB I/O size\. Choose an EBS–optimized instance that provides more dedicated Amazon EBS throughput than your application needs; otherwise, the connection between Amazon EBS and Amazon EC2 can become a performance bottleneck\.

### EBS optimized by default<a name="current"></a>

The following table lists the instance types that support EBS optimization and EBS optimization is enabled by default\. There is no need to enable EBS optimization and no effect if you disable EBS optimization\.


| Instance size | Maximum bandwidth \(Mbps\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | 
| a1\.medium \* | 3,500 | 437\.5 | 20,000 | 
| a1\.large \* | 3,500 | 437\.5 | 20,000 | 
| a1\.xlarge \* | 3,500 | 437\.5 | 20,000 | 
| a1\.2xlarge \* | 3,500 | 437\.5 | 20,000 | 
| a1\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| a1\.metal | 3,500 | 437\.5 | 20,000 | 
| c4\.large | 500 | 62\.5 | 4,000 | 
| c4\.xlarge | 750 | 93\.75 | 6,000 | 
| c4\.2xlarge | 1,000 | 125 | 8,000 | 
| c4\.4xlarge | 2,000 | 250 | 16,000 | 
| c4\.8xlarge | 4,000 | 500 | 32,000 | 
| c5\.large \* | 4,750 | 593\.75 | 20,000 | 
| c5\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c5\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c5\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| c5\.9xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c5\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c5\.18xlarge | 19,000 | 2,375 | 80,000 | 
| c5\.24xlarge | 19,000 | 2,375 | 80,000 | 
| c5\.metal | 19,000 | 2,375 | 80,000 | 
| c5a\.large \* | 3,170 | 396 | 13,300 | 
| c5a\.xlarge \* | 3,170 | 396 | 13,300 | 
| c5a\.2xlarge \* | 3,170 | 396 | 13,300 | 
| c5a\.4xlarge \* | 3,170 | 396 | 13,300 | 
| c5a\.8xlarge | 3,170 | 396 | 13,300 | 
| c5a\.12xlarge | 4,750 | 594 | 20,000 | 
| c5a\.16xlarge | 6,300 | 788 | 26,700 | 
| c5a\.24xlarge | 9,500 | 1,188 | 40,000 | 
| c5ad\.large \* | 3,170 | 396 | 13,300 | 
| c5ad\.xlarge \* | 3,170 | 396 | 13,300 | 
| c5ad\.2xlarge \* | 3,170 | 396 | 13,300 | 
| c5ad\.4xlarge \* | 3,170 | 396 | 13,300 | 
| c5ad\.8xlarge | 3,170 | 396 | 13,300 | 
| c5ad\.12xlarge | 4,750 | 594 | 20,000 | 
| c5ad\.16xlarge | 6,300 | 788 | 26,700 | 
| c5ad\.24xlarge | 9,500 | 1,188 | 40,000 | 
| c5d\.large \* | 4,750 | 593\.75 | 20,000 | 
| c5d\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c5d\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c5d\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| c5d\.9xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c5d\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c5d\.18xlarge | 19,000 | 2,375 | 80,000 | 
| c5d\.24xlarge | 19,000 | 2,375 | 80,000 | 
| c5d\.metal | 19,000 | 2,375 | 80,000 | 
| c5n\.large \* | 4,750 | 593\.75 | 20,000 | 
| c5n\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c5n\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c5n\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| c5n\.9xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c5n\.18xlarge | 19,000 | 2,375 | 80,000 | 
| c5n\.metal | 19,000 | 2,375 | 80,000 | 
| c6g\.medium \* | 4,750 | 593\.75 | 20,000 | 
| c6g\.large \* | 4,750 | 593\.75 | 20,000 | 
| c6g\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c6g\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c6g\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| c6g\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c6g\.12xlarge | 14,250 | 1,781\.25 | 50,000 | 
| c6g\.16xlarge | 19,000 | 2,375 | 80,000 | 
| c6g\.metal | 19,000 | 2,375 | 80,000 | 
| c6gd\.medium \* | 4,750 | 593\.75 | 20,000 | 
| c6gd\.large \* | 4,750 | 593\.75 | 20,000 | 
| c6gd\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c6gd\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| c6gd\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| c6gd\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c6gd\.12xlarge | 14,250 | 1,781\.25 | 50,000 | 
| c6gd\.16xlarge | 19,000 | 2,375 | 80,000 | 
| c6gd\.metal | 19,000 | 2,375 | 80,000 | 
| c6gn\.medium \* | 9,500 | 1,187\.5 | 40,000 | 
| c6gn\.large \* | 9,500 | 1,187\.5 | 40,000 | 
| c6gn\.xlarge \* | 9,500 | 1,187\.5 | 40,000 | 
| c6gn\.2xlarge \* | 9,500 | 1,187\.5 | 40,000 | 
| c6gn\.4xlarge | 9,500 | 1,187\.5 | 40,000 | 
| c6gn\.8xlarge | 19,000 | 2,375 | 80,000 | 
| c6gn\.12xlarge | 28,500 | 3,562\.5 | 120,000 | 
| c6gn\.16xlarge | 38,000 | 4,750 | 160,000 | 
| d2\.xlarge | 750 | 93\.75 | 6,000 | 
| d2\.2xlarge | 1,000 | 125 | 8,000 | 
| d2\.4xlarge | 2,000 | 250 | 16,000 | 
| d2\.8xlarge | 4,000 | 500 | 32,000 | 
| d3\.xlarge \* | 15,000 | 1,875 | 15,000 | 
| d3\.2xlarge \* | 15,000 | 1,875 | 15,000 | 
| d3\.4xlarge | 15,000 | 1,875 | 15,000 | 
| d3\.8xlarge | 25,000 | 3,125 | 30,000 | 
| d3en\.xlarge \* | 25,000 | 3,125 | 15,000 | 
| d3en\.2xlarge \* | 25,000 | 3,125 | 15,000 | 
| d3en\.4xlarge | 25,000 | 3,125 | 15,000 | 
| d3en\.8xlarge | 40,000 | 5,000 | 30,000 | 
| d3en\.12xlarge | 50,000 | 6,250 | 40,000 | 
| f1\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| f1\.4xlarge | 3,500 | 437\.5 | 44,000 | 
| f1\.16xlarge | 14,000 | 1,750 | 75,000 | 
| g3s\.xlarge | 850 | 106\.25 | 5,000 | 
| g3\.4xlarge | 3,500 | 437\.5 | 20,000 | 
| g3\.8xlarge | 7,000 | 875 | 40,000 | 
| g3\.16xlarge | 14,000 | 1,750 | 80,000 | 
| g4ad\.4xlarge \* | 3,170 | 396\.25 | 13,333 | 
| g4ad\.8xlarge | 3,170 | 396\.25 | 13,333 | 
| g4ad\.16xlarge | 6,300 | 787\.5 | 26,667 | 
| g4dn\.xlarge \* | 3,500 | 437\.5 | 20,000 | 
| g4dn\.2xlarge \* | 3,500 | 437\.5 | 20,000 | 
| g4dn\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| g4dn\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| g4dn\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| g4dn\.16xlarge | 9,500 | 1,187\.5 | 40,000 | 
| g4dn\.metal | 19,000 | 2,375 | 80,000 | 
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
| i3\.metal | 19,000 | 2,375 | 80,000 | 
| i3en\.large \* | 4,750 | 593\.75 | 20,000 | 
| i3en\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| i3en\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| i3en\.3xlarge \* | 4,750 | 593\.75 | 20,000 | 
| i3en\.6xlarge | 4,750 | 593\.75 | 20,000 | 
| i3en\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| i3en\.24xlarge | 19,000 | 2,375 | 80,000 | 
| i3en\.metal | 19,000 | 2,375 | 80,000 | 
| inf1\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| inf1\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| inf1\.6xlarge | 4,750 | 593\.75 | 20,000 | 
| inf1\.24xlarge | 19,000 | 2,375 | 80,000 | 
| m4\.large | 450 | 56\.25 | 3,600 | 
| m4\.xlarge | 750 | 93\.75 | 6,000 | 
| m4\.2xlarge | 1,000 | 125 | 8,000 | 
| m4\.4xlarge | 2,000 | 250 | 16,000 | 
| m4\.10xlarge | 4,000 | 500 | 32,000 | 
| m4\.16xlarge | 10,000 | 1,250 | 65,000 | 
| m5\.large \* | 4,750 | 593\.75 | 18,750 | 
| m5\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| m5\.8xlarge | 6,800 | 850 | 30,000 | 
| m5\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m5\.16xlarge | 13,600 | 1,700 | 60,000 | 
| m5\.24xlarge | 19,000 | 2,375 | 80,000 | 
| m5\.metal | 19,000 | 2,375 | 80,000 | 
| m5a\.large \* | 2,880 | 360 | 16,000 | 
| m5a\.xlarge \* | 2,880 | 360 | 16,000 | 
| m5a\.2xlarge \* | 2,880 | 360 | 16,000 | 
| m5a\.4xlarge | 2,880 | 360 | 16,000 | 
| m5a\.8xlarge | 4,750 | 593\.75 | 20,000 | 
| m5a\.12xlarge | 6,780 | 847\.5 | 30,000 | 
| m5a\.16xlarge | 9,500 | 1,187\.50 | 40,000 | 
| m5a\.24xlarge | 13,570 | 1,696\.25 | 60,000 | 
| m5ad\.large \* | 2,880 | 360 | 16,000 | 
| m5ad\.xlarge \* | 2,880 | 360 | 16,000 | 
| m5ad\.2xlarge \* | 2,880 | 360 | 16,000 | 
| m5ad\.4xlarge | 2,880 | 360 | 16,000 | 
| m5ad\.8xlarge | 4,750 | 593\.75 | 20,000 | 
| m5ad\.12xlarge | 6,780 | 847\.5 | 30,000 | 
| m5ad\.16xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m5ad\.24xlarge | 13,570 | 1,696\.25 | 60,000 | 
| m5d\.large \* | 4,750 | 593\.75 | 18,750 | 
| m5d\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5d\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5d\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| m5d\.8xlarge | 6,800 | 850 | 30,000 | 
| m5d\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m5d\.16xlarge | 13,600 | 1,700 | 60,000 | 
| m5d\.24xlarge | 19,000 | 2,375 | 80,000 | 
| m5d\.metal | 19,000 | 2,375 | 80,000 | 
| m5dn\.large \* | 4,750 | 593\.75 | 18,750 | 
| m5dn\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5dn\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5dn\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| m5dn\.8xlarge | 6,800 | 850 | 30,000 | 
| m5dn\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m5dn\.16xlarge | 13,600 | 1,700 | 60,000 | 
| m5dn\.24xlarge | 19,000 | 2,375 | 80,000 | 
| m5n\.large \* | 4,750 | 593\.75 | 18,750 | 
| m5n\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5n\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| m5n\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| m5n\.8xlarge | 6,800 | 850 | 30,000 | 
| m5n\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m5n\.16xlarge | 13,600 | 1,700 | 60,000 | 
| m5n\.24xlarge | 19,000 | 2,375 | 80,000 | 
| m5zn\.large \* | 3,170 | 396\.25 | 13,333 | 
| m5zn\.xlarge \* | 3,170 | 396\.25 | 13,333 | 
| m5zn\.2xlarge  | 3,170 | 396\.25 | 13,333 | 
| m5zn\.3xlarge  | 4,750 | 593\.75 | 20,000 | 
| m5zn\.6xlarge | 9,500 | 1187\.5 | 40,000 | 
| m5zn\.12xlarge | 19,000 | 2,375 | 80,000 | 
| m5zn\.metal | 19,000 | 2,375 | 80,000 | 
| m6g\.medium \* | 4,750 | 593\.75 | 20,000 | 
| m6g\.large \* | 4,750 | 593\.75 | 20,000 | 
| m6g\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| m6g\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| m6g\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| m6g\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m6g\.12xlarge | 14,250 | 1,781\.25 | 50,000 | 
| m6g\.16xlarge | 19,000 | 2,375 | 80,000 | 
| m6g\.metal | 19,000 | 2,375 | 80,000 | 
| m6gd\.medium \* | 4,750 | 593\.75 | 20,000 | 
| m6gd\.large \* | 4,750 | 593\.75 | 20,000 | 
| m6gd\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| m6gd\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| m6gd\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| m6gd\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| m6gd\.12xlarge | 14,250 | 1,781\.25 | 50,000 | 
| m6gd\.16xlarge | 19,000 | 2,375 | 80,000 | 
| m6gd\.metal | 19,000 | 2,375 | 80,000 | 
| mac1\.metal | 8,000 | 1,000 | 55,000 | 
| p2\.xlarge | 750 | 93\.75 | 6,000 | 
| p2\.8xlarge | 5,000 | 625 | 32,500 | 
| p2\.16xlarge | 10,000 | 1,250 | 65,000 | 
| p3\.2xlarge | 1,750 | 218\.75 | 10,000 | 
| p3\.8xlarge | 7,000 | 875 | 40,000 | 
| p3\.16xlarge | 14,000 | 1,750 | 80,000 | 
| p3dn\.24xlarge | 19,000 | 2,375 | 80,000 | 
| p4d\.2xlarge | 19,000 | 2,375 | 80,000 | 
| r4\.large | 425 | 53\.13 | 3,000 | 
| r4\.xlarge | 850 | 106\.25 | 6,000 | 
| r4\.2xlarge | 1,700 | 212\.5 | 12,000 | 
| r4\.4xlarge | 3,500 | 437\.5 | 18,750 | 
| r4\.8xlarge | 7,000 | 875 | 37,500 | 
| r4\.16xlarge | 14,000 | 1,750 | 75,000 | 
| r5\.large \* | 4,750 | 593\.75 | 18,750 | 
| r5\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| r5\.8xlarge | 6,800 | 850 | 30,000 | 
| r5\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r5\.16xlarge | 13,600 | 1,700 | 60,000 | 
| r5\.24xlarge | 19,000 | 2,375 | 80,000 | 
| r5\.metal | 19,000 | 2,375 | 80,000 | 
| r5a\.large \* | 2,880 | 360 | 16,000 | 
| r5a\.xlarge \* | 2,880 | 360 | 16,000 | 
| r5a\.2xlarge \* | 2,880 | 360 | 16,000 | 
| r5a\.4xlarge | 2,880 | 360 | 16,000 | 
| r5a\.8xlarge | 4,750 | 593\.75 | 20,000 | 
| r5a\.12xlarge | 6,780 | 847\.5 | 30,000 | 
| r5a\.16xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r5a\.24xlarge | 13,570 | 1,696\.25 | 60,000 | 
| r5ad\.large \* | 2,880 | 360 | 16,000 | 
| r5ad\.xlarge \* | 2,880 | 360 | 16,000 | 
| r5ad\.2xlarge \* | 2,880 | 360 | 16,000 | 
| r5ad\.4xlarge | 2,880 | 360 | 16,000 | 
| r5ad\.8xlarge | 4,750 | 593\.75 | 20,000 | 
| r5ad\.12xlarge | 6,780 | 847\.5 | 30,000 | 
| r5ad\.16xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r5ad\.24xlarge | 13,570 | 1,696\.25 | 60,000 | 
| r5b\.large \* | 10,000 | 1,250 | 43,333 | 
| r5b\.xlarge \* | 10,000 | 1,250 | 43,333 | 
| r5b\.2xlarge \* | 10,000 | 1,250 | 43,333 | 
| r5b\.4xlarge | 10,000 | 1,250 | 43,333 | 
| r5b\.8xlarge | 20,000 | 2,500 | 86,667 | 
| r5b\.12xlarge | 30,000 | 3,750 | 130,000 | 
| r5b\.16xlarge | 40,000 | 5,000 | 173,333 | 
| r5b\.24xlarge | 60,000 | 7,500 | 260,000 | 
| r5b\.metal | 60,000 | 7,500 | 260,000 | 
| r5d\.large \* | 4,750 | 593\.75 | 18,750 | 
| r5d\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5d\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5d\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| r5d\.8xlarge | 6,800 | 850 | 30,000 | 
| r5d\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r5d\.16xlarge | 13,600 | 1,700 | 60,000 | 
| r5d\.24xlarge | 19,000 | 2,375 | 80,000 | 
| r5d\.metal | 19,000 | 2,375 | 80,000 | 
| r5dn\.large \* | 4,750 | 593\.75 | 18,750 | 
| r5dn\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5dn\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5dn\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| r5dn\.8xlarge | 6,800 | 850 | 30,000 | 
| r5dn\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r5dn\.16xlarge | 13,600 | 1,700 | 60,000 | 
| r5dn\.24xlarge | 19,000 | 2,375 | 80,000 | 
| r5n\.large \* | 4,750 | 593\.75 | 18,750 | 
| r5n\.xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5n\.2xlarge \* | 4,750 | 593\.75 | 18,750 | 
| r5n\.4xlarge | 4,750 | 593\.75 | 18,750 | 
| r5n\.8xlarge | 6,800 | 850 | 30,000 | 
| r5n\.12xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r5n\.16xlarge | 13,600 | 1,700 | 60,000 | 
| r5n\.24xlarge | 19,000 | 2,375 | 80,000 | 
| r6g\.medium \* | 4,750 | 593\.75 | 20,000 | 
| r6g\.large \* | 4,750 | 593\.75 | 20,000 | 
| r6g\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| r6g\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| r6g\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| r6g\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r6g\.12xlarge | 14,250 | 1,781\.25 | 50,000 | 
| r6g\.16xlarge | 19,000 | 2,375 | 80,000 | 
| r6g\.metal | 19,000 | 2,375 | 80,000 | 
| r6gd\.medium \* | 4,750 | 593\.75 | 20,000 | 
| r6gd\.large \* | 4,750 | 593\.75 | 20,000 | 
| r6gd\.xlarge \* | 4,750 | 593\.75 | 20,000 | 
| r6gd\.2xlarge \* | 4,750 | 593\.75 | 20,000 | 
| r6gd\.4xlarge | 4,750 | 593\.75 | 20,000 | 
| r6gd\.8xlarge | 9,500 | 1,187\.5 | 40,000 | 
| r6gd\.12xlarge | 14,250 | 1,781\.25 | 50,000 | 
| r6gd\.16xlarge | 19,000 | 2,375 | 80,000 | 
| r6gd\.metal | 19,000 | 2,375 | 80,000 | 
| t3\.nano \* | 2,085 | 260\.57 | 11,800 | 
| t3\.micro \* | 2,085 | 260\.57 | 11,800 | 
| t3\.small \* | 2,085 | 260\.57 | 11,800 | 
| t3\.medium \* | 2,085 | 260\.57 | 11,800 | 
| t3\.large \* | 2,780 | 347\.5 | 15,700 | 
| t3\.xlarge \* | 2,780 | 347\.5 | 15,700 | 
| t3\.2xlarge \* | 2,780 | 347\.5 | 15,700 | 
| t3a\.nano \* | 2,085 | 260\.57 | 11,800 | 
| t3a\.micro \* | 2,085 | 260\.57 | 11,800 | 
| t3a\.small \* | 2,085 | 260\.57 | 11,800 | 
| t3a\.medium \* | 2,085 | 260\.57 | 11,800 | 
| t3a\.large \* | 2,780 | 347\.5 | 15,700 | 
| t3a\.xlarge \* | 2,780 | 347\.5 | 15,700 | 
| t3a\.2xlarge \* | 2,780 | 347\.5 | 15,700 | 
| t4g\.nano \* | 2,606 | 325\.75 | 11,800 | 
| t4g\.micro \* | 2,606 | 325\.75 | 11,800 | 
| t4g\.small \* | 2,606 | 325\.75 | 11,800 | 
| t4g\.medium \* | 2,606 | 325\.75 | 11,800 | 
| t4g\.large \* | 3,475 | 434\.37 | 15,700 | 
| t4g\.xlarge \* | 3,475 | 434\.37 | 15,700 | 
| t4g\.2xlarge \* | 3,475 | 434\.37 | 15,700 | 
| u\-6tb1\.metal | 38,000 | 4,750 | 160,000 | 
| u\-9tb1\.metal | 38,000 | 4,750 | 160,000 | 
| u\-12tb1\.metal | 38,000 | 4,750 | 160,000 | 
| u\-18tb1\.metal | 38,000 | 4,750 | 160,000 | 
| u\-24tb1\.metal | 38,000 | 4,750 | 160,000 | 
| x1\.16xlarge | 7,000 | 875 | 40,000 | 
| x1\.32xlarge | 14,000 | 1,750 | 80,000 | 
| x1e\.xlarge | 500 | 62\.5 | 3,700 | 
| x1e\.2xlarge | 1,000 | 125 | 7,400 | 
| x1e\.4xlarge | 1,750 | 218\.75 | 10,000 | 
| x1e\.8xlarge | 3,500 | 437\.5 | 20,000 | 
| x1e\.16xlarge | 7,000 | 875 | 40,000 | 
| x1e\.32xlarge | 14,000 | 1,750 | 80,000 | 
| z1d\.large \* | 3,170 | 396\.25 | 13,333 | 
| z1d\.xlarge \* | 3,170 | 396\.25 | 13,333 | 
| z1d\.2xlarge | 3,170 | 396\.25 | 13,333 | 
| z1d\.3xlarge | 4,750 | 593\.75 | 20,000 | 
| z1d\.6xlarge | 9,500 | 1,187\.5 | 40,000 | 
| z1d\.12xlarge | 19,000 | 2,375 | 80,000 | 
| z1d\.metal | 19,000 | 2,375 | 80,000 | 

\* These instance types can support maximum performance for 30 minutes at least once every 24 hours\. If you have a workload that requires sustained maximum performance for longer than 30 minutes, select an instance type according to baseline performance as shown in the following table\.


| Instance size | Baseline bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | 
| a1\.medium | 300 | 37\.5 | 2,500 | 
| a1\.large | 525 | 65\.625 | 4,000 | 
| a1\.xlarge | 800 | 100 | 6,000 | 
| a1\.2xlarge | 1,750 | 218\.75 | 10,000 | 
| c5\.large | 650 | 81\.25 | 4,000 | 
| c5\.xlarge | 1,150 | 143\.75 | 6,000 | 
| c5\.2xlarge | 2,300 | 287\.5 | 10,000 | 
| c5a\.large | 200 | 25 | 800 | 
| c5a\.xlarge | 400 | 50 | 1,600 | 
| c5a\.2xlarge | 800 | 100 | 3,200 | 
| c5a\.4xlarge | 1,580 | 198 | 6,600 | 
| c5ad\.large | 200 | 25 | 800 | 
| c5ad\.xlarge | 400 | 50 | 1,600 | 
| c5ad\.2xlarge | 800 | 100 | 3,200 | 
| c5ad\.4xlarge | 1,580 | 198 | 6,600 | 
| c5d\.large | 650 | 81\.25 | 4,000 | 
| c5d\.xlarge | 1,150 | 143\.75 | 6,000 | 
| c5d\.2xlarge | 2,300 | 287\.5 | 10,000 | 
| c5n\.large | 650 | 81\.25 | 4,000 | 
| c5n\.xlarge | 1,150 | 143\.75 | 6,000 | 
| c5n\.2xlarge | 2,300 | 287\.5 | 10,000 | 
| c6g\.medium | 315 | 39\.375 | 2,500 | 
| c6g\.large | 630 | 78\.75 | 3,600 | 
| c6g\.xlarge | 1,188 | 148\.5 | 6,000 | 
| c6g\.2xlarge | 2,375 | 296\.875 | 12,000 | 
| c6gd\.medium | 315 | 39\.375 | 2,500 | 
| c6gd\.large | 630 | 78\.75 | 3,600 | 
| c6gd\.xlarge | 1,188 | 148\.5 | 6,000 | 
| c6gd\.2xlarge | 2,375 | 296\.875 | 12,000 | 
| c6gn\.medium | 760 | 95 | 2,500 | 
| c6gn\.large | 1,235 | 154\.375 | 5,000 | 
| c6gn\.xlarge | 1,900 | 237\.5 | 10,000 | 
| c6gn\.2xlarge | 4,750 | 593\.75 | 20,000 | 
| d3\.xlarge | 850 | 106\.25 | 5,000 | 
| d3\.2xlarge | 1,700 | 212\.5 | 10,000 | 
| d3en\.large | 425 | 53\.125 | 2,500 | 
| d3en\.xlarge | 850 | 106\.25 | 5,000 | 
| d3en\.2xlarge | 1,700 | 212\.5 | 10,000 | 
| g4ad\.4xlarge | 1,580 | 197\.5 | 6,700 | 
| g4dn\.xlarge | 950 | 118\.75 | 3,000 | 
| g4dn\.2xlarge | 1,150 | 143\.75 | 6,000 | 
| i3en\.large | 577 | 72\.1 | 3,000 | 
| i3en\.xlarge | 1,154 | 144\.2 | 6,000 | 
| i3en\.2xlarge | 2,307 | 288\.39 | 12,000 | 
| i3en\.3xlarge | 3,800 | 475 | 15,000 | 
| inf1\.xlarge | 1,190 | 148\.75 | 4,000 | 
| inf1\.2xlarge | 1,190 | 148\.75 | 6,000 | 
| m5\.large | 650 | 81\.25 | 3,600 | 
| m5\.xlarge | 1,150 | 143\.75 | 6,000 | 
| m5\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| m5a\.large | 650 | 81\.25 | 3,600 | 
| m5a\.xlarge | 1,085 | 135\.63 | 6,000 | 
| m5a\.2xlarge | 1,580 | 197\.5 | 8,333 | 
| m5ad\.large | 650 | 81\.25 | 3,600 | 
| m5ad\.xlarge | 1,085 | 135\.63 | 6,000 | 
| m5ad\.2xlarge | 1,580 | 197\.5 | 8,333 | 
| m5d\.large | 650 | 81\.25 | 3,600 | 
| m5d\.xlarge | 1,150 | 143\.75 | 6,000 | 
| m5d\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| m5dn\.large | 650 | 81\.25 | 3,600 | 
| m5dn\.xlarge | 1,150 | 143\.75 | 6,000 | 
| m5dn\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| m5n\.large | 650 | 81\.25 | 3,600 | 
| m5n\.xlarge | 1,150 | 143\.75 | 6,000 | 
| m5n\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| m5zn\.large | 800 | 100 | 3,333 | 
| m5zn\.xlarge | 1,580 | 195\.5 | 6,667 | 
| m6g\.medium | 315 | 39\.375 | 2,500 | 
| m6g\.large | 630 | 78\.75 | 3,600 | 
| m6g\.xlarge | 1,188 | 148\.5 | 6,000 | 
| m6g\.2xlarge | 2,375 | 296\.875 | 12,000 | 
| m6gd\.medium | 315 | 39\.375 | 2,500 | 
| m6gd\.large | 630 | 78\.75 | 3,600 | 
| m6gd\.xlarge | 1,188 | 148\.5 | 6,000 | 
| m6gd\.2xlarge | 2,375 | 296\.875 | 12,000 | 
| r5\.large | 650 | 81\.25 | 3,600 | 
| r5\.xlarge | 1,150 | 143\.75 | 6,000 | 
| r5\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| r5a\.large | 650 | 81\.25 | 3,600 | 
| r5a\.xlarge | 1,085 | 135\.63 | 6,000 | 
| r5a\.2xlarge | 1,580 | 197\.5 | 8,333 | 
| r5ad\.large | 650 | 81\.25 | 3,600 | 
| r5ad\.xlarge | 1,085 | 135\.63 | 6,000 | 
| r5ad\.2xlarge | 1,580 | 197\.5 | 8,333 | 
| r5b\.large | 1,250 | 156\.25 | 5,417 | 
| r5b\.xlarge | 2,500 | 312\.5 | 10,833 | 
| r5b\.2xlarge | 5,000 | 625 | 21,667 | 
| r5d\.large | 650 | 81\.25 | 3,600 | 
| r5d\.xlarge | 1,150 | 143\.75 | 6,000 | 
| r5d\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| r5dn\.large | 650 | 81\.25 | 3,600 | 
| r5dn\.xlarge | 1,150 | 143\.75 | 6,000 | 
| r5dn\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| r5n\.large | 650 | 81\.25 | 3,600 | 
| r5n\.xlarge | 1,150 | 143\.75 | 6,000 | 
| r5n\.2xlarge | 2,300 | 287\.5 | 12,000 | 
| r6g\.medium | 315 | 39\.375 | 2,500 | 
| r6g\.large | 630 | 78\.75 | 3,600 | 
| r6g\.xlarge | 1,188 | 148\.5 | 6,000 | 
| r6g\.2xlarge | 2,375 | 296\.875 | 12,000 | 
| r6gd\.medium \* | 315 | 39\.375 | 2,500 | 
| r6gd\.large \* | 630 | 78\.75 | 3,600 | 
| r6gd\.xlarge \* | 1,188 | 148\.5 | 6,000 | 
| r6gd\.2xlarge \* | 2,375 | 296\.875 | 12,000 | 
| t3\.nano | 43 | 5\.43 | 250 | 
| t3\.micro | 87 | 10\.86 | 500 | 
| t3\.small | 174 | 21\.71 | 1,000 | 
| t3\.medium | 347 | 43\.43 | 2,000 | 
| t3\.large | 695 | 86\.86 | 4,000 | 
| t3\.xlarge | 695 | 86\.86 | 4,000 | 
| t3\.2xlarge | 695 | 86\.86 | 4,000 | 
| t3a\.nano | 45 | 5\.63 | 250 | 
| t3a\.micro | 90 | 11\.25 | 500 | 
| t3a\.small | 175 | 21\.88 | 1,000 | 
| t3a\.medium | 350 | 43\.75 | 2,000 | 
| t3a\.large | 695 | 86\.86 | 4,000 | 
| t3a\.xlarge | 695 | 86\.86 | 4,000 | 
| t3a\.2xlarge | 695 | 86\.86 | 4,000 | 
| t4g\.nano | 32 | 4 | 250 | 
| t4g\.micro | 64 | 8 | 500 | 
| t4g\.small | 128 | 16 | 1,000 | 
| t4g\.medium | 256 | 32 | 2,000 | 
| t4g\.large | 512 | 64 | 4,000 | 
| t4g\.xlarge | 1,024 | 128 | 4,000 | 
| t4g\.2xlarge | 2,048 | 256 | 4,000 | 
| z1d\.large | 800 | 100 | 3,333 | 
| z1d\.xlarge | 1,580 | 197\.5 | 6,667 | 

### EBS optimization supported<a name="previous"></a>

The following table lists the instance types that support EBS optimization but EBS optimization is not enabled by default\. You can enable EBS optimization when you launch these instances or after they are running\. Instances must have EBS optimization enabled to achieve the level of performance described\. When you enable EBS optimization for an instance that is not EBS\-optimized by default, you pay an additional low, hourly fee for the dedicated capacity\. For pricing information, see EBS\-Optimized Instances on the [Amazon EC2 Pricing, On\-Demand Pricing page](http://aws.amazon.com/ec2/pricing/on-demand/)\.


| Instance size | Maximum bandwidth \(Mbps\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
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

## Getting maximum performance<a name="ebs-optimization-performance"></a>

You can use the `EBSIOBalance%` and `EBSByteBalance%` metrics to help you determine whether your instances are sized correctly\. You can view these metrics in the CloudWatch console and set an alarm that is triggered based on a threshold you specify\. These metrics are expressed as a percentage\. Instances with a consistently low balance percentage are candidates to size up\. Instances where the balance percentage never drops below 100% are candidates for downsizing\. For more information, see [Monitoring your instances using CloudWatch](using-cloudwatch.md)\.

The high memory instances are designed to run large in\-memory databases, including production deployments of the SAP HANA in\-memory database, in the cloud\. To maximize EBS performance, use high memory instances with an even number of `io1` or `io2` volumes with identical provisioned performance\. For example, for IOPS heavy workloads, use four `io1` or `io2` volumes with 40,000 provisioned IOPS to get the maximum 160,000 instance IOPS\. Similarly, for throughput heavy workloads, use six `io1` or `io2` volumes with 48,000 provisioned IOPS to get the maximum 4,750 MB/s throughput\. For additional recommendations, see [Storage Configuration for SAP HANA](https://docs.aws.amazon.com/quickstart/latest/sap-hana/storage.html)\.

**Considerations**
+ G4dn, I3en, Inf1, M5a, M5ad, R5a, R5ad, T3, T3a, and Z1d instances launched after February 26, 2020 provide the maximum performance listed in the table above\. To get the maximum performance from an instance launched before February 26, 2020, stop and start it\.
+ C5, C5d, C5n, M5, M5d, M5n, M5dn, R5, R5d, R5n, R5dn, and P3dn instances launched after December 3, 2019 provide the maximum performance listed in the table above\. To get the maximum performance from an instance launched before December 3, 2019, stop and start it\.
+ `u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` instances launched after March 12, 2020 provide the performance in the table above\. Instances of these types launched before March 12, 2020 might provide lower performance\. To get the maximum performance from an instance launched before March 12, 2020, contact your account team to upgrade the instance at no additional cost\.

## Enabling EBS optimization at launch<a name="enable-ebs-optimization"></a>

You can enable optimization for an instance by setting its attribute for EBS optimization\.

**To enable Amazon EBS optimization when launching an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, select an AMI\.

1. In **Step 2: Choose an Instance Type**, select an instance type that is listed as supporting Amazon EBS optimization\.

1. In **Step 3: Configure Instance Details**, complete the fields that you need and choose **Launch as EBS\-optimized instance**\. If the instance type that you selected in the previous step doesn't support Amazon EBS optimization, this option is not present\. If the instance type that you selected is Amazon EBS–optimized by default, this option is selected and you can't deselect it\.

1. Follow the directions to complete the wizard and launch your instance\.

**To enable EBS optimization when launching an instance using the command line**

You can use one of the following commands with the corresponding option\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) with `--ebs-optimized` \(AWS CLI\)
+ [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) with `-EbsOptimized` \(AWS Tools for Windows PowerShell\)

## Enable EBS optimization for an existing instance<a name="modify-ebs-optimized-attribute"></a>

You can enable or disable optimization for an existing instance by modifying its Amazon EBS–optimized instance attribute\. If the instance is running, you must stop it first\.

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

**To enable EBS optimization for an existing instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. To stop the instance, choose **Actions**, **Instance state**, **Stop instance**\. It can take a few minutes for the instance to stop\.

1. With the instance still selected, choose **Actions**, **Instance settings**, **Change instance type**\.

1. For **Change Instance Type**, do one of the following:
   + If the instance type of your instance is Amazon EBS–optimized by default, **EBS\-optimized** is selected and you can't change it\. You can choose **Cancel**, because Amazon EBS optimization is already enabled for the instance\.
   + If the instance type of your instance supports Amazon EBS optimization, choose **EBS\-optimized** and then choose **Apply**\.
   + If the instance type of your instance does not support Amazon EBS optimization, you can't choose **EBS\-optimized**\. You can select an instance type from **Instance type** that supports Amazon EBS optimization, choose **EBS\-optimized**, and then choose **Apply**\.

1. Choose **Instance state**, **Start instance**\.

**To enable EBS optimization for an existing instance using the command line**

1. If the instance is running, use one of the following commands to stop it:
   + [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\)
   + [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

1. To enable EBS optimization, use one of the following commands with the corresponding option:
   + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) with `--ebs-optimized` \(AWS CLI\)
   + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) with `-EbsOptimized` \(AWS Tools for Windows PowerShell\)
# Amazon EBS–optimized instances<a name="ebs-optimized"></a>

An Amazon EBS–optimized instance uses an optimized configuration stack and provides additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

EBS–optimized instances deliver dedicated bandwidth to Amazon EBS\. When attached to an EBS–optimized instance, General Purpose SSD \(`gp2` and `gp3`\) volumes are designed to deliver at least 90% of their provisioned IOPS performance 99% of the time in a given year, and Provisioned IOPS SSD \(`io1` and `io2`\) volumes are designed to deliver at least 90% of their provisioned IOPS performance 99\.9% of the time in a given year\. Both Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) deliver at least 90% of their expected throughput performance 99% of the time in a given year\. Non\-compliant periods are approximately uniformly distributed, targeting 99% of expected total throughput each hour\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

**Topics**
+ [Supported instance types](#ebs-optimization-support)
+ [Get maximum performance](#ebs-optimization-performance)
+ [View instances types that support EBS optimization](#describe-ebs-optimization)
+ [Enable EBS optimization at launch](#enable-ebs-optimization)
+ [Enable EBS optimization for an existing instance](#modify-ebs-optimized-attribute)

## Supported instance types<a name="ebs-optimization-support"></a>

The following tables show which instance types support EBS optimization\. They include the dedicated bandwidth to Amazon EBS, the typical maximum aggregate throughput that can be achieved on that connection with a streaming read workload and 128 KiB I/O size, and the maximum IOPS the instance can support if you are using a 16 KiB I/O size\.

**Note**  
You can also view this information programatically using the AWS CLI\. For more information, see [View instances types that support EBS optimization](#describe-ebs-optimization)\.

Choose an EBS–optimized instance that provides more dedicated Amazon EBS throughput than your application needs; otherwise, the connection between Amazon EBS and Amazon EC2 can become a performance bottleneck\.

**Topics**
+ [EBS optimized by default](#current)
+ [EBS optimization supported](#previous)

### EBS optimized by default<a name="current"></a>

The following table lists the instance types that support EBS optimization and EBS optimization is enabled by default\. There is no need to enable EBS optimization and no effect if you disable EBS optimization\.

**Note**  
You can also view this information programatically using the AWS CLI\. For more information, see [View instances types that support EBS optimization](#describe-ebs-optimization)\.

**Important**  
**\*** These instances can support maximum performance for 30 minutes at least once every 24 hours, after which they revert to their baseline performance\.  
Other instances can sustain the maximum performance indefinitely\. If you have a workload that requires sustained maximum performance for longer than 30 minutes, select one of these instances\.

**Topics**
+ [General purpose](#current-general-purpose)
+ [Compute optimized](#current-compute-optimized)
+ [Memory optimized](#current-memory-optimized)
+ [Storage optimized](#current-storage-optimized)
+ [Accelerated computing](#current-accelerated-computing)

#### General purpose<a name="current-general-purpose"></a>


| Instance size | Baseline bandwidth \(Mbps\) | Maximum bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KiB I/O\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | --- | --- | --- | 
| a1\.medium \* | 300 | 3500 | 37\.5 | 437\.5 | 2500 | 20000 | 
| a1\.large \* | 525 | 3500 | 65\.625 | 437\.5 | 4000 | 20000 | 
| a1\.xlarge \* | 800 | 3500 | 100\.0 | 437\.5 | 6000 | 20000 | 
| a1\.2xlarge \* | 1750 | 3500 | 218\.75 | 437\.5 | 10000 | 20000 | 
| a1\.4xlarge | 3500 | 437\.5 | 20000 | 
| a1\.metal | 3500 | 437\.5 | 20000 | 
| m4\.large | 450 | 56\.25 | 3600 | 
| m4\.xlarge | 750 | 93\.75 | 6000 | 
| m4\.2xlarge | 1000 | 125\.0 | 8000 | 
| m4\.4xlarge | 2000 | 250\.0 | 16000 | 
| m4\.10xlarge | 4000 | 500\.0 | 32000 | 
| m4\.16xlarge | 10000 | 1250\.0 | 65000 | 
| m5\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| m5\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| m5\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| m5\.4xlarge | 4750 | 593\.75 | 18750 | 
| m5\.8xlarge | 6800 | 850\.0 | 30000 | 
| m5\.12xlarge | 9500 | 1187\.5 | 40000 | 
| m5\.16xlarge | 13600 | 1700\.0 | 60000 | 
| m5\.24xlarge | 19000 | 2375\.0 | 80000 | 
| m5\.metal | 19000 | 2375\.0 | 80000 | 
| m5a\.large \* | 650 | 2880 | 81\.25 | 360\.0 | 3600 | 16000 | 
| m5a\.xlarge \* | 1085 | 2880 | 135\.625 | 360\.0 | 6000 | 16000 | 
| m5a\.2xlarge \* | 1580 | 2880 | 197\.5 | 360\.0 | 8333 | 16000 | 
| m5a\.4xlarge | 2880 | 360\.0 | 16000 | 
| m5a\.8xlarge | 4750 | 593\.75 | 20000 | 
| m5a\.12xlarge | 6780 | 847\.5 | 30000 | 
| m5a\.16xlarge | 9500 | 1187\.5 | 40000 | 
| m5a\.24xlarge | 13750 | 1718\.75 | 60000 | 
| m5ad\.large \* | 650 | 2880 | 81\.25 | 360\.0 | 3600 | 16000 | 
| m5ad\.xlarge \* | 1085 | 2880 | 135\.625 | 360\.0 | 6000 | 16000 | 
| m5ad\.2xlarge \* | 1580 | 2880 | 197\.5 | 360\.0 | 8333 | 16000 | 
| m5ad\.4xlarge | 2880 | 360\.0 | 16000 | 
| m5ad\.8xlarge | 4750 | 593\.75 | 20000 | 
| m5ad\.12xlarge | 6780 | 847\.5 | 30000 | 
| m5ad\.16xlarge | 9500 | 1187\.5 | 40000 | 
| m5ad\.24xlarge | 13750 | 1718\.75 | 60000 | 
| m5d\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| m5d\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| m5d\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| m5d\.4xlarge | 4750 | 593\.75 | 18750 | 
| m5d\.8xlarge | 6800 | 850\.0 | 30000 | 
| m5d\.12xlarge | 9500 | 1187\.5 | 40000 | 
| m5d\.16xlarge | 13600 | 1700\.0 | 60000 | 
| m5d\.24xlarge | 19000 | 2375\.0 | 80000 | 
| m5d\.metal | 19000 | 2375\.0 | 80000 | 
| m5dn\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| m5dn\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| m5dn\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| m5dn\.4xlarge | 4750 | 593\.75 | 18750 | 
| m5dn\.8xlarge | 6800 | 850\.0 | 30000 | 
| m5dn\.12xlarge | 9500 | 1187\.5 | 40000 | 
| m5dn\.16xlarge | 13600 | 1700\.0 | 60000 | 
| m5dn\.24xlarge | 19000 | 2375\.0 | 80000 | 
| m5dn\.metal | 19000 | 2375\.0 | 80000 | 
| m5n\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| m5n\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| m5n\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| m5n\.4xlarge | 4750 | 593\.75 | 18750 | 
| m5n\.8xlarge | 6800 | 850\.0 | 30000 | 
| m5n\.12xlarge | 9500 | 1187\.5 | 40000 | 
| m5n\.16xlarge | 13600 | 1700\.0 | 60000 | 
| m5n\.24xlarge | 19000 | 2375\.0 | 80000 | 
| m5n\.metal | 19000 | 2375\.0 | 80000 | 
| m5zn\.large \* | 800 | 3170 | 100\.0 | 396\.25 | 3333 | 13333 | 
| m5zn\.xlarge \* | 1564 | 3170 | 195\.5 | 396\.25 | 6667 | 13333 | 
| m5zn\.2xlarge | 3170 | 396\.25 | 13333 | 
| m5zn\.3xlarge | 4750 | 593\.75 | 20000 | 
| m5zn\.6xlarge | 9500 | 1187\.5 | 40000 | 
| m5zn\.12xlarge | 19000 | 2375\.0 | 80000 | 
| m5zn\.metal | 19000 | 2375\.0 | 80000 | 
| m6a\.large \* | 531 | 6666 | 66\.375 | 833\.333333 | 3600 | 26667 | 
| m6a\.xlarge \* | 1061 | 6666 | 132\.625 | 833\.333333 | 6000 | 26667 | 
| m6a\.2xlarge \* | 2122 | 6666 | 265\.25 | 833\.333333 | 8333 | 26667 | 
| m6a\.4xlarge \* | 4245 | 6666 | 530\.625 | 833\.333333 | 16000 | 26667 | 
| m6a\.8xlarge | 6666 | 833\.333333 | 26667 | 
| m6a\.12xlarge | 10000 | 1250\.0 | 40000 | 
| m6a\.16xlarge | 13300 | 1662\.5 | 53333 | 
| m6a\.24xlarge | 20000 | 2500\.0 | 80000 | 
| m6a\.32xlarge | 26666 | 3333\.333333 | 100000 | 
| m6a\.48xlarge | 40000 | 5000\.0 | 160000 | 
| m6a\.metal | 40000 | 5000\.0 | 160000 | 
| m6g\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| m6g\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| m6g\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| m6g\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| m6g\.4xlarge | 4750 | 593\.75 | 20000 | 
| m6g\.8xlarge | 9500 | 1187\.5 | 40000 | 
| m6g\.12xlarge | 14250 | 1781\.25 | 50000 | 
| m6g\.16xlarge | 19000 | 2375\.0 | 80000 | 
| m6g\.metal | 19000 | 2375\.0 | 80000 | 
| m6gd\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| m6gd\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| m6gd\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| m6gd\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| m6gd\.4xlarge | 4750 | 593\.75 | 20000 | 
| m6gd\.8xlarge | 9500 | 1187\.5 | 40000 | 
| m6gd\.12xlarge | 14250 | 1781\.25 | 50000 | 
| m6gd\.16xlarge | 19000 | 2375\.0 | 80000 | 
| m6gd\.metal | 19000 | 2375\.0 | 80000 | 
| m6i\.large \* | 650 | 10000 | 81\.25 | 1250\.0 | 3600 | 40000 | 
| m6i\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 6000 | 40000 | 
| m6i\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| m6i\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| m6i\.8xlarge | 10000 | 1250\.0 | 40000 | 
| m6i\.12xlarge | 15000 | 1875\.0 | 60000 | 
| m6i\.16xlarge | 20000 | 2500\.0 | 80000 | 
| m6i\.24xlarge | 30000 | 3750\.0 | 120000 | 
| m6i\.32xlarge | 40000 | 5000\.0 | 160000 | 
| m6i\.metal | 40000 | 5000\.0 | 160000 | 
| m6id\.large \* | 650 | 10000 | 81\.25 | 1250\.0 | 3600 | 40000 | 
| m6id\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 6000 | 40000 | 
| m6id\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| m6id\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| m6id\.8xlarge | 10000 | 1250\.0 | 40000 | 
| m6id\.12xlarge | 15000 | 1875\.0 | 60000 | 
| m6id\.16xlarge | 20000 | 2500\.0 | 80000 | 
| m6id\.24xlarge | 30000 | 3750\.0 | 120000 | 
| m6id\.32xlarge | 40000 | 5000\.0 | 160000 | 
| m6id\.metal | 40000 | 5000\.0 | 160000 | 
| m6idn\.large \* | 1250 | 20000 | 156\.25 | 2500\.0 | 5468 | 87500 | 
| m6idn\.xlarge \* | 2500 | 20000 | 312\.5 | 2500\.0 | 10937 | 87500 | 
| m6idn\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 21875 | 87500 | 
| m6idn\.4xlarge \* | 10000 | 20000 | 1250\.0 | 2500\.0 | 43750 | 87500 | 
| m6idn\.8xlarge | 20000 | 2500\.0 | 87500 | 
| m6idn\.12xlarge | 30000 | 3750\.0 | 131250 | 
| m6idn\.16xlarge | 40000 | 5000\.0 | 175000 | 
| m6idn\.24xlarge | 60000 | 7500\.0 | 262500 | 
| m6idn\.32xlarge | 80000 | 10000\.0 | 350000 | 
| m6in\.large \* | 1250 | 20000 | 156\.25 | 2500\.0 | 5468 | 87500 | 
| m6in\.xlarge \* | 2500 | 20000 | 312\.5 | 2500\.0 | 10937 | 87500 | 
| m6in\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 21875 | 87500 | 
| m6in\.4xlarge \* | 10000 | 20000 | 1250\.0 | 2500\.0 | 43750 | 87500 | 
| m6in\.8xlarge | 20000 | 2500\.0 | 87500 | 
| m6in\.12xlarge | 30000 | 3750\.0 | 131250 | 
| m6in\.16xlarge | 40000 | 5000\.0 | 175000 | 
| m6in\.24xlarge | 60000 | 7500\.0 | 262500 | 
| m6in\.32xlarge | 80000 | 10000\.0 | 350000 | 
| mac1\.metal | 14000 | 1750\.0 | 80000 | 
| mac2\.metal | 10000 | 1250\.0 | 55000 | 
| t3\.nano \* | 43 | 2085 | 5\.375 | 260\.625 | 250 | 11800 | 
| t3\.micro \* | 87 | 2085 | 10\.875 | 260\.625 | 500 | 11800 | 
| t3\.small \* | 174 | 2085 | 21\.75 | 260\.625 | 1000 | 11800 | 
| t3\.medium \* | 347 | 2085 | 43\.375 | 260\.625 | 2000 | 11800 | 
| t3\.large \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t3\.xlarge \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t3\.2xlarge \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t3a\.nano \* | 45 | 2085 | 5\.625 | 260\.625 | 250 | 11800 | 
| t3a\.micro \* | 90 | 2085 | 11\.25 | 260\.625 | 500 | 11800 | 
| t3a\.small \* | 175 | 2085 | 21\.875 | 260\.625 | 1000 | 11800 | 
| t3a\.medium \* | 350 | 2085 | 43\.75 | 260\.625 | 2000 | 11800 | 
| t3a\.large \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t3a\.xlarge \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t3a\.2xlarge \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t4g\.nano \* | 43 | 2085 | 5\.375 | 260\.625 | 250 | 11800 | 
| t4g\.micro \* | 87 | 2085 | 10\.875 | 260\.625 | 500 | 11800 | 
| t4g\.small \* | 174 | 2085 | 21\.75 | 260\.625 | 1000 | 11800 | 
| t4g\.medium \* | 347 | 2085 | 43\.375 | 260\.625 | 2000 | 11800 | 
| t4g\.large \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t4g\.xlarge \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 
| t4g\.2xlarge \* | 695 | 2780 | 86\.875 | 347\.5 | 4000 | 15700 | 

#### Compute optimized<a name="current-compute-optimized"></a>


| Instance size | Baseline bandwidth \(Mbps\) | Maximum bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KiB I/O\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | --- | --- | --- | 
| c4\.large | 500 | 62\.5 | 4000 | 
| c4\.xlarge | 750 | 93\.75 | 6000 | 
| c4\.2xlarge | 1000 | 125\.0 | 8000 | 
| c4\.4xlarge | 2000 | 250\.0 | 16000 | 
| c4\.8xlarge | 4000 | 500\.0 | 32000 | 
| c5\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 4000 | 20000 | 
| c5\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 20000 | 
| c5\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 10000 | 20000 | 
| c5\.4xlarge | 4750 | 593\.75 | 20000 | 
| c5\.9xlarge | 9500 | 1187\.5 | 40000 | 
| c5\.12xlarge | 9500 | 1187\.5 | 40000 | 
| c5\.18xlarge | 19000 | 2375\.0 | 80000 | 
| c5\.24xlarge | 19000 | 2375\.0 | 80000 | 
| c5\.metal | 19000 | 2375\.0 | 80000 | 
| c5a\.large \* | 200 | 3170 | 25\.0 | 396\.25 | 800 | 13300 | 
| c5a\.xlarge \* | 400 | 3170 | 50\.0 | 396\.25 | 1600 | 13300 | 
| c5a\.2xlarge \* | 800 | 3170 | 100\.0 | 396\.25 | 3200 | 13300 | 
| c5a\.4xlarge \* | 1580 | 3170 | 197\.5 | 396\.25 | 6600 | 13300 | 
| c5a\.8xlarge | 3170 | 396\.25 | 13300 | 
| c5a\.12xlarge | 4750 | 593\.75 | 20000 | 
| c5a\.16xlarge | 6300 | 787\.5 | 26700 | 
| c5a\.24xlarge | 9500 | 1187\.5 | 40000 | 
| c5ad\.large \* | 200 | 3170 | 25\.0 | 396\.25 | 800 | 13300 | 
| c5ad\.xlarge \* | 400 | 3170 | 50\.0 | 396\.25 | 1600 | 13300 | 
| c5ad\.2xlarge \* | 800 | 3170 | 100\.0 | 396\.25 | 3200 | 13300 | 
| c5ad\.4xlarge \* | 1580 | 3170 | 197\.5 | 396\.25 | 6600 | 13300 | 
| c5ad\.8xlarge | 3170 | 396\.25 | 13300 | 
| c5ad\.12xlarge | 4750 | 593\.75 | 20000 | 
| c5ad\.16xlarge | 6300 | 787\.5 | 26700 | 
| c5ad\.24xlarge | 9500 | 1187\.5 | 40000 | 
| c5d\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 4000 | 20000 | 
| c5d\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 20000 | 
| c5d\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 10000 | 20000 | 
| c5d\.4xlarge | 4750 | 593\.75 | 20000 | 
| c5d\.9xlarge | 9500 | 1187\.5 | 40000 | 
| c5d\.12xlarge | 9500 | 1187\.5 | 40000 | 
| c5d\.18xlarge | 19000 | 2375\.0 | 80000 | 
| c5d\.24xlarge | 19000 | 2375\.0 | 80000 | 
| c5d\.metal | 19000 | 2375\.0 | 80000 | 
| c5n\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 4000 | 20000 | 
| c5n\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 20000 | 
| c5n\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 10000 | 20000 | 
| c5n\.4xlarge | 4750 | 593\.75 | 20000 | 
| c5n\.9xlarge | 9500 | 1187\.5 | 40000 | 
| c5n\.18xlarge | 19000 | 2375\.0 | 80000 | 
| c5n\.metal | 19000 | 2375\.0 | 80000 | 
| c6a\.large \* | 531 | 6666 | 66\.375 | 833\.333333 | 3600 | 26667 | 
| c6a\.xlarge \* | 1061 | 6666 | 132\.625 | 833\.333333 | 6000 | 26667 | 
| c6a\.2xlarge \* | 2122 | 6666 | 265\.25 | 833\.333333 | 8333 | 26667 | 
| c6a\.4xlarge \* | 4245 | 6666 | 530\.625 | 833\.333333 | 16000 | 26667 | 
| c6a\.8xlarge | 6666 | 833\.333333 | 26667 | 
| c6a\.12xlarge | 10000 | 1250\.0 | 40000 | 
| c6a\.16xlarge | 13300 | 1662\.5 | 53333 | 
| c6a\.24xlarge | 20000 | 2500\.0 | 80000 | 
| c6a\.32xlarge | 26666 | 3333\.333333 | 100000 | 
| c6a\.48xlarge | 40000 | 5000\.0 | 160000 | 
| c6a\.metal | 40000 | 5000\.0 | 160000 | 
| c6g\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| c6g\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| c6g\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| c6g\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| c6g\.4xlarge | 4750 | 593\.75 | 20000 | 
| c6g\.8xlarge | 9500 | 1187\.5 | 40000 | 
| c6g\.12xlarge | 14250 | 1781\.25 | 50000 | 
| c6g\.16xlarge | 19000 | 2375\.0 | 80000 | 
| c6g\.metal | 19000 | 2375\.0 | 80000 | 
| c6gd\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| c6gd\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| c6gd\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| c6gd\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| c6gd\.4xlarge | 4750 | 593\.75 | 20000 | 
| c6gd\.8xlarge | 9500 | 1187\.5 | 40000 | 
| c6gd\.12xlarge | 14250 | 1781\.25 | 50000 | 
| c6gd\.16xlarge | 19000 | 2375\.0 | 80000 | 
| c6gd\.metal | 19000 | 2375\.0 | 80000 | 
| c6gn\.medium \* | 760 | 9500 | 95\.0 | 1187\.5 | 2500 | 40000 | 
| c6gn\.large \* | 1235 | 9500 | 154\.375 | 1187\.5 | 5000 | 40000 | 
| c6gn\.xlarge \* | 2375 | 9500 | 296\.88 | 1187\.5 | 10000 | 40000 | 
| c6gn\.2xlarge \* | 4750 | 9500 | 593\.75 | 1187\.5 | 20000 | 40000 | 
| c6gn\.4xlarge | 9500 | 1187\.5 | 40000 | 
| c6gn\.8xlarge | 19000 | 2375\.0 | 80000 | 
| c6gn\.12xlarge | 28500 | 3562\.5 | 120000 | 
| c6gn\.16xlarge | 38000 | 4750\.0 | 160000 | 
| c6i\.large \* | 650 | 10000 | 81\.25 | 1250\.0 | 3600 | 40000 | 
| c6i\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 6000 | 40000 | 
| c6i\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| c6i\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| c6i\.8xlarge | 10000 | 1250\.0 | 40000 | 
| c6i\.12xlarge | 15000 | 1875\.0 | 60000 | 
| c6i\.16xlarge | 20000 | 2500\.0 | 80000 | 
| c6i\.24xlarge | 30000 | 3750\.0 | 120000 | 
| c6i\.32xlarge | 40000 | 5000\.0 | 160000 | 
| c6i\.metal | 40000 | 5000\.0 | 160000 | 
| c6id\.large \* | 650 | 10000 | 81\.25 | 1250\.0 | 3600 | 40000 | 
| c6id\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 6000 | 40000 | 
| c6id\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| c6id\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| c6id\.8xlarge | 10000 | 1250\.0 | 40000 | 
| c6id\.12xlarge | 15000 | 1875\.0 | 60000 | 
| c6id\.16xlarge | 20000 | 2500\.0 | 80000 | 
| c6id\.24xlarge | 30000 | 3750\.0 | 120000 | 
| c6id\.32xlarge | 40000 | 5000\.0 | 160000 | 
| c6id\.metal | 40000 | 5000\.0 | 160000 | 
| c6in\.large \* | 1250 | 20000 | 156\.25 | 2500\.0 | 5468 | 87500 | 
| c6in\.xlarge \* | 2500 | 20000 | 312\.5 | 2500\.0 | 10937 | 87500 | 
| c6in\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 21875 | 87500 | 
| c6in\.4xlarge \* | 10000 | 20000 | 1250\.0 | 2500\.0 | 43750 | 87500 | 
| c6in\.8xlarge | 20000 | 2500\.0 | 87500 | 
| c6in\.12xlarge | 30000 | 3750\.0 | 131250 | 
| c6in\.16xlarge | 40000 | 5000\.0 | 175000 | 
| c6in\.24xlarge | 60000 | 7500\.0 | 262500 | 
| c6in\.32xlarge | 80000 | 10000\.0 | 350000 | 
| c7g\.medium \* | 315 | 10000 | 39\.375 | 1250\.0 | 2500 | 40000 | 
| c7g\.large \* | 630 | 10000 | 78\.75 | 1250\.0 | 3600 | 40000 | 
| c7g\.xlarge \* | 1250 | 10000 | 156\.25039999999998 | 1250\.0 | 6000 | 40000 | 
| c7g\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| c7g\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| c7g\.8xlarge | 10000 | 1250\.0 | 40000 | 
| c7g\.12xlarge | 15000 | 1875\.0 | 60000 | 
| c7g\.16xlarge | 20000 | 2500\.0 | 80000 | 
| hpc6a\.48xlarge \* | 87 | 2085 | 10\.875 | 260\.625 | 500 | 11000 | 

#### Memory optimized<a name="current-memory-optimized"></a>


| Instance size | Baseline bandwidth \(Mbps\) | Maximum bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KiB I/O\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | --- | --- | --- | 
| hpc6id\.32xlarge \* | 87 | 2085 | 10\.875 | 260\.625 | 500 | 11000 | 
| r4\.large | 425 | 53\.125 | 3000 | 
| r4\.xlarge | 850 | 106\.25 | 6000 | 
| r4\.2xlarge | 1700 | 212\.5 | 12000 | 
| r4\.4xlarge | 3500 | 437\.5 | 18750 | 
| r4\.8xlarge | 7000 | 875\.0 | 37500 | 
| r4\.16xlarge | 14000 | 1750\.0 | 75000 | 
| r5\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| r5\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| r5\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| r5\.4xlarge | 4750 | 593\.75 | 18750 | 
| r5\.8xlarge | 6800 | 850\.0 | 30000 | 
| r5\.12xlarge | 9500 | 1187\.5 | 40000 | 
| r5\.16xlarge | 13600 | 1700\.0 | 60000 | 
| r5\.24xlarge | 19000 | 2375\.0 | 80000 | 
| r5\.metal | 19000 | 2375\.0 | 80000 | 
| r5a\.large \* | 650 | 2880 | 81\.25 | 360\.0 | 3600 | 16000 | 
| r5a\.xlarge \* | 1085 | 2880 | 135\.625 | 360\.0 | 6000 | 16000 | 
| r5a\.2xlarge \* | 1580 | 2880 | 197\.5 | 360\.0 | 8333 | 16000 | 
| r5a\.4xlarge | 2880 | 360\.0 | 16000 | 
| r5a\.8xlarge | 4750 | 593\.75 | 20000 | 
| r5a\.12xlarge | 6780 | 847\.5 | 30000 | 
| r5a\.16xlarge | 9500 | 1187\.5 | 40000 | 
| r5a\.24xlarge | 13570 | 1696\.25 | 60000 | 
| r5ad\.large \* | 650 | 2880 | 81\.25 | 360\.0 | 3600 | 16000 | 
| r5ad\.xlarge \* | 1085 | 2880 | 135\.625 | 360\.0 | 6000 | 16000 | 
| r5ad\.2xlarge \* | 1580 | 2880 | 197\.5 | 360\.0 | 8333 | 16000 | 
| r5ad\.4xlarge | 2880 | 360\.0 | 16000 | 
| r5ad\.8xlarge | 4750 | 593\.75 | 20000 | 
| r5ad\.12xlarge | 6780 | 847\.5 | 30000 | 
| r5ad\.16xlarge | 9500 | 1187\.5 | 40000 | 
| r5ad\.24xlarge | 13570 | 1696\.25 | 60000 | 
| r5b\.large \* | 1250 | 10000 | 156\.25 | 1250\.0 | 5417 | 43333 | 
| r5b\.xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 10833 | 43333 | 
| r5b\.2xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 21667 | 43333 | 
| r5b\.4xlarge | 10000 | 1250\.0 | 43333 | 
| r5b\.8xlarge | 20000 | 2500\.0 | 86667 | 
| r5b\.12xlarge | 30000 | 3750\.0 | 130000 | 
| r5b\.16xlarge | 40000 | 5000\.0 | 173333 | 
| r5b\.24xlarge | 60000 | 7500\.0 | 260000 | 
| r5b\.metal | 60000 | 7500\.0 | 260000 | 
| r5d\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| r5d\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| r5d\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| r5d\.4xlarge | 4750 | 593\.75 | 18750 | 
| r5d\.8xlarge | 6800 | 850\.0 | 30000 | 
| r5d\.12xlarge | 9500 | 1187\.5 | 40000 | 
| r5d\.16xlarge | 13600 | 1700\.0 | 60000 | 
| r5d\.24xlarge | 19000 | 2375\.0 | 80000 | 
| r5d\.metal | 19000 | 2375\.0 | 80000 | 
| r5dn\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| r5dn\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| r5dn\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| r5dn\.4xlarge | 4750 | 593\.75 | 18750 | 
| r5dn\.8xlarge | 6800 | 850\.0 | 30000 | 
| r5dn\.12xlarge | 9500 | 1187\.5 | 40000 | 
| r5dn\.16xlarge | 13600 | 1700\.0 | 60000 | 
| r5dn\.24xlarge | 19000 | 2375\.0 | 80000 | 
| r5dn\.metal | 19000 | 2375\.0 | 80000 | 
| r5n\.large \* | 650 | 4750 | 81\.25 | 593\.75 | 3600 | 18750 | 
| r5n\.xlarge \* | 1150 | 4750 | 143\.75 | 593\.75 | 6000 | 18750 | 
| r5n\.2xlarge \* | 2300 | 4750 | 287\.5 | 593\.75 | 12000 | 18750 | 
| r5n\.4xlarge | 4750 | 593\.75 | 18750 | 
| r5n\.8xlarge | 6800 | 850\.0 | 30000 | 
| r5n\.12xlarge | 9500 | 1187\.5 | 40000 | 
| r5n\.16xlarge | 13600 | 1700\.0 | 60000 | 
| r5n\.24xlarge | 19000 | 2375\.0 | 80000 | 
| r5n\.metal | 19000 | 2375\.0 | 80000 | 
| r6a\.large \* | 531 | 6666 | 66\.375 | 833\.333333 | 3600 | 26667 | 
| r6a\.xlarge \* | 1061 | 6666 | 132\.625 | 833\.333333 | 6000 | 26667 | 
| r6a\.2xlarge \* | 2122 | 6666 | 265\.25 | 833\.333333 | 8333 | 26667 | 
| r6a\.4xlarge \* | 4245 | 6666 | 530\.625 | 833\.333333 | 16000 | 26667 | 
| r6a\.8xlarge | 6666 | 833\.333333 | 26667 | 
| r6a\.12xlarge | 10000 | 1250\.0 | 40000 | 
| r6a\.16xlarge | 13300 | 1662\.5 | 53333 | 
| r6a\.24xlarge | 20000 | 2500\.0 | 80000 | 
| r6a\.32xlarge | 26666 | 3333\.333333 | 100000 | 
| r6a\.48xlarge | 40000 | 5000\.0 | 160000 | 
| r6a\.metal | 40000 | 5000\.0 | 160000 | 
| r6g\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| r6g\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| r6g\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| r6g\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| r6g\.4xlarge | 4750 | 593\.75 | 20000 | 
| r6g\.8xlarge | 9500 | 1187\.5 | 40000 | 
| r6g\.12xlarge | 14250 | 1781\.25 | 50000 | 
| r6g\.16xlarge | 19000 | 2375\.0 | 80000 | 
| r6g\.metal | 19000 | 2375\.0 | 80000 | 
| r6gd\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| r6gd\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| r6gd\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| r6gd\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| r6gd\.4xlarge | 4750 | 593\.75 | 20000 | 
| r6gd\.8xlarge | 9500 | 1187\.5 | 40000 | 
| r6gd\.12xlarge | 14250 | 1781\.25 | 50000 | 
| r6gd\.16xlarge | 19000 | 2375\.0 | 80000 | 
| r6gd\.metal | 19000 | 2375\.0 | 80000 | 
| r6i\.large \* | 650 | 10000 | 81\.25 | 1250\.0 | 3600 | 40000 | 
| r6i\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 6000 | 40000 | 
| r6i\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| r6i\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| r6i\.8xlarge | 10000 | 1250\.0 | 40000 | 
| r6i\.12xlarge | 15000 | 1875\.0 | 60000 | 
| r6i\.16xlarge | 20000 | 2500\.0 | 80000 | 
| r6i\.24xlarge | 30000 | 3750\.0 | 120000 | 
| r6i\.32xlarge | 40000 | 5000\.0 | 160000 | 
| r6i\.metal | 40000 | 5000\.0 | 160000 | 
| r6idn\.large \* | 1250 | 20000 | 156\.25 | 2500\.0 | 5468 | 87500 | 
| r6idn\.xlarge \* | 2500 | 20000 | 312\.5 | 2500\.0 | 10937 | 87500 | 
| r6idn\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 21875 | 87500 | 
| r6idn\.4xlarge \* | 10000 | 20000 | 1250\.0 | 2500\.0 | 43750 | 87500 | 
| r6idn\.8xlarge | 20000 | 2500\.0 | 87500 | 
| r6idn\.12xlarge | 30000 | 3750\.0 | 131250 | 
| r6idn\.16xlarge | 40000 | 5000\.0 | 175000 | 
| r6idn\.24xlarge | 60000 | 7500\.0 | 262500 | 
| r6idn\.32xlarge | 80000 | 10000\.0 | 350000 | 
| r6in\.large \* | 1250 | 20000 | 156\.25 | 2500\.0 | 5468 | 87500 | 
| r6in\.xlarge \* | 2500 | 20000 | 312\.5 | 2500\.0 | 10937 | 87500 | 
| r6in\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 21875 | 87500 | 
| r6in\.4xlarge \* | 10000 | 20000 | 1250\.0 | 2500\.0 | 43750 | 87500 | 
| r6in\.8xlarge | 20000 | 2500\.0 | 87500 | 
| r6in\.12xlarge | 30000 | 3750\.0 | 131250 | 
| r6in\.16xlarge | 40000 | 5000\.0 | 175000 | 
| r6in\.24xlarge | 60000 | 7500\.0 | 262500 | 
| r6in\.32xlarge | 80000 | 10000\.0 | 350000 | 
| r6id\.large \* | 650 | 10000 | 81\.25 | 1250\.0 | 3600 | 40000 | 
| r6id\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 6000 | 40000 | 
| r6id\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 12000 | 40000 | 
| r6id\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| r6id\.8xlarge | 10000 | 1250\.0 | 40000 | 
| r6id\.12xlarge | 15000 | 1875\.0 | 60000 | 
| r6id\.16xlarge | 20000 | 2500\.0 | 80000 | 
| r6id\.24xlarge | 30000 | 3750\.0 | 120000 | 
| r6id\.32xlarge | 40000 | 5000\.0 | 160000 | 
| r6id\.metal | 40000 | 5000\.0 | 160000 | 
| u\-3tb1\.56xlarge | 19000 | 2375\.0 | 80000 | 
| u\-6tb1\.56xlarge | 38000 | 4750\.0 | 160000 | 
| u\-6tb1\.112xlarge | 38000 | 4750\.0 | 160000 | 
| u\-6tb1\.metal | 38000 | 4750\.0 | 160000 | 
| u\-9tb1\.112xlarge | 38000 | 4750\.0 | 160000 | 
| u\-9tb1\.metal | 38000 | 4750\.0 | 160000 | 
| u\-12tb1\.112xlarge | 38000 | 4750\.0 | 160000 | 
| u\-12tb1\.metal | 38000 | 4750\.0 | 160000 | 
| u\-18tb1\.metal | 38000 | 4750\.0 | 160000 | 
| u\-24tb1\.metal | 38000 | 4750\.0 | 160000 | 
| x1\.16xlarge | 7000 | 875\.0 | 40000 | 
| x1\.32xlarge | 14000 | 1750\.0 | 80000 | 
| x2gd\.medium \* | 315 | 4750 | 39\.375 | 593\.75 | 2500 | 20000 | 
| x2gd\.large \* | 630 | 4750 | 78\.75 | 593\.75 | 3600 | 20000 | 
| x2gd\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| x2gd\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| x2gd\.4xlarge | 4750 | 593\.75 | 20000 | 
| x2gd\.8xlarge | 9500 | 1187\.5 | 40000 | 
| x2gd\.12xlarge | 14250 | 1781\.25 | 60000 | 
| x2gd\.16xlarge | 19000 | 2375\.0 | 80000 | 
| x2gd\.metal | 19000 | 2375\.0 | 80000 | 
| x2idn\.16xlarge | 40000 | 5000\.0 | 173333 | 
| x2idn\.24xlarge | 60000 | 7500\.0 | 260000 | 
| x2idn\.32xlarge | 80000 | 10000\.0 | 260000 | 
| x2idn\.metal | 80000 | 10000\.0 | 260000 | 
| x2iedn\.xlarge \* | 2500 | 20000 | 312\.5 | 2500\.0 | 8125 | 65000 | 
| x2iedn\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 16250 | 65000 | 
| x2iedn\.4xlarge \* | 10000 | 20000 | 1250\.0 | 2500\.0 | 32500 | 65000 | 
| x2iedn\.8xlarge | 20000 | 2500\.0 | 65000 | 
| x2iedn\.16xlarge | 40000 | 5000\.0 | 130000 | 
| x2iedn\.24xlarge | 60000 | 7500\.0 | 195000 | 
| x2iedn\.32xlarge | 80000 | 10000\.0 | 260000 | 
| x2iedn\.metal | 80000 | 10000\.0 | 260000 | 
| x2iezn\.2xlarge | 3170 | 396\.25 | 13333 | 
| x2iezn\.4xlarge | 4750 | 593\.75 | 20000 | 
| x2iezn\.6xlarge | 9500 | 1187\.5 | 40000 | 
| x2iezn\.8xlarge | 12000 | 1500\.0 | 55000 | 
| x2iezn\.12xlarge | 19000 | 2375\.0 | 80000 | 
| x2iezn\.metal | 19000 | 2375\.0 | 80000 | 
| x1e\.xlarge | 500 | 62\.5 | 3700 | 
| x1e\.2xlarge | 1000 | 125\.0 | 7400 | 
| x1e\.4xlarge | 1750 | 218\.75 | 10000 | 
| x1e\.8xlarge | 3500 | 437\.5 | 20000 | 
| x1e\.16xlarge | 7000 | 875\.0 | 40000 | 
| x1e\.32xlarge | 14000 | 1750\.0 | 80000 | 
| z1d\.large \* | 800 | 3170 | 100\.0 | 396\.25 | 3333 | 13333 | 
| z1d\.xlarge \* | 1580 | 3170 | 197\.5 | 396\.25 | 6667 | 13333 | 
| z1d\.2xlarge | 3170 | 396\.25 | 13333 | 
| z1d\.3xlarge | 4750 | 593\.75 | 20000 | 
| z1d\.6xlarge | 9500 | 1187\.5 | 40000 | 
| z1d\.12xlarge | 19000 | 2375\.0 | 80000 | 
| z1d\.metal | 19000 | 2375\.0 | 80000 | 

#### Storage optimized<a name="current-storage-optimized"></a>


| Instance size | Baseline bandwidth \(Mbps\) | Maximum bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KiB I/O\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | --- | --- | --- | 
| d2\.xlarge | 750 | 93\.75 | 6000 | 
| d2\.2xlarge | 1000 | 125\.0 | 8000 | 
| d2\.4xlarge | 2000 | 250\.0 | 16000 | 
| d2\.8xlarge | 4000 | 500\.0 | 32000 | 
| d3\.xlarge \* | 850 | 2800 | 106\.25 | 350\.0 | 5000 | 15000 | 
| d3\.2xlarge \* | 1700 | 2800 | 212\.5 | 350\.0 | 10000 | 15000 | 
| d3\.4xlarge | 2800 | 350\.0 | 15000 | 
| d3\.8xlarge | 5000 | 625\.0 | 30000 | 
| d3en\.xlarge \* | 850 | 2800 | 106\.25 | 350\.0 | 5000 | 15000 | 
| d3en\.2xlarge \* | 1700 | 2800 | 212\.5 | 350\.0 | 10000 | 15000 | 
| d3en\.4xlarge | 2800 | 350\.0 | 15000 | 
| d3en\.6xlarge | 4000 | 500\.0 | 25000 | 
| d3en\.8xlarge | 5000 | 625\.0 | 30000 | 
| d3en\.12xlarge | 7000 | 875\.0 | 40000 | 
| h1\.2xlarge | 1750 | 218\.75 | 12000 | 
| h1\.4xlarge | 3500 | 437\.5 | 20000 | 
| h1\.8xlarge | 7000 | 875\.0 | 40000 | 
| h1\.16xlarge | 14000 | 1750\.0 | 80000 | 
| i3\.large | 425 | 53\.125 | 3000 | 
| i3\.xlarge | 850 | 106\.25 | 6000 | 
| i3\.2xlarge | 1700 | 212\.5 | 12000 | 
| i3\.4xlarge | 3500 | 437\.5 | 16000 | 
| i3\.8xlarge | 7000 | 875\.0 | 32500 | 
| i3\.16xlarge | 14000 | 1750\.0 | 65000 | 
| i3\.metal | 19000 | 2375\.0 | 80000 | 
| i3en\.large \* | 576 | 4750 | 72\.1 | 593\.75 | 3000 | 20000 | 
| i3en\.xlarge \* | 1153 | 4750 | 144\.2 | 593\.75 | 6000 | 20000 | 
| i3en\.2xlarge \* | 2307 | 4750 | 288\.3875 | 593\.75 | 12000 | 20000 | 
| i3en\.3xlarge \* | 3800 | 4750 | 475\.0 | 593\.75 | 15000 | 20000 | 
| i3en\.6xlarge | 4750 | 593\.75 | 20000 | 
| i3en\.12xlarge | 9500 | 1187\.5 | 40000 | 
| i3en\.24xlarge | 19000 | 2375\.0 | 80000 | 
| i3en\.metal | 19000 | 2375\.0 | 80000 | 
| i4i\.large \* | 625 | 10000 | 78\.125 | 1250\.0 | 2500 | 40000 | 
| i4i\.xlarge \* | 1250 | 10000 | 156\.25 | 1250\.0 | 5000 | 40000 | 
| i4i\.2xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 10000 | 40000 | 
| i4i\.4xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| i4i\.8xlarge | 10000 | 1250\.0 | 40000 | 
| i4i\.16xlarge | 20000 | 2500\.0 | 80000 | 
| i4i\.32xlarge | 40000 | 5000\.0 | 160000 | 
| i4i\.metal | 40000 | 5000\.0 | 160000 | 
| im4gn\.large \* | 1250 | 10000 | 156\.25 | 1250\.0 | 5000 | 40000 | 
| im4gn\.xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 10000 | 40000 | 
| im4gn\.2xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| im4gn\.4xlarge | 10000 | 1250\.0 | 40000 | 
| im4gn\.8xlarge | 20000 | 2500\.0 | 80000 | 
| im4gn\.16xlarge | 40000 | 5000\.0 | 160000 | 
| is4gen\.medium \* | 625 | 10000 | 78\.125 | 1250\.0 | 2500 | 40000 | 
| is4gen\.large \* | 1250 | 10000 | 156\.25 | 1250\.0 | 5000 | 40000 | 
| is4gen\.xlarge \* | 2500 | 10000 | 312\.5 | 1250\.0 | 10000 | 40000 | 
| is4gen\.2xlarge \* | 5000 | 10000 | 625\.0 | 1250\.0 | 20000 | 40000 | 
| is4gen\.4xlarge | 10000 | 1250\.0 | 40000 | 
| is4gen\.8xlarge | 20000 | 2500\.0 | 80000 | 

#### Accelerated computing<a name="current-accelerated-computing"></a>


| Instance size | Baseline bandwidth \(Mbps\) | Maximum bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KiB I/O\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Baseline IOPS \(16 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | --- | --- | --- | 
| dl1\.24xlarge | 19000 | 2375\.0 | 80000 | 
| f1\.2xlarge | 1700 | 212\.5 | 12000 | 
| f1\.4xlarge | 3500 | 437\.5 | 44000 | 
| f1\.16xlarge | 14000 | 1750\.0 | 75000 | 
| g3\.4xlarge | 3500 | 437\.5 | 20000 | 
| g3\.8xlarge | 7000 | 875\.0 | 40000 | 
| g3\.16xlarge | 14000 | 1750\.0 | 80000 | 
| g4ad\.xlarge \* | 400 | 3170 | 50\.0 | 396\.25 | 1700 | 13333 | 
| g4ad\.2xlarge \* | 800 | 3170 | 100\.0 | 396\.25 | 3400 | 13333 | 
| g4ad\.4xlarge \* | 1580 | 3170 | 197\.5 | 396\.25 | 6700 | 13333 | 
| g4ad\.8xlarge | 3170 | 396\.25 | 13333 | 
| g4ad\.16xlarge | 6300 | 787\.5 | 26667 | 
| g4dn\.xlarge \* | 950 | 3500 | 118\.75 | 437\.5 | 3000 | 20000 | 
| g4dn\.2xlarge \* | 1150 | 3500 | 143\.75 | 437\.5 | 6000 | 20000 | 
| g4dn\.4xlarge | 4750 | 593\.75 | 20000 | 
| g4dn\.8xlarge | 9500 | 1187\.5 | 40000 | 
| g4dn\.12xlarge | 9500 | 1187\.5 | 40000 | 
| g4dn\.16xlarge | 9500 | 1187\.5 | 40000 | 
| g4dn\.metal | 19000 | 2375\.0 | 80000 | 
| g5\.xlarge \* | 700 | 3500 | 87\.5 | 437\.5 | 3000 | 15000 | 
| g5\.2xlarge \* | 850 | 3500 | 106\.25 | 437\.5 | 3500 | 15000 | 
| g5\.4xlarge | 4750 | 593\.75 | 20000 | 
| g5\.8xlarge | 16000 | 2000\.0 | 65000 | 
| g5\.12xlarge | 16000 | 2000\.0 | 65000 | 
| g5\.16xlarge | 16000 | 2000\.0 | 65000 | 
| g5\.24xlarge | 19000 | 2375\.0 | 80000 | 
| g5\.48xlarge | 19000 | 2375\.0 | 80000 | 
| g5g\.xlarge \* | 1188 | 4750 | 148\.5 | 593\.75 | 6000 | 20000 | 
| g5g\.2xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 12000 | 20000 | 
| g5g\.4xlarge | 4750 | 593\.75 | 20000 | 
| g5g\.8xlarge | 9500 | 1187\.5 | 40000 | 
| g5g\.16xlarge | 19000 | 2375\.0 | 80000 | 
| g5g\.metal | 19000 | 2375\.0 | 80000 | 
| inf1\.xlarge \* | 1190 | 4750 | 148\.75 | 593\.75 | 4000 | 20000 | 
| inf1\.2xlarge \* | 1190 | 4750 | 148\.75 | 593\.75 | 6000 | 20000 | 
| inf1\.6xlarge | 4750 | 593\.75 | 20000 | 
| inf1\.24xlarge | 19000 | 2375\.0 | 80000 | 
| p2\.xlarge | 750 | 93\.75 | 6000 | 
| p2\.8xlarge | 5000 | 625\.0 | 32500 | 
| p2\.16xlarge | 10000 | 1250\.0 | 65000 | 
| p3\.2xlarge | 1750 | 218\.75 | 10000 | 
| p3\.8xlarge | 7000 | 875\.0 | 40000 | 
| p3\.16xlarge | 14000 | 1750\.0 | 80000 | 
| p3dn\.24xlarge | 19000 | 2375\.0 | 80000 | 
| p4d\.24xlarge | 19000 | 2375\.0 | 80000 | 
| p4de\.24xlarge | 19000 | 2375\.0 | 80000 | 
| trn1\.2xlarge \* | 5000 | 20000 | 625\.0 | 2500\.0 | 16250 | 65000 | 
| trn1\.32xlarge | 80000 | 10000\.0 | 260000 | 
| vt1\.3xlarge \* | 2375 | 4750 | 296\.875 | 593\.75 | 10000 | 20000 | 
| vt1\.6xlarge | 4750 | 593\.75 | 20000 | 
| vt1\.24xlarge | 19000 | 2375\.0 | 80000 | 

### EBS optimization supported<a name="previous"></a>

The following table lists the instance types that support EBS optimization but EBS optimization is not enabled by default\. You can enable EBS optimization when you launch these instances or after they are running\. Instances must have EBS optimization enabled to achieve the level of performance described\. When you enable EBS optimization for an instance that is not EBS\-optimized by default, you pay an additional low, hourly fee for the dedicated capacity\. For pricing information, see EBS\-Optimized Instances on the [Amazon EC2 Pricing, On\-Demand Pricing page](http://aws.amazon.com/ec2/pricing/on-demand/)\.

**Note**  
You can also view this information programatically using the AWS CLI\. For more information, see [View instances types that support EBS optimization](#describe-ebs-optimization)\.


| Instance size | Maximum bandwidth \(Mbps\) | Maximum throughput \(MB/s, 128 KiB I/O\) | Maximum IOPS \(16 KiB I/O\) | 
| --- | --- | --- | --- | 
| c1\.xlarge | 1000 | 125\.0 | 8000 | 
| c3\.xlarge | 500 | 62\.5 | 4000 | 
| c3\.2xlarge | 1000 | 125\.0 | 8000 | 
| c3\.4xlarge | 2000 | 250\.0 | 16000 | 
| g2\.2xlarge | 1000 | 125\.0 | 8000 | 
| i2\.xlarge | 500 | 62\.5 | 4000 | 
| i2\.2xlarge | 1000 | 125\.0 | 8000 | 
| i2\.4xlarge | 2000 | 250\.0 | 16000 | 
| m1\.large | 500 | 62\.5 | 4000 | 
| m1\.xlarge | 1000 | 125\.0 | 8000 | 
| m2\.2xlarge | 500 | 62\.5 | 4000 | 
| m2\.4xlarge | 1000 | 125\.0 | 8000 | 
| m3\.xlarge | 500 | 62\.5 | 4000 | 
| m3\.2xlarge | 1000 | 125\.0 | 8000 | 
| r3\.xlarge | 500 | 62\.5 | 4000 | 
| r3\.2xlarge | 1000 | 125\.0 | 8000 | 
| r3\.4xlarge | 2000 | 250\.0 | 16000 | 

The `i2.8xlarge`, `c3.8xlarge`, and `r3.8xlarge` instances do not have dedicated EBS bandwidth and therefore do not offer EBS optimization\. On these instances, network traffic and Amazon EBS traffic share the same 10\-gigabit network interface\.

## Get maximum performance<a name="ebs-optimization-performance"></a>

You can use the `EBSIOBalance%` and `EBSByteBalance%` metrics to help you determine whether your instances are sized correctly\. You can view these metrics in the CloudWatch console and set an alarm that is triggered based on a threshold you specify\. These metrics are expressed as a percentage\. Instances with a consistently low balance percentage are candidates to size up\. Instances where the balance percentage never drops below 100% are candidates for downsizing\. For more information, see [Monitor your instances using CloudWatch](using-cloudwatch.md)\.

The high memory instances are designed to run large in\-memory databases, including production deployments of the SAP HANA in\-memory database, in the cloud\. To maximize EBS performance, use high memory instances with an even number of `io1` or `io2` volumes with identical provisioned performance\. For example, for IOPS heavy workloads, use four `io1` or `io2` volumes with 40,000 provisioned IOPS to get the maximum 160,000 instance IOPS\. Similarly, for throughput heavy workloads, use six `io1` or `io2` volumes with 48,000 provisioned IOPS to get the maximum 4,750 MB/s throughput\. For additional recommendations, see [Storage Configuration for SAP HANA](https://docs.aws.amazon.com/quickstart/latest/sap-hana/storage.html)\.

**Considerations**
+ G4dn, I3en, Inf1, M5a, M5ad, R5a, R5ad, T3, T3a, and Z1d instances launched after February 26, 2020 provide the maximum performance listed in the table above\. To get the maximum performance from an instance launched before February 26, 2020, stop and start it\.
+ C5, C5d, C5n, M5, M5d, M5n, M5dn, R5, R5d, R5n, R5dn, and P3dn instances launched after December 3, 2019 provide the maximum performance listed in the table above\. To get the maximum performance from an instance launched before December 3, 2019, stop and start it\.
+ `u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` instances launched after March 12, 2020 provide the performance in the table above\. Instances of these types launched before March 12, 2020 might provide lower performance\. To get the maximum performance from an instance launched before March 12, 2020, contact your account team to upgrade the instance at no additional cost\.

## View instances types that support EBS optimization<a name="describe-ebs-optimization"></a>

You can use the AWS CLI to view the instances types in the current Region that support EBS optimization\.

**To view the instance types that support EBS optimization and that have it enabled by default**  
Use the following [ describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command\.

```
aws ec2 describe-instance-types \
--query 'InstanceTypes[].{InstanceType:InstanceType,"MaxBandwidth(Mb/s)":EbsInfo.EbsOptimizedInfo.MaximumBandwidthInMbps,MaxIOPS:EbsInfo.EbsOptimizedInfo.MaximumIops,"MaxThroughput(MB/s)":EbsInfo.EbsOptimizedInfo.MaximumThroughputInMBps}' \
--filters Name=ebs-info.ebs-optimized-support,Values=default --output=table
```

Example output for `eu-west-1`:

```
-----------------------------------------------------------------------------------------------
|                                    DescribeInstanceTypes                                    |
+--------------+--------------------+---------------------+-----------+-----------------------+
| EBSOptimized |   InstanceType     | MaxBandwidth(Mb/s)  |  MaxIOPS  |  MaxThroughput(MB/s)  |
+--------------+--------------------+---------------------+-----------+-----------------------+
|  default     |  m5dn.8xlarge      |  6800               |  30000    |  850.0                |
|  default     |  m6gd.xlarge       |  4750               |  20000    |  593.75               |
|  default     |  c4.4xlarge        |  2000               |  16000    |  250.0                |
|  default     |  r4.16xlarge       |  14000              |  75000    |  1750.0               |
|  default     |  m5ad.large        |  2880               |  16000    |  360.0                |
...
```

**To view the instance types that support EBS optimization but do not have it enabled by default**  
Use the following [ describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command\.

```
aws ec2 describe-instance-types \
--query 'InstanceTypes[].{InstanceType:InstanceType,"MaxBandwidth(Mb/s)":EbsInfo.EbsOptimizedInfo.MaximumBandwidthInMbps,MaxIOPS:EbsInfo.EbsOptimizedInfo.MaximumIops,"MaxThroughput(MB/s)":EbsInfo.EbsOptimizedInfo.MaximumThroughputInMBps}' \
--filters Name=ebs-info.ebs-optimized-support,Values=supported --output=table
```

Example output for `eu-west-1`:

```
------------------------------------------------------------------------------------------
|                                  DescribeInstanceTypes                                 |
+--------------+---------------+----------------------+----------+-----------------------+
| EBSOptimized | InstanceType  | MaxBandwidth(Mb/s)   | MaxIOPS  |  MaxThroughput(MB/s)  |
+--------------+---------------+----------------------+----------+-----------------------+
|  supported   |  m2.4xlarge   |  1000                |  8000    |  125.0                |
|  supported   |  i2.2xlarge   |  1000                |  8000    |  125.0                |
|  supported   |  r3.4xlarge   |  2000                |  16000   |  250.0                |
|  supported   |  m3.xlarge    |  500                 |  4000    |  62.5                 |
|  supported   |  r3.2xlarge   |  1000                |  8000    |  125.0                |
...
```

## Enable EBS optimization at launch<a name="enable-ebs-optimization"></a>

You can enable optimization for an instance by setting its attribute for EBS optimization\.

**To enable Amazon EBS optimization when launching an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, select an AMI\.

1. In **Step 2: Choose an Instance Type**, select an instance type that is listed as supporting Amazon EBS optimization\.

1. In **Step 3: Configure Instance Details**, complete the fields that you need and choose **Launch as EBS\-optimized instance**\. If the instance type that you selected in the previous step doesn't support Amazon EBS optimization, this option is not present\. If the instance type that you selected is Amazon EBS–optimized by default, this option is selected and you can't deselect it\.

1. Follow the directions to complete the wizard and launch your instance\.

**To enable EBS optimization when launching an instance using the command line**

You can use one of the following commands with the corresponding option\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
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
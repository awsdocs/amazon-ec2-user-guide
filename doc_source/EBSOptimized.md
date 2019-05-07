# Amazon EBS–Optimized Instances<a name="EBSOptimized"></a>

An Amazon EBS–optimized instance uses an optimized configuration stack and provides additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

EBS–optimized instances deliver dedicated bandwidth to Amazon EBS, with options between 425 Mbps and 14,000 Mbps, depending on the instance type you use\. When attached to an EBS–optimized instance, General Purpose SSD \(`gp2`\) volumes are designed to deliver within 10% of their baseline and burst performance 99% of the time in a given year, and Provisioned IOPS SSD \(`io1`\) volumes are designed to deliver within 10% of their provisioned performance 99\.9% of the time in a given year\. Both Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) guarantee performance consistency of 90% of burst throughput 99% of the time in a given year\. Non\-compliant periods are approximately uniformly distributed, targeting 99% of expected total throughput each hour\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.

**Topics**
+ [Instance Types that Support EBS Optimization](#ebs-optimization-support)
+ [Enabling Amazon EBS Optimization at Launch](#enable-ebs-optimization)
+ [Modifying Amazon EBS Optimization for a Running Instance](#modify-ebs-optimized-attribute)

## Instance Types that Support EBS Optimization<a name="ebs-optimization-support"></a>

The following tables show which instance types support EBS optimization, the dedicated bandwidth to Amazon EBS, the maximum number of IOPS the instance can support if you are using a 16 KB I/O size, and the typical maximum aggregate throughput that can be achieved on that connection in MiB/s with a streaming read workload and 128 KB I/O size\. Choose an EBS–optimized instance that provides more dedicated Amazon EBS throughput than your application needs; otherwise, the connection between Amazon EBS and Amazon EC2 can become a performance bottleneck\.

For instance types that are EBS–optimized by default, there is no need to enable EBS optimization and no effect if you disable EBS optimization\. For instances that are not EBS–optimized by default, you can enable EBS optimization when you launch the instances, or enable EBS optimization after the instances are running\. Instances must have EBS optimization enabled to achieve the level of performance described in the table below\.

When you enable EBS optimization for an instance that is not EBS\-optimized by default, you pay an additional low, hourly fee for the dedicated capacity\. For pricing information, see EBS\-optimized Instances on the [Amazon EC2 Pricing page for On\-Demand instances](https://aws.amazon.com/ec2/pricing/on-demand/)\.

The `i2.8xlarge`, `c3.8xlarge`, and `r3.8xlarge` instances do not have dedicated EBS bandwidth and therefore do not offer EBS optimization\. On these instances, network traffic and Amazon EBS traffic share the same 10\-gigabit network interface\.

### Supported Current Generation Instance Types<a name="current"></a>

The following table lists current\-generation instance types that support EBS optimization\.


| Instance type | EBS\-optimized by default | Maximum bandwidth \(Mbps\) | Maximum throughput \(MB/s, 128 KB I/O\) | Maximum IOPS \(16 KB I/O\) | 
| --- | --- | --- | --- | --- | 
| a1\.medium | Yes | 3,500 | 437\.5 | 20,000 | 
| a1\.large | Yes | 3,500 | 437\.5 | 20,000 | 
| a1\.xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| a1\.2xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| a1\.4xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| c4\.large | Yes | 500 | 62\.5 | 4,000 | 
| c4\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| c4\.2xlarge | Yes | 1,000 | 125 | 8,000 | 
| c4\.4xlarge | Yes | 2,000 | 250 | 16,000 | 
| c4\.8xlarge | Yes | 4,000 | 500 | 32,000 | 
| c5\.large \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5\.xlarge \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5\.2xlarge \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5\.4xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| c5\.9xlarge | Yes | 7,000 | 875 | 40,000 | 
| c5\.18xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| c5d\.large \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5d\.xlarge \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5d\.2xlarge \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5d\.4xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| c5d\.9xlarge | Yes | 7,000 | 875 | 40,000 | 
| c5d\.18xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| c5n\.large \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5n\.xlarge \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5n\.2xlarge \* | Yes | 3,500 | 437\.5 | 20,000 | 
| c5n\.4xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| c5n\.9xlarge | Yes | 7,000 | 875 | 40,000 | 
| c5n\.18xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| d2\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| d2\.2xlarge | Yes | 1,000 | 125 | 8,000 | 
| d2\.4xlarge | Yes | 2,000 | 250 | 16,000 | 
| d2\.8xlarge | Yes | 4,000 | 500 | 32,000 | 
| f1\.2xlarge | Yes | 1,700 | 212\.5 | 12,000 | 
| f1\.4xlarge | Yes | 3,500 | 400 | 44,000 | 
| f1\.16xlarge | Yes | 14,000 | 1,750 | 75,000 | 
| g3s\.xlarge | Yes | 850 | 100 | 5,000 | 
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
| i3\.metal | Yes | 14,000 | 1,750 | 65,000 | 
| m4\.large | Yes | 450 | 56\.25 | 3,600 | 
| m4\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| m4\.2xlarge | Yes | 1,000 | 125 | 8,000 | 
| m4\.4xlarge | Yes | 2,000 | 250 | 16,000 | 
| m4\.10xlarge | Yes | 4,000 | 500 | 32,000 | 
| m4\.16xlarge | Yes | 10,000 | 1,250 | 65,000 | 
| m5\.large \* | Yes | 3,500 | 437\.5 | 18,750 | 
| m5\.xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| m5\.2xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| m5\.4xlarge | Yes | 3,500 | 437\.5 | 18,750 | 
| m5\.12xlarge | Yes | 7,000 | 875 | 40,000 | 
| m5\.24xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| m5\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| m5a\.large \* | Yes | 2,120 | 265 | 16,000 | 
| m5a\.xlarge \* | Yes | 2,120 | 265 | 16,000 | 
| m5a\.2xlarge \* | Yes | 2,120 | 265 | 16,000 | 
| m5a\.4xlarge | Yes | 2,120 | 265 | 16,000 | 
| m5a\.12xlarge | Yes | 5,000 | 625 | 30,000 | 
| m5a\.24xlarge | Yes | 10,000 | 1,250 | 60,000 | 
| m5ad\.large \* | Yes | 2,120 | 265 | 16,000 | 
| m5ad\.xlarge \* | Yes | 2,120 | 265 | 16,000 | 
| m5ad\.2xlarge \* | Yes | 2,120 | 265 | 16,000 | 
| m5ad\.4xlarge | Yes | 2,120 | 265 | 16,000 | 
| m5ad\.12xlarge | Yes | 5,000 | 675 | 30,000 | 
| m5ad\.24xlarge | Yes | 10,000 | 1,250 | 60,000 | 
| m5d\.large \* | Yes | 3,500 | 437\.5 | 18,750 | 
| m5d\.xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| m5d\.2xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| m5d\.4xlarge | Yes | 3,500 | 437\.5 | 18,750 | 
| m5d\.12xlarge | Yes | 7,000 | 875 | 40,000 | 
| m5d\.24xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| m5d\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| p2\.xlarge | Yes | 750 | 93\.75 | 6,000 | 
| p2\.8xlarge | Yes | 5,000 | 625 | 32,500 | 
| p2\.16xlarge | Yes | 10,000 | 1,250 | 65,000 | 
| p3\.2xlarge | Yes | 1,750 | 218 | 10,000 | 
| p3\.8xlarge | Yes | 7,000 | 875 | 40,000 | 
| p3\.16xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| p3dn\.24xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| r4\.large | Yes | 425 | 53\.13 | 3,000 | 
| r4\.xlarge | Yes | 850 | 106\.25 | 6,000 | 
| r4\.2xlarge | Yes | 1,700 | 212\.5 | 12,000 | 
| r4\.4xlarge | Yes | 3,500 | 437\.5 | 18,750 | 
| r4\.8xlarge | Yes | 7,000 | 875 | 37,500 | 
| r4\.16xlarge | Yes | 14,000 | 1,750 | 75,000 | 
| r5\.large \* | Yes | 3,500 | 437\.5 | 18,750 | 
| r5\.xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| r5\.2xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| r5\.4xlarge | Yes | 3,500 | 437\.5 | 18,750 | 
| r5\.12xlarge | Yes | 7,000 | 875 | 40,000 | 
| r5\.24xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| r5\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| r5a\.large \* | Yes | 2,210 | 265 | 16,000 | 
| r5a\.xlarge \* | Yes | 2,210 | 265 | 16,000 | 
| r5a\.2xlarge \* | Yes | 2,210 | 265 | 16,000 | 
| r5a\.4xlarge | Yes | 2,210 | 265 | 16,000 | 
| r5a\.12xlarge | Yes | 5,000 | 625 | 30,000 | 
| r5a\.24xlarge | Yes | 10,000 | 1,250 | 60,000 | 
| r5ad\.large \* | Yes | 2,210 | 265 | 16,000 | 
| r5ad\.xlarge \* | Yes | 2,210 | 265 | 16,000 | 
| r5ad\.2xlarge \* | Yes | 2,210 | 265 | 16,000 | 
| r5ad\.4xlarge | Yes | 2,210 | 265 | 16,000 | 
| r5ad\.12xlarge | Yes | 5,000 | 625 | 30,000 | 
| r5ad\.24xlarge | Yes | 10,000 | 1,250 | 60,000 | 
| r5d\.large \* | Yes | 3,500 | 437\.5 | 18,750 | 
| r5d\.xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| r5d\.2xlarge \* | Yes | 3,500 | 437\.5 | 18,750 | 
| r5d\.4xlarge | Yes | 3,500 | 437\.5 | 18,750 | 
| r5d\.12xlarge | Yes | 7,000 | 875 | 40,000 | 
| r5d\.24xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| r5d\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| t3\.nano \* | Yes | 1,536 | 192 | 11,800 | 
| t3\.micro \* | Yes | 1,536 | 192 | 11,800 | 
| t3\.small \* | Yes | 1,536 | 192 | 11,800 | 
| t3\.medium \* | Yes | 1,536 | 192 | 11,800 | 
| t3\.large \* | Yes | 2,048 | 256 | 15,700 | 
| t3\.xlarge \* | Yes | 2,048 | 256 | 15,700 | 
| t3\.2xlarge \* | Yes | 2,048 | 256 | 15,700 | 
| t3a\.nano \* | Yes | 1,536 | 192 | 11,800 | 
| t3a\.micro \* | Yes | 1,536 | 192 | 11,800 | 
| t3a\.small \* | Yes | 1,536 | 192 | 11,800 | 
| t3a\.medium \* | Yes | 1,536 | 192 | 11,800 | 
| t3a\.large \* | Yes | 2,048 | 256 | 15,700 | 
| t3a\.xlarge \* | Yes | 2,048 | 256 | 15,700 | 
| t3a\.2xlarge \* | Yes | 2,048 | 256 | 15,700 | 
| u\-6tb1\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| u\-9tb1\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| u\-12tb1\.metal | Yes | 14,000 | 1,750 | 80,000 | 
| x1\.16xlarge | Yes | 7,000 | 875 | 40,000 | 
| x1\.32xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| x1e\.xlarge | Yes | 500 | 62\.5 | 3,700 | 
| x1e\.2xlarge | Yes | 1,000 | 125 | 7,400 | 
| x1e\.4xlarge | Yes | 1,750 | 218\.75 | 10,000 | 
| x1e\.8xlarge | Yes | 3,500 | 437\.5 | 20,000 | 
| x1e\.16xlarge | Yes | 7,000 | 875 | 40,000 | 
| x1e\.32xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| z1d\.large \* | Yes | 2,333 | 291 | 13,333 | 
| z1d\.xlarge \* | Yes | 2,333 | 291 | 13,333 | 
| z1d\.2xlarge | Yes | 2,333 | 292 | 13,333 | 
| z1d\.3xlarge | Yes | 3,500 | 438 | 20,000 | 
| z1d\.6xlarge | Yes | 7,000 | 875 | 40,000 | 
| z1d\.12xlarge | Yes | 14,000 | 1,750 | 80,000 | 
| z1d\.metal | Yes | 14,000 | 1,750 | 80,000 | 

\* These instance types can support maximum performance for 30 minutes at least once every 24 hours\. For example, `c5.large` instances can deliver 437\.5 MB/s for 30 minutes at least once every 24 hours\. If you have a workload that requires sustained maximum performance for longer than 30 minutes, select an instance type according to baseline performance as shown in the following table:


| Instance type | Baseline bandwidth \(Mbps\) | Baseline throughput \(MB/s, 128 KB I/O\) | Baseline IOPS \(16 KB I/O\) | 
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

### Supported Previous Generation Instance Types<a name="previous"></a>

The following table lists previous\-generation instance types that support EBS optimization\.


**Previous Generation Instances**  

| Instance type | EBS\-optimized by default | Maximum bandwidth \(Mbps\) | Maximum throughput \(MB/s, 128 KB I/O\) | Maximum IOPS \(16 KB I/O\) | 
| --- | --- | --- | --- | --- | 
| c1\.xlarge | No | 1,000 | 125 | 8,000 | 
| c3\.xlarge | No | 500 | 62\.5 | 4,000 | 
| c3\.2xlarge | No | 1,000 | 125 | 8,000 | 
| c3\.4xlarge | No | 2,000 | 250 | 16,000 | 
| g2\.2xlarge | No | 1,000 | 125 | 8,000 | 
| i2\.xlarge | No | 500 | 62\.5 | 4,000 | 
| i2\.2xlarge | No | 1,000 | 125 | 8,000 | 
| i2\.4xlarge | No | 2,000 | 250 | 16,000 | 
| m1\.large | No | 500 | 62\.5 | 4,000 | 
| m1\.xlarge | No | 1,000 | 125 | 8,000 | 
| m2\.2xlarge | No | 500 | 62\.5 | 4,000 | 
| m2\.4xlarge | No | 1,000 | 125 | 8,000 | 
| m3\.xlarge | No | 500 | 62\.5 | 4,000 | 
| m3\.2xlarge | No | 1,000 | 125 | 8,000 | 
| r3\.xlarge | No | 500 | 62\.5 | 4,000 | 
| r3\.2xlarge | No | 1,000 | 125 | 8,000 | 
| r3\.4xlarge | No | 2,000 | 250 | 16,000 | 

## Enabling Amazon EBS Optimization at Launch<a name="enable-ebs-optimization"></a>

You can enable optimization for an instance by setting its Amazon EBS–optimized attribute\.

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

## Modifying Amazon EBS Optimization for a Running Instance<a name="modify-ebs-optimized-attribute"></a>

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
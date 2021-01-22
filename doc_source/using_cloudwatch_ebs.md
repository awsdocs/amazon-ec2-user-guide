# Amazon CloudWatch metrics for Amazon EBS<a name="using_cloudwatch_ebs"></a>

Amazon CloudWatch metrics are statistical data that you can use to view, analyze, and set alarms on the operational behavior of your volumes\. 

Data is available automatically in 1\-minute periods at no charge\.

When you get data from CloudWatch, you can include a `Period` request parameter to specify the granularity of the returned data\. This is different than the period that we use when we collect the data \(1\-minute periods\)\. We recommend that you specify a period in your request that is equal to or greater than the collection period to ensure that the returned data is valid\.

You can get the data using either the CloudWatch API or the Amazon EC2 console\. The console takes the raw data from the CloudWatch API and displays a series of graphs based on the data\. Depending on your needs, you might prefer to use either the data from the API or the graphs in the console\.

## Amazon EBS metrics<a name="ebs-metrics"></a>

Amazon Elastic Block Store \(Amazon EBS\) sends data points to CloudWatch for several metrics\. All EBS volume types automatically send 1\-minute metrics to CloudWatch\. Data is reported to CloudWatch only when the volume is attached to an instance

Some of these metrics have differences on instances built on the Nitro System\. For a list of these instance types, see [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

The `AWS/EBS` namespace includes the following metrics\.

**Topics**
+ [Volume metrics](#ebs-volume-metrics)
+ [Fast snapshot restore metrics](#fast-snapshot-restore-metrics)

### Volume metrics<a name="ebs-volume-metrics"></a>

The `AWS/EBS` namespace includes the following metrics for EBS volumes\. To get information about the available disk space from the operating system on an instance, see [View free disk space](ebs-describing-volumes.md#ebs-view-free-disk-space)\.


| Metric | Description | 
| --- | --- | 
| VolumeReadBytes |  Provides information on the read operations in a specified period of time\. The `Sum` statistic reports the total number of bytes transferred during the period\. The `Average` statistic reports the average size of each read operation during the period, except on volumes attached to a Nitro\-based instance, where the average represents the average over the specified period\. The `SampleCount` statistic reports the total number of read operations during the period, except on volumes attached to a Nitro\-based instance, where the sample count represents the number of data points used in the statistical calculation\. For Xen instances, data is reported only when there is read activity on the volume\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Bytes  | 
| VolumeWriteBytes |  Provides information on the write operations in a specified period of time\. The `Sum` statistic reports the total number of bytes transferred during the period\. The `Average` statistic reports the average size of each write operation during the period, except on volumes attached to a Nitro\-based instance, where the average represents the average over the specified period\. The `SampleCount` statistic reports the total number of write operations during the period, except on volumes attached to a Nitro\-based instance, where the sample count represents the number of data points used in the statistical calculation\. For Xen instances, data is reported only when there is write activity on the volume\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Bytes  | 
| VolumeReadOps |  The total number of read operations in a specified period of time\. To calculate the average read operations per second \(read IOPS\) for the period, divide the total read operations in the period by the number of seconds in that period\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Count  | 
| VolumeWriteOps |  The total number of write operations in a specified period of time\. To calculate the average write operations per second \(write IOPS\) for the period, divide the total write operations in the period by the number of seconds in that period\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Count  | 
| VolumeTotalReadTime |   This metric is not supported with Multi\-Attach enabled volumes\.  The total number of seconds spent by all read operations that completed in a specified period of time\. If multiple requests are submitted at the same time, this total could be greater than the length of the period\. For example, for a period of 1 minutes \(60 seconds\): if 150 operations completed during that period, and each operation took 1 second, the value would be 150 seconds\. For Xen instances, data is reported only when there is read activity on the volume\. The `Average` statistic on this metric is not relevant for volumes attached to Nitro\-based instances\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Seconds  | 
| VolumeTotalWriteTime |   This metric is not supported with Multi\-Attach enabled volumes\.  The total number of seconds spent by all write operations that completed in a specified period of time\. If multiple requests are submitted at the same time, this total could be greater than the length of the period\. For example, for a period of 1 minute \(60 seconds\): if 150 operations completed during that period, and each operation took 1 second, the value would be 150 seconds\. For Xen instances, data is reported only when there is write activity on the volume\. The `Average` statistic on this metric is not relevant for volumes attached to Nitro\-based instances\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Seconds  | 
| VolumeIdleTime |   This metric is not supported with Multi\-Attach enabled volumes\.  The total number of seconds in a specified period of time when no read or write operations were submitted\. The `Average` statistic on this metric is not relevant for volumes attached to Nitro\-based instances\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Seconds  | 
| VolumeQueueLength |  The number of read and write operation requests waiting to be completed in a specified period of time\. The `Sum` statistic on this metric is not relevant for volumes attached to Nitro\-based instances\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to Nitro\-based instances\. Units: Count  | 
| VolumeThroughputPercentage |   This metric is not supported with Multi\-Attach enabled volumes\.  Used with Provisioned IOPS SSD volumes only\. The percentage of I/O operations per second \(IOPS\) delivered of the total IOPS provisioned for an Amazon EBS volume\. Provisioned IOPS SSD volumes deliver their provisioned performance 99\.9 percent of the time\. During a write, if there are no other pending I/O requests in a minute, the metric value will be 100 percent\. Also, a volume's I/O performance may become degraded temporarily due to an action you have taken \(for example, creating a snapshot of a volume during peak usage, running the volume on a non\-EBS\-optimized instance, or accessing data on the volume for the first time\)\. Units: Percent  | 
| VolumeConsumedReadWriteOps |  Used with Provisioned IOPS SSD volumes only\. The total amount of read and write operations \(normalized to 256K capacity units\) consumed in a specified period of time\. I/O operations that are smaller than 256K each count as 1 consumed IOPS\. I/O operations that are larger than 256K are counted in 256K capacity units\. For example, a 1024K I/O would count as 4 consumed IOPS\. Units: Count  | 
| BurstBalance |  Used with General Purpose SSD \(`gp2`\), Throughput Optimized HDD \(`st1`\), and Cold HDD \(`sc1`\) volumes only\. Provides information about the percentage of I/O credits \(for `gp2`\) or throughput credits \(for `st1` and `sc1`\) remaining in the burst bucket\. Data is reported to CloudWatch only when the volume is active\. If the volume is not attached, no data is reported\. The `Sum` statistic on this metric is not relevant for volumes attached to instances built on the Nitro System\. If the baseline performance of the volume exceeds the maximum burst performance, credits are never spent\. If the volume is attached to an instance built on the Nitro System, the burst balance is not reported\. For other instances, the reported burst balance is 100%\. For more information, see [I/O Credits and burst performance](ebs-volume-types.md#IOcredit)\. Units: Percent  | 

### Fast snapshot restore metrics<a name="fast-snapshot-restore-metrics"></a>

`AWS/EBS` namespace includes the following metrics for [fast snapshot restore](ebs-fast-snapshot-restore.md)\.


| Metric | Description | 
| --- | --- | 
| FastSnapshotRestoreCreditsBucketSize |  The maximum number of volume create credits that can be accumulated\. This metric is reported per snapshot per Availability Zone\. The most meaningful statistic is `Average`\. The results for the `Minimum` and `Maximum` statistics are the same as for `Average` and could be used instead\.  | 
| FastSnapshotRestoreCreditsBalance |  The number of volume create credits available\. This metric is reported per snapshot per Availability Zone\. The most meaningful statistic is `Average`\. The results for the `Minimum` and `Maximum` statistics are the same as for `Average` and could be used instead\.  | 

## Dimensions for Amazon EBS metrics<a name="ebs-metric-dimensions"></a>

The supported dimension is the volume ID \(`VolumeId`\)\. All available statistics are filtered by volume ID\.

For the [volume metrics](#ebs-volume-metrics), the supported dimension is the volume ID \(`VolumeId`\)\. All available statistics are filtered by volume ID\.

For the [fast snapshot restore metrics](#fast-snapshot-restore-metrics), the supported dimensions are the snapshot ID \(`SnapshotId`\) and the Availability Zone \(`AvailabilityZone`\)\.

## Graphs in the Amazon EC2 console<a name="graphs-in-the-aws-management-console-2"></a>

After you create a volume, you can view the volume's monitoring graphs in the Amazon EC2 console\. Select a volume on the **Volumes** page in the console and choose **Monitoring**\. The following table lists the graphs that are displayed\. The column on the right describes how the raw data metrics from the CloudWatch API are used to produce each graph\. The period for all the graphs is 5 minutes\.


| Graph | Description using raw metrics | 
| --- | --- | 
|  Read Bandwidth \(KiB/s\)  | Sum\(VolumeReadBytes\) / Period / 1024 | 
|  Write Bandwidth \(KiB/s\)  | Sum\(VolumeWriteBytes\) / Period / 1024 | 
|  Read Throughput \(IOPS\)  | Sum\(VolumeReadOps\) / Period | 
|  Write Throughput \(IOPS\)  |  Sum\(VolumeWriteOps\) / Period  | 
|  Avg Queue Length \(Operations\)  |  Avg\(VolumeQueueLength\)  | 
|  % Time Spent Idle  |  Sum\(VolumeIdleTime\) / Period × 100  | 
|  Avg Read Size \(KiB/Operation\)  |  `Avg(VolumeReadBytes) / 1024` For Nitro\-based instances, the following formula derives Average Read Size using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeReadBytes) / Sum(VolumeReadOps)) / 1024` The `VolumeReadBytes` and `VolumeReadOps` metrics are available in the EBS CloudWatch console\.  | 
|  Avg Write Size \(KiB/Operation\)  |  `Avg(VolumeWriteBytes) / 1024` For Nitro\-based instances, the following formula derives Average Write Size using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeWriteBytes) / Sum(VolumeWriteOps)) / 1024` The `VolumeWriteBytes` and `VolumeWriteOps` metrics are available in the EBS CloudWatch console\.  | 
|  Avg Read Latency \(ms/Operation\)  |  `Avg(VolumeTotalReadTime) × 1000` For Nitro\-based instances, the following formula derives Average Read Latency using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeTotalReadTime) / Sum(VolumeReadOps)) × 1000` The `VolumeTotalReadTime` and `VolumeReadOps` metrics are available in the EBS CloudWatch console\.  | 
|  Avg Write Latency \(ms/Operation\)  |  `Avg(VolumeTotalWriteTime) × 1000` For Nitro\-based instances, the following formula derives Average Write Latency using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeTotalWriteTime) / Sum(VolumeWriteOps)) * 1000` The `VolumeTotalWriteTime` and `VolumeWriteOps` metrics are available in the EBS CloudWatch console\.  | 

For the average latency graphs and average size graphs, the average is calculated over the total number of operations \(read or write, whichever is applicable to the graph\) that completed during the period\.

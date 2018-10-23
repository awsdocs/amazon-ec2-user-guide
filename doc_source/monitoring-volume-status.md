# Monitoring the Status of Your Volumes<a name="monitoring-volume-status"></a>

Amazon Web Services \(AWS\) automatically provides data, such as Amazon CloudWatch metrics and volume status checks, that you can use to monitor your Amazon Elastic Block Store \(Amazon EBS\) volumes\.

**Topics**
+ [Monitoring Volumes with CloudWatch](#using_cloudwatch_ebs)
+ [Monitoring Volumes with Status Checks](#monitoring-volume-checks)
+ [Monitoring Volume Events](#monitoring-vol-events)
+ [Working with an Impaired Volume](#work_volumes_impaired)
+ [Working with the AutoEnableIO Volume Attribute](#volumeIO)

## Monitoring Volumes with CloudWatch<a name="using_cloudwatch_ebs"></a>

CloudWatch metrics are statistical data that you can use to view, analyze, and set alarms on the operational behavior of your volumes\. 

The following table describes the types of monitoring data available for your Amazon EBS volumes\.


| Type | Description | 
| --- | --- | 
|  Basic  |  Data is available automatically in 5\-minute periods at no charge\. This includes data for the root device volumes for EBS\-backed instances\.  | 
|  Detailed  |  Provisioned IOPS SSD \(`io1`\) volumes automatically send one\-minute metrics to CloudWatch\.  | 

When you get data from CloudWatch, you can include a `Period` request parameter to specify the granularity of the returned data\. This is different than the period that we use when we collect the data \(5\-minute periods\)\. We recommend that you specify a period in your request that is equal to or larger than the collection period to ensure that the returned data is valid\.

You can get the data using either the CloudWatch API or the Amazon EC2 console\. The console takes the raw data from the CloudWatch API and displays a series of graphs based on the data\. Depending on your needs, you might prefer to use either the data from the API or the graphs in the console\.

### Amazon EBS Metrics<a name="ebs-metrics"></a>

Amazon Elastic Block Store \(Amazon EBS\) sends data points to CloudWatch for several metrics\. Amazon EBS General Purpose SSD \(gp2\), Throughput Optimized HDD \(st1\) , Cold HDD \(sc1\), and Magnetic \(standard\) volumes automatically send five\-minute metrics to CloudWatch\. Provisioned IOPS SSD \(io1\) volumes automatically send one\-minute metrics to CloudWatch\. Data is only reported to CloudWatch when the volume is attached to an instance\. For more information about how to monitor Amazon EBS, see [Monitoring the Status of Your Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-volume-status.html) in the *Amazon EC2 User Guide for Linux Instances*\.

The `AWS/EBS` namespace includes the following metrics\.


| Metric | Description | 
| --- | --- | 
|  `VolumeReadBytes`  |  Provides information on the read operations in a specified period of time\. The `Sum` statistic reports the total number of bytes transferred during the period\. The `Average` statistic reports the average size of each read operation during the period, except on volumes attached to a Nitro instance, where the average represents the average over the specified period\. The `SampleCount` statistic reports the total number of read operations during the period, except on volumes attached to a Nitro instance, where the sample count represents the number of data points used in the statistical calculation\. Data is reported to CloudWatch only when the volume is active\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Bytes  | 
| VolumeWriteBytes |  Provides information on the write operations in a specified period of time\. The `Sum` statistic reports the total number of bytes transferred during the period\. The `Average` statistic reports the average size of each write operation during the period, except on volumes attached to a Nitro instance, where the average represents the average over the specified period\. The `SampleCount` statistic reports the total number of write operations during the period, except on volumes attached to a Nitro instance, where the sample count represents the number of data points used in the statistical calculation\. Data is reported to CloudWatch only when the volume is active\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Bytes  | 
|  `VolumeReadOps`  |  The total number of read operations in a specified period of time\. To calculate the average read operations per second \(read IOPS\) for the period, divide the total read operations in the period by the number of seconds in that period\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Count  | 
| VolumeWriteOps |  The total number of write operations in a specified period of time\. To calculate the average write operations per second \(write IOPS\) for the period, divide the total write operations in the period by the number of seconds in that period\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Count  | 
|  `VolumeTotalReadTime`  |  The total number of seconds spent by all read operations that completed in a specified period of time\. If multiple requests are submitted at the same time, this total could be greater than the length of the period\. For example, for a period of 5 minutes \(300 seconds\): if 700 operations completed during that period, and each operation took 1 second, the value would be 700 seconds\. The `Average` statistic on this metric is not relevant for volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Seconds  | 
| VolumeTotalWriteTime |  The total number of seconds spent by all write operations that completed in a specified period of time\. If multiple requests are submitted at the same time, this total could be greater than the length of the period\. For example, for a period of 5 minutes \(300 seconds\): if 700 operations completed during that period, and each operation took 1 second, the value would be 700 seconds\. The `Average` statistic on this metric is not relevant for volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Seconds  | 
|  `VolumeIdleTime`  |  The total number of seconds in a specified period of time when no read or write operations were submitted\. The `Average` statistic on this metric is not relevant for volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Seconds  | 
|  `VolumeQueueLength`  |  The number of read and write operation requests waiting to be completed in a specified period of time\. The `Sum` statistic on this metric is not relevant for volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. The `Minimum` and `Maximum` statistics on this metric are supported only by volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Count  | 
|  `VolumeThroughputPercentage`  |  Used with Provisioned IOPS SSD volumes only\. The percentage of I/O operations per second \(IOPS\) delivered of the total IOPS provisioned for an Amazon EBS volume\. Provisioned IOPS SSD volumes deliver within 10 percent of the provisioned IOPS performance 99\.9 percent of the time over a given year\. During a write, if there are no other pending I/O requests in a minute, the metric value will be 100 percent\. Also, a volume's I/O performance may become degraded temporarily due to an action you have taken \(for example, creating a snapshot of a volume during peak usage, running the volume on a non\-EBS\-optimized instance, or accessing data on the volume for the first time\)\. Units: Percent  | 
|  `VolumeConsumedReadWriteOps`  |  Used with Provisioned IOPS SSD volumes only\. The total amount of read and write operations \(normalized to 256K capacity units\) consumed in a specified period of time\. I/O operations that are smaller than 256K each count as 1 consumed IOPS\. I/O operations that are larger than 256K are counted in 256K capacity units\. For example, a 1024K I/O would count as 4 consumed IOPS\. Units: Count  | 
| `BurstBalance` |  Used with General Purpose SSD \(`gp2`\), Throughput Optimized HDD \(`st1`\), and Cold HDD \(`sc1`\) volumes only\. Provides information about the percentage of I/O credits \(for `gp2`\) or throughput credits \(for `st1` and `sc1`\) remaining in the burst bucket\. Data is reported to CloudWatch only when the volume is active\. If the volume is not attached, no data is reported\. The `Sum` statistic on this metric is not relevant for volumes attached to the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d\. Units: Percent  | 

### Dimensions for Amazon EBS Metrics<a name="ebs-metric-dimensions"></a>

The only dimension that Amazon EBS sends to CloudWatch is the volume ID\. This means that all available statistics are filtered by volume ID\. 

### Graphs in the Amazon EC2 Console<a name="graphs-in-the-aws-management-console-2"></a>

After you create a volume, you can view the volume's monitoring graphs in the Amazon EC2 console\. Select a volume on the **Volumes** page in the console and choose **Monitoring**\. The following table lists the graphs that are displayed\. The column on the right describes how the raw data metrics from the CloudWatch API are used to produce each graph\. The period for all the graphs is 5 minutes\. 


| Graph | Description using raw metrics | 
| --- | --- | 
|  Read Bandwidth \(KiB/s\)  |  `Sum(VolumeReadBytes) / Period / 1024`  | 
|  Write Bandwidth \(KiB/s\)  |  `Sum(VolumeWriteBytes) / Period / 1024`  | 
|  Read Throughput \(IOPS\)  |  `Sum(VolumeReadOps) / Period`  | 
|  Write Throughput \(IOPS\)  |  `Sum(VolumeWriteOps) / Period`  | 
|  Avg Queue Length \(Operations\)  |  `Avg(VolumeQueueLength)`  | 
|  % Time Spent Idle  |  `Sum(VolumeIdleTime) / Period × 100`  | 
|  Avg Read Size \(KiB/Operation\)  |  `Avg(VolumeReadBytes) / 1024`  For C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d instances, the following formula derives Average Read Size using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeReadBytes) / Sum(VolumeReadOps)) / 1024` The `VolumeReadBytes` and `VolumeReadOps` metrics are available in the EBS CloudWatch console\.   | 
|  Avg Write Size \(KiB/Operation\)  |  `Avg(VolumeWriteBytes) / 1024`  For C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d instances, the following formula derives Average Write Size using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeWriteBytes) / Sum(VolumeWriteOps)) / 1024` The `VolumeWriteBytes` and `VolumeWriteOps` metrics are available in the EBS CloudWatch console\.   | 
|  Avg Read Latency \(ms/Operation\)  |  `Avg(VolumeTotalReadTime) × 1000`  For C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d instances, the following formula derives Average Read Latency using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeTotalReadTime) / Sum(VolumeReadOps)) × 1000` The `VolumeTotalReadTime` and `VolumeReadOps` metrics are available in the EBS CloudWatch console\.   | 
|  Avg Write Latency \(ms/Operation\)  |  `Avg(VolumeTotalWriteTime) × 1000`  For C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, and z1d instances, the following formula derives Average Write Latency using [CloudWatch Metric Math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html): `(Sum(VolumeTotalWriteTime) / Sum(VolumeWriteOps)) * 1000` The `VolumeTotalWriteTime` and `VolumeWriteOps` metrics are available in the EBS CloudWatch console\.   | 

For the average latency graphs and average size graphs, the average is calculated over the total number of operations \(read or write, whichever is applicable to the graph\) that completed during the period\. 

## Monitoring Volumes with Status Checks<a name="monitoring-volume-checks"></a>

Volume status checks enable you to better understand, track, and manage potential inconsistencies in the data on an Amazon EBS volume\. They are designed to provide you with the information that you need to determine whether your Amazon EBS volumes are impaired, and to help you control how a potentially inconsistent volume is handled\.

Volume status checks are automated tests that run every 5 minutes and return a pass or fail status\. If all checks pass, the status of the volume is `ok`\. If a check fails, the status of the volume is `impaired`\. If the status is `insufficient-data`, the checks may still be in progress on the volume\. You can view the results of volume status checks to identify any impaired volumes and take any necessary actions\.

When Amazon EBS determines that a volume's data is potentially inconsistent, the default is that it disables I/O to the volume from any attached EC2 instances, which helps to prevent data corruption\. After I/O is disabled, the next volume status check fails, and the volume status is `impaired`\. In addition, you'll see an event that lets you know that I/O is disabled, and that you can resolve the impaired status of the volume by enabling I/O to the volume\. We wait until you enable I/O to give you the opportunity to decide whether to continue to let your instances use the volume, or to run a consistency check using a command, such as fsck, before doing so\.

**Note**  
Volume status is based on the volume status checks, and does not reflect the volume state\. Therefore, volume status does not indicate volumes in the `error` state \(for example, when a volume is incapable of accepting I/O\.\)

If the consistency of a particular volume is not a concern for you, and you'd prefer that the volume be made available immediately if it's impaired, you can override the default behavior by configuring the volume to automatically enable I/O\. If you enable the `AutoEnableIO` volume attribute, the volume status check continues to pass\. In addition, you'll see an event that lets you know that the volume was determined to be potentially inconsistent, but that its I/O was automatically enabled\. This enables you to check the volume's consistency or replace it at a later time\.

The I/O performance status check compares actual volume performance to the expected performance of a volume and alerts you if the volume is performing below expectations\. This status check is only available for `io1` volumes that are attached to an instance and is not valid for General Purpose SSD \(`gp2`\), Throughput Optimized HDD \(`st1`\), Cold HDD \(`sc1`\), or Magnetic \(`standard`\) volumes\. The I/O performance status check is performed once every minute and CloudWatch collects this data every 5 minutes, so it may take up to 5 minutes from the moment you attach a `io1` volume to an instance for this check to report the I/O performance status\.

**Important**  
While initializing `io1` volumes that were restored from snapshots, the performance of the volume may drop below 50 percent of its expected level, which causes the volume to display a `warning` state in the **I/O Performance** status check\. This is expected, and you can ignore the `warning` state on `io1` volumes while you are initializing them\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.

The following table lists statuses for Amazon EBS volumes\.


| Volume status | I/O enabled status | I/O performance status \(only available for Provisioned IOPS volumes\) | 
| --- | --- | --- | 
|  `ok`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\)  |  Normal \(Volume performance is as expected\)  | 
|  `warning`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\)  |  Degraded \(Volume performance is below expectations\) Severely Degraded \(Volume performance is well below expectations\)  | 
|  `impaired`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\) Disabled \(Volume is offline and pending recovery, or is waiting for the user to enable I/O\)  |  Stalled \(Volume performance is severely impacted\) Not Available \(Unable to determine I/O performance because I/O is disabled\)  | 
|  `insufficient-data`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\) Insufficient Data  |  Insufficient Data  | 

To view and work with status checks, you can use the Amazon EC2 console, the API, or the command line interface\.

**To view status checks in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. On the **EBS Volumes** page, use the **Volume Status** column lists the operational status of each volume\.

1. To view an individual volume's status, select the volume, and choose **Status Checks**\.  
![\[Viewing EBS volume status\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/AutoEnableSetting_statustab_gwt.png)

1. If you have a volume with a failed status check \(status is `impaired`\), see [Working with an Impaired Volume](#work_volumes_impaired)\.

Alternatively, you can use the **Events** pane to view all events for your instances and volumes in a single pane\. For more information, see [Monitoring Volume Events](#monitoring-vol-events)\.

**To view volume status information with the command line**

You can use one of the following commands to view the status of your Amazon EBS volumes\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volume\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volume-status.html) \(AWS CLI\)
+ [Get\-EC2VolumeStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VolumeStatus.html) \(AWS Tools for Windows PowerShell\)

## Monitoring Volume Events<a name="monitoring-vol-events"></a>

When Amazon EBS determines that a volume's data is potentially inconsistent, it disables I/O to the volume from any attached EC2 instances by default\. This causes the volume status check to fail, and creates a volume status event that indicates the cause of the failure\. 

To automatically enable I/O on a volume with potential data inconsistencies, change the setting of the `AutoEnableIO` volume attribute\. For more information about changing this attribute, see [Working with an Impaired Volume](#work_volumes_impaired)\.

Each event includes a start time that indicates the time at which the event occurred, and a duration that indicates how long I/O for the volume was disabled\. The end time is added to the event when I/O for the volume is enabled\.

Volume status events include one of the following descriptions:

Awaiting Action: Enable IO  
Volume data is potentially inconsistent\. I/O is disabled for the volume until you explicitly enable it\. The event description changes to IO Enabled after you explicitly enable I/O\.

IO Enabled  
I/O operations were explicitly enabled for this volume\.

IO Auto\-Enabled  
I/O operations were automatically enabled on this volume after an event occurred\. We recommend that you check for data inconsistencies before continuing to use the data\.

Normal  
For `io1` volumes only\. Volume performance is as expected\.

Degraded  
For `io1` volumes only\. Volume performance is below expectations\.

Severely Degraded  
For `io1` volumes only\. Volume performance is well below expectations\.

Stalled  
For `io1` volumes only\. Volume performance is severely impacted\.

You can view events for your volumes using the Amazon EC2 console, the API, or the command line interface\. 

**To view events for your volumes in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\. 

1. All instances and volumes that have events are listed\. You can filter by volume to view only volume status\. You can also filter on specific status types\.

1. Select a volume to view its specific event\.  
![\[Viewing volume events\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ViewVolEvents-gwt.png)

If you have a volume where I/O is disabled, see [Working with an Impaired Volume](#work_volumes_impaired)\. If you have a volume where I/O performance is below normal, this might be a temporary condition due to an action you have taken \(e\.g\., creating a snapshot of a volume during peak usage, running the volume on an instance that cannot support the I/O bandwidth required, accessing data on the volume for the first time, etc\.\)\.

**To view events for your volumes with the command line**

You can use one of the following commands to view event information for your Amazon EBS volumes\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volume\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volume-status.html) \(AWS CLI\)
+ [Get\-EC2VolumeStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VolumeStatus.html) \(AWS Tools for Windows PowerShell\)

## Working with an Impaired Volume<a name="work_volumes_impaired"></a>

This section discusses your options if a volume is impaired because the volume's data is potentially inconsistent\.

**Topics**
+ [Option 1: Perform a Consistency Check on the Volume Attached to its Instance](#work_volumes_impaired_option1)
+ [Option 2: Perform a Consistency Check on the Volume Using Another Instance](#work_volumes_impaired_option2)
+ [Option 3: Delete the Volume If You No Longer Need It](#work_volumes_impaired_option3)

### Option 1: Perform a Consistency Check on the Volume Attached to its Instance<a name="work_volumes_impaired_option1"></a>

The simplest option is to enable I/O and then perform a data consistency check on the volume while the volume is still attached to its Amazon EC2 instance\.

**To perform a consistency check on an attached volume**

1. Stop any applications from using the volume\.

1. Enable I/O on the volume\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Volumes**\.

   1. Select the volume on which to enable I/O operations\. 

   1. In the details pane, choose **Enable Volume IO**\.  
![\[Enable IO\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EnableIO_volumepage_gwt.png)

   1. In **Enable Volume IO**, choose **Yes, Enable**\.

1. Check the data on the volume\.

   1. Run the fsck command\.

   1. \(Optional\) Review any available application or system logs for relevant error messages\.

   1. If the volume has been impaired for more than 20 minutes you can contact support\. Choose **Troubleshoot**, and then on the **Troubleshoot Status Checks** dialog box, choose **Contact Support** to submit a support case\.

**To enable I/O for a volume with the command line**

You can use one of the following commands to view event information for your Amazon EBS volumes\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [enable\-volume\-io](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-volume-io.html) \(AWS CLI\)
+ [Enable\-EC2VolumeIO](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VolumeIO.html) \(AWS Tools for Windows PowerShell\)

### Option 2: Perform a Consistency Check on the Volume Using Another Instance<a name="work_volumes_impaired_option2"></a>

Use the following procedure to check the volume outside your production environment\.

**Important**  
This procedure may cause the loss of write I/Os that were suspended when volume I/O was disabled\.

**To perform a consistency check on a volume in isolation**

1. Stop any applications from using the volume\.

1. Detach the volume from the instance\.

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Volumes**\. 

   1. Select the volume to detach\.

   1. Choose **Actions**, **Force Detach Volume**\. You'll be prompted for confirmation\.

1. Enable I/O on the volume\.

   1. In the navigation pane, choose **Volumes**\. 

   1. Select the volume that you detached in the previous step\.

   1. In the details pane, choose **Enable Volume IO**\.   
![\[Enable IO\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EnableIO_volumepage_gwt.png)

   1. In the **Enable Volume IO** dialog box, choose **Yes, Enable**\.

1. Attach the volume to another instance\. For information, see [Launch Your Instance](LaunchingAndUsingInstances.md) and [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. Check the data on the volume\.

   1. Run the fsck command\.

   1. \(Optional\) Review any available application or system logs for relevant error messages\.

   1. If the volume has been impaired for more than 20 minutes, you can contact support\. Choose **Troubleshoot**, and then in the troubleshooting dialog box, choose **Contact Support** to submit a support case\.

**To enable I/O for a volume with the command line**

You can use one of the following commands to view event information for your Amazon EBS volumes\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [enable\-volume\-io](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-volume-io.html) \(AWS CLI\)
+ [Enable\-EC2VolumeIO](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VolumeIO.html) \(AWS Tools for Windows PowerShell\)

### Option 3: Delete the Volume If You No Longer Need It<a name="work_volumes_impaired_option3"></a>

If you want to remove the volume from your environment, simply delete it\. For information about deleting a volume, see [Deleting an Amazon EBS Volume](ebs-deleting-volume.md)\.

If you have a recent snapshot that backs up the data on the volume, you can create a new volume from the snapshot\. For information about creating a volume from a snapshot, see [Restoring an Amazon EBS Volume from a Snapshot](ebs-restoring-volume.md)\.

## Working with the AutoEnableIO Volume Attribute<a name="volumeIO"></a>

When Amazon EBS determines that a volume's data is potentially inconsistent, it disables I/O to the volume from any attached EC2 instances by default\. This causes the volume status check to fail, and creates a volume status event that indicates the cause of the failure\. If the consistency of a particular volume is not a concern, and you prefer that the volume be made available immediately if it's `impaired`, you can override the default behavior by configuring the volume to automatically enable I/O\. If you enable the `AutoEnableIO` volume attribute, I/O between the volume and the instance is automatically re\-enabled and the volume's status check will pass\. In addition, you'll see an event that lets you know that the volume was in a potentially inconsistent state, but that its I/O was automatically enabled\. When this event occurs, you should check the volume's consistency and replace it if necessary\. For more information, see [Monitoring Volume Events](#monitoring-vol-events)\.

This section explains how to view and modify the `AutoEnableIO` attribute of a volume using the Amazon EC2 console, the command line interface, or the API\.

**To view the AutoEnableIO attribute of a volume in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select the volume\.

1. In the lower pane, choose **Status Checks**\.

1. In the **Status Checks** tab, **Auto\-Enable IO** displays the current setting for your volume, either `Enabled` or `Disabled`\.  
![\[View Auto-Enable IO\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/AutoEnableSetting_statustab_gwt.png)

**To modify the AutoEnableIO attribute of a volume in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select the volume\.

1. At the top of the **Volumes** page, choose **Actions**\.

1. Choose **Change Auto\-Enable IO Setting**\.  
![\[Change Auto-Enable IO setting\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ChangeAutoEnable-gwt.png)

1. In the **Change Auto\-Enable IO Setting** dialog box, select the **Auto\-Enable Volume IO** option to automatically enable I/O for an impaired volume\. To disable the feature, clear the option\.  
![\[Modify Auto-Enable IO setting\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ModifyAutoIO-gwt.png)

1. Choose **Save**\.

Alternatively, instead of completing steps 4\-6 in the previous procedure, choose **Status Checks**, **Edit**\.

**To view or modify the AutoEnableIO attribute of a volume with the command line**

You can use one of the following commands to view the `AutoEnableIO` attribute of your Amazon EBS volumes\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volume\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volume-attribute.html) \(AWS CLI\)
+ [Get\-EC2VolumeAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VolumeAttribute.html) \(AWS Tools for Windows PowerShell\)

To modify the `AutoEnableIO` attribute of a volume, you can use one of the commands below\.
+ [modify\-volume\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-volume-attribute.html) \(AWS CLI\)
+ [Edit\-EC2VolumeAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VolumeAttribute.html) \(AWS Tools for Windows PowerShell\)
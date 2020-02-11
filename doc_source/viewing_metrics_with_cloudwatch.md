# List the Available CloudWatch Metrics for Your Instances<a name="viewing_metrics_with_cloudwatch"></a>

Amazon EC2 sends metrics to Amazon CloudWatch\. You can use the AWS Management Console, the AWS CLI, or an API to list the metrics that Amazon EC2 sends to CloudWatch\. By default, each data point covers the 5 minutes that follow the start time of activity for the instance\. If you've enabled detailed monitoring, each data point covers the next minute of activity from the start time\.

For information about getting the statistics for these metrics, see [Get Statistics for Metrics for Your Instances](monitoring_get_statistics.md)\.

**Topics**
+ [Instance Metrics](#ec2-cloudwatch-metrics)
+ [CPU Credit Metrics](#cpu-credit-metrics)
+ [Amazon EBS Metrics for Nitro\-based Instances](#ebs-metrics-nitro)
+ [Status Check Metrics](#status-check-metrics)
+ [Amazon EC2 Metric Dimensions](#ec2-cloudwatch-dimensions)
+ [Listing Metrics Using the Console](#list-ec2-metrics-console)
+ [Listing Metrics Using the AWS CLI](#list-ec2-metrics-cli)

## Instance Metrics<a name="ec2-cloudwatch-metrics"></a>

The `AWS/EC2` namespace includes the following instance metrics\.


| Metric | Description | 
| --- | --- | 
| `CPUUtilization` |  The percentage of allocated EC2 compute units that are currently in use on the instance\. This metric identifies the processing power required to run an application on a selected instance\. Depending on the instance type, tools in your operating system can show a lower percentage than CloudWatch when the instance is not allocated a full processor core\. Units: Percent  | 
| `DiskReadOps` |  Completed read operations from all instance store volumes available to the instance in a specified period of time\. To calculate the average I/O operations per second \(IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. If there are no instance store volumes, either the value is 0 or the metric is not reported\. Units: Count  | 
| `DiskWriteOps` |  Completed write operations to all instance store volumes available to the instance in a specified period of time\. To calculate the average I/O operations per second \(IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. If there are no instance store volumes, either the value is 0 or the metric is not reported\. Units: Count  | 
| `DiskReadBytes` |  Bytes read from all instance store volumes available to the instance\. This metric is used to determine the volume of the data the application reads from the hard disk of the instance\. This can be used to determine the speed of the application\. The number reported is the number of bytes received during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. If there are no instance store volumes, either the value is 0 or the metric is not reported\. Units: Bytes  | 
| `DiskWriteBytes` |  Bytes written to all instance store volumes available to the instance\. This metric is used to determine the volume of the data the application writes onto the hard disk of the instance\. This can be used to determine the speed of the application\. The number reported is the number of bytes received during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. If there are no instance store volumes, either the value is 0 or the metric is not reported\.  Units: Bytes  | 
| `NetworkIn` |  The number of bytes received on all network interfaces by the instance\. This metric identifies the volume of incoming network traffic to a single instance\. The number reported is the number of bytes received during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Units: Bytes  | 
| `NetworkOut` |  The number of bytes sent out on all network interfaces by the instance\. This metric identifies the volume of outgoing network traffic from a single instance\. The number reported is the number of bytes sent during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Units: Bytes  | 
| `NetworkPacketsIn` |  The number of packets received on all network interfaces by the instance\. This metric identifies the volume of incoming traffic in terms of the number of packets on a single instance\. This metric is available for basic monitoring only\. Units: Count Statistics: Minimum, Maximum, Average  | 
| `NetworkPacketsOut` |  The number of packets sent out on all network interfaces by the instance\. This metric identifies the volume of outgoing traffic in terms of the number of packets on a single instance\. This metric is available for basic monitoring only\. Units: Count Statistics: Minimum, Maximum, Average  | 
| `MetadataNoToken` |  The number of times the instance metadata service was succesfully accessed using a method that does not use a token\. This metric is used to determine if there are any processes accessing instance metadata that are using Instance Metadata Service Version 1, which does not use a token\. If all requests use token\-backed sessions, i\.e\., Instance Metadata Service Version 2, the value is 0\. For more information, see [Transitioning to Using Instance Metadata Service Version 2](configuring-instance-metadata-service.md#instance-metadata-transition-to-version-2)\. Units: Count  | 

## CPU Credit Metrics<a name="cpu-credit-metrics"></a>

The `AWS/EC2` namespace includes the following CPU credit metrics for your [burstable performance instances](burstable-performance-instances.md)\.


| Metric | Description | 
| --- | --- | 
| CPUCreditUsage |  The number of CPU credits spent by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100% utilization for one minute or an equivalent combination of vCPUs, utilization, and time \(for example, one vCPU running at 50% utilization for two minutes or two vCPUs running at 25% utilization for two minutes\)\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\. Units: Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |  The number of earned CPU credits that an instance has accrued since it was launched or started\. For T2 Standard, the `CPUCreditBalance` also includes the number of launch credits that have been accrued\. Credits are accrued in the credit balance after they are earned, and removed from the credit balance when they are spent\. The credit balance has a maximum limit, determined by the instance size\. After the limit is reached, any new credits that are earned are discarded\. For T2 Standard, launch credits do not count towards the limit\. The credits in the `CPUCreditBalance` are available for the instance to spend to burst beyond its baseline CPU utilization\. When an instance is running, credits in the `CPUCreditBalance` do not expire\. When a T3 or T3a instance stops, the `CPUCreditBalance` value persists for seven days\. Thereafter, all accrued credits are lost\. When a T2 instance stops, the `CPUCreditBalance` value does not persist, and all accrued credits are lost\. CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)  | 
| CPUSurplusCreditBalance  |  The number of surplus credits that have been spent by an `unlimited` instance when its `CPUCreditBalance` value is zero\. The `CPUSurplusCreditBalance` value is paid down by earned CPU credits\. If the number of surplus credits exceeds the maximum number of credits that the instance can earn in a 24\-hour period, the spent surplus credits above the maximum incur an additional charge\. CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)   | 
| CPUSurplusCreditsCharged |  The number of spent surplus credits that are not paid down by earned CPU credits, and which thus incur an additional charge\. Spent surplus credits are charged when any of the following occurs:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html) CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)  | 

## Amazon EBS Metrics for Nitro\-based Instances<a name="ebs-metrics-nitro"></a>

The `AWS/EC2` namespace includes the following Amazon EBS metrics for the Nitro\-based instances that are not bare metal instances\. For the list of Nitro\-based instance types, see [Nitro\-based Instances](instance-types.md#ec2-nitro-instances)\.

Metric values for Nitro\-based instances will always be integers \(whole numbers\), whereas values for Xen\-based instances support decimals\. Therefore, low instance CPU utilization on Nitro\-based instances may appear to be rounded down to 0\.


| Metric | Description | 
| --- | --- | 
|  `EBSReadOps` |  Completed read operations from all Amazon EBS volumes attached to the instance in a specified period of time\. To calculate the average read I/O operations per second \(Read IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to calculate the Read IOPS\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Unit: Count  | 
|  `EBSWriteOps`  |  Completed write operations to all EBS volumes attached to the instance in a specified period of time\. To calculate the average write I/O operations per second \(Write IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to calculate the Write IOPS\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Unit: Count  | 
|  `EBSReadBytes`  |  Bytes read from all EBS volumes attached to the instance in a specified period of time\.  The number reported is the number of bytes read during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Read Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Unit: Bytes  | 
|  `EBSWriteBytes`  |  Bytes written to all EBS volumes attached to the instance in a specified period of time\. The number reported is the number of bytes written during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Write Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Unit: Bytes  | 
|  `EBSIOBalance%`  |  Available only for the smaller instance sizes\. Provides information about the percentage of I/O credits remaining in the burst bucket\. This metric is available for basic monitoring only\. The `Sum` statistic is not applicable to this metric\. Unit: Percent  | 
|  `EBSByteBalance%`  |  Available only for the smaller instance sizes\. Provides information about the percentage of throughput credits remaining in the burst bucket\. This metric is available for basic monitoring only\. The `Sum` statistic is not applicable to this metric\. Unit: Percent  | 

For information about the metrics provided for your EBS volumes, see [Amazon EBS Metrics](using_cloudwatch_ebs.md#ebs-metrics)\. For information about the metrics provided for your Spot fleets, see [CloudWatch Metrics for Spot Fleet](spot-fleet-cloudwatch-metrics.md)\.

## Status Check Metrics<a name="status-check-metrics"></a>

The `AWS/EC2` namespace includes the following status check metrics\. By default, status check metrics are available at a 1\-minute frequency at no charge\. For a newly\-launched instance, status check metric data is only available after the instance has completed the initialization state \(within a few minutes of the instance entering the running state\)\. For more information about EC2 status checks, see [Status Checks For Your Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html)\.


| Metric | Description | 
| --- | --- | 
| `StatusCheckFailed` |  Reports whether the instance has passed both the instance status check and the system status check in the last minute\. This metric can be either 0 \(passed\) or 1 \(failed\)\. By default, this metric is available at a 1\-minute frequency at no charge\. Units: Count  | 
| `StatusCheckFailed_Instance` |  Reports whether the instance has passed the instance status check in the last minute\. This metric can be either 0 \(passed\) or 1 \(failed\)\. By default, this metric is available at a 1\-minute frequency at no charge\. Units: Count  | 
| `StatusCheckFailed_System` |  Reports whether the instance has passed the system status check in the last minute\. This metric can be either 0 \(passed\) or 1 \(failed\)\. By default, this metric is available at a 1\-minute frequency at no charge\. Units: Count  | 

## Amazon EC2 Metric Dimensions<a name="ec2-cloudwatch-dimensions"></a>

You can use the following dimensions to refine the metrics returned for your instances\.


|  Dimension  |  Description  | 
| --- | --- | 
|  AutoScalingGroupName  |  This dimension filters the data you request for all instances in a specified capacity group\. An *Auto Scaling group* is a collection of instances you define if you're using Auto Scaling\. This dimension is available only for Amazon EC2 metrics when the instances are in such an Auto Scaling group\. Available for instances with Detailed or Basic Monitoring enabled\.  | 
|  ImageId  |  This dimension filters the data you request for all instances running this Amazon EC2 Amazon Machine Image \(AMI\)\. Available for instances with Detailed Monitoring enabled\.  | 
|  InstanceId  |  This dimension filters the data you request for the identified instance only\. This helps you pinpoint an exact instance from which to monitor data\.  | 
|  InstanceType  |  This dimension filters the data you request for all instances running with this specified instance type\. This helps you categorize your data by the type of instance running\. For example, you might compare data from an m1\.small instance and an m1\.large instance to determine which has the better business value for your application\. Available for instances with Detailed Monitoring enabled\.  | 

## Listing Metrics Using the Console<a name="list-ec2-metrics-console"></a>

Metrics are grouped first by namespace, and then by the various dimension combinations within each namespace\. For example, you can view all metrics provided by Amazon EC2, or metrics grouped by instance ID, instance type, image \(AMI\) ID, or Auto Scaling group\.

**To view available metrics by category \(console\)**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Choose the **EC2** metric namespace\.  
![\[Choose the EC2 metrics namespace\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_categories.png)

1. Select a metric dimension \(for example, **Per\-Instance Metrics**\)\.  
![\[View the metric dimensions for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_metric_category.png)

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\. To filter by resource, choose the resource ID and then choose **Add to search**\. To filter by metric, choose the metric name and then choose **Add to search**\.  
![\[View the metrics for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_metrics.png)

## Listing Metrics Using the AWS CLI<a name="list-ec2-metrics-cli"></a>

Use the [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command to list the CloudWatch metrics for your instances\.

**To list all the available metrics for Amazon EC2 \(AWS CLI\)**  
The following example specifies the `AWS/EC2` namespace to view all the metrics for Amazon EC2\.

```
aws cloudwatch list-metrics --namespace AWS/EC2
```

The following is example output:

```
{
  "Metrics": [
    {
        "Namespace": "AWS/EC2",
        "Dimensions": [
            {
                "Name": "InstanceId",
                "Value": "i-1234567890abcdef0"
            }
        ],
        "MetricName": "NetworkOut"
    },
    {
        "Namespace": "AWS/EC2",
        "Dimensions": [
            {
                "Name": "InstanceId",
                "Value": "i-1234567890abcdef0"
            }
        ],
        "MetricName": "CPUUtilization"
    },
    {
        "Namespace": "AWS/EC2",
        "Dimensions": [
            {
                "Name": "InstanceId",
                "Value": "i-1234567890abcdef0"
            }
        ],
        "MetricName": "NetworkIn"
    },
    ...
  ]
}
```

**To list all the available metrics for an instance \(AWS CLI\)**  
The following example specifies the `AWS/EC2` namespace and the `InstanceId` dimension to view the results for the specified instance only\.

```
aws cloudwatch list-metrics --namespace AWS/EC2 --dimensions Name=InstanceId,Value=i-1234567890abcdef0
```

**To list a metric across all instances \(AWS CLI\)**  
The following example specifies the `AWS/EC2` namespace and a metric name to view the results for the specified metric only\.

```
aws cloudwatch list-metrics --namespace AWS/EC2 --metric-name CPUUtilization
```
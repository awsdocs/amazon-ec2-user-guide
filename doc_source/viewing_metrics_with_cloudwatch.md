# List the Available CloudWatch Metrics for Your Instances<a name="viewing_metrics_with_cloudwatch"></a>

Amazon EC2 sends metrics to Amazon CloudWatch\. You can use the AWS Management Console, the AWS CLI, or an API to list the metrics that Amazon EC2 sends to CloudWatch\. By default, each data point covers the previous 5 minutes of activity for the instance\. If you've enabled detailed monitoring, each data point covers the previous 1 minute of activity\.

For information about getting the statistics for these metrics, see [Get Statistics for Metrics for Your Instances](monitoring_get_statistics.md)\.

## Instance Metrics<a name="ec2-cloudwatch-metrics"></a>

The `AWS/EC2` namespace includes the following CPU credit metrics for your T2 instances\.


| Metric | Description | 
| --- | --- | 
| CPUCreditUsage |  \[T2 instances\] The number of CPU credits spent by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100% utilization for one minute or an equivalent combination of vCPUs, utilization, and time \(for example, one vCPU running at 50% utilization for two minutes or two vCPUs running at 25% utilization for two minutes\)\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\. Units: Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |  \[T2 instances\] The number of earned CPU credits that an instance has accrued since it was launched or started\. For T2 Standard, the `CPUCreditBalance` also includes the number of launch credits that have been accrued\. Credits are accrued in the credit balance after they are earned, and removed from the credit balance when they are spent\. The credit balance has a maximum limit, determined by the instance size\. Once the limit is reached, any new credits that are earned are discarded\. For T2 Standard, launch credits do not count towards the limit\. The credits in the `CPUCreditBalance` are available for the instance to spend to burst beyond its baseline CPU utilization\. When an instance is running, credits in the `CPUCreditBalance` do not expire\. When the instance stops, the `CPUCreditBalance` does not persist, and all accrued credits are lost\. CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)  | 
| CPUSurplusCreditBalance  |  \[T2 Unlimited instances\] The number of surplus credits that have been spent by a T2 Unlimited instance when its `CPUCreditBalance` is zero\. The `CPUSurplusCreditBalance` is paid down by earned CPU credits\. If the number of surplus credits exceeds the maximum number of credits the instance can earn in a 24\-hour period, the spent surplus credits above the maximum incur an additional charge\. Units: Credits \(vCPU\-minutes\)   | 
| CPUSurplusCreditsCharged |  \[T2 Unlimited instances\] The number of spent surplus credits that are not paid down by earned CPU credits, and thus incur an additional charge\. Spent surplus credits are charged when any of the following occurs:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html) Units: Credits \(vCPU\-minutes\)   | 

The `AWS/EC2` namespace includes the following instance metrics\.


| Metric | Description | 
| --- | --- | 
| `CPUUtilization` |  The percentage of allocated EC2 compute units that are currently in use on the instance\. This metric identifies the processing power required to run an application upon a selected instance\. To use the percentiles statistic, you must enable detailed monitoring\. Depending on the instance type, tools in your operating system can show a lower percentage than CloudWatch when the instance is not allocated a full processor core\. Units: Percent  | 
| `DiskReadOps` |  Completed read operations from all instance store volumes available to the instance in a specified period of time\. To calculate the average I/O operations per second \(IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. Units: Count  | 
| `DiskWriteOps` |  Completed write operations to all instance store volumes available to the instance in a specified period of time\. To calculate the average I/O operations per second \(IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. Units: Count  | 
| `DiskReadBytes` |  Bytes read from all instance store volumes available to the instance\. This metric is used to determine the volume of the data the application reads from the hard disk of the instance\. This can be used to determine the speed of the application\. The number reported is the number of bytes received during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Units: Bytes  | 
| `DiskWriteBytes` |  Bytes written to all instance store volumes available to the instance\. This metric is used to determine the volume of the data the application writes onto the hard disk of the instance\. This can be used to determine the speed of the application\. The number reported is the number of bytes received during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Units: Bytes  | 
| `NetworkIn` |  The number of bytes received on all network interfaces by the instance\. This metric identifies the volume of incoming network traffic to a single instance\. The number reported is the number of bytes received during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Units: Bytes  | 
| `NetworkOut` |  The number of bytes sent out on all network interfaces by the instance\. This metric identifies the volume of outgoing network traffic from a single instance\. The number reported is the number of bytes sent during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Units: Bytes  | 
| `NetworkPacketsIn` |  The number of packets received on all network interfaces by the instance\. This metric identifies the volume of incoming traffic in terms of the number of packets on a single instance\. This metric is available for basic monitoring only\. Units: Count Statistics: Minimum, Maximum, Average  | 
| `NetworkPacketsOut` |  The number of packets sent out on all network interfaces by the instance\. This metric identifies the volume of outgoing traffic in terms of the number of packets on a single instance\. This metric is available for basic monitoring only\. Units: Count Statistics: Minimum, Maximum, Average  | 

The `AWS/EC2` namespace includes the following status checks metrics\. By default, status check metrics are available at a 1\-minute frequency at no charge\. For a newly\-launched instance, status check metric data is only available after the instance has completed the initialization state \(within a few minutes of the instance entering the running state\)\. For more information about EC2 status checks, see [Status Checks For Your Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html)\.


| Metric | Description | 
| --- | --- | 
| `StatusCheckFailed` |  Reports whether the instance has passed both the instance status check and the system status check in the last minute\. This metric can be either 0 \(passed\) or 1 \(failed\)\. By default, this metric is available at a 1\-minute frequency at no charge\. Units: Count  | 
| `StatusCheckFailed_Instance` |  Reports whether the instance has passed the instance status check in the last minute\. This metric can be either 0 \(passed\) or 1 \(failed\)\. By default, this metric is available at a 1\-minute frequency at no charge\. Units: Count  | 
| `StatusCheckFailed_System` |  Reports whether the instance has passed the system status check in the last minute\. This metric can be either 0 \(passed\) or 1 \(failed\)\. By default, this metric is available at a 1\-minute frequency at no charge\. Units: Count  | 

The `AWS/EC2` namespace includes the following Amazon EBS metrics for your C5 and M5 instances\.


| Metric | Description | 
| --- | --- | 
|  `EBSReadOps` |  Completed read operations from all Amazon EBS volumes attached to the instance in a specified period of time\. To calculate the average read I/O operations per second \(Read IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to calculate the Read IOPS\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Unit: Count  | 
|  `EBSWriteOps`  |  Completed write operations to all EBS volumes attached to the instance in a specified period of time\. To calculate the average write I/O operations per second \(Write IOPS\) for the period, divide the total operations in the period by the number of seconds in that period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to calculate the Write IOPS\. If you have detailed \(one\-minute\) monitoring, divide it by 60\. Unit: Count  | 
|  `EBSReadBytes`  |  Bytes read from all EBS volumes attached to the instance in a specified period of time\.  The number reported is the number of bytes read during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Read Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Unit: Bytes  | 
|  `EBSWriteBytes`  |  Bytes written to all EBS volumes attached to the instance in a specified period of time\. The number reported is the number of bytes written during the period\. If you are using basic \(five\-minute\) monitoring, you can divide this number by 300 to find Write Bytes/second\. If you have detailed \(one\-minute\) monitoring, divide it by 60\.  Unit: Bytes  | 
|  `EBSIOBalance%`  |  Available only for the smaller C5 and M5 instance sizes\. Provides information about the percentage of I/O credits remaining in the burst bucket\. This metric is available for basic monitoring only\. The `Sum` statistic is not applicable to this metric\. Unit: Percent  | 
|  `EBSByteBalance%`  |  Available only for the smaller C5 and M5 instance sizes\. Provides information about the percentage of throughput credits remaining in the burst bucket\. This metric is available for basic monitoring only\. The `Sum` statistic is not applicable to this metric\. Unit: Percent  | 

For information about the metrics provided for your EBS volumes, see [Amazon EBS Metrics](monitoring-volume-status.md#ebs-metrics)\. For information about the metrics provided for your Spot fleets, see [CloudWatch Metrics for Spot Fleet](spot-fleet-cloudwatch-metrics.md)\.

## Amazon EC2 Dimensions<a name="ec2-cloudwatch-dimensions"></a>

 You can use the following dimensions to refine the metrics returned for your instances\.


|  Dimension  |  Description  | 
| --- | --- | 
|  AutoScalingGroupName  |  This dimension filters the data you request for all instances in a specified capacity group\. An *Auto Scaling group* is a collection of instances you define if you're using Auto Scaling\. This dimension is available only for Amazon EC2 metrics when the instances are in such an Auto Scaling group\. Available for instances with Detailed or Basic Monitoring enabled\.  | 
|  ImageId  |  This dimension filters the data you request for all instances running this Amazon EC2 Amazon Machine Image \(AMI\)\. Available for instances with Detailed Monitoring enabled\.  | 
|  InstanceId  |  This dimension filters the data you request for the identified instance only\. This helps you pinpoint an exact instance from which to monitor data\.  | 
|  InstanceType  |  This dimension filters the data you request for all instances running with this specified instance type\. This helps you categorize your data by the type of instance running\. For example, you might compare data from an m1\.small instance and an m1\.large instance to determine which has the better business value for your application\. Available for instances with Detailed Monitoring enabled\.  | 

## Listing Metrics Using the Console<a name="list-ec2-metrics-console"></a>

Metrics are grouped first by namespace, and then by the various dimension combinations within each namespace\. For example, you can view all metrics provided by Amazon EC2, or metrics grouped by instance ID, instance type, image \(AMI\) ID, or Auto Scaling group\.

**To view available metrics by category**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the EC2 metric namespace\.  
![\[Select the EC2 metrics namespace\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_categories.png)

1. Select a metric dimension \(for example, Per\-Instance Metrics\)\.  
![\[View the metric dimensions for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_metric_category.png)

1. To sort the metrics, use the column heading\. To graph a metric, select the check box next to the metric\. To filter by resource, choose the resource ID and then choose **Add to search**\. To filter by metric, choose the metric name and then choose **Add to search**\.  
![\[View the metrics for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_metrics.png)

## Listing Metrics Using the AWS CLI<a name="list-ec2-metrics-cli"></a>

Use the [list\-metrics](http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command to list the CloudWatch metrics for your instances\.

**To list all the available metrics for Amazon EC2**  
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

**To list all the available metrics for an instance**  
The following example specifies the `AWS/EC2` namespace and the `InstanceId` dimension to view the results for the specified instance only\.

```
aws cloudwatch list-metrics --namespace AWS/EC2 --dimensions Name=InstanceId,Value=i-1234567890abcdef0
```

**To list a metric across all instances**  
The following example specifies the `AWS/EC2` namespace and a metric name to view the results for the specified metric only\.

```
aws cloudwatch list-metrics --namespace AWS/EC2 --metric-name CPUUtilization
```
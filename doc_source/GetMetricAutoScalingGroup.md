# Aggregate statistics by Auto Scaling group<a name="GetMetricAutoScalingGroup"></a>

You can aggregate statistics for the EC2 instances in an Auto Scaling group\. Note that Amazon CloudWatch cannot aggregate data across regions\. Metrics are completely separate between regions\.

This example shows you how to retrieve the total bytes written to disk for one Auto Scaling group\. The total is computed for one\-minute periods for a 24\-hour interval across all EC2 instances in the specified Auto Scaling group\.

**To display DiskWriteBytes for the instances in an Auto Scaling group \(console\)**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Choose the **EC2** namespace and then choose **By Auto Scaling Group**\.

1. Choose the row for the **DiskWriteBytes** metric and the specific Auto Scaling group, which displays a graph for the metric for the instances in the Auto Scaling group\. To name the graph, choose the pencil icon\. To change the time range, select one of the predefined values or choose **custom**\.

1. To change the statistic or the period for the metric, choose the **Graphed metrics** tab\. Choose the column heading or an individual value, and then choose a different value\.

**To display DiskWriteBytes for the instances in an Auto Scaling group \(AWS CLI\)**  
Use the [get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command as follows\.

```
aws cloudwatch get-metric-statistics --namespace AWS/EC2 --metric-name DiskWriteBytes --period 360 \
--statistics "Sum" "SampleCount" --dimensions Name=AutoScalingGroupName,Value=my-asg --start-time 2016-10-16T23:18:00 --end-time 2016-10-18T23:18:00
```

The following is example output:

```
{
    "Datapoints": [
        {
            "SampleCount": 18.0, 
            "Timestamp": "2016-10-19T21:36:00Z", 
            "Sum": 0.0, 
            "Unit": "Bytes"
        }, 
        {
            "SampleCount": 5.0, 
            "Timestamp": "2016-10-19T21:42:00Z", 
            "Sum": 0.0, 
            "Unit": "Bytes"
        }
    ], 
    "Label": "DiskWriteBytes"
}
```
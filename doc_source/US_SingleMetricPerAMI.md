# Aggregate Statistics by AMI<a name="US_SingleMetricPerAMI"></a>

You can aggregate statistics for your instances that have detailed monitoring enabled\. Instances that use basic monitoring are not included\. Note that Amazon CloudWatch cannot aggregate data across regions\. Metrics are completely separate between regions\.

Before you can get statistics aggregated across instances, you must enable detailed monitoring \(at an additional charge\), which provides data in 1\-minute periods\. For more information, see [Enable or Disable Detailed Monitoring for Your Instances](using-cloudwatch-new.md)\.

This example shows you how to determine average CPU utilization for all instances that use a specific Amazon Machine Image \(AMI\)\. The average is over 60\-second time intervals for a one\-day period\.

**To display the average CPU utilization by AMI using the console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the **EC2** namespace and then select **By Image \(AMI\) Id**\.

1. Select the row for the **CPUUtilization** metric and the specific AMI, which displays a graph for the metric for the specified AMI\. To name the graph, choose the pencil icon\. To change the time range, select one of the predefined values or choose **custom**\.

1. To change the statistic or the period for the metric, choose the **Graphed metrics** tab\. Choose the column heading or an individual value, and then choose a different value\.

**To get the average CPU utilization for an image ID**  
Use the [get\-metric\-statistics](http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command as follows\.

```
aws cloudwatch get-metric-statistics --namespace AWS/EC2 --metric-name CPUUtilization  --period 3600 \
--statistics Average --dimensions Name=ImageId,Value=ami-3c47a355 --start-time 2016-10-10T00:00:00 --end-time 2016-10-11T00:00:00
```

The following is example output\. Each value represents an average CPU utilization percentage for the EC2 instances running the specified AMI\.

```
{
    "Datapoints": [
        {
            "Timestamp": "2016-10-10T07:00:00Z", 
            "Average": 0.041000000000000009, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2016-10-10T14:00:00Z", 
            "Average": 0.079579831932773085, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2016-10-10T06:00:00Z", 
            "Average": 0.036000000000000011, 
            "Unit": "Percent"
        }, 
        ...
    ], 
    "Label": "CPUUtilization"
}
```
# Aggregate Statistics Across Instances<a name="GetSingleMetricAllDimensions"></a>

Aggregate statistics are available for the instances that have detailed monitoring enabled\. Instances that use basic monitoring are not included in the aggregates\. In addition, Amazon CloudWatch does not aggregate data across regions\. Therefore, metrics are completely separate between regions\. Before you can get statistics aggregated across instances, you must enable detailed monitoring \(at an additional charge\), which provides data in 1\-minute periods\.

This example shows you how to use detailed monitoring to get the average CPU usage for your EC2 instances\. Because no dimension is specified, CloudWatch returns statistics for all dimensions in the `AWS/EC2` namespace\.

**Important**  
This technique for retrieving all dimensions across an AWS namespace does not work for custom namespaces that you publish to Amazon CloudWatch\. With custom namespaces, you must specify the complete set of dimensions that are associated with any given data point to retrieve statistics that include the data point\. 

**To display average CPU utilization across your instances**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the **EC2** namespace and then select **Across All Instances**\.

1. Select the row that contains **CPUUtilization**, which displays a graph for the metric for all your EC2 instances\. To name the graph, choose the pencil icon\. To change the time range, select one of the predefined values or choose **custom**\.  
![\[Metrics aggregated across your EC2 instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_aggregated_instances.png)

1. To change the statistic or the period for the metric, choose the **Graphed metrics** tab\. Choose the column heading or an individual value, and then choose a different value\.

**To get average CPU utilization across your instances**  
Use the [get\-metric\-statistics](http://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command as follows to get the average of the **CPUUtilization** metric across your instances\.

```
aws cloudwatch get-metric-statistics --namespace AWS/EC2 --metric-name CPUUtilization \ 
--period 3600  --statistics "Average" "SampleCount" \ 
--start-time 2016-10-11T23:18:00 --end-time 2016-10-12T23:18:00
```

The following is example output:

```
{
    "Datapoints": [
        {
            "SampleCount": 238.0, 
            "Timestamp": "2016-10-12T07:18:00Z", 
            "Average": 0.038235294117647062, 
            "Unit": "Percent"
        }, 
        {
            "SampleCount": 240.0, 
            "Timestamp": "2016-10-12T09:18:00Z", 
            "Average": 0.16670833333333332, 
            "Unit": "Percent"
        }, 
        {
            "SampleCount": 238.0, 
            "Timestamp": "2016-10-11T23:18:00Z", 
            "Average": 0.041596638655462197, 
            "Unit": "Percent"
        },
        ...
    ], 
    "Label": "CPUUtilization"
}
```
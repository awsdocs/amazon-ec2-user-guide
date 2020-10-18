# Get statistics for a specific instance<a name="US_SingleMetricPerInstance"></a>

The following examples show you how to use the AWS Management Console or the AWS CLI to determine the maximum CPU utilization of a specific EC2 instance\.

**Requirements**
+ You must have the ID of the instance\. You can get the instance ID using the AWS Management Console or the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.
+ By default, basic monitoring is enabled, but you can enable detailed monitoring\. For more information, see [Enable or turn off detailed monitoring for your instances](using-cloudwatch-new.md)\.

**To display the CPU utilization for a specific instance \(console\)**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Choose the **EC2** metric namespace\.  
![\[Choose the EC2 metrics namespace\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_categories.png)

1. Choose the **Per\-Instance Metrics** dimension\.  
![\[View the metric dimensions for Amazon EC2\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_view_metric_category.png)

1. In the search field, enter **CPUUtilization** and press Enter\. Choose the row for the specific instance, which displays a graph for the **CPUUtilization** metric for the instance\. To name the graph, choose the pencil icon\. To change the time range, select one of the predefined values or choose **custom**\.  
![\[Graph a single metric\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_statistics_ec2_instance.png)

1. To change the statistic or the period for the metric, choose the **Graphed metrics** tab\. Choose the column heading or an individual value, and then choose a different value\.  
![\[Change the statistic or period for a metric\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/metric_statistics_ec2_instance_statistic_period.png)

**To get the CPU utilization for a specific instance \(AWS CLI\)**  
Use the following [get\-metric\-statistics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/get-metric-statistics.html) command to get the **CPUUtilization** metric for the specified instance, using the specified period and time interval:

```
aws cloudwatch get-metric-statistics --namespace AWS/EC2 --metric-name CPUUtilization  --period 3600 \
--statistics Maximum --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
--start-time 2016-10-18T23:18:00 --end-time 2016-10-19T23:18:00
```

The following is example output\. Each value represents the maximum CPU utilization percentage for a single EC2 instance\.

```
{
    "Datapoints": [
        {
            "Timestamp": "2016-10-19T00:18:00Z", 
            "Maximum": 0.33000000000000002, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2016-10-19T03:18:00Z", 
            "Maximum": 99.670000000000002, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2016-10-19T07:18:00Z", 
            "Maximum": 0.34000000000000002, 
            "Unit": "Percent"
        }, 
        {
            "Timestamp": "2016-10-19T12:18:00Z", 
            "Maximum": 0.34000000000000002, 
            "Unit": "Percent"
        }, 
        ...
    ], 
    "Label": "CPUUtilization"
}
```
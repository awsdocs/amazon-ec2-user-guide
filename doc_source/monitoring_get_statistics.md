# Get Statistics for Metrics for Your Instances<a name="monitoring_get_statistics"></a>

You can get statistics for the CloudWatch metrics for your instances\.

**Topics**
+ [Statistics Overview](#overview-statistics)
+ [Get Statistics for a Specific Instance](US_SingleMetricPerInstance.md)
+ [Aggregate Statistics Across Instances](GetSingleMetricAllDimensions.md)
+ [Aggregate Statistics by Auto Scaling Group](GetMetricAutoScalingGroup.md)
+ [Aggregate Statistics by AMI](US_SingleMetricPerAMI.md)

## Statistics Overview<a name="overview-statistics"></a>

*Statistics* are metric data aggregations over specified periods of time\. CloudWatch provides statistics based on the metric data points provided by your custom data or provided by other services in AWS to CloudWatch\. Aggregations are made using the namespace, metric name, dimensions, and the data point unit of measure, within the time period you specify\. The following table describes the available statistics\.


| Statistic | Description | 
| --- | --- | 
| Minimum |  The lowest value observed during the specified period\. You can use this value to determine low volumes of activity for your application\.   | 
| Maximum |  The highest value observed during the specified period\. You can use this value to determine high volumes of activity for your application\.   | 
| Sum |  All values submitted for the matching metric added together\. This statistic can be useful for determining the total volume of a metric\.   | 
| Average |  The value of `Sum` / `SampleCount` during the specified period\. By comparing this statistic with the `Minimum` and `Maximum`, you can determine the full scope of a metric and how close the average use is to the `Minimum` and `Maximum`\. This comparison helps you to know when to increase or decrease your resources as needed\.   | 
| SampleCount |  The count \(number\) of data points used for the statistical calculation\.  | 
| pNN\.NN |  The value of the specified percentile\. You can specify any percentile, using up to two decimal places \(for example, p95\.45\)\.  | 
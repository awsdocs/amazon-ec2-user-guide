# Monitoring Your Instances Using CloudWatch<a name="using-cloudwatch"></a>

You can monitor your instances using Amazon CloudWatch, which collects and processes raw data from Amazon EC2 into readable, near real\-time metrics\. These statistics are recorded for a period of 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\.

By default, Amazon EC2 sends metric data to CloudWatch in 5\-minute periods\. To send metric data for your instance to CloudWatch in 1\-minute periods, you can enable detailed monitoring on the instance\. For more information, see [Enable or Disable Detailed Monitoring for Your Instances](using-cloudwatch-new.md)\.

The Amazon EC2 console displays a series of graphs based on the raw data from Amazon CloudWatch\. Depending on your needs, you might prefer to get data for your instances from Amazon CloudWatch instead of the graphs in the console\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)\.


+ [Enable or Disable Detailed Monitoring for Your Instances](using-cloudwatch-new.md)
+ [List the Available CloudWatch Metrics for Your Instances](viewing_metrics_with_cloudwatch.md)
+ [Get Statistics for Metrics for Your Instances](monitoring_get_statistics.md)
+ [Graph Metrics for Your Instances](graphs-in-the-aws-management-console.md)
+ [Create a CloudWatch Alarm for an Instance](using-cloudwatch-createalarm.md)
+ [Create Alarms That Stop, Terminate, Reboot, or Recover an Instance](UsingAlarmActions.md)
# Monitor Amazon EC2<a name="monitoring_ec2"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of your Amazon Elastic Compute Cloud \(Amazon EC2\) instances and your AWS solutions\. You should collect monitoring data from all of the parts in your AWS solutions so that you can more easily debug a multi\-point failure if one occurs\. Before you start monitoring Amazon EC2, however, you should create a monitoring plan that should include:
+ What are your goals for monitoring?
+ What resources will you monitor?
+ How often will you monitor these resources?
+ What monitoring tools will you use?
+ Who will perform the monitoring tasks?
+ Who should be notified when something goes wrong? 

After you have defined your monitoring goals and have created your monitoring plan, the next step is to establish a baseline for normal Amazon EC2 performance in your environment\. You should measure Amazon EC2 performance at various times and under different load conditions\. As you monitor Amazon EC2, you should store a history of monitoring data that you've collected\. You can compare current Amazon EC2 performance to this historical data to help you to identify normal performance patterns and performance anomalies, and devise methods to address them\. For example, you can monitor CPU utilization, disk I/O, and network utilization for your EC2 instances\. When performance falls outside your established baseline, you might need to reconfigure or optimize the instance to reduce CPU utilization, improve disk I/O, or reduce network traffic\.

To establish a baseline you should, at a minimum, monitor the following items:


| Item to monitor | Amazon EC2 metric | Monitoring agent/CloudWatch Logs | 
| --- | --- | --- | 
|  CPU utilization  |  [CPUUtilization](viewing_metrics_with_cloudwatch.md)  |  | 
|  Network utilization  |  [NetworkIn](viewing_metrics_with_cloudwatch.md) [NetworkOut](viewing_metrics_with_cloudwatch.md)  |  | 
|  Disk performance  |  [DiskReadOps](viewing_metrics_with_cloudwatch.md) [DiskWriteOps](viewing_metrics_with_cloudwatch.md)  |  | 
|  Disk Reads/Writes  |  [DiskReadBytes](viewing_metrics_with_cloudwatch.md) [DiskWriteBytes](viewing_metrics_with_cloudwatch.md)  |  | 
|  Memory utilization, disk swap utilization, disk space utilization, page file utilization, log collection  |  |  \[Linux and Windows Server instances\] [Collect Metrics and Logs from Amazon EC2 Instances and On\-Premises Servers with the CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) \[Migration from previous CloudWatch Logs agent on Windows Server instances\] [ Migrate Windows Server Instance Log Collection to the CloudWatch Agent ](https://docs.aws.amazon.com/systems-manager/latest/userguide/monitoring-cloudwatch-agent.html#monitoring-cloudwatch-agent-migrate)  | 
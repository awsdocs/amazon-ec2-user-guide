# Monitoring Amazon EC2<a name="monitoring_ec2"></a>

Monitoring is an important part of maintaining the reliability, availability, and performance of your Amazon Elastic Compute Cloud \(Amazon EC2\) instances and your AWS solutions\. You should collect monitoring data from all of the parts in your AWS solutions so that you can more easily debug a multi\-point failure if one occurs\. Before you start monitoring Amazon EC2, however, you should create a monitoring plan that should include:

+ What are your goals for monitoring?

+ What resources you will monitor?

+ How often you will monitor these resources?

+ What monitoring tools will you use?

+ Who will perform the monitoring tasks?

+ Who should be notified when something goes wrong? 

After you have defined your monitoring goals and have created your monitoring plan, the next step is to establish a baseline for normal Amazon EC2 performance in your environment\. You should measure Amazon EC2 performance at various times and under different load conditions\. As you monitor Amazon EC2, you should store a history of monitoring data that you've collected\. You can compare current Amazon EC2 performance to this historical data to help you to identify normal performance patterns and performance anomalies, and devise methods to address them\. For example, you can monitor CPU utilization, disk I/O, and network utilization for your Amazon EC2 instances\. When performance falls outside your established baseline, you might need to reconfigure or optimize the instance to reduce CPU utilization, improve disk I/O, or reduce network traffic\.

To establish a baseline you should, at a minimum, monitor the following items:


| Item to Monitor | Amazon EC2 Metric | Monitoring Script/CloudWatch Logs | 
| --- | --- | --- | 
|  CPU utilization  |  CPUUtilization  |  | 
|  Memory utilization  |  |  \[Linux instances\] [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html) \[Windows instances\] [Configure Integration with CloudWatch](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/send_logs_to_cwl_instances.html#ec2-configuration-cwl)  | 
|  Memory used  |  |  \[Linux instances\] [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html) \[Windows instances\] [Configure Integration with CloudWatch](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/send_logs_to_cwl_instances.html#ec2-configuration-cwl)  | 
|  Memory available  |  |  \[Linux instances\] [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html) \[Windows instances\] [Configure Integration with CloudWatch](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/send_logs_to_cwl_instances.html#ec2-configuration-cwl)  | 
|  Network utilization  |  NetworkIn NetworkOut  |  | 
|  Disk performance  |  DiskReadOps DiskWriteOps  |  | 
|  Disk Swap utilization \[Linux instances\] Swap used \[Linux instances\]  |  |  [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html)  | 
|  Page File utilization \(Windows instances only\) Page File used \(Windows instances only\) Page File available \(Windows instances only\)  |  |  [Configure Integration with CloudWatch](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/send_logs_to_cwl_instances.html#ec2-configuration-cwl)  | 
|  Disk Reads/Writes  |  DiskReadBytes DiskWriteBytes  |  | 
|  Disk Space utilization \[Linux instances\]  |  |  [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html)  | 
|  Disk Space used \[Linux instances\]  |  |  [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html)  | 
|  Disk Space available \[Linux instances only\]  |  |  [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html)  | 
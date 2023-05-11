# Monitor memory and disk metrics for Amazon EC2 Linux instances<a name="mon-scripts"></a>

You can use Amazon CloudWatch to collect metrics and logs from the operating systems for your EC2 instances\.

**Important**  
The CloudWatch monitoring scripts are deprecated\. We strongly recommend that you use the CloudWatch agent to collect metrics and logs\. For more information, see [ Collect Metrics from Amazon EC2 Instances and On\-Premises Servers with the CloudWatch Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*\.  
If you are still migrating from the deprecated monitoring scripts to the agent, and require information about the monitoring scripts, see [Deprecated: Collect metrics using the CloudWatch monitoring scripts](monitoring-scripts-intro.md)\.

## Collect metrics using the CloudWatch agent<a name="new-cloudwatch-agent"></a>

You can use the CloudWatch agent to collect both system metrics and log files from Amazon EC2 instances and on\-premises servers\. The agent supports both Windows Server and Linux, and enables you to select the metrics to be collected, including sub\-resource metrics such as per\-CPU core\. We recommend that you use the agent to collect metrics and logs instead of using the deprecated monitoring scripts\. For more information, see [Collect metrics and logs from Amazon EC2 instances and on\-premises servers with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*\.
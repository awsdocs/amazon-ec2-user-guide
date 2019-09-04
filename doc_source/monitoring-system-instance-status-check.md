# Status Checks for Your Instances<a name="monitoring-system-instance-status-check"></a>

With instance status monitoring, you can quickly determine whether Amazon EC2 has detected any problems that might prevent your instances from running applications\. Amazon EC2 performs automated checks on every running EC2 instance to identify hardware and software issues\. You can view the results of these status checks to identify specific and detectable problems\. The event status data augments the information that Amazon EC2 already provides about the state of each instance \(such as `pending`, `running`, `stopping`\) and the utilization metrics that Amazon CloudWatch monitors \(CPU utilization, network traffic, and disk activity\)\.

Status checks are performed every minute, returning a pass or a fail status\. If all checks pass, the overall status of the instance is **OK**\. If one or more checks fail, the overall status is **impaired**\. Status checks are built into Amazon EC2, so they cannot be disabled or deleted\.

When a status check fails, the corresponding CloudWatch metric for status checks is incremented\. For more information, see [Status Check Metrics](viewing_metrics_with_cloudwatch.md#status-check-metrics)\. You can use these metrics to create CloudWatch alarms that are triggered based on the result of the status checks\. For example, you can create an alarm to warn you if status checks fail on a specific instance\. For more information, see [Creating and Editing Status Check Alarms](#creating_status_check_alarms)\.

You can also create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance and automatically recovers the instance if it becomes impaired due to an underlying issue\. For more information, see [Recover Your Instance](ec2-instance-recover.md)\.

**Topics**
+ [Types of Status Checks](#types-of-instance-status-checks)
+ [Viewing Status Checks](#viewing_status)
+ [Reporting Instance Status](#reporting_status)
+ [Creating and Editing Status Check Alarms](#creating_status_check_alarms)

## Types of Status Checks<a name="types-of-instance-status-checks"></a>

There are two types of status checks: system status checks and instance status checks\.

**System Status Checks**  
Monitor the AWS systems on which your instance runs\. These checks detect underlying problems with your instance that require AWS involvement to repair\. When a system status check fails, you can choose to wait for AWS to fix the issue, or you can resolve it yourself\. For instances backed by Amazon EBS, you can stop and start the instance yourself, which in most cases results in the instance being migrated to a new host\. For instances backed by instance store, you can terminate and replace the instance\.

The following are examples of problems that can cause system status checks to fail:
+ Loss of network connectivity
+ Loss of system power
+ Software issues on the physical host
+ Hardware issues on the physical host that impact network reachability

**Instance Status Checks**  
Monitor the software and network configuration of your individual instance\. Amazon EC2 checks the health of the instance by sending an address resolution protocol \(ARP\) request to the network interface \(NIC\)\. These checks detect problems that require your involvement to repair\. When an instance status check fails, you typically must address the problem yourself \(for example, by rebooting the instance or by making instance configuration changes\)\.

The following are examples of problems that can cause instance status checks to fail:
+ Failed system status checks
+ Incorrect networking or startup configuration
+ Exhausted memory
+ Corrupted file system
+ Incompatible kernel

## Viewing Status Checks<a name="viewing_status"></a>

Amazon EC2 provides you with several ways to view and work with status checks\.

### Viewing Status Using the Console<a name="viewing_status-new-console"></a>

You can view status checks using the AWS Management Console\.

**To view status checks \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. On the **Instances** page, the **Status Checks** column lists the operational status of each instance\.

1. To view the status of a specific instance, select the instance, and then choose the **Status Checks** tab\.  
![\[Viewing status\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/status-check-tab.png)

   If you have an instance with a failed status check and the instance has been unreachable for over 20 minutes, choose **AWS Support** to submit a request for assistance\. To troubleshoot system or instance status check failures yourself, see [Troubleshooting Instances with Failed Status Checks](TroubleshootingInstances.md)\.

1. To review the CloudWatch metrics for status checks, select the instance, and then choose the **Monitoring** tab\. Scroll until you see the graphs for the following metrics:
   + **Status Check Failed \(Any\)**
   + **Status Check Failed \(Instance\)**
   + **Status Check Failed \(System\)**

### Viewing Status Using the Command Line<a name="viewing_status-cli"></a>

You can view status checks for running instances using the [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) \(AWS CLI\) command\.

To view the status of all instances, use the following command\.

```
aws ec2 describe-instance-status
```

To get the status of all instances with an instance status of `impaired`, use the following command\.

```
aws ec2 describe-instance-status --filters Name=instance-status.status,Values=impaired
```

To get the status of a single instance, use the following command\.

```
aws ec2 describe-instance-status --instance-ids i-1234567890abcdef0
```

Alternatively, use the following commands:
+  [Get\-EC2InstanceStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceStatus.html) \(AWS Tools for Windows PowerShell\) 
+  [DescribeInstanceStatus](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeInstanceStatus.html) \(Amazon EC2 Query API\)

If you have an instance with a failed status check, see [Troubleshooting Instances with Failed Status Checks](TroubleshootingInstances.md)\.

## Reporting Instance Status<a name="reporting_status"></a>

You can provide feedback if you are having problems with an instance whose status is not shown as impaired, or if you want to send AWS additional details about the problems you are experiencing with an impaired instance\.

We use reported feedback to identify issues impacting multiple customers, but do not respond to individual account issues\. Providing feedback does not change the status check results that you currently see for the instance\.

### Reporting Status Feedback Using the Console<a name="reporting_status-new-console"></a>

**To report instance status \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose the **Status Checks** tab, and choose **Submit feedback**\.

1. Complete the **Report Instance Status** form, and then choose **Submit**\.

### Reporting Status Feedback Using the Command Line<a name="reporting_status-cli"></a>

Use the following [report\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/report-instance-status.html) \(AWS CLI\) command to send feedback about the status of an impaired instance\.

```
aws ec2 report-instance-status --instances i-1234567890abcdef0 --status impaired --reason-codes code
```

Alternatively, use the following commands:
+  [Send\-EC2InstanceStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Send-EC2InstanceStatus.html) \(AWS Tools for Windows PowerShell\) 
+  [ReportInstanceStatus](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ReportInstanceStatus.html) \(Amazon EC2 Query API\)

## Creating and Editing Status Check Alarms<a name="creating_status_check_alarms"></a>

You can use the [status check metrics](viewing_metrics_with_cloudwatch.md#status-check-metrics) to create CloudWatch alarms to notify you when an instance has a failed status check\.

### Creating a Status Check Alarm Using the Console<a name="using-cloudwatch-new-console2"></a>

Use the following procedure to configure an alarm that sends you a notification by email, or stops, terminates, or recovers an instance when it fails a status check\.

**To create a status check alarm \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, choose the **Status Checks** tab, and choose **Create Status Check Alarm**\.

1. Select **Send a notification to**\. Choose an existing SNS topic, or choose **create topic** to create a new one\. If creating a new topic, in **With these recipients**, enter your email address and the addresses of any additional recipients, separated by commas\.

1. \(Optional\) Select **Take the action**, and then select the action that you'd like to take\.

1. In **Whenever**, select the status check that you want to be notified about\.

   If you selected **Recover this instance** in the previous step, select **Status Check Failed \(System\)**\.

1. In **For at least**, set the number of periods you want to evaluate and in **consecutive periods**, select the evaluation period duration before triggering the alarm and sending an email\.

1. \(Optional\) In **Name of alarm**, replace the default name with another name for the alarm\.

1. Choose **Create Alarm**\.
**Important**  
If you added an email address to the list of recipients or created a new topic, Amazon SNS sends a subscription confirmation email message to each new address\. Each recipient must confirm the subscription by choosing the link contained in that message\. Alert notifications are sent only to confirmed addresses\.

If you need to make changes to an instance status alarm, you can edit it\.

**To edit a status check alarm using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **CloudWatch Monitoring**, **Add/Edit Alarms**\.

1. In the **Alarm Details** dialog box, choose the name of the alarm\.

1. In the **Edit Alarm** dialog box, make the desired changes, and then choose **Save**\.

### Creating a Status Check Alarm Using the AWS CLI<a name="using-cloudwatch-new-cli2"></a>

In the following example, the alarm publishes a notification to an SNS topic, `arn:aws:sns:us-west-2:111122223333:my-sns-topic`, when the instance fails either the instance check or system status check for at least two consecutive periods\. The CloudWatch metric used is `StatusCheckFailed`\.

**To create a status check alarm using the AWS CLI**

1. Select an existing SNS topic or create a new one\. For more information, see [Using the AWS CLI with Amazon SNS](https://docs.aws.amazon.com/cli/latest/userguide/cli-sqs-queue-sns-topic.html) in the *AWS Command Line Interface User Guide*\.

1. Use the following [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command to view the available Amazon CloudWatch metrics for Amazon EC2\.

   ```
   aws cloudwatch list-metrics --namespace AWS/EC2
   ```

1. Use the following [put\-metric\-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html) command to create the alarm\.

   ```
   aws cloudwatch put-metric-alarm --alarm-name StatusCheckFailed-Alarm-for-i-1234567890abcdef0 --metric-name StatusCheckFailed --namespace AWS/EC2 --statistic Maximum --dimensions Name=InstanceId,Value=i-1234567890abcdef0 --unit Count --period 300 --evaluation-periods 2 --threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --alarm-actions arn:aws:sns:us-west-2:111122223333:my-sns-topic
   ```

   The period is the time frame, in seconds, in which Amazon CloudWatch metrics are collected\. This example uses 300, which is 60 seconds multiplied by 5 minutes\. The evaluation period is the number of consecutive periods for which the value of the metric must be compared to the threshold\. This example uses 2\. The alarm actions are the actions to perform when this alarm is triggered\. This example configures the alarm to send an email using Amazon SNS\.
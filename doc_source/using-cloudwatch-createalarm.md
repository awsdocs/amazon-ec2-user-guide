# Create a CloudWatch alarm for an instance<a name="using-cloudwatch-createalarm"></a>

You can create a CloudWatch alarm that monitors CloudWatch metrics for one of your instances\. CloudWatch will automatically send you a notification when the metric reaches a threshold you specify\. You can create a CloudWatch alarm using the Amazon EC2 console, or using the more advanced options provided by the CloudWatch console\.

**To create an alarm using the CloudWatch console**  
For examples, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.

**To create an alarm using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitor and troubleshoot**, **Manage CloudWatch alarms**\.

1. On the **Manage CloudWatch alarms** detail page, under **Add or edit alarm**, select **Create an alarm**\.

1. For **Alarm notification**, choose whether to turn the toggle on or off to configure Amazon Simple Notification Service \(Amazon SNS\) notifications\. Enter an existing Amazon SNS topic or enter a name to create a new topic\.

1. For **Alarm action**, choose whether to turn the toggle on or off to specify an action to take when the alarm is triggered\. Select an action from the dropdown\.

1. For **Alarm thresholds**, select the metric and criteria for the alarm\. For example, you can leave the default settings for **Group samples by** \(**Average**\) and **Type of data to sample** \(**CPU utilization**\)\. For **Alarm when**, choose **>=** and enter **0\.80**\. For **Consecutive period**, enter **1**\. For **Period**, select **5 minutes**\.

1. \(Optional\) For **Sample metric data**, choose **Add to dashboard**\.

1. Choose **Create**\.

You can edit your CloudWatch alarm settings from the Amazon EC2 console or the CloudWatch console\. If you want to delete your alarm, you can do so from the CloudWatch console\. For more information, see [Editing or deleting a CloudWatch alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Edit-CloudWatch-Alarm.html) in the *Amazon CloudWatch User Guide*\.
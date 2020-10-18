# Create a CloudWatch alarm for an instance<a name="using-cloudwatch-createalarm"></a>

You can create a CloudWatch alarm that monitors CloudWatch metrics for one of your instances\. CloudWatch will automatically send you a notification when the metric reaches a threshold you specify\. You can create a CloudWatch alarm using the Amazon EC2 console, or using the more advanced options provided by the CloudWatch console\.

**To create an alarm using the CloudWatch console**  
For examples, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.

------
#### [ New console ]

**To create an alarm using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitoring**, **Manage CloudWatch alarms**\.

1. On the **Manage CloudWatch alarms** detail page, under **Add or edit alarm**, select **Create a new alarm**\.

1. For **Alarm notification**, choose whether to turn the toggle on or off to configure Amazon Simple Notification Service \(Amazon SNS\) notifications\. Enter an existing Amazon SNS topic or enter a name to create a new topic\.

1. For **Alarm action**, choose whether to turn the toggle on or off to specify an action to take when the alarm is triggered\. Select an action from the dropdown\.

1. For **Alarm thresholds**, select the metric and criteria for the alarm\. For example, you can leave the default settings for **Group samples by** \(**Average**\) and **Type of data to sample** \(**CPU utilization**\)\. For **Alarm when**, choose **>=** and enter **0\.80**\. For **Consecutive period**, enter **1**\. For **Period**, select **5 minutes**\.

1. \(Optional\) For **Sample metric data**, choose **Add to dashboard**\.

1. Choose **Create**\.

------
#### [ Old console ]

**To create an alarm using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Monitoring** tab located at the bottom of the page, choose **Create Alarm**\. Or, from the **Actions** dropdown, choose **CloudWatch Monitoring**, **Add/Edit Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. Choose **create topic**\. For **Send a notification to**, enter a name for the SNS topic\. For **With these recipients**, enter one or more email addresses to receive notification\.

   1. Specify the metric and the criteria for the policy\. For example, you can leave the default settings for **Whenever** \(Average of CPU Utilization\)\. For **Is**, choose `>=` and enter `80` percent\. For **For at least**, enter `1` consecutive period of `5 Minutes`\.

   1. Choose **Create Alarm**\.  
![\[Create a CloudWatch alarm using the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/monitoring_create_alarm.png)

------
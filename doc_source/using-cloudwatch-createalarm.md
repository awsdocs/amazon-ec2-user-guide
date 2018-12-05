# Create a CloudWatch Alarm for an Instance<a name="using-cloudwatch-createalarm"></a>

You can create a CloudWatch alarm that monitors CloudWatch metrics for one of your instances\. CloudWatch will automatically send you a notification when the metric reaches a threshold you specify\. You can create a CloudWatch alarm using the Amazon EC2 console, or using the more advanced options provided by the CloudWatch console\.

**To create an alarm using the CloudWatch console**  
For examples, see [Creating Amazon CloudWatch Alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) in the *Amazon CloudWatch User Guide*\.

**To create an alarm using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. Choose **create topic**\. For **Send a notification to**, enter a name for the SNS topic\. For **With these recipients**, enter one or more email addresses to receive notification\.

   1. Specify the metric and the criteria for the policy\. For example, you can leave the default settings for **Whenever** \(Average of CPU Utilization\)\. For **Is**, choose `>=` and enter `80` percent\. For **For at least**, enter `1` consecutive period of `5 Minutes`\.

   1. Choose **Create Alarm**\.  
![\[Create a CloudWatch alarm using the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/monitoring_create_alarm.png)
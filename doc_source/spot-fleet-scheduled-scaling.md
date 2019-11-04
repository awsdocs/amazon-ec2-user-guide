# Scale Spot Fleet Using Scheduled Scaling<a name="spot-fleet-scheduled-scaling"></a>

Scaling based on a schedule enables you to scale your application in response to predictable changes in demand\. To use scheduled scaling, you create *scheduled actions*, which tell Spot Fleet to perform scaling activities at specific times\. When you create a scheduled action, you specify the Spot Fleet, when the scaling activity should occur, minimum capacity, and maximum capacity\. You can create scheduled actions that scale one time only or that scale on a recurring schedule\.

**Limits**
+ The Spot Fleet request must have a request type of `maintain`\. Automatic scaling is not supported for one\-time requests or Spot blocks\.

**To create a one\-time scheduled action**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request and choose **Scheduled Scaling**\.

1. Choose **Create Scheduled Action**\.

1. For **Name**, specify a name for the scheduled action\.

1. Enter a value for **Minimum capacity**, **Maximum capacity**, or both\.

1. For **Recurrence**, choose **Once**\.

1. \(Optional\) Choose a date and time for **Start time**, **End time**, or both\.

1. Choose **Submit**\.

**To scale on a recurring schedule**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request and choose **Scheduled Scaling**\.

1. For **Recurrence**, choose one of the predefined schedules \(for example, **Every day**\), or choose **Custom** and type a cron expression\. For more information about the cron expressions supported by scheduled scaling, see [Cron Expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.

1. \(Optional\) Choose a date and time for **Start time**, **End time**, or both\.

1. Choose **Submit**\.

**To edit a scheduled action**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request and choose **Scheduled Scaling**\.

1. Select the scheduled action and choose **Actions**, **Edit**\.

1. Make the needed changes and choose **Submit**\.

**To delete a scheduled action**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request and choose **Scheduled Scaling**\.

1. Select the scheduled action and choose **Actions**, **Delete**\.

1. When prompted for confirmation, choose **Delete**\.

**To manage scheduled scaling using the AWS CLI**

Use the following commands:
+ [put\-scheduled\-action](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/put-scheduled-action.html)
+ [describe\-scheduled\-actions](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/describe-scheduled-actions.html)
+ [delete\-scheduled\-action](https://docs.aws.amazon.com/cli/latest/reference/application-autoscaling/delete-scheduled-action.html)
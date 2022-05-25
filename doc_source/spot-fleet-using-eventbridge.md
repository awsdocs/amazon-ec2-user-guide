# Create Amazon EventBridge rules to monitor Spot Fleet events<a name="spot-fleet-using-eventbridge"></a>

When a notification of a state change is emitted for a Spot Fleet, the event for the notification is sent to Amazon EventBridge in the form of a JSON file\. You can write an EventBridge rule to automate what actions to take when an event pattern matches the rule\. If EventBridge detects an event pattern that matches a pattern defined in a rule, EventBridge invokes the target \(or targets\) specified in the rule\.

The following fields form the event pattern that is defined in the rule:

`"source": "aws.ec2spotfleet"`  
Identifies that the event is from Spot Fleet\.

`"detail-type": "EC2 Spot Fleet State Change"`  
Identifies the event type\.

`"detail": { "sub-type": "submitted" }`  
Identifies the event sub\-type\.

For the list of Spot Fleet events and example event data, see [Spot Fleet event types](spot-fleet-event-types.md)\. 

**Topics**
+ [Create an EventBridge rule to send a notification](#eventbridge-send-notification)
+ [Create an EventBridge rule to trigger a Lambda function](#eventbridge-trigger-lambda)

## Create an EventBridge rule to send a notification<a name="eventbridge-send-notification"></a>

The following example creates an EventBridge rule to send an email, text message, or mobile push notification every time that Amazon EC2 emits a Spot Fleet state change notification\. The signal in this example is emitted as an `EC2 Spot Fleet State Change` event, which triggers the action defined by the rule\. Before creating the EventBridge rule, you must create the Amazon SNS topic for the email, text message, or mobile push notification\.

**To create an EventBridge rule to send a notification when a Spot Fleet state changes**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. For **Define rule detail**, do the following:

   1. Enter a **Name** for the rule, and, optionally, a description\.

      A rule can't have the same name as another rule in the same Region and on the same event bus\.

   1. For **Event bus**, choose **default**\. When an AWS service in your account generates an event, it always goes to your account's default event bus\.

   1. For **Rule type**, choose **Rule with an event pattern**\.

   1. Choose **Next**\.

1. For **Build event pattern**, do the following:

   1. For **Event source**, choose **AWS events or EventBridge partner events**\.

   1. For **Event pattern**, for this example you’ll specify the following event pattern to match the `EC2 Spot Fleet Instance Change` event\.

      ```
      {
       "source": ["aws.ec2spotfleet"],
       "detail-type": ["EC2 Spot Fleet Instance Change"]
      }
      ```

      To add the event pattern, you can either use a template by choosing **Event pattern form**, or specify your own pattern by choosing **Custom pattern \(JSON editor\)**, as follows:

      1. To use a template to create the event pattern, do the following:

         1. Choose **Event pattern form**\.

         1. For **Event source**, choose **AWS services**\.

         1. For **AWS Service**, choose **EC2 Spot Fleet**\.

         1. For **Event type**, choose **EC2 Spot Fleet Instance Change**\.

         1. To customize the template, choose **Edit pattern** and make your changes to match the example event pattern\.

      1. \(Alternative\) To specify a custom event pattern, do the following:

         1. Choose **Custom pattern \(JSON editor\)**\.

         1. In the **Event pattern** box, add the event pattern for this example\.

   1. Choose **Next**\.

1. For **Select target\(s\)**, do the following:

   1. For **Target types**, choose **AWS service**\.

   1. For **Select a target**, choose **SNS topic** to send an email, text message, or mobile push notification when the event occurs\.

   1. For **Topic**, choose an existing topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

   1. \(Optional\) Under **Additional settings**, you can optionally configure additional settings\. For more information, see [Creating Amazon EventBridge rules that react to events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule.html) \(step 16\) in the *Amazon EventBridge User Guide*\.

   1. Choose **Next**\.

1. \(Optional\) For **Tags**, you can optionally assign one or more tags to your rule, and then choose **Next**\.

1. For **Review and create**, do the following:

   1. Review the details of the rule and modify them as necessary\.

   1. Choose **Create rule**\.

For more information, see [Amazon EventBridge rules](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) and [Amazon EventBridge event patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html) in the *Amazon EventBridge User Guide*

## Create an EventBridge rule to trigger a Lambda function<a name="eventbridge-trigger-lambda"></a>

The following example creates an EventBridge rule to trigger a Lambda function every time that Amazon EC2 emits a Spot Fleet instance change notification for when an instance is launched\. The signal in this example is emitted as an `EC2 Spot Fleet Instance Change` event, sub\-type `launched`, which triggers the action defined by the rule\.

Before creating the EventBridge rule, you must create the Lambda function\.

**To create the Lambda function to use in the EventBridge rule**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Enter a name for your function, configure the code, and then choose **Create function**\.

   For more information about using Lambda, see [Create a Lambda function with the console](https://docs.aws.amazon.com/lambda/latest/dg/getting-started-create-function.html) in the *AWS Lambda Developer Guide*\.

**To create an EventBridge rule to trigger a Lambda function when an instance in a Spot Fleet changes state**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. For **Define rule detail**, do the following:

   1. Enter a **Name** for the rule, and, optionally, a description\.

      A rule can't have the same name as another rule in the same Region and on the same event bus\.

   1. For **Event bus**, choose **default**\. When an AWS service in your account generates an event, it always goes to your account's default event bus\.

   1. For **Rule type**, choose **Rule with an event pattern**\.

   1. Choose **Next**\.

1. For **Build event pattern**, do the following:

   1. For **Event source**, choose **AWS events or EventBridge partner events**\.

   1. For **Event pattern**, for this example you’ll specify the following event pattern to match the `EC2 Spot Fleet Instance Change` event and `launched` sub\-type\.

      ```
      {
       "source": ["aws.ec2spotfleet"],
       "detail-type": ["EC2 Spot Fleet Instance Change"],
       "detail": {
         "sub-type": ["launched"]
      }
      ```

      To add the event pattern, you can either use a template by choosing **Event pattern form**, or specify your own pattern by choosing **Custom pattern \(JSON editor\)**, as follows:

      1. To use a template to create the event pattern, do the following:

         1. Choose **Event pattern form**\.

         1. For **Event source**, choose **AWS services**\.

         1. For **AWS Service**, choose **EC2 Spot Fleet**\.

         1. For **Event type**, choose **EC2 Spot Fleet Instance Change**\.

         1. Choose **Edit pattern**, and add `"detail": {"sub-type": ["launched"]` to match the example event pattern\. For proper JSON format, insert a comma \(`,`\) after the preceding square bracket \(`]`\)\.

      1. \(Alternative\) To specify a custom event pattern, do the following:

         1. Choose **Custom pattern \(JSON editor\)**\.

         1. In the **Event pattern** box, add the event pattern for this example\.

   1. Choose **Next**\.

1. For **Select target\(s\)**, do the following:

   1. For **Target types**, choose **AWS service**\.

   1. For **Select a target**, choose **SNS topic** to send an email, text message, or mobile push notification when the event occurs\.

   1. For **Topic**, choose **Lambda function**, and for **Function**, choose the function that you created to respond when the event occurs\.

   1. \(Optional\) Under **Additional settings**, you can optionally configure additional settings\. For more information, see [Creating Amazon EventBridge rules that react to events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-create-rule.html) \(step 16\) in the *Amazon EventBridge User Guide*\.

   1. Choose **Next**\.

1. \(Optional\) For **Tags**, you can optionally assign one or more tags to your rule, and then choose **Next**\.

1. For **Review and create**, do the following:

   1. Review the details of the rule and modify them as necessary\.

   1. Choose **Create rule**\.

For a tutorial on how to create a Lambda function and an EventBridge rule that runs the Lambda function, see [Tutorial: Log the State of an Amazon EC2 Instance Using EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/log-ec2-instance-state.html) in the *AWS Lambda Developer Guide*\.
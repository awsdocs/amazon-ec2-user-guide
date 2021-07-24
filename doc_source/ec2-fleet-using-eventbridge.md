# Create Amazon EventBridge rules to monitor EC2 Fleet events<a name="ec2-fleet-using-eventbridge"></a>

When a notification of a state change is emitted for an EC2 Fleet, the event for the notification is sent to Amazon EventBridge in the form of a JSON file\. You can write an EventBridge rule to automate what actions to take when an event pattern matches the rule\. If EventBridge detects an event pattern that matches a pattern defined in a rule, EventBridge invokes the target \(or targets\) specified in the rule\.

The following fields form the event pattern that is defined in the rule:

`"source": "aws.ec2fleet"`  
Identifies that the event is from EC2 Fleet\.

`"detail-type": "EC2 Fleet State Change"`  
Identifies the event type\.

`"detail": { "sub-type": "submitted" }`  
Identifies the event sub\-type\.

For the list of EC2 Fleet events and example event data, see [EC2 Fleet event types](ec2-fleet-event-types.md)\.

**Topics**
+ [Create an EventBridge rule to send a notification](#eventbridge-send-notification)
+ [Create an EventBridge rule to trigger a Lambda function](#eventbridge-trigger-lambda)

## Create an EventBridge rule to send a notification<a name="eventbridge-send-notification"></a>

The following example creates an EventBridge rule to send an email, text message, or mobile push notification every time that Amazon EC2 emits an EC2 Fleet state change notification\. The signal in this example is emitted as an `EC2 Fleet State Change` event, which triggers the action defined by the rule\. Before creating the EventBridge rule, you must create the Amazon SNS topic for the email, text message, or mobile push notification\.

**To create an EventBridge rule to send a notification when an EC2 Fleet state changes**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. Enter a **Name** for the rule, and, optionally, a description\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Event pattern**\.

1. Under **Event matching pattern**, you can choose **Pre\-defined pattern by service** or **Custom pattern**\. The **Custom pattern** allows you to create a more detailed rule\.

   1. If you choose **Pre\-defined pattern by service**, do the following:

      1. For **Service provider**, choose **AWS**\.

      1. For **Service name**, choose **EC2 Fleet**\.

      1. For **Event type**, select the required event type\. For this example, choose **EC2 Fleet Instance Change**\.

   1. If you choose **Custom pattern**, do the following:

      1. In the **Event pattern** box, add the following pattern to match the `EC2 Fleet Instance Change` event for this example, and then choose **Save**\.

        ```
        {
            "source": ["aws.ec2fleet"],
            "detail-type": ["EC2 Fleet Instance Change"]
        }
        ```

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account's default event bus\.

1. Confirm that **Enable the rule on the selected event bus** is toggled on\. 

1. For **Target**, choose **SNS topic** to send an email, text message, or mobile push notification when the event occurs\.

1. For **Topic**, choose an existing topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

1. For **Configure input**, choose the input for the email, text message, or mobile push notification\.

1. Choose **Create**\.

For more information, see [Amazon EventBridge rules](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) and [Amazon EventBridge event patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html) in the *Amazon EventBridge User Guide*

## Create an EventBridge rule to trigger a Lambda function<a name="eventbridge-trigger-lambda"></a>

The following example creates an EventBridge rule to trigger a Lambda function every time that Amazon EC2 emits an EC2 Fleet instance change notification for when an instance is launched\. The signal in this example is emitted as an `EC2 Fleet Instance Change` event, sub\-type `launched`, which triggers the action defined by the rule\. Before creating the EventBridge rule, you must create the Lambda function\.

**To create an EventBridge rule to trigger a Lambda function when an instance in an EC2 Fleet changes state**

1. Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. Choose **Create function**\.

1. Enter a name for your function, configure the code, and then choose **Create function**\.

   For more information about using Lambda, see [Create a Lambda function with the console](https://docs.aws.amazon.com/lambda/latest/dg/getting-started-create-function.html) in the *AWS Lambda Developer Guide*\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. Enter a **Name** for the rule, and, optionally, a description\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Event pattern**\.

1. Under **Event matching pattern**, you can choose **Pre\-defined pattern by service** or **Custom pattern**\. The **Custom pattern** allows you to create a more detailed rule\.

   1. If you choose **Pre\-defined pattern by service**, do the following:

      1. For **Service provider**, choose **AWS**\.

      1. For **Service name**, choose **EC2 Fleet**\.

      1. For **Event type**, select the required event type\. For this example, choose **EC2 Fleet Instance Change**\.

   1. If you choose **Custom pattern**, do the following:

      1. In the **Event pattern** box, add the following pattern to match the `EC2 Fleet Instance Change` event and `launched` sub\-type for this example, and then choose **Save**\.

        ```
        {
            "source": ["aws.ec2fleet"],
            "detail-type": ["EC2 Fleet Instance Change"],
            "detail": {
              "sub-type": ["launched"]
            }
        }
        ```

1. For **Target**, choose **Lambda function**, and for **Function**, choose the function that you created to respond when the event occurs\.

1. Choose **Create**\.

   In this example, the Lambda function will be triggered when the `EC2 Fleet Instance Change` event with the sub\-type `launched` occurs\.

For a tutorial on how to create a Lambda function and an EventBridge rule that runs the Lambda function, see [Tutorial: Log the State of an Amazon EC2 Instance Using EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/log-ec2-instance-state.html) in the *AWS Lambda Developer Guide*\.
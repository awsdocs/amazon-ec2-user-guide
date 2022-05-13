# Monitor AMI events using Amazon EventBridge<a name="monitor-ami-events"></a>

When the state of an Amazon Machine Image \(AMI\) changes, Amazon EC2 generates an event that is sent to Amazon EventBridge \(formerly known as Amazon CloudWatch Events\)\. You can use Amazon EventBridge to detect and react to these events\. You do this by creating rules in EventBridge that trigger an action in response to an event\. For example, you can create an EventBridge rule that detects when the AMI creation process has completed and then invokes an Amazon SNS topic to send an email notification to you\.

Amazon EC2 generates an event when an AMI enters any of the following states:
+ `available`
+ `failed`
+ `deregistered`

An AMI can enter the `available` or `failed` state when one of the following AMI operations runs:
+ CreateImage
+ CopyImage
+ RegisterImage
+ CreateRestoreImageTask

An AMI can enter the `deregistered` state when the following AMI operation runs:
+ DeregisterImage

Events are generated on a best effort basis\.

**Topics**
+ [AMI events](#ami-events)
+ [Create Amazon EventBridge rules](#create-eventbridge-rules)

## AMI events<a name="ami-events"></a>

**There are three `EC2 AMI State Change` events:**
+ [`available`](#ami-event-available)
+ [`failed`](#ami-event-failed)
+ [`deregistered`](#ami-event-deregistered)

The events are sent to the default EventBridge event bus in JSON format\.

The following fields in the event can be used to create rules that trigger an action:

`"source": "aws.ec2“`  
Identifies that the event is from Amazon EC2\.

`"detail-type": "EC2 AMI State Change"`  
Identifies the event name\.

`"detail": { "ImageId": "ami-0123456789example", "State": "available", }`  
Provides the following information:  
+ The AMI ID – If you want to track a specific AMI\.
+ The state of the AMI \(`available`, `failed`, or `deregistered`\)\.

### `available`<a name="ami-event-available"></a>

The following is an example of an event that Amazon EC2 generates when the AMI enters the `available` state following a successful CreateImage, CopyImage, RegisterImage, or CreateRestoreImageTask operation\.

`"State": "available"` indicates that the operation was successful\.

```
{
    "version": "0",
    "id": "example-9f07-51db-246b-d8b8441bcdf0",
    "detail-type": "EC2 AMI State Change",
    "source": "aws.ec2",
    "account": "012345678901",
    "time": "yyyy-mm-ddThh:mm:ssZ",
    "region": "us-east-1",
    "resources": ["arn:aws:ec2:us-east-1::image/ami-0123456789example"],
    "detail": {
        "RequestId": "example-9dcc-40a6-aa77-7ce457d5442b",
        "ImageId": "ami-0123456789example",
        "State": "available",
        "ErrorMessage": ""
    }
}
```

### `failed`<a name="ami-event-failed"></a>

The following is an example of an event that Amazon EC2 generates when the AMI enters the `failed` state following a failed CreateImage, CopyImage, RegisterImage, or CreateRestoreImageTask operation\.

The following fields provide pertinent information:
+ `"State": "failed"` – Indicates that the operation failed\.
+ `"ErrorMessage": ""` – Provides the reason for the failed operation\.

```
{
    "version": "0",
    "id": "example-9f07-51db-246b-d8b8441bcdf0",
    "detail-type": "EC2 AMI State Change",
    "source": "aws.ec2",
    "account": "012345678901",
    "time": "yyyy-mm-ddThh:mm:ssZ",
    "region": "us-east-1",
    "resources": ["arn:aws:ec2:us-east-1::image/ami-0123456789example"],
    "detail": {
        "RequestId": "example-9dcc-40a6-aa77-7ce457d5442b",
        "ImageId": "ami-0123456789example",
        "State": "failed",
        "ErrorMessage": "Description of failure"
    }
}
```

### `deregistered`<a name="ami-event-deregistered"></a>

The following is an example of an event that Amazon EC2 generates when the AMI enters the `deregistered` state following a successful DeregisterImage operation\. If the operation fails, no event is generated\. Any failure is known immediately because DeregisterImage is a synchronous operation\.

`"State": "deregistered"` indicates that the DeregisterImage operation was successful\.

```
{
    "version": "0",
    "id": "example-9f07-51db-246b-d8b8441bcdf0",
    "detail-type": "EC2 AMI State Change",
    "source": "aws.ec2",
    "account": "012345678901",
    "time": "yyyy-mm-ddThh:mm:ssZ",
    "region": "us-east-1",
    "resources": ["arn:aws:ec2:us-east-1::image/ami-0123456789example"],
    "detail": {
        "RequestId": "example-9dcc-40a6-aa77-7ce457d5442b",
        "ImageId": "ami-0123456789example",
        "State": "deregistered",
        "ErrorMessage": ""
    }
}
```

## Create Amazon EventBridge rules<a name="create-eventbridge-rules"></a>

You can create an Amazon EventBridge [rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) that specifies an action to take when EventBridge receives an [event](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html) that matches the [event pattern](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html) in the rule\. When an event matches, EventBridge sends the event to the specified [target](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-targets.html) and triggers the action defined in the rule\.

Event patterns have the same structure as the events they match\. An event pattern either matches an event or it doesn't\.

When creating a rule for an AMI state change event, you can include the following fields in the event pattern:

`"source": "aws.ec2“`  
Identifies that the event is from Amazon EC2\.

`"detail-type": "EC2 AMI State Change"`  
Identifies the event name\.

`"detail": { "ImageId": "ami-0123456789example", "State": "available", }`  
Provides the following information:  
+ The AMI ID – If you want to track a specific AMI\.
+ The state of the AMI \(`available`, `failed`, or `deregistered`\)\.

### Example: Create an EventBridge rule to send a notification<a name="create-eventbridge-rules-example"></a>

The following example creates an EventBridge rule to send an email, text message, or mobile push notification when any AMI is in the `available` state after the CreateImage operation has completed successfully\.

Before creating the EventBridge rule, you must create the Amazon SNS topic for the email, text message, or mobile push notification\.

**To create an EventBridge rule to send a notification when an AMI is created and in the `available` state**

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

   1. For **Event pattern**, for this example you’ll specify the following event pattern to match any `EC2 AMI State Change` event that is generated when an AMI enters the `available` state:

      ```
      {
       "source": ["aws.ec2"],
       "detail-type": ["EC2 AMI State Change"],
       "detail": {"State": ["available"]}
      }
      ```

      To add the event pattern, you can either use a template by choosing **Event pattern form**, or specify your own pattern by choosing **Custom pattern \(JSON editor\)**, as follows:

      1. To use a template to create the event pattern, do the following:

         1. Choose **Event pattern form**\.

         1. For **Event source**, choose **AWS services**\.

         1. For **AWS Service**, choose **EC2**\.

         1. For **Event type**, choose **EC2 AMI State Change**\.

         1. To customize the template, choose **Edit pattern** and make your changes to match the example event pattern\.

      1. To specify a custom event pattern, do the following:

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

For more information, see the following topics in the *Amazon EventBridge User Guide*:
+ [Amazon EventBridge events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html)
+ [Amazon EventBridge event patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html)
+ [Amazon EventBridge rules](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html)

For a tutorial on how to create a Lambda function and an EventBridge rule that runs the Lambda function, see [Tutorial: Log the state of an Amazon EC2 instance using EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/log-ec2-instance-state.html) in the *AWS Lambda Developer Guide*\.
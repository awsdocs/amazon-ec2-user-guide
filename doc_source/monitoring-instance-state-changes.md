# State change events for your instances<a name="monitoring-instance-state-changes"></a>

Amazon EC2 sends an `EC2 Instance State-change Notification` event to Amazon EventBridge when the state of an instance changes\.

The following is example data for this event\. In this example, the instance entered the `pending` state\.

```
{
   "id":"7bf73129-1428-4cd3-a780-95db273d1602",
   "detail-type":"EC2 Instance State-change Notification",
   "source":"aws.ec2",
   "account":"123456789012",
   "time":"2021-11-11T21:29:54Z",
   "region":"us-east-1",
   "resources":[
      "arn:aws:ec2:us-east-1:123456789012:instance/i-abcd1111"
   ],
   "detail":{
      "instance-id":"i-abcd1111",
      "state":"pending"
   }
}
```

The possible values for `state` are:
+ `pending`
+ `running`
+ `stopping`
+ `stopped`
+ `shutting-down`
+ `terminated`

When you launch or start an instance, it enters the `pending` state and then the `running` state\. When you stop an instance, it enters the `stopping` state and then the `stopped` state\. When you terminate an instance, it enters the `shutting-down` state and then the `terminated` state\.

## Get an email notification when an instance changes state<a name="ec2-events-eventbridge-example"></a>

To receive email notifications when your instance changes state, create an Amazon SNS topic and then create an EventBridge rule for the `EC2 Instance State-change Notification` event\.

**To create an SNS topic**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Topics**\.

1. Choose **Create topic**\.

1. For **Type**, choose **Standard**\.

1. For **Name**, enter a name for your topic\.

1. Choose **Create topic**\.

1. Choose **Create subscription**\.

1. For **Protocol**, choose **Email**\.

1. For **Endpoint**, enter the email address that receives the notifications\.

1. Choose **Create subscription**\.

1. You'll receive an email message with the following subject line: AWS Notification \- Subscription Confirmation\. Follow the directions to confirm your subscription\.

**To create an EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. For **Name**, enter a name for your rule\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. For **Event pattern**, do the following:

   1. For **Event source**, choose **AWS services**\.

   1. For **AWS service**, choose **EC2**\.

   1. For **Event type**, choose **EC2 Instance State\-change Notification**\.

   1. By default, we send notifications for any state change for any instance\. If you prefer, you can select specific states or specific instances\.

1. Choose **Next**\.

1. Specify a target as follows:

   1. For **Target types**, choose **AWS service**\.

   1. For **Select a target**, choose **SNS topic**\.

   1. For **Topic**, choose the SNS topic that you created in the previous procedure\.

1. Choose **Next**\.

1. \(Optional\) Add tags to your rule\.

1. Choose **Next**\.

1. Choose **Create rule**\.

1. To test your rule, initiate a state change\. For example, start a stopped instance, stop a running instance, or launch an instance\. You'll receive email messages with the following subject line: AWS Notification Message\. The body of the email contains the event data\.
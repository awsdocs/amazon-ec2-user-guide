# Monitor Capacity Reservations using EventBridge<a name="cr-eventbridge"></a>

AWS Health sends events to Amazon EventBridge when a Capacity Reservation in your account is below 20 percent usage over certain periods\. With EventBridge, you can establish rules that trigger programmatic actions in response to such events\. For example, you can create a rule that automatically cancels a Capacity Reservation when its utilization drops below 20 percent utilization over a 7\-day period\.

Events in EventBridge are represented as JSON objects\. The fields that are unique to the event are contained in the "detail" section of the JSON object\. The "event" field contains the event name\. The "result" field contains the completed status of the action that triggered the event\. For more information, see [Amazon EventBridge event patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html) in the *Amazon EventBridge User Guide*\.

For more information, see [ What Is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) in the *Amazon EventBridge User Guide*\.

**Topics**
+ [Events](#cr-eventbridge-events)
+ [Create an EventBridge rule](#cr-eventbridge-use)

## Events<a name="cr-eventbridge-events"></a>

AWS Health sends the following events when capacity usage for a Capacity Reservation is below 20 percent\.

**Topics**
+ [`AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION`](#cr-underutilization)
+ [`AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION_SUMMARY`](#cr-underutilization-summary)

### `AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION`<a name="cr-underutilization"></a>

The following is an example of an event that is generated when a newly created Capacity Reservation is below 20 percent capacity usage over a 24\-hour period\.

```
{
    "version": "0",
    "id": "b3e00086-f271-12a1-a36c-55e8ddaa130a",
    "detail-type": "AWS Health Event",
    "source": "aws.health",
    "account": "123456789012",
    "time": "2023-03-10T12:03:38Z",
    "region": "ap-south-1",
    "resources": [
        "cr-01234567890abcdef"
    ],
    "detail": {
        "eventArn": "arn:aws:health:ap-south-1::event/EC2/AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION/AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION_cr-01234567890abcdef-6211-4d50-9286-0c9fbc243f04",
        "service": "EC2",
        "eventTypeCode": "AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION",
        "eventTypeCategory": "accountNotification",
        "startTime": "Fri, 10 Mar 2023 12:03:38 GMT",
        "endTime": "Fri, 10 Mar 2023 12:03:38 GMT",
        "eventDescription": [
            {
                "language": "en_US",
                "latestDescription": "A description of the event will be provided here"
            }
        ],
        "affectedEntities": [
            {
                "entityValue": "cr-01234567890abcdef"
            }
        ]
    }
    }
```

### `AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION_SUMMARY`<a name="cr-underutilization-summary"></a>

The following is an example of an event that is generated when one or more Capacity Reservation is below 20 percent capacity usage over a 7\-day period\.

```
{ 
    "version": "0", "id":"7439d42b-3c7f-ad50-6a88-25e2a70977e2", 
    "detail-type": "AWS Health Event", 
    "source": "aws.health", 
    "account": "123456789012", 
    "time": "2023-03-07T06:06:01Z", 
    "region": "us-east-1", 
    "resources": [ 
        "cr-01234567890abcdef | us-east-1b | t3.medium | Linux/UNIX | 0.0%", 
        "cr-09876543210fedcba | us-east-1a | t3.medium | Linux/UNIX | 0.0%" 
    ], 
    "detail": { 
        "eventArn": "arn:aws:health:us-east-1::event/EC2/AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION_SUMMARY/AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION_SUMMARY_726c1732-d6f6-4037-b9b8-bec3c2d3ba65", 
        "service": "EC2", 
        "eventTypeCode": "AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION_SUMMARY", 
        "eventTypeCategory": "accountNotification", 
        "startTime": "Tue, 7 Mar 2023 06:06:01 GMT", 
        "endTime": "Tue, 7 Mar 2023 06:06:01 GMT", 
        "eventDescription": [
            { 
                "language": "en_US", 
                "latestDescription": "A description of the event will be provided here" 
            }
        ], 
        "affectedEntities": [
            { 
                "entityValue": "cr-01234567890abcdef | us-east-1b | t3.medium | Linux/UNIX | 0.0%" 
            }, 
            { 
                "entityValue": "cr-09876543210fedcba | us-east-1a | t3.medium | Linux/UNIX | 0.0%" 
            }
        ]
    }
}
```

## Create an EventBridge rule<a name="cr-eventbridge-use"></a>

To receive email notifications when your Capacity Reservation utilization drops below 20 percent, create an Amazon SNS topic, and then create an EventBridge rule for the `AWS_EC2_ODCR_UNDERUTILIZATION_NOTIFICATION` event\.

**To create the Amazon SNS topic**

1. Open the Amazon SNS console at [https://console\.aws\.amazon\.com/sns/v3/home](https://console.aws.amazon.com/sns/v3/home)\.

1. In the navigation pane, choose **Topics**, and then choose **Create topic**\.

1. For **Type**, choose **Standard**\.

1. For **Name**, enter a name for the new topic\.

1. Choose **Create topic**\.

1. Choose **Create subscription**\.

1. For **Protocol**, choose **Email**, and then for **Endpoint**, enter the email address that receives the notifications\.

1. Choose **Create subscription**\.

1. The email address entered above will receive email message with the following subject line: `AWS Notification - Subscription Confirmation`\. Follow the directions to confirm your subscription\.

**To create the EventBridge rule**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, and then choose **Create rule**\.

1. For **Name**, enter a name for the new rule\.

1. For **Rule type**, choose **Rule with an event pattern**\.

1. Choose **Next**\.

1. In the **Event pattern**, do the following:

   1. For **Event source**, choose **AWS services**\.

   1. For **AWS service**, choose **AWS Health**\.

   1. For **Event type**, choose **EC2 ODCR Underutilization Notification**\.

1. Choose **Next**\.

1. For **Target 1**, do the following:

   1. For **Target types**, choose **AWS service**\.

   1. For **Select a target**, choose **SNS topic**\.

   1. For **Topic**, choose the topic that you created earlier\.

1. Choose **Next** and then **Next** again\.

1. Choose **Create rule**\.
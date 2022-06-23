# EC2 instance rebalance recommendations<a name="rebalance-recommendations"></a>

An EC2 Instance *rebalance recommendation* is a signal that notifies you when a Spot Instance is at elevated risk of interruption\. The signal can arrive sooner than the [two\-minute Spot Instance interruption notice](spot-instance-termination-notices.md), giving you the opportunity to proactively manage the Spot Instance\. You can decide to rebalance your workload to new or existing Spot Instances that are not at an elevated risk of interruption\.

It is not always possible for Amazon EC2 to send the rebalance recommendation signal before the two\-minute Spot Instance interruption notice\. Therefore, the rebalance recommendation signal can arrive along with the two\-minute interruption notice\.

Rebalance recommendations are made available as a CloudWatch event and as an item in the [instance metadata](ec2-instance-metadata.md) on the Spot Instance\. Events are emitted on a best effort basis\.

**Note**  
Rebalance recommendations are only supported for Spot Instances that are launched after November 5, 2020 00:00 UTC\.

**Topics**
+ [Rebalance actions you can take](#rebalancing-actions)
+ [Monitor rebalance recommendation signals](#monitor-rebalance-recommendations)
+ [Services that use the rebalance recommendation signal](#services-using-rebalance-rec-signal)

## Rebalance actions you can take<a name="rebalancing-actions"></a>

These are some of the possible rebalancing actions that you can take:

Graceful shutdown  
When you receive the rebalance recommendation signal for a Spot Instance, you can start your instance shutdown procedures, which might include ensuring that processes are completed before stopping them\. For example, you can upload system or application logs to Amazon Simple Storage Service \(Amazon S3\), you can shut down Amazon SQS workers, or you can complete deregistration from the Domain Name System \(DNS\)\. You can also save your work in external storage and resume it at a later time\.

Prevent new work from being scheduled  
When you receive the rebalance recommendation signal for a Spot Instance, you can prevent new work from being scheduled on the instance, while continuing to use the instance until the scheduled work is completed\.

Proactively launch new replacement instances  
You can configure Auto Scaling groups, EC2 Fleet, or Spot Fleet to automatically launch replacement Spot Instances when a rebalance recommendation signal is emitted\. For more information, see [Amazon EC2 Auto Scaling Capacity Rebalancing](https://docs.aws.amazon.com/autoscaling/ec2/userguide/capacity-rebalance.html) in the *Amazon EC2 Auto Scaling User Guide*, and [Capacity Rebalancing](ec2-fleet-capacity-rebalance.md) for EC2 Fleet and [Capacity Rebalancing](spot-fleet-capacity-rebalance.md) for Spot Fleet in this user guide\.

## Monitor rebalance recommendation signals<a name="monitor-rebalance-recommendations"></a>

You can monitor the rebalance recommendation signal so that, when it is emitted, you can take the actions that are specified in the preceding section\. The rebalance recommendation signal is made available as an event that is sent to Amazon EventBridge \(formerly known as Amazon CloudWatch Events\) and as instance metadata on the Spot Instance\.

**Topics**
+ [Use Amazon EventBridge](#cp-eventbridge)
+ [Use instance metadata](#cp-instance-metadata)

### Use Amazon EventBridge<a name="cp-eventbridge"></a>

When the rebalance recommendation signal is emitted for a Spot Instance, the event for the signal is sent to Amazon EventBridge\. If EventBridge detects an event pattern that matches a pattern defined in a rule, EventBridge invokes a target \(or targets\) specified in the rule\.

The following is an example event for the rebalance recommendation signal\.

```
{
    "version": "0",
    "id": "12345678-1234-1234-1234-123456789012",
    "detail-type": "EC2 Instance Rebalance Recommendation",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "yyyy-mm-ddThh:mm:ssZ",
    "region": "us-east-2",
    "resources": ["arn:aws:ec2:us-east-2:123456789012:instance/i-1234567890abcdef0"],
    "detail": {
        "instance-id": "i-1234567890abcdef0"
    }
}
```

The following fields form the event pattern that is defined in the rule:

`"detail-type": "EC2 Instance Rebalance Recommendation"`  
Identifies that the event is a rebalance recommendation event

`"source": "aws.ec2"`  
Identifies that the event is from Amazon EC2

#### Create an EventBridge rule<a name="cp-eventbridge-rule"></a>

You can write an EventBridge rule and automate what actions to take when the event pattern matches the rule\.

The following example creates an EventBridge rule to send an email, text message, or mobile push notification every time Amazon EC2 emits a rebalance recommendation signal\. The signal is emitted as an `EC2 Instance Rebalance Recommendation` event, which triggers the action defined by the rule\.

**To create an EventBridge rule for a rebalance recommendation event**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. Enter a **Name** for the rule, and, optionally, a description\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Event pattern**\.

1. Under **Event matching pattern**, choose **Custom pattern**\.

1. In the **Event pattern** box, add the following pattern to match the `EC2 Instance Rebalance Recommendation` event, and then choose **Save**\.

   ```
   {
       "source": [ "aws.ec2" ],
       "detail-type": [ "EC2 Instance Rebalance Recommendation" ]
   }
   ```

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account's default event bus\.

1. Confirm that **Enable the rule on the selected event bus** is toggled on\. 

1. For **Target**, choose **SNS topic** to send an email, text message, or mobile push notification when the event occurs\.

1. For **Topic**, choose an existing topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

1. For **Configure input**, choose the input for the email, text message, or mobile push notification\.

1. Choose **Create**\.

For more information, see [Creating a rule for an AWS service](https://docs.aws.amazon.com/eventbridge/latest/userguide/create-eventbridge-rule.html) and [Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/filtering-examples-structure.html) in the *Amazon EventBridge User Guide*

### Use instance metadata<a name="cp-instance-metadata"></a>

The instance metadata category `events/recommendations/rebalance` provides the approximate time, in UTC, when the rebalance recommendation signal was emitted for a Spot Instance\.

We recommend that you check for rebalance recommendation signals every 5 seconds so that you don't miss an opportunity to act on the rebalance recommendation\.

If a Spot Instance receives a rebalance recommendation, the time that the signal was emitted is present in the instance metadata\. You can retrieve the time that the signal was emitted as follows\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/events/recommendations/rebalance
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/events/recommendations/rebalance
```

------

The following is example output, which indicates the time, in UTC, that the rebalance recommendation signal was emitted for the Spot Instance\.

```
{"noticeTime": "2020-10-27T08:22:00Z"}
```

If the signal has not been emitted for the instance, `events/recommendations/rebalance` is not present and you receive an HTTP 404 error when you try to retrieve it\.

## Services that use the rebalance recommendation signal<a name="services-using-rebalance-rec-signal"></a>

Amazon EC2 Auto Scaling, EC2 Fleet, and Spot Fleet use the rebalance recommendation signal to make it easy for you to maintain workload availability by proactively augmenting your fleet with a new Spot Instance before a running instance receives the two\-minute Spot Instance interruption notice\. You can have these services monitor and respond proactively to changes affecting the availability of your Spot Instances\. For more information, see the following:
+ [Amazon EC2 Auto Scaling Capacity Rebalancing](https://docs.aws.amazon.com/autoscaling/ec2/userguide/capacity-rebalance.html) in the *Amazon EC2 Auto Scaling User Guide*
+ [Capacity Rebalancing](ec2-fleet-capacity-rebalance.md) in the EC2 Fleet topic in this user guide
+ [Capacity Rebalancing](spot-fleet-capacity-rebalance.md) in the Spot Fleet topic in this user guide

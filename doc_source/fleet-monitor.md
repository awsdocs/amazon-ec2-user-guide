# Monitor fleet events using Amazon EventBridge<a name="fleet-monitor"></a>

When the state of an EC2 Fleet or Spot Fleet changes, the fleet emits a notification\. The notification is made available as an event that is sent to Amazon EventBridge \(formerly known as Amazon CloudWatch Events\)\. Events are emitted on a best effort basis\.

With Amazon EventBridge, you can create rules that trigger programmatic actions in response to an event\. For example, you can create two EventBridge rules, one that's triggered when a fleet state changes, and one that's triggered when an instance in the fleet is terminated\. You can configure the first rule so that, if the fleet state changes, the rule invokes an SNS topic to send an email notification to you\. You can configure the second rule so that, if an instance is terminated, the rule invokes a Lambda function to launch a new instance\.

**Topics**
+ [EC2 Fleet event types](ec2-fleet-event-types.md)
+ [Spot Fleet event types](spot-fleet-event-types.md)
+ [Create Amazon EventBridge rules](fleet_create-eventbridge-rules.md)
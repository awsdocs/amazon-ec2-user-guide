# Create Amazon EventBridge rules<a name="fleet_create-eventbridge-rules"></a>

When a notification of a state change is emitted for an EC2 Fleet or Spot Fleet, the event for the notification is sent to Amazon EventBridge\. If EventBridge detects an event pattern that matches a pattern defined in a rule, EventBridge invokes a target \(or targets\) specified in the rule\.

You can write an EventBridge rule and automate what actions to take when the event pattern matches the rule\.

**Topics**
+ [Create Amazon EventBridge rules to monitor EC2 Fleet events](ec2-fleet-using-eventbridge.md)
+ [Create Amazon EventBridge rules to monitor Spot Fleet events](spot-fleet-using-eventbridge.md)
# Automate Amazon EC2 using EventBridge<a name="automating_with_eventbridge"></a>

You can use Amazon EventBridge to automate your AWS services and respond automatically to system events, such as application availability issues or resource changes\. Events from AWS services are delivered to EventBridge in near real time\. You can create rules to indicate which events you're interested in, and the actions to take when an event matches a rule\. The actions that can be automatically triggered include the following:
+ Invoke an AWS Lambda function
+ Invoke Amazon EC2 Run Command
+ Relay the event to Amazon Kinesis Data Streams
+ Activate an AWS Step Functions state machine
+ Notify an Amazon SNS topic
+ Notify an Amazon SQS queue

The following are examples of how you can use EventBridge with Amazon EC2:
+ Activate a Lambda function whenever an instance enters the running state\.
+ Notify an Amazon SNS topic when an Amazon EBS volume is created or modified\.
+ Send a command to one or more Amazon EC2 instances using Amazon EC2 Run Command whenever a certain event in another AWS service occurs\.

For more information, see the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.

## Amazon EC2 event types<a name="ec2-events-eventbridge"></a>

Amazon EC2 supports the following event types:
+ [EC2 AMI State Change](monitor-ami-events.md#ami-events)
+ [EC2 Fleet Error](ec2-fleet-event-types.md#ec2-fleet-config-not-valid)
+ [EC2 Fleet Information](ec2-fleet-event-types.md#ec2-fleet-info)
+ [EC2 Fleet Instance Change](ec2-fleet-event-types.md#ec2-fleet-instance-change)
+ [EC2 Fleet Spot Instance Request Change](ec2-fleet-event-types.md#ec2-fleet-spot-instance-request-change)
+ [EC2 Fleet State Change](ec2-fleet-event-types.md#ec2-fleet-state-change)
+ [EC2 Instance Rebalance Recommendation](rebalance-recommendations.md#cp-eventbridge)
+ [EC2 Instance State\-change Notification](monitoring-instance-state-changes.md)
+ [EC2 Spot Fleet Error](spot-fleet-event-types.md#spot-fleet-config-not-valid)
+ [EC2 Spot Fleet Information](spot-fleet-event-types.md#spot-fleet-info)
+ [EC2 Spot Fleet Instance Change](spot-fleet-event-types.md#spot-fleet-instance-change)
+ [EC2 Spot Fleet Spot Instance Request Change](spot-fleet-event-types.md#spot-fleet-spot-instance-request-change)
+ [EC2 Spot Fleet State Change](spot-fleet-event-types.md#spot-fleet-state-change)
+ [EC2 Spot Instance Interruption Warning](spot-instance-termination-notices.md#ec2-spot-instance-interruption-warning-event)
+ [EC2 Spot Instance Request Fulfillment](spot-request-status.md#spot-request-fulfillment-event)

For information about the event types supported by Amazon EBS, see [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.
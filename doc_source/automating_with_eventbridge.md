# Automating Amazon EC2 with EventBridge<a name="automating_with_eventbridge"></a>

Amazon EventBridge enables you to automate your AWS services and respond automatically to system events such as application availability issues or resource changes\. Events from AWS services are delivered to EventBridge in near real time\. You can write simple rules to indicate which events are of interest to you, and the automated actions to take when an event matches a rule\. The actions that can be automatically triggered include the following:
+ Invoking an AWS Lambda function
+ Invoking Amazon EC2 Run Command
+ Relaying the event to Amazon Kinesis Data Streams
+ Activating an AWS Step Functions state machine
+ Notifying an Amazon SNS topic or an Amazon SQS queue

Some examples of using EventBridge with Amazon EC2 include:
+ Activating a Lambda function whenever a new Amazon EC2 instance starts\.
+ Notifying an Amazon SNS topic when an Amazon EBS volume is created or modified\.
+ Sending a command to one or more Amazon EC2 instances using Amazon EC2 Run Command whenever a certain event in another AWS service occurs\.

For more information, see the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.
# Using Amazon EventBridge to monitor Spot Fleet events<a name="spot-fleet-eventbridge"></a>

When the state of a Spot Fleet changes, the Spot Fleet emits a notification\. The notification is made available as an event that is sent to Amazon EventBridge \(formerly known as Amazon CloudWatch Events\)\.

With Amazon EventBridge, you can create rules that trigger programmatic actions in response to an event\. For example, you can create two EventBridge rules, one that's triggered when a fleet state changes, and one that's triggered when an instance in the fleet is terminated\. If the fleet state changes, the first EventBridge rule invokes an SNS topic to send an email notification to you\. If an instance is terminated, the second EventBridge rule invokes a Lambda function to launch a new instance\.

**Topics**
+ [Spot Fleet event types](#spot-fleet-event-types)
+ [Using Amazon EventBridge](#spot-fleet-using-eventbridge)

## Spot Fleet event types<a name="spot-fleet-event-types"></a>

There are five Spot Fleet event types\. For each event type, there are several sub\-types\.

The events are sent to EventBridge in JSON format\. The following fields in the event form the event pattern that is defined in the rule, and which trigger an action:

`"source": "aws.ec2spotfleet"`  
Identifies that the event is from Spot Fleet\.

`"detail-type": "EC2 Spot Fleet State Change"`  
Identifies the event type\.

`"sub-type": "submitted"`  
Identifies the event sub\-type\.

**Topics**
+ [EC2 Spot Fleet State Change](#spot-fleet-state-change)
+ [EC2 Spot Fleet Spot Instance Request Change](#spot-fleet-spot-instance-request-change)
+ [EC2 Spot Fleet Instance Change](#spot-fleet-instance-change)
+ [EC2 Spot Fleet Information](#spot-fleet-info)
+ [EC2 Spot Fleet Error](#spot-fleet-config-not-valid)

### EC2 Spot Fleet State Change<a name="spot-fleet-state-change"></a>

Spot Fleet sends an `EC2 Spot Fleet State Change` event to Amazon EventBridge when a Spot Fleet changes state\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "d1af1091-6cc3-2e24-203a-3b870e455d5b",
    "detail-type": "EC2 Spot Fleet State Change",
    "source": "aws.ec2spotfleet",
    "account": "123456789012",
    "time": "2020-11-09T08:57:06Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:spot-fleet-request/sfr-4b6d274d-0cea-4b2c-b3be-9dc627ad1f55"
    ],
    "detail": {
        "sub-type": "submitted"
    }
}
```

The possible values for `sub-type` are:

`submitted`  
The Spot Fleet request is being evaluated and Amazon EC2 is preparing to launch the target number of instances\. 

`active`  
The Spot Fleet request has been validated and Amazon EC2 is attempting to maintain the target number of running Spot Instances\.

`progress`  
The Spot Fleet request is in the process of being fulfilled\.

`cancelled_terminating`  
The Spot Fleet request is deleted and its instances are terminating\. The request remains in this state until all instances are terminated\. 

`cancelled_running`  
The Spot Fleet request is deleted and does not launch additional instances\. Its existing instances continue to run until they are interrupted or terminated\. The request remains in this state until all instances are interrupted or terminated\. 

`cancelled`  
The Spot Fleet request is deleted and has no running instances\. The Spot Fleet will be deleted two days after its instances are terminated\.

`modify_in_progress`  
The Spot Fleet request is being modified\. The request remains in this state until the modification is fully processed or the Spot Fleet request is deleted\.

`modify_succeeded`  
The Spot Fleet request was modified\.

`expired`  
The Spot Fleet request has expired\. If the request was created with `TerminateInstancesWithExpiration` set, a subsequent event indicates that the instances are terminated\.

### EC2 Spot Fleet Spot Instance Request Change<a name="spot-fleet-spot-instance-request-change"></a>

Spot Fleet sends an `EC2 Spot Fleet Spot Instance Request Change` event to Amazon EventBridge when a Spot Instance request in the fleet changes state\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "cd141ef0-14af-d670-a71d-fe46e9971bd2",
    "detail-type": "EC2 Spot Fleet Spot Instance Request Change",
    "source": "aws.ec2spotfleet",
    "account": "123456789012",
    "time": "2020-11-09T08:53:21Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:spot-fleet-request/sfr-a98d2133-941a-47dc-8b03-0f94c6852ad1"
    ],
    "detail": {
        "spot-instance-request-id": "sir-a2w9gc5h",
        "description": "SpotInstanceRequestId sir-a2w9gc5h, PreviousState: cancelled_running",
        "sub-type": "cancelled"
    }
}
```

The possible values for `sub-type` are:

`submitted`  
The request is submitted\.

`disabled`  
You stopped the Spot Instance\.

`active`  
The request is fulfilled and has an associated Spot Instance\.

`cancelled`  
You cancelled the request, or the request expired\.

### EC2 Spot Fleet Instance Change<a name="spot-fleet-instance-change"></a>

Spot Fleet sends an `EC2 Spot Fleet Instance Change` event to Amazon EventBridge when an instance in the fleet changes state\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "11591686-5bd7-bbaa-eb40-d46529c2710f",
    "detail-type": "EC2 Spot Fleet Instance Change",
    "source": "aws.ec2spotfleet",
    "account": "123456789012",
    "time": "2020-11-09T07:25:02Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:spot-fleet-request/sfr-c8a764a4-bedc-4b62-af9c-0095e6e3ba61"
    ],
    "detail": {
        "instance-id": "i-08b90df1e09c30c9b",
        "description": "{\"instanceType\":\"r4.2xlarge\",\"image\":\"ami-032930428bf1abbff\",\"productDescription\":\"Linux/UNIX\",\"availabilityZone\":\"us-east-1a\"}",
        "sub-type": "launched"
    }
}
```

The possible values for `sub-type` are:

`launched`  
A new instance was launched\.

`terminated`  
The instance was terminated\.

`termination_notified`  
An instance termination notification was sent\.

### EC2 Spot Fleet Information<a name="spot-fleet-info"></a>

Spot Fleet sends an `EC2 Spot Fleet Information` event to Amazon EventBridge when there is an error during fulfillment\. The information event does not block the fleet from attempting to fulfil its target capacity\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "73a60f70-3409-a66c-635c-7f66c5f5b669",
    "detail-type": "EC2 Spot Fleet Information",
    "source": "aws.ec2spotfleet",
    "account": "123456789012",
    "time": "2020-11-08T20:56:12Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:spot-fleet-request/sfr-2531ea06-af18-4647-8757-7d69c94971b1"
    ],
    "detail": {
        "description": "r3.8xlarge, ami-032930428bf1abbff, Linux/UNIX, us-east-1a, Spot bid price is less than Spot market price $0.5291",
        "sub-type": "launchSpecUnusable"
    }
}
```

The possible values for `sub-type` are:

`launchSpecUnusable`  
The price in a launch specification is not valid because it is below the Spot price or the Spot price is above the On\-Demand price\.

`fleetProgressHalted`  
The price in every launch specification is not valid\. A launch specification might become valid if the Spot price changes\.

`registerWithLoadBalancersFailed`  
An attempt to register instances with load balancers failed\. For more information, see the description of the event\.

`launchSpecTemporarilyBlacklisted`  
The configuration is not valid and several attempts to launch instances have failed\. For more information, see the description of the event\.

### EC2 Spot Fleet Error<a name="spot-fleet-config-not-valid"></a>

Spot Fleet sends an `EC2 Spot Fleet Error` event to Amazon EventBridge when there is an error during fulfillment\. The error event blocks the fleet from attempting to fulfil its target capacity\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "10adc4e7-675c-643e-125c-5bfa1b1ba5d2",
    "detail-type": "EC2 Spot Fleet Error",
    "source": "aws.ec2spotfleet",
    "account": "123456789012",
    "time": "2020-11-09T06:56:07Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:spot-fleet-request/sfr-38725d30-25f1-4f30-83ce-2907c56dba17"
    ],
    "detail": {
        "description": "r4.2xlarge, ami-032930428bf1abbff, Linux/UNIX: The associatePublicIPAddress parameter can only be specified for the network interface with DeviceIndex 0. ",
        "sub-type": "spotFleetRequestConfigurationInvalid"
    }
}
```

The possible values for `sub-type` are:

`allLaunchSpecsTemporarilyBlacklisted`  
None of the configurations are valid, and several attempts to launch instances have failed\. For more information, see the description of the event\.

`spotFleetRequestConfigurationInvalid`  
The configuration is not valid\. For more information, see the description of the event\.

`spotInstanceCountLimitExceeded`  
You’ve reached the limit on the number of Spot Instances that you can launch\.

## Using Amazon EventBridge<a name="spot-fleet-using-eventbridge"></a>

When a notification of a state change is emitted for a Spot Fleet, the event for the notification is sent to Amazon EventBridge\. If EventBridge detects an event pattern that matches a pattern defined in a rule, EventBridge invokes a target \(or targets\) specified in the rule\.

The preceding sections provide example events for the Spot Fleet notifications\. The event JSON files contain the fields that form the event pattern\.

The following fields form the event pattern that is defined in the rule:

`"source": "aws.ec2spotfleet"`  
Identifies that the event is from Spot Fleet\.

`"detail-type": "EC2 Spot Fleet State Change"`  
Identifies the event type\.

`"sub-type": "submitted"`  
Identifies the event sub\-type\.

You can write an EventBridge rule and automate what actions to take when the event pattern matches the rule\.

**Topics**
+ [Create an EventBridge rule to send a notification](#eventbridge-send-notification)
+ [Create an EventBridge rule to trigger a Lambda function](#eventbridge-trigger-lambda)

### Create an EventBridge rule to send a notification<a name="eventbridge-send-notification"></a>

The following example creates an EventBridge rule to send an email, text message, or mobile push notification every time that Amazon EC2 emits a Spot Fleet notification\. The signal in this example is emitted as an `EC2 Spot Fleet State Change` event, which triggers the action defined by the rule\.

**To create an EventBridge rule to send a notification when a Spot Fleet state changes**

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. Choose **Create rule**\.

1. Enter a **Name** for the rule, and, optionally, a description\.

   A rule can't have the same name as another rule in the same Region and on the same event bus\.

1. For **Define pattern**, choose **Event pattern**\.

1. Under **Event matching pattern**, choose **Custom pattern**\.

1. In the **Event pattern** box, add the following pattern to match the `EC2 Spot Fleet State Change` event, and then choose **Save**\.

   ```
   {
       "source": [ "aws.ec2spotfleet" ],
       "detail-type": [ "EC2 Spot Fleet State Change" ],
       "sub-type": [ "submitted" ]
   }
   ```

1. For **Select event bus**, choose **AWS default event bus**\. When an AWS service in your account emits an event, it always goes to your account's default event bus\.

1. Confirm that **Enable the rule on the selected event bus** is toggled on\. 

1. For **Target**, choose **SNS topic** to send an email, text message, or mobile push notification when the event occurs\.

1. For **Topic**, choose an existing topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

1. For **Configure input**, choose the input for the email, text message, or mobile push notification\.

1. Choose **Create**\.

For more information, see [Creating a rule for an AWS service](https://docs.aws.amazon.com/eventbridge/latest/userguide/create-eventbridge-rule.html) and [Event Patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/filtering-examples-structure.html) in the *Amazon EventBridge User Guide*

### Create an EventBridge rule to trigger a Lambda function<a name="eventbridge-trigger-lambda"></a>

The following example creates an EventBridge rule to trigger a Lambda function every time that Amazon EC2 emits a Spot Fleet notification\. The signal in this example is emitted as an `EC2 Spot Fleet Instance Change` event, which triggers the action defined by the rule\. Before creating the EventBridge rule, you must create the Lambda function\.

**To create an EventBridge rule to trigger a Lambda function when an instance in a Spot Fleet changes state**

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

      1. For **Service name**, choose **EC2 Spot Fleet**\.

      1. For **Event type**, select the required event type\. For this example, choose **EC2 Spot Fleet Instance Change**\.

   1. If you choose **Custom pattern**, do the following:

      1. In the **Event pattern** box, add the following pattern to match the `EC2 Spot Fleet Instance Change` event and `launched` sub\-type for this example, and then choose **Save**\.

        ```
        {
            "source": [ "aws.ec2spotfleet" ],
            "detail-type": [ "EC2 Spot Fleet Instance Change" ],
            "sub-type": [ "launched" ]
        }
        ```

1. For **Target**, choose **Lambda function**, and for **Function**, choose the function that you created to respond when the event occurs\.

1. Choose **Create**\.

   In this example, the Lambda function will be triggered when the `EC2 Fleet Instance Change` event with the sub\-type `launched` occurs\.

For a tutorial on how to create a Lambda function and an EventBridge rule that runs the Lambda function, see [Tutorial: Log the State of an Amazon EC2 Instance Using EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/log-ec2-instance-state.html) in the *AWS Lambda Developer Guide*\.
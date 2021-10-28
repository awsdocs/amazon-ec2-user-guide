# EC2 Fleet event types<a name="ec2-fleet-event-types"></a>

**Note**  
Only fleets of type `maintain` and `request` emit events\. Fleets of type `instant` do not emit events because they submit synchronous one\-time requests, and the state of the fleet is known immediately in the response\.

There are five EC2 Fleet event types\. For each event type, there are several sub\-types\.

The events are sent to EventBridge in JSON format\. The following fields in the event form the event pattern that is defined in the rule, and which trigger an action:

`"source": "aws.ec2fleet"`  
Identifies that the event is from EC2 Fleet\.

`"detail-type": "EC2 Fleet State Change"`  
Identifies the event type\.

`"detail": { "sub-type": "submitted" }`  
Identifies the event sub\-type\.

**Topics**
+ [EC2 Fleet State Change](#ec2-fleet-state-change)
+ [EC2 Fleet Spot Instance Request Change](#ec2-fleet-spot-instance-request-change)
+ [EC2 Fleet Instance Change](#ec2-fleet-instance-change)
+ [EC2 Fleet Information](#ec2-fleet-info)
+ [EC2 Fleet Error](#ec2-fleet-config-not-valid)

## EC2 Fleet State Change<a name="ec2-fleet-state-change"></a>

EC2 Fleet sends an `EC2 Fleet State Change` event to Amazon EventBridge when an EC2 Fleet changes state\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "715ed6b3-b8fc-27fe-fad6-528c7b8bf8a2",
    "detail-type": "EC2 Fleet State Change",
    "source": "aws.ec2fleet",
    "account": "123456789012",
    "time": "2020-11-09T09:00:20Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:fleet/fleet-598fb973-87b7-422d-be4d-6b0809bfff0a"
    ],
    "detail": {
        "sub-type": "active"
    }
}
```

The possible values for `sub-type` are:

`active`  
The EC2 Fleet request has been validated and Amazon EC2 is attempting to maintain the target number of running instances\.

`cancelled`  
The EC2 Fleet request is canceled and has no running instances\. The EC2 Fleet will be deleted two days after its instances are terminated\.

`cancelled_running`  
The EC2 Fleet request is canceled and does not launch additional instances\. Its existing instances continue to run until they are interrupted or terminated\. The request remains in this state until all instances are interrupted or terminated\. 

`cancelled_terminating`  
The EC2 Fleet request is canceled and its instances are terminating\. The request remains in this state until all instances are terminated\. 

`expired`  
The EC2 Fleet request has expired\. If the request was created with `TerminateInstancesWithExpiration` set, a subsequent `terminated` event indicates that the instances are terminated\.

`modify_in_progress`  
The EC2 Fleet request is being modified\. The request remains in this state until the modification is fully processed\.

`modify_succeeded`  
The EC2 Fleet request was modified\.

`submitted`  
The EC2 Fleet request is being evaluated and Amazon EC2 is preparing to launch the target number of instances\. 

`progress`  
The EC2 Fleet request is in the process of being fulfilled\.

## EC2 Fleet Spot Instance Request Change<a name="ec2-fleet-spot-instance-request-change"></a>

EC2 Fleet sends an `EC2 Fleet Spot Instance Request Change` event to Amazon EventBridge when a Spot Instance request in the fleet changes state\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "19331f74-bf4b-a3dd-0f1b-ddb1422032b9",
    "detail-type": "EC2 Fleet Spot Instance Request Change",
    "source": "aws.ec2fleet",
    "account": "123456789012",
    "time": "2020-11-09T09:00:05Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:fleet/fleet-83fd4e48-552a-40ef-9532-82a3acca5f10"
    ],
    "detail": {
        "spot-instance-request-id": "sir-rmqske6h",
        "description": "SpotInstanceRequestId sir-rmqske6h, PreviousState: cancelled_running",
        "sub-type": "cancelled"
    }
}
```

The possible values for `sub-type` are:

`active`  
The Spot Instance request is fulfilled and has an associated Spot Instance\.

`cancelled`  
You cancelled the Spot Instance request, or the Spot Instance request expired\.

`disabled`  
You stopped the Spot Instance\.

`submitted`  
The Spot Instance request is submitted\.

## EC2 Fleet Instance Change<a name="ec2-fleet-instance-change"></a>

EC2 Fleet sends an `EC2 Fleet Instance Change` event to Amazon EventBridge when an instance in the fleet changes state\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "542ce428-c8f1-0608-c015-e8ed6522c5bc",
    "detail-type": "EC2 Fleet Instance Change",
    "source": "aws.ec2fleet",
    "account": "123456789012",
    "time": "2020-11-09T09:00:23Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:fleet/fleet-598fb973-87b7-422d-be4d-6b0809bfff0a"
    ],
    "detail": {
        "instance-id": "i-0c594155dd5ff1829",
        "description": "{\"instanceType\":\"c5.large\",\"image\":\"ami-6057e21a\",\"productDescription\":\"Linux/UNIX\",\"availabilityZone\":\"us-east-1d\"}",
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
An instance termination notification was sent when a Spot Instance was terminated by Amazon EC2 during scale\-down, when the target capacity of the fleet was modified down, for example, from a target capacity of 4 to a target capacity of 3\.

## EC2 Fleet Information<a name="ec2-fleet-info"></a>

EC2 Fleet sends an `EC2 Fleet Information` event to Amazon EventBridge when there is an error during fulfillment\. The information event does not block the fleet from attempting to fulfil its target capacity\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "76529817-d605-4571-7224-d36cc1b2c0c4",
    "detail-type": "EC2 Fleet Information",
    "source": "aws.ec2fleet",
    "account": "123456789012",
    "time": "2020-11-09T08:17:07Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:fleet/fleet-8becf5fe-bb9e-415d-8f54-3fa5a8628b91"
    ],
    "detail": {
        "description": "r3.8xlarge, ami-032930428bf1abbff, Linux/UNIX, us-east-1a, Spot bid price is less than Spot market price $0.5291",
        "sub-type": "launchSpecUnusable"
    }
}
```

The possible values for `sub-type` are:

`fleetProgressHalted`  
The price in every launch specification is not valid because it is below the Spot price \(all the launch specifications have produced `launchSpecUnusable` events\)\. A launch specification might become valid if the Spot price changes\.

`launchSpecTemporarilyBlacklisted`  
The configuration is not valid and several attempts to launch instances have failed\. For more information, see the description of the event\.

`launchSpecUnusable`  
The price in a launch specification is not valid because it is below the Spot price\.

`registerWithLoadBalancersFailed`  
An attempt to register instances with load balancers failed\. For more information, see the description of the event\.

## EC2 Fleet Error<a name="ec2-fleet-config-not-valid"></a>

EC2 Fleet sends an `EC2 Fleet Error` event to Amazon EventBridge when there is an error during fulfillment\. The error event blocks the fleet from attempting to fulfil its target capacity\.

The following is example data for this event\. 

```
{
    "version": "0",
    "id": "69849a22-6d0f-d4ce-602b-b47c1c98240e",
    "detail-type": "EC2 Fleet Error",
    "source": "aws.ec2fleet",
    "account": "123456789012",
    "time": "2020-10-07T01:44:24Z",
    "region": "us-east-1",
    "resources": [
        "arn:aws:ec2:us-east-1:123456789012:fleet/fleet-9bb19bc6-60d3-4fd2-ae47-d33e68eafa08"
    ],
    "detail": {
        "description": "m3.large, ami-00068cd7555f543d5, Linux/UNIX: IPv6 is not supported for the instance type 'm3.large'. ",
        "sub-type": "spotFleetRequestConfigurationInvalid"
    }
}
```

The possible values for `sub-type` are:

`iamFleetRoleInvalid`  
The EC2 Fleet does not have the required permissions to either launch or terminate an instance\.

`allLaunchSpecsTemporarilyBlacklisted`  
None of the configurations are valid, and several attempts to launch instances have failed\. For more information, see the description of the event\.

`spotInstanceCountLimitExceeded`  
You’ve reached the limit on the number of Spot Instances that you can launch\.

`spotFleetRequestConfigurationInvalid`  
The configuration is not valid\. For more information, see the description of the event\.
# Spot Fleet event types<a name="spot-fleet-event-types"></a>

There are five Spot Fleet event types\. For each event type, there are several sub\-types\.

The events are sent to EventBridge in JSON format\. The following fields in the event form the event pattern that is defined in the rule, and which trigger an action:

`"source": "aws.ec2spotfleet"`  
Identifies that the event is from Spot Fleet\.

`"detail-type": "EC2 Spot Fleet State Change"`  
Identifies the event type\.

`"detail": { "sub-type": "submitted" }`  
Identifies the event sub\-type\.

**Topics**
+ [EC2 Spot Fleet State Change](#spot-fleet-state-change)
+ [EC2 Spot Fleet Spot Instance Request Change](#spot-fleet-spot-instance-request-change)
+ [EC2 Spot Fleet Instance Change](#spot-fleet-instance-change)
+ [EC2 Spot Fleet Information](#spot-fleet-info)
+ [EC2 Spot Fleet Error](#spot-fleet-config-not-valid)

## EC2 Spot Fleet State Change<a name="spot-fleet-state-change"></a>

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

## EC2 Spot Fleet Spot Instance Request Change<a name="spot-fleet-spot-instance-request-change"></a>

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

## EC2 Spot Fleet Instance Change<a name="spot-fleet-instance-change"></a>

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

## EC2 Spot Fleet Information<a name="spot-fleet-info"></a>

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

## EC2 Spot Fleet Error<a name="spot-fleet-config-not-valid"></a>

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
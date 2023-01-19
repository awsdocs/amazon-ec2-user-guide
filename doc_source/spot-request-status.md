# Spot request status<a name="spot-request-status"></a>

To help you track your Spot Instance requests and plan your use of Spot Instances, use the request status provided by Amazon EC2\. For example, the request status can provide the reason why your Spot request isn't fulfilled yet, or list the constraints that are preventing the fulfillment of your Spot request\.

At each step of the process—also called the Spot request *lifecycle*—specific events determine successive request states\.

**Topics**
+ [Lifecycle of a Spot request](#spot-instances-request-status-lifecycle)
+ [Get request status information](#get-spot-instance-request-status)
+ [Spot request status codes](#spot-instance-request-status-understand)
+ [EC2 Spot Instance Request Fulfillment event](#spot-request-fulfillment-event)

## Lifecycle of a Spot request<a name="spot-instances-request-status-lifecycle"></a>

The following diagram shows you the paths that your Spot request can follow throughout its lifecycle, from submission to termination\. Each step is depicted as a node, and the status code for each node describes the status of the Spot request and Spot Instance\.

![\[Life cycle of a Spot Instance request.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot-request-status-diagram.png)

**Pending evaluation**  
As soon as you create a Spot Instance request, it goes into the `pending-evaluation` state unless one or more request parameters are not valid \(`bad-parameters`\)\.


| Status code | Request state | Instance state | 
| --- | --- | --- | 
| pending\-evaluation | open | Not applicable | 
| bad\-parameters | closed | Not applicable | 

**Holding**  
If one or more request constraints are valid but can't be met yet, or if there is not enough capacity, the request goes into a holding state waiting for the constraints to be met\. The request options affect the likelihood of the request being fulfilled\. For example, if there is no capacity, your request stays in a holding state until there is available capacity\. If you specify an Availability Zone group, the request stays in a holding state until the Availability Zone constraint is met\.

In the event of an outage of one of the Availability Zones, there is a chance that the spare EC2 capacity available for Spot Instance requests in other Availability Zones can be affected\.


| Status code | Request state | Instance state | 
| --- | --- | --- | 
| capacity\-not\-available | open |  Not applicable  | 
| price\-too\-low | open |  Not applicable  | 
| not\-scheduled\-yet | open |  Not applicable  | 
| launch\-group\-constraint | open |  Not applicable  | 
| az\-group\-constraint | open |  Not applicable  | 
|  placement\-group\-constraint  |  open  |  Not applicable  | 
|  constraint\-not\-fulfillable  |  open  |  Not applicable  | 

**Pending evaluation/fulfillment\-terminal**  
Your Spot Instance request can go to a `terminal` state if you create a request that is valid only during a specific time period and this time period expires before your request reaches the pending fulfillment phase\. It might also happen if you cancel the request, or if a system error occurs\.


| Status code | Request state | Instance state | 
| --- | --- | --- | 
|  schedule\-expired  |  cancelled  |  Not applicable  | 
|  canceled\-before\-fulfillment ¹  |  cancelled  |  Not applicable  | 
|  bad\-parameters  |  failed  |  Not applicable  | 
|  system\-error  |  closed  |  Not applicable  | 

¹ If you cancel the request\.

**Pending fulfillment**  
When the constraints you specified \(if any\) are met, your Spot request goes into the `pending-fulfillment` state\.

At this point, Amazon EC2 is getting ready to provision the instances that you requested\. If the process stops at this point, it is likely to be because it was canceled by the user before a Spot Instance was launched\. It might also be because an unexpected system error occurred\.


| Status code | Request state | Instance state | 
| --- | --- | --- | 
|  pending\-fulfillment  |  open  |  Not applicable  | 

**Fulfilled**  
When all the specifications for your Spot Instances are met, your Spot request is fulfilled\. Amazon EC2 launches the Spot Instances, which can take a few minutes\. If a Spot Instance is hibernated or stopped when interrupted, it remains in this state until the request can be fulfilled again or the request is canceled\.


| Status code | Request state | Instance state | 
| --- | --- | --- | 
|  fulfilled  |  active  |  pending → running  | 
|  fulfilled  |  active  |  stopped → running  | 

If you stop a Spot Instance, your Spot request goes into the `marked-for-stop` or `instance-stopped-by-user` state until the Spot Instance can be started again or the request is cancelled\. 


| Status code | Request state | Instance state | 
| --- | --- | --- | 
|  marked\-for\-stop  | active |  stopping  | 
|  instance\-stopped\-by\-user ¹  |  disabled or cancelled ²  |  stopped  | 

¹ A Spot Instance goes into the `instance-stopped-by-user` state if you stop the instance or run the shutdown command from the instance\. After you've stopped the instance, you can start it again\. On restart, the Spot Instance request returns to the `pending-evaluation` state and then Amazon EC2 launches a new Spot Instance when the constraints are met\. 

² The Spot request state is `disabled` if you stop the Spot Instance but do not cancel the request\. The request state is `cancelled` if your Spot Instance is stopped and the request expires\.

**Fulfilled\-terminal**  
Your Spot Instances continue to run as long as there is available capacity for your instance type, and you don't terminate the instance\. If Amazon EC2 must terminate your Spot Instances, the Spot request goes into a terminal state\. A request also goes into the terminal state if you cancel the Spot request or terminate the Spot Instances\.


| Status code | Request state | Instance state | 
| --- | --- | --- | 
|  request\-canceled\-and\-instance\-running  |  cancelled  |  running  | 
|  marked\-for\-stop  |  active  |  running  | 
|  marked\-for\-termination  |  active  |  running  | 
|  instance\-stopped\-by\-price  |  disabled  |  stopped  | 
|  instance\-stopped\-by\-user  |  disabled  |  stopped  | 
|  instance\-stopped\-no\-capacity  |  disabled  |  stopped  | 
|  instance\-terminated\-by\-price  |  closed \(one\-time\), open \(persistent\)  |  terminated  | 
|  instance\-terminated\-by\-schedule  |  closed  |  terminated  | 
|  instance\-terminated\-by\-service  |  cancelled  |  terminated  | 
|  instance\-terminated\-by\-user  |  closed or cancelled ¹  |  terminated  | 
|  instance\-terminated\-no\-capacity  |  closed \(one\-time\), open \(persistent\)  |  running †  | 
|  instance\-terminated\-no\-capacity  |  closed \(one\-time\), open \(persistent\)  |  terminated  | 
|  instance\-terminated\-launch\-group\-constraint  |  closed \(one\-time\), open \(persistent\)  |  terminated  | 

¹ The request state is `closed` if you terminate the instance but do not cancel the request\. The request state is `cancelled` if you terminate the instance and cancel the request\. Even if you terminate a Spot Instance before you cancel its request, there might be a delay before Amazon EC2 detects that your Spot Instance was terminated\. In this case, the request state can either be `closed` or `cancelled`\.

† When Amazon EC2 interrupts a Spot Instance if it needs the capacity back *and* the instance is configured to *terminate* on interruption, the status is immediately set to `instance-terminated-no-capacity` \(it is not set to `marked-for-termination`\)\. However, the instance remains in the `running` state for 2 minutes to reflect the 2\-minute period when the instance receives the Spot Instance interruption notice\. After 2 minutes, the instance state is set to `terminated`\.

**Persistent requests**  
When your Spot Instances are terminated \(either by you or Amazon EC2\), if the Spot request is a persistent request, it returns to the `pending-evaluation` state and then Amazon EC2 can launch a new Spot Instance when the constraints are met\.

## Get request status information<a name="get-spot-instance-request-status"></a>

You can get request status information using the AWS Management Console or a command line tool\.

**To get request status information \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests** and select the Spot request\.

1. To check the status, on the **Description** tab, check the **Status** field\.

**To get request status information using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) \(AWS CLI\)
+ [Get\-EC2SpotInstanceRequest](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SpotInstanceRequest.html) \(AWS Tools for Windows PowerShell\)

## Spot request status codes<a name="spot-instance-request-status-understand"></a>

Spot request status information is composed of a status code, the update time, and a status message\. Together, these help you determine the disposition of your Spot request\.

The following are the Spot request status codes:

`az-group-constraint`  
Amazon EC2 cannot launch all the instances you requested in the same Availability Zone\.

`bad-parameters`  
One or more parameters for your Spot request are not valid \(for example, the AMI you specified does not exist\)\. The status message indicates which parameter is not valid\.

`canceled-before-fulfillment`  
The user canceled the Spot request before it was fulfilled\.

`capacity-not-available`  
There is not enough capacity available for the instances that you requested\.

`constraint-not-fulfillable`  
The Spot request can't be fulfilled because one or more constraints are not valid \(for example, the Availability Zone does not exist\)\. The status message indicates which constraint is not valid\.

`fulfilled`  
The Spot request is `active`, and Amazon EC2 is launching your Spot Instances\.

`instance-stopped-by-price`  
Your instance was stopped because the Spot price exceeded your maximum price\.

`instance-stopped-by-user`  
Your instance was stopped because a user stopped the instance or ran the shutdown command from the instance\.

`instance-stopped-no-capacity`  
Your instance was stopped due to EC2 capacity management needs\.

`instance-terminated-by-price`  
Your instance was terminated because the Spot price exceeded your maximum price\. If your request is persistent, the process restarts, so your request is pending evaluation\.

`instance-terminated-by-schedule`  
Your Spot Instance was terminated at the end of its scheduled duration\.

`instance-terminated-by-service`  
Your instance was terminated from a stopped state\.

`instance-terminated-by-user` or `spot-instance-terminated-by-user`  
You terminated a Spot Instance that had been fulfilled, so the request state is `closed` \(unless it's a persistent request\) and the instance state is `terminated`\.

`instance-terminated-launch-group-constraint`  
One or more of the instances in your launch group was terminated, so the launch group constraint is no longer fulfilled\.

`instance-terminated-no-capacity`  
Your instance was terminated due to standard capacity management processes\.

`launch-group-constraint`  
Amazon EC2 cannot launch all the instances that you requested at the same time\. All instances in a launch group are started and terminated together\.

`limit-exceeded`  
The limit on the number of EBS volumes or total volume storage was exceeded\. For more information about these limits and how to request an increase, see [Amazon EBS Limits](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html#limits_ebs) in the *Amazon Web Services General Reference*\.

`marked-for-stop`  
The Spot Instance is marked for stopping\.

`marked-for-termination`  
The Spot Instance is marked for termination\.

`not-scheduled-yet`  
The Spot request is not evaluated until the scheduled date\.

`pending-evaluation`  
After you make a Spot Instance request, it goes into the `pending-evaluation` state while the system evaluates the parameters of your request\.

`pending-fulfillment`  
Amazon EC2 is trying to provision your Spot Instances\.

`placement-group-constraint`  
The Spot request can't be fulfilled yet because a Spot Instance can't be added to the placement group at this time\.

`price-too-low`  
The request can't be fulfilled yet because your maximum price is below the Spot price\. In this case, no instance is launched and your request remains `open`\.

`request-canceled-and-instance-running`  
You canceled the Spot request while the Spot Instances are still running\. The request is `cancelled`, but the instances remain `running`\.

`schedule-expired`  
The Spot request expired because it was not fulfilled before the specified date\.

`system-error`  
There was an unexpected system error\. If this is a recurring issue, please contact AWS Support for assistance\.

## EC2 Spot Instance Request Fulfillment event<a name="spot-request-fulfillment-event"></a>

When a Spot Instance request is fulfilled, Amazon EC2 sends an EC2 Spot Instance Request Fulfillment event to Amazon EventBridge\. You can create a rule to take an action whenever this event occurs, such as invoking a Lambda function or notifying an Amazon SNS topic\.

The following is example data for this event\.

```
{
    "version": "0",
    "id": "01234567-1234-0123-1234-012345678901",
    "detail-type": "EC2 Spot Instance Request Fulfillment",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "yyyy-mm-ddThh:mm:ssZ",
    "region": "us-east-2",
    "resources": ["arn:aws:ec2:us-east-2:123456789012:instance/i-1234567890abcdef0"],
    "detail": {
        "spot-instance-request-id": "sir-1a2b3c4d",
        "instance-id": "i-1234567890abcdef0"
    }
}
```

For more information, see the [Amazon EventBridge User Guide](https://docs.aws.amazon.com/eventbridge/latest/userguide/)\.
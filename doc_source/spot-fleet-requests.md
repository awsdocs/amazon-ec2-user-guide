# Spot Fleet Requests<a name="spot-fleet-requests"></a>

To use a Spot Fleet, you create a Spot Fleet request that includes the target capacity, one or more launch specifications for the instances, and the maximum price that you are willing to pay\. Amazon EC2 attempts to maintain your Spot Fleet's target capacity as Spot prices change\. For more information, see [How Spot Fleet Works](spot-fleet.md)\.

There are two types of Spot Fleet requests: `request` and `maintain`\. You can create a Spot Fleet to submit a one\-time request for your desired capacity, or require it to maintain a target capacity over time\. Both types of requests benefit from Spot Fleet's allocation strategy\.

When you make a one\-time request, Spot Fleet places the required requests but will not attempt to replenish Spot Instances if capacity is diminished\. If capacity is not available, Spot Fleet does not submit requests in alternative Spot pools\.

To maintain a target capacity, Spot Fleet places requests to meet the target capacity and automatically replenish any interrupted instances\.

It is not possible to modify the target capacity of a one\-time request after it's been submitted\. To change the target capacity, cancel the request and submit a new one\.

A Spot Fleet request remains active until it expires or you cancel it\. When you cancel a Spot Fleet request, you may specify whether canceling your Spot Fleet request terminates the Spot Instances in your Spot Fleet\.

Each launch specification includes the information that Amazon EC2 needs to launch an instance—such as an AMI, instance type, subnet or Availability Zone, and one or more security groups\.


+ [Spot Fleet Request States](#spot-fleet-states)
+ [Spot Fleet Prerequisites](#spot-fleet-prerequisites)
+ [Spot Fleet and IAM Users](#spot-fleet-iam-users)
+ [Spot Fleet Health Checks](#spot-fleet-health-checks)
+ [Planning a Spot Fleet Request](#plan-spot-fleet)
+ [Service\-Linked Role for Spot Fleet Requests](#service-linked-roles-spot-fleet-requests)
+ [Creating a Spot Fleet Request](#create-spot-fleet)
+ [Monitoring Your Spot Fleet](#manage-spot-fleet)
+ [Modifying a Spot Fleet Request](#modify-spot-fleet)
+ [Canceling a Spot Fleet Request](#cancel-spot-fleet)
+ [Spot Fleet Example Configurations](spot-fleet-examples.md)

## Spot Fleet Request States<a name="spot-fleet-states"></a>

A Spot Fleet request can be in one of the following states:

+ `submitted`—The Spot Fleet request is being evaluated and Amazon EC2 is preparing to launch the target number of Spot Instances\.

+ `active`—The Spot Fleet has been validated and Amazon EC2 is attempting to maintain the target number of running Spot Instances\. The request remains in this state until it is modified or cancelled\.

+ `modifying`—The Spot Fleet request is being modified\. The request remains in this state until the modification is fully processed or the Spot Fleet is cancelled\. A one\-time `request` cannot be modified, and this state does not apply to such Spot requests\.

+ `cancelled_running`—The Spot Fleet is cancelled and will not launch additional Spot Instances\. Its existing Spot Instances continue to run until they are interrupted or terminated\. The request remains in this state until all instances are interrupted or terminated\.

+ `cancelled_terminating`—The Spot Fleet is cancelled and its Spot Instances are terminating\. The request remains in this state until all instances are terminated\.

+ `cancelled`—The Spot Fleet is cancelled and has no running Spot Instances\. The Spot Fleet request is deleted two days after its instances were terminated\.

The following illustration represents the transitions between the request states\. If you exceed your Spot Fleet limits, the request is cancelled immediately\.

![\[Spot Fleet request states\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Spot_fleet_states.png)

## Spot Fleet Prerequisites<a name="spot-fleet-prerequisites"></a>

If you use the Amazon EC2 console to create a Spot Fleet, it creates a role named aws\-ec2\-spot\-fleet\-tagging\-role that grants the Spot Fleet permission to request, launch, terminate, and tag instances on your behalf\. This role is selected when you create your Spot Fleet request\. If you use the AWS CLI or an API instead, you must ensure that this role exists\. You can either use the Request Spot Instances wizard \(the role is created when you advance to the second page of the wizard\) or use the IAM console as follows\.

**To create the IAM role for Spot Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. On the **Select type of trusted entity** page, choose **AWS service**, **EC2**, **EC2 \- Spot Fleet Tagging**, and then choose **Next: Permissions**\.

1. On the **Attached permissions policy** page, choose **Next:Review**\.

1. On the **Review** page, type a name for the role \(for example, **aws\-ec2\-spot\-fleet\-tagging\-role**\) and then choose **Create role**\.

## Spot Fleet and IAM Users<a name="spot-fleet-iam-users"></a>

If your IAM users will create or manage a Spot Fleet, be sure to grant them the required permissions as follows\.

**To grant an IAM user permissions for Spot Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, and then choose **Create policy**\.

1. On the **Create policy** page, choose the **JSON** tab, replace the text with the following, and choose **Review policy**\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "ec2:*"
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": [
                 "iam:ListRoles",
                 "iam:PassRole",
                 "iam:ListInstanceProfiles"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   The `ec2:*` grants an IAM user permission to call all Amazon EC2 API actions\. To limit the user to specific Amazon EC2 API actions, specify those actions instead\.

   An IAM user must have permission to call the `iam:ListRoles` action to enumerate existing IAM roles, the `iam:PassRole` action to specify the Spot Fleet role, and the `iam:ListInstanceProfiles` action to enumerate existing instance profiles\.

   \(Optional\) To enable an IAM user to create roles or instance profiles using the IAM console, you must also add the following actions to the policy:

   + `iam:AddRoleToInstanceProfile`

   + `iam:AttachRolePolicy`

   + `iam:CreateInstanceProfile`

   + `iam:CreateRole`

   + `iam:GetRole`

   + `iam:ListPolicies`

1. On the **Review policy** page, type a policy name and description, and then choose **Create policy**\.

1. In the navigation pane, choose **Users**, and then choose the user\.

1. On the **Permissions** tab, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\. Select the policy you created above and choose **Next: Review**\.

1. Choose **Add permissions**\.

## Spot Fleet Health Checks<a name="spot-fleet-health-checks"></a>

Spot Fleet checks the health status of the Spot Instances in the fleet every two minutes\. The health status of an instance is either `healthy` or `unhealthy`\. Spot Fleet determines the health status of an instance using the status checks provided by Amazon EC2\. If the status of either the instance status check or the system status check is `impaired` for three consecutive health checks, the health status of the instance is `unhealthy`\. Otherwise, the health status is `healthy`\. For more information, see [Status Checks for Your Instances](monitoring-system-instance-status-check.md)\.

You can configure your Spot Fleet to replace unhealthy instances\. After enabling health check replacement, an instance is replaced after its health status is reported as `unhealthy`\. The Spot Fleet could go below its target capacity for up to a few minutes while an unhealthy instance is being replaced\.

**Requirements**

+ Health check replacement is supported only with Spot Fleets that maintain a target capacity, not with one\-time Spot Fleets\.

+ You can configure your Spot Fleet to replace unhealthy instances only when you create it\.

+ IAM users can use health check replacement only if they have permission to call the `ec2:DescribeInstanceStatus` action\.

## Planning a Spot Fleet Request<a name="plan-spot-fleet"></a>

Before you create a Spot Fleet request, review [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices)\. Use these best practices when you plan your Spot Fleet request so that you can provision the type of instances you want at the lowest possible price\. We also recommend that you do the following:

+ Determine whether you want to create a Spot Fleet that submits a one\-time request for the desired target capacity, or one that maintains a target capacity over time\.

+ Determine the instance types that meet your application requirements\.

+ Determine the target capacity for your Spot Fleet request\. You can set target capacity in instances or in custom units\. For more information, see [Spot Fleet Instance Weighting](spot-fleet.md#spot-instance-weighting)\.

+ Determine your price per unit, if you are using instance weighting\. To calculate the price per unit, divide the price per instance hour by the number of units \(or weight\) that this instance represents\. \(If you are not using instance weighting, the default price per unit is the price per instance hour\.\)

+ Review the possible options for your Spot Fleet request\. For more information, see the [request\-spot\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command in the *AWS CLI Command Reference*\. For additional examples, see [Spot Fleet Example Configurations](spot-fleet-examples.md)\.

## Service\-Linked Role for Spot Fleet Requests<a name="service-linked-roles-spot-fleet-requests"></a>

Amazon EC2 creates a service\-linked role when you request a Spot Fleet\. A service\-linked role includes all the permissions that Amazon EC2 requires to call other AWS services on your behalf\. For more information, see [Using Service\-Linked Roles](http://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2SpotFleet** to complete the following actions:

+ `ec2:RequestSpotInstances` \- Request Spot Instances

+ `ec2:TerminateInstances` \- Terminate Spot Instances

+ `ec2:DescribeImages` \- Describe Amazon Machine Images \(AMI\) for the Spot Instances

+ `ec2:DescribeInstanceStatus` \- Describe the status of the Spot Instances

+ `ec2:DescribeSubnets` \- Describe the subnets for Spot Instances

+ `ec2:CreateTags` \- Add system tags to Spot Instances

Amazon EC2 also creates the **AWSServiceRoleForEC2Spot** role when you request a Spot Fleet\. For more information, see [Service\-Linked Role for Spot Instance Requests](spot-requests.md#service-linked-roles-spot-instance-requests)\.

If you had an active Spot Fleet request before November 2017, when Amazon EC2 began supporting this service\-linked role, Amazon EC2 created the **AWSServiceRoleForEC2SpotFleet** role in your AWS account\. For more information, see [A New Role Appeared in My Account](http://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared) in the *IAM User Guide*\.

If you no longer need to use Spot Fleet, we recommend that you delete the **AWSServiceRoleForEC2SpotFleet** role\. After this role is deleted from your account, Amazon EC2 will create the role again if you request a Spot Fleet\.

## Creating a Spot Fleet Request<a name="create-spot-fleet"></a>

When you create a Spot Fleet request, you must specify information about the Spot Instances to launch, such as the instance type and the maximum price you are willing to pay\.

**To create a Spot Fleet request using the console**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. If you are new to Spot, you see a welcome page; choose **Get started**\. Otherwise, choose **Request Spot Instances**\.

1. For **Request type**, select **Request** or **Request and Maintain**\.

1. For **Target capacity**, type the number of units to request\. You can choose instances or performance characteristics that are important to your application workload, such as vCPUs, memory, and storage\. If the request type is **Request and Maintain**, you can specify a target capacity of 0 and add capacity later\.

1. For **Requirements**, do the following:

   1. \(Optional\) For **Launch template**, choose a launch template\. The launch template must specify an Amazon Machine Image \(AMI\), as you cannot override the AMI using Spot Fleet if you specify a launch template\.

   1. For **AMI**, choose one of the basic Amazon Machine Images \(AMI\) provided by AWS, or choose **Use custom AMI** to use an AMI from our user community, the AWS Marketplace, or one of your own\.

   1. For **Instance type\(s\)**, choose **Select**\. Select the instance types that have the minimum hardware specifications that you need \(vCPUs, memory, and storage\)\.

   1. For **Network**, your account supports either the EC2\-Classic and EC2\-VPC platforms, or the EC2\-VPC platform only\. To find out which platforms your account supports, see [Supported Platforms](ec2-supported-platforms.md)\.

      \[Existing VPC\] Select the VPC\.

      \[New VPC\] Select **Create new VPC** to go the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

      \[EC2\-Classic\] Select **EC2\-Classic**\.

   1. \(Optional\) For **Availability Zones**, the default is to let AWS choose the Availability Zones for your Spot Instances\. If you prefer, you can specify specific Availability Zones\.

      \[EC2\-VPC\] Select one or more Availability Zones\. If you have more than one subnet in an Availability Zone, select the appropriate subnet from **Subnet**\. To add subnets, select **Create new subnet** to go to the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

      \[EC2\-Classic\] Select **Select specific zone/subnet**, and then select one or more Availability Zones\.

   1. \(Optional\) To add storage, specify additional instance store volumes or EBS volumes, depending on the instance type\. You can also enable EBS optimization\.

   1. \(Optional\) By default, basic monitoring is enabled for your instances\. To enable detailed monitoring, select **Enable CloudWatch detailed monitoring**\.

   1. \(Optional\) To replace unhealthy instances in a **Request and Maintain** Spot Fleet, select **Replace unhealthy instances**\.

   1. \(Optional\) To run a Dedicated Spot Instance, choose **Dedicated \- run a dedicated instance** for **Tenancy**\.

   1. \(Optional\) By default, the Spot service terminates Spot Instances when they are interrupted\. If the fleet type is `maintain`, you can specify that the Spot service hibernates or stops Spot Instances when they are interrupted\. To do so, choose the corresponding option from **Interruption behavior**\.

   1. For **Security groups**, select one or more security groups\.

   1. \[EC2\-VPC\] If you need to connect to your instances in a VPC, you can enable **Auto\-assign IPv4 Public IP**\.

   1. \(Optional\) If you need to connect to your instances, specify your key pair using **Key pair name**\.

   1. \(Optional\) To launch your Spot Instances with an IAM role, choose the role for **IAM instance profile**\.

   1. \(Optional\) To run a start\-up script, copy it to **User data**\.

   1. \(Optional\) To add a tag, choose **Add new tag** and type the key and value for the tag\. Repeat for each tag\.

1. For **Spot request fulfillment**, do the following:

   1. For **Allocation strategy**, choose the strategy that meets your needs\. For more information, see [Spot Fleet Allocation Strategy](spot-fleet.md#spot-fleet-allocation-strategy)\.

   1. For **Maximum price**, you can use the default maximum price \(the On\-Demand price\) or specify the maximum price you are willing to pay\. Your Spot Instances are not launched if your maximum price is lower than the Spot price for the instance types that you selected\.

   1. \(Optional\) To create a request that is valid only during a specific time period, edit **Request valid from** and **Request valid until**\.

   1. \(Optional\) By default, we terminate your Spot Instances when the request expires\. To keep them running after your request expires, clear **Terminate instances at expiration**\.

1. \(Optional\) To register your Spot Instances with a load balancer, select **Receive traffic from one or more load balancers** and select one or more Classic Load Balancers or target groups\.

1. \(Optional\) To download a copy of the launch configuration for use with the AWS CLI, choose **JSON config**\.

1. Choose **Launch**\.

   The request type is `fleet`\. When the request is fulfilled, requests of type `instance` are added, where the state is `active` and the status is `fulfilled`\.

**To create a Spot Fleet request using the AWS CLI**

+ Use the following [request\-spot\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command to create a Spot Fleet request:

```
aws ec2 request-spot-fleet --spot-fleet-request-config file://config.json
```

For example configuration files, see [Spot Fleet Example Configurations](spot-fleet-examples.md)\.

The following is example output:

```
{
    "SpotFleetRequestId": "sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE"
}
```

## Monitoring Your Spot Fleet<a name="manage-spot-fleet"></a>

The Spot Fleet launches Spot Instances when your maximum price exceeds the Spot price and capacity is available\. The Spot Instances run until they are interrupted or you terminate them\.

**To monitor your Spot Fleet using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request\. The configuration details are available in the **Description** tab\.

1. To list the Spot Instances for the Spot Fleet, choose the **Instances** tab\.

1. To view the history for the Spot Fleet, choose the **History** tab\.

**To monitor your Spot Fleet using the AWS CLI**  
Use the following [describe\-spot\-fleet\-requests](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-requests.html) command to describe your Spot Fleet requests:

```
aws ec2 describe-spot-fleet-requests
```

Use the following [describe\-spot\-fleet\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-instances.html) command to describe the Spot Instances for the specified Spot Fleet:

```
aws ec2 describe-spot-fleet-instances --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE
```

Use the following [describe\-spot\-fleet\-request\-history](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-request-history.html) command to describe the history for the specified Spot Fleet request:

```
aws ec2 describe-spot-fleet-request-history --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --start-time 2015-05-18T00:00:00Z
```

## Modifying a Spot Fleet Request<a name="modify-spot-fleet"></a>

You can modify an active Spot Fleet request to complete the following tasks:

+ Increase the target capacity

+ Decrease the target capacity

**Note**  
It is not possible to modify a one\-time Spot Fleet request\.

When you increase the target capacity, the Spot Fleet launches the additional Spot Instances according to the allocation strategy for its Spot Fleet request\. If the allocation strategy is `lowestPrice`, the Spot Fleet launches the instances from the lowest\-priced Spot Instance pool in the Spot Fleet request\. If the allocation strategy is `diversified`, the Spot Fleet distributes the instances across the pools in the Spot Fleet request\.

When you decrease the target capacity, the Spot Fleet cancels any open requests that exceed the new target capacity\. You can request that the Spot Fleet terminate Spot Instances until the size of the fleet reaches the new target capacity\. If the allocation strategy is `lowestPrice`, the Spot Fleet terminates the instances with the highest price per unit\. If the allocation strategy is `diversified`, the Spot Fleet terminates instances across the pools\. Alternatively, you can request that the Spot Fleet keep the fleet at its current size, but not replace any Spot Instances that are interrupted or that you terminate manually\.

When a Spot Fleet terminates an instance because the target capacity was decreased, the instance receives a Spot Instance interruption notice\.

**To modify a Spot Fleet request using the console**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot/home/fleet](https://console.aws.amazon.com/ec2spot/home/fleet)\.

1. Select your Spot Fleet request\.

1. Choose **Actions**, and then choose **Modify target capacity**\.

1. In **Modify target capacity**, do the following:

   1. Enter the new target capacity\.

   1. \(Optional\) If you are decreasing the target capacity but want to keep the fleet at its current size, deselect **Terminate instances**\.

   1. Choose **Submit**\.

**To modify a Spot Fleet request using the AWS CLI**  
Use the following [modify\-spot\-fleet\-request](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-spot-fleet-request.html) command to update the target capacity of the specified Spot Fleet request:

```
aws ec2 modify-spot-fleet-request --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --target-capacity 20
```

You can modify the previous command as follows to decrease the target capacity of the specified Spot Fleet without terminating any Spot Instances as a result:

```
aws ec2 modify-spot-fleet-request --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --target-capacity 10 --excess-capacity-termination-policy NoTermination
```

## Canceling a Spot Fleet Request<a name="cancel-spot-fleet"></a>

When you are finished using your Spot Fleet, you can cancel the Spot Fleet request\. This cancels all Spot requests associated with the Spot Fleet, so that no new Spot Instances are launched for your Spot Fleet\. You must specify whether the Spot Fleet should terminate its Spot Instances\. If you terminate the instances, the Spot Fleet request enters the `cancelled_terminating` state\. Otherwise, the Spot Fleet request enters the `cancelled_running` state and the instances continue to run until they are interrupted or you terminate them manually\.

**To cancel a Spot Fleet request using the console**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot/home/fleet](https://console.aws.amazon.com/ec2spot/home/fleet)\.

1. Select your Spot Fleet request\.

1. Choose **Actions**, and then choose **Cancel spot request**\.

1. In **Cancel spot request**, verify that you want to cancel the Spot Fleet\. To keep the fleet at its current size, deselect **Terminate instances**\. When you are ready, choose **Confirm**\.

**To cancel a Spot Fleet request using the AWS CLI**  
Use the following [cancel\-spot\-fleet\-requests](http://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-spot-fleet-requests.html) command to cancel the specified Spot Fleet request and terminate the instances:

```
aws ec2 cancel-spot-fleet-requests --spot-fleet-request-ids sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --terminate-instances
```

The following is example output:

```
{
    "SuccessfulFleetRequests": [
        {
            "SpotFleetRequestId": "sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE",
            "CurrentSpotFleetRequestState": "cancelled_terminating",
            "PreviousSpotFleetRequestState": "active"
        }
    ],
    "UnsuccessfulFleetRequests": []
}
```

You can modify the previous command as follows to cancel the specified Spot Fleet request without terminating the instances:

```
aws ec2 cancel-spot-fleet-requests --spot-fleet-request-ids sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --no-terminate-instances
```

The following is example output:

```
{
    "SuccessfulFleetRequests": [
        {
            "SpotFleetRequestId": "sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE",
            "CurrentSpotFleetRequestState": "cancelled_running",
            "PreviousSpotFleetRequestState": "active"
        }
    ],
    "UnsuccessfulFleetRequests": []
}
```
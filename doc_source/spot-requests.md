# Spot Instance Requests<a name="spot-requests"></a>

To use Spot Instances, you create a Spot Instance request that includes the desired number of instances, the instance type, the Availability Zone, and the maximum price that you are willing to pay per instance hour\. If your maximum price exceeds the current Spot price, Amazon EC2 fulfills your request immediately if capacity is available\. Otherwise, Amazon EC2 waits until your request can be fulfilled or until you cancel the request\.

The following illustration shows how Spot requests work\. Notice that the request type \(one\-time or persistent\) determines whether the request is opened again when Amazon EC2 interrupts a Spot Instance or if you stop a Spot Instance\. If the request is persistent, the request is opened again after your Spot Instance is interrupted\. If the request is persistent and you stop your Spot Instance, the request only opens after you start your Spot Instance\.

![\[How Spot Instance requests work\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_lifecycle.png)

**Topics**
+ [Spot Instance Request States](#creating-spot-request-status)
+ [Defining a Duration for Your Spot Instances](#fixed-duration-spot-instances)
+ [Specifying a Tenancy for Your Spot Instances](#spot-instance-tenancy)
+ [Service\-Linked Role for Spot Instance Requests](#service-linked-roles-spot-instance-requests)
+ [Creating a Spot Instance Request](#using-spot-instances-request)
+ [Finding Running Spot Instances](#using-spot-instances-running)
+ [Tagging Spot Instance Requests](#concepts-spot-instances-request-tags)
+ [Canceling a Spot Instance Request](#using-spot-instances-cancel)
+ [Stopping a Spot Instance](#stopping-a-spot-instance)
+ [Starting a Spot Instance](#starting-a-spot-instance)
+ [Terminating a Spot Instance](#terminating-a-spot-instance)
+ [Spot Request Example Launch Specifications](spot-request-examples.md)

## Spot Instance Request States<a name="creating-spot-request-status"></a>

A Spot Instance request can be in one of the following states:
+ `open` – The request is waiting to be fulfilled\.
+ `active` – The request is fulfilled and has an associated Spot Instance\.
+ `failed` – The request has one or more bad parameters\.
+ `closed` – The Spot Instance was interrupted or terminated\.
+ `disabled` – You stopped the Spot Instance\.
+ `cancelled` – You canceled the request, or the request expired\.

The following illustration represents the transitions between the request states\. Notice that the transitions depend on the request type \(one\-time or persistent\)\.

![\[Spot Instance request states\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_request_states.png)

A one\-time Spot Instance request remains active until Amazon EC2 launches the Spot Instance, the request expires, or you cancel the request\. If the Spot price exceeds your maximum price or capacity is not available, your Spot Instance is terminated and the Spot Instance request is closed\.

A persistent Spot Instance request remains active until it expires or you cancel it, even if the request is fulfilled\. If the Spot price exceeds your maximum price or capacity is not available, your Spot Instance is interrupted\. After your instance is interrupted, when your maximum price exceeds the Spot price or capacity becomes available again, the Spot Instance is started if stopped or resumed if hibernated\. You can stop a Spot Instance and start it again if capacity is available and your maximum price exceeds the current Spot price\. If the Spot Instance is terminated \(irrespective of whether the Spot Instance is in a stopped or running state\), the Spot Instance request is opened again and Amazon EC2 launches a new Spot Instance\. For more information, see [Stopping a Spot Instance](#stopping-a-spot-instance), [Starting a Spot Instance](#starting-a-spot-instance), and [Terminating a Spot Instance](#terminating-a-spot-instance)\.

You can track the status of your Spot Instance requests, as well as the status of the Spot Instances launched, through the status\. For more information, see [Spot Request Status](spot-bid-status.md)\.

## Defining a Duration for Your Spot Instances<a name="fixed-duration-spot-instances"></a>

Spot Instances with a defined duration \(also known as Spot blocks\) are designed not to be interrupted and will run continuously for the duration you select\. This makes them ideal for jobs that take a finite time to complete, such as batch processing, encoding and rendering, modeling and analysis, and continuous integration\.

You can use a duration of 1, 2, 3, 4, 5, or 6 hours\. The price that you pay depends on the specified duration\. To view the current prices for a 1\-hour duration or a 6\-hour duration, see [Spot Instance Prices](https://aws.amazon.com/ec2/spot/pricing/#Spot_Instance_Prices)\. You can use these prices to estimate the cost of the 2, 3, 4, and 5\-hour durations\. When a request with a duration is fulfilled, the price for your Spot Instance is fixed, and this price remains in effect until the instance terminates\. You are billed at this price for each hour or partial hour that the instance is running\. A partial instance hour is billed to the nearest second\.

When you define a duration in your Spot request, the duration period for each Spot Instance starts as soon as the instance receives its instance ID\. The Spot Instance runs until you terminate it or the duration period ends\. At the end of the duration period, Amazon EC2 marks the Spot Instance for termination and provides a Spot Instance termination notice, which gives the instance a two\-minute warning before it terminates\. In rare situations, Spot blocks may be interrupted due to Amazon EC2 capacity needs\. In these cases, we provide a two\-minute warning before we terminate an instance, and you are not charged for the terminated instances even if you used them\.

**To launch Spot Instances with a defined duration \(console\)**  
For more information, see [Creating a Spot Fleet Request](spot-fleet-requests.md#create-spot-fleet)\.

**To launch Spot Instances with a defined duration \(AWS CLI\)**  
To specify a duration for your Spot Instances, include the `--block-duration-minutes` option with the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command\. For example, the following command creates a Spot request that launches Spot Instances that run for two hours:

```
aws ec2 request-spot-instances --instance-count 5 --block-duration-minutes 120 --type "one-time" --launch-specification file://specification.json
```

**To retrieve the cost for Spot Instances with a defined duration \(AWS CLI\)**  
Use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command to retrieve the fixed cost for your Spot Instances with a specified duration\. The information is in the `actualBlockHourlyPrice` field\.

## Specifying a Tenancy for Your Spot Instances<a name="spot-instance-tenancy"></a>

You can run a Spot Instance on single\-tenant hardware\. Dedicated Spot Instances are physically isolated from instances that belong to other AWS accounts\. For more information, see [Dedicated Instances](dedicated-instance.md) and the [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/) product page\.

To run a Dedicated Spot Instance, do one of the following:
+ Specify a tenancy of `dedicated` when you create the Spot Instance request\. For more information, see [Creating a Spot Instance Request](#using-spot-instances-request)\.
+ Request a Spot Instance in a VPC with an instance tenancy of `dedicated`\. For more information, see [Creating a VPC with an Instance Tenancy of Dedicated](dedicated-instance.md#creatingdedicatedvpc)\. You cannot request a Spot Instance with a tenancy of `default` if you request it in a VPC with an instance tenancy of `dedicated`\.

The following instance types support Dedicated Spot Instances\.

**Current Generation**
+ `c4.8xlarge`
+ `d2.8xlarge`
+ `i3.16xlarge`
+ `m4.10xlarge`
+ `m4.16xlarge`
+ `p2.16xlarge`
+ `r4.16xlarge`
+ `x1.32xlarge`

**Previous Generation**
+ `c3.8xlarge`
+ `cc2.8xlarge`
+ `cr1.8xlarge`
+ `g2.8xlarge`
+ `i2.8xlarge`
+ `r3.8xlarge`

## Service\-Linked Role for Spot Instance Requests<a name="service-linked-roles-spot-instance-requests"></a>

Amazon EC2 uses service\-linked roles for the permissions that it requires to call other AWS services on your behalf\. A service\-linked role is a unique type of IAM role that is linked directly to an AWS service\. Service\-linked roles provide a secure way to delegate permissions to AWS services because only the linked service can assume a service\-linked role\. For more information, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2Spot** to launch and manage Spot Instances on your behalf\.

### Permissions Granted by AWSServiceRoleForEC2Spot<a name="service-linked-role-permissions-granted-by-AWSServiceRoleForEC2Spot"></a>

Amazon EC2 uses **AWSServiceRoleForEC2Spot** to complete the following actions:
+ `ec2:DescribeInstances` – Describe Spot Instances
+ `ec2:StopInstances` – Stop Spot Instances
+ `ec2:StartInstances` – Start Spot Instances

### Create the Service\-Linked Role<a name="service-linked-role-creating-for-spot"></a>

Under most circumstances, you don't need to manually create a service\-linked role\. Amazon EC2 creates the **AWSServiceRoleForEC2Spot** service\-linked role the first time you request a Spot Instance using the console\.

If you had an active Spot Instance request before October 2017, when Amazon EC2 began supporting this service\-linked role, Amazon EC2 created the **AWSServiceRoleForEC2Spot** role in your AWS account\. For more information, see [A New Role Appeared in My Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared) in the *IAM User Guide*\.

Ensure that this role exists before you use the AWS CLI or an API to request a Spot Instance\. To create the role, use the IAM console as follows\.

**To manually create the AWSServiceRoleForEC2Spot service\-linked role**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. On the **Select type of trusted entity** page, choose **EC2**, **EC2 \- Spot Instances**, **Next: Permissions**\.

1. On the next page, choose **Next:Review**\.

1. On the **Review** page, choose **Create role**\.

If you no longer need to use Spot Instances, we recommend that you delete the **AWSServiceRoleForEC2Spot** role\. After this role is deleted from your account, Amazon EC2 will create the role again if you request Spot Instances\.

### Granting Access to CMKs for Use with Encrypted AMIs and EBS Snapshots<a name="spot-instance-service-linked-roles-access-to-cmks"></a>

If you specify an [encrypted AMI](AMIEncryption.md) or an [encrypted Amazon EBS snapshot](EBSEncryption.md) for your Spot Instances and you use a customer managed customer master key \(CMK\) for encryption, you must grant the **AWSServiceRoleForEC2Spot** role permission to use the CMK so that Amazon EC2 can launch Spot Instances on your behalf\. To do this, you must add a grant to the CMK, as shown in the following procedure\.

When providing permissions, grants are an alternative to key policies\. For more information, see [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) and [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.

**To grant the AWSServiceRoleForEC2Spot role permissions to use the CMK**
+ Use the [create\-grant](https://docs.aws.amazon.com/cli/latest/reference/kms/create-grant.html) command to add a grant to the CMK and to specify the principal \(the **AWSServiceRoleForEC2Spot** service\-linked role\) that is given permission to perform the operations that the grant permits\. The CMK is specified by the `key-id` parameter and the ARN of the CMK\. The principal is specified by the `grantee-principal` parameter and the ARN of the **AWSServiceRoleForEC2Spot** service\-linked role\.

  The following example is formatted for legibility\. 

  ```
  aws kms create-grant \
  --region us-east-1 \
  --key-id arn:aws:kms:us-east-1:444455556666:key/1234abcd-12ab-34cd-56ef-1234567890ab \
  --grantee-principal arn:aws:iam::111122223333:role/AWSServiceRoleForEC2Spot \
  --operations "Decrypt" "Encrypt" "GenerateDataKey" "GenerateDataKeyWithoutPlaintext" "CreateGrant" "DescribeKey" "ReEncryptFrom" "ReEncryptTo"
  ```

## Creating a Spot Instance Request<a name="using-spot-instances-request"></a>

The process for requesting a Spot Instance is similar to the process for launching an On\-Demand Instance\. You can't change the parameters of your Spot Instance request, including your maximum price, after you've submitted the request\.

If you request multiple Spot Instances at one time, Amazon EC2 creates separate Spot Instance requests so that you can track the status of each request separately\. For more information about tracking Spot Instance requests, see [Spot Request Status](spot-bid-status.md)\.

**Prerequisites**  
Before you begin, decide on your maximum price, how many Spot Instances you'd like, and what instance type to use\. To review Spot price trends, see [Spot Instance Pricing History](using-spot-instances-history.md)\.

**To create a Spot Instance request \(console\)**  
For more information, see [Creating a Spot Fleet Request](spot-fleet-requests.md#create-spot-fleet)\.

**To create a Spot Instance request \(AWS CLI\)**  
Use the following [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command to create a one\-time request:

```
aws ec2 request-spot-instances --instance-count 5 --type "one-time" --launch-specification file://specification.json
```

Use the following [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command to create a persistent request:

```
aws ec2 request-spot-instances --instance-count 5 --type "persistent" --launch-specification file://specification.json
```

For example launch specification files to use with these commands, see [Spot Request Example Launch Specifications](spot-request-examples.md)\. If you download a launch specification file from the console, you must use the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command instead \(the console specifies a Spot request using a Spot Fleet\)\.

Amazon EC2 launches your Spot Instance when the maximum price exceeds the Spot price and capacity is available\. The Spot Instance runs until it is interrupted or you terminate it yourself\. Use the following [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command to monitor your Spot Instance request:

```
aws ec2 describe-spot-instance-requests --spot-instance-request-ids sir-08b93456
```

## Finding Running Spot Instances<a name="using-spot-instances-running"></a>

Amazon EC2 launches a Spot Instance when the maximum price exceeds the Spot price and capacity is available\. A Spot Instance runs until it is interrupted or you terminate it yourself\. If your maximum price is exactly equal to the Spot price, there is a chance that your Spot Instance remains running, depending on demand\.

**To find running Spot Instances \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

   You can see both Spot Instance requests and Spot Fleet requests\. If a Spot Instance request has been fulfilled, **Capacity** is the ID of the Spot Instance\. For a Spot Fleet, **Capacity** indicates how much of the requested capacity has been fulfilled\. To view the IDs of the instances in a Spot Fleet, choose the expand arrow, or select the fleet and choose **Instances**\.
**Note**  
Spot Instance requests are not tagged instantly and for a period of time may appear separate from Spot Fleet Requests \(SFR\)\.

1. Alternatively, in the navigation pane, choose **Instances**\. In the top right corner, choose the **Show/Hide** icon, and then select **Lifecycle**\. For each instance, **Lifecycle** is either `normal`, `spot`, or `scheduled`\.

**To find running Spot Instances \(AWS CLI\)**  
To enumerate your Spot Instances, use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command with the `--query` option as follows:

```
aws ec2 describe-spot-instance-requests --query "SpotInstanceRequests[*].{ID:InstanceId}"
```

The following is example output:

```
[
    {
        "ID": "i-1234567890abcdef0"
    },
    {
        "ID": "i-0598c7d356eba48d7"
    }
]
```

Alternatively, you can enumerate your Spot Instances using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command with the `--filters` option as follows:

```
aws ec2 describe-instances --filters "Name=instance-lifecycle,Values=spot"
```

## Tagging Spot Instance Requests<a name="concepts-spot-instances-request-tags"></a>

To help categorize and manage your Spot Instance requests, you can tag them with metadata of your choice\. For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.

You can assign a tag to a Spot Instance request after you create it\. The tags that you create for your Spot Instance requests only apply to the requests\. These tags are not added automatically to the Spot Instance that the Spot service launches to fulfill the request\. You must add tags to a Spot Instance yourself after the Spot Instance is launched\.

**To add a tag to your Spot Instance request or Spot Instance using the AWS CLI**  
Use the following [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command to tag your resources:

```
aws ec2 create-tags --resources sir-08b93456 i-1234567890abcdef0 --tags Key=purpose,Value=test
```

## Canceling a Spot Instance Request<a name="using-spot-instances-cancel"></a>

If you no longer want your Spot Instance request, you can cancel it\. You can only cancel Spot Instance requests that are `open`, `active`, or `disabled`\.
+ Your Spot Instance request is `open` when your request has not yet been fulfilled and no instances have been launched\.
+ Your Spot Instance request is `active` when your request has been fulfilled and Spot Instances have launched as a result\. 
+ Your Spot Instance request is `disabled` when you stop your Spot Instance\.

If your Spot Instance request is `active` and has an associated running Spot Instance, or your Spot Instance request is `disabled` and has an associated stopped Spot Instance, canceling the request does not terminate the instance\. For more information about terminating a Spot Instance, see [Terminating a Spot Instance](#terminating-a-spot-instance)\.

**To cancel a Spot Instance request \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests** and select the Spot request\.

1. Choose **Actions**, **Cancel spot request**\.

1. \(Optional\) If you are finished with the associated Spot Instances, you can terminate them\. In the navigation pane, choose **Instances**, select the instance, and then choose **Actions**, **Instance State**, **Terminate**\.

**To cancel a Spot Instance request \(AWS CLI\)**
+ Use the following [cancel\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-spot-instance-requests.html) command to cancel the specified Spot request:

  ```
  aws ec2 cancel-spot-instance-requests --spot-instance-request-ids sir-08b93456
  ```

## Stopping a Spot Instance<a name="stopping-a-spot-instance"></a>

If you don’t need your Spot Instances now, but you want to restart them later without losing the data persisted in the Amazon EBS volume, you can stop them\. The steps for stopping a Spot Instance are similar to the steps for stopping an On\-Demand Instance\. You can only stop a Spot Instance if the Spot Instance was launched from a `persistent` Spot Instance request\.

**Note**  
While a Spot Instance is stopped, you can modify some of its instance attributes, but not the instance type\.   
We don't charge usage for a stopped Spot Instance, or data transfer fees, but we do charge for the storage for any Amazon EBS volumes\.

**Limitations**
+ You can't stop a Spot Instance if it is part of a fleet or launch group, Availability Zone group, or Spot block\.

**To stop a Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the Spot Instance\.

1. Choose **Actions**, **Instance State**, **Stop**\.

**To stop a Spot Instance \(AWS CLI\)**
+ Use the following [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command to manually stop one or more Spot Instances\.

  ```
  aws ec2 stop-instances --instance-ids i-1234567890abcdef0
  ```

## Starting a Spot Instance<a name="starting-a-spot-instance"></a>

You can start a Spot Instance that you previously stopped\. The steps for starting a Spot Instance are similar to the steps for starting an On\-Demand Instance\.

**Prerequisites**

You can only start a Spot Instance if:
+ You manually stopped the Spot Instance\.
+ The Spot Instance is an EBS\-backed instance\.
+ Spot Instance capacity is available\.
+ The Spot price is lower than your maximum price\.

**Limitations**
+ You can't start a Spot Instance if it is part of fleet or launch group, Availability Zone group, or Spot block\.

**To start a Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the Spot Instance\.

1. Choose **Actions**, **Instance State**, **Start**\.

**To start a Spot Instance \(AWS CLI\)**
+ Use the following [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command to manually start one or more Spot Instances\.

  ```
  aws ec2 start-instances --instance-ids i-1234567890abcdef0
  ```

## Terminating a Spot Instance<a name="terminating-a-spot-instance"></a>

If your Spot Instance request is `active` and has an associated running Spot Instance, or your Spot Instance request is `disabled` and has an associated stopped Spot Instance, canceling the request does not terminate the instance; you must terminate the running Spot Instance manually\. If you terminate a running or stopped Spot Instance that was launched by a persistent Spot request, the Spot request returns to the `open` state so that a new Spot Instance can be launched\. To cancel a persistent Spot request and terminate its Spot Instances, you must cancel the Spot request first and then terminate the Spot Instances\. Otherwise, the persistent Spot request can launch a new instance\. For more information about canceling a Spot Instance request, see [Canceling a Spot Instance Request](#using-spot-instances-cancel)\.

**To manually terminate a Spot Instance \(AWS CLI\)**
+ Use the following [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) command to manually terminate Spot Instances: 

  ```
  aws ec2 terminate-instances --instance-ids i-1234567890abcdef0 i-0598c7d356eba48d7
  ```
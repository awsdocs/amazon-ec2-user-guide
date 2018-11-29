# Spot Instance Requests<a name="spot-requests"></a>

To use Spot Instances, you create a Spot Instance request that includes the number of instances, the instance type, the Availability Zone, and the maximum price that you are willing to pay per instance hour\. If your maximum price exceeds the current Spot price, Amazon EC2 fulfills your request immediately if capacity is available\. Otherwise, Amazon EC2 waits until your request can be fulfilled or until you cancel the request\.

The following illustration shows how Spot requests work\. Notice that the action taken for a Spot Instance interruption depends on the request type \(one\-time or persistent\) and the interruption behavior \(hibernate, stop, or terminate\)\. If the request is a persistent request, the request is opened again after your Spot Instance is interrupted\.

![\[The Spot lifecycle\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_lifecycle.png)

**Topics**
+ [Spot Instance Request States](#creating-spot-request-status)
+ [Specifying a Duration for Your Spot Instances](#fixed-duration-spot-instances)
+ [Specifying a Tenancy for Your Spot Instances](#spot-instance-tenancy)
+ [Service\-Linked Role for Spot Instance Requests](#service-linked-roles-spot-instance-requests)
+ [Creating a Spot Instance Request](#using-spot-instances-request)
+ [Finding Running Spot Instances](#using-spot-instances-running)
+ [Tagging Spot Instance Requests](#concepts-spot-instances-request-tags)
+ [Canceling a Spot Instance Request](#using-spot-instances-cancel)
+ [Spot Request Example Launch Specifications](spot-request-examples.md)

## Spot Instance Request States<a name="creating-spot-request-status"></a>

A Spot Instance request can be in one of the following states:
+ `open`—The request is waiting to be fulfilled\.
+ `active`—The request is fulfilled and has an associated Spot Instance\.
+ `failed`—The request has one or more bad parameters\.
+ `closed`—The Spot Instance was interrupted or terminated\.
+ `cancelled`—You cancelled the request, or the request expired\.

The following illustration represents the transitions between the request states\. Notice that the transitions depend on the request type \(one\-time or persistent\)\.

![\[Spot request states\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_request_states.png)

A one\-time Spot Instance request remains active until Amazon EC2 launches the Spot Instance, the request expires, or you cancel the request\. If the Spot price exceeds your maximum price or capacity is not available, your Spot Instance is terminated and the Spot Instance request is closed\.

A persistent Spot Instance request remains active until it expires or you cancel it, even if the request is fulfilled\. If the Spot price exceeds your maximum price or capacity is not available, your Spot Instance is interrupted\. After your instance is interrupted, when the maximum price exceeds the Spot price or capacity becomes available again, the Spot Instance is started if stopped or resumed if hibernated\. If the Spot Instance is terminated, the Spot Instance request is opened again and Amazon EC2 launches a new Spot Instance\.

You can track the status of your Spot Instance requests, as well as the status of the Spot Instances launched, through the status\. For more information, see [Spot Request Status](spot-bid-status.md)\.

## Specifying a Duration for Your Spot Instances<a name="fixed-duration-spot-instances"></a>

Amazon EC2 does not terminate Spot Instances with a specified duration \(also known as Spot blocks\) when the Spot price changes\. This makes them ideal for jobs that take a finite time to complete, such as batch processing, encoding and rendering, modeling and analysis, and continuous integration\.

You can specify a duration of 1, 2, 3, 4, 5, or 6 hours\. The price that you pay depends on the specified duration\. To view the current prices for a 1\-hour duration or a 6\-hour duration, see [Spot Instance Prices](https://aws.amazon.com/ec2/spot/pricing/)\. You can use these prices to estimate the cost of the 2, 3, 4, and 5\-hour durations\. When a request with a duration is fulfilled, the price for your Spot Instance is fixed, and this price remains in effect until the instance terminates\. You are billed at this price for each hour or partial hour that the instance is running\. A partial instance hour is billed to the nearest second\.

When you specify a duration in your Spot request, the duration period for each Spot Instance starts as soon as the instance receives its instance ID\. The Spot Instance runs until you terminate it or the duration period ends\. At the end of the duration period, Amazon EC2 marks the Spot Instance for termination and provides a Spot Instance termination notice, which gives the instance a two\-minute warning before it terminates\.

**To launch Spot Instances with a specified duration \(console\)**  
Select the appropriate request type\. For more information, see [Creating a Spot Instance Request](#using-spot-instances-request)\.

**To launch Spot Instances with a specified duration \(AWS CLI\)**  
To specify a duration for your Spot Instances, include the `--block-duration-minutes` option with the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command\. For example, the following command creates a Spot request that launches Spot Instances that run for two hours:

```
aws ec2 request-spot-instances --instance-count 5 --block-duration-minutes 120 --type "one-time" --launch-specification file://specification.json
```

**To retrieve the cost for Spot Instances with a specified duration \(AWS CLI\)**  
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

Amazon EC2 creates a service\-linked role when you request Spot Instances\. A service\-linked role includes all the permissions that Amazon EC2 requires to call other AWS services on your behalf\. For more information, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2Spot** to complete the following actions:
+ `ec2:DescribeInstances` \- Describe Spot Instances
+ `ec2:StopInstances` \- Stop Spot Instances
+ `ec2:StartInstances` \- Start Spot Instances

If you specify encrypted EBS snapshots for your Spot Instances and you use customer managed CMKs for encryption, you must grant the **AWSServiceRoleForEC2Spot** role access to the CMKs so that Amazon EC2 can launch Spot Instances on your behalf\. The principal is the Amazon Resource Name \(ARN\) of the **AWSServiceRoleForEC2Spot** role\. For more information, see [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html)\.

If you had an active Spot Instance request before October 2017, when Amazon EC2 began supporting this service\-linked role, Amazon EC2 created the **AWSServiceRoleForEC2Spot** role in your AWS account\. For more information, see [A New Role Appeared in My Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared) in the *IAM User Guide*\.

Ensure that this role exists before you use the AWS CLI or an API to create a Spot Fleet\. To create the role, use the IAM console as follows\.

**To create the IAM role \(console\)**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. On the **Select type of trusted entity** page, choose **EC2**, **EC2 \- Spot Instances**, **Next: Permissions**\.

1. On the next page, choose **Next:Review**\.

1. On the **Review** page, choose **Create role**\.

If you no longer need to use Spot Instances, we recommend that you delete the **AWSServiceRoleForEC2Spot** role\. After this role is deleted from your account, Amazon EC2 will create the role again if you request Spot Instances\.

## Creating a Spot Instance Request<a name="using-spot-instances-request"></a>

The process for requesting a Spot Instance is similar to the process for launching an On\-Demand Instance\. You can't change the parameters of your Spot request, including your maximum price, after you've submitted the request\.

If you request multiple Spot Instances at one time, Amazon EC2 creates separate Spot Instance requests so that you can track the status of each request separately\. For more information about tracking Spot requests, see [Spot Request Status](spot-bid-status.md)\.

**Prerequisites**  
Before you begin, decide on your maximum price, how many Spot Instances you'd like, and what instance type to use\. To review Spot price trends, see [Spot Instance Pricing History](using-spot-instances-history.md)\.

**To create a Spot Instance request \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, choose **Spot Requests**\.

1. If you are new to Spot Instances, you see a welcome page; choose **Get started**\. Otherwise, choose **Request Spot Instances**\.

1. For **Request type**, the default is **Request**, which specifies a one\-time Spot request created using a Spot Fleet\. To use Spot blocks instead, choose **Reserve for duration** and select the number of hours for the job to complete\.

   To use **Request and Maintain**, see [Creating a Spot Fleet Request](spot-fleet-requests.md#create-spot-fleet)\.

1. For **Target capacity**, enter the number of units to request\. You can choose instances or performance characteristics that are important to your application workload, such as vCPUs, memory, and storage\.

1. For **Requirements**, do the following:

   1. \[Spot Fleet\] \(Optional\) For **Launch template**, choose a launch template\. The launch template must specify an Amazon Machine Image \(AMI\), as you cannot override the AMI using Spot Fleet if you specify a launch template\.

   1. For **AMI**, choose one of the basic AMIs provided by AWS, or choose **Use custom AMI** to specify your own AMI\.

   1. For **Instance type\(s\)**, choose **Select**\. Select the instance types that have the minimum hardware specifications that you need \(vCPUs, memory, and storage\)\.

   1. For **Network**, you can select an existing VPC or create a new one\.

      \[Existing VPC\] Select the VPC\.

      \[New VPC\] Choose **Create new VPC** to go to the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

   1. \(Optional\) For **Availability Zones**, the default is to let AWS choose the Availability Zones for your Spot Instances\. If you prefer, you can specify specific Availability Zones\.

      Select one or more Availability Zones\. If you have more than one subnet in an Availability Zone, select the appropriate subnet from **Subnet**\. To add subnets, select **Create new subnet** to go to the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

   1. \(Optional\) To add storage, specify additional instance store volumes or EBS volumes, depending on the instance type\. You can also enable Amazon EBS optimization\.

   1. \(Optional\) By default, basic monitoring is enabled for your instances\. To enable detailed monitoring, choose **Enable CloudWatch detailed monitoring**\.

   1. \(Optional\) To run a Dedicated Spot Instance, for **Tenancy**, choose **Dedicated \- run a dedicated instance**\.

   1. For **Security groups**, select one or more security groups\.

   1. To connect to your instances, enable **Auto\-assign IPv4 Public IP**\.

   1. \(Optional\) To connect to your instances, specify your key pair for **Key pair name**\.

   1. \(Optional\) To launch your Spot Instances with an IAM role, for **IAM instance profile**, specify the role\.

   1. \(Optional\) To run a start\-up script, copy it to **User data**\.

   1. \[Spot Fleet\] To add a tag, choose **Add new tag** and type the key and value for the tag\. Repeat for each tag\.

1. For **Spot request fulfillment**, do the following:

   1. \[Spot Fleet\] For **Allocation strategy**, choose the strategy that meets your needs\. For more information, see [Allocation Strategy for Spot Instances](spot-fleet.md#spot-fleet-allocation-strategy)\.

   1. \[Spot Fleet\] For **Maximum price**, you can use the default maximum price \(the On\-Demand price\) or specify the maximum price that you are willing to pay\. If your maximum price is lower than the Spot price for the instance types that you selected, your Spot Instances are not launched\.

   1. \(Optional\) To create a request that is valid only during a specific time period, edit the values for **Request valid from** and **Request valid until**\.

   1. \[Spot Fleet\] By default, we terminate your Spot Instances when the request expires\. To keep them running after your request expires, clear **Terminate instances at expiration**\.

1. \(Optional\) To register your Spot Instances with a load balancer, choose **Receive traffic from one or more load balancers** and select one or more Classic Load Balancers or target groups\.

1. \(Optional\) To download a copy of the launch configuration for use with the AWS CLI, choose **JSON config**\.

1. Choose **Launch**\.

   \[Spot Fleet\] The request type is `fleet`\. When the request is fulfilled, requests of type `instance` are added, where the state is `active` and the status is `fulfilled`\.

   \[Spot block\] The request type is `block` and the initial state is `open`\. When the request is fulfilled, the state is `active` and the status is `fulfilled`\.

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

1. Alternatively, in the navigation pane, choose **Instances**\. In the top right corner, choose the **Show/Hide** icon, and then select **Lifecycle**\. For each instance, **Lifecycle** is either `normal`, `spot`, or `scheduled`\.

**To find running Spot Instances \(AWS CLI\)**  
To enumerate your Spot Instances, use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command with the `--query` option as follows:

```
aws ec2 describe-spot-instance-requests --query SpotInstanceRequests[*].{ID:InstanceId}
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

**Note**  
Spot Instance requests are not tagged instantly and for a period of time may appear separate from Spot Fleet requests\.

## Canceling a Spot Instance Request<a name="using-spot-instances-cancel"></a>

If you no longer want your Spot request, you can cancel it\. You can only cancel Spot Instance requests that are `open` or `active`\. Your Spot request is `open` when your request has not yet been fulfilled and no instances have been launched\. Your Spot request is `active` when your request has been fulfilled, and Spot Instances have launched as a result\. If your Spot request is `active` and has an associated running Spot Instance, canceling the request does not terminate the instance; you must terminate the running Spot Instance manually\.

If the Spot request is a persistent Spot request, it returns to the `open` state so that a new Spot Instance can be launched\. To cancel a persistent Spot request and terminate its Spot Instances, you must cancel the Spot request first and then terminate the Spot Instances\. Otherwise, the Spot request can launch a new instance\.

**To cancel a Spot Instance request \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests** and select the Spot request\.

1. Choose **Actions**, **Cancel spot request**\.

1. \(Optional\) If you are finished with the associated Spot Instances, you can terminate them\. In the navigation pane, choose **Instances**, select the instance, and then choose **Actions**, **Instance State**, **Terminate**\.

**To cancel a Spot Instance request using the AWS CLI**  
Use the following [cancel\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-spot-instance-requests.html) command to cancel the specified Spot request:

```
aws ec2 cancel-spot-instance-requests --spot-instance-request-ids sir-08b93456
```

If you are finished with the associated Spot Instances, you can terminate them manually using the following [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) command:

```
aws ec2 terminate-instances --instance-ids i-1234567890abcdef0 i-0598c7d356eba48d7
```
# Spot Instance requests<a name="spot-requests"></a>

To use Spot Instances, you create a Spot Instance request that includes the desired number of instances, the instance type, the Availability Zone, and the maximum price that you are willing to pay per instance hour\. If your maximum price exceeds the current Spot price, Amazon EC2 fulfills your request immediately if capacity is available\. Otherwise, Amazon EC2 waits until your request can be fulfilled or until you cancel the request\.

The following illustration shows how Spot requests work\. Notice that the request type \(one\-time or persistent\) determines whether the request is opened again when Amazon EC2 interrupts a Spot Instance or if you stop a Spot Instance\. If the request is persistent, the request is opened again after your Spot Instance is interrupted\. If the request is persistent and you stop your Spot Instance, the request only opens after you start your Spot Instance\.

![\[How Spot Instance requests work\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_lifecycle.png)

**Topics**
+ [Spot Instance request states](#creating-spot-request-status)
+ [Defining a duration for your Spot Instances](#fixed-duration-spot-instances)
+ [Specifying a tenancy for your Spot Instances](#spot-instance-tenancy)
+ [Service\-linked role for Spot Instance requests](#service-linked-roles-spot-instance-requests)
+ [Creating a Spot Instance request](#using-spot-instances-request)
+ [Finding running Spot Instances](#using-spot-instances-running)
+ [Tagging Spot Instance requests](#concepts-spot-instances-request-tags)
+ [Canceling a Spot Instance request](#using-spot-instances-cancel)
+ [Stopping a Spot Instance](#stopping-a-spot-instance)
+ [Starting a Spot Instance](#starting-a-spot-instance)
+ [Terminating a Spot Instance](#terminating-a-spot-instance)
+ [Spot Instance request example launch specifications](spot-request-examples.md)

## Spot Instance request states<a name="creating-spot-request-status"></a>

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

You can track the status of your Spot Instance requests, as well as the status of the Spot Instances launched, through the status\. For more information, see [Spot request status](spot-request-status.md)\.

## Defining a duration for your Spot Instances<a name="fixed-duration-spot-instances"></a>

Spot Instances with a defined duration \(also known as Spot blocks\) are designed not to be interrupted and will run continuously for the duration you select\. This makes them ideal for jobs that take a finite time to complete, such as batch processing, encoding and rendering, modeling and analysis, and continuous integration\.

You can use a duration of 1, 2, 3, 4, 5, or 6 hours\. The price that you pay depends on the specified duration\. To view the current prices for a 1\-hour duration or a 6\-hour duration, see [Spot Instance Prices](https://aws.amazon.com/ec2/spot/pricing/#Spot_Instance_Prices)\. You can use these prices to estimate the cost of the 2, 3, 4, and 5\-hour durations\. When a request with a duration is fulfilled, the price for your Spot Instance is fixed, and this price remains in effect until the instance terminates\. You are billed at this price for each hour or partial hour that the instance is running\. A partial instance hour is billed to the nearest second\.

When you define a duration in your Spot request, the duration period for each Spot Instance starts as soon as the instance receives its instance ID\. The Spot Instance runs until you terminate it or the duration period ends\. At the end of the duration period, Amazon EC2 marks the Spot Instance for termination and provides a Spot Instance termination notice, which gives the instance a two\-minute warning before it terminates\. In rare situations, Spot blocks may be interrupted due to Amazon EC2 capacity needs\. In these cases, we provide a two\-minute warning before we terminate an instance, and you are not charged for the terminated instances even if you used them\.

New accounts or accounts with no previous billing history with AWS are not eligible for Spot Instances with a defined duration \(also known as Spot blocks\)\.

**To launch Spot Instances with a defined duration \(console\)**  
Follow the [Creating a Spot Fleet request](spot-fleet-requests.md#create-spot-fleet) procedure\. To launch Spot Instances with a defined duration, for **Tell us your application or task need**, choose **Defined duration workloads**\.

**To launch Spot Instances with a defined duration \(AWS CLI\)**  
To specify a duration for your Spot Instances, include the `--block-duration-minutes` option with the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command\. For example, the following command creates a Spot request that launches Spot Instances that run for two hours\.

```
aws ec2 request-spot-instances \
    --instance-count 5 \
    --block-duration-minutes 120 \
    --type "one-time" \
    --launch-specification file://specification.json
```

**To retrieve the cost for Spot Instances with a defined duration \(AWS CLI\)**  
Use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command to retrieve the fixed cost for your Spot Instances with a specified duration\. The information is in the `actualBlockHourlyPrice` field\.

## Specifying a tenancy for your Spot Instances<a name="spot-instance-tenancy"></a>

You can run a Spot Instance on single\-tenant hardware\. Dedicated Spot Instances are physically isolated from instances that belong to other AWS accounts\. For more information, see [Dedicated Instances](dedicated-instance.md) and the [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/) product page\.

To run a Dedicated Spot Instance, do one of the following:
+ Specify a tenancy of `dedicated` when you create the Spot Instance request\. For more information, see [Creating a Spot Instance request](#using-spot-instances-request)\.
+ Request a Spot Instance in a VPC with an instance tenancy of `dedicated`\. For more information, see [Creating a VPC with an instance tenancy of dedicated](dedicated-instance.md#creatingdedicatedvpc)\. You cannot request a Spot Instance with a tenancy of `default` if you request it in a VPC with an instance tenancy of `dedicated`\.

The following instance types support Dedicated Spot Instances\.

**Current generation**
+ `c4.8xlarge`
+ `d2.8xlarge`
+ `i3.16xlarge`
+ `m4.10xlarge`
+ `m4.16xlarge`
+ `p2.16xlarge`
+ `r4.16xlarge`
+ `x1.32xlarge`

**Previous generation**
+ `c3.8xlarge`
+ `cc2.8xlarge`
+ `cr1.8xlarge`
+ `g2.8xlarge`
+ `i2.8xlarge`
+ `r3.8xlarge`

## Service\-linked role for Spot Instance requests<a name="service-linked-roles-spot-instance-requests"></a>

Amazon EC2 uses service\-linked roles for the permissions that it requires to call other AWS services on your behalf\. A service\-linked role is a unique type of IAM role that is linked directly to an AWS service\. Service\-linked roles provide a secure way to delegate permissions to AWS services because only the linked service can assume a service\-linked role\. For more information, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2Spot** to launch and manage Spot Instances on your behalf\.

### Permissions granted by AWSServiceRoleForEC2Spot<a name="service-linked-role-permissions-granted-by-AWSServiceRoleForEC2Spot"></a>

Amazon EC2 uses **AWSServiceRoleForEC2Spot** to complete the following actions:
+ `ec2:DescribeInstances` – Describe Spot Instances
+ `ec2:StopInstances` – Stop Spot Instances
+ `ec2:StartInstances` – Start Spot Instances

### Create the service\-linked role<a name="service-linked-role-creating-for-spot"></a>

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

### Granting access to CMKs for use with encrypted AMIs and EBS snapshots<a name="spot-instance-service-linked-roles-access-to-cmks"></a>

If you specify an [encrypted AMI](AMIEncryption.md) or an [encrypted Amazon EBS snapshot](EBSEncryption.md) for your Spot Instances and you use a customer managed customer master key \(CMK\) for encryption, you must grant the **AWSServiceRoleForEC2Spot** role permission to use the CMK so that Amazon EC2 can launch Spot Instances on your behalf\. To do this, you must add a grant to the CMK, as shown in the following procedure\.

When providing permissions, grants are an alternative to key policies\. For more information, see [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) and [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.

**To grant the AWSServiceRoleForEC2Spot role permissions to use the CMK**
+ Use the [create\-grant](https://docs.aws.amazon.com/cli/latest/reference/kms/create-grant.html) command to add a grant to the CMK and to specify the principal \(the **AWSServiceRoleForEC2Spot** service\-linked role\) that is given permission to perform the operations that the grant permits\. The CMK is specified by the `key-id` parameter and the ARN of the CMK\. The principal is specified by the `grantee-principal` parameter and the ARN of the **AWSServiceRoleForEC2Spot** service\-linked role\.

  ```
  aws kms create-grant \
      --region us-east-1 \
      --key-id arn:aws:kms:us-east-1:444455556666:key/1234abcd-12ab-34cd-56ef-1234567890ab \
      --grantee-principal arn:aws:iam::111122223333:role/AWSServiceRoleForEC2Spot \
      --operations "Decrypt" "Encrypt" "GenerateDataKey" "GenerateDataKeyWithoutPlaintext" "CreateGrant" "DescribeKey" "ReEncryptFrom" "ReEncryptTo"
  ```

## Creating a Spot Instance request<a name="using-spot-instances-request"></a>

The procedure for requesting a Spot Instance is similar to the procedure for launching an On\-Demand Instance\. You can request a Spot Instance in the following ways:
+ To request a Spot Instance using the console, use the launch instance wizard\. For more information, see [To create a Spot Instance request \(console\)](#create-spot-instance-request-console-procedure)\.
+ To request a Spot Instance using the CLI, use the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command or the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. For more information, see [To create a Spot Instance request using request-spot-instances (AWS CLI)](#create-spot-request-request-spot-instances) and [To create a Spot Instance request using run-instances (AWS CLI)](#create-spot-request-run-instances)\.
+ To request a Spot Instance with a defined duration using the console, follow the [Creating a Spot Fleet request](spot-fleet-requests.md#create-spot-fleet) procedure\. For **Tell us your application or task need**, choose **Defined duration workloads**\. For more information, see [Defining a duration for your Spot Instances](#fixed-duration-spot-instances)\.
+ To request a Spot Instance with a defined duration using the CLI, use the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command and specify the `--block-duration-minutes` parameter\. For more information, see [Defining a duration for your Spot Instances](#fixed-duration-spot-instances)\.

After you've submitted your Spot Instance request, you can't change the parameters of the request\. This means that you can't make changes to the maximum price that you're willing to pay\.

If you request multiple Spot Instances at one time, Amazon EC2 creates separate Spot Instance requests so that you can track the status of each request separately\. For more information about tracking Spot Instance requests, see [Spot request status](spot-request-status.md)\.

To launch a fleet that includes Spot Instances and On\-Demand Instances, see [Creating a Spot Fleet request](spot-fleet-requests.md#create-spot-fleet)\.

**Note**  
You can't launch a Spot Instance and an On\-Demand Instance in the same call using the launch instance wizard or the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\.

**Prerequisites**  
Before you begin, decide on your maximum price, how many Spot Instances you'd like, and what instance type to use\. To review Spot price trends, see [Spot Instance pricing history](using-spot-instances-history.md)\.<a name="create-spot-instance-request-console-procedure"></a>

**To create a Spot Instance request \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar at the top of the screen, select a Region\.

1. From the Amazon EC2 console dashboard, choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose an AMI\. For more information, see [Step 1: Choose an Amazon Machine Image \(AMI\)](launching-instance.md#step-1-AMI)\.

1. On the **Choose an Instance Type** page, select the hardware configuration and size of the instance to launch, and then choose **Next: Configure Instance Details**\. For more information, see [Step 2: Choose an Instance Type](launching-instance.md#choose-an-instance-type-page)\.

1. On the **Configure Instance Details** page, configure the Spot Instance request as follows:
   + **Number of instances**: Enter the number of instances to launch\.
**Note**  
Amazon EC2 creates a separate request for each Spot Instance\.
   + \(Optional\) To help ensure that you maintain the correct number of instances to handle demand on your application, you can choose **Launch into Auto Scaling Group** to create a launch configuration and an Auto Scaling group\. Auto Scaling scales the number of instances in the group according to your specifications\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/ec2/userguide/)\.
   + **Purchasing option**: Choose **Request Spot instances** to launch a Spot Instance\. When you choose this option, the following fields appear\.
   + **Current price**: The current Spot price in each Availability Zone is displayed for the instance type that you selected\.
   + \(Optional\) **Maximum price**: You can leave the field empty, or you can specify the maximum amount you're willing to pay\.
     + If you leave the field empty, then the maximum price defaults to the current On\-Demand price\. Your Spot Instance launches at the current Spot price, not exceeding the On\-Demand price\.
     + If you specify a maximum price that is more than the current Spot Price, your Spot Instance launches and is charged at the current Spot price\.
     + If you specify a maximum price that is lower than the Spot price, your Spot Instance is not launched\.
   + **Persistent request**: Choose **Persistent request** to resubmit the Spot Instance request if your Spot Instance is interrupted\.
   + **Interruption behavior**: By default, the Spot service terminates a Spot Instance when it is interrupted\. If you choose **Persistent request**, you can then specify that the Spot service stops or hibernates your Spot Instance when it's interrupted\. For more information, see [Interruption behaviors](spot-interruptions.md#interruption-behavior)\.
   + \(Optional\) **Request valid to**: Choose **Edit** to specify when the Spot Instance request expires\.

   For more information about configuring your Spot Instance, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.

1. The AMI you selected includes one or more volumes of storage, including the root device volume\. On the **Add Storage** page, you can specify additional volumes to attach to the instance by choosing **Add New Volume**\. For more information, see [Step 4: Add Storage](launching-instance.md#step-4-add-storage)\.

1. On the **Add Tags** page, specify [tags](Using_Tags.md) by providing key and value combinations\. For more information, see [Step 5: Add Tags](launching-instance.md#step-5-add-tags)\.

1. On the **Configure Security Group** page, use a security group to define firewall rules for your instance\. These rules specify which incoming network traffic is delivered to your instance\. All other traffic is ignored\. \(For more information about security groups, see [Amazon EC2 security groups for Linux instances](ec2-security-groups.md)\.\) Select or create a security group, and then choose **Review and Launch**\. For more information, see [Step 6: Configure Security Group](launching-instance.md#step-6-configure-security-group)\.

1. On the **Review Instance Launch** page, check the details of your instance, and make any necessary changes by choosing the appropriate **Edit** link\. When you are ready, choose **Launch**\. For more information, see [Step 7: Review Instance Launch and Select Key Pair](launching-instance.md#step-7-review-instance-launch)\.

1. In the **Select an existing key pair or create a new key pair** dialog box, you can choose an existing key pair, or create a new one\. For example, choose **Choose an existing key pair**, then select the key pair that you created when getting set up\. For more information, see [Amazon EC2 key pairs and Linux instances](ec2-key-pairs.md)\.
**Important**  
If you choose the **Proceed without key pair** option, you won't be able to connect to the instance unless you choose an AMI that is configured to allow users another way to log in\.

1. To launch your instance, select the acknowledgment check box, then choose **Launch Instances**\.

   If the instance fails to launch or the state immediately goes to `terminated` instead of `running`, see [Troubleshooting instance launch issues](troubleshooting-launch.md)\.

**To create a Spot Instance request using [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) \(AWS CLI\)**  
Use the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command to create a one\-time request\.

```
aws ec2 request-spot-instances \
    --instance-count 5 \
    --type "one-time" \
    --launch-specification file://specification.json
```

Use the [request\-spot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command to create a persistent request\.

```
aws ec2 request-spot-instances \
    --instance-count 5 \
    --type "persistent" \
    --launch-specification file://specification.json
```

For example launch specification files to use with these commands, see [Spot Instance request example launch specifications](spot-request-examples.md)\. If you download a launch specification file from the console, you must use the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command instead \(the console specifies a Spot request using a Spot Fleet\)\.

**To create a Spot Instance request using [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-instances.html) command and specify the Spot Instance options in the `--instance-market-options` parameter\.

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type t2.micro \
    --count 5 \
    --subnet-id subnet-08fc749671b2d077c \
    --key-name MyKeyPair \
    --security-group-ids sg-0b0384b66d7d692f9 \
    --instance-market-options file://spot-options.json
```

The following is the data structure to specify in the JSON file for `--instance-market-options`\. You can also specify `BlockDurationMinutes`, `ValidUntil`, and `InstanceInterruptionBehavior`\. If you do not specify a field in the data structure, the default value is used\. This example creates a `one-time` request and specifies `0.02` as the maximum price you're willing to pay for the Spot Instance\. 

```
{
  "MarketType": "spot",
  "SpotOptions": {
    "MaxPrice": "0.02",
    "SpotInstanceType": "one-time"
  }
}
```

## Finding running Spot Instances<a name="using-spot-instances-running"></a>

Amazon EC2 launches a Spot Instance when the maximum price exceeds the Spot price and capacity is available\. A Spot Instance runs until it is interrupted or you terminate it yourself\. If your maximum price is exactly equal to the Spot price, there is a chance that your Spot Instance remains running, depending on demand\.

**To find running Spot Instances \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\. You can see both Spot Instance requests and Spot Fleet requests\. If a Spot Instance request has been fulfilled, **Capacity** is the ID of the Spot Instance\. For a Spot Fleet, **Capacity** indicates how much of the requested capacity has been fulfilled\. To view the IDs of the instances in a Spot Fleet, choose the expand arrow, or select the fleet and choose **Instances**\.
**Note**  
For Spot Instance requests that are created by a Spot Fleet, the requests are not tagged instantly with the system tag that indicates the Spot Fleet to which they belong, and for a period of time may appear separate from Spot Fleet request\.

   Alternatively, in the navigation pane, choose **Instances**\. In the top right corner, choose the settings icon \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \), and then under **Attribute columns**, select **Instance lifecycle**\. For each instance, **Instance lifecycle** is either `normal`, `spot`, or `scheduled`\.

**To find running Spot Instances \(AWS CLI\)**  
To enumerate your Spot Instances, use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command with the `--query` option\.

```
aws ec2 describe-spot-instance-requests \
    --query "SpotInstanceRequests[*].{ID:InstanceId}"
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

Alternatively, you can enumerate your Spot Instances using the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command with the `--filters` option\.

```
aws ec2 describe-instances \
    --filters "Name=instance-lifecycle,Values=spot"
```

To describe a single Spot Instance instance, use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command with the `--spot-instance-request-ids` option\.

```
aws ec2 describe-spot-instance-requests \
    --spot-instance-request-ids sir-08b93456
```

## Tagging Spot Instance requests<a name="concepts-spot-instances-request-tags"></a>

To help categorize and manage your Spot Instance requests, you can tag them with custom metadata\. You can assign a tag to a Spot Instance request when you create it, or afterward\. You can assign tags using the Amazon EC2 console or a command line tool\.

When you tag a Spot Instance request, the instances and volumes that are launched by the Spot Instance request are not automatically tagged\. You need to explicitly tag the instances and volumes launched by the Spot Instance request\. You can assign a tag to a Spot Instance and volumes during launch, or afterward\.

For more information about how tags work, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

**Topics**
+ [Prerequisites](#tag-spot-request-prereqs)
+ [Tagging a new Spot Instance request](#tag-new-spot-instance-request)
+ [Tagging an existing Spot Instance request](#tag-existing-spot-instance-request)
+ [Viewing Spot Instance request tags](#view-spot-instance-request-tags)

### Prerequisites<a name="tag-spot-request-prereqs"></a>

Grant the IAM user the permission to tag resources\. For more information about IAM policies and example policies, see [Example: Tagging resources](ExamplePolicies_EC2.md#iam-example-taggingresources)\.

The IAM policy you create is determined by which method you use for creating a Spot Instance request\.
+ If you use the launch instance wizard or `run-instances` to request Spot Instances, see [To grant an IAM user the permission to tag resources when using the launch instance wizard or run-instances](#iam-run-instances)\.
+ If you use the Spot console to request Spot Instances with a defined duration or use the `request-spot-instances` command to request Spot Instances, see [To grant an IAM user the permission to tag resources when using request-spot-instances](#iam-request-spot-instances)\.

**To grant an IAM user the permission to tag resources when using the launch instance wizard or run\-instances**  
Create a IAM policy that includes the following:
+ The `ec2:RunInstances` action\. This grants the IAM user permission to launch an instance\.
+ For `Resource`, specify `spot-instances-request`\. This allows users to create Spot Instance requests, which request Spot Instances\.
+ The `ec2:CreateTags` action\. This grants the IAM user permission to create tags\.
+ For `Resource`, specify `*`\. This allows users to tag all resources that are created during instance launch\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowLaunchInstances",
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances"
            ],
            "Resource": [
                "arn:aws:ec2:us-east-1::image/*",
                "arn:aws:ec2:us-east-1:*:subnet/*",
                "arn:aws:ec2:us-east-1:*:network-interface/*",
                "arn:aws:ec2:us-east-1:*:security-group/*",
                "arn:aws:ec2:us-east-1:*:key-pair/*",
                "arn:aws:ec2:us-east-1:*:volume/*",
                "arn:aws:ec2:us-east-1:*:instance/*",
                "arn:aws:ec2:us-east-1:*:spot-instances-request/*"
            ]
        },
        {
            "Sid": "TagSpotInstanceRequests",
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }
    ]
}
```

**Note**  
When you use the RunInstances action to create Spot Instance requests and tag the Spot Instance requests on create, you need to be aware of how Amazon EC2 evaluates the `spot-instances-request` resource in the RunInstances statement\.  
The `spot-instances-request` resource is evaluated in the IAM policy as follows:  
If you don't tag a Spot Instance request on create, Amazon EC2 does not evaluate the `spot-instances-request` resource in the RunInstances statement\.
If you tag a Spot Instance request on create, Amazon EC2 evaluates the `spot-instances-request` resource in the RunInstances statement\.
Therefore, for the `spot-instances-request` resource, the following rules apply to the IAM policy:  
If you use RunInstances to create a Spot Instance request and you don't intend to tag the Spot Instance request on create, you don’t need to explicitly allow the `spot-instances-request` resource; the call will succeed\.
If you use RunInstances to create a Spot Instance request and intend to tag the Spot Instance request on create, you must include the `spot-instances-request` resource in the RunInstances allow statement, otherwise the call will fail\.
If you use RunInstances to create a Spot Instance request and intend to tag the Spot Instance request on create, you must specify the `spot-instances-request` resource or include a `*` wildcard in the CreateTags allow statement, otherwise the call will fail\.
For example IAM policies, including policies that are not supported for Spot Instance requests, see [Working with Spot Instances](ExamplePolicies_EC2.md#iam-example-spot-instances)\.

**To grant an IAM user the permission to tag resources when using request\-spot\-instances**  
Create a IAM policy that includes the following:
+ The `ec2:RequestSpotInstances` action\. This grants the IAM user permission to create a Spot Instance request\.
+ The `ec2:CreateTags` action\. This grants the IAM user permission to create tags\.
+ For `Resource`, specify `spot-instances-request`\. This allows users to tag only the Spot Instance request\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "TagSpotInstanceRequest",
            "Effect": "Allow",
            "Action": [
                "ec2:RequestSpotInstances",
                "ec2:CreateTags"
            ],
            "Resource": "arn:aws:ec2:us-east-1:111122223333:spot-instances-request/*"
}
```

### Tagging a new Spot Instance request<a name="tag-new-spot-instance-request"></a>

**To tag a new Spot Instance request using the console**

1. Follow the [Creating a Spot Instance request](#using-spot-instances-request) procedure\.

1. To add a tag, on the **Add Tags** page, choose **Add Tag**, and enter the key and value for the tag\. Choose **Add another tag** for each additional tag\.

   For each tag, you can tag the Spot Instance request, the Spot Instances, and the volumes with the same tag\. To tag all three, ensure that **Instances**, **Volumes**, and **Spot Instance Requests** are selected\. To tag only one or two, ensure that the resources you want to tag are selected, and the other resources are cleared\.

1. Complete the required fields to create a Spot Instance request, and then choose **Launch**\. For more information, see [Creating a Spot Instance request](#using-spot-instances-request)\.

**To tag a new Spot Instance request using the AWS CLI**  
To tag a Spot Instance request when you create it, configure the Spot Instance request configuration as follows:
+ Specify the tags for the Spot Instance request using the `--tag-specification` parameter\.
+ For `ResourceType`, specify `spot-instances-request`\. If you specify another value, the Spot Instance request will fail\.
+ For `Tags`, specify the key\-value pair\. You can specify more than one key\-value pair\.

In the following example, the Spot Instance request is tagged with two tags: Key=Environment and Value=Production, and Key=Cost\-Center and Value=123\.

```
aws ec2 request-spot-instances \
    --instance-count 5 \
    --type "one-time" \
    --launch-specification file://specification.json \
    --tag-specification 'ResourceType=spot-instances-request,Tags=[{Key=Environment,Value=Production},{Key=Cost-Center,Value=123}]'
```

### Tagging an existing Spot Instance request<a name="tag-existing-spot-instance-request"></a>

**To tag an existing Spot Instance request using the console**

After you have created a Spot Instance request, you can add tags to the Spot Instance request using the console\.

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. Select your Spot Instance request\.

1. Choose the **Tags** tab and choose **Create Tag**\.

**To tag an existing Spot Instance using the console**  
After your Spot Instance request has launched your Spot Instance, you can add tags to the instance using the console\. For more information, see [Adding and deleting tags on an individual resource](Using_Tags.md#adding-or-deleting-tags)\.

**To tag an existing Spot Instance request or Spot Instance using the AWS CLI**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command to tag existing resources\. In the following example, the existing Spot Instance request and the Spot Instance are tagged with Key=purpose and Value=test\. 

```
aws ec2 create-tags \
    --resources sir-08b93456 i-1234567890abcdef0 \
    --tags Key=purpose,Value=test
```

### Viewing Spot Instance request tags<a name="view-spot-instance-request-tags"></a>

**To view Spot Instance request tags using the console**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. Select your Spot Instance request and choose the **Tags** tab\.

**To describe Spot Instance request tags**  
Use the [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) command to view the tags for the specified resource\. In the following example, you describe the tags for the specified request\.

```
aws ec2 describe-tags \
    --filters "Name=resource-id,Values=sir-11112222-3333-4444-5555-66666EXAMPLE"
```

```
{
    "Tags": [
        {
            "Key": "Environment",
            "ResourceId": "sir-11112222-3333-4444-5555-66666EXAMPLE",
            "ResourceType": "spot-instances-request",
            "Value": "Production"
        },
        {
            "Key": "Another key",
            "ResourceId": "sir-11112222-3333-4444-5555-66666EXAMPLE",
            "ResourceType": "spot-instances-request",
            "Value": "Another value"
        }
    ]
}
```

You can also view the tags of a Spot Instance request by describing the Spot Instance request\.

Use the [describe\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-instance-requests.html) command to view the configuration of the specified Spot Instance request, which includes any tags that were specified for the request\.

```
aws ec2 describe-spot-instance-requests \
    --spot-instance-request-ids sir-11112222-3333-4444-5555-66666EXAMPLE
```

```
{
    "SpotInstanceRequests": [
        {
            "CreateTime": "2020-06-24T14:22:11+00:00",
            "InstanceId": "i-1234567890EXAMPLE",
            "LaunchSpecification": {
                "SecurityGroups": [
                    {
                        "GroupName": "launch-wizard-6",
                        "GroupId": "sg-1234567890EXAMPLE"
                    }
                ],
                "BlockDeviceMappings": [
                    {
                        "DeviceName": "/dev/xvda",
                        "Ebs": {
                            "DeleteOnTermination": true,
                            "VolumeSize": 8,
                            "VolumeType": "gp2"
                        }
                    }
                ],
                "ImageId": "ami-1234567890EXAMPLE",
                "InstanceType": "t2.micro",
                "KeyName": "my-key-pair",
                "NetworkInterfaces": [
                    {
                        "DeleteOnTermination": true,
                        "DeviceIndex": 0,
                        "SubnetId": "subnet-11122233"
                    }
                ],
                "Placement": {
                    "AvailabilityZone": "eu-west-1c",
                    "Tenancy": "default"
                },
                "Monitoring": {
                    "Enabled": false
                }
            },
            "LaunchedAvailabilityZone": "eu-west-1c",
            "ProductDescription": "Linux/UNIX",
            "SpotInstanceRequestId": "sir-1234567890EXAMPLE",
            "SpotPrice": "0.012600",
            "State": "active",
            "Status": {
                "Code": "fulfilled",
                "Message": "Your spot request is fulfilled.",
                "UpdateTime": "2020-06-25T18:30:21+00:00"
            },
            "Tags": [
                {
                    "Key": "Environment",
                    "Value": "Production"
                },
                {
                    "Key": "Another key",
                    "Value": "Another value"
                }
            ],
            "Type": "one-time",
            "InstanceInterruptionBehavior": "terminate"
        }
    ]
}
```

## Canceling a Spot Instance request<a name="using-spot-instances-cancel"></a>

If you no longer want your Spot Instance request, you can cancel it\. You can only cancel Spot Instance requests that are `open`, `active`, or `disabled`\.
+ Your Spot Instance request is `open` when your request has not yet been fulfilled and no instances have been launched\.
+ Your Spot Instance request is `active` when your request has been fulfilled and Spot Instances have launched as a result\. 
+ Your Spot Instance request is `disabled` when you stop your Spot Instance\.

If your Spot Instance request is `active` and has an associated running Spot Instance, canceling the request does not terminate the instance\. For more information about terminating a Spot Instance, see [Terminating a Spot Instance](#terminating-a-spot-instance)\.

**To cancel a Spot Instance request \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests** and select the Spot request\.

1. Choose **Actions**, **Cancel request**\.

1. \(Optional\) If you are finished with the associated Spot Instances, you can terminate them\. In the **Cancel Spot request** dialog box, select **Terminate instances**, and then choose **Confirm**\.

**To cancel a Spot Instance request \(AWS CLI\)**
+ Use the [cancel\-spot\-instance\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-spot-instance-requests.html) command to cancel the specified Spot request\.

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

------
#### [ New console ]

**To stop a Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the Spot Instance\.

1. Choose **Instance state**, **Stop instance**\.

1. When prompted for confirmation, choose **Stop**\.

------
#### [ Old console ]

**To stop a Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the Spot Instance\.

1. Choose **Actions**, **Instance State**, **Stop**\.

------
#### [ AWS CLI ]

**To stop a Spot Instance \(AWS CLI\)**
+ Use the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command to manually stop one or more Spot Instances\.

  ```
  aws ec2 stop-instances --instance-ids i-1234567890abcdef0
  ```

------

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

------
#### [ New console ]

**To start a Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the Spot Instance\.

1. Choose **Instance state**, **Start instance**\.

------
#### [ Old console ]

**To start a Spot Instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the Spot Instance\.

1. Choose **Actions**, **Instance State**, **Start**\.

------
#### [ AWS CLI ]

**To start a Spot Instance \(AWS CLI\)**
+ Use the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command to manually start one or more Spot Instances\.

  ```
  aws ec2 start-instances --instance-ids i-1234567890abcdef0
  ```

------

## Terminating a Spot Instance<a name="terminating-a-spot-instance"></a>

If your Spot Instance request is `active` and has an associated running Spot Instance, or your Spot Instance request is `disabled` and has an associated stopped Spot Instance, canceling the request does not terminate the instance; you must terminate the running Spot Instance manually\.

If you terminate a running or stopped Spot Instance that was launched by a persistent Spot request, the Spot request returns to the `open` state so that a new Spot Instance can be launched\. To cancel a persistent Spot request and terminate its Spot Instances, you must cancel the Spot request first and then terminate the Spot Instances\. Otherwise, the persistent Spot request can launch a new instance\. For more information about canceling a Spot Instance request, see [Canceling a Spot Instance request](#using-spot-instances-cancel)\.

------
#### [ New console ]

**To manually terminate a Spot Instance using the console**

1. Before you terminate an instance, verify that you won't lose any data by checking that your Amazon EBS volumes won't be deleted on termination and that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. To confirm that the instance is a Spot Instance, check that **spot** appears in the **Instance lifecycle** column\.

1. Select the instance, and choose **Actions**, **Instance state**, **Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

------
#### [ Old console ]

**To manually terminate a Spot Instance using the console**

1. Before you terminate an instance, verify that you won't lose any data by checking that your Amazon EBS volumes won't be deleted on termination and that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. To confirm that the instance is a Spot Instance, check that **spot** appears in the **Lifecycle** column\.

1. Select the instance, and choose **Actions**, **Instance State**, **Terminate**\.

1. Choose **Yes, Terminate** when prompted for confirmation\.

------
#### [ AWS CLI ]

**To manually terminate a Spot Instance using the AWS CLI**
+ Use the [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) command to manually terminate Spot Instances\.

  ```
  aws ec2 terminate-instances --instance-ids i-1234567890abcdef0 i-0598c7d356eba48d7
  ```

------
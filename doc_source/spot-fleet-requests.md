# Spot Fleet requests<a name="spot-fleet-requests"></a>

To use a Spot Fleet, you create a Spot Fleet request that includes the target capacity, an optional On\-Demand portion, one or more launch specifications for the instances, and the maximum price that you are willing to pay\. Amazon EC2 attempts to maintain your Spot Fleet's target capacity as Spot prices change\. For more information, see [Spot Fleet](spot-fleet.md)\.

There are two types of Spot Fleet requests: `request` and `maintain`\. You can create a Spot Fleet to submit a one\-time request for your desired capacity, or require it to maintain a target capacity over time\. Both types of requests benefit from Spot Fleet's allocation strategy\.

When you make a one\-time request, Spot Fleet places the required requests but does not attempt to replenish Spot Instances if capacity is diminished\. If capacity is not available, Spot Fleet does not submit requests in alternative Spot pools\.

To maintain a target capacity, Spot Fleet places requests to meet the target capacity and automatically replenish any interrupted instances\.

It is not possible to modify the target capacity of a one\-time request after it's been submitted\. To change the target capacity, cancel the request and submit a new one\.

A Spot Fleet request remains active until it expires or you cancel it\. When you cancel a Spot Fleet request, you may specify whether canceling your Spot Fleet request terminates the Spot Instances in your Spot Fleet\.

Each launch specification includes the information that Amazon EC2 needs to launch an instance, such as an AMI, instance type, subnet or Availability Zone, and one or more security groups\.

**Topics**
+ [Spot Fleet request states](#spot-fleet-states)
+ [Spot Fleet health checks](#spot-fleet-health-checks)
+ [Plan a Spot Fleet request](#plan-spot-fleet)
+ [Spot Fleet permissions](#spot-fleet-prerequisites)
+ [Create a Spot Fleet request](#create-spot-fleet)
+ [Tag a Spot Fleet](#tag-spot-fleet)
+ [Monitor your Spot Fleet](#manage-spot-fleet)
+ [Modify a Spot Fleet request](#modify-spot-fleet)
+ [Cancel a Spot Fleet request](#cancel-spot-fleet)
+ [Spot Fleet example configurations](spot-fleet-examples.md)

## Spot Fleet request states<a name="spot-fleet-states"></a>

A Spot Fleet request can be in one of the following states:
+ `submitted` – The Spot Fleet request is being evaluated and Amazon EC2 is preparing to launch the target number of instances\.
+ `active` – The Spot Fleet has been validated and Amazon EC2 is attempting to maintain the target number of running Spot Instances\. The request remains in this state until it is modified or canceled\.
+ `modifying` – The Spot Fleet request is being modified\. The request remains in this state until the modification is fully processed or the Spot Fleet is canceled\. A one\-time `request` cannot be modified, and this state does not apply to such Spot requests\.
+ `cancelled_running` – The Spot Fleet is canceled and does not launch additional Spot Instances\. Its existing Spot Instances continue to run until they are interrupted or terminated\. The request remains in this state until all instances are interrupted or terminated\.
+ `cancelled_terminating` – The Spot Fleet is canceled and its Spot Instances are terminating\. The request remains in this state until all instances are terminated\.
+ `cancelled` – The Spot Fleet is canceled and has no running Spot Instances\. The Spot Fleet request is deleted two days after its instances were terminated\.

The following illustration represents the transitions between the request states\. If you exceed your Spot Fleet limits, the request is canceled immediately\.

![\[Spot Fleet request states\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/Spot_fleet_states.png)

## Spot Fleet health checks<a name="spot-fleet-health-checks"></a>

Spot Fleet checks the health status of the Spot Instances in the fleet every two minutes\. The health status of an instance is either `healthy` or `unhealthy`\.

Spot Fleet determines the health status of an instance by using the status checks provided by Amazon EC2\. An instance is determined as `unhealthy` when the the status of either the instance status check or the system status check is `impaired` for three consecutive health checks\. For more information, see [Status checks for your instances](monitoring-system-instance-status-check.md)\.

You can configure your fleet to replace unhealthy Spot Instances\. After enabling health check replacement, a Spot Instance is replaced when it is reported as `unhealthy`\. The fleet could go below its target capacity for up to a few minutes while an unhealthy Spot Instance is being replaced\.

**Requirements**
+ Health check replacement is supported only for Spot Fleets that maintain a target capacity \(fleets of type `maintain`\), not for one\-time Spot Fleets \(fleets of type `request`\)\.
+ Health check replacement is supported only for Spot Instances\. This feature is not supported for On\-Demand Instances\.
+ You can configure your Spot Fleet to replace unhealthy instances only when you create it\.
+ IAM users can use health check replacement only if they have permission to call the `ec2:DescribeInstanceStatus` action\.

------
#### [ Console ]

**To configure a Spot Fleet to replace unhealthy Spot Instances using the console**

1. Follow the steps for creating a Spot Fleet\. For more information, see [Create a Spot Fleet request using defined parameters \(console\)](#create-spot-fleet-advanced)\.

1. To configure the fleet to replace unhealthy Spot Instances, for **Health check**, choose **Replace unhealthy instances**\. To enable this option, you must first choose **Maintain target capacity**\.

------
#### [ AWS CLI ]

**To configure a Spot Fleet to replace unhealthy Spot Instances using the AWS CLI**

1. Follow the steps for creating a Spot Fleet\. For more information, see [Create a Spot Fleet using the AWS CLI](#create-spot-fleet-cli)\.

1. To configure the fleet to replace unhealthy Spot Instances, for `ReplaceUnhealthyInstances`, enter `true`\. 

------

## Plan a Spot Fleet request<a name="plan-spot-fleet"></a>

Before you create a Spot Fleet request, review [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices)\. Use these best practices when you plan your Spot Fleet request so that you can provision the type of instances you want at the lowest possible price\. We also recommend that you do the following:
+ Determine whether you want to create a Spot Fleet that submits a one\-time request for the desired target capacity, or one that maintains a target capacity over time\.
+ Determine the instance types that meet your application requirements\.
+ Determine the target capacity for your Spot Fleet request\. You can set the target capacity in instances or in custom units\. For more information, see [Spot Fleet instance weighting](how-spot-fleet-works.md#spot-instance-weighting)\.
+ Determine what portion of the Spot Fleet target capacity must be On\-Demand capacity\. You can specify 0 for On\-Demand capacity\.
+ Determine your price per unit, if you are using instance weighting\. To calculate the price per unit, divide the price per instance hour by the number of units \(or weight\) that this instance represents\. If you are not using instance weighting, the default price per unit is the price per instance hour\.
+ Review the possible options for your Spot Fleet request\. For more information, see the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command in the *AWS CLI Command Reference*\. For additional examples, see [Spot Fleet example configurations](spot-fleet-examples.md)\.

## Spot Fleet permissions<a name="spot-fleet-prerequisites"></a>

If your IAM users will create or manage a Spot Fleet, you need to grant them the required permissions\.

If you use the Amazon EC2 console to create a Spot Fleet, it creates a service\-linked role named `AWSServiceRoleForEC2SpotFleet` and a role named `aws-ec2-spot-fleet-tagging-role` that grant the Spot Fleet the permissions to request, launch, terminate, and tag resources on your behalf\. If you use the AWS CLI or an API, you must ensure that these roles exist\.

Use the following instructions to grant the required permissions and create the roles\.

**Topics**
+ [Grant permission to IAM users for Spot Fleet](#spot-fleet-iam-users)
+ [Service\-linked role for Spot Fleet](#service-linked-roles-spot-fleet-requests)
+ [IAM role for Spot Fleet](#spot-fleet-service-linked-role)

### Grant permission to IAM users for Spot Fleet<a name="spot-fleet-iam-users"></a>

If your IAM users will create or manage a Spot Fleet, be sure to grant them the required permissions as follows\.

**To grant an IAM user permissions for Spot Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**, **Create policy**\.

1. On the **Create policy** page, choose **JSON**, and replace the text with the following\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "ec2:RunInstances",
                   "ec2:CreateTags",
                   "ec2:RequestSpotFleet",
                   "ec2:ModifySpotFleetRequest",
                   "ec2:CancelSpotFleetRequests",
                   "ec2:DescribeSpotFleetRequests",
                   "ec2:DescribeSpotFleetInstances",
                   "ec2:DescribeSpotFleetRequestHistory"
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": "iam:PassRole",
               "Resource": "arn:aws:iam::*:role/aws-ec2-spot-fleet-tagging-role"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "iam:CreateServiceLinkedRole",
                   "iam:ListRoles",
                   "iam:ListInstanceProfiles"
               ],
               "Resource": "*"
           }
       ]
   }
   ```

   The preceding example policy grants an IAM user the permissions required for most Spot Fleet use cases\. To limit the user to specific API actions, specify only those API actions instead\.

   **Required EC2 and IAM APIs**

   The following APIs must be included in the policy:
   + `ec2:RunInstances` – Required to launch instances in a Spot Fleet
   + `ec2:CreateTags` – Required to tag the Spot Fleet request, instances, or volumes
   + `iam:PassRole` – Required to specify the Spot Fleet role
   + `iam:CreateServiceLinkedRole` – Required to create the service\-linked role
   + `iam:ListRoles` – Required to enumerate existing IAM roles
   + `iam:ListInstanceProfiles` – Required to enumerate existing instance profiles
**Important**  
If you specify a role for the IAM instance profile in the launch specification or launch template, you must grant the IAM user the permission to pass the role to the service\. To do this, in the IAM policy include `"arn:aws:iam::*:role/IamInstanceProfile-role"` as a resource for the `iam:PassRole` action\. For more information, see [Granting a User Permissions to Pass a Role to an AWS Service](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_passrole.html) in the *IAM User Guide*\.

   **Spot Fleet APIs**

   Add the following Spot Fleet API actions to your policy, as needed:
   + `ec2:RequestSpotFleet`
   + `ec2:ModifySpotFleetRequest`
   + `ec2:CancelSpotFleetRequests`
   + `ec2:DescribeSpotFleetRequests`
   + `ec2:DescribeSpotFleetInstances`
   + `ec2:DescribeSpotFleetRequestHistory`

   **Optional IAM APIs**

   \(Optional\) To enable an IAM user to create roles or instance profiles using the IAM console, you must add the following actions to the policy:
   + `iam:AddRoleToInstanceProfile`
   + `iam:AttachRolePolicy`
   + `iam:CreateInstanceProfile`
   + `iam:CreateRole`
   + `iam:GetRole`
   + `iam:ListPolicies`

1. Choose **Review policy**\.

1. On the **Review policy** page, enter a policy name and description, and choose **Create policy**\.

1. In the navigation pane, choose **Users** and select the user\.

1. Choose **Permissions**, **Add permissions**\.

1. Choose **Attach existing policies directly**\. Select the policy that you created earlier and choose **Next: Review**\.

1. Choose **Add permissions**\.

### Service\-linked role for Spot Fleet<a name="service-linked-roles-spot-fleet-requests"></a>

Amazon EC2 uses service\-linked roles for the permissions that it requires to call other AWS services on your behalf\. A service\-linked role is a unique type of IAM role that is linked directly to an AWS service\. Service\-linked roles provide a secure way to delegate permissions to AWS services because only the linked service can assume a service\-linked role\. For more information, see [Using Service\-Linked Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

Amazon EC2 uses the service\-linked role named **AWSServiceRoleForEC2SpotFleet** to launch and manage instances on your behalf\.

**Important**  
If you specify an [encrypted AMI](AMIEncryption.md) or an [encrypted Amazon EBS snapshot](EBSEncryption.md) in your Spot Fleet, you must grant the **AWSServiceRoleForEC2SpotFleet** role permission to use the CMK so that Amazon EC2 can launch instances on your behalf\. For more information, see [Grant access to CMKs for use with encrypted AMIs and EBS snapshots](#spot-fleet-service-linked-roles-access-to-cmks)\.

#### Permissions granted by AWSServiceRoleForEC2SpotFleet<a name="service-linked-role-permissions-granted-by-AWSServiceRoleForEC2SpotFleet"></a>

Amazon EC2 uses **AWSServiceRoleForEC2SpotFleet** to complete the following actions:
+ `ec2:RequestSpotInstances` \- Request Spot Instances
+ `ec2:RunInstances` \- Launch instances
+ `ec2:TerminateInstances` \- Terminate instances
+ `ec2:DescribeImages` \- Describe Amazon Machine Images \(AMIs\) for the instances
+ `ec2:DescribeInstanceStatus` \- Describe the status of the instances
+ `ec2:DescribeSubnets` \- Describe the subnets for the instances
+ `ec2:CreateTags` \- Add tags to the Spot Fleet request, instances, and volumes
+ `elasticloadbalancing:RegisterInstancesWithLoadBalancer` \- Add the specified instances to the specified load balancer
+ `elasticloadbalancing:RegisterTargets` \- Register the specified targets with the specified target group

#### Create the service\-linked role<a name="service-linked-role-creating-for-spot-fleet"></a>

Under most circumstances, you don't need to manually create a service\-linked role\. Amazon EC2 creates the **AWSServiceRoleForEC2SpotFleet** service\-linked role the first time you create a Spot Fleet using the console\. 

If you use the AWS CLI or an API, you must ensure that this role exists\.

If you had an active Spot Fleet request before October 2017, when Amazon EC2 began supporting this service\-linked role, Amazon EC2 created the **AWSServiceRoleForEC2SpotFleet** role in your AWS account\. For more information, see [A New Role Appeared in My AWS Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_roles.html#troubleshoot_roles_new-role-appeared) in the *IAM User Guide*\.

Ensure that this role exists before you use the AWS CLI or an API to create a Spot Fleet\.

**To create AWSServiceRoleForEC2SpotFleet using the console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. For **Select type of trusted entity**, choose **AWS service**\.

1.  In the list of services, choose **EC2**\.

1. In the **Select your use case** section, choose **EC2 \- Spot Fleet**

1. Choose **Next: Permissions**\.

1. On the next page, choose **Next:Review**\.

1. On the **Review** page, choose **Create role**\.

**To create AWSServiceRoleForEC2SpotFleet using the AWS CLI**  
Use the [create\-service\-linked\-role](https://docs.aws.amazon.com/cli/latest/reference/iam/create-service-linked-role.html) command as follows\.

```
aws iam create-service-linked-role --aws-service-name spotfleet.amazonaws.com
```

If you no longer need to use Spot Fleet, we recommend that you delete the **AWSServiceRoleForEC2SpotFleet** role\. After this role is deleted from your account, Amazon EC2 will create the role again if you request a Spot Fleet using the console\. For more information, see [Deleting a Service\-Linked Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html#delete-service-linked-role) in the *IAM User Guide*\.

#### Grant access to CMKs for use with encrypted AMIs and EBS snapshots<a name="spot-fleet-service-linked-roles-access-to-cmks"></a>

If you specify an [encrypted AMI](AMIEncryption.md) or an [encrypted Amazon EBS snapshot](EBSEncryption.md) in your Spot Fleet request and you use a customer managed customer master key \(CMK\) for encryption, you must grant the **AWSServiceRoleForEC2SpotFleet** role permission to use the CMK so that Amazon EC2 can launch instances on your behalf\. To do this, you must add a grant to the CMK, as shown in the following procedure\.

When providing permissions, grants are an alternative to key policies\. For more information, see [Using Grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) and [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.

**To grant the AWSServiceRoleForEC2SpotFleet role permissions to use the CMK**
+ Use the [create\-grant](https://docs.aws.amazon.com/cli/latest/reference/kms/create-grant.html) command to add a grant to the CMK and to specify the principal \(the **AWSServiceRoleForEC2SpotFleet** service\-linked role\) that is given permission to perform the operations that the grant permits\. The CMK is specified by the `key-id` parameter and the ARN of the CMK\. The principal is specified by the `grantee-principal` parameter and the ARN of the **AWSServiceRoleForEC2SpotFleet** service\-linked role\.

  ```
  aws kms create-grant \
      --region us-east-1 \
      --key-id arn:aws:kms:us-east-1:444455556666:key/1234abcd-12ab-34cd-56ef-1234567890ab \
      --grantee-principal arn:aws:iam::111122223333:role/AWSServiceRoleForEC2SpotFleet \
      --operations "Decrypt" "Encrypt" "GenerateDataKey" "GenerateDataKeyWithoutPlaintext" "CreateGrant" "DescribeKey" "ReEncryptFrom" "ReEncryptTo"
  ```

### IAM role for Spot Fleet<a name="spot-fleet-service-linked-role"></a>

The `aws-ec2-spot-fleet-tagging-role` IAM role grants the Spot Fleet permission to tag the Spot Fleet request, instances, and volumes\. For more information, see [Tag a Spot Fleet](#tag-spot-fleet)\.

**Important**  
If you choose to tag instances in the fleet and you choose to maintain target capacity \(the Spot Fleet request is of type `maintain`\), the differences in permissions of the IAM user and the `IamFleetRole` might lead to inconsistent tagging behavior of instances in the fleet\. If the `IamFleetRole` does not include the `CreateTags` permission, some of the instances launched by the fleet might not be tagged\. While we are working to fix this inconsistency, to ensure that all instances launched by the fleet are tagged, we recommend that you use the `aws-ec2-spot-fleet-tagging-role` role for the `IamFleetRole`\. Alternatively, to use an existing role, attach the `AmazonEC2SpotFleetTaggingRole` AWS Managed Policy to the existing role\. Otherwise, you need to manually add the `CreateTags` permission to your existing policy\.

**To create the IAM role for tagging a Spot Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**\.

1. On the **Select type of trusted entity** page, choose **AWS service**, **EC2**, **EC2 \- Spot Fleet Tagging**, **Next: Permissions**\.

1. On the **Attached permissions policy** page, choose **Next:Review**\.

1. On the **Review** page, enter a name for the role \(for example, **aws\-ec2\-spot\-fleet\-tagging\-role**\) and choose **Create role**\.

## Create a Spot Fleet request<a name="create-spot-fleet"></a>

Using the AWS Management Console, quickly create a Spot Fleet request by choosing only your application or task need and minimum compute specs\. Amazon EC2 configures a fleet that best meets your needs and follows Spot best practice\. For more information, see [Quickly create a Spot Fleet request \(console\)](#create-spot-fleet-quick)\. Otherwise, you can modify any of the default settings\. For more information, see [Create a Spot Fleet request using defined parameters \(console\)](#create-spot-fleet-advanced) and [Create a Spot Fleet using the AWS CLI](#create-spot-fleet-cli)\.

**Topics**
+ [Quickly create a Spot Fleet request \(console\)](#create-spot-fleet-quick)
+ [Create a Spot Fleet request using defined parameters \(console\)](#create-spot-fleet-advanced)
+ [Create a Spot Fleet using the AWS CLI](#create-spot-fleet-cli)

### Quickly create a Spot Fleet request \(console\)<a name="create-spot-fleet-quick"></a>

Follow these steps to quickly create a Spot Fleet request\.

**To create a Spot Fleet request using the recommended settings \(console\)**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. If you are new to Spot, you see a welcome page; choose **Get started**\. Otherwise, choose **Request Spot Instances**\.

1. For **Tell us your application or task need**, choose **Load balancing workloads**, **Flexible workloads**, **Big data workloads**, or **Defined duration workloads**\.

1. Under **Configure your instances**, for **Minimum compute unit**, choose the minimum hardware specifications \(vCPUs, memory, and storage\) that you need for your application or task, either **as specs** or **as an instance type**\.
   + For **as specs**, specify the required number of vCPUs and amount of memory\.
   + For **as an instance type**, accept the default instance type, or choose **Change instance type** to choose a different instance type\.

1. Under **Tell us how much capacity you need**, for **Total target capacity**, specify the number of units to request for target capacity\. You can choose instances or vCPUs\.

1. Review the recommended **Fleet request settings** based on your application or task selection, and choose **Launch**\.

### Create a Spot Fleet request using defined parameters \(console\)<a name="create-spot-fleet-advanced"></a>

You can create a Spot Fleet using the parameters that you define\.

**To create a Spot Fleet request using defined parameters \(console\)**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. If you are new to Spot, you see a welcome page; choose **Get started**\. Otherwise, choose **Request Spot Instances**\.

1. For **Tell us your application or task need**, choose **Load balancing workloads**, **Flexible workloads**, **Big data workloads**, or **Defined duration workloads**\.

1. For **Configure your instances**, do the following:

   1. \(Optional\) For **Launch template**, choose a launch template\. The launch template must specify an Amazon Machine Image \(AMI\), as you cannot override the AMI using Spot Fleet if you specify a launch template\.
**Important**  
 If you intend to specify **Optional On\-Demand portion**, you must choose a launch template\.

   1. For **AMI**, choose one of the basic AMIs provided by AWS, or choose **Search for AMI** to use an AMI from our user community, the AWS Marketplace, or one of your own\.

   1. For **Minimum compute unit**, choose the minimum hardware specifications \(vCPUs, memory, and storage\) that you need for your application or task, either **as specs** or **as an instance type**\.
      + For **as specs**, specify the required number of vCPUs and amount of memory\.
      + For **as an instance type**, accept the default instance type, or choose **Change instance type** to choose a different instance type\.

   1. For **Network**, choose an existing VPC or create a new one\.

      \[Existing VPC\] Choose the VPC\.

      \[New VPC\] Choose **Create new VPC** to go the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

   1. \(Optional\) For **Availability Zone**, let AWS choose the Availability Zones for your Spot Instances, or specify one or more Availability Zones\.

      If you have more than one subnet in an Availability Zone, choose the appropriate subnet from **Subnet**\. To add subnets, choose **Create new subnet** to go to the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

   1. \(Optional\) For **Key pair name**, choose an existing key pair or create a new one\.

      \[Existing key pair\] Choose the key pair\.

      \[New key pair\] Choose **Create new key pair** to go the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

1. \(Optional\) For **Additional configurations**, do the following:

   1. \(Optional\) To add storage, specify additional instance store volumes or Amazon EBS volumes, depending on the instance type\.

   1. \(Optional\) To enable Amazon EBS optimization, for **EBS\-optimized**, choose **Launch EBS\-optimized instances**\.

   1. \(Optional\) To add temporary block\-level storage for your instances, for **Instance store**, choose **Attach at launch**\.

   1. \(Optional\) By default, basic monitoring is enabled for your instances\. To enable detailed monitoring, for **Monitoring**, choose **Enable CloudWatch detailed monitoring**\.

   1. \(Optional\) To replace unhealthy Spot Instances, for **Health check**, choose **Replace unhealthy instances**\. To enable this option, you must first choose **Maintain target capacity**\.

   1. \(Optional\) To run a Dedicated Spot Instance, for **Tenancy**, choose **Dedicated \- run a dedicated instance**\.

   1. \(Optional\) For **Security groups**, choose one or more security groups or create a new one\.

      \[Existing security group\] Choose one or more security groups\.

      \[New security group\] Choose **Create new security group** to go the Amazon VPC console\. When you are done, return to the wizard and refresh the list\.

   1. \(Optional\) To make your instances reachable from the internet, for **Auto\-assign IPv4 Public IP**, choose **Enable**\.

   1. \(Optional\) To launch your Spot Instances with an IAM role, for **IAM instance profile**, choose the role\.

   1. \(Optional\) To run a start\-up script, copy it to **User data**\.

   1. \(Optional\) To add a tag, choose **Add new tag** and enter the key and value for the tag\. Repeat for each tag\.

      For each tag, to tag the instances and the Spot Fleet request with the same tag, ensure that both **Instance tags** and **Fleet tags** are selected\. To tag only the instances launched by the fleet, clear **Fleet tags**\. To tag only the Spot Fleet request, clear **Instance tags**\. 

1. For **Tell us how much capacity you need**, do the following:

   1. For **Total target capacity**, specify the number of units to request for target capacity\. You can choose instances or vCPUs\. To specify a target capacity of 0 so that you can add capacity later, choose **Maintain target capacity**\.

   1. \(Optional\) For **Optional On\-Demand portion**, specify the number of On\-Demand units to request\. The number must be less than the **Total target capacity**\. Amazon EC2 calculates the difference, and allocates the difference to Spot units to request\.
**Important**  
 To specify an optional On\-Demand portion, you must first choose a launch template\.

   1. \(Optional\) By default, the Spot service terminates Spot Instances when they are interrupted\. To maintain the target capacity, select **Maintain target capacity**\. You can then specify that the Spot service terminates, stops, or hibernates Spot Instances when they are interrupted\. To do so, choose the corresponding option from **Interruption behavior**\.

   1. \(Optional\) To allow Spot Fleet to launch a replacement Spot Instance when an instance rebalance notification is emitted for an existing Spot Instance in the fleet, select **Capacity rebalance**\. For more information, see [Capacity Rebalancing](how-spot-fleet-works.md#spot-fleet-capacity-rebalance)\.
**Note**  
When a replacement instance is launched, the instance marked for rebalance is not automatically terminated\. You can terminate it, or you can leave it running\. You are charged for both instances while they are running\.  
The instance marked for rebalance is at an elevated risk of interruption, and you will receive a two\-minute Spot Instance interruption notice before Amazon EC2 interrupts it\. 

   1. \(Optional\) To control the amount you pay per hour for all the Spot Instances in your fleet, select **Maintain target cost for Spot \(advanced \- optional\)** and then enter the maximum total amount you're willing to pay per hour\. When the maximum total amount is reached, Spot Fleet stops launching Spot Instances even if it hasn’t met the target capacity\. For more information, see [Control spending](how-spot-fleet-works.md#spot-fleet-control-spending)\.

1. For **Fleet request settings**, do the following:

   1. Review the fleet request and fleet allocation strategy based on your application or task selection\. To change the instance types or allocation strategy, clear **Apply recommendations**\.

   1. \(Optional\) To remove instance types, for **Fleet request**, choose **Remove**\. To add instance types, choose **Select instance types**\.

   1. \(Optional\) For **Fleet allocation strategy**, choose the strategy that meets your needs\. For more information, see [Allocation strategy for Spot Instances](how-spot-fleet-works.md#spot-fleet-allocation-strategy)\.

1. For **Additional request details**, do the following:

   1. Review the additional request details\. To make changes, clear **Apply defaults**\.

   1. \(Optional\) For **IAM fleet role**, you can use the default role or choose a different role\. To use the default role after changing the role, choose **Use default role**\.

   1. \(Optional\) For **Maximum price**, you can use the default maximum price \(the On\-Demand price\) or specify the maximum price you are willing to pay\. If your maximum price is lower than the Spot price for the instance types that you selected, your Spot Instances are not launched\.

   1. \(Optional\) To create a request that is valid only during a specific time period, edit **Request valid from** and **Request valid until**\.

   1. \(Optional\) By default, we terminate your Spot Instances when the request expires\. To keep them running after your request expires, clear **Terminate the instances when the request expires**\.

   1. \(Optional\) To register your Spot Instances with a load balancer, choose **Receive traffic from one or more load balancers** and choose one or more Classic Load Balancers or target groups\.

1. \(Optional\) To download a copy of the launch configuration for use with the AWS CLI, choose **JSON config**\.

1. Choose **Launch**\.

   The Spot Fleet request type is `fleet`\. When the request is fulfilled, requests of type `instance` are added, where the state is `active` and the status is `fulfilled`\.

### Create a Spot Fleet using the AWS CLI<a name="create-spot-fleet-cli"></a>

**To create a Spot Fleet request using the AWS CLI**
+ Use the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command to create a Spot Fleet request\.

```
aws ec2 request-spot-fleet --spot-fleet-request-config file://config.json
```

For example configuration files, see [Spot Fleet example configurations](spot-fleet-examples.md)\.

The following is example output:

```
{
    "SpotFleetRequestId": "sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE"
}
```

## Tag a Spot Fleet<a name="tag-spot-fleet"></a>

To help categorize and manage your Spot Fleet requests, you can tag them with custom metadata\. You can assign a tag to a Spot Fleet request when you create it, or afterward\. You can assign tags using the Amazon EC2 console or a command line tool\.

When you tag a Spot Fleet request, the instances and volumes that are launched by the Spot Fleet are not automatically tagged\. You need to explicitly tag the instances and volumes launched by the Spot Fleet\. You can choose to assign tags to only the Spot Fleet request, or to only the instances launched by the fleet, or to only the volumes attached to the instances launched by the fleet, or to all three\.

**Note**  
Volume tags are only supported for volumes that are attached to On\-Demand Instances\. You can't tag volumes that are attached to Spot Instances\.

For more information about how tags work, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

**Topics**
+ [Prerequisite](#tag-spot-fleet-prereqs)
+ [Tag a new Spot Fleet](#tag-new-spot-fleet)
+ [Tag a new Spot Fleet and the instances and volumes that it launches](#tag-new-spot-fleet-and-resources)
+ [Tag an existing Spot Fleet](#tag-existing-spot-fleet)
+ [View Spot Fleet request tags](#view-spot-fleet-tags)

### Prerequisite<a name="tag-spot-fleet-prereqs"></a>

Grant the IAM user the permission to tag resources\. For more information, see [Example: Tag resources](ExamplePolicies_EC2.md#iam-example-taggingresources)\.

**To grant an IAM user the permission to tag resources**  
Create a IAM policy that includes the following:
+ The `ec2:CreateTags` action\. This grants the IAM user permission to create tags\.
+ The `ec2:RequestSpotFleet` action\. This grants the IAM user permission to create a Spot Fleet request\.
+ For `Resource`, you must specify `"*"`\. This allows users to tag all resource types\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "TagSpotFleetRequest",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags",
                "ec2:RequestSpotFleet"
            ],
            "Resource": "*"
}
```

**Important**  
We currently do not support resource\-level permissions for the `spot-fleet-request` resource\. If you specify `spot-fleet-request` as a resource, you will get an unauthorized exception when you try to tag the fleet\. The following example illustrates how *not* to set the policy\.   

```
{
    "Effect": "Allow",
    "Action": [
        "ec2:CreateTags",
        "ec2:RequestSpotFleet"
    ],
    "Resource": "arn:aws:ec2:us-east-1:111122223333:spot-fleet-request/*"
}
```

### Tag a new Spot Fleet<a name="tag-new-spot-fleet"></a>

**To tag a new Spot Fleet request using the console**

1. Follow the [Create a Spot Fleet request using defined parameters \(console\)](#create-spot-fleet-advanced) procedure\.

1. To add a tag, expand **Additional configurations**, choose **Add new tag**, and enter the key and value for the tag\. Repeat for each tag\.

   For each tag, you can tag the Spot Fleet request and the instances with the same tag\. To tag both, ensure that both **Instance tags** and **Fleet tags** are selected\. To tag only the Spot Fleet request, clear **Instance tags**\. To tag only the instances launched by the fleet, clear **Fleet tags**\.

1. Complete the required fields to create a Spot Fleet request, and then choose **Launch**\. For more information, see [Create a Spot Fleet request using defined parameters \(console\)](#create-spot-fleet-advanced)\.

**To tag a new Spot Fleet request using the AWS CLI**  
To tag a Spot Fleet request when you create it, configure the Spot Fleet request configuration as follows:
+ Specify the tags for the Spot Fleet request in `SpotFleetRequestConfig`\.
+ For `ResourceType`, specify `spot-fleet-request`\. If you specify another value, the fleet request will fail\.
+ For `Tags`, specify the key\-value pair\. You can specify more than one key\-value pair\.

In the following example, the Spot Fleet request is tagged with two tags: Key=Environment and Value=Production, and Key=Cost\-Center and Value=123\.

```
{
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "lowestPrice",
        "ExcessCapacityTerminationPolicy": "default",
        "IamFleetRole": "arn:aws:iam::111122223333:role/aws-ec2-spot-fleet-tagging-role",
        "LaunchSpecifications": [
            {
                "ImageId": "ami-0123456789EXAMPLE",
                "InstanceType": "c4.large"
            }
        ],
        "SpotPrice": "5",
        "TargetCapacity": 2,
        "TerminateInstancesWithExpiration": true,
        "Type": "maintain",
        "ReplaceUnhealthyInstances": true,
        "InstanceInterruptionBehavior": "terminate",
        "InstancePoolsToUseCount": 1,
        "TagSpecifications": [
            {
                "ResourceType": "spot-fleet-request",
                "Tags": [
                    {
                        "Key": "Environment",
                        "Value":"Production"
                    },
                    {
                        "Key": "Cost-Center",
                        "Value":"123"
                    }
                ]
            }
        ]
    }
}
```

### Tag a new Spot Fleet and the instances and volumes that it launches<a name="tag-new-spot-fleet-and-resources"></a>

**To tag a new Spot Fleet request and the instances and volumes that it launches using the AWS CLI**  
To tag a Spot Fleet request when you create it, and to tag the instances and volumes when they are launched by the fleet, configure the Spot Fleet request configuration as follows:

**Spot Fleet request tags:**
+ Specify the tags for the Spot Fleet request in `SpotFleetRequestConfig`\.
+ For `ResourceType`, specify `spot-fleet-request`\. If you specify another value, the fleet request will fail\.
+ For `Tags`, specify the key\-value pair\. You can specify more than one key\-value pair\.

**Instance tags:**
+ Specify the tags for the instances in `LaunchSpecifications`\.
+ For `ResourceType`, specify `instance`\. If you specify another value, the fleet request will fail\.
+ For `Tags`, specify the key\-value pair\. You can specify more than one key\-value pair\.

  Alternatively, you can specify the tags for the instance in the [launch template](ec2-launch-templates.md#create-launch-template) that is referenced in the Spot Fleet request\.

**Volume tags:**
+ Specify the tags for the volumes in the [launch template](ec2-launch-templates.md#create-launch-template) that is referenced in the Spot Fleet request\. Volume tagging in `LaunchSpecifications` is not supported\.

In the following example, the Spot Fleet request is tagged with two tags: Key=Environment and Value=Production, and Key=Cost\-Center and Value=123\. The instances that are launched by the fleet are tagged with one tag \(which is the same as one of the tags for the Spot Fleet request\): Key=Cost\-Center and Value=123\.

```
{
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "lowestPrice",
        "ExcessCapacityTerminationPolicy": "default",
        "IamFleetRole": "arn:aws:iam::111122223333:role/aws-ec2-spot-fleet-tagging-role",
        "LaunchSpecifications": [
            {
                "ImageId": "ami-0123456789EXAMPLE",
                "InstanceType": "c4.large",
                "TagSpecifications": [
                    {
                        "ResourceType": "instance",
                        "Tags": [
                            {
                                "Key": "Cost-Center",
                                "Value": "123"
                            }
                        ]
                    }
                ]
            }
        ],
        "SpotPrice": "5",
        "TargetCapacity": 2,
        "TerminateInstancesWithExpiration": true,
        "Type": "maintain",
        "ReplaceUnhealthyInstances": true,
        "InstanceInterruptionBehavior": "terminate",
        "InstancePoolsToUseCount": 1,
        "TagSpecifications": [
            {
                "ResourceType": "spot-fleet-request",
                "Tags": [
                    {
                        "Key": "Environment",
                        "Value":"Production"
                    },
                    {
                        "Key": "Cost-Center",
                        "Value":"123"
                    }
                ]
            }
        ]
    }
}
```

**To tag instances launched by a Spot Fleet using the AWS CLI**  
To tag instances when they are launched by the fleet, you can either specify the tags in the [launch template](ec2-launch-templates.md#create-launch-template) that is referenced in the Spot Fleet request, or you can specify the tags in the Spot Fleet request configuration as follows:
+ Specify the tags for the instances in `LaunchSpecifications`\.
+ For `ResourceType`, specify `instance`\. If you specify another value, the fleet request will fail\.
+ For `Tags`, specify the key\-value pair\. You can specify more than one key\-value pair\.

In the following example, the instances that are launched by the fleet are tagged with one tag: Key=Cost\-Center and Value=123\.

```
{
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "lowestPrice",
        "ExcessCapacityTerminationPolicy": "default",
        "IamFleetRole": "arn:aws:iam::111122223333:role/aws-ec2-spot-fleet-tagging-role",
        "LaunchSpecifications": [
            {
                "ImageId": "ami-0123456789EXAMPLE",
                "InstanceType": "c4.large",
                "TagSpecifications": [
                    {
                        "ResourceType": "instance",
                        "Tags": [
                            {
                                "Key": "Cost-Center",
                                "Value": "123"
                            }
                        ]
                    }
                ]
            }
        ],
        "SpotPrice": "5",
        "TargetCapacity": 2,
        "TerminateInstancesWithExpiration": true,
        "Type": "maintain",
        "ReplaceUnhealthyInstances": true,
        "InstanceInterruptionBehavior": "terminate",
        "InstancePoolsToUseCount": 1
    }
}
```

**To tag volumes attached to On\-Demand Instances launched by a Spot Fleet using the AWS CLI**  
To tag volumes when they are created by the fleet, you must specify the tags in the [launch template](ec2-launch-templates.md#create-launch-template) that is referenced in the Spot Fleet request\.

**Note**  
Volume tags are only supported for volumes that are attached to On\-Demand Instances\. You can't tag volumes that are attached to Spot Instances\.  
Volume tagging in `LaunchSpecifications` is not supported\.

### Tag an existing Spot Fleet<a name="tag-existing-spot-fleet"></a>

**To tag an existing Spot Fleet request using the console**

After you have created a Spot Fleet request, you can add tags to the fleet request using the console\.

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. Select your Spot Fleet request\.

1. Choose the **Tags** tab and choose **Create Tag**\.

**To tag an existing Spot Fleet request using the AWS CLI**  
You can use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command to tag existing resources\. In the following example, the existing Spot Fleet request is tagged with Key=purpose and Value=test\.

```
aws ec2 create-tags \
    --resources sfr-11112222-3333-4444-5555-66666EXAMPLE \
    --tags Key=purpose,Value=test
```

### View Spot Fleet request tags<a name="view-spot-fleet-tags"></a>

**To view Spot Fleet request tags using the console**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. Select your Spot Fleet request and choose the **Tags** tab\.

**To describe Spot Fleet request tags**  
Use the [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) command to view the tags for the specified resource\. In the following example, you describe the tags for the specified Spot Fleet request\.

```
aws ec2 describe-tags \
    --filters "Name=resource-id,Values=sfr-11112222-3333-4444-5555-66666EXAMPLE"
```

```
{
    "Tags": [
        {
            "Key": "Environment",
            "ResourceId": "sfr-11112222-3333-4444-5555-66666EXAMPLE",
            "ResourceType": "spot-fleet-request",
            "Value": "Production"
        },
        {
            "Key": "Another key",
            "ResourceId": "sfr-11112222-3333-4444-5555-66666EXAMPLE",
            "ResourceType": "spot-fleet-request",
            "Value": "Another value"
        }
    ]
}
```

You can also view the tags of a Spot Fleet request by describing the Spot Fleet request\.

Use the [describe\-spot\-fleet\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-requests.html) command to view the configuration of the specified Spot Fleet request, which includes any tags that were specified for the fleet request\.

```
aws ec2 describe-spot-fleet-requests \
    --spot-fleet-request-ids sfr-11112222-3333-4444-5555-66666EXAMPLE
```

```
{
    "SpotFleetRequestConfigs": [
        {
            "ActivityStatus": "fulfilled",
            "CreateTime": "2020-02-13T02:49:19.709Z",
            "SpotFleetRequestConfig": {
                "AllocationStrategy": "capacityOptimized",
                "OnDemandAllocationStrategy": "lowestPrice",
                "ExcessCapacityTerminationPolicy": "Default",
                "FulfilledCapacity": 2.0,
                "OnDemandFulfilledCapacity": 0.0,
                "IamFleetRole": "arn:aws:iam::111122223333:role/aws-ec2-spot-fleet-tagging-role",
                "LaunchSpecifications": [
                    {
                        "ImageId": "ami-0123456789EXAMPLE",
                        "InstanceType": "c4.large"
                    }
                ],
                "TargetCapacity": 2,
                "OnDemandTargetCapacity": 0,
                "Type": "maintain",
                "ReplaceUnhealthyInstances": false,
                "InstanceInterruptionBehavior": "terminate"
            },
            "SpotFleetRequestId": "sfr-11112222-3333-4444-5555-66666EXAMPLE",
            "SpotFleetRequestState": "active",
            "Tags": [
                {
                    "Key": "Environment",
                    "Value": "Production"
                },
                {
                    "Key": "Another key",
                    "Value": "Another value"
                }
            ]
        }
    ]
}
```

## Monitor your Spot Fleet<a name="manage-spot-fleet"></a>

The Spot Fleet launches Spot Instances when your maximum price exceeds the Spot price and capacity is available\. The Spot Instances run until they are interrupted or you terminate them\.

**To monitor your Spot Fleet \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Spot Requests**\.

1. Select your Spot Fleet request\. To see the configuration details, choose **Description**\.

1. To list the Spot Instances for the Spot Fleet, choose **Instances**\.

1. To view the history for the Spot Fleet, choose **History**\.

**To monitor your Spot Fleet \(AWS CLI\)**  
Use the [describe\-spot\-fleet\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-requests.html) command to describe your Spot Fleet requests\.

```
aws ec2 describe-spot-fleet-requests
```

Use the [describe\-spot\-fleet\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-instances.html) command to describe the Spot Instances for the specified Spot Fleet\.

```
aws ec2 describe-spot-fleet-instances \
    --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE
```

Use the [describe\-spot\-fleet\-request\-history](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-request-history.html) command to describe the history for the specified Spot Fleet request\.

```
aws ec2 describe-spot-fleet-request-history \
    --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --start-time 2015-05-18T00:00:00Z
```

## Modify a Spot Fleet request<a name="modify-spot-fleet"></a>

You can modify an active Spot Fleet request to complete the following tasks:
+ Increase the target capacity and On\-Demand portion
+ Decrease the target capacity and On\-Demand portion

**Note**  
You can't modify a one\-time Spot Fleet request\. You can only modify a Spot Fleet request if you selected **Maintain target capacity** when you created the Spot Fleet request\.

When you increase the target capacity, the Spot Fleet launches additional Spot Instances\. When you increase the On\-Demand portion, the Spot Fleet launches additional On\-Demand Instances\.

When you increase the target capacity, the Spot Fleet launches the additional Spot Instances according to the allocation strategy for its Spot Fleet request\. If the allocation strategy is `lowestPrice`, the Spot Fleet launches the instances from the lowest\-priced Spot capacity pool in the Spot Fleet request\. If the allocation strategy is `diversified`, the Spot Fleet distributes the instances across the pools in the Spot Fleet request\.

When you decrease the target capacity, the Spot Fleet cancels any open requests that exceed the new target capacity\. You can request that the Spot Fleet terminate Spot Instances until the size of the fleet reaches the new target capacity\. If the allocation strategy is `lowestPrice`, the Spot Fleet terminates the instances with the highest price per unit\. If the allocation strategy is `diversified`, the Spot Fleet terminates instances across the pools\. Alternatively, you can request that the Spot Fleet keep the fleet at its current size, but not replace any Spot Instances that are interrupted or that you terminate manually\.

When a Spot Fleet terminates an instance because the target capacity was decreased, the instance receives a Spot Instance interruption notice\.

**To modify a Spot Fleet request \(console\)**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot/home/fleet](https://console.aws.amazon.com/ec2spot/home/fleet)\.

1. Select your Spot Fleet request\.

1. Choose **Actions**, **Modify target capacity**\.

1. In **Modify target capacity**, do the following:

   1. Enter the new target capacity and On\-Demand portion\.

   1. \(Optional\) If you are decreasing the target capacity but want to keep the fleet at its current size, clear **Terminate instances**\.

   1. Choose **Submit**\.

**To modify a Spot Fleet request using the AWS CLI**  
Use the [modify\-spot\-fleet\-request](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-spot-fleet-request.html) command to update the target capacity of the specified Spot Fleet request\.

```
aws ec2 modify-spot-fleet-request \
    --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --target-capacity 20
```

You can modify the previous command as follows to decrease the target capacity of the specified Spot Fleet without terminating any Spot Instances as a result\.

```
aws ec2 modify-spot-fleet-request \
    --spot-fleet-request-id sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --target-capacity 10 \
    --excess-capacity-termination-policy NoTermination
```

## Cancel a Spot Fleet request<a name="cancel-spot-fleet"></a>

When you are finished using your Spot Fleet, you can cancel the Spot Fleet request\. This cancels all Spot requests associated with the Spot Fleet, so that no new Spot Instances are launched for your Spot Fleet\. You must specify whether the Spot Fleet should terminate its Spot Instances\. If you terminate the instances, the Spot Fleet request enters the `cancelled_terminating` state\. Otherwise, the Spot Fleet request enters the `cancelled_running` state and the instances continue to run until they are interrupted or you terminate them manually\.

**To cancel a Spot Fleet request \(console\)**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot/home/fleet](https://console.aws.amazon.com/ec2spot/home/fleet)\.

1. Select your Spot Fleet request\.

1. Choose **Actions**, **Cancel spot request**\.

1. In **Cancel spot request**, verify that you want to cancel the Spot Fleet\. To keep the fleet at its current size, clear **Terminate instances**\. When you are ready, choose **Confirm**\.

**To cancel a Spot Fleet request using the AWS CLI**  
Use the [cancel\-spot\-fleet\-requests](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-spot-fleet-requests.html) command to cancel the specified Spot Fleet request and terminate the instances\.

```
aws ec2 cancel-spot-fleet-requests \
    --spot-fleet-request-ids sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --terminate-instances
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

You can modify the previous command as follows to cancel the specified Spot Fleet request without terminating the instances\.

```
aws ec2 cancel-spot-fleet-requests \
    --spot-fleet-request-ids sfr-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --no-terminate-instances
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
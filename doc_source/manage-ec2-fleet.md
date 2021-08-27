# Work with EC2 Fleets<a name="manage-ec2-fleet"></a>

To start using an EC2 Fleet, you create a request that includes the total target capacity, On\-Demand capacity, Spot capacity, one or more launch specifications for the instances, and the maximum price that you are willing to pay\. The fleet request must include a launch template that defines the information that the fleet needs to launch an instance, such as an AMI, instance type, subnet or Availability Zone, and one or more security groups\. You can specify launch specification overrides for the instance type, subnet, Availability Zone, and maximum price you're willing to pay, and you can assign weighted capacity to each launch specification override\.

If your fleet includes Spot Instances, Amazon EC2 can attempt to maintain your fleet target capacity as Spot prices change\.

An EC2 Fleet request of type `maintain` or `request` remains active until it expires or you delete it\. When you delete a fleet of type `maintain` or `request`, you can specify whether deletion terminates the instances in that fleet\.

**Topics**
+ [EC2 Fleet request states](#EC2-fleet-states)
+ [EC2 Fleet prerequisites](#ec2-fleet-prerequisites)
+ [EC2 Fleet health checks](#ec2-fleet-health-checks)
+ [Generate an EC2 Fleet JSON configuration file](#ec2-fleet-cli-skeleton)
+ [Create an EC2 Fleet](#create-ec2-fleet)
+ [Tag an EC2 Fleet](#tag-ec2-fleet)
+ [Monitor your EC2 Fleet](#monitor-ec2-fleet)
+ [Modify an EC2 Fleet](#modify-ec2-fleet)
+ [Delete an EC2 Fleet](#delete-fleet)

## EC2 Fleet request states<a name="EC2-fleet-states"></a>

An EC2 Fleet request can be in one of the following states:

`submitted`  
The EC2 Fleet request is being evaluated and Amazon EC2 is preparing to launch the target number of instances\. The request can include On\-Demand Instances, Spot Instances, or both\.

`active`  
The EC2 Fleet request has been validated and Amazon EC2 is attempting to maintain the target number of running instances\. The request remains in this state until it is modified or deleted\.

`modifying`  
The EC2 Fleet request is being modified\. The request remains in this state until the modification is fully processed or the request is deleted\. Only a `maintain` fleet type can be modified\. This state does not apply to other request types\.

`deleted_running`  
The EC2 Fleet request is deleted and does not launch additional instances\. Its existing instances continue to run until they are interrupted or terminated manually\. The request remains in this state until all instances are interrupted or terminated\. Only an EC2 Fleet of type `maintain` or `request` can have running instances after the EC2 Fleet request is deleted\. A deleted `instant` fleet with running instances is not supported\. This state does not apply to `instant` fleets\.

`deleted_terminating`  
The EC2 Fleet request is deleted and its instances are terminating\. The request remains in this state until all instances are terminated\.

`deleted`  
The EC2 Fleet is deleted and has no running instances\. The request is deleted two days after its instances are terminated\.

The following illustration represents the transitions between the EC2 Fleet request states\. If you exceed your fleet limits, the request is deleted immediately\.

![\[EC2 Fleet request states\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_fleet_states.png)

## EC2 Fleet prerequisites<a name="ec2-fleet-prerequisites"></a>

**Topics**
+ [Launch template](#ec2-fleet-prerequisites-launch-template)
+ [Service\-linked role for EC2 Fleet](#ec2-fleet-service-linked-role)
+ [Grant access to customer managed keys for use with encrypted AMIs and EBS snapshots](#ec2-fleet-service-linked-roles-access-to-cmks)
+ [Permissions for EC2 Fleet IAM users](#ec2-fleet-iam-users)

### Launch template<a name="ec2-fleet-prerequisites-launch-template"></a>

A launch template includes information about the instances to launch, such as the instance type, Availability Zone, and the maximum price that you are willing to pay\. For more information, see [Launch an instance from a launch template](ec2-launch-templates.md)\.

### Service\-linked role for EC2 Fleet<a name="ec2-fleet-service-linked-role"></a>

The `AWSServiceRoleForEC2Fleet` role grants the EC2 Fleet permission to request, launch, terminate, and tag instances on your behalf\. Amazon EC2 uses this service\-linked role to complete the following actions:
+ `ec2:RunInstances` – Launch instances\.
+ `ec2:RequestSpotInstances` – Request Spot Instances\.
+ `ec2:TerminateInstances` – Terminate instances\.
+ `ec2:DescribeImages` – Describe Amazon Machine Images \(AMIs\) for the Spot Instances\.
+ `ec2:DescribeInstanceStatus` – Describe the status of the Spot Instances\.
+ `ec2:DescribeSubnets` – Describe the subnets for Spot Instances\.
+ `ec2:CreateTags` – Add tags to the EC2 Fleet, instances, and volumes\.

Ensure that this role exists before you use the AWS CLI or an API to create an EC2 Fleet\.

**Note**  
An `instant` EC2 Fleet does not require this role\.

To create the role, use the IAM console as follows\.

**To create the AWSServiceRoleForEC2Fleet role for EC2 Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Roles**, and then choose **Create role**\.

1. For **Select type of trusted entity**, choose **AWS service**\.

1. For **Choose the service that will use this role**, choose **EC2 \- Fleet**, and then choose **Next: Permissions**, **Next: Tags**, and **Next: Review**\.

1. On the **Review** page, choose **Create role**\.

If you no longer need to use EC2 Fleet, we recommend that you delete the **AWSServiceRoleForEC2Fleet** role\. After this role is deleted from your account, you can create the role again if you create another fleet\.

For more information, see [Using service\-linked roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/using-service-linked-roles.html) in the *IAM User Guide*\.

### Grant access to customer managed keys for use with encrypted AMIs and EBS snapshots<a name="ec2-fleet-service-linked-roles-access-to-cmks"></a>

If you specify an [encrypted AMI](AMIEncryption.md) or an [encrypted Amazon EBS snapshot](EBSEncryption.md) in your EC2 Fleet and you use an AWS KMS key for encryption, you must grant the **AWSServiceRoleForEC2Fleet** role permission to use the customer managed key so that Amazon EC2 can launch instances on your behalf\. To do this, you must add a grant to the customer managed key, as shown in the following procedure\.

When providing permissions, grants are an alternative to key policies\. For more information, see [Using grants](https://docs.aws.amazon.com/kms/latest/developerguide/grants.html) and [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.

**To grant the AWSServiceRoleForEC2Fleet role permissions to use the customer managed key**
+ Use the [create\-grant](https://docs.aws.amazon.com/cli/latest/reference/kms/create-grant.html) command to add a grant to the customer managed key and to specify the principal \(the **AWSServiceRoleForEC2Fleet** service\-linked role\) that is given permission to perform the operations that the grant permits\. The customer managed key is specified by the `key-id` parameter and the ARN of the customer managed key\. The principal is specified by the `grantee-principal` parameter and the ARN of the **AWSServiceRoleForEC2Fleet** service\-linked role\.

  ```
  aws kms create-grant \
      --region us-east-1 \
      --key-id arn:aws:kms:us-east-1:444455556666:key/1234abcd-12ab-34cd-56ef-1234567890ab \
      --grantee-principal arn:aws:iam::111122223333:role/AWSServiceRoleForEC2Fleet \
      --operations "Decrypt" "Encrypt" "GenerateDataKey" "GenerateDataKeyWithoutPlaintext" "CreateGrant" "DescribeKey" "ReEncryptFrom" "ReEncryptTo"
  ```

### Permissions for EC2 Fleet IAM users<a name="ec2-fleet-iam-users"></a>

If your IAM users will create or manage an EC2 Fleet, be sure to grant them the required permissions as follows\.

**To grant an IAM user permissions for EC2 Fleet**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Policies**\.

1. Choose **Create policy**\.

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

   An IAM user must have permission to call the `iam:ListRoles` action to enumerate existing IAM roles, the `iam:PassRole` action to specify the EC2 Fleet role, and the `iam:ListInstanceProfiles` action to enumerate existing instance profiles\.

   \(Optional\) To enable an IAM user to create roles or instance profiles using the IAM console, you must also add the following actions to the policy:
   + `iam:AddRoleToInstanceProfile`
   + `iam:AttachRolePolicy`
   + `iam:CreateInstanceProfile`
   + `iam:CreateRole`
   + `iam:GetRole`
   + `iam:ListPolicies`

1. On the **Review policy** page, enter a policy name and description, and choose **Create policy**\.

1. In the navigation pane, choose **Users** and select the user\.

1. On the **Permissions** tab, choose **Add permissions**\.

1. Choose **Attach existing policies directly**\. Select the policy that you created earlier and choose **Next: Review**\.

1. Choose **Add permissions**\.

## EC2 Fleet health checks<a name="ec2-fleet-health-checks"></a>

EC2 Fleet checks the health status of the instances in the fleet every two minutes\. The health status of an instance is either `healthy` or `unhealthy`\.

EC2 Fleet determines the health status of an instance by using the status checks provided by Amazon EC2\. An instance is determined as `unhealthy` when the status of either the instance status check or the system status check is `impaired` for three consecutive health status checks\. For more information, see [Status checks for your instances](monitoring-system-instance-status-check.md)\.

You can configure your fleet to replace unhealthy Spot Instances\. After setting `ReplaceUnhealthyInstances` to `true`, a Spot Instance is replaced when it is reported as `unhealthy`\. The fleet can go below its target capacity for up to a few minutes while an unhealthy Spot Instance is being replaced\.

**Requirements**
+ Health check replacement is supported only for EC2 Fleets that maintain a target capacity \(fleets of type `maintain`\), and not for fleets of type `request` or `instant`\.
+ Health check replacement is supported only for Spot Instances\. This feature is not supported for On\-Demand Instances\.
+ You can configure your EC2 Fleet to replace unhealthy instances only when you create it\.
+ IAM users can use health check replacement only if they have permission to call the `ec2:DescribeInstanceStatus` action\.

**To configure an EC2 Fleet to replace unhealthy Spot Instances**

1. Follow the steps for creating an EC2 Fleet\. For more information, see [Create an EC2 Fleet](#create-ec2-fleet)\.

1. To configure the fleet to replace unhealthy Spot Instances, in the JSON file, for `ReplaceUnhealthyInstances`, enter `true`\. 

## Generate an EC2 Fleet JSON configuration file<a name="ec2-fleet-cli-skeleton"></a>

To create an EC2 Fleet, you need only specify the launch template, total target capacity, and whether the default purchasing option is On\-Demand or Spot\. If you do not specify a parameter, the fleet uses the default value\. To view the full list of fleet configuration parameters, you can generate a JSON file as follows\.

**To generate a JSON file with all possible EC2 Fleet parameters using the command line**
+ Use the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) \(AWS CLI\) command and the `--generate-cli-skeleton` parameter to generate an EC2 Fleet JSON file:

  ```
  aws ec2 create-fleet \
      --generate-cli-skeleton
  ```

  The following EC2 Fleet parameters are available:

  ```
  {
      "DryRun": true, 
      "ClientToken": "", 
      "SpotOptions": {
          "AllocationStrategy": "lowest-price", 
          "InstanceInterruptionBehavior": "hibernate", 
          "InstancePoolsToUseCount": 0,
          "SingleInstanceType": true, 
          "SingleAvailabilityZone": true, 
          "MaxTotalPrice": 0,
          "MinTargetCapacity": 0
      }, 
      "OnDemandOptions": {
          "AllocationStrategy": "prioritized",
          "SingleInstanceType": true, 
          "SingleAvailabilityZone": true, 
          "MaxTotalPrice": 0,
          "MinTargetCapacity": 0
      }, 
      "ExcessCapacityTerminationPolicy": "termination", 
      "LaunchTemplateConfigs": [
          {
              "LaunchTemplateSpecification": {
                  "LaunchTemplateId": "", 
                  "LaunchTemplateName": "", 
                  "Version": ""
              }, 
              "Overrides": [
                  {
                      "InstanceType": "t2.micro", 
                      "MaxPrice": "", 
                      "SubnetId": "", 
                      "AvailabilityZone": "", 
                      "WeightedCapacity": null, 
                      "Priority": null,
                      "Placement": {
                          "AvailabilityZone": "", 
                          "Affinity": "", 
                          "GroupName": "", 
                          "PartitionNumber": 0, 
                          "HostId": "", 
                          "Tenancy": "dedicated", 
                          "SpreadDomain": ""
                  }
              ]
          }
      ], 
      "TargetCapacitySpecification": {
          "TotalTargetCapacity": 0, 
          "OnDemandTargetCapacity": 0, 
          "SpotTargetCapacity": 0, 
          "DefaultTargetCapacityType": "spot"
      }, 
      "TerminateInstancesWithExpiration": true, 
      "Type": "maintain", 
      "ValidFrom": "1970-01-01T00:00:00", 
      "ValidUntil": "1970-01-01T00:00:00", 
      "ReplaceUnhealthyInstances": true, 
      "TagSpecifications": [
          {
              "ResourceType": "fleet", 
              "Tags": [
                  {
                      "Key": "", 
                      "Value": ""
                  }
              ]
          }
      ]
  }
  ```

### EC2 Fleet JSON configuration file reference<a name="ec2-fleet-json-reference"></a>

**Note**  
Use lowercase for all parameter values; otherwise, you get an error when Amazon EC2 uses the JSON file to launch the EC2 Fleet\.

**AllocationStrategy \(for SpotOptions\)**  
\(Optional\) Indicates how to allocate the Spot Instance target capacity across the Spot capacity pools specified by the EC2 Fleet\. Valid values are `lowest-price`, `diversified`, `capacity-optimized`, `capacity-optimized-prioritized`\. The default is `lowest-price`\. Specify the allocation strategy that meets your needs\. For more information, see [Allocation strategies for Spot Instances](ec2-fleet-configuration-strategies.md#ec2-fleet-allocation-strategy)\.

**InstanceInterruptionBehavior**  
\(Optional\) The behavior when a Spot Instance is interrupted\. Valid values are `hibernate`, `stop`, and `terminate`\. By default, the Spot service terminates Spot Instances when they are interrupted\. If the fleet type is `maintain`, you can specify that the Spot service hibernates or stops Spot Instances when they are interrupted\.

**InstancePoolsToUseCount**  
The number of Spot capacity pools across which to allocate your target Spot capacity\. Valid only when Spot **AllocationStrategy** is set to `lowest-price`\. EC2 Fleet selects the cheapest Spot capacity pools and evenly allocates your target Spot capacity across the number of Spot capacity pools that you specify\.

**SingleInstanceType**  
Indicates that the fleet uses a single instance type to launch all Spot Instances in the fleet\.

**SingleAvailabilityZone**  
Indicates that the fleet launches all Spot Instances into a single Availability Zone\.

**MaxTotalPrice**  
The maximum amount per hour for Spot Instances that you're willing to pay\.

**MinTargetCapacity**  
The minimum target capacity for Spot Instances in the fleet\. If the minimum target capacity is not reached, the fleet launches no instances\.

**AllocationStrategy \(for OnDemandOptions\)**  
The order of the launch template overrides to use in fulfilling On\-Demand capacity\. If you specify `lowest-price`, EC2 Fleet uses price to determine the order, launching the lowest price first\. If you specify prioritized, EC2 Fleet uses the priority that you assigned to each launch template override, launching the highest priority first\. If you do not specify a value, EC2 Fleet defaults to `lowest-price`\.

**SingleInstanceType**  
Indicates that the fleet uses a single instance type to launch all On\-Demand Instances in the fleet\.

**SingleAvailabilityZone**  
Indicates that the fleet launches all On\-Demand Instances into a single Availability Zone\.

**MaxTotalPrice**  
The maximum amount per hour for On\-Demand Instances that you're willing to pay\.

**MinTargetCapacity**  
The minimum target capacity for On\-Demand Instances in the fleet\. If the minimum target capacity is not reached, the fleet launches no instances\.

**ExcessCapacityTerminationPolicy**  
\(Optional\) Indicates whether running instances should be terminated if the total target capacity of the EC2 Fleet is decreased below the current size of the EC2 Fleet\. Valid values are `no-termination` and `termination`\.

**LaunchTemplateId**  
The ID of the launch template to use\. You must specify either the launch template ID or launch template name\. The launch template must specify an Amazon Machine Image \(AMI\)\. For information about creating launch templates, see [Launch an instance from a launch template](ec2-launch-templates.md)\.

**LaunchTemplateName**  
The name of the launch template to use\. You must specify either the launch template ID or launch template name\. The launch template must specify an Amazon Machine Image \(AMI\)\. For more information, see [Launch an instance from a launch template](ec2-launch-templates.md)\.

**Version**  
The launch template version number, `$Latest`, or `$Default`\. You must specify a value, otherwise the request fails\. If the value is `$Latest`, Amazon EC2 uses the latest version of the launch template\. If the value is `$Default`, Amazon EC2 uses the default version of the launch template\. For more information, see [Modify a launch template \(manage launch template versions\)](ec2-launch-templates.md#manage-launch-template-versions)\.

**InstanceType**  
\(Optional\) The instance type\. If entered, this value overrides the launch template\. The instance types must have the minimum hardware specifications that you need \(vCPUs, memory, or storage\)\.

**MaxPrice**  
\(Optional\) The maximum price per unit hour that you are willing to pay for a Spot Instance\. If entered, this value overrides the launch template\. You can use the default maximum price \(the On\-Demand price\) or specify the maximum price that you are willing to pay\. Your Spot Instances are not launched if your maximum price is lower than the Spot price for the instance types that you specified\.

**SubnetId**  
\(Optional\) The ID of the subnet in which to launch the instances\. If entered, this value overrides the launch template\.  
To create a new VPC, go the Amazon VPC console\. When you are done, return to the JSON file and enter the new subnet ID\.

**AvailabilityZone**  
\(Optional\) The Availability Zone in which to launch the instances\. The default is to let AWS choose the zones for your instances\. If you prefer, you can specify specific zones\. If entered, this value overrides the launch template\.   
Specify one or more Availability Zones\. If you have more than one subnet in a zone, specify the appropriate subnet\. To add subnets, go to the Amazon VPC console\. When you are done, return to the JSON file and enter the new subnet ID\.

**WeightedCapacity**  
\(Optional\) The number of units provided by the specified instance type\. If entered, this value overrides the launch template\.

**Priority**  
The priority for the launch template override\. The highest priority is launched first\.  
If the On\-Demand **AllocationStrategy** is set to `prioritized`, EC2 Fleet uses priority to determine which launch template override to use first in fulfilling On\-Demand capacity\.  
If the Spot **AllocationStrategy** is set to `capacity-optimized-prioritized`, EC2 Fleet uses priority on a best\-effort basis to determine which launch template override to use first in fulfilling Spot capacity, but optimizes for capacity first\.  
Valid values are whole numbers starting at `0`\. The lower the number, the higher the priority\. If no number is set, the launch template override has the lowest priority\. You can set the same priority for different launch template overrides\.

**TotalTargetCapacity**  
The number of instances to launch\. You can choose instances or performance characteristics that are important to your application workload, such as vCPUs, memory, or storage\. If the request type is `maintain`, you can specify a target capacity of 0 and add capacity later\.

**OnDemandTargetCapacity**  
\(Optional\) The number of On\-Demand Instances to launch\. This number must be less than the `TotalTargetCapacity`\.

**SpotTargetCapacity**  
\(Optional\) The number of Spot Instances to launch\. This number must be less than the `TotalTargetCapacity`\.

**DefaultTargetCapacityType**  
If the value for `TotalTargetCapacity` is higher than the combined values for `OnDemandTargetCapacity` and `SpotTargetCapacity`, the difference is launched as the instance purchasing option specified here\. Valid values are `on-demand` or `spot`\.

**TerminateInstancesWithExpiration**  
\(Optional\) By default, Amazon EC2 terminates your instances when the EC2 Fleet request expires\. The default value is `true`\. To keep them running after your request expires, do not enter a value for this parameter\.

**Type**  
\(Optional\) The type of request\. Valid values are `instant`, `request`, and `maintain`\. The default value is `maintain`\.  
+ `instant` – The EC2 Fleet submits a synchronous one\-time request for your desired capacity, and returns errors for any instances that could not be launched\.
+ `request` – The EC2 Fleet submits an asynchronous one\-time request for your desired capacity, but does submit Spot requests in alternative Spot capacity pools if Spot capacity is unavailable, and does not maintain Spot capacity if Spot Instances are interrupted\.
+ `maintain` – The EC2 Fleet submits an asynchronous request for your desired capacity, and continues to maintain your desired Spot capacity by replenishing interrupted Spot Instances\.
For more information, see [EC2 Fleet request types](ec2-fleet-request-type.md)\.

**ValidFrom**  
\(Optional\) To create a request that is valid only during a specific time period, enter a start date\.

**ValidUntil**  
\(Optional\) To create a request that is valid only during a specific time period, enter an end date\.

**ReplaceUnhealthyInstances**  
\(Optional\) To replace unhealthy instances in an EC2 Fleet that is configured to `maintain` the fleet, enter `true`\. Otherwise, leave this parameter empty\.

**TagSpecifications**  
\(Optional\) The key\-value pair for tagging the EC2 Fleet request on creation\. The value for `ResourceType` must be `fleet`, otherwise the fleet request fails\. To tag instances at launch, specify the tags in the [launch template](ec2-launch-templates.md#create-launch-template)\. For information about tagging after launch, see [Tag your resources](Using_Tags.md#tag-resources)\.

## Create an EC2 Fleet<a name="create-ec2-fleet"></a>

When you create an EC2 Fleet, you must specify a launch template that includes information about the instances to launch, such as the instance type, Availability Zone, and the maximum price you are willing to pay\.

You can create an EC2 Fleet that includes multiple launch specifications that override the launch template\. The launch specifications can vary by instance type, Availability Zone, subnet, and maximum price, and can include a different weighted capacity\.

When you create an EC2 Fleet, use a JSON file to specify information about the instances to launch\. For more information, see [EC2 Fleet JSON configuration file reference](#ec2-fleet-json-reference)\.

EC2 Fleets can only be created using the AWS CLI\.

**To create an EC2 Fleet \(AWS CLI\)**
+ Use the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) \(AWS CLI\) command to create an EC2 Fleet\.

```
aws ec2 create-fleet \
    --cli-input-json file://file_name.json
```

For example configuration files, see [EC2 Fleet example configurations](ec2-fleet-examples.md)\.

The following is example output for a fleet of type `request` or `maintain`\.

```
{
    "FleetId": "fleet-12a34b55-67cd-8ef9-ba9b-9208dEXAMPLE"
}
```

The following is example output for a fleet of type `instant` that launched the target capacity\.

```
{
  "FleetId": "fleet-12a34b55-67cd-8ef9-ba9b-9208dEXAMPLE",
  "Errors": [],
  "Instances": [
    {
      "LaunchTemplateAndOverrides": {
        "LaunchTemplateSpecification": {
          "LaunchTemplateId": "lt-01234a567b8910abcEXAMPLE",
          "Version": "1"
        },
        "Overrides": {
          "InstanceType": "c5.large",
          "AvailabilityZone": "us-east-1a"
        }
      },
      "Lifecycle": "on-demand",
      "InstanceIds": [
        "i-1234567890abcdef0",
        "i-9876543210abcdef9" 
      ],
      "InstanceType": "c5.large",
      "Platform": null
    },
    {
      "LaunchTemplateAndOverrides": {
        "LaunchTemplateSpecification": {
          "LaunchTemplateId": "lt-01234a567b8910abcEXAMPLE",
          "Version": "1"
        },
        "Overrides": {
          "InstanceType": "c4.large",
          "AvailabilityZone": "us-east-1a"
        }
      },
      "Lifecycle": "on-demand",
      "InstanceIds": [
        "i-5678901234abcdef0",
        "i-5432109876abcdef9" 
      ],
      "InstanceType": "c4.large",
      "Platform": null
    },
  ]
}
```

The following is example output for a fleet of type `instant` that launched part of the target capacity with errors for instances that were not launched\.

```
{
  "FleetId": "fleet-12a34b55-67cd-8ef9-ba9b-9208dEXAMPLE",
  "Errors": [
    {
      "LaunchTemplateAndOverrides": {
        "LaunchTemplateSpecification": {
          "LaunchTemplateId": "lt-01234a567b8910abcEXAMPLE",
          "Version": "1"
        },
        "Overrides": {
          "InstanceType": "c4.xlarge",
          "AvailabilityZone": "us-east-1a",
        }
      },
      "Lifecycle": "on-demand",
      "ErrorCode": "InsufficientInstanceCapacity",
      "ErrorMessage": "",
      "InstanceType": "c4.xlarge",
      "Platform": null
    },
  ],
  "Instances": [
    {
      "LaunchTemplateAndOverrides": {
        "LaunchTemplateSpecification": {
          "LaunchTemplateId": "lt-01234a567b8910abcEXAMPLE",
          "Version": "1"
        },
        "Overrides": {
          "InstanceType": "c5.large",
          "AvailabilityZone": "us-east-1a"
        }
      },
      "Lifecycle": "on-demand",
      "InstanceIds": [
        "i-1234567890abcdef0",
        "i-9876543210abcdef9" 
      ],
      "InstanceType": "c5.large",
      "Platform": null
    },
  ]
}
```

The following is example output for a fleet of type `instant` that launched no instances\.

```
{
  "FleetId": "fleet-12a34b55-67cd-8ef9-ba9b-9208dEXAMPLE",
  "Errors": [
    {
      "LaunchTemplateAndOverrides": {
        "LaunchTemplateSpecification": {
          "LaunchTemplateId": "lt-01234a567b8910abcEXAMPLE",
          "Version": "1"
        },
        "Overrides": {
          "InstanceType": "c4.xlarge",
          "AvailabilityZone": "us-east-1a",
        }
      },
      "Lifecycle": "on-demand",
      "ErrorCode": "InsufficientCapacity",
      "ErrorMessage": "",
      "InstanceType": "c4.xlarge",
      "Platform": null
    },
    {
      "LaunchTemplateAndOverrides": {
        "LaunchTemplateSpecification": {
          "LaunchTemplateId": "lt-01234a567b8910abcEXAMPLE",
          "Version": "1"
        },
        "Overrides": {
          "InstanceType": "c5.large",
          "AvailabilityZone": "us-east-1a",
        }
      },
      "Lifecycle": "on-demand",
      "ErrorCode": "InsufficientCapacity",
      "ErrorMessage": "",
      "InstanceType": "c5.large",
      "Platform": null
    },
  ],
  "Instances": []
}
```

## Tag an EC2 Fleet<a name="tag-ec2-fleet"></a>

To help categorize and manage your EC2 Fleet requests, you can tag them with custom metadata\. You can assign a tag to an EC2 Fleet request when you create it, or afterward\.

When you tag a fleet request, the instances and volumes that are launched by the fleet are not automatically tagged\. You need to explicitly tag the instances and volumes launched by the fleet\. You can choose to assign tags to only the fleet request, or to only the instances launched by the fleet, or to only the volumes attached to the instances launched by the fleet, or to all three\.

**Note**  
For `instant` fleet types, you can tag volumes that are attached to On\-Demand Instances and Spot Instances\. For `request` or `maintain` fleet types, you can only tag volumes that are attached to On\-Demand Instances\.

For more information about how tags work, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

**Prerequisite**

Grant the IAM user the permission to tag resources\. For more information, see [Example: Tag resources](ExamplePolicies_EC2.md#iam-example-taggingresources)\.

**To grant an IAM user the permission to tag resources**  
Create a IAM policy that includes the following:
+ The `ec2:CreateTags` action\. This grants the IAM user permission to create tags\.
+ The `ec2:CreateFleet` action\. This grants the IAM user permission to create an EC2 Fleet request\.
+ For `Resource`, we recommend that you specify `"*"`\. This allows users to tag all resource types\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "TagEC2FleetRequest",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags",
                "ec2:CreateFleet"
            ],
            "Resource": "*"
}
```

**Important**  
We currently do not support resource\-level permissions for the `create-fleet` resource\. If you specify `create-fleet` as a resource, you will get an unauthorized exception when you try to tag the fleet\. The following example illustrates how *not* to set the policy\.   

```
{
    "Effect": "Allow",
    "Action": [
        "ec2:CreateTags",
        "ec2:CreateFleet"
    ],
    "Resource": "arn:aws:ec2:us-east-1:111122223333:create-fleet/*"
}
```

**To tag a new EC2 Fleet request**  
To tag an EC2 Fleet request when you create it, specify the key\-value pair in the [JSON file](#ec2-fleet-cli-skeleton) used to create the fleet\. The value for `ResourceType` must be `fleet`\. If you specify another value, the fleet request fails\.

**To tag instances and volumes launched by an EC2 Fleet**  
To tag instances and volumes when they are launched by the fleet, specify the tags in the [launch template](ec2-launch-templates.md#create-launch-template) that is referenced in the EC2 Fleet request\.

**Note**  
You can't tag volumes attached to Spot Instances that are launched by a `request` or `maintain` fleet type\.

**To tag an existing EC2 Fleet request, instance, and volume \(AWS CLI\)**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command to tag existing resources\.

```
aws ec2 create-tags \
    --resources fleet-12a34b55-67cd-8ef9-ba9b-9208dEXAMPLE i-1234567890abcdef0 vol-1234567890EXAMPLE \
    --tags Key=purpose,Value=test
```

## Monitor your EC2 Fleet<a name="monitor-ec2-fleet"></a>

The EC2 Fleet launches On\-Demand Instances when there is available capacity, and launches Spot Instances when your maximum price exceeds the Spot price and capacity is available\. The On\-Demand Instances run until you terminate them, and the Spot Instances run until they are interrupted or you terminate them\.

The returned list of running instances is refreshed periodically and might be out of date\.

**To monitor your EC2 Fleet \(AWS CLI\)**  
Use the [describe\-fleets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fleets.html) command to describe your EC2 Fleets\.

```
aws ec2 describe-fleets
```

The following is example output\.

```
{
    "Fleets": [
        {
            "Type": "maintain", 
            "FulfilledCapacity": 2.0, 
            "LaunchTemplateConfigs": [
                {
                    "LaunchTemplateSpecification": {
                        "Version": "2", 
                        "LaunchTemplateId": "lt-07b3bc7625cdab851"
                    }
                }
            ], 
            "TerminateInstancesWithExpiration": false, 
            "TargetCapacitySpecification": {
                "OnDemandTargetCapacity": 0, 
                "SpotTargetCapacity": 2, 
                "TotalTargetCapacity": 2, 
                "DefaultTargetCapacityType": "spot"
            }, 
            "FulfilledOnDemandCapacity": 0.0, 
            "ActivityStatus": "fulfilled", 
            "FleetId": "fleet-76e13e99-01ef-4bd6-ba9b-9208de883e7f", 
            "ReplaceUnhealthyInstances": false, 
            "SpotOptions": {
                "InstanceInterruptionBehavior": "terminate",
                "InstancePoolsToUseCount": 1, 
                "AllocationStrategy": "lowest-price"
            }, 
            "FleetState": "active", 
            "ExcessCapacityTerminationPolicy": "termination", 
            "CreateTime": "2018-04-10T16:46:03.000Z"
        }
    ]
}
```

Use the [describe\-fleet\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fleet-instances.html) command to describe the instances for the specified EC2 Fleet\.

```
aws ec2 describe-fleet-instances \
    --fleet-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE
```

```
{
    "ActiveInstances": [
        {
            "InstanceId": "i-09cd595998cb3765e", 
            "InstanceHealth": "healthy", 
            "InstanceType": "m4.large", 
            "SpotInstanceRequestId": "sir-86k84j6p"
        }, 
        {
            "InstanceId": "i-09cf95167ca219f17", 
            "InstanceHealth": "healthy", 
            "InstanceType": "m4.large", 
            "SpotInstanceRequestId": "sir-dvxi7fsm"
        }
    ], 
    "FleetId": "fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE"
}
```

Use the [describe\-fleet\-history](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-fleet-request-history.html) command to describe the history for the specified EC2 Fleet for the specified time\. 

```
aws ec2 describe-fleet-history --fleet-request-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE --start-time 2018-04-10T00:00:00Z
```

```
{
    "HistoryRecords": [], 
    "FleetId": "fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE", 
    "LastEvaluatedTime": "1970-01-01T00:00:00.000Z", 
    "StartTime": "2018-04-09T23:53:20.000Z"
}
```

## Modify an EC2 Fleet<a name="modify-ec2-fleet"></a>

You can modify an EC2 Fleet that is in the `submitted` or `active` state\. When you modify a fleet, it enters the `modifying` state\.

You can only modify an EC2 Fleet that is of type `maintain`\. You cannot modify an EC2 Fleet of type `request` or `instant`\.

You can modify the following parameters of an EC2 Fleet:
+ `target-capacity-specification` – Increase or decrease the target capacity for `TotalTargetCapacity`, `OnDemandTargetCapacity`, and `SpotTargetCapacity`\.
+ `excess-capacity-termination-policy` – Whether running instances should be terminated if the total target capacity of the EC2 Fleet is decreased below the current size of the fleet\. Valid values are `no-termination` and `termination`\.

When you increase the target capacity, the EC2 Fleet launches the additional instances according to the instance purchasing option specified for `DefaultTargetCapacityType`, which are either On\-Demand Instances or Spot Instances\.

If the `DefaultTargetCapacityType` is `spot`, the EC2 Fleet launches the additional Spot Instances according to its allocation strategy\. If the allocation strategy is `lowest-price`, the fleet launches the instances from the lowest\-priced Spot capacity pool in the request\. If the allocation strategy is `diversified`, the fleet distributes the instances across the pools in the request\.

When you decrease the target capacity, the EC2 Fleet deletes any open requests that exceed the new target capacity\. You can request that the fleet terminate instances until the size of the fleet reaches the new target capacity\. If the allocation strategy is `lowest-price`, the fleet terminates the instances with the highest price per unit\. If the allocation strategy is `diversified`, the fleet terminates instances across the pools\. Alternatively, you can request that EC2 Fleet keep the fleet at its current size, but not replace any Spot Instances that are interrupted or any instances that you terminate manually\.

When an EC2 Fleet terminates a Spot Instance because the target capacity was decreased, the instance receives a Spot Instance interruption notice\.

**To modify an EC2 Fleet \(AWS CLI\)**  
Use the [modify\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-fleet.html) command to update the target capacity of the specified EC2 Fleet\.

```
aws ec2 modify-fleet \
    --fleet-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --target-capacity-specification TotalTargetCapacity=20
```

If you are decreasing the target capacity but want to keep the fleet at its current size, you can modify the previous command as follows\.

```
aws ec2 modify-fleet \
    --fleet-id fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --target-capacity-specification TotalTargetCapacity=10 \
    --excess-capacity-termination-policy no-termination
```

## Delete an EC2 Fleet<a name="delete-fleet"></a>

If you no longer require an EC2 Fleet, you can delete it\. After you delete a fleet, it launches no new instances\.

When you delete an EC2 Fleet, you must specify if you want to also terminate its instances\. If you specify that the instances must be terminated when the fleet is deleted, it enters the `deleted_terminating` state\. Otherwise, it enters the `deleted_running` state, and the instances continue to run until they are interrupted or you terminate them manually\.

**Restrictions**
+ You can delete up to 25 `instant` fleets in a single request\. If you exceed this number, no `instant` fleets are deleted and an error is returned\. There is no restriction on the number of fleets of type `maintain` or `request` that can be deleted in a single request\.
+ Up to 1000 instances can be terminated in a single request to delete `instant` fleets\. 

**To delete an EC2 Fleet and terminate its instances \(AWS CLI\)**  
Use the [delete\-fleets](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-fleets.html) command and the `--terminate-instances` parameter to delete the specified EC2 Fleet and terminate the instances\.

```
aws ec2 delete-fleets \
    --fleet-ids fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --terminate-instances
```

The following is example output\.

```
{
    "UnsuccessfulFleetDeletions": [], 
    "SuccessfulFleetDeletions": [
        {
            "CurrentFleetState": "deleted_terminating", 
            "PreviousFleetState": "active", 
            "FleetId": "fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE"
        }
    ]
}
```

**To delete an EC2 Fleet without terminating the instances \(AWS CLI\)**  
You can modify the previous command using the `--no-terminate-instances` parameter to delete the specified EC2 Fleet without terminating the instances\.

**Note**  
`--no-terminate-instances` is not supported for `instant` fleets\.

```
aws ec2 delete-fleets \
    --fleet-ids fleet-73fbd2ce-aa30-494c-8788-1cee4EXAMPLE \
    --no-terminate-instances
```

The following is example output\.

```
{
    "UnsuccessfulFleetDeletions": [], 
    "SuccessfulFleetDeletions": [
        {
            "CurrentFleetState": "deleted_running", 
            "PreviousFleetState": "active", 
            "FleetId": "fleet-4b8aaae8-dfb5-436d-a4c6-3dafa4c6b7dcEXAMPLE"
        }
    ]
}
```

### Troubleshoot when a fleet fails to delete<a name="troubleshoot-delete-fleet"></a>

If an EC2 Fleet fails to delete, `UnsuccessfulFleetDeletions` in the output returns the ID of the EC2 Fleet, an error code, and an error message\.

The error codes are:
+ `ExceededInstantFleetNumForDeletion`
+ `fleetIdDoesNotExist`
+ `fleetIdMalformed`
+ `fleetNotInDeletableState`
+ `NoTerminateInstancesNotSupported`
+ `UnauthorizedOperation`
+ `unexpectedError`

**Troubleshooting `ExceededInstantFleetNumForDeletion`**  
If you try to delete more than 25 `instant` fleets in a single request, the `ExceededInstantFleetNumForDeletion` error is returned\. The following is example output for this error\.

```
{
    "UnsuccessfulFleetDeletions": [
     {
          "FleetId": " fleet-5d130460-0c26-bfd9-2c32-0100a098f625",
          "Error": {
                  "Message": "Can’t delete more than 25 instant fleets in a single request.",
                  "Code": "ExceededInstantFleetNumForDeletion"
           }
     },
     {
           "FleetId": "fleet-9a941b23-0286-5bf4-2430-03a029a07e31",
           "Error": {
                  "Message": "Can’t delete more than 25 instant fleets in a single request.",
                  "Code": "ExceededInstantFleetNumForDeletion"
            }
     }
     .
     .
     .
     ],
     "SuccessfulFleetDeletions": []
}
```

**Troubleshoot `NoTerminateInstancesNotSupported`**  
If you specify that the instances in an `instant` fleet must not be terminated when you delete the fleet, the `NoTerminateInstancesNotSupported` error is returned\. `--no-terminate-instances` is not supported for `instant` fleets\. The following is example output for this error\.

```
{
      "UnsuccessfulFleetDeletions": [
            {
                  "FleetId": "fleet-5d130460-0c26-bfd9-2c32-0100a098f625",
                  "Error": {
                          "Message": "NoTerminateInstances option is not supported for instant fleet",
                          "Code": "NoTerminateInstancesNotSupported"
                   }
            }
       ],
       "SuccessfulFleetDeletions": []
```

**Troubleshoot `UnauthorizedOperation`**  
If you do not have permission to terminate instances, you get the `UnauthorizedOperation` error when deleting a fleet that must terminate its instances\. The following is the error response\.

```
<Response><Errors><Error><Code>UnauthorizedOperation</Code><Message>You are not authorized to perform this 
operation. Encoded authorization failure message: VvuncIxj7Z_CPGNYXWqnuFV-YjByeAU66Q9752NtQ-I3-qnDLWs6JLFd
KnSMMiq5s6cGqjjPtEDpsnGHzzyHasFHOaRYJpaDVravoW25azn6KNkUQQlFwhJyujt2dtNCdduJfrqcFYAjlEiRMkfDHt7N63SKlweKUl
BHturzDK6A560Y2nDSUiMmAB1y9UNtqaZJ9SNe5sNxKMqZaqKtjRbk02RZu5V2vn9VMk6fm2aMVHbY9JhLvGypLcMUjtJ76H9ytg2zRlje
VPiU5v2s-UgZ7h0p2yth6ysUdhlONg6dBYu8_y_HtEI54invCj4CoK0qawqzMNe6rcmCQHvtCxtXsbkgyaEbcwmrm2m01-EMhekLFZeJLr
DtYOpYcEl4_nWFX1wtQDCnNNCmxnJZAoJvb3VMDYpDTsxjQv1PxODZuqWHs23YXWVywzgnLtHeRf2o4lUhGBw17mXsS07k7XAfdPMP_brO
PT9vrHtQiILor5VVTsjSPWg7edj__1rsnXhwPSu8gI48ZLRGrPQqFq0RmKO_QIE8N8s6NWzCK4yoX-9gDcheurOGpkprPIC9YPGMLK9tug
</Message></Error></Errors><RequestID>89b1215c-7814-40ae-a8db-41761f43f2b0</RequestID></Response>
```

To resolve the error, you must add the `ec2:TerminateInstances` action to the IAM policy, as shown in the following example\.

```
{
       "Version": "2012-10-17",
       "Statement": [
            {
                "Sid": "DeleteFleetsAndTerminateInstances",
                "Effect": "Allow",
                "Action": [
                       "ec2:DeleteFleets"
                       "ec2:TerminateInstances"
                ],
                "Resource": "*"
            }
       ]
    }
```
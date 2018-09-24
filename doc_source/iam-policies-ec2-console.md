# Example Policies for Working in the Amazon EC2 Console<a name="iam-policies-ec2-console"></a>

You can use IAM policies to grant users permissions to view and work with specific resources in the Amazon EC2 console\. You can use the example policies in the previous section; however, they are designed for requests that are made with the AWS CLI or an AWS SDK\. The console uses additional API actions for its features, so these policies may not work as expected\. For example, a user that has permission to use only the `DescribeVolumes` API action will encounter errors when trying to view volumes in the console\. This section demonstrates policies that enable users to work with specific parts of the console\.

**Topics**
+ [1: Read\-Only Access](#ex-read-only)
+ [2: Using the EC2 Launch Wizard](#ex-launch-wizard)
+ [3: Working with Volumes](#ex-volumes)
+ [4: Working with Security Groups](#ex-security-groups)
+ [5: Working with Elastic IP Addresses](#ex-eip)
+ [6: Working with Reserved Instances](#ex-reservedinstances)

**Note**  
To help you work out which API actions are required to perform tasks in the console, you can use a service such as AWS CloudTrail\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\. If your policy does not grant permission to create or modify a specific resource, the console displays an encoded message with diagnostic information\. You can decode the message using the [DecodeAuthorizationMessage](https://docs.aws.amazon.com/STS/latest/APIReference/API_DecodeAuthorizationMessage.html) API action for AWS STS, or the [decode\-authorization\-message](https://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html) command in the AWS CLI\.

For additional information about creating policies for the Amazon EC2 console, see the following AWS Security Blog post: [Granting Users Permission to Work in the Amazon EC2 Console](https://aws.amazon.com/blogs/security/granting-users-permission-to-work-in-the-amazon-ec2-console/)\.

## 1: Read\-Only Access<a name="ex-read-only"></a>

To allow users to view all resources in the Amazon EC2 console, you can use the same policy as the following example: [1: Read\-Only Access](ExamplePolicies_EC2.md#iam-example-read-only)\. Users cannot perform any actions on those resources or create new resources, unless another statement grants them permission to do so\.

**a\. View instances, AMIs, and snapshots**

Alternatively, you can provide read\-only access to a subset of resources\. To do this, replace the \* wildcard in the `ec2:Describe` API action with specific `ec2:Describe` actions for each resource\. The following policy allows users to view all instances, AMIs, and snapshots in the Amazon EC2 console\. The `ec2:DescribeTags` action allows users to view public AMIs\. The console requires the tagging information to display public AMIs; however, you can remove this action to allow users to view only private AMIs\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
         "ec2:DescribeInstances", "ec2:DescribeImages",
         "ec2:DescribeTags", "ec2:DescribeSnapshots"
      ],
      "Resource": "*"
   }
   ]
}
```

**Note**  
Currently, the Amazon EC2 `ec2:Describe*` API actions do not support resource\-level permissions, so you cannot control which individual resources users can view in the console\. Therefore, the \* wildcard is necessary in the `Resource` element of the above statement\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. 

**b\. View instances and CloudWatch metrics**

The following policy allows users to view instances in the Amazon EC2 console, as well as CloudWatch alarms and metrics in the **Monitoring** tab of the **Instances** page\. The Amazon EC2 console uses the CloudWatch API to display the alarms and metrics, so you must grant users permission to use the `cloudwatch:DescribeAlarms` and `cloudwatch:GetMetricStatistics` actions\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
         "ec2:DescribeInstances",
         "cloudwatch:DescribeAlarms",
         "cloudwatch:GetMetricStatistics"
      ],
      "Resource": "*"
   }
   ]
}
```

## 2: Using the EC2 Launch Wizard<a name="ex-launch-wizard"></a>

The Amazon EC2 launch wizard is a series of screens with options to configure and launch an instance\. Your policy must include permission to use the API actions that allow users to work with the wizard's options\. If your policy does not include permission to use those actions, some items in the wizard cannot load properly, and users cannot complete a launch\.

**a\. Basic launch wizard access**

To complete a launch successfully, users must be given permission to use the `ec2:RunInstances` API action, and at least the following API actions:
+ `ec2:DescribeImages`: To view and select an AMI\. 
+ `ec2:DescribeVpcs`: To view the available network options, which are EC2\-Classic and a list of VPCs\. This is required even if you are not launching into a VPC\.
+ `ec2:DescribeSubnets`: If launching into a VPC, to view all available subnets for the chosen VPC\. 
+ `ec2:DescribeSecurityGroups`: To view the security groups page in the wizard\. Users can select an existing security group\. 
+ `ec2:DescribeKeyPairs` or `ec2:CreateKeyPair`: To select an existing key pair, or create a new one\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
		"ec2:DescribeInstances", "ec2:DescribeImages",
        "ec2:DescribeKeyPairs","ec2:DescribeVpcs", "ec2:DescribeSubnets", 
        "ec2:DescribeSecurityGroups"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "*"
    }
   ]
}
```

You can add API actions to your policy to provide more options for users, for example:
+ `ec2:DescribeAvailabilityZones`: If launching into EC2\-Classic, to view and select a specific Availability Zone\.
+ `ec2:DescribeNetworkInterfaces`: If launching into a VPC, to view and select existing network interfaces for the selected subnet\.
+ `ec2:CreateSecurityGroup`: To create a new security group; for example, to create the wizard's suggested `launch-wizard-x` security group\. However, this action alone only creates the security group; it does not add or modify any rules\. To add inbound rules, users must be granted permission to use the `ec2:AuthorizeSecurityGroupIngress` API action\. To add outbound rules to VPC security groups, users must be granted permission to use the `ec2:AuthorizeSecurityGroupEgress` API action\. To modify or delete existing rules, users must be granted permission to use the relevant `ec2:RevokeSecurityGroup*` API action\.
+ `ec2:CreateTags`: To tag the resources that are created by `RunInstances`\. For more information, see [Resource\-Level Permissions for Tagging](ec2-supported-iam-actions-resources.md#supported-iam-actions-tagging)\. If users do not have permission to use this action and they attempt to apply tags on the tagging page of the launch wizard, the launch fails\.
**Important**  
Be careful about granting users permission to use the `ec2:CreateTags` action\. This limits your ability to use the `ec2:ResourceTag` condition key to restrict the use of other resources; users can change a resource's tag in order to bypass those restrictions\.

Currently, the Amazon EC2 `Describe*` API actions do not support resource\-level permissions, so you cannot restrict which individual resources users can view in the launch wizard\. However, you can apply resource\-level permissions on the `ec2:RunInstances` API action to restrict which resources users can use to launch an instance\. The launch fails if users select options that they are not authorized to use\. 

**b\. Restrict access to specific instance type, subnet, and region**

The following policy allows users to launch `m1.small` instances using AMIs owned by Amazon, and only into a specific subnet \(`subnet-1a2b3c4d`\)\. Users can only launch in the sa\-east\-1 region\. If users select a different region, or select a different instance type, AMI, or subnet in the launch wizard, the launch fails\. 

The first statement grants users permission to view the options in the launch wizard, as demonstrated in the example above\. The second statement grants users permission to use the network interface, volume, key pair, security group, and subnet resources for the `ec2:RunInstances` action, which are required to launch an instance into a VPC\. For more information about using the `ec2:RunInstances` action, see [6: Launching Instances \(RunInstances\)](ExamplePolicies_EC2.md#iam-example-runinstances)\. The third and fourth statements grant users permission to use the instance and AMI resources respectively, but only if the instance is an `m1.small` instance, and only if the AMI is owned by Amazon\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
         "ec2:DescribeInstances", "ec2:DescribeImages",
         "ec2:DescribeKeyPairs","ec2:DescribeVpcs", "ec2:DescribeSubnets", "ec2:DescribeSecurityGroups"
	  ],
	  "Resource": "*"
   },
   {
      "Effect": "Allow",
      "Action":"ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:sa-east-1:111122223333:network-interface/*",
         "arn:aws:ec2:sa-east-1:111122223333:volume/*",
         "arn:aws:ec2:sa-east-1:111122223333:key-pair/*",
         "arn:aws:ec2:sa-east-1:111122223333:security-group/*",
         "arn:aws:ec2:sa-east-1:111122223333:subnet/subnet-1a2b3c4d"
      ]
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:sa-east-1:111122223333:instance/*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:InstanceType": "m1.small"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [ 
            "arn:aws:ec2:sa-east-1::image/ami-*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:Owner": "amazon"
         }
      }
   }
   ]
}
```

## 3: Working with Volumes<a name="ex-volumes"></a>

The following policy grants users permission to view and create volumes, and attach and detach volumes to specific instances\. 

Users can attach any volume to instances that have the tag "`purpose=test`", and also detach volumes from those instances\. To attach a volume using the Amazon EC2 console, it is helpful for users to have permission to use the `ec2:DescribeInstances` action, as this allows them to select an instance from a pre\-populated list in the **Attach Volume** dialog box\. However, this also allows users to view all instances on the **Instances** page in the console, so you can omit this action\.

In the first statement, the `ec2:DescribeAvailabilityZones` action is necessary to ensure that a user can select an Availability Zone when creating a volume\.

Users cannot tag the volumes that they create \(either during or after volume creation\)\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeVolumes",
        "ec2:DescribeAvailabilityZones",
        "ec2:CreateVolume",
        "ec2:DescribeInstances"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:DetachVolume"
      ],
      "Resource": "arn:aws:ec2:region:111122223333:instance/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/purpose": "test"
        }
     }
   },
   {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:DetachVolume"
      ],
      "Resource": "arn:aws:ec2:region:111122223333:volume/*"
   }    
   ]
}
```

## 4: Working with Security Groups<a name="ex-security-groups"></a>

**a\. View security groups and add and remove rules**

The following policy grants users permission to view security groups in the Amazon EC2 console, and to add and remove inbound and outbound rules for existing security groups that have the tag `Department=Test`\.

**Note**  
You can't modify outbound rules for EC2\-Classic security groups\. For more information about security groups, see [Amazon EC2 Security Groups for Linux Instances](using-network-security.md)\.

In the first statement, the `ec2:DescribeTags` action allows users to view tags in the console, which makes it easier for users to identify the security groups that they are allowed to modify\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
         "ec2:DescribeSecurityGroups", "ec2:DescribeTags"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:AuthorizeSecurityGroupIngress", "ec2:RevokeSecurityGroupIngress",
         "ec2:AuthorizeSecurityGroupEgress", "ec2:RevokeSecurityGroupEgress"
      ],
      "Resource": [
         "arn:aws:ec2:region:111122223333:security-group/*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/Department": "Test"
         }
      }
   }
   ]
}
```

**b\. Working with the Create Security Group dialog box**

You can create a policy that allows users to work with the **Create Security Group** dialog box in the Amazon EC2 console\. To use this dialog box, users must be granted permission to use at the least the following API actions:
+ `ec2:CreateSecurityGroup`: To create a new security group\. 
+ `ec2:DescribeVpcs`: To view a list of existing VPCs in the **VPC** list\. This action is not required for creating security groups in EC2\-Classic\.

With these permissions, users can create a new security group successfully, but they cannot add any rules to it\. To work with rules in the **Create Security Group** dialog box, you can add the following API actions to your policy:
+ `ec2:AuthorizeSecurityGroupIngress`: To add inbound rules\.
+ `ec2:AuthorizeSecurityGroupEgress`: To add outbound rules to VPC security groups\.
+ `ec2:RevokeSecurityGroupIngress`: To modify or delete existing inbound rules\. This is useful to allow users to use the **Copy to new** feature in the console\. This feature opens the **Create Security Group** dialog box and populates it with the same rules as the security group that was selected\. 
+ `ec2:RevokeSecurityGroupEgress`: To modify or delete outbound rules for VPC security groups\. This is useful to allow users to modify or delete the default outbound rule that allows all outbound traffic\.
+ `ec2:DeleteSecurityGroup`: To cater for when invalid rules cannot be saved\. The console first creates the security group, and then adds the specified rules\. If the rules are invalid, the action fails, and the console attempts to delete the security group\. The user remains in the **Create Security Group** dialog box so that they can correct the invalid rule and try to create the security group again\. This API action is not required, but if a user is not granted permission to use it and attempts to create a security group with invalid rules, the security group is created without any rules, and the user must add them afterward\.

Currently, the `ec2:CreateSecurityGroup` API action does not support resource\-level permissions; however, you can apply resource\-level permissions to the `ec2:AuthorizeSecurityGroupIngress` and `ec2:AuthorizeSecurityGroupEgress` actions to control how users can create rules\.

The following policy grants users permission to use the **Create Security Group** dialog box, and to create inbound and outbound rules for security groups that are associated with a specific VPC \(`vpc-1a2b3c4d`\)\. Users can create security groups for EC2\-Classic or another VPC, but they cannot add any rules to them\. Similarly, users cannot add any rules to any existing security group that's not associated with VPC `vpc-1a2b3c4d`\. Users are also granted permission to view all security groups in the console\. This makes it easier for users to identify the security groups to which they can add inbound rules\. This policy also grants users permission to delete security groups that are associated with VPC `vpc-1a2b3c4d`\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeSecurityGroups", "ec2:CreateSecurityGroup", "ec2:DescribeVpcs"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DeleteSecurityGroup", "ec2:AuthorizeSecurityGroupIngress", "ec2:AuthorizeSecurityGroupEgress"
      ],
      "Resource": "arn:aws:ec2:region:111122223333:security-group/*",
      "Condition":{
         "ArnEquals": {
            "ec2:Vpc": "arn:aws:ec2:region:111122223333:vpc/vpc-1a2b3c4d"
         }
      }
    }
   ]
}
```

## 5: Working with Elastic IP Addresses<a name="ex-eip"></a>

To allow users to view Elastic IP addresses in the Amazon EC2 console, you must grant users permission to use the `ec2:DescribeAddresses` action\.

To allow users to work with Elastic IP addresses, you can add the following actions to your policy\.
+ `ec2:AllocateAddress`: To allocate an address for use in VPC or EC2\-Classic\.
+ `ec2:ReleaseAddress`: To release an Elastic IP address\.
+ `ec2:AssociateAddress`: To associate an Elastic IP address with an instance or a network interface\.
+ `ec2:DescribeNetworkInterfaces` and `ec2:DescribeInstances`: To work with the **Associate address** screen\. The screen displays the available instances or network interfaces to which you can associate an Elastic IP address\. For an EC2\-Classic instance, users only need permission to use `ec2:DescribeInstances`\.
+ `ec2:DisassociateAddress`: To disassociate an Elastic IP address from an instance or a network interface\.

The following policy allows users to view, allocate, and associate Elastic IP addresses with instances\. Users cannot associate Elastic IP addresses with network interfaces, disassociate Elastic IP addresses, or release them\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeAddresses",
                "ec2:AllocateAddress",
                "ec2:DescribeInstances",
                "ec2:AssociateAddress"
            ],
            "Resource": "*"
        }
    ]
}
```

## 6: Working with Reserved Instances<a name="ex-reservedinstances"></a>

The following policy can be attached to an IAM user\. It gives the user access to view and modify Reserved Instances in your account, as well as purchase new Reserved Instances in the AWS Management Console\.

This policy allows users to view all the Reserved Instances, as well as On\-Demand Instances, in the account\. It's not possible to set resource\-level permissions for individual Reserved Instances\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
         "ec2:DescribeReservedInstances", "ec2:ModifyReservedInstances",
         "ec2:PurchaseReservedInstancesOffering", "ec2:DescribeInstances",
         "ec2:DescribeAvailabilityZones", "ec2:DescribeReservedInstancesOfferings"
      ],
      "Resource": "*"
   }
   ]
}
```

 The `ec2:DescribeAvailabilityZones` action is necessary to ensure that the Amazon EC2 console can display information about the Availability Zones in which you can purchase Reserved Instances\. The `ec2:DescribeInstances` action is not required, but ensures that the user can view the instances in the account and purchase reservations to match the correct specifications\.

You can adjust the API actions to limit user access, for example removing `ec2:DescribeInstances` and `ec2:DescribeAvailabilityZones` means the user has read\-only access\.
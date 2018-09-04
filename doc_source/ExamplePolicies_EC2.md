# Example Policies for Working with the AWS CLI or an AWS SDK<a name="ExamplePolicies_EC2"></a>

The following examples show policy statements that you could use to control the permissions that IAM users have to Amazon EC2\. These policies are designed for requests that are made with the AWS CLI or an AWS SDK\. For example policies for working in the Amazon EC2 console, see [Example Policies for Working in the Amazon EC2 Console](iam-policies-ec2-console.md)\. For examples of IAM policies specific to Amazon VPC, see [Controlling Access to Amazon VPC Resources](http://docs.aws.amazon.com/vpc/latest/userguide/VPC_IAM.html)\.

**Topics**
+ [1: Read\-Only Access](#iam-example-read-only)
+ [2: Restricting Access to a Specific Region](#iam-example-region)
+ [3: Working with Instances](#iam-example-instances)
+ [4\. Working with Volumes](#iam-example-manage-volumes)
+ [5\. Working with Snapshots](#iam-example-manage-snapshots)
+ [6: Launching Instances \(RunInstances\)](#iam-example-runinstances)
+ [7\. Working with ClassicLink](#iam-example-classiclink)
+ [8\. Working with Reserved Instances](#iam-example-reservedinstances)
+ [9\. Tagging Resources](#iam-example-taggingresources)
+ [10: Working with IAM Roles](#iam-example-iam-roles)
+ [11: Working with Route Tables](#iam-example-route-tables)
+ [12: Allowing a Specific Instance to View Resources in Other AWS Services](#iam-example-source-instance)
+ [13\. Working with Launch Templates](#iam-example-launch-templates)

## 1: Read\-Only Access<a name="iam-example-read-only"></a>

The following policy grants users permissions to use all Amazon EC2 API actions whose names begin with `Describe`\. The `Resource` element uses a wildcard to indicate that users can specify all resources with these API actions\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. 

Users don't have permission to perform any actions on the resources \(unless another statement grants them permission to do so\) because they're denied permission to use API actions by default\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    }
   ]
}
```

## 2: Restricting Access to a Specific Region<a name="iam-example-region"></a>

The following policy grants users permissions to use all Amazon EC2 API actions in the EU \(Frankfurt\) region only\. Users cannot view, create, modify, or delete resources in any other region\.

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect": "Allow",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {"StringEquals": {"aws:RequestedRegion": [
	    "eu-central-1"
	  ]}
       }
    }  
  ]
}
```

## 3: Working with Instances<a name="iam-example-instances"></a>

**Topics**
+ [Describe, Launch, Stop, Start, and Terminate All Instances](#iam-example-instances-all)
+ [Describe All Instances, and Stop, Start, and Terminate Only Particular Instances](#iam-example-instances-specific)

### Describe, Launch, Stop, Start, and Terminate All Instances<a name="iam-example-instances-all"></a>

The following policy grants users permissions to use the API actions specified in the `Action` element\. The `Resource` element uses a \* wildcard to indicate that users can specify all resources with these API actions\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. 

The users don't have permission to use any other API actions \(unless another statement grants them permission to do so\) because users are denied permission to use API actions by default\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances", "ec2:DescribeImages",
        "ec2:DescribeKeyPairs", "ec2:DescribeSecurityGroups",
        "ec2:DescribeAvailabilityZones",
        "ec2:RunInstances", "ec2:TerminateInstances",
        "ec2:StopInstances", "ec2:StartInstances"
      ],
      "Resource": "*"
    }
   ]
}
```

### Describe All Instances, and Stop, Start, and Terminate Only Particular Instances<a name="iam-example-instances-specific"></a>

The following policy allows users to describe all instances, to start and stop only instances i\-1234567890abcdef0 and i\-0598c7d356eba48d7, and to terminate only instances in the US East \(N\. Virginia\) Region \(`us-east-1`\) with the resource tag "`purpose=test`"\. 

The first statement uses a \* wildcard for the `Resource` element to indicate that users can specify all resources with the action; in this case, they can list all instances\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions \(in this case, `ec2:DescribeInstances`\)\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Supported Resource\-Level Permissions for Amazon EC2 API Actions](ec2-supported-iam-actions-resources.md)\. 

The second statement uses resource\-level permissions for the `StopInstances` and `StartInstances` actions\. The specific instances are indicated by their ARNs in the `Resource` element\.

The third statement allows users to terminate all instances in the US East \(N\. Virginia\) Region \(`us-east-1`\) that belong to the specified AWS account, but only where the instance has the tag `"purpose=test"`\. The `Condition` element qualifies when the policy statement is in effect\. 

```
{
   "Version": "2012-10-17",
   "Statement": [
   {
   "Effect": "Allow",
      "Action": "ec2:DescribeInstances",
      "Resource": "*"
   },
   {
      "Effect": "Allow",
      "Action": [
        "ec2:StopInstances", 
        "ec2:StartInstances"
      ],
      "Resource": [
      "arn:aws:ec2:us-east-1:123456789012:instance/i-1234567890abcdef0",
      "arn:aws:ec2:us-east-1:123456789012:instance/i-0598c7d356eba48d7"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "ec2:TerminateInstances",
      "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*",
      "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/purpose": "test"
         }
      }
   }

   ]
}
```

## 4\. Working with Volumes<a name="iam-example-manage-volumes"></a>

**Topics**
+ [Attaching and Detaching Volumes](#iam-example-manage-volumes-attach-detach)
+ [Creating a Volume](#iam-example-manage-volumes-create)
+ [Creating a Volume with Tags](#iam-example-manage-volumes-tags)

### Attaching and Detaching Volumes<a name="iam-example-manage-volumes-attach-detach"></a>

When an API action requires a caller to specify multiple resources, you must create a policy statement that allows users to access all required resources\. If you need to use a `Condition` element with one or more of these resources, you must create multiple statements as shown in this example\.

The following policy allows users to attach volumes with the tag "`volume_user`=*iam\-user\-name*" to instances with the tag "`department=dev`", and to detach those volumes from those instances\. If you attach this policy to an IAM group, the `aws:username` policy variable gives each IAM user in the group permission to attach or detach volumes from the instances with a tag named `volume_user` that has his or her IAM user name as a value\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:DetachVolume"
      ],
      "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/department": "dev"
        }
      }
   },
   {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:DetachVolume"
      ],
      "Resource": "arn:aws:ec2:us-east-1:123456789012:volume/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/volume_user": "${aws:username}"
        }
      }
   }
  ]
}
```

### Creating a Volume<a name="iam-example-manage-volumes-create"></a>

The following policy allows users to use the [CreateVolume](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateVolume.html) API action\. The user is allowed to create a volume only if the volume is encrypted and only if the volume size is less than 20 GiB\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateVolume"
      ],
      "Resource": "arn:aws:ec2:us-east-1:123456789012:volume/*",
      "Condition":{
         "NumericLessThan": {
             "ec2:VolumeSize" : "20"
          },
          "Bool":{
              "ec2:Encrypted" : "true"
          }
       }
    }
  ]
}
```

### Creating a Volume with Tags<a name="iam-example-manage-volumes-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires users to tag any volumes they create with the tags `costcenter=115` and `stack=prod`\. The `aws:TagKeys` condition key uses the `ForAllValues` modifier to indicate that only the keys `costcenter` and `stack` are allowed in the request \(no other tags can be specified\)\. If users don't pass these specific tags, or if they don't specify tags at all, the request fails\. 

For resource\-creating actions that apply tags, users must also have permissions to use the `CreateTags` action\. The second statement uses the `ec2:CreateAction` condition key to allow users to create tags only in the context of `CreateVolume`\. Users cannot tag existing volumes or any other resources\. For more information, see [Resource\-Level Permissions for Tagging](ec2-supported-iam-actions-resources.md#supported-iam-actions-tagging)\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCreateTaggedVolumes",
      "Effect": "Allow",
      "Action": "ec2:CreateVolume",
      "Resource": "arn:aws:ec2:us-east-1:123456789012:volume/*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/costcenter": "115",
          "aws:RequestTag/stack": "prod"
         },
         "ForAllValues:StringEquals": {
             "aws:TagKeys": ["costcenter","stack"]
         }
       }
     },
     {
       "Effect": "Allow",
       "Action": [
         "ec2:CreateTags"
       ],
       "Resource": "arn:aws:ec2:us-east-1:123456789012:volume/*",
       "Condition": {
         "StringEquals": {
             "ec2:CreateAction" : "CreateVolume"
        }
      }
    }
  ]
}
```

The following policy allows users to create a volume without having to specify tags\. The `CreateTags` action is only evaluated if tags are specified in the `CreateVolume` request\. If users do specify tags, the tag must be `purpose=test`\. No other tags are allowed in the request\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:CreateVolume",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:us-east-1:1234567890:volume/*",
      "Condition": {
         "StringEquals": {
             "aws:RequestTag/purpose": "test",
             "ec2:CreateAction" : "CreateVolume"
          },
         "ForAllValues:StringEquals": {
             "aws:TagKeys": "purpose"
          }
       }
    }
  ]
}
```

## 5\. Working with Snapshots<a name="iam-example-manage-snapshots"></a>

**Topics**
+ [Creating a Snapshot](#iam-creating-snapshop)
+ [Creating a Snapshot with Tags](#iam-creating-snapshot-with-tags)
+ [Modifying Permission Settings for Snapshots](#iam-modifying-snapshot-with-tags)

### Creating a Snapshot<a name="iam-creating-snapshop"></a>

The following policy allows customers to use the [CreateSnapshot](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshot.html) API action\. The customer may create a snapshot only if the volume is encrypted and only if the volume size is less than 20 GiB\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*"
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1:123456789012:volume/*",
         "Condition":{
            "NumericLessThan":{
               "ec2:VolumeSize":"20"
            },
            "Bool":{
               "ec2:Encrypted":"true"
            }
         }
      }
   ]
}
```

### Creating a Snapshot with Tags<a name="iam-creating-snapshot-with-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires the customer to apply the tags `costcenter=115` and `stack=prod` to any new snapshot\. The `aws:TagKeys` condition key uses the `ForAllValues` modifier to indicate that only the keys `costcenter` and `stack` may be specified in the request\. The request fails if either of these conditions is not met\.

For resource\-creating actions that apply tags, customers must also have permissions to use the `CreateTags` action\. The third statement uses the `ec2:CreateAction` condition key to allow customers to create tags only in the context of `CreateSnapshot`\. Customers cannot tag existing volumes or any other resources\. For more information, see [Resource\-Level Permissions for Tagging](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-iam-actions-resources.html#supported-iam-actions-tagging)\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1:123456789012:volume/*"
      },
      {
         "Sid":"AllowCreateTaggedSnapshots",
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "aws:RequestTag/costcenter":"115",
               "aws:RequestTag/stack":"prod"
            },
            "ForAllValues:StringEquals":{
               "aws:TagKeys":[
                  "costcenter",
                  "stack"
               ]
            }
         }
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateTags",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "ec2:CreateAction":"CreateSnapshot"
            }
         }
      }
   ]
}
```

The following policy allows customers to create a snapshot without having to specify tags\. The `CreateTags` action is evaluated only if tags are specified in the `CreateSnapshot` request\. If a tag is specified, the tag must be `purpose=test`\. No other tags are allowed in the request\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"*"
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateTags",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "aws:RequestTag/purpose":"test",
               "ec2:CreateAction":"CreateSnapshot"
            },
            "ForAllValues:StringEquals":{
               "aws:TagKeys":"purpose"
            }
         }
      }
   ]
}
```

The following policy allows a snapshot to be created only if the source volume is tagged with `User:username` for the customer, and the snapshot itself is tagged with `Environment:Dev` and `User:username`\. The customer may add additional tags to the snapshot\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1:123456789012:volume/*",
         "Condition":{
            "StringEquals":{
               "ec2:ResourceTag/User":"${aws:username}"
            }
         }
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "aws:RequestTag/Environment":"Dev",
               "aws:RequestTag/User":"${aws:username}"
            }
         }
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateTags",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*"
      }
   ]
}
```

The following policy allows deletion of a snapshot only if the snapshot is tagged with User:*username* for the customer\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:DeleteSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "ec2:ResourceTag/User":"${aws:username}"
            }
         }
      }
   ]
}
```

The following policy allows a customer to create a snapshot but denies the action if the snapshot being created has a tag key `value=stack`\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "ec2:CreateSnapshot",
            "ec2:CreateTags"
         ],
         "Resource":"*"
      },
      {
         "Effect":"Deny",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "ForAnyValue:StringEquals":{
               "aws:TagKeys":"stack"
            }
         }
      }
   ]
}
```

### Modifying Permission Settings for Snapshots<a name="iam-modifying-snapshot-with-tags"></a>

The following policy allows modification of a snapshot only if the snapshot is tagged with `User:username`, where *username* is the customer's AWS account user name\. The request fails if this condition is not met\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2: ModifySnapshotAttribute",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "ec2:ResourceTag/user-name":"${aws:username}"
            }
         }
      }
   ]
}
```

## 6: Launching Instances \(RunInstances\)<a name="iam-example-runinstances"></a>

The [RunInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RunInstances.html) API action launches one or more instances\. `RunInstances` requires an AMI and creates an instance; and users can specify a key pair and security group in the request\. Launching into EC2\-VPC requires a subnet, and creates a network interface\. Launching from an Amazon EBS\-backed AMI creates a volume\. Therefore, the user must have permissions to use these Amazon EC2 resources\. You can create a policy statement that requires users to specify an optional parameter on `RunInstances`, or restricts users to particular values for a parameter\.

For more information about the resource\-level permissions that are required to launch an instance, see [Resource\-Level Permissions for RunInstances](ec2-supported-iam-actions-resources.md#supported-iam-actions-runinstances)\.

By default, users don't have permissions to describe, start, stop, or terminate the resulting instances\. One way to grant the users permission to manage the resulting instances is to create a specific tag for each instance, and then create a statement that enables them to manage instances with that tag\. For more information, see [3: Working with Instances](#iam-example-instances)\.

**Topics**
+ [AMI](#iam-example-runinstances-ami)
+ [Instance Type](#iam-example-runinstances-instance-type)
+ [Subnet](#iam-example-runinstances-subnet)
+ [EBS Volumes](#iam-example-runinstances-volumes)
+ [Applying Tags](#iam-example-runinstances-tags)
+ [Applying Tags in a Launch Template](#iam-example-tags-launch-template)
+ [Attaching an Elastic GPU](#iam-example-runinstances-egpu)
+ [Launch Templates](#iam-example-runinstances-launch-templates)

### AMI<a name="iam-example-runinstances-ami"></a>

The following policy allows users to launch instances using only the specified AMIs, `ami-9e1670f7` and `ami-45cf5c3c`\. The users can't launch an instance using other AMIs \(unless another statement grants the users permission to do so\)\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:region::image/ami-9e1670f7",
        "arn:aws:ec2:region::image/ami-45cf5c3c",
        "arn:aws:ec2:region:account:instance/*",
        "arn:aws:ec2:region:account:volume/*",
        "arn:aws:ec2:region:account:key-pair/*",
        "arn:aws:ec2:region:account:security-group/*",
        "arn:aws:ec2:region:account:subnet/*",
        "arn:aws:ec2:region:account:network-interface/*"
      ]
    }
   ]
}
```

Alternatively, the following policy allows users to launch instances from all AMIs owned by Amazon\. The `Condition` element of the first statement tests whether `ec2:Owner` is `amazon`\. The users can't launch an instance using other AMIs \(unless another statement grants the users permission to do so\)\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [ 
         "arn:aws:ec2:region::image/ami-*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:Owner": "amazon"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [ 
         "arn:aws:ec2:region:account:instance/*",
         "arn:aws:ec2:region:account:subnet/*",
         "arn:aws:ec2:region:account:volume/*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:key-pair/*",
         "arn:aws:ec2:region:account:security-group/*"
         ]
      }
   ]
}
```

\[EC2\-Classic only\] The following policy allows users to launch instances using only the AMIs that have the specified tag, "`department=dev`", associated with them\. The users can't launch instances using other AMIs because the `Condition` element of the first statement requires that users specify an AMI that has this tag\. Users can only launch into EC2\-Classic, as the policy does not grant permissions for the subnet and network interface resources\. The second statement uses a wildcard to enable users to create instance resources, and requires users to specify the key pair `project_keypair` and the security group `sg-1a2b3c4d`\. Users are still able to launch instances without a key pair\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [ 
         "arn:aws:ec2:region::image/ami-*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/department": "dev"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [ 
          "arn:aws:ec2:region:account:instance/*",
          "arn:aws:ec2:region:account:volume/*",
          "arn:aws:ec2:region:account:key-pair/project_keypair",
          "arn:aws:ec2:region:account:security-group/sg-1a2b3c4d"
         ]
      }
   ]
}
```

### Instance Type<a name="iam-example-runinstances-instance-type"></a>

The following policy allows users to launch instances using only the `t2.micro` or `t2.small` instance type, which you might do to control costs\. The users can't launch larger instances because the `Condition` element of the first statement tests whether `ec2:InstanceType` is either `t2.micro` or `t2.small`\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region:account:instance/*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:InstanceType": ["t2.micro", "t2.small"]
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region::image/ami-*",
         "arn:aws:ec2:region:account:subnet/*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:volume/*",
         "arn:aws:ec2:region:account:key-pair/*",
         "arn:aws:ec2:region:account:security-group/*"
         ]
      }
   ]
}
```

Alternatively, you can create a policy that denies users permissions to launch any instances except `t2.micro` and `t2.small` instance types\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region:account:instance/*"
      ],
      "Condition": {
         "StringNotEquals": {
            "ec2:InstanceType": ["t2.micro", "t2.small"]
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region::image/ami-*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:instance/*",
         "arn:aws:ec2:region:account:subnet/*",
         "arn:aws:ec2:region:account:volume/*",
         "arn:aws:ec2:region:account:key-pair/*",
         "arn:aws:ec2:region:account:security-group/*"
         ]
      }
   ]
}
```

### Subnet<a name="iam-example-runinstances-subnet"></a>

The following policy allows users to launch instances using only the specified subnet, `subnet-12345678`\. The group can't launch instances into any another subnet \(unless another statement grants the users permission to do so\)\. Users are still able to launch instances into EC2\-Classic\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:region:account:subnet/subnet-12345678",
        "arn:aws:ec2:region:account:network-interface/*",
        "arn:aws:ec2:region:account:instance/*",
        "arn:aws:ec2:region:account:volume/*",
        "arn:aws:ec2:region::image/ami-*",
        "arn:aws:ec2:region:account:key-pair/*",
        "arn:aws:ec2:region:account:security-group/*"
      ]
    }
   ]
}
```

Alternatively, you could create a policy that denies users permissions to launch an instance into any other subnet\. The statement does this by denying permission to create a network interface, except where subnet `subnet-12345678` is specified\. This denial overrides any other policies that are created to allow launching instances into other subnets\. Users are still able to launch instances into EC2\-Classic\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region:account:network-interface/*"
      ],
      "Condition": {
         "ArnNotEquals": {
            "ec2:Subnet": "arn:aws:ec2:region:account:subnet/subnet-12345678"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region::image/ami-*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:instance/*",
         "arn:aws:ec2:region:account:subnet/*",
         "arn:aws:ec2:region:account:volume/*",
         "arn:aws:ec2:region:account:key-pair/*",
         "arn:aws:ec2:region:account:security-group/*"
         ]
      }
   ]
}
```

### EBS Volumes<a name="iam-example-runinstances-volumes"></a>

The following policy allows users to launch instances only if the EBS volumes for the instance are encrypted\. The user must launch an instance from an AMI that was created with encrypted snapshots, to ensure that the root volume is encrypted\. Any additional volume that the user attaches to the instance during launch must also be encrypted\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:RunInstances",
            "Resource": [
                "arn:aws:ec2:*:*:volume/*"
            ],
            "Condition": {
                "Bool": {
                    "ec2:Encrypted": "true"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "ec2:RunInstances",
            "Resource": [
                "arn:aws:ec2:*::image/ami-*",
                "arn:aws:ec2:*:*:network-interface/*",
                "arn:aws:ec2:*:*:instance/*",
                "arn:aws:ec2:*:*:subnet/*",
                "arn:aws:ec2:*:*:key-pair/*",
                "arn:aws:ec2:*:*:security-group/*"
            ]
        }
    ]
}
```

### Applying Tags<a name="iam-example-runinstances-tags"></a>

The following policy allows users to launch instances and tag the instances during creation\. For resource\-creating actions that apply tags, users must have permissions to use the `CreateTags` action\. The second statement uses the `ec2:CreateAction` condition key to allow users to create tags only in the context of `RunInstances`, and only for instances\. Users cannot tag existing resources, and users cannot tag volumes using the `RunInstances` request\. 

For more information, see [Resource\-Level Permissions for Tagging](ec2-supported-iam-actions-resources.md#supported-iam-actions-tagging)\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*",
      "Condition": {
         "StringEquals": {
             "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

The following policy includes the `aws:RequestTag` condition key that requires users to tag any instances and volumes that are created by `RunInstances` with the tags `environment=production` and `purpose=webserver`\. The `aws:TagKeys` condition key uses the `ForAllValues` modifier to indicate that only the keys `environment` and `purpose` are allowed in the request \(no other tags can be specified\)\. If no tags are specified in the request, the request fails\. 

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances"
      ],
      "Resource": [
         "arn:aws:ec2:region::image/*",
         "arn:aws:ec2:region:account:subnet/*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:security-group/*",
         "arn:aws:ec2:region:account:key-pair/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances"
      ],
      "Resource": [
          "arn:aws:ec2:region:account:volume/*",
          "arn:aws:ec2:region:account:instance/*"
      ],
      "Condition": {
         "StringEquals": {
             "aws:RequestTag/environment": "production" ,
             "aws:RequestTag/purpose": "webserver"
          },
          "ForAllValues:StringEquals": {
              "aws:TagKeys": ["environment","purpose"]
          }
       }
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:region:account:*/*",
      "Condition": {
         "StringEquals": {
             "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

The following policy uses the `ForAnyValue` modifier on the `aws:TagKeys` condition to indicate that at least one tag must be specified in the request, and it must contain the key `environment` or `webserver`\. The tag must be applied to both instances and volumes\. Any tag values can be specified in the request\. 

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances"
      ],
      "Resource": [
         "arn:aws:ec2:region::image/*",
         "arn:aws:ec2:region:account:subnet/*",
         "arn:aws:ec2:region:account:network-interface/*",
         "arn:aws:ec2:region:account:security-group/*",
         "arn:aws:ec2:region:account:key-pair/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
          "ec2:RunInstances"
      ],
      "Resource": [
          "arn:aws:ec2:region:account:volume/*",
          "arn:aws:ec2:region:account:instance/*"
      ],
      "Condition": {
          "ForAnyValue:StringEquals": {
              "aws:TagKeys": ["environment","webserver"]
          }
       }
    },
    {
      "Effect": "Allow",
      "Action": [
          "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:region:account:*/*",
      "Condition": {
          "StringEquals": {
              "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

In the following policy, users do not have to specify tags in the request, but if they do, the tag must be `purpose=test`\. No other tags are allowed\. Users can apply the tags to any taggable resource in the `RunInstances` request\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:region:account:*/*",
      "Condition": {
         "StringEquals": {
             "aws:RequestTag/purpose": "test",
             "ec2:CreateAction" : "RunInstances"
          },
          "ForAllValues:StringEquals": {
              "aws:TagKeys": "purpose"
          }
       }
    }
  ]
}
```

### Applying Tags in a Launch Template<a name="iam-example-tags-launch-template"></a>

In the following example, users can launch instances, but only if they use a specific launch template \(`lt-09477bcd97b0d310e`\)\. The `ec2:IsLaunchTemplateResource` condition key prevents users from overriding any of the resources specified in the launch template\. The second part of the statement allows users to tag instances on creation—this part of the statement is necessary if tags are specified for the instance in the launch template\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "*",
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account:launch-template/lt-09477bcd97b0d310e" 
          },
          "Bool": {
             "ec2:IsLaunchTemplateResource": "true"
          }
       }
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:region:account:instance/*",
      "Condition": {
         "StringEquals": {
             "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

### Attaching an Elastic GPU<a name="iam-example-runinstances-egpu"></a>

In the following policy, users can launch an instance and specify an elastic GPU to attach to the instance\. Users can launch instances in any region, but they can only attach an elastic GPU during a launch in the `us-east-2` region\. 

The `ec2:ElasticGpuType` condition key uses the `ForAnyValue` modifier to indicate that only the elastic GPU types `eg1.medium` and `eg1.large` are allowed in the request\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances"
            ],
            "Resource": [
                "arn:aws:ec2:*:account:elastic-gpu/*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:Region": "us-east-2"
                },
                "ForAnyValue:StringLike": {
                    "ec2:ElasticGpuType": [
                        "eg1.medium",
                        "eg1.large"
                    ]
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "ec2:RunInstances",
            "Resource": [
                "arn:aws:ec2:*::image/ami-*",
                "arn:aws:ec2:*:account:network-interface/*",
                "arn:aws:ec2:*:account:instance/*",
                "arn:aws:ec2:*:account:subnet/*",
                "arn:aws:ec2:*:account:volume/*",
                "arn:aws:ec2:*:account:key-pair/*",
                "arn:aws:ec2:*:account:security-group/*"
            ]
        }
    ]
}
```

### Launch Templates<a name="iam-example-runinstances-launch-templates"></a>

In the following example, users can launch instances, but only if they use a specific launch template \(`lt-09477bcd97b0d310e`\)\. Users can override any parameters in the launch template by specifying the parameters in the `RunInstances` action\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "*",
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account:launch-template/lt-09477bcd97b0d310e" 
          }
       }
    }
  ]
}
```

In this example, users can launch instances only if they use a launch template\. The policy uses the `ec2:IsLaunchTemplateResource` condition key to prevent users from overriding any of the launch template resources in the `RunInstances` request\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "*",
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account:launch-template/*" 
          },
          "Bool": {
             "ec2:IsLaunchTemplateResource": "true"
          }
       }
    }
  ]
}
```

The following example policy allows user to launch instances, but only if they use a launch template\. Users cannot override the subnet and network interface parameters in the request; these parameters can only be specified in the launch template\. The first part of the statement uses the [NotResource](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notresource.html) element to allow all other resources except subnets and network interfaces\. The second part of the statement allows the subnet and network interface resources, but only if they are sourced from the launch template\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "NotResource": ["arn:aws:ec2:region:account:subnet/*",
                      "arn:aws:ec2:region:account:network-interface/*" ],
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account:launch-template/*" 
          }
       }
    },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": ["arn:aws:ec2:region:account:subnet/*",
                   "arn:aws:ec2:region:account:network-interface/*" ],
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account:launch-template/*" 
          },
          "Bool": {
             "ec2:IsLaunchTemplateResource": "true"
          }
       }
    }
  ]
}
```

The following example allows users to launch instances only if they use a launch template, and only if the launch template has the tag `Purpose=Webservers`\. Users cannot override any of the launch template parameters in the `RunInstances` action\.

```
{
  "Version": "2012-10-17", 
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "NotResource": "arn:aws:ec2:region:account:launch-template/*",
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account:launch-template/*" 
          },
         "Bool": {
             "ec2:IsLaunchTemplateResource": "true"
          }
       }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:region:account:launch-template/*",
      "Condition": {
       "StringEquals": {
           "ec2:ResourceTag/Purpose": "Webservers" 
        }
       }
     }
  ]
}
```

## 7\. Working with ClassicLink<a name="iam-example-classiclink"></a>

You can enable a VPC for ClassicLink and then link an EC2\-Classic instance to the VPC\. You can also view your ClassicLink\-enabled VPCs, and all of your EC2\-Classic instances that are linked to a VPC\. You can create policies with resource\-level permission for the `ec2:EnableVpcClassicLink`, `ec2:DisableVpcClassicLink`, `ec2:AttachClassicLinkVpc`, and `ec2:DetachClassicLinkVpc` actions to control how users are able to use those actions\. Resource\-level permissions are not supported for `ec2:Describe*` actions\.

**Topics**
+ [Full Permissions to Work with ClassicLink](#iam-example-classiclink-full)
+ [Enable and Disable a VPC for ClassicLink](#iam-example-classiclink-enable)
+ [Link Instances](#iam-example-classiclink-link)
+ [Unlink Instances](#iam-example-classiclink-unlink)

### Full Permissions to Work with ClassicLink<a name="iam-example-classiclink-full"></a>

The following policy grants users permissions to view ClassicLink\-enabled VPCs and linked EC2\-Classic instances, to enable and disable a VPC for ClassicLink, and to link and unlink instances from a ClassicLink\-enabled VPC\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeClassicLinkInstances", "ec2:DescribeVpcClassicLink",
        "ec2:EnableVpcClassicLink", "ec2:DisableVpcClassicLink",
        "ec2:AttachClassicLinkVpc", "ec2:DetachClassicLinkVpc"
      ],
      "Resource": "*"
    }
   ]
}
```

### Enable and Disable a VPC for ClassicLink<a name="iam-example-classiclink-enable"></a>

The following policy allows user to enable and disable VPCs for ClassicLink that have the specific tag '`purpose=classiclink`'\. Users cannot enable or disable any other VPCs for ClassicLink\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*VpcClassicLink",
      "Resource": "arn:aws:ec2:region:account:vpc/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/purpose":"classiclink"
        }
      }
    }
  ]
}
```

### Link Instances<a name="iam-example-classiclink-link"></a>

The following policy grants users permissions to link instances to a VPC only if the instance is an `m3.large` instance type\. The second statement allows users to use the VPC and security group resources, which are required to link an instance to a VPC\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:AttachClassicLinkVpc",
      "Resource": "arn:aws:ec2:region:account:instance/*",
      "Condition": {
        "StringEquals": {
          "ec2:InstanceType":"m3.large"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:AttachClassicLinkVpc",
      "Resource": [
        "arn:aws:ec2:region:account:vpc/*",
        "arn:aws:ec2:region:account:security-group/*"
      ]
    }
  ]
}
```

The following policy grants users permissions to link instances to a specific VPC \(`vpc-1a2b3c4d`\) only, and to associate only specific security groups from the VPC to the instance \(`sg-1122aabb` and `sg-aabb2233`\)\. Users cannot link an instance to any other VPC, and they cannot specify any other of the VPC security groups to associate with the instance in the request\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:AttachClassicLinkVpc",
      "Resource": [
         "arn:aws:ec2:region:account:vpc/vpc-1a2b3c4d",
         "arn:aws:ec2:region:account:instance/*",
         "arn:aws:ec2:region:account:security-group/sg-1122aabb",
         "arn:aws:ec2:region:account:security-group/sg-aabb2233"
       ]
    }
  ]
}
```

### Unlink Instances<a name="iam-example-classiclink-unlink"></a>

The following grants users permission to unlink any linked EC2\-Classic instance from a VPC, but only if the instance has the tag "`unlink=true`"\. The second statement grants users permissions to use the VPC resource, which is required to unlink an instance from a VPC\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:DetachClassicLinkVpc",
      "Resource": [
         "arn:aws:ec2:region:account:instance/*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/unlink":"true"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:DetachClassicLinkVpc",
      "Resource": [
         "arn:aws:ec2:region:account:vpc/*"
         ]
      }
   ]
}
```

## 8\. Working with Reserved Instances<a name="iam-example-reservedinstances"></a>

The following policy gives users permission to view, modify, and purchase Reserved Instances in your account\.

It is not possible to set resource\-level permissions for individual Reserved Instances\. This policy means that users have access to all the Reserved Instances in the account\.

The `Resource` element uses a \* wildcard to indicate that users can specify all resources with the action; in this case, they can list and modify all Reserved Instances in the account\. They can also purchase Reserved Instances using the account credentials\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeReservedInstances", "ec2:ModifyReservedInstances",
        "ec2:PurchaseReservedInstancesOffering", "ec2:DescribeAvailabilityZones",
        "ec2:DescribeReservedInstancesOfferings"
      ],
      "Resource": "*"
    }
   ]
}
```

To allow users to view and modify the Reserved Instances in your account, but not purchase new Reserved Instances\.

```
{
  "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeReservedInstances", "ec2:ModifyReservedInstances",
        "ec2:DescribeAvailabilityZones"
      ],
      "Resource": "*"
    }
  ]
}
```

## 9\. Tagging Resources<a name="iam-example-taggingresources"></a>

The following policy allows users to use the `CreateTags` action to apply tags to an instance only if the tag contains the key `environment` and the value `production`\. The `ForAllValues` modifier is used with the `aws:TagKeys` condition key to indicate that only the key `environment` is allowed in the request \(no other tags are allowed\)\. The user cannot tag any other resource types\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": "arn:aws:ec2:region:account:instance/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/environment": "production"
                },
                "ForAllValues:StringEquals": {
                    "aws:TagKeys": [
                        "environment"
                    ]
                }
            }
        }
    ]
}
```

The following policy allows users to tag any taggable resource that already has a tag with a key of `owner` and a value of the IAM username\. In addition, users must specify a tag with a key of `environment` and a value of either `test` or `prod` in the request\. Users can specify additional tags in the request\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": "arn:aws:ec2:region:account:*/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/environment": ["test","prod"],
                    "ec2:ResourceTag/owner": "${aws:username}"
                }
            }
        }
    ]
}
```

You can create an IAM policy that allows users to delete specific tags for a resource\. For example, the following policy allows users to delete tags for a volume if the tag keys specified in the request are `environment` or `cost-center`\. Any value can be specified for the tag but the tag key must match either of the specified keys\.

**Note**  
If you delete a resource, all tags associated with the resource are also deleted\. Users do not need permissions to use the `ec2:DeleteTags` action to delete a resource that has tags; they only need permissions to perform the deleting action\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:DeleteTags",
      "Resource": "arn:aws:ec2:us-east-1:123456789012:volume/*",
      "Condition": {
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["environment","cost-center"]
        }
      }
    }
  ]
}
```

This policy allows users to delete only the `environment=prod` tag on any resource, and only if the resource is already tagged with a key of `owner` and a value of the IAM username\. Users cannot delete any other tags for a resource\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DeleteTags"
      ],
      "Resource": "arn:aws:ec2:region:account:*/*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/environment": "prod",
          "ec2:ResourceTag/owner": "${aws:username}"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["environment"]
        }
      }
    }
  ]
}
```

## 10: Working with IAM Roles<a name="iam-example-iam-roles"></a>

The following policy allows users to attach, replace, and detach an IAM role to instances that have the tag `department=test`\. Replacing or detaching an IAM role requires an association ID, therefore the policy also grants users permission to use the `ec2:DescribeIamInstanceProfileAssociations` action\. 

IAM users must have permission to use the `iam:PassRole` action in order to pass the role to the instance\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:AssociateIamInstanceProfile",
         "ec2:ReplaceIamInstanceProfileAssociation",
         "ec2:DisassociateIamInstanceProfile"
      ],
      "Resource": "arn:aws:ec2:region:account:instance/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/department":"test"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:DescribeIamInstanceProfileAssociations",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "iam:PassRole",
      "Resource": "*"
    }
  ]
}
```

The following policy allows users to attach or replace an IAM role for any instance\. Users can only attach or replace IAM roles with names that begin with `TestRole-`\. For the `iam:PassRole` action, ensure that you specify the name of the IAM role and not the instance profile \(if the names are different\)\. For more information, see [Instance Profiles](iam-roles-for-amazon-ec2.md#ec2-instance-profile)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:AssociateIamInstanceProfile",
                "ec2:ReplaceIamInstanceProfileAssociation"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "ec2:DescribeIamInstanceProfileAssociations",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::account:role/TestRole-*"
        }
    ]
}
```

## 11: Working with Route Tables<a name="iam-example-route-tables"></a>

The following policy allows users to add, remove, and replace routes for route tables that are associated with VPC `vpc-ec43eb89` only\. To specify a VPC for the `ec2:Vpc` condition key, you must specify the full ARN of the VPC\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DeleteRoute",
                "ec2:CreateRoute",
                "ec2:ReplaceRoute"
            ],
            "Resource": [
                "arn:aws:ec2:region:account:route-table/*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:Vpc": "arn:aws:ec2:region:account:vpc/vpc-ec43eb89"
                }
            }
        }
    ]
}
```

## 12: Allowing a Specific Instance to View Resources in Other AWS Services<a name="iam-example-source-instance"></a>

The following is an example of a policy that you might attach to an IAM role\. The policy allows an instance to view resources in various AWS services\. It uses the `ec2:SourceInstanceARN` condition key to specify that the instance from which the request is made must be instance `i-093452212644b0dd6`\. If the same IAM role is associated with another instance, the other instance cannot perform any of these actions\.

The `ec2:SourceInstanceARN` key is an AWS\-wide condition key, therefore it can be used for other service actions, not just Amazon EC2\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeVolumes",
                "s3:ListAllMyBuckets",
                "dynamodb:ListTables",
                "rds:DescribeDBInstances"
            ],
            "Resource": [
                "*"
            ],
            "Condition": {
                "ArnEquals": {
                    "ec2:SourceInstanceARN": "arn:aws:ec2:region:account:instance/i-093452212644b0dd6"
                }
            }
        }
    ]
}
```

## 13\. Working with Launch Templates<a name="iam-example-launch-templates"></a>

The following policy allows users to create a launch template version and modify a launch template, but only for a specific launch template \(`lt-09477bcd97b0d3abc`\)\. Users cannot work with other launch templates\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ec2:CreateLaunchTemplateVersion",
        "ec2:ModifyLaunchTemplate"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:ec2:region:account:launch-template/lt-09477bcd97b0d3abc"
    }
  ]
}
```

The following policy allows users to delete any launch template and launch template version, provided that the launch template has the tag `Purpose`=`Testing`\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "ec2:DeleteLaunchTemplate",
        "ec2:DeleteLaunchTemplateVersions"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:ec2:region:account:launch-template/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/Purpose": "Testing"
        }
      }
    }
  ]
}
```
# Example Policies for Working with the AWS CLI or an AWS SDK<a name="ExamplePolicies_EC2"></a>

The following examples show policy statements that you could use to control the permissions that IAM users have to Amazon EC2\. These policies are designed for requests that are made with the AWS CLI or an AWS SDK\. For example policies for working in the Amazon EC2 console, see [Example Policies for Working in the Amazon EC2 Console](iam-policies-ec2-console.md)\. For examples of IAM policies specific to Amazon VPC, see [Controlling Access to Amazon VPC Resources](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_IAM.html)\.

**Topics**
+ [Read\-Only Access](#iam-example-read-only)
+ [Restricting Access to a Specific Region](#iam-example-region)
+ [Working with Instances](#iam-example-instances)
+ [Working with Volumes](#iam-example-manage-volumes)
+ [Working with Snapshots](#iam-example-manage-snapshots)
+ [Launching Instances \(RunInstances\)](#iam-example-runinstances)
+ [Working with Reserved Instances](#iam-example-reservedinstances)
+ [Tagging Resources](#iam-example-taggingresources)
+ [Working with IAM Roles](#iam-example-iam-roles)
+ [Working with Route Tables](#iam-example-route-tables)
+ [Allowing a Specific Instance to View Resources in Other AWS Services](#iam-example-source-instance)
+ [Working with Launch Templates](#iam-example-launch-templates)

## Example: Read\-Only Access<a name="iam-example-read-only"></a>

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

## Example: Restricting Access to a Specific Region<a name="iam-example-region"></a>

The following policy denies users permission to use all Amazon EC2 API actions unless the Region is EU \(Frankfurt\)\. It uses the global condition key `aws:RequestedRegion`, which is supported by all Amazon EC2 API actions\.

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect": "Deny",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": "eu-central-1"
        }
      }
    }  
  ]
}
```

Alternatively, you can use the condition key `ec2:Region`, which is specific to Amazon EC2 and is supported by all Amazon EC2 API actions\.

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect": "Deny",
      "Action": "ec2:*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "ec2:Region": "eu-central-1"
        }
      }
    }  
  ]
}
```

## Working with Instances<a name="iam-example-instances"></a>

**Topics**
+ [Example: Describe, Launch, Stop, Start, and Terminate All Instances](#iam-example-instances-all)
+ [Example: Describe All Instances, and Stop, Start, and Terminate Only Particular Instances](#iam-example-instances-specific)

### Example: Describe, Launch, Stop, Start, and Terminate All Instances<a name="iam-example-instances-all"></a>

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

### Example: Describe All Instances, and Stop, Start, and Terminate Only Particular Instances<a name="iam-example-instances-specific"></a>

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

## Working with Volumes<a name="iam-example-manage-volumes"></a>

**Topics**
+ [Example: Attaching and Detaching Volumes](#iam-example-manage-volumes-attach-detach)
+ [Example: Creating a Volume](#iam-example-manage-volumes-create)
+ [Example: Creating a Volume with Tags](#iam-example-manage-volumes-tags)

### Example: Attaching and Detaching Volumes<a name="iam-example-manage-volumes-attach-detach"></a>

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

### Example: Creating a Volume<a name="iam-example-manage-volumes-create"></a>

The following policy allows users to use the [CreateVolume](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateVolume.html) API action\. The user is allowed to create a volume only if the volume is encrypted and only if the volume size is less than 20 GiB\.

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

### Example: Creating a Volume with Tags<a name="iam-example-manage-volumes-tags"></a>

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

## Working with Snapshots<a name="iam-example-manage-snapshots"></a>

This sections contains example policies for both `CreateSnapshot` \(point\-in\-time snapshot of an EBS volume\) and `CreateSnapshots` \(multi\-volume snapshots\)\.

**Topics**
+ [Example: Creating a Snapshot](#iam-creating-snapshot)
+ [Example: Creating Snapshots](#iam-creating-snapshots)
+ [Example: Creating a Snapshot with Tags](#iam-creating-snapshot-with-tags)
+ [Example: Creating Snapshots with Tags](#iam-creating-snapshots-with-tags)
+ [Example: Modifying Permission Settings for Snapshots](#iam-modifying-snapshot-with-tags)

### Example: Creating a Snapshot<a name="iam-creating-snapshot"></a>

The following policy allows customers to use the [CreateSnapshot](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshot.html) API action\. The customer can create snapshots only if the volume is encrypted and only if the volume size is less than 20 GiB\.

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

### Example: Creating Snapshots<a name="iam-creating-snapshots"></a>

The following policy allows customers to use the [CreateSnapshots](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshots.html) API action\. The customer can create snapshots only if all of the volumes on the instance are type GP2\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":[
"arn:aws:ec2:us-east-1::snapshot/*",
"arn:aws:ec2:*:*:instance/*"
   ]
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":"arn:aws:ec2:us-east-1:*:volume/*",
         "Condition":{
            "StringLikeIfExists":{
               "ec2:VolumeType":"gp2"
             }
	    }
            
      }
   ]
}
```

### Example: Creating a Snapshot with Tags<a name="iam-creating-snapshot-with-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires the customer to apply the tags `costcenter=115` and `stack=prod` to any new snapshot\. The `aws:TagKeys` condition key uses the `ForAllValues` modifier to indicate that only the keys `costcenter` and `stack` can be specified in the request\. The request fails if either of these conditions is not met\.

For resource\-creating actions that apply tags, customers must also have permissions to use the `CreateTags` action\. The third statement uses the `ec2:CreateAction` condition key to allow customers to create tags only in the context of `CreateSnapshot`\. Customers cannot tag existing volumes or any other resources\. For more information, see [Resource\-Level Permissions for Tagging](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-iam-actions-resources.html#supported-iam-actions-tagging)\.

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

### Example: Creating Snapshots with Tags<a name="iam-creating-snapshots-with-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires the customer to apply the tags `costcenter=115` and `stack=prod` to any new snapshot\. The `aws:TagKeys` condition key uses the `ForAllValues` modifier to indicate that only the keys `costcenter` and `stack` can be specified in the request\. The request fails if either of these conditions is not met\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":[
"arn:aws:ec2:us-east-1::snapshot/*",
"arn:aws:ec2:*:*:instance/*",
"arn:aws:ec2:*:*:volume/*"

   ]
      },
      {
         "Sid":"AllowCreateTaggedSnapshots",
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
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
               "ec2:CreateAction":"CreateSnapshots"
            }
         }
      }
   ]
}
```

The following policy allows customers to create a snapshot without having to specify tags\. The `CreateTags` action is evaluated only if tags are specified in the `CreateSnapshot` or `CreateSnapshots` request\. If a tag is specified, the tag must be `purpose=test`\. No other tags are allowed in the request\.

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

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":"*"
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateTags",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "aws:RequestTag/purpose":"test",
               "ec2:CreateAction":"CreateSnapshots"
            },
            "ForAllValues:StringEquals":{
               "aws:TagKeys":"purpose"
            }
         }
      }
   ]
}
```

The following policy allows snapshots to be created only if the source volume is tagged with `User:username` for the customer, and the snapshot itself is tagged with `Environment:Dev` and `User:username`\. The customer can add additional tags to the snapshot\.

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

The following policy for `CreateSnapshots` allows snapshots to be created only if the source volume is tagged with `User:username` for the customer, and the snapshot itself is tagged with `Environment:Dev` and `User:username`\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
	{
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":"arn:aws:ec2:us-east-1:*:instance/*",
	},
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":"arn:aws:ec2:us-east-1:123456789012:volume/*",
         "Condition":{
            "StringEquals":{
               "ec2:ResourceTag/User":"${aws:username}"
            }
         }
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
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

The following policy allows a customer to create snapshots but denies the action if the snapshots being created have a tag key `value=stack`\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "ec2:CreateSnapshots",
            "ec2:CreateTags"
         ],
         "Resource":"*"
      },
      {
         "Effect":"Deny",
         "Action":"ec2:CreateSnapshots",
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

The following policy allows you to combine multiple actions into a single policy\. You can only create a snapshot \(in the context of `CreateSnapshots`\) when the snapshot is created in Region `us-east-1`\. You can only create snapshots \(in the context of `CreateSnapshots`\) when the snapshots are being created in the Region `us-east-1` and when the instance type is `t2*`\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "ec2:CreateSnapshots",
            "ec2:CreateSnapshot",
            "ec2:CreateTags"
         ],
         "Resource": [
            "arn:aws:ec2:*:*:instance/*",
            "arn:aws:ec2:*:*:snapshot/*",
            "arn:aws:ec2:*:*:volume/*"
         ],
         "Condition":{
            "StringEqualsIgnoreCase": {
            "ec2:Region": "us-east-1"
            },
            "StringLikeIfExists": {
            "ec2:InstanceType": [
            "t2.*"
            ]
            }
         }
      }
   ]
}
```

### Example: Modifying Permission Settings for Snapshots<a name="iam-modifying-snapshot-with-tags"></a>

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

## Launching Instances \(RunInstances\)<a name="iam-example-runinstances"></a>

The [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RunInstances.html) API action launches one or more instances\. `RunInstances` requires an AMI and creates an instance; and users can specify a key pair and security group in the request\. Launching into a VPC requires a subnet, and creates a network interface\. Launching from an Amazon EBS\-backed AMI creates a volume\. Therefore, the user must have permissions to use these Amazon EC2 resources\. You can create a policy statement that requires users to specify an optional parameter on `RunInstances`, or restricts users to particular values for a parameter\.

For more information about the resource\-level permissions that are required to launch an instance, see [Actions, Resources, and Condition Keys for Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html) in the *IAM User Guide*\.

By default, users don't have permissions to describe, start, stop, or terminate the resulting instances\. One way to grant the users permission to manage the resulting instances is to create a specific tag for each instance, and then create a statement that enables them to manage instances with that tag\. For more information, see [Working with Instances](#iam-example-instances)\.

**Topics**
+ [AMIs](#iam-example-runinstances-ami)
+ [Instance Types](#iam-example-runinstances-instance-type)
+ [Subnets](#iam-example-runinstances-subnet)
+ [EBS Volumes](#iam-example-runinstances-volumes)
+ [Tags](#iam-example-runinstances-tags)
+ [Tags in a Launch Template](#iam-example-tags-launch-template)
+ [Elastic GPUs](#iam-example-runinstances-egpu)
+ [Launch Templates](#iam-example-runinstances-launch-templates)

### AMIs<a name="iam-example-runinstances-ami"></a>

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

### Instance Types<a name="iam-example-runinstances-instance-type"></a>

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

### Subnets<a name="iam-example-runinstances-subnet"></a>

The following policy allows users to launch instances using only the specified subnet, `subnet-12345678`\. The group can't launch instances into any another subnet \(unless another statement grants the users permission to do so\)\.

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

Alternatively, you could create a policy that denies users permissions to launch an instance into any other subnet\. The statement does this by denying permission to create a network interface, except where subnet `subnet-12345678` is specified\. This denial overrides any other policies that are created to allow launching instances into other subnets\.

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

### Tags<a name="iam-example-runinstances-tags"></a>

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

### Tags in a Launch Template<a name="iam-example-tags-launch-template"></a>

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

### Elastic GPUs<a name="iam-example-runinstances-egpu"></a>

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

The following example policy allows user to launch instances, but only if they use a launch template\. Users cannot override the subnet and network interface parameters in the request; these parameters can only be specified in the launch template\. The first part of the statement uses the [NotResource](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notresource.html) element to allow all other resources except subnets and network interfaces\. The second part of the statement allows the subnet and network interface resources, but only if they are sourced from the launch template\.

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

## Example: Working with Reserved Instances<a name="iam-example-reservedinstances"></a>

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

## Example: Tagging Resources<a name="iam-example-taggingresources"></a>

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

## Example: Working with IAM Roles<a name="iam-example-iam-roles"></a>

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

## Example: Working with Route Tables<a name="iam-example-route-tables"></a>

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

## Example: Allowing a Specific Instance to View Resources in Other AWS Services<a name="iam-example-source-instance"></a>

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

## Example: Working with Launch Templates<a name="iam-example-launch-templates"></a>

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
# Example policies for working with the AWS CLI or an AWS SDK<a name="ExamplePolicies_EC2"></a>

You must grant users the permissions they require for Amazon EC2 using IAM policies\. The following examples show policy statements that you could use to control the permissions that users have to Amazon EC2\. These policies are designed for requests that are made with the AWS CLI or an AWS SDK\. For more information, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the IAM User Guide\. For example policies for working in the Amazon EC2 console, see [Example policies for working in the Amazon EC2 console](iam-policies-ec2-console.md)\. For examples of IAM policies specific to Amazon VPC, see [Identity and Access Management for Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/security-iam.html)\.

In the following examples, replace each *user input placeholder* with your own information\.

**Note**  
 

**Topics**
+ [Read\-only access](#iam-example-read-only)
+ [Restrict access to a specific Region](#iam-example-region)
+ [Work with instances](#iam-example-instances)
+ [Work with volumes](#iam-example-manage-volumes)
+ [Work with snapshots](#iam-example-manage-snapshots)
+ [Launch instances \(RunInstances\)](#iam-example-runinstances)
+ [Work with Spot Instances](#iam-example-spot-instances)
+ [Work with Reserved Instances](#iam-example-reservedinstances)
+ [Tag resources](#iam-example-taggingresources)
+ [Work with IAM roles](#iam-example-iam-roles)
+ [Work with route tables](#iam-example-route-tables)
+ [Allow a specific instance to view resources in other AWS services](#iam-example-source-instance)
+ [Work with launch templates](#iam-example-launch-templates)
+ [Work with instance metadata](#iam-example-instance-metadata)

## Example: Read\-only access<a name="iam-example-read-only"></a>

The following policy grants users permissions to use all Amazon EC2 API actions whose names begin with `Describe`\. The `Resource` element uses a wildcard to indicate that users can specify all resources with these API actions\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Actions, resources, and condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html)\.

Users don't have permission to perform any actions on the resources \(unless another statement grants them permission to do so\) because they're denied permission to use API actions by default\.

```
{
   "Version": "2012-10-17",
   "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:Describe*",
      "Resource": "*"
    }
   ]
}
```

## Example: Restrict access to a specific Region<a name="iam-example-region"></a>

The following policy denies users permission to use all Amazon EC2 API actions unless the Region is Europe \(Frankfurt\)\. It uses the global condition key `aws:RequestedRegion`, which is supported by all Amazon EC2 API actions\.

```
{
  "Version":"2012-10-17",
  "Statement": [
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
  "Statement": [
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

## Work with instances<a name="iam-example-instances"></a>

**Topics**
+ [Example: Describe, launch, stop, start, and terminate all instances](#iam-example-instances-all)
+ [Example: Describe all instances, and stop, start, and terminate only particular instances](#iam-example-instances-specific)

### Example: Describe, launch, stop, start, and terminate all instances<a name="iam-example-instances-all"></a>

The following policy grants users permissions to use the API actions specified in the `Action` element\. The `Resource` element uses a \* wildcard to indicate that users can specify all resources with these API actions\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Actions, resources, and condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html)\.

The users don't have permission to use any other API actions \(unless another statement grants them permission to do so\) because users are denied permission to use API actions by default\.

```
{
   "Version": "2012-10-17",
   "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeInstances", 
        "ec2:DescribeImages",
        "ec2:DescribeKeyPairs", 
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeAvailabilityZones",
        "ec2:RunInstances", 
        "ec2:TerminateInstances",
        "ec2:StopInstances", 
        "ec2:StartInstances"
      ],
      "Resource": "*"
    }
   ]
}
```

### Example: Describe all instances, and stop, start, and terminate only particular instances<a name="iam-example-instances-specific"></a>

The following policy allows users to describe all instances, to start and stop only instances i\-1234567890abcdef0 and i\-0598c7d356eba48d7, and to terminate only instances in the US East \(N\. Virginia\) Region \(`us-east-1`\) with the resource tag "`purpose=test`"\. 

The first statement uses a \* wildcard for the `Resource` element to indicate that users can specify all resources with the action; in this case, they can list all instances\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions \(in this case, `ec2:DescribeInstances`\)\. For more information about which ARNs you can use with which Amazon EC2 API actions, see [Actions, resources, and condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html)\.

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
        "arn:aws:ec2:us-east-1:account-id:instance/i-1234567890abcdef0",
        "arn:aws:ec2:us-east-1:account-id:instance/i-0598c7d356eba48d7"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "ec2:TerminateInstances",
      "Resource": "arn:aws:ec2:us-east-1:account-id:instance/*",
      "Condition": {
         "StringEquals": {
            "aws:ResourceTag/purpose": "test"
         }
      }
   }

   ]
}
```

## Work with volumes<a name="iam-example-manage-volumes"></a>

**Topics**
+ [Example: Attach and detach volumes](#iam-example-manage-volumes-attach-detach)
+ [Example: Create a volume](#iam-example-manage-volumes-create)
+ [Example: Create a volume with tags](#iam-example-manage-volumes-tags)

### Example: Attach and detach volumes<a name="iam-example-manage-volumes-attach-detach"></a>

When an API action requires a caller to specify multiple resources, you must create a policy statement that allows users to access all required resources\. If you need to use a `Condition` element with one or more of these resources, you must create multiple statements as shown in this example\.

The following policy allows users to attach volumes with the tag "`volume_user`=*iam\-user\-name*" to instances with the tag "`department=dev`", and to detach those volumes from those instances\. If you attach this policy to an IAM group, the `aws:username` policy variable gives each user in the group permission to attach or detach volumes from the instances with a tag named `volume_user` that has their username as a value\.

```
{
   "Version": "2012-10-17",
   "Statement": [
       {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:DetachVolume"
      ],
      "Resource": "arn:aws:ec2:us-east-1:account-id:instance/*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/department": "dev"
        }
      }
   },
   {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:DetachVolume"
      ],
      "Resource": "arn:aws:ec2:us-east-1:account-id:volume/*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/volume_user": "${aws:username}"
        }
      }
   }
  ]
}
```

### Example: Create a volume<a name="iam-example-manage-volumes-create"></a>

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
      "Resource": "arn:aws:ec2:us-east-1:account-id:volume/*",
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

### Example: Create a volume with tags<a name="iam-example-manage-volumes-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires users to tag any volumes they create with the tags `costcenter=115` and `stack=prod`\. If users don't pass these specific tags, or if they don't specify tags at all, the request fails\. 

For resource\-creating actions that apply tags, users must also have permissions to use the `CreateTags` action\. The second statement uses the `ec2:CreateAction` condition key to allow users to create tags only in the context of `CreateVolume`\. Users cannot tag existing volumes or any other resources\. For more information, see [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)\.

```
{
  "Version": "2012-10-17",
  "Statement": [
        {
      "Sid": "AllowCreateTaggedVolumes",
      "Effect": "Allow",
      "Action": "ec2:CreateVolume",
      "Resource": "arn:aws:ec2:us-east-1:account-id:volume/*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/costcenter": "115",
          "aws:RequestTag/stack": "prod"
         }
       }
     },
     {
       "Effect": "Allow",
       "Action": [
         "ec2:CreateTags"
       ],
       "Resource": "arn:aws:ec2:us-east-1:account-id:volume/*",
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
      "Resource": "arn:aws:ec2:us-east-1:account-id:volume/*",
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

## Work with snapshots<a name="iam-example-manage-snapshots"></a>

The following are example policies for both `CreateSnapshot` \(point\-in\-time snapshot of an EBS volume\) and `CreateSnapshots` \(multi\-volume snapshots\)\.

**Topics**
+ [Example: Create a snapshot](#iam-creating-snapshot)
+ [Example: Create snapshots](#iam-creating-snapshots)
+ [Example: Create a snapshot with tags](#iam-creating-snapshot-with-tags)
+ [Example: Create multi\-volume snapshots with tags](#iam-creating-snapshots-with-tags)
+ [Example: Copying snapshots](#iam-copy-snapshot)
+ [Example: Modify permission settings for snapshots](#iam-modifying-snapshot-with-tags)

### Example: Create a snapshot<a name="iam-creating-snapshot"></a>

The following policy allows customers to use the [CreateSnapshot](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshot.html) API action\. The customer can create snapshots only if the volume is encrypted and only if the volume size is less than 20 GiB\.

```
{
   "Version":"2012-10-17",
   "Statement": [
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*"
      },
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1:account-id:volume/*",
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

### Example: Create snapshots<a name="iam-creating-snapshots"></a>

The following policy allows customers to use the [CreateSnapshots](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateSnapshots.html) API action\. The customer can create snapshots only if all of the volumes on the instance are type GP2\.

```
{
   "Version":"2012-10-17",
   "Statement": [
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

### Example: Create a snapshot with tags<a name="iam-creating-snapshot-with-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires the customer to apply the tags `costcenter=115` and `stack=prod` to any new snapshot\. If users don't pass these specific tags, or if they don't specify tags at all, the request fails\.

For resource\-creating actions that apply tags, customers must also have permissions to use the `CreateTags` action\. The third statement uses the `ec2:CreateAction` condition key to allow customers to create tags only in the context of `CreateSnapshot`\. Customers cannot tag existing volumes or any other resources\. For more information, see [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)\.

```
{
   "Version":"2012-10-17",
   "Statement": [
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1:account-id:volume/*"
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

### Example: Create multi\-volume snapshots with tags<a name="iam-creating-snapshots-with-tags"></a>

The following policy includes the `aws:RequestTag` condition key that requires the customer to apply the tags `costcenter=115` and `stack=prod` when creating a multi\-volume snapshot set\. If users don't pass these specific tags, or if they don't specify tags at all, the request fails\.

```
{
   "Version":"2012-10-17",
   "Statement": [
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

The following policy allows customers to create a snapshot without having to specify tags\. The `CreateTags` action is evaluated only if tags are specified in the `CreateSnapshot` or `CreateSnapshots` request\. Tags can be omitted in the request\. If a tag is specified, the tag must be `purpose=test`\. No other tags are allowed in the request\.

```
{
   "Version":"2012-10-17",
   "Statement": [
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

The following policy allows customers to create multi\-volume snapshot sets without having to specify tags\. The `CreateTags` action is evaluated only if tags are specified in the `CreateSnapshot` or `CreateSnapshots` request\. Tags can be omitted in the request\. If a tag is specified, the tag must be `purpose=test`\. No other tags are allowed in the request\.

```
{
   "Version":"2012-10-17",
   "Statement": [
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
   "Statement": [
           {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshot",
         "Resource":"arn:aws:ec2:us-east-1:account-id:volume/*",
         "Condition":{
            "StringEquals":{
               "aws:ResourceTag/User":"${aws:username}"
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
   "Statement": [
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":"arn:aws:ec2:us-east-1:*:instance/*",
	},
      {
         "Effect":"Allow",
         "Action":"ec2:CreateSnapshots",
         "Resource":"arn:aws:ec2:us-east-1:account-id:volume/*",
         "Condition":{
            "StringEquals":{
               "aws:ResourceTag/User":"${aws:username}"
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
   "Statement": [
            {
         "Effect":"Allow",
         "Action":"ec2:DeleteSnapshot",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "aws:ResourceTag/User":"${aws:username}"
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
   "Statement": [
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
   "Statement": [
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
   "Statement": [
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
              "ec2:InstanceType": ["t2.*"]
            }
         }
      }
   ]
}
```

### Example: Copying snapshots<a name="iam-copy-snapshot"></a>

Resource\-level permissions specified for the *CopySnapshot* action apply to the new snapshot only\. They cannot be specified for the source snapshot\.

The following example policy allows principals to copy snapshots only if the new snapshot is created with tag key of `purpose` and a tag value of `production` \(`purpose=production`\)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCopySnapshotWithTags",
            "Effect": "Allow",
            "Action": "ec2:CopySnapshot",
            "Resource": "arn:aws:ec2:*:account-id:snapshot/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/purpose": "production"
                }
            }
        }
    ]
}
```

### Example: Modify permission settings for snapshots<a name="iam-modifying-snapshot-with-tags"></a>

The following policy allows modification of a snapshot only if the snapshot is tagged with `User:username`, where *username* is the customer's AWS account user name\. The request fails if this condition is not met\.

```
{
   "Version":"2012-10-17",
   "Statement": [
           {
         "Effect":"Allow",
         "Action":"ec2:ModifySnapshotAttribute",
         "Resource":"arn:aws:ec2:us-east-1::snapshot/*",
         "Condition":{
            "StringEquals":{
               "aws:ResourceTag/user-name":"${aws:username}"
            }
         }
      }
   ]
}
```

## Launch instances \(RunInstances\)<a name="iam-example-runinstances"></a>

The [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RunInstances.html) API action launches one or more On\-Demand Instances or one or more Spot Instances\. `RunInstances` requires an AMI and creates an instance\. Users can specify a key pair and security group in the request\. Launching into a VPC requires a subnet, and creates a network interface\. Launching from an Amazon EBS\-backed AMI creates a volume\. Therefore, the user must have permissions to use these Amazon EC2 resources\. You can create a policy statement that requires users to specify an optional parameter on `RunInstances`, or restricts users to particular values for a parameter\.

For more information about the resource\-level permissions that are required to launch an instance, see [Actions, resources, and condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html)\.

By default, users don't have permissions to describe, start, stop, or terminate the resulting instances\. One way to grant the users permission to manage the resulting instances is to create a specific tag for each instance, and then create a statement that enables them to manage instances with that tag\. For more information, see [Work with instances](#iam-example-instances)\.

**Topics**
+ [AMIs](#iam-example-runinstances-ami)
+ [Instance types](#iam-example-runinstances-instance-type)
+ [Subnets](#iam-example-runinstances-subnet)
+ [EBS volumes](#iam-example-runinstances-volumes)
+ [Tags](#iam-example-runinstances-tags)
+ [Tags in a launch template](#iam-example-tags-launch-template)
+ [Elastic GPUs](#iam-example-runinstances-egpu)
+ [Launch templates](#iam-example-runinstances-launch-templates)

### AMIs<a name="iam-example-runinstances-ami"></a>

The following policy allows users to launch instances using only the specified AMIs, `ami-9e1670f7` and `ami-45cf5c3c`\. The users can't launch an instance using other AMIs \(unless another statement grants the users permission to do so\)\.

```
{
   "Version": "2012-10-17",
   "Statement": [
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:region::image/ami-9e1670f7",
        "arn:aws:ec2:region::image/ami-45cf5c3c",
        "arn:aws:ec2:region:account-id:instance/*",
        "arn:aws:ec2:region:account-id:volume/*",
        "arn:aws:ec2:region:account-id:key-pair/*",
        "arn:aws:ec2:region:account-id:security-group/*",
        "arn:aws:ec2:region:account-id:subnet/*",
        "arn:aws:ec2:region:account-id:network-interface/*"
      ]
    }
   ]
}
```

Alternatively, the following policy allows users to launch instances from all AMIs owned by Amazon, or certain trusted and verified partners\. The `Condition` element of the first statement tests whether `ec2:Owner` is `amazon`\. The users can't launch an instance using other AMIs \(unless another statement grants the users permission to do so\)\.

```
{
   "Version": "2012-10-17",
   "Statement": [
         {
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
         "arn:aws:ec2:region:account-id:instance/*",
         "arn:aws:ec2:region:account-id:subnet/*",
         "arn:aws:ec2:region:account-id:volume/*",
         "arn:aws:ec2:region:account-id:network-interface/*",
         "arn:aws:ec2:region:account-id:key-pair/*",
         "arn:aws:ec2:region:account-id:security-group/*"
         ]
      }
   ]
}
```

### Instance types<a name="iam-example-runinstances-instance-type"></a>

The following policy allows users to launch instances using only the `t2.micro` or `t2.small` instance type, which you might do to control costs\. The users can't launch larger instances because the `Condition` element of the first statement tests whether `ec2:InstanceType` is either `t2.micro` or `t2.small`\. 

```
{
   "Version": "2012-10-17",
   "Statement": [
        {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region:account-id:instance/*"
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
         "arn:aws:ec2:region:account-id:subnet/*",
         "arn:aws:ec2:region:account-id:network-interface/*",
         "arn:aws:ec2:region:account-id:volume/*",
         "arn:aws:ec2:region:account-id:key-pair/*",
         "arn:aws:ec2:region:account-id:security-group/*"
         ]
      }
   ]
}
```

Alternatively, you can create a policy that denies users permissions to launch any instances except `t2.micro` and `t2.small` instance types\.

```
{
   "Version": "2012-10-17",
   "Statement": [
        { 
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region:account-id:instance/*"
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
         "arn:aws:ec2:region:account-id:network-interface/*",
         "arn:aws:ec2:region:account-id:instance/*",
         "arn:aws:ec2:region:account-id:subnet/*",
         "arn:aws:ec2:region:account-id:volume/*",
         "arn:aws:ec2:region:account-id:key-pair/*",
         "arn:aws:ec2:region:account-id:security-group/*"
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
   "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
        "arn:aws:ec2:region:account-id:subnet/subnet-12345678",
        "arn:aws:ec2:region:account-id:network-interface/*",
        "arn:aws:ec2:region:account-id:instance/*",
        "arn:aws:ec2:region:account-id:volume/*",
        "arn:aws:ec2:region::image/ami-*",
        "arn:aws:ec2:region:account-id:key-pair/*",
        "arn:aws:ec2:region:account-id:security-group/*"
      ]
    }
   ]
}
```

Alternatively, you could create a policy that denies users permissions to launch an instance into any other subnet\. The statement does this by denying permission to create a network interface, except where subnet `subnet-12345678` is specified\. This denial overrides any other policies that are created to allow launching instances into other subnets\.

```
{
   "Version": "2012-10-17",
   "Statement": [
         {
      "Effect": "Deny",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region:account-id:network-interface/*"
      ],
      "Condition": {
         "ArnNotEquals": {
            "ec2:Subnet": "arn:aws:ec2:region:account-id:subnet/subnet-12345678"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": [
         "arn:aws:ec2:region::image/ami-*",
         "arn:aws:ec2:region:account-id:network-interface/*",
         "arn:aws:ec2:region:account-id:instance/*",
         "arn:aws:ec2:region:account-id:subnet/*",
         "arn:aws:ec2:region:account-id:volume/*",
         "arn:aws:ec2:region:account-id:key-pair/*",
         "arn:aws:ec2:region:account-id:security-group/*"
         ]
      }
   ]
}
```

### EBS volumes<a name="iam-example-runinstances-volumes"></a>

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

**Tag instances on creation**

The following policy allows users to launch instances and tag the instances during creation\. For resource\-creating actions that apply tags, users must have permissions to use the `CreateTags` action\. The second statement uses the `ec2:CreateAction` condition key to allow users to create tags only in the context of `RunInstances`, and only for instances\. Users cannot tag existing resources, and users cannot tag volumes using the `RunInstances` request\. 

For more information, see [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)\.

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
      "Resource": "arn:aws:ec2:us-east-1:account-id:instance/*",
      "Condition": {
         "StringEquals": {
             "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

**Tag instances and volumes on creation with specific tags**

The following policy includes the `aws:RequestTag` condition key that requires users to tag any instances and volumes that are created by `RunInstances` with the tags `environment=production` and `purpose=webserver`\. If users don't pass these specific tags, or if they don't specify tags at all, the request fails\.

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
         "arn:aws:ec2:region:account-id:subnet/*",
         "arn:aws:ec2:region:account-id:network-interface/*",
         "arn:aws:ec2:region:account-id:security-group/*",
         "arn:aws:ec2:region:account-id:key-pair/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:RunInstances"
      ],
      "Resource": [
          "arn:aws:ec2:region:account-id:volume/*",
          "arn:aws:ec2:region:account-id:instance/*"
      ],
      "Condition": {
         "StringEquals": {
             "aws:RequestTag/environment": "production" ,
             "aws:RequestTag/purpose": "webserver"
          }
       }
    },
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateTags"
      ],
      "Resource": "arn:aws:ec2:region:account-id:*/*",
      "Condition": {
         "StringEquals": {
             "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

**Tag instances and volumes on creation with at least one specific tag**

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
         "arn:aws:ec2:region:account-id:subnet/*",
         "arn:aws:ec2:region:account-id:network-interface/*",
         "arn:aws:ec2:region:account-id:security-group/*",
         "arn:aws:ec2:region:account-id:key-pair/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
          "ec2:RunInstances"
      ],
      "Resource": [
          "arn:aws:ec2:region:account-id:volume/*",
          "arn:aws:ec2:region:account-id:instance/*"
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
      "Resource": "arn:aws:ec2:region:account-id:*/*",
      "Condition": {
          "StringEquals": {
              "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

**If instances are tagged on creation, they must be tagged with a specific tag**

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
      "Resource": "arn:aws:ec2:region:account-id:*/*",
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

To disallow anyone called tag on create for RunInstances



```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
            "Sid": "VisualEditor0",
            "Effect": "Deny",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }
    ]
}
```



Only allow specific tags for spot\-instances\-request\. Surprise inconsistency number 2 comes into play here\. Under normal circumstances, specifying no tags will result in Unauthenticated\. In the case of spot\-instances\-request, this policy will not be evaluated if there are no spot\-instances\-request tags, so a non\-tag Spot on Run request will succeed\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
            ]
        },
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:us-east-1:*:spot-instances-request/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/environment": "production"
                }
            }
        }
    ]
}
```

### Tags in a launch template<a name="iam-example-tags-launch-template"></a>

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
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account-id:launch-template/lt-09477bcd97b0d310e" 
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
      "Resource": "arn:aws:ec2:region:account-id:instance/*",
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

In the following policy, users can launch an instance and specify an elastic GPU to attach to the instance\. Users can launch instances in any Region, but they can only attach an elastic GPU during a launch in the `us-east-2` Region\. 

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
                "arn:aws:ec2:*:account-id:elastic-gpu/*"
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
                "arn:aws:ec2:*:account-id:network-interface/*",
                "arn:aws:ec2:*:account-id:instance/*",
                "arn:aws:ec2:*:account-id:subnet/*",
                "arn:aws:ec2:*:account-id:volume/*",
                "arn:aws:ec2:*:account-id:key-pair/*",
                "arn:aws:ec2:*:account-id:security-group/*"
            ]
        }
    ]
}
```

### Launch templates<a name="iam-example-runinstances-launch-templates"></a>

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
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account-id:launch-template/lt-09477bcd97b0d310e" 
          }
       }
    }
  ]
}
```

In this example, users can launch instances only if they use a launch template\. The policy uses the `ec2:IsLaunchTemplateResource` condition key to prevent users from overriding any pre\-existing ARNs in the launch template\.

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
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account-id:launch-template/*" 
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
      "NotResource": ["arn:aws:ec2:region:account-id:subnet/*",
                      "arn:aws:ec2:region:account-id:network-interface/*" ],
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account-id:launch-template/*" 
          }
       }
    },
   {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": ["arn:aws:ec2:region:account-id:subnet/*",
                   "arn:aws:ec2:region:account-id:network-interface/*" ],
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account-id:launch-template/*" 
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
      "NotResource": "arn:aws:ec2:region:account-id:launch-template/*",
      "Condition": {
         "ArnLike": {
             "ec2:LaunchTemplate": "arn:aws:ec2:region:account-id:launch-template/*" 
          },
         "Bool": {
             "ec2:IsLaunchTemplateResource": "true"
          }
       }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:region:account-id:launch-template/*",
      "Condition": {
       "StringEquals": {
           "aws:ResourceTag/Purpose": "Webservers" 
        }
       }
     }
  ]
}
```

## Work with Spot Instances<a name="iam-example-spot-instances"></a>

You can use the RunInstances action to create Spot Instance requests, and tag the Spot Instance requests on create\. The resource to specify for RunInstances is `spot-instances-request`\.

The `spot-instances-request` resource is evaluated in the IAM policy as follows:
+ If you don't tag a Spot Instance request on create, Amazon EC2 does not evaluate the `spot-instances-request` resource in the RunInstances statement\.
+ If you tag a Spot Instance request on create, Amazon EC2 evaluates the `spot-instances-request` resource in the RunInstances statement\.

Therefore, for the `spot-instances-request` resource, the following rules apply to the IAM policy:
+ If you use RunInstances to create a Spot Instance request and you don't intend to tag the Spot Instance request on create, you don’t need to explicitly allow the `spot-instances-request` resource; the call will succeed\.
+ If you use RunInstances to create a Spot Instance request and intend to tag the Spot Instance request on create, you must include the `spot-instances-request` resource in the RunInstances allow statement, otherwise the call will fail\.
+ If you use RunInstances to create a Spot Instance request and intend to tag the Spot Instance request on create, you must specify the `spot-instances-request` resource or `*` wildcard in the CreateTags allow statement, otherwise the call will fail\. 

You can request Spot Instances using RunInstances or RequestSpotInstances\. The following example IAM policies apply only when requesting Spot Instances using RunInstances\.

**Example: Request Spot Instances using RunInstances**

The following policy allows users to request Spot Instances by using the RunInstances action\. The `spot-instances-request` resource, which is created by RunInstances, requests Spot Instances\.

**Note**  
To use RunInstances to create Spot Instance requests, you can omit `spot-instances-request` from the `Resource` list if you do not intend to tag the Spot Instance requests on create\. This is because Amazon EC2 does not evaluate the `spot-instances-request` resource in the RunInstances statement if the Spot Instance request is not tagged on create\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
        }
    ]
}
```

**Warning**  
**NOT SUPPORTED – Example: Deny users permission to request Spot Instances using RunInstances**  
The following policy is not supported for the `spot-instances-request` resource\.  
The following policy is meant to give users the permission to launch On\-Demand Instances, but deny users the permission to request Spot Instances\. The `spot-instances-request` resource, which is created by RunInstances, is the resource that requests Spot Instances\. The second statement is meant to deny the RunInstances action for the `spot-instances-request` resource\. However, this condition is not supported because Amazon EC2 does not evaluate the `spot-instances-request` resource in the RunInstances statement if the Spot Instance request is not tagged on create\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
                "arn:aws:ec2:us-east-1:*:instance/*"
            ]
        },
        {
            "Sid": "DenySpotInstancesRequests - NOT SUPPORTED - DO NOT USE!",
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:us-east-1:*:spot-instances-request/*"
        }
    ]
}
```

**Example: Tag Spot Instance requests on create**

The following policy allows users to tag all resources that are created during instance launch\. The first statement allows RunInstances to create the listed resources\. The `spot-instances-request` resource, which is created by RunInstances, is the resource that requests Spot Instances\. The second statement provides a `*` wildcard to allow all resources to be tagged when they are created at instance launch\.

**Note**  
If you tag a Spot Instance request on create, Amazon EC2 evaluates the `spot-instances-request` resource in the RunInstances statement\. Therefore, you must explicitly allow the `spot-instances-request` resource for the RunInstances action, otherwise the call will fail\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
            "Sid": "TagResources",
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }
    ]
}
```

**Example: Deny tag on create for Spot Instance requests**

The following policy denies users the permission to tag the resources that are created during instance launch\.

The first statement allows RunInstances to create the listed resources\. The `spot-instances-request` resource, which is created by RunInstances, is the resource that requests Spot Instances\. The second statement provides a `*` wildcard to deny all resources being tagged when they are created at instance launch\. If `spot-instances-request` or any other resource is tagged on create, the RunInstances call will fail\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
            "Sid": "DenyTagResources",
            "Effect": "Deny",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }
    ]
}
```

**Warning**  
**NOT SUPPORTED – Example: Allow creating a Spot Instance request only if it is assigned a specific tag**  
The following policy is not supported for the `spot-instances-request` resource\.  
The following policy is meant to grant RunInstances the permission to create a Spot Instance request only if the request is tagged with a specific tag\.   
The first statement allows RunInstances to create the listed resources\.  
The second statement is meant to grant users the permission to create a Spot Instance request only if the request has the tag `environment=production`\. If this condition is applied to other resources created by RunInstances, specifying no tags results in an `Unauthenticated` error\. However, if no tags are specified for the Spot Instance request, Amazon EC2 does not evaluate the `spot-instances-request` resource in the RunInstances statement, which results in non\-tagged Spot Instance requests being created by RunInstances\.  
Note that specifying another tag other than `environment=production` results in an `Unauthenticated` error, because if a user tags a Spot Instance request, Amazon EC2 evaluates the `spot-instances-request` resource in the RunInstances statement\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
                "arn:aws:ec2:us-east-1:*:instance/*"
            ]
        },
        {
            "Sid": "RequestSpotInstancesOnlyIfTagIs_environment=production - NOT SUPPORTED - DO NOT USE!",
            "Effect": "Allow",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:us-east-1:*:spot-instances-request/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/environment": "production"
                }
            }
        },
        {
            "Sid": "TagResources",
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }

    ]
}
```

**Example: Deny creating a Spot Instance request if it is assigned a specific tag**

The following policy denies RunInstances the permission to create a Spot Instance request if the request is tagged with `environment=production`\. 

The first statement allows RunInstances to create the listed resources\.

The second statement denies users the permission to create a Spot Instance request if the request has the tag `environment=production`\. Specifying `environment=production` as a tag results in an `Unauthenticated` error\. Specifying other tags or specifying no tags will result in the creation of a Spot Instance request\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRun",
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
            "Sid": "DenySpotInstancesRequests",
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:us-east-1:*:spot-instances-request/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/environment": "production"
                }
            }
        },
        {
            "Sid": "TagResources",
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }
    ]
}
```

## Example: Work with Reserved Instances<a name="iam-example-reservedinstances"></a>

The following policy gives users permission to view, modify, and purchase Reserved Instances in your account\.

It is not possible to set resource\-level permissions for individual Reserved Instances\. This policy means that users have access to all the Reserved Instances in the account\.

The `Resource` element uses a \* wildcard to indicate that users can specify all resources with the action; in this case, they can list and modify all Reserved Instances in the account\. They can also purchase Reserved Instances using the account credentials\. The \* wildcard is also necessary in cases where the API action does not support resource\-level permissions\.

```
{
   "Version": "2012-10-17",
   "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeReservedInstances", 
        "ec2:ModifyReservedInstances",
        "ec2:PurchaseReservedInstancesOffering", 
        "ec2:DescribeAvailabilityZones",
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
   "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeReservedInstances", 
        "ec2:ModifyReservedInstances",
        "ec2:DescribeAvailabilityZones"
      ],
      "Resource": "*"
    }
  ]
}
```

## Example: Tag resources<a name="iam-example-taggingresources"></a>

The following policy allows users to use the `CreateTags` action to apply tags to an instance only if the tag contains the key`environment` and the value `production`\. No other tags are allowe and the user cannot tag any other resource types\.

```
{
    "Version": "2012-10-17",
    "Statement": [
              {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": "arn:aws:ec2:region:account-id:instance/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/environment": "production"
                }
            }
        }
    ]
}
```

The following policy allows users to tag any taggable resource that already has a tag with a key of `owner` and a value of the username\. In addition, users must specify a tag with a key of `anycompany:environment-type` and a value of either `test` or `prod` in the request\. Users can specify additional tags in the request\.

```
{
    "Version": "2012-10-17",
    "Statement": [
               {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateTags"
            ],
            "Resource": "arn:aws:ec2:region:account-id:*/*",
            "Condition": {
                "StringEquals": {
                    "aws:RequestTag/anycompany:environment-type": ["test","prod"],
                    "aws:ResourceTag/owner": "${aws:username}"
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
      "Resource": "arn:aws:ec2:us-east-1:account-id:volume/*",
      "Condition": {
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["environment","cost-center"]
        }
      }
    }
  ]
}
```

This policy allows users to delete only the `environment=prod` tag on any resource, and only if the resource is already tagged with a key of `owner` and a value of the username\. Users can't delete any other tags for a resource\.

```
{
  "Version": "2012-10-17",
  "Statement": [
      {
      "Effect": "Allow",
      "Action": [
        "ec2:DeleteTags"
      ],
      "Resource": "arn:aws:ec2:region:account-id:*/*",
      "Condition": {
        "StringEquals": {
          "aws:RequestTag/environment": "prod",
          "aws:ResourceTag/owner": "${aws:username}"
        },
        "ForAllValues:StringEquals": {
          "aws:TagKeys": ["environment"]
        }
      }
    }
  ]
}
```

## Example: Work with IAM roles<a name="iam-example-iam-roles"></a>

The following policy allows users to attach, replace, and detach an IAM role to instances that have the tag `department=test`\. Replacing or detaching an IAM role requires an association ID, therefore the policy also grants users permission to use the `ec2:DescribeIamInstanceProfileAssociations` action\. 

Users must have permission to use the `iam:PassRole` action in order to pass the role to the instance\. 

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
      "Resource": "arn:aws:ec2:us-east-1:account-id:instance/*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/department":"test"
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
      "Resource": "arn:aws:iam::account-id:role/DevTeam*"
    }
  ]
}
```

The following policy allows users to attach or replace an IAM role for any instance\. Users can only attach or replace IAM roles with names that begin with `TestRole-`\. For the `iam:PassRole` action, ensure that you specify the name of the IAM role and not the instance profile \(if the names are different\)\. For more information, see [Instance profiles](iam-roles-for-amazon-ec2.md#ec2-instance-profile)\.

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
            "Resource": "arn:aws:iam::account-id:role/TestRole-*"
        }
    ]
}
```

## Example: Work with route tables<a name="iam-example-route-tables"></a>

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
                "arn:aws:ec2:region:account-id:route-table/*"
            ],
            "Condition": {
                "StringEquals": {
                    "ec2:Vpc": "arn:aws:ec2:region:account-id:vpc/vpc-ec43eb89"
                }
            }
        }
    ]
}
```

## Example: Allow a specific instance to view resources in other AWS services<a name="iam-example-source-instance"></a>

The following is an example of a policy that you might attach to an IAM role\. The policy allows an instance to view resources in various AWS services\. It uses the `ec2:SourceInstanceARN` condition key to specify that the instance from which the request is made must be instance `i-093452212644b0dd6`\. If the same IAM role is associated with another instance, the other instance cannot perform any of these actions\.

The `ec2:SourceInstanceARN` key is an AWS global condition key, therefore it can be used for other service actions, not just Amazon EC2\.

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
                    "ec2:SourceInstanceARN": "arn:aws:ec2:region:account-id:instance/i-093452212644b0dd6"
                }
            }
        }
    ]
}
```

## Example: Work with launch templates<a name="iam-example-launch-templates"></a>

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
      "Resource": "arn:aws:ec2:region:account-id:launch-template/lt-09477bcd97b0d3abc"
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
      "Resource": "arn:aws:ec2:region:account-id:launch-template/*",
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/Purpose": "Testing"
        }
      }
    }
  ]
}
```

## Work with instance metadata<a name="iam-example-instance-metadata"></a>

The following policies ensure that users can only retrieve [instance metadata](ec2-instance-metadata.md) using Instance Metadata Service Version 2 \(IMDSv2\)\. You can combine the following four policies into one policy with four statements\. When combined as one policy, you can use the policy as a service control policy \(SCP\)\. It can work equally well as a *deny* policy that you apply to an existing IAM policy \(taking away and limiting existing permission\), or as an SCP that is applied globally across an account, an organizational unit \(OU\), or an entire organization\.

**Note**  
The following RunInstances metadata options policies must be used in conjunction with a policy that gives the principal permissions to launch an instance with RunInstances\. If the principal does not also have RunInstances permissions, it will not be able to launch an instance\. For more information, see the policies in [Work with instances](#iam-example-instances) and [Launch instances \(RunInstances\)](#iam-example-runinstances)\.

**Important**  
If you use Auto Scaling groups and you need to require the use of IMDSv2 on all new instances, your Auto Scaling groups must use *launch templates*\.  
When an Auto Scaling group uses a launch template, the `ec2:RunInstances` permissions of the IAM principal are checked when a new Auto Scaling group is created\. They are also checked when an existing Auto Scaling group is updated to use a new launch template or a new version of a launch template\.  
Restrictions on the use of IMDSv1 on IAM principals for `RunInstances` are only checked when an Auto Scaling group that is using a launch template, is created or updated\. For an Auto Scaling group that is configured to use the `Latest` or `Default` launch template, the permissions are not checked when a new version of the launch template is created\. For permissions to be checked, you must configure the Auto Scaling group to use a *specific version* of the launch template\.  
Disable the use of launch configurations for all accounts in your organization by using either service control policies \(SCPs\) or IAM permissions boundaries for new principals that are created\. For existing IAM principals with Auto Scaling group permissions, update their associated policies with this condition key\. To disable the use of launch configurations, create or modify the relevant SCP, permissions boundary, or IAM policy with the `"autoscaling:LaunchConfigurationName"` condition key with the value specified as `null`\.
For new launch templates, configure the instance metadata options in the launch template\. For existing launch templates, create a new version of the launch template and configure the instance metadata options in the new version\.
In the policy that gives any principal the permission to use a launch template, restrict association of `$latest` and `$default` by specifying `"autoscaling:LaunchTemplateVersionSpecified": "true"`\. By restricting the use to a specific version of a launch template, you can ensure that new instances will be launched using the version in which the instance metadata options are configured\. For more information, see [LaunchTemplateSpecification](https://docs.aws.amazon.com/autoscaling/ec2/APIReference/API_LaunchTemplateSpecification.html) in the *Amazon EC2 Auto Scaling API Reference*, specifically the `Version` parameter\.
For an Auto Scaling group that uses a launch configuration, replace the launch configuration with a launch template\. For more information, see [Replacing a Launch Configuration with a Launch Template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/replace-launch-config.html) in the *Amazon EC2 Auto Scaling User Guide*\.
For an Auto Scaling group that uses a launch template, make sure that it uses a new launch template with the instance metadata options configured, or uses a new version of the current launch template with the instance metadata options configured\. For more information, see [update\-auto\-scaling\-group ](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) in the *AWS CLI Command Reference*\.

**Topics**
+ [Require the use of IMDSv2](#iam-example-instance-metadata-requireIMDSv2)
+ [Specify maximum hop limit](#iam-example-instance-metadata-maxHopLimit)
+ [Limit who can modify the instance metadata options](#iam-example-instance-metadata-limit-modify-IMDS-options)
+ [Require role credentials to be retrieved from IMDSv2](#iam-example-instance-metadata-require-roles-to-use-IMDSv2-credentials)

### Require the use of IMDSv2<a name="iam-example-instance-metadata-requireIMDSv2"></a>

The following policy specifies that you can’t call the RunInstances API unless the instance is also opted in to require the use of IMDSv2 \(indicated by `"ec2:MetadataHttpTokens": "required"`\)\. If you do not specify that the instance requires IMDSv2, you get an `UnauthorizedOperation` error when you call the RunInstances API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
               {
            "Sid": "RequireImdsV2",
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:*:*:instance/*",
            "Condition": {
                "StringNotEquals": {
                    "ec2:MetadataHttpTokens": "required"
                }
            }
        }
    ]
}
```

### Specify maximum hop limit<a name="iam-example-instance-metadata-maxHopLimit"></a>

The following policy specifies that you can’t call the RunInstances API unless you also specify a hop limit, and the hop limit can’t be more than 3\. If you fail to do that, you get an `UnauthorizedOperation` error when you call the RunInstances API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
               {
            "Sid": "MaxImdsHopLimit",
            "Effect": "Deny",
            "Action": "ec2:RunInstances",
            "Resource": "arn:aws:ec2:*:*:instance/*",
            "Condition": {
                "NumericGreaterThan": {
                    "ec2:MetadataHttpPutResponseHopLimit": "3"
                }
            }
        }
    ]
}
```

### Limit who can modify the instance metadata options<a name="iam-example-instance-metadata-limit-modify-IMDS-options"></a>

The following policy permits only users with the role `ec2-imds-admins` to make changes to the instance metadata options\. If any principal other than the `ec2-imds-admins` role tries to call the ModifyInstanceMetadataOptions API, it will get an `UnauthorizedOperation` error\. This statement could be used to control the use of the ModifyInstanceMetadataOptions API; there are currently no fine\-grained access controls \(conditions\) for the ModifyInstanceMetadataOptions API\.

```
{
    "Version": "2012-10-17",
    "Statement": [
               {
            "Sid": "AllowOnlyImdsAdminsToModifySettings",
            "Effect": "Deny",
            "Action": "ec2:ModifyInstanceMetadataOptions",
            "Resource": "*",
            "Condition": {
                "StringNotLike": {
                    "aws:PrincipalARN": "arn:aws:iam::*:role/ec2-imds-admins"
                }
            }
        }
    ]
}
```

### Require role credentials to be retrieved from IMDSv2<a name="iam-example-instance-metadata-require-roles-to-use-IMDSv2-credentials"></a>

The following policy specifies that if this policy is applied to a role, and the role is assumed by the EC2 service and the resulting credentials are used to sign a request, then the request must be signed by EC2 role credentials retrieved from IMDSv2\. Otherwise, all of its API calls will get an `UnauthorizedOperation` error\. This statement/policy can be applied generally because, if the request is not signed by EC2 role credentials, it has no effect\.

```
{
    "Version": "2012-10-17",
    "Statement": [
               {
            "Sid": "RequireAllEc2RolesToUseV2",
            "Effect": "Deny",
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "NumericLessThan": {
                    "ec2:RoleDelivery": "2.0"
                }
            }
        }
    ]
}
```
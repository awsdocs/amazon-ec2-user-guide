# IAM service roles<a name="service-role"></a>

An AWS Identity and Access Management \(IAM\) role is similar to a user, in that it is an AWS identity with permissions policies that determine what the identity can and can't do in AWS\. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it\. A service role is a role that an AWS service assumes to perform actions on your behalf\. As a service that performs backup operations on your behalf, Amazon Data Lifecycle Manager requires that you pass it a role to assume when performing policy operations on your behalf\. For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\.

The role that you pass to Amazon Data Lifecycle Manager must have an IAM policy with the permissions that enable Amazon Data Lifecycle Manager to perform actions associated with policy operations, such as creating snapshots and AMIs, copying snapshots and AMIs, deleting snapshots, and deregistering AMIs\. Different permissions are required for each of the Amazon Data Lifecycle Manager policy types\. The role must also have Amazon Data Lifecycle Manager listed as a trusted entity, which enables Amazon Data Lifecycle Manager to assume the role\.

**Topics**
+ [Default service roles for Amazon Data Lifecycle Manager](#default-service-roles)
+ [Custom service roles for Amazon Data Lifecycle Manager](#custom-role)

## Default service roles for Amazon Data Lifecycle Manager<a name="default-service-roles"></a>

Amazon Data Lifecycle Manager uses the following default service roles:
+ **AWSDataLifecycleManagerDefaultRole**—default role for managing snapshots\. It trusts only the `dlm.amazonaws.com` service to assume the role and it allows Amazon Data Lifecycle Manager to perform the actions required by snapshot and cross\-account snapshot copy policies on your behalf\. This role uses the ` AWSDataLifecycleManagerServiceRole` AWS managed policy\.
+ **AWSDataLifecycleManagerDefaultRoleForAMIManagement**—default role for managing AMIs\. It trusts only the `dlm.amazonaws.com` service to assume the role and it allows Amazon Data Lifecycle Manager to perform the actions required by EBS\-backed AMI policies on your behalf\. This role uses the `AWSDataLifecycleManagerServiceRoleForAMIManagement` AWS managed policy\.

If you are using the Amazon Data Lifecycle Manager console, Amazon Data Lifecycle Manager automatically creates the ** AWSDataLifecycleManagerDefaultRole** service role the first time you create a snapshot or cross\-account snapshot copy policy, and it automatically creates the ** AWSDataLifecycleManagerDefaultRoleForAMIManagement** service role the first time you create an EBS\-backed AMI policy\.

If you are not using the console, you can manually create the service roles using the [create\-default\-role](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-default-role.html) command\. For `--resource-type`, specify `snapshot` to create AWSDataLifecycleManagerDefaultRole, or `image` to create AWSDataLifecycleManagerDefaultRoleForAMIManagement\.

```
$ aws dlm create-default-role --resource-type snapshot|image
```

If you delete the default service roles, and then need to create them again, you can use the same process to recreate them in your account\.

## Custom service roles for Amazon Data Lifecycle Manager<a name="custom-role"></a>

As an alternative to using the default service roles, you can create custom IAM roles with the required permissions and then select them when you create a lifecycle policy\. 

**To create a custom IAM role**

1. Create roles with the following permissions\.
   + Permissions required for managing snapshot lifecycle policies

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Effect": "Allow",
                 "Action": [
                     "ec2:CreateSnapshot",
                     "ec2:CreateSnapshots",
                     "ec2:DeleteSnapshot",
                     "ec2:DescribeInstances",
                     "ec2:DescribeVolumes",
                     "ec2:DescribeSnapshots",
                     "ec2:EnableFastSnapshotRestores",
                     "ec2:DescribeFastSnapshotRestores",
                     "ec2:DisableFastSnapshotRestores",
                     "ec2:CopySnapshot",
                     "ec2:ModifySnapshotAttribute",
                     "ec2:DescribeSnapshotAttribute"
                 ],
                 "Resource": "*"
             },
             {
                 "Effect": "Allow",
                 "Action": [
                     "ec2:CreateTags"
                 ],
                 "Resource": "arn:aws:ec2:*::snapshot/*"
             },
             {
                 "Effect": "Allow",
                 "Action": [
                     "events:PutRule",
                     "events:DeleteRule",
                     "events:DescribeRule",
                     "events:EnableRule",
                     "events:DisableRule",
                     "events:ListTargetsByRule",
                     "events:PutTargets",
                     "events:RemoveTargets"
                 ],
                 "Resource": "arn:aws:events:*:*:rule/AwsDataLifecycleRule.managed-cwe.*"
             }
         ]
     }
     ```
   + Permissions required for managing AMI lifecycle policies

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
         {
             "Effect": "Allow",
             "Action": "ec2:CreateTags",
             "Resource": [
                 "arn:aws:ec2:*::snapshot/*",
                 "arn:aws:ec2:*::image/*"
             ]
         },
         {
             "Effect": "Allow",
             "Action": [
                 "ec2:DescribeImages",
                 "ec2:DescribeInstances",
                 "ec2:DescribeImageAttribute",
                 "ec2:DescribeVolumes",
                 "ec2:DescribeSnapshots"
             ],
             "Resource": "*"
         },
         {
             "Effect": "Allow",
             "Action": "ec2:DeleteSnapshot",
             "Resource": "arn:aws:ec2:*::snapshot/*"
         },
         {
             "Effect": "Allow",
             "Action": [
             "ec2:ResetImageAttribute",
             "ec2:DeregisterImage",
             "ec2:CreateImage",
             "ec2:CopyImage",
             "ec2:ModifyImageAttribute"
         ],
         "Resource": "*"
         }]
     }
     ```

   For more information, see [ Creating a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

1. Add a trust relationship to the roles\.

   1. In the IAM console, choose **Roles**\.

   1. Select the roles that you created, and then choose **Trust relationships**\.

   1. Choose **Edit Trust Relationship**, add the following policy, and then choose **Update Trust Policy**\.

      ```
      { 
          "Version": "2012-10-17",
          "Statement": [ 
          { 
              "Effect": "Allow", 
              "Principal": { 
              "Service": "dlm.amazonaws.com"
          }, 
              "Action": "sts:AssumeRole" 
          } ] 
      }
      ```
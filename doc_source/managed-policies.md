# AWS managed policies<a name="managed-policies"></a>

An AWS managed policy is a standalone policy that is created and administered by AWS\. AWS managed policies are designed to provide permissions for many common use cases\. AWS managed policies make it more efficient for you to assign appropriate permissions to users, groups, and roles, than if you had to write the policies yourself\.

However, you can't change the permissions defined in AWS managed policies\. AWS occasionally updates the permissions defined in an AWS managed policy\. When this occurs, the update affects all principal entities \(users, groups, and roles\) that the policy is attached to\.

Amazon Data Lifecycle Manager provides two AWS managed policies for common use cases\. These policies make it more efficient to define the appropriate permissions and control access to your resources\. The AWS managed policies provided by Amazon Data Lifecycle Manager are designed to be attached to roles that you pass to Amazon Data Lifecycle Manager\.

The following are the AWS managed policies that Amazon Data Lifecycle Manager provides\. You can also find these AWS managed policies in the **Policies** section of the IAM console\.

------
#### [ AWSDataLifecycleManagerServiceRole ]

The **AWSDataLifecycleManagerServiceRole** policy provides appropriate permissions to Amazon Data Lifecycle Manager to create and manage Amazon EBS snapshot policies and cross\-account copy event policies\.

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

------
#### [ AWSDataLifecycleManagerServiceRoleForAMIManagement ]

The **AWSDataLifecycleManagerServiceRoleForAMIManagement** policy provides appropriate permissions to Amazon Data Lifecycle Manager to create and manage Amazon EBS\-backed AMI policies\.

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
                "ec2:DescribeSnapshots",
                "ec2:EnableImageDeprecation",
                "ec2:DisableImageDeprecation"
                
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
        }
    ]
}
```

------

## AWS managed policy updates<a name="policy-update"></a>

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

The following table provides details about updates to AWS managed policies for Amazon Data Lifecycle Manager since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the [Document history](DocumentHistory.md)\.


| Change | Description | Date | 
| --- | --- | --- | 
| AWSDataLifecycleManagerServiceRoleForAMIManagement — Added permissions to support AMI deprecation\. | Amazon Data Lifecycle Manager added the ec2:EnableImageDeprecation and ec2:DisableImageDeprecation actions to grant EBS\-backed AMI policies permission to enable and disable AMI deprecation\. | August 23, 2021 | 
| Amazon Data Lifecycle Manager started tracking changes | Amazon Data Lifecycle Manager started tracking changes for its AWS managed policies\. | August 23, 2021 | 
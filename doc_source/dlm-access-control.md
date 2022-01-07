# Permissions for IAM users<a name="dlm-access-control"></a>

An IAM user must have the following permissions to use Amazon Data Lifecycle Manager\.

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

For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.
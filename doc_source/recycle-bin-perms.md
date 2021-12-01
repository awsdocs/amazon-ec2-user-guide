# Required permissions<a name="recycle-bin-perms"></a>

By default, IAM users don't have permission to work with Recycle Bin, retention rules, or with snapshots that are in the Recycle Bin\. To allow IAM users to work with these resources, you must create IAM policies that grant permission to use specific resources and API actions\. You then attach those policies to the IAM users or the groups that require those permissions\.

**Topics**
+ [Permissions for working with Recycle Bin](#rule-perms)
+ [Permissions for working with snapshots in the Recycle Bin](#snap-perms)

## Permissions for working with Recycle Bin<a name="rule-perms"></a>

IAM users must have the following permissions to work with Recycle Bin\.
+ `rbin:CreateRule`
+ `rbin:UpdateRule`
+ `rbin:GetRule`
+ `rbin:ListRules`
+ `rbin:DeleteRule`
+ `rbin:TagResource`
+ `rbin:UntagResource`
+ `rbin:ListTagsForResource`

**Note**  
Console users additionally require the `tag:GetResources` permission\. The example policy below includes this permission\. If it is not needed, you can remove the permission from the policy\.

The following is an example IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Action": [
            "rbin:CreateRule",
            "rbin:UpdateRule",
            "rbin:GetRule",
            "rbin:ListRules",
            "rbin:DeleteRule",
            "rbin:TagResource",
            "rbin:UntagResource",
            "rbin:ListTagsForResource",
            "tag:GetResources"
        ],
        "Resource": "*"
    }]
}
```

## Permissions for working with snapshots in the Recycle Bin<a name="snap-perms"></a>

IAM users must have the following permissions to work with snapshots that are in the Recycle Bin\.
+ `ec2:ListSnapshotsInRecycleBin`
+ `ec2:RestoreSnapshotFromRecycleBin`
+ `ec2:CreateTags`
+ `ec2:DeleteTags`

**Note**  
Console users additionally require the `ec2:DescribeTags` permission\. The example policy below includes this permission\. If it is not needed, you can remove the permission from the policy\.

The following is an example IAM policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
            "ec2:ListSnapshotsInRecycleBin", 
            "ec2:RestoreSnapshotFromRecycleBin"
        ],
        "Resource": "*"
      },
      {
        "Effect": "Allow",
        "Action": [
            "ec2:CreateTags",
            "ec2:DeleteTags",
            "ec2:DescribeTags"
        ],
        "Resource": "arn:aws:ec2:Region:account-id:snapshot/*"
      },
    
    ]
}
```
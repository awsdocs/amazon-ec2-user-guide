# Required IAM permissions<a name="recycle-bin-perms"></a>

By default, AWS Identity and Access Management \(IAM\) users don't have permission to work with Recycle Bin, retention rules, or with resources that are in the Recycle Bin\. To allow IAM users to work with these resources, you must create IAM policies that grant permission to use specific resources and API actions\. You then attach those policies to the IAM users or the groups that require those permissions\.

**Topics**
+ [Permissions for working with Recycle Bin and retention rules](#rule-perms)
+ [Permissions for working with resources in the Recycle Bin](#resource-perms)
+ [Condition keys for Recycle Bin](rbin-condition-keys.md)

## Permissions for working with Recycle Bin and retention rules<a name="rule-perms"></a>

To work with Recycle Bin and retention rules, IAM users need the following permissions\.
+ `rbin:CreateRule`
+ `rbin:UpdateRule`
+ `rbin:GetRule`
+ `rbin:ListRules`
+ `rbin:DeleteRule`
+ `rbin:TagResource`
+ `rbin:UntagResource`
+ `rbin:ListTagsForResource`
+ `rbin:LockRule`
+ `rbin:UnlockRule`

To use the Recycle Bin console, IAM users need the `tag:GetResources` permission\.

The following is an example IAM policy that includes the `tag:GetResources` permission for console users\. If some permissions are not needed, you can remove them from the policy\.

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
            "rbin:LockRule",
            "rbin:UnlockRule",
            "tag:GetResources"
        ],
        "Resource": "*"
    }]
}
```

## Permissions for working with resources in the Recycle Bin<a name="resource-perms"></a>

For more information about the IAM permissions needed to work with resources in the Recycle Bin, see the following:
+ [Permissions for working with snapshots in the Recycle Bin](recycle-bin-working-with-snaps.md#snap-perms)
+ [Permissions for working with AMIs in the Recycle Bin](recycle-bin-working-with-amis.md#ami-perms)
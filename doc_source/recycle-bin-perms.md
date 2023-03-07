# Required IAM permissions<a name="recycle-bin-perms"></a>

By default, users don't have permission to work with Recycle Bin, retention rules, or with resources that are in the Recycle Bin\. To allow users to work with these resources, you must create IAM policies that grant permission to use specific resources and API actions\. Once the policies are created, you must add permissions to your users, groups, or roles\.

**Topics**
+ [Permissions for working with Recycle Bin and retention rules](#rule-perms)
+ [Permissions for working with resources in the Recycle Bin](#resource-perms)
+ [Condition keys for Recycle Bin](rbin-condition-keys.md)

## Permissions for working with Recycle Bin and retention rules<a name="rule-perms"></a>

To work with Recycle Bin and retention rules, users need the following permissions\.
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

To use the Recycle Bin console, users need the `tag:GetResources` permission\.

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

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

## Permissions for working with resources in the Recycle Bin<a name="resource-perms"></a>

For more information about the IAM permissions needed to work with resources in the Recycle Bin, see the following:
+ [Permissions for working with snapshots in the Recycle Bin](recycle-bin-working-with-snaps.md#snap-perms)
+ [Permissions for working with AMIs in the Recycle Bin](recycle-bin-working-with-amis.md#ami-perms)
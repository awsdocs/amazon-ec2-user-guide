# Condition keys for Recycle Bin<a name="rbin-condition-keys"></a>

Recycle Bin defines the following condition keys that you can use in the `Condition` element of an IAM policy to control the conditions under which the policy statement applies\. For more information, see [ IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

**Topics**
+ [`rbin:Request/ResourceType` condition key](#resource-type-parameter)
+ [`rbin:Attribute/ResourceType` condition key](#resource-type-attribute)

## `rbin:Request/ResourceType` condition key<a name="resource-type-parameter"></a>

The `rbin:Request/ResourceType` condition key can be used to filter access on [ CreateRule](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_CreateRule.html) and [ ListRules](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_ListRules.html) requests based on the value specified for the `ResourceType` request parameter\.

**Example 1 \- CreateRule**  
The following sample IAM policy allows IAM principals to make **CreateRule** requests only if the value specified for the `ResourceType` request parameter is `EBS_SNAPSHOT` or `EC2_IMAGE`\. This allows the principal to create new retention rules for snapshots and AMIs only\.

```
{
    "Version" : "2012-10-17",
    "Statement" : [
        {
            "Effect" : "Allow",
            "Action" :[
                "rbin:CreateRule"
            ],
            "Resource" : "*",
            "Condition" : {
                "StringEquals" : {
                    "rbin:Request/ResourceType" : ["EBS_SNAPSHOT", "EC2_IMAGE"]
                }
            }
        }
    ]
}
```

**Example 2 \- ListRules**  
The following sample IAM policy allows IAM principals to make **ListRules** requests only if the value specified for the `ResourceType` request parameter is `EBS_SNAPSHOT`\. This allows the principal to list retention rules for snapshots only, and it prevents them from listing retention rules for any other resource type\.

```
{
    "Version" : "2012-10-17",
    "Statement" : [
        {
            "Effect" : "Allow",
            "Action" :[
                "rbin:ListRules"
            ],
            "Resource" : "*",
            "Condition" : {
                "StringEquals" : {
                    "rbin:Request/ResourceType" : "EBS_SNAPSHOT"
                }
            }
        }
    ]
}
```

## `rbin:Attribute/ResourceType` condition key<a name="resource-type-attribute"></a>

The `rbin:Attribute/ResourceType` condition key can be used to filter access on [DeleteRule](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_DeleteRule.html), [GetRule](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_GetRule.html), [UpdateRule](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_UpdateRule.html), [LockRule](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_LockRule.html), [UnlockRule](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_UnlockRule.html), [TagResource](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_TagResource.html), [UntagResource](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_UntagResource.html), and [ ListTagsForResource](https://docs.aws.amazon.com/recyclebin/latest/APIReference/API_ListTagsForResource.html) requests based on the value of the retention rule's `ResourceType` attribute\.

**Example 1 \- UpdateRule**  
The following sample IAM policy allows IAM principals to make **UpdateRule** requests only if the `ResourceType` attribute of the requested retention rule is `EBS_SNAPSHOT` or `EC2_IMAGE`\. This allows the principal to update retention rules for snapshots and AMIs only\.

```
{
    "Version" : "2012-10-17",
    "Statement" : [
        {
            "Effect" : "Allow",
            "Action" :[
                "rbin:UpdateRule"
            ],
            "Resource" : "*",
            "Condition" : {
                "StringEquals" : {
                    "rbin:Attribute/ResourceType" : ["EBS_SNAPSHOT", "EC2_IMAGE"]
                }
            }
        }
    ]
}
```

**Example 2 \- DeleteRule**  
The following sample IAM policy allows IAM principals to make **DeleteRule** requests only if the `ResourceType` attribute of the requested retention rule is `EBS_SNAPSHOT`\. This allows the principal to delete retention rules for snapshots only\.

```
{
    "Version" : "2012-10-17",
    "Statement" : [
        {
            "Effect" : "Allow",
            "Action" :[
                "rbin:DeleteRule"
            ],
            "Resource" : "*",
            "Condition" : {
                "StringEquals" : {
                    "rbin:Attribute/ResourceType" : "EBS_SNAPSHOT"
                }
            }
        }
    ]
}
```
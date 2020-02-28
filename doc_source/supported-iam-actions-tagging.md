# Granting Permission to Tag Resources During Creation<a name="supported-iam-actions-tagging"></a>

Some resource\-creating Amazon EC2 API actions enable you to specify tags when you create the resource\. For more information, see [Tagging Your Resources](Using_Tags.md#tag-resources)\.

To enable users to tag resources on creation, they must have permissions to use the action that creates the resource \(for example, `ec2:RunInstances` or `ec2:CreateVolume`\)\. If tags are specified in the resource\-creating action, Amazon performs additional authorization on the `ec2:CreateTags` action to verify if users have permissions to create tags\. Therefore, users must also have explicit permissions to use the `ec2:CreateTags` action\. 

For the `ec2:CreateTags` action, you can use the `ec2:CreateAction` condition key to restrict tagging permissions to the resource\-creating actions only\. For example, the following policy allows users to launch instances and apply any tags to instances and volumes during launch\. Users are not permitted to tag any existing resources \(they cannot call the `ec2:CreateTags` action directly\)\.

```
{
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
             "ec2:CreateAction" : "RunInstances"
          }
       }
    }
  ]
}
```

Similarly, the following policy allows users to create volumes and apply any tags to the volumes during volume creation\. Users are not permitted to tag any existing resources \(they cannot call the `ec2:CreateTags` action directly\)\.

```
{
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
         "ec2:CreateVolume"
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
             "ec2:CreateAction" : "CreateVolume"
          }
       }
    }
  ]
}
```

The `ec2:CreateTags` action is only evaluated if tags are applied during the resource\-creating action\. Therefore, a user that has permissions to create a resource \(assuming there are no tagging conditions\) does not require permissions to use the `ec2:CreateTags` action if no tags are specified in the request\. However, if the user attempts to create a resource with tags, the request fails if the user does not have permissions to use the `ec2:CreateTags` action\.

The `ec2:CreateTags` action is also evaluated if tags are provided in a launch template and the launch template is specified in the `ec2:RunInstances` action\. For an example policy, see [Tags in a Launch Template](ExamplePolicies_EC2.md#iam-example-tags-launch-template)\.

You can control the tag keys and values that are applied to resources by using the following condition keys:
+ `aws:RequestTag`: To indicate that a particular tag key or tag key and value must be present in a request\. Other tags can also be specified in the request\.
  + Use with the `StringEquals` condition operator to enforce a specific tag key and value combination, for example, to enforce the tag `cost-center`=`cc123`:

    ```
    "StringEquals": { "aws:RequestTag/cost-center": "cc123" }
    ```
  + Use with the `StringLike` condition operator to enforce a specific tag key in the request; for example, to enforce the tag key `purpose`:

    ```
    "StringLike": { "aws:RequestTag/purpose": "*" }
    ```
+ `aws:TagKeys`: To enforce the tag keys that are used in the request\.
  + Use with the `ForAllValues` modifier to enforce specific tag keys if they are provided in the request \(if tags are specified in the request, only specific tag keys are allowed; no other tags are allowed\)\. For example, the tag keys `environment` or `cost-center` are allowed:

    ```
    "ForAllValues:StringEquals": { "aws:TagKeys": ["environment","cost-center"] }
    ```
  + Use with the `ForAnyValue` modifier to enforce the presence of at least one of the specified tag keys in the request\. For example, at least one of the tag keys `environment` or `webserver` must be present in the request:

    ```
    "ForAnyValue:StringEquals": { "aws:TagKeys": ["environment","webserver"] }
    ```

These condition keys can be applied to resource\-creating actions that support tagging, as well as the `ec2:CreateTags` and `ec2:DeleteTags` actions\. 

To force users to specify tags when they create a resource, you must use the `aws:RequestTag` condition key or the `aws:TagKeys` condition key with the `ForAnyValue` modifier on the resource\-creating action\. The `ec2:CreateTags` action is not evaluated if a user does not specify tags for the resource\-creating action\.

For conditions, the condition key is not case\-sensitive and the condition value is case\-sensitive\. Therefore, to enforce the case\-sensitivity of a tag key, use the `aws:TagKeys` condition key, where the tag key is specified as a value in the condition\.

For more information about multi\-value conditions, see [Creating a Condition That Tests Multiple Key Values](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) in the *IAM User Guide*\. For example IAM policies, see [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\.
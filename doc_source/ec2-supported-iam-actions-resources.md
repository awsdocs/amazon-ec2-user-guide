# Supported Resource\-Level Permissions for Amazon EC2 API Actions<a name="ec2-supported-iam-actions-resources"></a>

*Resource\-level permissions* refers to the ability to specify which resources users are allowed to perform actions on\. Amazon EC2 has partial support for resource\-level permissions\. This means that for certain Amazon EC2 actions, you can control when users are allowed to use those actions based on conditions that have to be fulfilled, or specific resources that users are allowed to use\. For example, you can grant users permissions to launch instances, but only of a specific type, and only using a specific AMI\.

The following table describes the Amazon EC2 API actions that currently support resource\-level permissions, as well as the supported resources \(and their ARNs\) and condition keys for each action\. When specifying an ARN, you can use the \* wildcard in your paths; for example, when you cannot or do not want to specify exact resource IDs\. For examples of using wildcards, see [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\.

**Important**  
If an Amazon EC2 API action is not listed in this table, then it does not support resource\-level permissions\. If an Amazon EC2 API action does not support resource\-level permissions, you can grant users permissions to use the action, but you have to specify a \* for the resource element of your policy statement\. For an example, see [1: Read\-Only Access](ExamplePolicies_EC2.md#iam-example-read-only)\. For a list of Amazon EC2 API actions that currently do not support resource\-level permissions, see [Unsupported Resource\-Level Permissions](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ec2-api-permissions.html#ec2-api-unsupported-resource-permissions) in the *Amazon EC2 API Reference*\.   
All Amazon EC2 actions support the `ec2:Region` condition key\. For an example, see [2: Restricting Access to a Specific Region](ExamplePolicies_EC2.md#iam-example-region)\.

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-iam-actions-resources.html)

## Resource\-Level Permissions for RunInstances<a name="supported-iam-actions-runinstances"></a>

The [RunInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RunInstances.html) API action launches one or more instances, and creates and uses a number of Amazon EC2 resources\. The action requires an AMI and creates an instance; and the instance must be associated with a security group\. Launching into a VPC requires a subnet, and creates a network interface\. Launching from an Amazon EBS\-backed AMI creates a volume\. The user must have permissions to use these resources, so they must be specified in the `Resource` element of any policy that uses resource\-level permissions for the `ec2:RunInstances` action\. If you don't intend to use resource\-level permissions with the `ec2:RunInstances` action, you can specify the \* wildcard in the `Resource` element of your statement instead of individual ARNs\.

If you are using resource\-level permissions, the following table describes the minimum resources required to use the `ec2:RunInstances` action\. 

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-iam-actions-resources.html)

We recommend that you also specify the key pair resource in your policy — even though it's not required to launch an instance, you can't connect to your instance without a key pair\. For examples of using resource\-level permissions with the `ec2:RunInstances` action, see [6: Launching Instances \(RunInstances\)](ExamplePolicies_EC2.md#iam-example-runinstances)\.

For additional information about resource\-level permissions in Amazon EC2, see the following AWS Security Blog post: [Demystifying EC2 Resource\-Level Permissions](https://aws.amazon.com/blogs/security/demystifying-ec2-resource-level-permissions/)\.

### Resource\-Level Permissions for RunInstances and Launch Templates<a name="supported-iam-actions-runinstances-launch-templates"></a>

You can create a [launch template](ec2-launch-templates.md) that contains the parameters to launch an instance\. When users use the `ec2:RunInstances` action, they can specify the launch template to use to launch instances\. You can apply resource\-level permissions for the launch template resource for the `ec2:RunInstances` action\. For example, you can specify that users can only launch instances using a launch template, and that they must use a specific launch template\. You can also control the parameters that users can or cannot override in the launch template\. This enables you to manage the parameters for launching an instance in a launch template rather than an IAM policy\. For example policies, see [Launch Templates](ExamplePolicies_EC2.md#iam-example-runinstances-launch-templates)\.

## Resource\-Level Permissions for Tagging<a name="supported-iam-actions-tagging"></a>

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

The `ec2:CreateTags` action is also evaluated if tags are provided in a launch template and the launch template is specified in the `ec2:RunInstances` action\. For an example policy, see [Applying Tags in a Launch Template](ExamplePolicies_EC2.md#iam-example-tags-launch-template)\.

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

For more information about multi\-value conditions, see [Creating a Condition That Tests Multiple Key Values](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) in the *IAM User Guide*\. For example IAM policies, see [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\.
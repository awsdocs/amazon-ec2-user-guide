# Policy structure<a name="iam-policy-structure"></a>

The following topics explain the structure of an IAM policy\.

**Topics**
+ [Policy syntax](#policy-syntax)
+ [Actions for Amazon EC2](#UsingWithEC2_Actions)
+ [Supported resource\-level permissions for Amazon EC2 API actions](#ec2-supported-iam-actions-resources)
+ [Amazon Resource Names \(ARNs\) for Amazon EC2](#EC2_ARN_Format)
+ [Condition keys for Amazon EC2](#amazon-ec2-keys)
+ [Check that users have the required permissions](#check-required-permissions)

## Policy syntax<a name="policy-syntax"></a>

An IAM policy is a JSON document that consists of one or more statements\. Each statement is structured as follows\.

```
{
  "Statement":[{
    "Effect":"effect",
    "Action":"action",
    "Resource":"arn",
    "Condition":{
      "condition":{
        "key":"value"
        }
      }
    }
  ]
}
```

There are various elements that make up a statement:
+ **Effect:** The *effect* can be `Allow` or `Deny`\. By default, IAM users don't have permission to use resources and API actions, so all requests are denied\. An explicit allow overrides the default\. An explicit deny overrides any allows\.
+ **Action**: The *action* is the specific API action for which you are granting or denying permission\. To learn about specifying *action*, see [Actions for Amazon EC2](#UsingWithEC2_Actions)\. 
+ **Resource**: The resource that's affected by the action\. Some Amazon EC2 API actions allow you to include specific resources in your policy that can be created or modified by the action\. You specify a resource using an Amazon Resource Name \(ARN\) or using the wildcard \(\*\) to indicate that the statement applies to all resources\. For more information, see [Supported resource\-level permissions for Amazon EC2 API actions](#ec2-supported-iam-actions-resources)\. 
+ **Condition**: Conditions are optional\. They can be used to control when your policy is in effect\. For more information about specifying conditions for Amazon EC2, see [Condition keys for Amazon EC2](#amazon-ec2-keys)\.

For more information about policy requirements, see the [IAM JSON policy reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\. For example IAM policy statements for Amazon EC2, see [Example policies for working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\. 

## Actions for Amazon EC2<a name="UsingWithEC2_Actions"></a>

In an IAM policy statement, you can specify any API action from any service that supports IAM\. For Amazon EC2, use the following prefix with the name of the API action: `ec2:`\. For example: `ec2:RunInstances` and `ec2:CreateImage`\.

To specify multiple actions in a single statement, separate them with commas as follows:

```
"Action": ["ec2:action1", "ec2:action2"]
```

You can also specify multiple actions using wildcards\. For example, you can specify all actions whose name begins with the word "Describe" as follows:

```
"Action": "ec2:Describe*"
```

**Note**  
Currently, the Amazon EC2 Describe\* API actions do not support resource\-level permissions\. For more information about resource\-level permissions for Amazon EC2, see [IAM policies for Amazon EC2](iam-policies-for-amazon-ec2.md)\.

To specify all Amazon EC2 API actions, use the \* wildcard as follows:

```
"Action": "ec2:*"
```

For a list of Amazon EC2 actions, see [Actions defined by Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html#amazonec2-actions-as-permissions) in the *Service Authorization Reference*\.

## Supported resource\-level permissions for Amazon EC2 API actions<a name="ec2-supported-iam-actions-resources"></a>

*Resource\-level permissions* refers to the ability to specify which resources users are allowed to perform actions on\. Amazon EC2 has partial support for resource\-level permissions\. This means that for certain Amazon EC2 actions, you can control when users are allowed to use those actions based on conditions that have to be fulfilled, or specific resources that users are allowed to use\. For example, you can grant users permissions to launch instances, but only of a specific type, and only using a specific AMI\.

To specify a resource in an IAM policy statement, use its Amazon Resource Name \(ARN\)\. For more information about specifying the ARN value, see [Amazon Resource Names \(ARNs\) for Amazon EC2](#EC2_ARN_Format)\. If an API action does not support individual ARNs, you must use a wildcard \(\*\) to specify that all resources can be affected by the action\. 

To see tables that identify which Amazon EC2 API actions support resource\-level permissions, and the ARNs and condition keys that you can use in a policy, see [Actions, resources, and condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html)\.

Keep in mind that you can apply tag\-based resource\-level permissions in the IAM policies you use for Amazon EC2 API actions\. This gives you better control over which resources a user can create, modify, or use\. For more information, see [Grant permission to tag resources during creation](supported-iam-actions-tagging.md)\. 

## Amazon Resource Names \(ARNs\) for Amazon EC2<a name="EC2_ARN_Format"></a>

Each IAM policy statement applies to the resources that you specify using their ARNs\. 

An ARN has the following general syntax:

```
arn:aws:[service]:[region]:[account-id]:resourceType/resourcePath
```

*service*  
The service \(for example, `ec2`\)\.

*region*  
The Region for the resource \(for example, `us-east-1`\)\.

*account\-id*  
The AWS account ID, with no hyphens \(for example, `123456789012`\)\.

*resourceType*  
The type of resource \(for example, `instance`\)\.

*resourcePath*  
A path that identifies the resource\. You can use the \* wildcard in your paths\.

For example, you can indicate a specific instance \(`i-1234567890abcdef0`\) in your statement using its ARN as follows\. 

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:instance/i-1234567890abcdef0"
```

You can specify all instances that belong to a specific account by using the \* wildcard as follows\.

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*"
```

You can also specify all Amazon EC2 resources that belong to a specific account by using the \* wildcard as follows\.

```
"Resource": "arn:aws:ec2:us-east-1:123456789012:*"
```

To specify all resources, or if a specific API action does not support ARNs, use the \* wildcard in the `Resource` element as follows\.

```
"Resource": "*"
```

Many Amazon EC2 API actions involve multiple resources\. For example, `AttachVolume` attaches an Amazon EBS volume to an instance, so an IAM user must have permissions to use the volume and the instance\. To specify multiple resources in a single statement, separate their ARNs with commas, as follows\.

```
"Resource": ["arn1", "arn2"]
```

For a list of ARNs for Amazon EC2 resources, see [Resource types defined by Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html#amazonec2-resources-for-iam-policies)\.

## Condition keys for Amazon EC2<a name="amazon-ec2-keys"></a>

In a policy statement, you can optionally specify conditions that control when it is in effect\. Each condition contains one or more key\-value pairs\. Condition keys are not case\-sensitive\. We've defined AWS\-wide condition keys, plus additional service\-specific condition keys\.

For a list of service\-specific condition keys for Amazon EC2, see [Condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html#amazonec2-policy-keys)\. Amazon EC2 also implements the AWS\-wide condition keys\. For more information, see [Information available in all requests](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html#policy-vars-infoallreqs) in the *IAM User Guide*\. 

To use a condition key in your IAM policy, use the `Condition` statement\. For example, the following policy grants users permission to add and remove inbound and outbound rules for any security group\. It uses the `ec2:Vpc` condition key to specify that these actions can only be performed on security groups in a specific VPC\.

```
{
"Version": "2012-10-17",
  "Statement":[{
    "Effect":"Allow",
    "Action": [
       "ec2:AuthorizeSecurityGroupIngress",
       "ec2:AuthorizeSecurityGroupEgress",
       "ec2:RevokeSecurityGroupIngress",
       "ec2:RevokeSecurityGroupEgress"],
     "Resource": "arn:aws:ec2:region:account:security-group/*",
      "Condition": {
        "StringEquals": {
          "ec2:Vpc": "arn:aws:ec2:region:account:vpc/vpc-11223344556677889"
        }
      }
    }
  ]
}
```

If you specify multiple conditions, or multiple keys in a single condition, we evaluate them using a logical AND operation\. If you specify a single condition with multiple values for one key, we evaluate the condition using a logical OR operation\. For permissions to be granted, all conditions must be met\.

You can also use placeholders when you specify conditions\. For example, you can grant an IAM user permission to use resources with a tag that specifies his or her IAM user name\. For more information, see [IAM policy elements: Variables and tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/PolicyVariables.html) in the *IAM User Guide*\.

**Important**  
Many condition keys are specific to a resource, and some API actions use multiple resources\. If you write a policy with a condition key, use the `Resource` element of the statement to specify the resource to which the condition key applies\. If not, the policy may prevent users from performing the action at all, because the condition check fails for the resources to which the condition key does not apply\. If you do not want to specify a resource, or if you've written the `Action` element of your policy to include multiple API actions, then you must use the `...IfExists` condition type to ensure that the condition key is ignored for resources that do not use it\. For more information, see [\.\.\.IfExists Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_IfExists) in the *IAM User Guide*\.

All Amazon EC2 actions support the `aws:RequestedRegion` and `ec2:Region` condition keys\. For more information, see [Example: Restrict access to a specific Region](ExamplePolicies_EC2.md#iam-example-region)\.

### `ec2:SourceInstanceARN` condition key<a name="SourceInstanceARN"></a>

The `ec2:SourceInstanceARN` condition key can be used for conditions that specify the ARN of the instance from which a request is made\. This condition key is available AWS\-wide and is not service\-specific\. For policy examples, see [Amazon EC2: Attach or detach volumes to an EC2 instance](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_ec2_volumes-instance.html) and [Example: Allow a specific instance to view resources in other AWS services](ExamplePolicies_EC2.md#iam-example-source-instance)\. The `ec2:SourceInstanceARN` key cannot be used as a variable to populate the ARN for the `Resource` element in a statement\.

For example policy statements for Amazon EC2, see [Example policies for working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md)\.

### `ec2:Attribute` condition key<a name="attribute-key"></a>

The `ec2:Attribute` condition key can be used for conditions that filter access by an attribute of a resource\. The condition key supports only properties that are of a primitive data type, such as a string or integer, or complex objects that have only a `Value` property, such as the **Description** object of the [ ModifyImageAttribute](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_AttributeValue.html) API action\.

For example, the following policy uses the `ec2:Attribute/Description` condition key to filter access by the complex **Description** object of the **ModifyImageAttribute** API action\. The condition key allows only requests that modify an image's description to either `Production` or `Development`\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:ModifyImageAttribute",
      "Resource": "arn:aws:ec2:us-east-1::image/ami-*",
      "Condition": {
        "StringEquals": {
          "ec2:Attribute/Description": [
            "Production",
            "Development"
          ]
        }
      }
    }
  ]
}
```

The following example policy uses the `ec2:Attribute` condition key to filter access by the primitive **Attribute** property of the ** ModifyImageAttribute** API action\. The condition key denies all requests that attempt to modify an image's description\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": "ec2:ModifyImageAttribute",
      "Resource": "arn:aws:ec2:us-east-1::image/ami-*",
      "Condition": {
        "StringEquals": {
          "ec2:Attribute": "Description"
        }
      }
    }
  ]
}
```

## Check that users have the required permissions<a name="check-required-permissions"></a>

After you've created an IAM policy, we recommend that you check whether it grants users the permissions to use the particular API actions and resources they need before you put the policy into production\.

First, create an IAM user for testing purposes, and then attach the IAM policy that you created to the test user\. Then, make a request as the test user\.

If the Amazon EC2 action that you are testing creates or modifies a resource, you should make the request using the `DryRun` parameter \(or run the AWS CLI command with the `--dry-run` option\)\. In this case, the call completes the authorization check, but does not complete the operation\. For example, you can check whether the user can terminate a particular instance without actually terminating it\. If the test user has the required permissions, the request returns `DryRunOperation`; otherwise, it returns `UnauthorizedOperation`\.

If the policy doesn't grant the user the permissions that you expected, or is overly permissive, you can adjust the policy as needed and retest until you get the desired results\. 

**Important**  
It can take several minutes for policy changes to propagate before they take effect\. Therefore, we recommend that you allow five minutes to pass before you test your policy updates\.

If an authorization check fails, the request returns an encoded message with diagnostic information\. You can decode the message using the `DecodeAuthorizationMessage` action\. For more information, see [DecodeAuthorizationMessage](https://docs.aws.amazon.com/STS/latest/APIReference/API_DecodeAuthorizationMessage.html) in the *AWS Security Token Service API Reference*, and [decode\-authorization\-message](https://docs.aws.amazon.com/cli/latest/reference/sts/decode-authorization-message.html) in the *AWS CLI Command Reference*\.
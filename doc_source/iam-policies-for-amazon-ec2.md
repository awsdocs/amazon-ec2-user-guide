# IAM policies for Amazon EC2<a name="iam-policies-for-amazon-ec2"></a>

By default, users don't have permission to create or modify Amazon EC2 resources, or perform tasks using the Amazon EC2 API, Amazon EC2 console, or CLI\. To allow users to create or modify resources and perform tasks, you must create IAM policies that grant users permission to use the specific resources and API actions they'll need, and then attach those policies to the users, groups, or IAM roles that require those permissions\.

When you attach a policy to a user, group of users, or role it allows or denies the users permission to perform the specified tasks on the specified resources\. For more general information about IAM policies, see [Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\. For more information about managing and creating custom IAM policies, see [Managing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html)\.

**Getting Started**

An IAM policy must grant or deny permissions to use one or more Amazon EC2 actions\. It must also specify the resources that can be used with the action, which can be all resources, or in some cases, specific resources\. The policy can also include conditions that you apply to the resource\. 

Amazon EC2 partially supports resource\-level permissions\. This means that for some EC2 API actions, you cannot specify which resource a user is allowed to work with for that action\. Instead, you have to allow users to work with all resources for that action\. 


| Task | Topic | 
| --- | --- | 
| Understand the basic structure of a policy | [Policy syntax](iam-policy-structure.md#policy-syntax) | 
| Define actions in your policy | [Actions for Amazon EC2](iam-policy-structure.md#UsingWithEC2_Actions) | 
| Define specific resources in your policy | [Amazon Resource Names \(ARNs\) for Amazon EC2](iam-policy-structure.md#EC2_ARN_Format) | 
| Apply conditions to the use of the resources | [Condition keys for Amazon EC2](iam-policy-structure.md#amazon-ec2-keys) | 
| Work with the available resource\-level permissions for Amazon EC2 | [Actions, resources, and condition keys for Amazon EC2](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazonec2.html) | 
| Test your policy |  [Check that users have the required permissions](iam-policy-structure.md#check-required-permissions)  | 
| Generate an IAM policy |  [Generate policies based on access activity](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_generate-policy.html)  | 
| Example policies for a CLI or SDK | [Example policies for working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md) | 
| Example policies for the Amazon EC2 console | [Example policies for working in the Amazon EC2 console](iam-policies-ec2-console.md) | 

## Grant permissions to users, groups, and roles<a name="granting-iam-permissions"></a>

The following are examples of some AWS managed policies that are available to utilize if they meet your needs:
+ `PowerUserAccess`
+ `ReadOnlyAccess`
+ `AmazonEC2FullAccess`
+ `AmazonEC2ReadOnlyAccess`

For more information on the AWS managed policies available to work with Amazon EC2, see [AWS managed policies for Amazon Elastic Compute Cloud](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/security-iam-awsmanpol.html)\.

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.
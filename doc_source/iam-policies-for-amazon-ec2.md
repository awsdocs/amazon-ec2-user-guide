# IAM Policies for Amazon EC2<a name="iam-policies-for-amazon-ec2"></a>

By default, IAM users don't have permission to create or modify Amazon EC2 resources, or perform tasks using the Amazon EC2 API\. \(This means that they also can't do so using the Amazon EC2 console or CLI\.\) To allow IAM users to create or modify resources and perform tasks, you must create IAM policies that grant IAM users permission to use the specific resources and API actions they'll need, and then attach those policies to the IAM users or groups that require those permissions\.

When you attach a policy to a user or group of users, it allows or denies the users permission to perform the specified tasks on the specified resources\. For more general information about IAM policies, see [Permissions and Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/PermissionsAndPolicies.html) in the *IAM User Guide*\. For more information about managing and creating custom IAM policies, see [Managing IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/ManagingPolicies.html)\.

**Getting Started**

An IAM policy must grant or deny permissions to use one or more Amazon EC2 actions\. It must also specify the resources that can be used with the action, which can be all resources, or in some cases, specific resources\. The policy can also include conditions that you apply to the resource\. 

Amazon EC2 partially supports resource\-level permissions\. This means that for some EC2 API actions, you cannot specify which resource a user is allowed to work with for that action\. Instead, you have to allow users to work with all resources for that action\. 


| Task | Topic | 
| --- | --- | 
| Understand the basic structure of a policy | [Policy Syntax](iam-policy-structure.md#policy-syntax) | 
| Define actions in your policy | [Actions for Amazon EC2](iam-policy-structure.md#UsingWithEC2_Actions) | 
| Define specific resources in your policy | [Amazon Resource Names \(ARNs\) for Amazon EC2](iam-policy-structure.md#EC2_ARN_Format) | 
| Apply conditions to the use of the resources | [Condition Keys for Amazon EC2](iam-policy-structure.md#amazon-ec2-keys) | 
| Work with the available resource\-level permissions for Amazon EC2 | [Actions, Resources, and Condition Keys for Amazon EC2](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_amazonec2.html) \(IAM User Guide\) | 
| Test your policy |  [Checking That Users Have the Required Permissions](iam-policy-structure.md#check-required-permissions)  | 
| Example policies for a CLI or SDK | [Example Policies for Working with the AWS CLI or an AWS SDK](ExamplePolicies_EC2.md) | 
| Example policies for the Amazon EC2 console | [Example Policies for Working in the Amazon EC2 Console](iam-policies-ec2-console.md) | 
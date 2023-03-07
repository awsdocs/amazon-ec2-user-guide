# Identity and access management for Amazon EC2<a name="security-iam"></a>

Your security credentials identify you to services in AWS and grant you unlimited use of your AWS resources, such as your Amazon EC2 resources\. You can use features of Amazon EC2 and AWS Identity and Access Management \(IAM\) to allow other users, services, and applications to use your Amazon EC2 resources without sharing your security credentials\. You can use IAM to control how other users use resources in your AWS account, and you can use security groups to control access to your Amazon EC2 instances\. You can choose to allow full use or limited use of your Amazon EC2 resources\. 

For best practices for securing your AWS resources using IAM, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\.

**Topics**
+ [Network access to your instance](#intro-to-security-groups)
+ [Amazon EC2 permission attributes](#AmazonEC2Permissions)
+ [IAM and Amazon EC2](#intro-to-iam)
+ [IAM policies for Amazon EC2](iam-policies-for-amazon-ec2.md)
+ [AWS managed policies for Amazon Elastic Compute Cloud](security-iam-awsmanpol.md)
+ [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)
+ [Authorize inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)

## Network access to your instance<a name="intro-to-security-groups"></a>

A security group acts as a firewall that controls the traffic allowed to reach one or more instances\. When you launch an instance, you assign it one or more security groups\. You add rules to each security group that control traffic for the instance\. You can modify the rules for a security group at any time; the new rules are automatically applied to all instances to which the security group is assigned\. 

For more information, see [Authorize inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.

## Amazon EC2 permission attributes<a name="AmazonEC2Permissions"></a>

Your organization might have multiple AWS accounts\. Amazon EC2 enables you to specify additional AWS accounts that can use your Amazon Machine Images \(AMIs\) and Amazon EBS snapshots\. These permissions work at the AWS account level only; you can't restrict permissions for specific users within the specified AWS account\. All users in the AWS account that you've specified can use the AMI or snapshot\.

Each AMI has a `LaunchPermission` attribute that controls which AWS accounts can access the AMI\. For more information, see [Make an AMI public](sharingamis-intro.md)\.

Each Amazon EBS snapshot has a `createVolumePermission` attribute that controls which AWS accounts can use the snapshot\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

## IAM and Amazon EC2<a name="intro-to-iam"></a>

IAM enables you to do the following:
+ Create users and groups under your AWS account
+ Assign unique security credentials to each user under your AWS account
+ Control each user's permissions to perform tasks using AWS resources
+ Allow the users in another AWS account to share your AWS resources
+ Create roles for your AWS account and define the users or services that can assume them
+ Use existing identities for your enterprise to grant permissions to perform tasks using AWS resources

By using IAM with Amazon EC2, you can control whether users in your organization can perform a task using specific Amazon EC2 API actions and whether they can use specific AWS resources\.

This topic helps you answer the following questions:
+ How do I create groups and users in IAM?
+ How do I create a policy?
+ What IAM policies do I need to carry out tasks in Amazon EC2?
+ How do I grant permissions to perform actions in Amazon EC2?
+ How do I grant permissions to perform actions on specific resources in Amazon EC2?

### Create users, groups, and roles<a name="creating-an-iam-group"></a>

You can create users and groups for your AWS account and then assign them the permissions they require\. As a best practice, users should acquire the permissions by assuming IAM roles\. For more information on how to set up users and groups for your AWS account, see [Set up to use Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html)\.

  An [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is an IAM identity that you can create in your account that has specific permissions\. An IAM role is similar to an IAM user in that it is an AWS identity with permissions policies that determine what the identity can and cannot do in AWS\. However, instead of being uniquely associated with one person, a role is intended to be assumable by anyone who needs it\. Also, a role does not have standard long\-term credentials such as a password or access keys associated with it\. Instead, when you assume a role, it provides you with temporary security credentials for your role session\. For more information on how to create IAM roles and grant permissions with them, see [IAM roles for Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)\.

### Related topics<a name="iam-related-topics"></a>

For more information about IAM, see the following:
+ [IAM policies for Amazon EC2](iam-policies-for-amazon-ec2.md)
+ [IAM roles for Amazon EC2](iam-roles-for-amazon-ec2.md)
+ [AWS Identity and Access Management \(IAM\)](https://aws.amazon.com/iam)
+ [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)
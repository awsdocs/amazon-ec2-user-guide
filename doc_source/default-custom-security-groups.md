# Default and custom security groups<a name="default-custom-security-groups"></a>

Your AWS account automatically has a default security group for the default VPC in each Region\. If you don't specify a security group when you launch an instance, the instance is automatically associated with the default security group for the VPC\. If you don't want your instances to use the default security group, you can create your own custom security groups and specify them when you launch your instances\.

**Topics**
+ [Default security groups](#default-security-group)
+ [Custom security groups](#creating-your-own-security-groups)

## Default security groups<a name="default-security-group"></a>

Your AWS account automatically has a default security group for the default VPC in each Region\. If you don't specify a security group when you launch an instance, the instance is automatically associated with the default security group for the VPC\.

A default security group is named `default`, and it has an ID assigned by AWS\. The following are the default rules for each default security group:
+ Allows all inbound traffic from other instances associated with the default security group\. The security group specifies itself as a source security group in its inbound rules\.
+ Allows all outbound traffic from the instance\.

You can add or remove inbound and outbound rules for any default security group\.

You can't delete a default security group\. If you try to delete a default security group, you see the following error: `Client.CannotDelete: the specified group: "sg-51530134" name: "default" cannot be deleted by a user`\. 

## Custom security groups<a name="creating-your-own-security-groups"></a>

If you don't want your instances to use the default security group, you can create your own security groups and specify them when you launch your instances\. You can create multiple security groups to reflect the different roles that your instances play; for example, a web server or a database server\. 

When you create a security group, you must provide it with a name and a description\. Security group names and descriptions can be up to 255 characters in length, and are limited to the following characters:

a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=&;\{\}\!$\*

A security group name cannot start with `sg-`\. A security group name must be unique for the VPC\.

The following are the default rules for a security group that you create:
+ Allows no inbound traffic
+ Allows all outbound traffic

After you've created a security group, you can change its inbound rules to reflect the type of inbound traffic that you want to reach the associated instances\. You can also change its outbound rules\.

For more information about the rules you can add to a security group, see [Security group rules for different use cases](security-group-rules-reference.md)\.
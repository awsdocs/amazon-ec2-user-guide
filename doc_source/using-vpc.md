# Amazon EC2 and Amazon Virtual Private Cloud<a name="using-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to define a virtual network in your own logically isolated area within the AWS cloud, known as a *virtual private cloud \(VPC\)*\. You can launch your AWS resources, such as instances, into your VPC\. Your VPC closely resembles a traditional network that you might operate in your own data center, with the benefits of using AWS's scalable infrastructure\. You can configure your VPC; you can select its IP address range, create subnets, and configure route tables, network gateways, and security settings\. You can connect instances in your VPC to the internet\. You can connect your VPC to your own corporate data center, making the AWS cloud an extension of your data center\. To protect the resources in each subnet, you can use multiple layers of security, including security groups and network access control lists\. For more information, see the [Amazon VPC User Guide](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/)\.

Your account may support both the EC2\-VPC and EC2\-Classic platforms, on a region\-by\-region basis\. If you created your account after 2013\-12\-04, it supports EC2\-VPC only\. To find out which platforms your account supports, see [Supported Platforms](ec2-supported-platforms.md)\. If your accounts supports EC2\-VPC only, we create a *default VPC* for you\. A default VPC is a VPC that is already configured and ready for you to use\. You can launch instances into your default VPC immediately\. For more information, see [Your Default VPC and Subnets](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html) in the *Amazon VPC User Guide*\. If your account supports EC2\-Classic and EC2\-VPC, you can launch instances into either platform\. Regardless of which platforms your account supports, you can create your own *nondefault VPC*, and configure it as you need\. 

**Topics**
+ [Benefits of Using a VPC](#concepts-vpc)
+ [Differences Between EC2\-Classic and EC2\-VPC](#differences-ec2-classic-vpc)
+ [Sharing and Accessing Resources Between EC2\-Classic and EC2\-VPC](#vpc-classic-shared-resources)
+ [Instance Types Available Only in a VPC](#vpc-only-instance-types)
+ [Amazon VPC Documentation](#amazon-vpc-docs)
+ [Supported Platforms](ec2-supported-platforms.md)
+ [ClassicLink](vpc-classiclink.md)
+ [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)

## Benefits of Using a VPC<a name="concepts-vpc"></a>

By launching your instances into a VPC instead of EC2\-Classic, you gain the ability to: 
+ Assign static private IPv4 addresses to your instances that persist across starts and stops
+ Assign multiple IPv4 addresses to your instances
+ Define network interfaces, and attach one or more network interfaces to your instances
+ Change security group membership for your instances while they're running
+ Control the outbound traffic from your instances \(egress filtering\) in addition to controlling the inbound traffic to them \(ingress filtering\)
+ Add an additional layer of access control to your instances in the form of network access control lists \(ACL\)
+ Run your instances on single\-tenant hardware
+ Assign IPv6 addresses to your instances

## Differences Between EC2\-Classic and EC2\-VPC<a name="differences-ec2-classic-vpc"></a>

The following table summarizes the differences between instances launched in EC2\-Classic, instances launched in a default VPC, and instances launched in a nondefault VPC\.


| Characteristic | EC2\-Classic | Default VPC | Nondefault VPC | 
| --- | --- | --- | --- | 
|  Public IPv4 address \(from Amazon's public IP address pool\)  |  Your instance receives a public IPv4 address\.  |  Your instance launched in a default subnet receives a public IPv4 address by default, unless you specify otherwise during launch, or you modify the subnet's public IPv4 address attribute\.   |  Your instance doesn't receive a public IPv4 address by default, unless you specify otherwise during launch, or you modify the subnet's public IPv4 address attribute\.  | 
|  Private IPv4 address  |  Your instance receives a private IPv4 address from the EC2\-Classic range each time it's started\.  |  Your instance receives a static private IPv4 address from the address range of your default VPC\.  |  Your instance receives a static private IPv4 address from the address range of your VPC\.  | 
|  Multiple private IPv4 addresses  |  We select a single private IP address for your instance; multiple IP addresses are not supported\.  |  You can assign multiple private IPv4 addresses to your instance\.  |  You can assign multiple private IPv4 addresses to your instance\.  | 
|  Elastic IP address \(IPv4\)  |  An Elastic IP is disassociated from your instance when you stop it\.  |  An Elastic IP remains associated with your instance when you stop it\.  |  An Elastic IP remains associated with your instance when you stop it\.  | 
|  DNS hostnames  |  DNS hostnames are enabled by default\.  |  DNS hostnames are enabled by default\.  |  DNS hostnames are disabled by default\.  | 
|  Security group  |  A security group can reference security groups that belong to other AWS accounts\. You can create up to 500 security groups in each region\.  |  A security group can reference security groups for your VPC only\.  You can create up to 500 security groups per VPC\.  |  A security group can reference security groups for your VPC only\.  You can create up to 500 security groups per VPC\.  | 
|  Security group association  |  You can assign an unlimited number of security groups to an instance when you launch it\. You can't change the security groups of your running instance\. You can either modify the rules of the assigned security groups, or replace the instance with a new one \(create an AMI from the instance, launch a new instance from this AMI with the security groups that you need, disassociate any Elastic IP address from the original instance and associate it with the new instance, and then terminate the original instance\)\.  |  You can assign up to 5 security groups to an instance\. You can assign security groups to your instance when you launch it and while it's running\.  |  You can assign up to 5 security groups to an instance\. You can assign security groups to your instance when you launch it and while it's running\.  | 
|  Security group rules  |  You can add rules for inbound traffic only\. You can add up to 100 rules to a security group\.  |  You can add rules for inbound and outbound traffic\.  You can add up to 50 rules to a security group\.  |  You can add rules for inbound and outbound traffic\.  You can add up to 50 rules to a security group\.  | 
|  Tenancy  |  Your instance runs on shared hardware\.  |  You can run your instance on shared hardware or single\-tenant hardware\.  |  You can run your instance on shared hardware or single\-tenant hardware\.  | 
| Accessing the Internet | Your instance can access the Internet\. Your instance automatically receives a public IP address, and can access the Internet directly through the AWS network edge\. | By default, your instance can access the Internet\. Your instance receives a public IP address by default\. An Internet gateway is attached to your default VPC, and your default subnet has a route to the Internet gateway\. | By default, your instance cannot access the Internet\. Your instance doesn't receive a public IP address by default\. Your VPC may have an Internet gateway, depending on how it was created\.  | 
| IPv6 addressing | IPv6 addressing is not supported\. You cannot assign IPv6 addresses to your instances\. | You can optionally associate an IPv6 CIDR block with your VPC, and assign IPv6 addresses to instances in your VPC\. | You can optionally associate an IPv6 CIDR block with your VPC, and assign IPv6 addresses to instances in your VPC\. | 

The following diagram shows instances in each platform\. Note the following:
+ Instances 1, 2, 3, and 4 are in the EC2\-Classic platform\. 1 and 2 were launched by one account, and 3 and 4 were launched by a different account\. These instances can communicate with each other, can access the Internet directly\.
+ Instances 5 and 6 are in different subnets in the same VPC in the EC2\-VPC platform\. They were launched by the account that owns the VPC; no other account can launch instances in this VPC\. These instances can communicate with each other and can access instances in EC2\-Classic and the Internet through the Internet gateway\.

![\[Amazon EC2 supported platforms\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/supported_platforms.png)

## Sharing and Accessing Resources Between EC2\-Classic and EC2\-VPC<a name="vpc-classic-shared-resources"></a>

Some resources and features in your AWS account can be shared or accessed between the EC2\-Classic and EC2\-VPC platforms, for example, through ClassicLink\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

If your account supports EC2\-Classic, you might have set up resources for use in EC2\-Classic\. If you want to migrate from EC2\-Classic to a VPC, you must recreate those resources in your VPC\. For more information about migrating from EC2\-Classic to a VPC, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.

The following resources can be shared or accessed between EC2\-Classic and a VPC\.


| Resource | Notes | 
| --- | --- | 
| AMI |  | 
| Bundle task |  | 
| EBS volume |  | 
| Elastic IP address \(IPv4\) |  You can migrate an Elastic IP address from EC2\-Classic to EC2\-VPC\. You can't migrate an Elastic IP address that was originally allocated for use in a VPC to EC2\-Classic\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic to EC2\-VPC](elastic-ip-addresses-eip.md#migrating-eip)\.  | 
| Instance |  An EC2\-Classic instance can communicate with instances in a VPC using public IPv4 addresses, or you can use ClassicLink to enable communication over private IPv4 addresses\. You can't migrate an instance from EC2\-Classic to a VPC\. However, you can migrate your application from an instance in EC2\-Classic to an instance in a VPC\. For more information, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.  | 
| Key pair |  | 
| Load balancer |  If you're using ClassicLink, you can register a linked EC2\-Classic instance with a load balancer in a VPC, provided that the VPC has a subnet in the same Availability Zone as the instance\. You can't migrate a load balancer from EC2\-Classic to a VPC\. You can't register an instance in a VPC with a load balancer in EC2\-Classic\.  | 
| Placement group |  | 
| Reserved Instance |  You can change the network platform for your Reserved Instances from EC2\-Classic to EC2\-VPC\. For more information, see [Modifying Reserved Instances](ri-modifying.md)\.  | 
| Security group |  A linked EC2\-Classic instance can use a VPC security groups through ClassicLink to control traffic to and from the VPC\. VPC instances can't use EC2\-Classic security groups\. You can't migrate a security group from EC2\-Classic to a VPC\. You can copy rules from a security group in EC2\-Classic to a security group in a VPC\. For more information, see [Creating a Security Group](using-network-security.md#creating-security-group)\. | 
| Snapshot |  | 

The following resources can't be shared or moved between EC2\-Classic and a VPC:
+ Spot instances

## Instance Types Available Only in a VPC<a name="vpc-only-instance-types"></a>

Instances of the following instance types are not supported in EC2\-Classic and must be launched in a VPC:
+ General purpose: M4, M5, M5d, T2
+ Compute optimized: C4, C5, C5d
+ Memory optimized: R4, R5, R5d, X1, z1d
+ Storage optimized: H1, I3
+ Accelerated computing: F1, G3, P2, P3

If your account supports EC2\-Classic but you have not created a nondefault VPC, you can do one of the following to launch a VPC\-only instance:
+ Create a nondefault VPC and launch your VPC\-only instance into it by specifying a subnet ID or a network interface ID in the request\. Note that you must create a nondefault VPC if you do not have a default VPC and you are using the AWS CLI, Amazon EC2 API, or AWS SDK to launch a VPC\-only instance\. For more information, see [Create a Virtual Private Cloud \(VPC\)](get-set-up-for-amazon-ec2.md#create-a-vpc)\.
+ Launch your VPC\-only instance using the Amazon EC2 console\. The Amazon EC2 console creates a nondefault VPC in your account and launches the instance into the subnet in the first Availability Zone\. The console creates the VPC with the following attributes:
  + One subnet in each Availability Zone, with the public IPv4 addressing attribute set to `true` so that instances receive a public IPv4 address\. For more information, see [IP Addressing in Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\.
  + An Internet gateway, and a main route table that routes traffic in the VPC to the Internet gateway\. This enables the instances you launch in the VPC to communicate over the Internet\. For more information, see [Internet Gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.
  + A default security group for the VPC and a default network ACL that is associated with each subnet\. For more information, see [Security in Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Security.html) in the *Amazon VPC User Guide*\.

If you have other resources in EC2\-Classic, you can take steps to migrate them to EC2\-VPC\. For more information, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.

## Amazon VPC Documentation<a name="amazon-vpc-docs"></a>

For more information about Amazon VPC, see the following documentation\.


| Guide | Description | 
| --- | --- | 
|  [Amazon VPC Getting Started Guide](http://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/)  |  Provides a hands\-on introduction to Amazon VPC\.  | 
|  [Amazon VPC User Guide](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/)  |  Provides detailed information about how to use Amazon VPC\.  | 
|  [Amazon VPC Network Administrator Guide](http://docs.aws.amazon.com/AmazonVPC/latest/NetworkAdminGuide/)  |  Helps network administrators configure your customer gateway\.  | 
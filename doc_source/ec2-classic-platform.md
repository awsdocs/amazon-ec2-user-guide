# EC2\-Classic<a name="ec2-classic-platform"></a>


|  | 
| --- |
| We are retiring EC2\-Classic\. We recommend that you [migrate from EC2\-Classic to a VPC](vpc-migrate.md)\. | 

With EC2\-Classic, your instances run in a single, flat network that you share with other customers\. With Amazon VPC, your instances run in a virtual private cloud \(VPC\) that's logically isolated to your AWS account\.

The EC2\-Classic platform was introduced in the original release of Amazon EC2\. If you created your AWS account after 2013\-12\-04, it does not support EC2\-Classic, so you must launch your Amazon EC2 instances in a VPC\.

## Detect supported platforms<a name="ec2-supported-platforms"></a>

The Amazon EC2 console indicates which platforms you can launch instances into for the selected region, and whether you have a default VPC in that Region\. Alternatively, you can use the [describe\-account\-attributes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-account-attributes.html) command from the AWS CLI\.

### Accounts that support EC2\-Classic<a name="both-platforms"></a>

Select the Region and locate **Account attributes** on the dashboard\. The following indicates that the account supports EC2\-Classic\.

![\[The Supported platforms indicator\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/classic_indicator.png)

### Accounts that require a VPC<a name="vpc-only"></a>

Select the Region and locate **Account attributes** on the dashboard\. The following indicates that the account does not support EC2\-Classic\.

![\[The Supported platforms indicator\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc_indicator.png)

## Instance types available in EC2\-Classic<a name="ec2-classic-instance-types"></a>

Most of the newer instance types require a VPC\. The following are the only instance types supported in EC2\-Classic:
+ General purpose: M1, M3, and T1
+ Compute optimized: C1, C3, and CC2
+ Memory optimized: CR1, M2, and R3
+ Storage optimized: D2, HS1, and I2
+ Accelerated computing: G2

If your account supports EC2\-Classic but you have not created a nondefault VPC, you can do one of the following to launch instances that require a VPC:
+ Create a nondefault VPC and launch your VPC\-only instance into it by specifying a subnet ID or a network interface ID in the request\. Note that you must create a nondefault VPC if you do not have a default VPC and you are using the AWS CLI, Amazon EC2 API, or AWS SDK to launch a VPC\-only instance\.
+ Launch your VPC\-only instance using the Amazon EC2 console\. The Amazon EC2 console creates a nondefault VPC in your account and launches the instance into the subnet in the first Availability Zone\. The console creates the VPC with the following attributes:
  + One subnet in each Availability Zone, with the public IPv4 addressing attribute set to `true` so that instances receive a public IPv4 address\.
  + An internet gateway, and a main route table that routes traffic in the VPC to the Internet gateway\. This enables the instances you launch in the VPC to communicate over the Internet\. For more information, see [Internet gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.
  + A default security group for the VPC and a default network ACL that is associated with each subnet\. For more information, see [Default security groups](https://docs.aws.amazon.com/vpc/latest/userguide/default-security-group.html) and [Default network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#default-network-acl) in the *Amazon VPC User Guide*\.

If you have other resources in EC2\-Classic, you can take steps to migrate them to a VPC\. For more information, see [Migrate from EC2\-Classic to a VPC](vpc-migrate.md)\.

## Differences between instances in EC2\-Classic and a VPC<a name="differences-ec2-classic-vpc"></a>

The following table summarizes the differences between instances launched in EC2\-Classic, instances launched in a default VPC, and instances launched in a nondefault VPC\.


| Characteristic | EC2\-Classic | Default VPC | Nondefault VPC | 
| --- | --- | --- | --- | 
|  Public IPv4 address \(from Amazon's public IP address pool\)  |  Your instance receives a public IPv4 address from the EC2\-Classic public IPv4 address pool\.  |  Your instance launched in a default subnet receives a public IPv4 address by default, unless you specify otherwise during launch, or you modify the subnet's public IPv4 address attribute\.   |  Your instance doesn't receive a public IPv4 address by default, unless you specify otherwise during launch, or you modify the subnet's public IPv4 address attribute\.  | 
|  Private IPv4 address  |  Your instance receives a private IPv4 address from the EC2\-Classic range each time it's started\.  |  Your instance receives a static private IPv4 address from the address range of your default VPC\.  |  Your instance receives a static private IPv4 address from the address range of your VPC\.  | 
|  Multiple private IPv4 addresses  |  We select a single private IP address for your instance; multiple IP addresses are not supported\.  |  You can assign multiple private IPv4 addresses to your instance\.  |  You can assign multiple private IPv4 addresses to your instance\.  | 
|  Elastic IP address \(IPv4\)  |  An Elastic IP is disassociated from your instance when you stop it\.  |  An Elastic IP remains associated with your instance when you stop it\.  |  An Elastic IP remains associated with your instance when you stop it\.  | 
|  Associating an Elastic IP address  |  You associate an Elastic IP address with an instance\.  |  An Elastic IP address is a property of a network interface\. You associate an Elastic IP address with an instance by updating the network interface attached to the instance\.  |  An Elastic IP address is a property of a network interface\. You associate an Elastic IP address with an instance by updating the network interface attached to the instance\.  | 
|  Reassociating an Elastic IP address  |  If the Elastic IP address is already associated with another instance, the address is automatically associated with the new instance\.  |  If the Elastic IP address is already associated with another instance, the address is automatically associated with the new instance\.  |  If the Elastic IP address is already associated with another instance, it succeeds only if you allowed reassociation\.  | 
|  Tagging Elastic IP addresses  |  You cannot apply tags to an Elastic IP address\.  |  You can apply tags to an Elastic IP address\.  |  You can apply tags to an Elastic IP address\.  | 
|  DNS hostnames  |  DNS hostnames are enabled by default\.  |  DNS hostnames are enabled by default\.  |  DNS hostnames are disabled by default\.  | 
|  Security group  |  A security group can reference security groups that belong to other AWS accounts\.  |  A security group can reference security groups for your VPC, or for a peer VPC in a VPC peering connection\.  |  A security group can reference security groups for your VPC only\.  | 
|  Security group association  |  You can't change the security groups of your running instance\. You can either modify the rules of the assigned security groups, or replace the instance with a new one \(create an AMI from the instance, launch a new instance from this AMI with the security groups that you need, disassociate any Elastic IP address from the original instance and associate it with the new instance, and then terminate the original instance\)\.  |  You can assign up to 5 security groups to an instance\. You can assign security groups to your instance when you launch it and while it's running\.  |  You can assign up to 5 security groups to an instance\. You can assign security groups to your instance when you launch it and while it's running\.  | 
|  Security group rules  |  You can add rules for inbound traffic only\.  |  You can add rules for inbound and outbound traffic\.  |  You can add rules for inbound and outbound traffic\.  | 
|  Tenancy  |  Your instance runs on shared hardware\.  |  You can run your instance on shared hardware or single\-tenant hardware\.  |  You can run your instance on shared hardware or single\-tenant hardware\.  | 
| Accessing the Internet | Your instance can access the Internet\. Your instance automatically receives a public IP address, and can access the Internet directly through the AWS network edge\. | By default, your instance can access the Internet\. Your instance receives a public IP address by default\. An Internet gateway is attached to your default VPC, and your default subnet has a route to the Internet gateway\. | By default, your instance cannot access the Internet\. Your instance doesn't receive a public IP address by default\. Your VPC may have an Internet gateway, depending on how it was created\.  | 
| IPv6 addressing | IPv6 addressing is not supported\. You cannot assign IPv6 addresses to your instances\. | You can optionally associate an IPv6 CIDR block with your VPC, and assign IPv6 addresses to instances in your VPC\. | You can optionally associate an IPv6 CIDR block with your VPC, and assign IPv6 addresses to instances in your VPC\. | 

### Security groups for EC2\-Classic<a name="ec2-classic-security-groups"></a>

If you're using EC2\-Classic, you must use security groups created specifically for EC2\-Classic\. When you launch an instance in EC2\-Classic, you must specify a security group in the same Region as the instance\. You can't specify a security group that you created for a VPC when you launch an instance in EC2\-Classic\.

After you launch an instance in EC2\-Classic, you can't change its security groups\. However, you can add rules to or remove rules from a security group, and those changes are automatically applied to all instances that are associated with the security group after a short period\.

Your AWS account automatically has a default security group per Region for EC2\-Classic\. If you try to delete the default security group, you'll get the following error: Client\.InvalidGroup\.Reserved: The security group 'default' is reserved\.

You can create custom security groups\. The security group name must be unique within your account for the Region\. To create a security group for use in EC2\-Classic, choose **No VPC** for the VPC\.

You can add inbound rules to your default and custom security groups\. You can't change the outbound rules for an EC2\-Classic security group\. When you create a security group rule, you can use a different security group for EC2\-Classic in the same Region as the source or destination\. To specify a security group for another AWS account, add the AWS account ID as a prefix; for example, `111122223333/sg-edcd9784`\.

In EC2\-Classic, you can have up to 500 security groups in each Region for each account\. You can add up to 100 rules to a security group\. You can have up to 800 security group rules per instance\. This is calculated as the multiple of rules per security group and security groups per instance\. If you reference other security groups in your security group rules, we recommend that you use security group names that are 22 characters or less in length\.

### IP addressing and DNS<a name="ip-addressing-differences"></a>

Amazon provides a DNS server that resolves Amazon\-provided IPv4 DNS hostnames to IPv4 addresses\. In EC2\-Classic, the Amazon DNS server is located at `172.16.0.23`\.

If you create a custom firewall configuration in EC2\-Classic, you must create a rule in your firewall that allows inbound traffic from port 53 \(DNS\)—with a destination port from the ephemeral range—from the address of the Amazon DNS server; otherwise, internal DNS resolution from your instances fails\. If your firewall doesn't automatically allow DNS query responses, then you need to allow traffic from the IP address of the Amazon DNS server\. To get the IP address of the Amazon DNS server, use the following command from within your instance:

```
grep nameserver /etc/resolv.conf
```

### Elastic IP addresses<a name="VPC_EIP_EC2_Differences"></a>

If your account supports EC2\-Classic, there's one pool of Elastic IP addresses for use with the EC2\-Classic platform and another for use with your VPCs\. You can't associate an Elastic IP address that you allocated for use with a VPC with an instance in EC2\-Classic, or vice\-versa\.

**To allocate an Elastic IP address for use in EC2\-Classic**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate Elastic IP address**\.

1. For **Scope**, select **EC2\-Classic**\.

1. Choose **Allocate**\.

## Share and access resources between EC2\-Classic and a VPC<a name="vpc-classic-shared-resources"></a>

Some resources and features in your AWS account can be shared or accessed between EC2\-Classic and a VPC, for example, through ClassicLink\. For more information, see [ClassicLink](vpc-classiclink.md)\.

If your account supports EC2\-Classic, you might have set up resources for use in EC2\-Classic\. If you want to migrate from EC2\-Classic to a VPC, you must recreate those resources in your VPC\. For more information about migrating from EC2\-Classic to a VPC, see [Migrate from EC2\-Classic to a VPC](vpc-migrate.md)\.

The following resources can be shared or accessed between EC2\-Classic and a VPC\.


| Resource | Notes | 
| --- | --- | 
| AMI |  | 
| Bundle task |  | 
| EBS volume |  | 
| Elastic IP address \(IPv4\) |  You can migrate an Elastic IP address from EC2\-Classic to a VPC\. You can't migrate an Elastic IP address that was originally allocated for use in a VPC to EC2\-Classic\. For more information, see [Elastic IP addresses](vpc-migrate.md#vpc-migrate-eip)\.  | 
| Instance |  An EC2\-Classic instance can communicate with instances in a VPC using public IPv4 addresses, or you can use ClassicLink to enable communication over private IPv4 addresses\. You can't migrate an instance from EC2\-Classic to a VPC\. However, you can migrate your application from an instance in EC2\-Classic to an instance in a VPC\. For more information, see [Migrate from EC2\-Classic to a VPC](vpc-migrate.md)\.  | 
| Key pair |  | 
| Load balancer |  If you're using ClassicLink, you can register a linked EC2\-Classic instance with a load balancer in a VPC, provided that the VPC has a subnet in the same Availability Zone as the instance\. You can't migrate a load balancer from EC2\-Classic to a VPC\. You can't register an instance in a VPC with a load balancer in EC2\-Classic\.  | 
| Placement group |  | 
| Reserved Instance |  You can change the network platform for your Reserved Instances from EC2\-Classic to a VPC\. For more information, see [Modify Reserved Instances](ri-modifying.md)\.  | 
| Security group |  A linked EC2\-Classic instance can use a VPC security groups through ClassicLink to control traffic to and from the VPC\. VPC instances can't use EC2\-Classic security groups\. You can't migrate a security group from EC2\-Classic to a VPC\. You can copy rules from a security group for EC2\-Classic to a security group for a VPC\. For more information, see [Create a security group](working-with-security-groups.md#creating-security-group)\. | 
| Snapshot |  | 

The following resources can't be shared or moved between EC2\-Classic and a VPC:
+ Spot Instances
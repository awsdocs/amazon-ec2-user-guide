# EC2\-Classic<a name="ec2-classic-platform"></a>

With EC2\-Classic, your instances run in a single, flat network that you share with other customers\. With Amazon VPC, your instances run in a virtual private cloud \(VPC\) that's logically isolated to your AWS account\.

The EC2\-Classic platform was introduced in the original release of Amazon EC2\. If you created your AWS account after 2013\-12\-04, it does not support EC2\-Classic, so you must launch your Amazon EC2 instances in a VPC\.

If your account does not support EC2\-Classic, we create a default VPC for you\. By default, when you launch an instance, we launch it into your default VPC\. Alternatively, you can create a nondefault VPC and specify it when you launch an instance\.

## Detecting Supported Platforms<a name="ec2-supported-platforms"></a>

The Amazon EC2 console indicates which platforms you can launch instances into for the selected region, and whether you have a default VPC in that region\.

Verify that the region you'll use is selected in the navigation bar\. On the Amazon EC2 console dashboard, look for **Supported Platforms** under **Account Attributes**\.

### Accounts that Support EC2\-Classic<a name="both-platforms"></a>

The dashboard displays the following under **Account Attributes** to indicate that the account supports both the EC2\-Classic platform and VPCs in this region, but the region does not have a default VPC\.

![\[The Supported Platforms indicator\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/classic_indicator.png)

The output of the [describe\-account\-attributes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-account-attributes.html) command includes both the `EC2` and `VPC` values for the `supported-platforms` attribute\.

```
aws ec2 describe-account-attributes --attribute-names supported-platforms
{
    "AccountAttributes": [
        {
            "AttributeName": "supported-platforms",
            "AttributeValues": [
                {
                    "AttributeValue": "EC2"
                },
                {
                    "AttributeValue": "VPC"
                }
            ]
        }
    ]
}
```

### Accounts that Require a VPC<a name="vpc-only"></a>

The dashboard displays the following under **Account Attributes** to indicate that the account requires a VPC to launch instances in this region, does not support the EC2\-Classic platform in this region, and the region has a default VPC with the identifier `vpc-1a2b3c4d`\.

![\[The Supported Platforms indicator\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vpc_indicator.png)

The output of the [describe\-account\-attributes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-account-attributes.html) command includes only the `VPC` value for the `supported-platforms` attribute\.

```
aws ec2 describe-account-attributes --attribute-names supported-platforms
{
    "AccountAttributes": [
        {
            "AttributeValues": [
                {
                    "AttributeValue": "VPC"
                }
            ]
            "AttributeName": "supported-platforms",
        }
    ]
}
```

## Instance Types Available in EC2\-Classic<a name="ec2-classic-instance-types"></a>

Most of the newer instance types require a VPC\. The following are the only instance types supported in EC2\-Classic:
+ General purpose: M1, M3, and T1
+ Compute optimized: C1, C3, and CC2
+ Memory optimized: CR1, M2, and R3
+ Storage optimized: D2, HS1, and I2
+ Accelerated computing: G2

If your account supports EC2\-Classic but you have not created a nondefault VPC, you can do one of the following to launch instances that require a VPC:
+ Create a nondefault VPC and launch your VPC\-only instance into it by specifying a subnet ID or a network interface ID in the request\. Note that you must create a nondefault VPC if you do not have a default VPC and you are using the AWS CLI, Amazon EC2 API, or AWS SDK to launch a VPC\-only instance\. For more information, see [Create a Virtual Private Cloud \(VPC\)](get-set-up-for-amazon-ec2.md#create-a-vpc)\.
+ Launch your VPC\-only instance using the Amazon EC2 console\. The Amazon EC2 console creates a nondefault VPC in your account and launches the instance into the subnet in the first Availability Zone\. The console creates the VPC with the following attributes:
  + One subnet in each Availability Zone, with the public IPv4 addressing attribute set to `true` so that instances receive a public IPv4 address\. For more information, see [IP Addressing in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\.
  + An Internet gateway, and a main route table that routes traffic in the VPC to the Internet gateway\. This enables the instances you launch in the VPC to communicate over the Internet\. For more information, see [Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.
  + A default security group for the VPC and a default network ACL that is associated with each subnet\. For more information, see [Security in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html) in the *Amazon VPC User Guide*\.

If you have other resources in EC2\-Classic, you can take steps to migrate them to a VPC\. For more information, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.

## Differences Between Instances in EC2\-Classic and a VPC<a name="differences-ec2-classic-vpc"></a>

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

### Security Groups for EC2\-Classic<a name="ec2-classic-security-groups"></a>

If you're using EC2\-Classic, you must use security groups created specifically for EC2\-Classic\. When you launch an instance in EC2\-Classic, you must specify a security group in the same region as the instance\. You can't specify a security group that you created for a VPC when you launch an instance in EC2\-Classic\.

After you launch an instance in EC2\-Classic, you can't change its security groups\. However, you can add rules to or remove rules from a security group, and those changes are automatically applied to all instances that are associated with the security group after a short period\.

Your AWS account automatically has a default security group per region for EC2\-Classic\. If you try to delete the default security group, you'll get the following error: Client\.InvalidGroup\.Reserved: The security group 'default' is reserved\.

You can create custom security groups\. The security group name must be unique within your account for the region\. To create a security group for use in EC2\-Classic, choose **No VPC** for the VPC\.

You can add inbound rules to your default and custom security groups\. You can't change the outbound rules for an EC2\-Classic security group\. When you create a security group rule, you can use a different security group for EC2\-Classic in the same region as the source or destination\. To specify a security group for another AWS account, add the AWS account ID as a prefix; for example, `111122223333/sg-edcd9784`\.

In EC2\-Classic, you can have up to 500 security groups in each region for each account\. You can add up to 100 rules to a security group\.

### IP Addressing and DNS<a name="ip-addressing-differences"></a>

Amazon provides a DNS server that resolves Amazon\-provided IPv4 DNS hostnames to IPv4 addresses\. In EC2\-Classic, the Amazon DNS server is located at `172.16.0.23`\.

If you create a custom firewall configuration in EC2\-Classic, you must create a rule in your firewall that allows inbound traffic from port 53 \(DNS\)—with a destination port from the ephemeral range—from the address of the Amazon DNS server; otherwise, internal DNS resolution from your instances fails\. If your firewall doesn't automatically allow DNS query responses, then you need to allow traffic from the IP address of the Amazon DNS server\. To get the IP address of the Amazon DNS server, use the following command from within your instance:

```
grep nameserver /etc/resolv.conf
```

### Elastic IP Addresses<a name="VPC_EIP_EC2_Differences"></a>

If your account supports EC2\-Classic, there's one pool of Elastic IP addresses for use with the EC2\-Classic platform and another for use with your VPCs\. You can't associate an Elastic IP address that you allocated for use with a VPC with an instance in EC2\-Classic, and vice\- versa\. However, you can migrate an Elastic IP address you've allocated for use in the EC2\-Classic platform for use with a VPC\. You cannot migrate an Elastic IP address to another region\.

**To allocate an Elastic IP address for use in EC2\-Classic using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\. 

1. Select **Classic**, and then choose **Allocate**\. Close the confirmation screen\.

#### Migrating an Elastic IP Address from EC2\-Classic<a name="migrating-eip"></a>

If your account supports EC2\-Classic, you can migrate Elastic IP addresses that you've allocated for use with EC2\-Classic platform to be used with a VPC, within the same region\. This can assist you to migrate your resources from EC2\-Classic to a VPC; for example, you can launch new web servers in your VPC, and then use the same Elastic IP addresses that you used for your web servers in EC2\-Classic for your new VPC web servers\.

After you've migrated an Elastic IP address to a VPC, you cannot use it with EC2\-Classic\. However, if required, you can restore it to EC2\-Classic\. You cannot migrate an Elastic IP address that was originally allocated for use with a VPC to EC2\-Classic\.

To migrate an Elastic IP address, it must not be associated with an instance\. For more information about disassociating an Elastic IP address from an instance, see [Disassociating an Elastic IP Address and Reassociating with a Different Instance](elastic-ip-addresses-eip.md#using-instance-addressing-eips-associating-different)\.

You can migrate as many EC2\-Classic Elastic IP addresses as you can have in your account\. However, when you migrate an Elastic IP address, it counts against your Elastic IP address limit for VPCs\. You cannot migrate an Elastic IP address if it will result in your exceeding your limit\. Similarly, when you restore an Elastic IP address to EC2\-Classic, it counts against your Elastic IP address limit for EC2\-Classic\. For more information, see [Elastic IP Address Limit](elastic-ip-addresses-eip.md#using-instance-addressing-limit)\. 

You cannot migrate an Elastic IP address that has been allocated to your account for less than 24 hours\.

You can migrate an Elastic IP address from EC2\-Classic using the Amazon EC2 console or the Amazon VPC console\. This option is only available if your account supports EC2\-Classic\.

**To move an Elastic IP address using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and choose **Actions**, **Move to VPC scope**\.

1. In the confirmation dialog box, choose **Move Elastic IP**\.

You can restore an Elastic IP address to EC2\-Classic using the Amazon EC2 console or the Amazon VPC console\.

**To restore an Elastic IP address to EC2\-Classic using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, **Restore to EC2 scope**\.

1. In the confirmation dialog box, choose **Restore**\.

After you've performed the command to move or restore your Elastic IP address, the process of migrating the Elastic IP address can take a few minutes\. Use the [describe\-moving\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-moving-addresses.html) command to check whether your Elastic IP address is still moving, or has completed moving\.

After you've moved your Elastic IP address, you can view its allocation ID on the **Elastic IPs** page in the **Allocation ID** field\.

If the Elastic IP address is in a moving state for longer than 5 minutes, contact [Premium Support](https://aws.amazon.com/premiumsupport/)\.

**To move an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [move\-address\-to\-vpc](https://docs.aws.amazon.com/cli/latest/reference/ec2/move-address-to-vpc.html) \(AWS CLI\)
+ [Move\-EC2AddressToVpc](https://docs.aws.amazon.com/powershell/latest/reference/items/Move-EC2AddressToVpc.html) \(AWS Tools for Windows PowerShell\)

**To restore an Elastic IP address to EC2\-Classic using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [restore\-address\-to\-classic](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-address-to-classic.html) \(AWS CLI\)
+ [Restore\-EC2AddressToClassic](https://docs.aws.amazon.com/powershell/latest/reference/items/Restore-EC2AddressToClassic.html) \(AWS Tools for Windows PowerShell\)

**To describe the status of your moving addresses using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-moving\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-moving-addresses.html) \(AWS CLI\)
+ [Get\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

## Sharing and Accessing Resources Between EC2\-Classic and a VPC<a name="vpc-classic-shared-resources"></a>

Some resources and features in your AWS account can be shared or accessed between EC2\-Classic and a VPC, for example, through ClassicLink\. For more information, see [ClassicLink](vpc-classiclink.md)\.

If your account supports EC2\-Classic, you might have set up resources for use in EC2\-Classic\. If you want to migrate from EC2\-Classic to a VPC, you must recreate those resources in your VPC\. For more information about migrating from EC2\-Classic to a VPC, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.

The following resources can be shared or accessed between EC2\-Classic and a VPC\.


| Resource | Notes | 
| --- | --- | 
| AMI |  | 
| Bundle task |  | 
| EBS volume |  | 
| Elastic IP address \(IPv4\) |  You can migrate an Elastic IP address from EC2\-Classic to a VPC\. You can't migrate an Elastic IP address that was originally allocated for use in a VPC to EC2\-Classic\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic](#migrating-eip)\.  | 
| Instance |  An EC2\-Classic instance can communicate with instances in a VPC using public IPv4 addresses, or you can use ClassicLink to enable communication over private IPv4 addresses\. You can't migrate an instance from EC2\-Classic to a VPC\. However, you can migrate your application from an instance in EC2\-Classic to an instance in a VPC\. For more information, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.  | 
| Key pair |  | 
| Load balancer |  If you're using ClassicLink, you can register a linked EC2\-Classic instance with a load balancer in a VPC, provided that the VPC has a subnet in the same Availability Zone as the instance\. You can't migrate a load balancer from EC2\-Classic to a VPC\. You can't register an instance in a VPC with a load balancer in EC2\-Classic\.  | 
| Placement group |  | 
| Reserved Instance |  You can change the network platform for your Reserved Instances from EC2\-Classic to a VPC\. For more information, see [Modifying Reserved Instances](ri-modifying.md)\.  | 
| Security group |  A linked EC2\-Classic instance can use a VPC security groups through ClassicLink to control traffic to and from the VPC\. VPC instances can't use EC2\-Classic security groups\. You can't migrate a security group from EC2\-Classic to a VPC\. You can copy rules from a security group for EC2\-Classic to a security group for a VPC\. For more information, see [Creating a Security Group](ec2-security-groups.md#creating-security-group)\. | 
| Snapshot |  | 

The following resources can't be shared or moved between EC2\-Classic and a VPC:
+ Spot Instances
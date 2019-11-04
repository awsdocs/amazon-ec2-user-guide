# Amazon EC2 Security Groups for Linux Instances<a name="ec2-security-groups"></a>

A *security group* acts as a virtual firewall that controls the traffic for one or more instances\. When you launch an instance, you can specify one or more security groups; otherwise, we use the default security group\. You can add rules to each security group that allow traffic to or from its associated instances\. You can modify the rules for a security group at any time; the new rules are automatically applied to all instances that are associated with the security group\. When we decide whether to allow traffic to reach an instance, we evaluate all the rules from all the security groups that are associated with the instance\.

When you launch an instance in a VPC, you must specify a security group that's created for that VPC\. After you launch an instance, you can change its security groups\. Security groups are associated with network interfaces\. Changing an instance's security groups changes the security groups associated with the primary network interface \(eth0\)\. For more information, see [Changing an Instance's Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SG_Changing_Group_Membership) in the *Amazon VPC User Guide*\. You can also change the security groups associated with any other network interface\. For more information, see [Changing the Security Group](using-eni.md#eni_security_group)\.

If you have requirements that aren't met by security groups, you can maintain your own firewall on any of your instances in addition to using security groups\.

If you need to allow traffic to a Windows instance, see [Amazon EC2 Security Groups for Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/using-network-security.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Contents**
+ [Security Group Rules](#security-group-rules)
  + [Connection Tracking](#security-group-connection-tracking)
+ [Default Security Groups](#default-security-group)
+ [Custom Security Groups](#creating-your-own-security-groups)
+ [Working with Security Groups](#working-with-security-groups)
  + [Creating a Security Group](#creating-security-group)
  + [Describing Your Security Groups](#describing-security-group)
  + [Adding Rules to a Security Group](#adding-security-group-rule)
  + [Updating Security Group Rules](#updating-security-group-rules)
  + [Deleting Rules from a Security Group](#deleting-security-group-rule)
  + [Deleting a Security Group](#deleting-security-group)
+ [Security Group Rules Reference](security-group-rules-reference.md)
  + [Web Server Rules](security-group-rules-reference.md#sg-rules-web-server)
  + [Database Server Rules](security-group-rules-reference.md#sg-rules-db-server)
  + [Rules to Connect to Instances from Your Computer](security-group-rules-reference.md#sg-rules-local-access)
  + [Rules to Connect to Instances from an Instance with the Same Security Group](security-group-rules-reference.md#sg-rules-other-instances)
  + [Rules for Path MTU Discovery](security-group-rules-reference.md#sg-rules-path-mtu)
  + [Rules for Ping/ICMP](security-group-rules-reference.md#sg-rules-ping)
  + [DNS Server Rules](security-group-rules-reference.md#sg-rules-dns)
  + [Amazon EFS Rules](security-group-rules-reference.md#sg-rules-efs)
  + [Elastic Load Balancing Rules](security-group-rules-reference.md#sg-rules-elb)
  + [VPC Peering Rules](security-group-rules-reference.md#peer-vpc-rules)

## Security Group Rules<a name="security-group-rules"></a>

The rules of a security group control the inbound traffic that's allowed to reach the instances that are associated with the security group and the outbound traffic that's allowed to leave them\.

The following are the characteristics of security group rules:
+ By default, security groups allow all outbound traffic\.
+ Security group rules are always permissive; you can't create rules that deny access\.
+ Security groups are stateful — if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules\. For VPC security groups, this also means that responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules\. For more information, see [Connection Tracking](#security-group-connection-tracking)\.
+ You can add and remove rules at any time\. Your changes are automatically applied to the instances associated with the security group\.
**Note**  
The effect of some rule changes may depend on how the traffic is tracked\. For more information, see [Connection Tracking](#security-group-connection-tracking)\.
+ When you associate multiple security groups with an instance, the rules from each security group are effectively aggregated to create one set of rules\. We use this set of rules to determine whether to allow access\.
**Note**  
You can assign multiple security groups to an instance, therefore an instance can have hundreds of rules that apply\. This might cause problems when you access the instance\. We recommend that you condense your rules as much as possible\. 

For each rule, you specify the following:
+ **Protocol**: The protocol to allow\. The most common protocols are 6 \(TCP\) 17 \(UDP\), and 1 \(ICMP\)\. 
+ **Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow\. You can specify a single port number \(for example, `22`\), or range of port numbers \(for example, `7000-8000`\)\.
+ **ICMP type and code**: For ICMP, the ICMP type and code\.
+ **Source or destination**: The source \(inbound rules\) or destination \(outbound rules\) for the traffic\. Specify one of these options:
  + An individual IPv4 address\. You must use the `/32` prefix length; for example, `203.0.113.1/32`\. 
  + An individual IPv6 address\. You must use the `/128` prefix length; for example `2001:db8:1234:1a00::123/128`\.
  + A range of IPv4 addresses, in CIDR block notation, for example, `203.0.113.0/24`\.
  + A range of IPv6 addresses, in CIDR block notation, for example, `2001:db8:1234:1a00::/64`\.
  + The prefix list ID for the AWS service; for example, `pl-1a2b3c4d`\. For more information, see [Gateway VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html) in the *Amazon VPC User Guide*\.
  + Another security group\. This allows instances associated with the specified security group to access instances associated with this security group\. This does not add rules from the source security group to this security group\. You can specify one of the following security groups:
    + The current security group
    + A different security group for the same VPC
    + A different security group for a peer VPC in a VPC peering connection
+ **\(Optional\) Description**: You can add a description for the rule; for example, to help you identify it later\. A description can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\.

When you specify a security group as the source or destination for a rule, the rule affects all instances associated with the security group\. Incoming traffic is allowed based on the private IP addresses of the instances that are associated with the source security group \(and not the public IP or Elastic IP addresses\)\. For more information about IP addresses, see [Amazon EC2 Instance IP Addressing](using-instance-addressing.md)\. If your security group rule references a security group in a peer VPC, and the referenced security group or VPC peering connection is deleted, the rule is marked as stale\. For more information, see [Working with Stale Security Group Rules](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html#vpc-peering-stale-groups) in the *Amazon VPC Peering Guide*\.

If there is more than one rule for a specific port, we apply the most permissive rule\. For example, if you have a rule that allows access to TCP port 22 \(SSH\) from IP address `203.0.113.1` and another rule that allows access to TCP port 22 from everyone, everyone has access to TCP port 22\.

### Connection Tracking<a name="security-group-connection-tracking"></a>

Your security groups use connection tracking to track information about traffic to and from the instance\. Rules are applied based on the connection state of the traffic to determine if the traffic is allowed or denied\. This allows security groups to be stateful — responses to inbound traffic are allowed to flow out of the instance regardless of outbound security group rules, and vice versa\. For example, if you initiate an ICMP `ping` command to your instance from your home computer, and your inbound security group rules allow ICMP traffic, information about the connection \(including the port information\) is tracked\. Response traffic from the instance for the `ping` command is not tracked as a new request, but rather as an established connection and is allowed to flow out of the instance, even if your outbound security group rules restrict outbound ICMP traffic\.

Not all flows of traffic are tracked\. If a security group rule permits TCP or UDP flows for all traffic \(`0.0.0.0/0`\) and there is a corresponding rule in the other direction that permits all response traffic \(`0.0.0.0/0`\) for all ports \(0\-65535\), then that flow of traffic is not tracked\. The response traffic is therefore allowed to flow based on the inbound or outbound rule that permits the response traffic, and not on tracking information\. 

In the following example, the security group has specific inbound rules for TCP and ICMP traffic, and an outbound rule that allows all outbound traffic\.


****  

|  | 
| --- |
| Inbound rules | 
| Protocol type | Port number | Source IP | 
| TCP  | 22 \(SSH\) | 203\.0\.113\.1/32 | 
| TCP  | 80 \(HTTP\) | 0\.0\.0\.0/0 | 
| ICMP | All | 0\.0\.0\.0/0 | 
| Outbound rules | 
| Protocol type | Port number | Destination IP | 
| All | All | 0\.0\.0\.0/0 | 

TCP traffic on port 22 \(SSH\) to and from the instance is tracked, because the inbound rule allows traffic from `203.0.113.1/32` only, and not all IP addresses \(`0.0.0.0/0`\)\. TCP traffic on port 80 \(HTTP\) to and from the instance is not tracked, because both the inbound and outbound rules allow all traffic \(`0.0.0.0/0`\)\. ICMP traffic is always tracked, regardless of rules\. If you remove the outbound rule from the security group, then all traffic to and from the instance is tracked, including traffic on port 80 \(HTTP\)\.

An existing flow of traffic that is tracked may not be interrupted when you remove the security group rule that enables that flow\. Instead, the flow is interrupted when it's stopped by you or the other host for at least a few minutes \(or up to 5 days for established TCP connections\)\. For UDP, this may require terminating actions on the remote side of the flow\. An untracked flow of traffic is immediately interrupted if the rule that enables the flow is removed or modified\. For example, if you remove a rule that allows all inbound SSH traffic to the instance, then your existing SSH connections to the instance are immediately dropped\.

For protocols other than TCP, UDP, or ICMP, only the IP address and protocol number is tracked\. If your instance sends traffic to another host \(host B\), and host B initiates the same type of traffic to your instance in a separate request within 600 seconds of the original request or response, your instance accepts it regardless of inbound security group rules, because it’s regarded as response traffic\.

To ensure that traffic is immediately interrupted when you remove a security group rule, or to ensure that all inbound traffic is subject to firewall rules, you can use a network ACL for your subnet — network ACLs are stateless and therefore do not automatically allow response traffic\. For more information, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ACLs.html) in the *Amazon VPC User Guide*\.

## Default Security Groups<a name="default-security-group"></a>

Your AWS account automatically has a *default security group* for the default VPC in each Region\. If you don't specify a security group when you launch an instance, the instance is automatically associated with the default security group for the VPC\.

A default security group is named `default`, and it has an ID assigned by AWS\. The following are the default rules for each default security group:
+ Allows all inbound traffic from other instances associated with the default security group \(the security group specifies itself as a source security group in its inbound rules\)
+ Allows all outbound traffic from the instance\.

You can add or remove inbound and outbound rules for any default security group\.

You can't delete a default security group\. If you try to delete a default security group, you see the following error: `Client.CannotDelete: the specified group: "sg-51530134" name: "default" cannot be deleted by a user`\. 

## Custom Security Groups<a name="creating-your-own-security-groups"></a>

If you don't want your instances to use the default security group, you can create your own security groups and specify them when you launch your instances\. You can create multiple security groups to reflect the different roles that your instances play; for example, a web server or a database server\. 

When you create a security group, you must provide it with a name and a description\. Security group names and descriptions can be up to 255 characters in length, and are limited to the following characters:

a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=&;\{\}\!$\*

A security group name cannot start with `sg-`\. A security group name must be unique for the VPC\.

The following are the default rules for a security group that you create:
+ Allows no inbound traffic
+ Allows all outbound traffic

After you've created a security group, you can change its inbound rules to reflect the type of inbound traffic that you want to reach the associated instances\. You can also change its outbound rules\.

For more information about the rules you can add to a security group, see [Security Group Rules Reference](security-group-rules-reference.md)\.

## Working with Security Groups<a name="working-with-security-groups"></a>

You can create, view, update, and delete security groups and security group rules using the Amazon EC2 console\.

**Topics**
+ [Creating a Security Group](#creating-security-group)
+ [Describing Your Security Groups](#describing-security-group)
+ [Adding Rules to a Security Group](#adding-security-group-rule)
+ [Updating Security Group Rules](#updating-security-group-rules)
+ [Deleting Rules from a Security Group](#deleting-security-group-rule)
+ [Deleting a Security Group](#deleting-security-group)

### Creating a Security Group<a name="creating-security-group"></a>

You can create a custom security group using the Amazon EC2 console\. You must specify the VPC for which you're creating the security group\.

**To create a new security group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create Security Group**\.

1. Specify a name and description for the security group\.

1. For **VPC**, choose the ID of the VPC\.

1. You can start adding rules, or you can choose **Create** to create the security group now \(you can always add rules later\)\. For more information about adding rules, see [Adding Rules to a Security Group](#adding-security-group-rule)\. 

**To create a security group using the command line**
+ [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)
+ [New\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

The Amazon EC2 console enables you to copy the rules from an existing security group to a new security group\.

**To copy a security group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. 

1. Select the security group you want to copy, choose **Actions**, **Copy to new**\.

1. The **Create Security Group** dialog opens, and is populated with the rules from the existing security group\. Specify a name and description for your new security group\. For **VPC**, choose the ID of the VPC\. When you are done, choose **Create**\.

You can assign a security group to an instance when you launch the instance\. When you add or remove rules, those changes are automatically applied to all instances to which you've assigned the security group\.

After you launch an instance, you can change its security groups\. For more information, see [Changing an Instance's Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SG_Changing_Group_Membership) in the *Amazon VPC User Guide*\.

### Describing Your Security Groups<a name="describing-security-group"></a>

You can view information about your security groups using the Amazon EC2 console or the command line\.

**To describe your security groups using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. 

1. \(Optional\) Select **VPC ID** from the filter list, then choose the ID of the VPC\.

1. Select a security group\. We display general information in the **Description** tab, inbound rules on the **Inbound** tab, outbound rules on the **Outbound** tab, and tags on the **Tags** tab\.

**To describe one or more security groups using the command line**
+ [describe\-security\-groups](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html) \(AWS CLI\)
+ [Get\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)

### Adding Rules to a Security Group<a name="adding-security-group-rule"></a>

When you add a rule to a security group, the new rule is automatically applied to any instances associated with the security group after a short period\.

For more information about choosing security group rules for specific types of access, see [Security Group Rules Reference](security-group-rules-reference.md)\.

**To add rules to a security group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups** and select the security group\.

1. On the **Inbound** tab, choose **Edit**\. 

1. In the dialog, choose **Add Rule** and do the following:
   + For **Type**, select the protocol\.
   + If you select a custom TCP or UDP protocol, specify the port range in **Port Range**\. 
   + If you select a custom ICMP protocol, choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port Range**\.
   + For **Source**, choose one of the following:
     + **Custom**: in the provided field, you must specify an IP address in CIDR notation, a CIDR block, or another security group\.
     + **Anywhere**: automatically adds the `0.0.0.0/0` IPv4 CIDR block\. This option enables all traffic of the specified type to reach your instance\. This is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instance\.
**Note**  
If your security group is in a VPC that's enabled for IPv6, the **Anywhere** option creates two rules—one for IPv4 traffic \(`0.0.0.0/0`\) and one for IPv6 traffic \(`::/0`\)\.
     + **My IP**: automatically adds the public IPv4 address of your local computer\.
   + For **Description**, you can optionally specify a description for the rule\.

   For more information about the types of rules that you can add, see [Security Group Rules Reference](security-group-rules-reference.md)\.

1. Choose **Save**\.

1. You can also specify outbound rules\. On the **Outbound tab**, choose **Edit**, **Add Rule**, and do the following:
   + For **Type**, select the protocol\.
   + If you select a custom TCP or UDP protocol, specify the port range in **Port Range**\. 
   + If you select a custom ICMP protocol, choose the ICMP type name from **Protocol**, and, if applicable, the code name from **Port Range**\.
   + For **Destination**, choose one of the following:
     + **Custom**: in the provided field, you must specify an IP address in CIDR notation, a CIDR block, or another security group\.
     + **Anywhere**: automatically adds the `0.0.0.0/0` IPv4 CIDR block\. This option enables outbound traffic to all IP addresses\.
**Note**  
If your security group is in a VPC that's enabled for IPv6, the **Anywhere** option creates two rules—one for IPv4 traffic \(`0.0.0.0/0`\) and one for IPv6 traffic \(`::/0`\)\.
     + **My IP**: automatically adds the IP address of your local computer\.
   + For **Description**, you can optionally specify a description for the rule\.

1. Choose **Save**\.

**To add one or more ingress rules to a security group using the command line**
+ [authorize\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-ingress.html) \(AWS CLI\)
+ [Grant\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupIngress.html) \(AWS Tools for Windows PowerShell\)

**To add one or more egress rules to a security group using the command line**
+ [authorize\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/authorize-security-group-egress.html) \(AWS CLI\)
+ [Grant\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Grant-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

### Updating Security Group Rules<a name="updating-security-group-rules"></a>

When you modify the protocol, port range, or source or destination of an existing security group rule using the console, the console deletes the existing rule and adds a new one for you\. 

**To update a security group rule using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select the security group to update, and choose **Inbound Rules** to update a rule for inbound traffic or **Outbound Rules** to update a rule for outbound traffic\.

1. Choose **Edit**\. Modify the rule entry as required and choose **Save**\.

To update the protocol, port range, or source or destination of an existing rule using the Amazon EC2 API or a command line tool, you cannot modify the rule\. Instead, you must delete the existing rule and add a new rule\. To update the rule description only, you can use the [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html) and [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) commands\. 

**To update the description for an ingress security group rule using the command line**
+ [update\-security\-group\-rule\-descriptions\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-ingress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleIngressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleIngressDescription.html) \(AWS Tools for Windows PowerShell\)

**To update the description for an egress security group rule using the command line**
+ [update\-security\-group\-rule\-descriptions\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/update-security-group-rule-descriptions-egress.html) \(AWS CLI\)
+ [Update\-EC2SecurityGroupRuleEgressDescription](https://docs.aws.amazon.com/powershell/latest/reference/items/Update-EC2SecurityGroupRuleEgressDescription.html) \(AWS Tools for Windows PowerShell\)

### Deleting Rules from a Security Group<a name="deleting-security-group-rule"></a>

When you delete a rule from a security group, the change is automatically applied to any instances associated with the security group\.

**To delete a security group rule using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\. 

1. Select a security group\.

1. On the **Inbound** tab \(for inbound rules\) or **Outbound** tab \(for outbound rules\), choose **Edit**\. Choose **Delete** \(a cross icon\) next to each rule to delete\. 

1. Choose **Save**\.

**To remove one or more ingress rules from a security group using the command line**
+ [revoke\-security\-group\-ingress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-ingress.html) \(AWS CLI\)
+ [Revoke\-EC2SecurityGroupIngress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupIngress.html) \(AWS Tools for Windows PowerShell\)

**To remove one or more egress rules from a security group using the command line**
+ [revoke\-security\-group\-egress](https://docs.aws.amazon.com/cli/latest/reference/ec2/revoke-security-group-egress.html) \(AWS CLI\)
+ [Revoke\-EC2SecurityGroupEgress](https://docs.aws.amazon.com/powershell/latest/reference/items/Revoke-EC2SecurityGroupEgress.html) \(AWS Tools for Windows PowerShell\)

### Deleting a Security Group<a name="deleting-security-group"></a>

You can't delete a security group that is associated with an instance\. You can't delete the default security group\. You can't delete a security group that is referenced by a rule in another security group in the same VPC\. If your security group is referenced by one of its own rules, you must delete the rule before you can delete the security group\.

**To delete a security group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Select a security group and choose **Actions**, **Delete Security Group**\.

1. Choose **Yes, Delete**\.

**To delete a security group using the command line**
+ [delete\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-security-group.html) \(AWS CLI\)
+ [Remove\-EC2SecurityGroup](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2SecurityGroup.html) \(AWS Tools for Windows PowerShell\)
# Amazon EC2 security groups for Linux instances<a name="ec2-security-groups"></a>

A *security group* acts as a virtual firewall for your instance to control incoming and outgoing traffic\. Inbound rules control the incoming traffic to your instance, and outbound rules control the outgoing traffic from your instance\. When you launch an instance, you can specify one or more security groups; otherwise, Amazon EC2 uses the default security group\. You can add rules to each security group that allow traffic to or from its associated instances\. You can modify the rules for a security group at any time\. New and modified rules are automatically applied to all instances that are associated with the security group\. When Amazon EC2 decides whether to allow traffic to reach an instance, it evaluates all of the rules from all of the security groups that are associated with the instance\.

When you launch an instance in a VPC, you must specify a security group that's created for that VPC\. After you launch an instance, you can change its security groups\. Security groups are associated with network interfaces\. Changing an instance's security groups changes the security groups associated with the primary network interface \(eth0\)\. For more information, see [Changing an instance's security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SG_Changing_Group_Membership) in the *Amazon VPC User Guide*\. You can also change the security groups associated with any other network interface\. For more information, see [Changing the security group](using-eni.md#eni_security_group)\.

If you have requirements that aren't fully met by security groups, you can maintain your own firewall on any of your instances in addition to using security groups\.

To allow traffic to a Windows instance, see [Amazon EC2 security groups for Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/using-network-security.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Contents**
+ [Security group rules](#security-group-rules)
  + [Connection tracking](#security-group-connection-tracking)
+ [Default security groups](#default-security-group)
+ [Custom security groups](#creating-your-own-security-groups)
+ [Working with security groups](working-with-security-groups.md)
  + [Creating a security group](working-with-security-groups.md#creating-security-group)
  + [Copying a security group](working-with-security-groups.md#copy-security-group)
  + [Viewing your security groups](working-with-security-groups.md#describing-security-group)
  + [Adding rules to a security group](working-with-security-groups.md#adding-security-group-rule)
  + [Updating Security Group Rules](working-with-security-groups.md#updating-security-group-rules)
  + [Deleting rules from a security group](working-with-security-groups.md#deleting-security-group-rule)
  + [Deleting a security group](working-with-security-groups.md#deleting-security-group)
+ [Security group rules reference](security-group-rules-reference.md)
  + [Web server rules](security-group-rules-reference.md#sg-rules-web-server)
  + [Database server rules](security-group-rules-reference.md#sg-rules-db-server)
  + [Rules to connect to instances from your computer](security-group-rules-reference.md#sg-rules-local-access)
  + [Rules to connect to instances from an instance with the same security group](security-group-rules-reference.md#sg-rules-other-instances)
  + [Rules for ping/ICMP](security-group-rules-reference.md#sg-rules-ping)
  + [DNS server rules](security-group-rules-reference.md#sg-rules-dns)
  + [Amazon EFS rules](security-group-rules-reference.md#sg-rules-efs)
  + [Elastic Load Balancing rules](security-group-rules-reference.md#sg-rules-elb)
  + [VPC peering rules](security-group-rules-reference.md#peer-vpc-rules)

## Security group rules<a name="security-group-rules"></a>

The rules of a security group control the inbound traffic that's allowed to reach the instances that are associated with the security group\. The rules also control the outbound traffic that's allowed to leave them\.

The following are the characteristics of security group rules:
+ By default, security groups allow all outbound traffic\.
+ Security group rules are always permissive; you can't create rules that deny access\.
+ Security groups are stateful — if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules\. For VPC security groups, this also means that responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules\. For more information, see [Connection tracking](#security-group-connection-tracking)\.
+ You can add and remove rules at any time\. Your changes are automatically applied to the instances that are associated with the security group\.

  The effect of some rule changes can depend on how the traffic is tracked\. For more information, see [Connection tracking](#security-group-connection-tracking)\.
+ When you associate multiple security groups with an instance, the rules from each security group are effectively aggregated to create one set of rules\. Amazon EC2 uses this set of rules to determine whether to allow access\.

  You can assign multiple security groups to an instance\. Therefore, an instance can have hundreds of rules that apply\. This might cause problems when you access the instance\. We recommend that you condense your rules as much as possible\. 

For each rule, you specify the following:
+ **Protocol**: The protocol to allow\. The most common protocols are 6 \(TCP\), 17 \(UDP\), and 1 \(ICMP\)\. 
+ **Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow\. You can specify a single port number \(for example, `22`\), or range of port numbers \(for example, `7000-8000`\)\.
+ **ICMP type and code**: For ICMP, the ICMP type and code\.
+ **Source or destination**: The source \(inbound rules\) or destination \(outbound rules\) for the traffic\. Specify one of these options:
  + An individual IPv4 address\. You must use the `/32` prefix length; for example, `203.0.113.1/32`\. 
  + An individual IPv6 address\. You must use the `/128` prefix length; for example, `2001:db8:1234:1a00::123/128`\.
  + A range of IPv4 addresses, in CIDR block notation; for example, `203.0.113.0/24`\.
  + A range of IPv6 addresses, in CIDR block notation; for example, `2001:db8:1234:1a00::/64`\.
  + The prefix list ID for the AWS service; for example, `pl-1a2b3c4d`\. For more information, see [Gateway VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html) in the *Amazon VPC User Guide*\.
  + Another security group\. This allows instances that are associated with the specified security group to access instances associated with this security group\. Choosing this option does not add rules from the source security group to this security group\. You can specify one of the following security groups:
    + The current security group
    + A different security group for the same VPC
    + A different security group for a peer VPC in a VPC peering connection
+ **\(Optional\) Description**: You can add a description for the rule, which can help you identify it later\. A description can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\.

When you specify a security group as the source or destination for a rule, the rule affects all instances that are associated with the security group\. Incoming traffic is allowed based on the private IP addresses of the instances that are associated with the source security group \(and not the public IP or Elastic IP addresses\)\. For more information about IP addresses, see [Amazon EC2 instance IP addressing](using-instance-addressing.md)\. If your security group rule references a security group in a peer VPC, and the referenced security group or VPC peering connection is deleted, the rule is marked as stale\. For more information, see [Working with Stale Security Group Rules](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html#vpc-peering-stale-groups) in the *Amazon VPC Peering Guide*\.

If there is more than one rule for a specific port, Amazon EC2 applies the most permissive rule\. For example, if you have a rule that allows access to TCP port 22 \(SSH\) from IP address `203.0.113.1`, and another rule that allows access to TCP port 22 from everyone, everyone has access to TCP port 22\.

### Connection tracking<a name="security-group-connection-tracking"></a>

Your security groups use connection tracking to track information about traffic to and from the instance\. Rules are applied based on the connection state of the traffic to determine if the traffic is allowed or denied\. This approach allows security groups to be stateful\. This means that responses to inbound traffic are allowed to flow out of the instance regardless of outbound security group rules, and vice versa\. For example, if you initiate an ICMP `ping` command to your instance from your home computer, and your inbound security group rules allow ICMP traffic, information about the connection \(including the port information\) is tracked\. Response traffic from the instance for the `ping` command is not tracked as a new request, but rather as an established connection and is allowed to flow out of the instance, even if your outbound security group rules restrict outbound ICMP traffic\.

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

TCP traffic on port 22 \(SSH\) to and from the instance is tracked, because the inbound rule allows traffic from `203.0.113.1/32` only, and not all IP addresses \(`0.0.0.0/0`\)\. TCP traffic on port 80 \(HTTP\) to and from the instance is not tracked, because both the inbound and outbound rules allow all traffic \(`0.0.0.0/0`\)\. ICMP traffic is always tracked, regardless of rules\. If you remove the outbound rule from the security group, all traffic to and from the instance is tracked, including traffic on port 80 \(HTTP\)\.

An untracked flow of traffic is immediately interrupted if the rule that enables the flow is removed or modified\. For example, if you have an open \(0\.0\.0\.0/0\) outbound rule, and you remove a rule that allows all \(0\.0\.0\.0/0\) inbound SSH \(TCP port 22\) traffic to the instance \(or modify it such that the connection would no longer be permitted\), your existing SSH connections to the instance are immediately dropped\. The connection was not previously being tracked, so the change will break the connection\. On the other hand, if you have a narrower inbound rule that initially allows the SSH connection \(meaning that the connection was tracked\), but change that rule to no longer allow new connections from the address of the current SSH client, the existing connection will not be broken by changing the rule\.

For protocols other than TCP, UDP, or ICMP, only the IP address and protocol number is tracked\. If your instance sends traffic to another host \(host B\), and host B initiates the same type of traffic to your instance in a separate request within 600 seconds of the original request or response, your instance accepts it regardless of inbound security group rules\. Your instance accepts it because it’s regarded as response traffic\.

To ensure that traffic is immediately interrupted when you remove a security group rule, or to ensure that all inbound traffic is subject to firewall rules, you can use a network ACL for your subnet\. Network ACLs are stateless and therefore do not automatically allow response traffic\. For more information, see [Network ACLs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html) in the *Amazon VPC User Guide*\.

## Default security groups<a name="default-security-group"></a>

Your AWS account automatically has a *default security group* for the default VPC in each Region\. If you don't specify a security group when you launch an instance, the instance is automatically associated with the default security group for the VPC\.

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

For more information about the rules you can add to a security group, see [Security group rules reference](security-group-rules-reference.md)\.
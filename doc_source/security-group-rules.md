# Security group rules<a name="security-group-rules"></a>

The rules of a security group control the inbound traffic that's allowed to reach the instances that are associated with the security group\. The rules also control the outbound traffic that's allowed to leave them\.

The following are the characteristics of security group rules:
+ By default, security groups allow all outbound traffic\.
+ Security group rules are always permissive; you can't create rules that deny access\.
+ Security group rules enable you to filter traffic based on protocols and port numbers\.
+ Security groups are stateful—if you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules\. For VPC security groups, this also means that responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules\. For more information, see [Connection tracking](security-group-connection-tracking.md)\.
+ You can add and remove rules at any time\. Your changes are automatically applied to the instances that are associated with the security group\.

  The effect of some rule changes can depend on how the traffic is tracked\. For more information, see [Connection tracking](security-group-connection-tracking.md)\.
+ When you associate multiple security groups with an instance, the rules from each security group are effectively aggregated to create one set of rules\. Amazon EC2 uses this set of rules to determine whether to allow access\.

  You can assign multiple security groups to an instance\. Therefore, an instance can have hundreds of rules that apply\. This might cause problems when you access the instance\. We recommend that you condense your rules as much as possible\. 

For each rule, you specify the following:
+ **Name**: The name for the security group \(for example, `my-security-group`\)\. 

  A name can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\. When the name contains trailing spaces, we trim the spaces when we save the name\. For example, if you enter "Test Security Group " for the name, we store it as "Test Security Group"\.
+ **Protocol**: The protocol to allow\. The most common protocols are 6 \(TCP\), 17 \(UDP\), and 1 \(ICMP\)\. 
+ **Port range**: For TCP, UDP, or a custom protocol, the range of ports to allow\. You can specify a single port number \(for example, `22`\), or range of port numbers \(for example, `7000-8000`\)\.
+ **ICMP type and code**: For ICMP, the ICMP type and code\.
+ **Source or destination**: The source \(inbound rules\) or destination \(outbound rules\) for the traffic\. Specify one of these options:
  + An individual IPv4 address\. You must use the `/32` prefix length; for example, `203.0.113.1/32`\. 
  + An individual IPv6 address\. You must use the `/128` prefix length; for example, `2001:db8:1234:1a00::123/128`\.
  + A range of IPv4 addresses, in CIDR block notation; for example, `203.0.113.0/24`\.
  + A range of IPv6 addresses, in CIDR block notation; for example, `2001:db8:1234:1a00::/64`\.
  + A prefix list ID, for example, `pl-1234abc1234abc123`\. For more information, see [Prefix lists](https://docs.aws.amazon.com/vpc/latest/userguide/managed-prefix-lists.html) in the *Amazon VPC User Guide*\.
  + Another security group\. This allows instances that are associated with the specified security group to access instances associated with this security group\. Choosing this option does not add rules from the source security group to this security group\. You can specify one of the following security groups:
    + The current security group
    + A different security group for the same VPC
    + A different security group for a peer VPC in a VPC peering connection
+ **\(Optional\) Description**: You can add a description for the rule, which can help you identify it later\. A description can be up to 255 characters in length\. Allowed characters are a\-z, A\-Z, 0\-9, spaces, and \.\_\-:/\(\)\#,@\[\]\+=;\{\}\!$\*\.

When you specify a security group as the source or destination for a rule, the rule affects all instances that are associated with the security group\. Incoming traffic is allowed based on the private IP addresses of the instances that are associated with the source security group \(and not the public IP or Elastic IP addresses\)\. For more information about IP addresses, see [Amazon EC2 instance IP addressing](using-instance-addressing.md)\. If your security group rule references a security group in a peer VPC, and the referenced security group or VPC peering connection is deleted, the rule is marked as stale\. For more information, see [Working with Stale Security Group Rules](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html#vpc-peering-stale-groups) in the *Amazon VPC Peering Guide*\.

If there is more than one rule for a specific port, Amazon EC2 applies the most permissive rule\. For example, if you have a rule that allows access to TCP port 22 \(SSH\) from IP address `203.0.113.1`, and another rule that allows access to TCP port 22 from everyone, everyone has access to TCP port 22\.
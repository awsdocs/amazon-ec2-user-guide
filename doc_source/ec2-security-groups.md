# Amazon EC2 security groups for Linux instances<a name="ec2-security-groups"></a>

A *security group* acts as a virtual firewall for your EC2 instances to control incoming and outgoing traffic\. Inbound rules control the incoming traffic to your instance, and outbound rules control the outgoing traffic from your instance\. When you launch an instance, you can specify one or more security groups\. If you don't specify a security group, Amazon EC2 uses the default security group\. You can add rules to each security group that allow traffic to or from its associated instances\. You can modify the rules for a security group at any time\. New and modified rules are automatically applied to all instances that are associated with the security group\. When Amazon EC2 decides whether to allow traffic to reach an instance, it evaluates all of the rules from all of the security groups that are associated with the instance\.

When you launch an instance in a VPC, you must specify a security group that's created for that VPC\. After you launch an instance, you can change its security groups\. Security groups are associated with network interfaces\. Changing an instance's security groups changes the security groups associated with the primary network interface \(eth0\)\. For more information, see [Changing an instance's security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SG_Changing_Group_Membership) in the *Amazon VPC User Guide*\. You can also change the security groups associated with any other network interface\. For more information, see [Modify network interface attributes](using-eni.md#modify-network-interface-attributes)\.

Security is a shared responsibility between AWS and you\. For more information, see [Security in Amazon EC2](ec2-security.md)\. AWS provides security groups as one of the tools for securing your instances, and you need to configure them to meet your security needs\. If you have requirements that aren't fully met by security groups, you can maintain your own firewall on any of your instances in addition to using security groups\.

To allow traffic to a Windows instance, see [Amazon EC2 security groups for Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/using-network-security.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Contents**
+ [Security group rules](security-group-rules.md)
+ [Connection tracking](security-group-connection-tracking.md)
  + [Throttling](security-group-connection-tracking.md#connection-tracking-throttling)
+ [Default and custom security groups](default-custom-security-groups.md)
  + [Default security groups](default-custom-security-groups.md#default-security-group)
  + [Custom security groups](default-custom-security-groups.md#creating-your-own-security-groups)
+ [Work with security groups](working-with-security-groups.md)
  + [Create a security group](working-with-security-groups.md#creating-security-group)
  + [Copy a security group](working-with-security-groups.md#copy-security-group)
  + [View your security groups](working-with-security-groups.md#describing-security-group)
  + [Add rules to a security group](working-with-security-groups.md#adding-security-group-rule)
  + [Update security group rules](working-with-security-groups.md#updating-security-group-rules)
  + [Delete rules from a security group](working-with-security-groups.md#deleting-security-group-rule)
  + [Delete a security group](working-with-security-groups.md#deleting-security-group)
  + [Assign a security group to an instance](working-with-security-groups.md#assigning-security-group)
  + [Change an instance's security group](working-with-security-groups.md#changing-security-group)
+ [Security group rules for different use cases](security-group-rules-reference.md)
  + [Web server rules](security-group-rules-reference.md#sg-rules-web-server)
  + [Database server rules](security-group-rules-reference.md#sg-rules-db-server)
  + [Rules to connect to instances from your computer](security-group-rules-reference.md#sg-rules-local-access)
  + [Rules to connect to instances from an instance with the same security group](security-group-rules-reference.md#sg-rules-other-instances)
  + [Rules for ping/ICMP](security-group-rules-reference.md#sg-rules-ping)
  + [DNS server rules](security-group-rules-reference.md#sg-rules-dns)
  + [Amazon EFS rules](security-group-rules-reference.md#sg-rules-efs)
  + [Elastic Load Balancing rules](security-group-rules-reference.md#sg-rules-elb)
  + [VPC peering rules](security-group-rules-reference.md#peer-vpc-rules)
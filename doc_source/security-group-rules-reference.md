# Security group rules for different use cases<a name="security-group-rules-reference"></a>

You can create a security group and add rules that reflect the role of the instance that's associated with the security group\. For example, an instance that's configured as a web server needs security group rules that allow inbound HTTP and HTTPS access\. Likewise, a database instance needs rules that allow access for the type of database, such as access over port 3306 for MySQL\.

The following are examples of the kinds of rules that you can add to security groups for specific kinds of access\.

**Topics**
+ [Web server rules](#sg-rules-web-server)
+ [Database server rules](#sg-rules-db-server)
+ [Rules to connect to instances from your computer](#sg-rules-local-access)
+ [Rules to connect to instances from an instance with the same security group](#sg-rules-other-instances)
+ [Rules for ping/ICMP](#sg-rules-ping)
+ [DNS server rules](#sg-rules-dns)
+ [Amazon EFS rules](#sg-rules-efs)
+ [Elastic Load Balancing rules](#sg-rules-elb)
+ [VPC peering rules](#peer-vpc-rules)

## Web server rules<a name="sg-rules-web-server"></a>

The following inbound rules allow HTTP and HTTPS access from any IP address\. If your VPC is enabled for IPv6, you can add rules to control inbound HTTP and HTTPS traffic from IPv6 addresses\.


| Protocol type | Protocol number | Port | Source IP | Notes | 
| --- | --- | --- | --- | --- | 
| TCP | 6 | 80 \(HTTP\) | 0\.0\.0\.0/0 | Allows inbound HTTP access from any IPv4 address | 
| TCP | 6 | 443 \(HTTPS\) | 0\.0\.0\.0/0 | Allows inbound HTTPS access from any IPv4 address | 
| TCP | 6 | 80 \(HTTP\) | ::/0 | Allows inbound HTTP access from any IPv6 address | 
| TCP | 6 | 443 \(HTTPS\) | ::/0 | Allows inbound HTTPS access from any IPv6 address | 

## Database server rules<a name="sg-rules-db-server"></a>

The following inbound rules are examples of rules you might add for database access, depending on what type of database you're running on your instance\. For more information about Amazon RDS instances, see the [Amazon RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)\.

For the source IP, specify one of the following:
+ A specific IP address or range of IP addresses \(in CIDR block notation\) in your local network
+ A security group ID for a group of instances that access the database


| Protocol type | Protocol number | Port | Notes | 
| --- | --- | --- | --- | 
| TCP | 6 | 1433 \(MS SQL\) | The default port to access a Microsoft SQL Server database, for example, on an Amazon RDS instance | 
| TCP | 6 | 3306 \(MYSQL/Aurora\) | The default port to access a MySQL or Aurora database, for example, on an Amazon RDS instance | 
| TCP | 6 | 5439 \(Redshift\) | The default port to access an Amazon Redshift cluster database\. | 
| TCP | 6 | 5432 \(PostgreSQL\) | The default port to access a PostgreSQL database, for example, on an Amazon RDS instance | 
| TCP | 6 | 1521 \(Oracle\) | The default port to access an Oracle database, for example, on an Amazon RDS instance | 

You can optionally restrict outbound traffic from your database servers\. For example, you might want to allow access to the internet for software updates, but restrict all other kinds of traffic\. You must first remove the default outbound rule that allows all outbound traffic\.


| Protocol type | Protocol number | Port | Destination IP | Notes | 
| --- | --- | --- | --- | --- | 
| TCP | 6 | 80 \(HTTP\) | 0\.0\.0\.0/0 | Allows outbound HTTP access to any IPv4 address | 
| TCP | 6 | 443 \(HTTPS\) | 0\.0\.0\.0/0 | Allows outbound HTTPS access to any IPv4 address | 
| TCP | 6 | 80 \(HTTP\) | ::/0 | \(IPv6\-enabled VPC only\) Allows outbound HTTP access to any IPv6 address | 
| TCP | 6 | 443 \(HTTPS\) | ::/0 | \(IPv6\-enabled VPC only\) Allows outbound HTTPS access to any IPv6 address | 

## Rules to connect to instances from your computer<a name="sg-rules-local-access"></a>

To connect to your instance, your security group must have inbound rules that allow SSH access \(for Linux instances\) or RDP access \(for Windows instances\)\.


| Protocol type | Protocol number | Port | Source IP | 
| --- | --- | --- | --- | 
| TCP | 6 | 22 \(SSH\) | The public IPv4 address of your computer, or a range of IP addresses in your local network\. If your VPC is enabled for IPv6 and your instance has an IPv6 address, you can enter an IPv6 address or range\. | 
| TCP | 6 | 3389 \(RDP\) | The public IPv4 address of your computer, or a range of IP addresses in your local network\. If your VPC is enabled for IPv6 and your instance has an IPv6 address, you can enter an IPv6 address or range\. | 

## Rules to connect to instances from an instance with the same security group<a name="sg-rules-other-instances"></a>

To allow instances that are associated with the same security group to communicate with each other, you must explicitly add rules for this\. 

**Note**  
If you configure routes to forward the traffic between two instances in different subnets through a middlebox appliance, you must ensure that the security groups for both instances allow traffic to flow between the instances\. The security group for each instance must reference the private IP address of the other instance, or the CIDR range of the subnet that contains the other instance, as the source\. If you reference the security group of the other instance as the source, this does not allow traffic to flow between the instances\.

The following table describes the inbound rule for a security group that enables associated instances to communicate with each other\. The rule allows all types of traffic\.


| Protocol type | Protocol number | Ports | Source IP | 
| --- | --- | --- | --- | 
| \-1 \(All\) | \-1 \(All\) | \-1 \(All\) | The ID of the security group, or the CIDR range of the subnet that contains the other instance \(see note\)\. | 

## Rules for ping/ICMP<a name="sg-rules-ping"></a>

The ping command is a type of ICMP traffic\. To ping your instance, you must add the following inbound ICMP rule\.


| Protocol type | Protocol number | ICMP type | ICMP code | Source IP | 
| --- | --- | --- | --- | --- | 
| ICMP | 1 | 8 \(Echo Request\) | N/A | The public IPv4 address of your computer, or a range of IPv4 addresses in your local network\. | 

To use the ping6 command to ping the IPv6 address for your instance, you must add the following inbound ICMPv6 rule\.


| Protocol type | Protocol number | ICMP type | ICMP code | Source IP | 
| --- | --- | --- | --- | --- | 
| ICMPv6 | 58 | 128 \(Echo Request\) | 0 | The IPv6 address of your computer, or a range of IPv6 addresses in your local network\. | 

## DNS server rules<a name="sg-rules-dns"></a>

If you've set up your EC2 instance as a DNS server, you must ensure that TCP and UDP traffic can reach your DNS server over port 53\. 

For the source IP, specify one of the following:
+ An IP address or range of IP addresses \(in CIDR block notation\) in a network
+ The ID of a security group for the set of instances in your network that require access to the DNS server


| Protocol type | Protocol number | Port | 
| --- | --- | --- | 
| TCP | 6 | 53 | 
| UDP | 17 | 53 | 

## Amazon EFS rules<a name="sg-rules-efs"></a>

If you're using an Amazon EFS file system with your Amazon EC2 instances, the security group that you associate with your Amazon EFS mount targets must allow traffic over the NFS protocol\. 


| Protocol type | Protocol number | Ports | Source IP | Notes | 
| --- | --- | --- | --- | --- | 
| TCP | 6 | 2049 \(NFS\) | The ID of the security group | Allows inbound NFS access from resources \(including the mount target\) associated with this security group | 

To mount an Amazon EFS file system on your Amazon EC2 instance, you must connect to your instance\. Therefore, the security group associated with your instance must have rules that allow inbound SSH from your local computer or local network\.


| Protocol type | Protocol number | Ports | Source IP | Notes | 
| --- | --- | --- | --- | --- | 
| TCP | 6 | 22 \(SSH\) | The IP address range of your local computer, or the range of IP addresses \(in CIDR block notation\) for your network\. | Allows inbound SSH access from your local computer\. | 

## Elastic Load Balancing rules<a name="sg-rules-elb"></a>

If you're using a load balancer, the security group associated with your load balancer must have rules that allow communication with your instances or targets\.


| 
| 
| **Inbound** | 
| --- |
| Protocol type | Protocol number | Port | Source IP | Notes | 
| TCP | 6 | The listener port |  For an Internet\-facing load\-balancer: 0\.0\.0\.0/0 \(all IPv4 addresses\) For an internal load\-balancer: the IPv4 CIDR block of the VPC  | Allow inbound traffic on the load balancer listener port\. | 
| **Outbound** | 
| --- |
| Protocol type | Protocol number | Port | Destination IP | Notes | 
| TCP | 6 | The instance listener port | The ID of the instance security group | Allow outbound traffic to instances on the instance listener port\. | 
| TCP | 6 | The health check port | The ID of the instance security group | Allow outbound traffic to instances on the health check port\. | 

The security group rules for your instances must allow the load balancer to communicate with your instances on both the listener port and the health check port\.


| 
| 
| **Inbound** | 
| --- |
| Protocol type | Protocol number | Port | Source IP | Notes | 
| TCP | 6 | The instance listener port |  The ID of the load balancer security group  | Allow traffic from the load balancer on the instance listener port\. | 
| TCP | 6 | The health check port | The ID of the load balancer security group | Allow traffic from the load balancer on the health check port\. | 

For more information, see [Configure security groups for your Classic Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-security-groups.html) in the *User Guide for Classic Load Balancers*, and [Security groups for your Application Load Balancer](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-update-security-groups.html) in the *User Guide for Application Load Balancers*\.

## VPC peering rules<a name="peer-vpc-rules"></a>

You can update the inbound or outbound rules for your VPC security groups to reference security groups in the peered VPC\. Doing so allows traffic to flow to and from instances that are associated with the referenced security group in the peered VPC\. For more information about how to configure security groups for VPC peering, see [Updating your security groups to reference peer VPC groups](https://docs.aws.amazon.com/vpc/latest/peering/vpc-peering-security-groups.html)\.
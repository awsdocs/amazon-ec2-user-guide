# Infrastructure security in Amazon EC2<a name="infrastructure-security"></a>

You use AWS published API calls to access Amazon EC2 through the network\. Clients must support Transport Layer Security \(TLS\) 1\.0 or later\. We recommend TLS 1\.2 or later\. Clients must also support cipher suites with perfect forward secrecy \(PFS\) such as Ephemeral Diffie\-Hellman \(DHE\) or Elliptic Curve Ephemeral Diffie\-Hellman \(ECDHE\)\. Most modern systems such as Java 7 and later support these modes\.

Additionally, requests must be signed using an access key ID and a secret access key that is associated with an IAM principal\. Or you can use the [AWS Security Token Service](https://docs.aws.amazon.com/STS/latest/APIReference/Welcome.html) \(AWS STS\) to generate temporary security credentials to sign requests\.

## Network isolation<a name="network-isolation"></a>

A virtual private cloud \(VPC\) is a virtual network in your own logically isolated area in the AWS Cloud\. Use separate VPCs to isolate infrastructure by workload or organizational entity\.

A subnet is a range of IP addresses in a VPC\. When you launch an instance, you launch it into a subnet in your VPC\. Use subnets to isolate the tiers of your application \(for example, web, application, and database\) within a single VPC\. Use private subnets for your instances if they should not be accessed directly from the internet\.

To call the Amazon EC2 API from your VPC without sending traffic over the public internet, use AWS PrivateLink\.

## Isolation on physical hosts<a name="physical-isolation"></a>

Different EC2 instances on the same physical host are isolated from each other as though they are on separate physical hosts\. The hypervisor isolates CPU and memory, and the instances are provided virtualized disks instead of access to the raw disk devices\.

When you stop or terminate an instance, the memory allocated to it is scrubbed \(set to zero\) by the hypervisor before it is allocated to a new instance, and every block of storage is reset\. This ensures that your data is not unintentionally exposed to another instance\.

Network MAC addresses are dynamically assigned to instances by the AWS network infrastructure\. IP addresses are either dynamically assigned to instances by the AWS network infrastructure, or assigned by an EC2 administrator through authenticated API requests\. The AWS network allows instances to send traffic only from the MAC and IP addresses assigned to them\. Otherwise, the traffic is dropped\.

By default, an instance cannot receive traffic that is not specifically addressed to it\. If you need to run network address translation \(NAT\), routing, or firewall services on your instance, you can disable source/destination checking for the network interface\.

## Controlling network traffic<a name="control-network-traffic"></a>

Consider the following options for controlling network traffic to your EC2 instances:
+ Restrict access to your instances using [security groups](ec2-security-groups.md)\. For example, you can allow traffic only from the address ranges for your corporate network\.
+ Use private subnets for your instances if they should not be accessed directly from the internet\. Use a bastion host or NAT gateway for internet access from an instance in a private subnet\.
+ Use AWS Virtual Private Network or AWS Direct Connect to establish private connections from your remote networks to your VPCs\. For more information, see [Network\-to\-Amazon VPC Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html)\.
+ Use [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) to monitor the traffic that reaches your instances\.
+ Use [AWS Security Hub](http://aws.amazon.com/security-hub/) to check for unintended network accessibility from your instances\.
+ Use [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md) to connect to your instances using Secure Shell \(SSH\) without the need to share and manage SSH keys\.
+ Use [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) to access your instances remotely instead of opening inbound SSH ports and managing SSH keys\.
+ Use [AWS Systems Manager Run Command](https://docs.aws.amazon.com/systems-manager/latest/userguide/execute-remote-commands.html) to automate common administrative tasks instead of opening inbound SSH ports and managing SSH keys\.

In addition to restricting network access to each Amazon EC2 instance, Amazon VPC supports implementing additional network security controls like in\-line gateways, proxy servers, and various network monitoring options\.

For more information, see the [AWS Security Best Practices](https://d1.awsstatic.com/whitepapers/Security/AWS_Security_Best_Practices.pdf) whitepaper\.
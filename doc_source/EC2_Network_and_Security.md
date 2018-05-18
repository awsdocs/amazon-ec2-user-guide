# Network and Security<a name="EC2_Network_and_Security"></a>

Amazon EC2 provides the following network and security features\.

**Topics**
+ [Amazon EC2 Key Pairs](ec2-key-pairs.md)
+ [Amazon EC2 Security Groups for Linux Instances](using-network-security.md)
+ [Controlling Access to Amazon EC2 Resources](UsingIAM.md)
+ [Amazon EC2 and Amazon Virtual Private Cloud](using-vpc.md)
+ [Amazon EC2 Instance IP Addressing](using-instance-addressing.md)
+ [Elastic IP Addresses](elastic-ip-addresses-eip.md)
+ [Elastic Network Interfaces](using-eni.md)
+ [Placement Groups](placement-groups.md)
+ [Network Maximum Transmission Unit \(MTU\) for Your EC2 Instance](network_mtu.md)
+ [Enhanced Networking on Linux](enhanced-networking.md)

If you access Amazon EC2 using the command line tools or an API, you'll need your access key ID and secret access key\. For more information, see [How Do I Get Security Credentials?](http://docs.aws.amazon.com/general/latest/gr/getting-aws-sec-creds.html) in the *Amazon Web Services General Reference*\.

You can launch an instance into one of two platforms: EC2\-Classic or EC2\-VPC\. An instance that's launched into EC2\-Classic or a default VPC is automatically assigned a public IP address\. An instance that's launched into a nondefault VPC can be assigned a public IP address on launch\. For more information about EC2\-Classic and EC2\-VPC, see [Supported Platforms](ec2-supported-platforms.md)\.

Instances can fail or terminate for reasons outside of your control\. If an instance fails and you launch a replacement instance, the replacement has a different public IP address than the original\. However, if your application needs a static IP address, you can use an *Elastic IP address*\.

You can use *security groups* to control who can access your instances\. These are analogous to an inbound network firewall that enables you to specify the protocols, ports, and source IP ranges that are allowed to reach your instances\. You can create multiple security groups and assign different rules to each group\. You can then assign each instance to one or more security groups, and we use the rules to determine which traffic is allowed to reach the instance\. You can configure a security group so that only specific IP addresses or specific security groups have access to the instance\.
# Networking in Amazon EC2<a name="ec2-networking"></a>

Amazon VPC enables you to launch AWS resources, such as Amazon EC2 instances, into a virtual network dedicated to your AWS account, known as a virtual private cloud \(VPC\)\. When you launch an instance, you can select a subnet from the VPC\. The instance is configured with a primary network interface, which is a logical virtual network card\. The instance receives a primary private IP address from the IPv4 address of the subnet, and it is assigned to the primary network interface\.

You can control whether the instance receives a public IP address from Amazon's pool of public IP addresses\. The public IP address of an instance is associated with your instance only until it is stopped or terminated\. If you require a persistent public IP address, you can allocate an Elastic IP address for your AWS account and associate it with an instance or a network interface\. An Elastic IP address remains associated with your AWS account until you release it, and you can move it from one instance to another as needed\. You can bring your own IP address range to your AWS account, where it appears as an address pool, and then allocate Elastic IP addresses from your address pool\.

To increase network performance and reduce latency, you can launch instances in a placement group\. You can get significantly higher packet per second \(PPS\) performance using enhanced networking\. You can accelerate high performance computing and machine learning applications using an Elastic Fabric Adapter \(EFA\), which is a network device that you can attach to a supported instance type\.

**Topics**
+ [Regions and Zones](using-regions-availability-zones.md)
+ [Instance IP addressing](using-instance-addressing.md)
+ [Bring your own IP addresses](ec2-byoip.md)
+ [Assigning prefixes](ec2-prefix-eni.md)
+ [Elastic IP addresses](elastic-ip-addresses-eip.md)
+ [Network interfaces](using-eni.md)
+ [Network bandwidth](ec2-instance-network-bandwidth.md)
+ [Enhanced networking](enhanced-networking.md)
+ [Elastic Fabric Adapter](efa.md)
+ [Placement groups](placement-groups.md)
+ [Network MTU](network_mtu.md)
+ [Virtual private clouds](using-vpc.md)
+ [EC2\-Classic](ec2-classic-platform.md)
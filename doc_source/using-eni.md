# Elastic network interfaces<a name="using-eni"></a>

An *elastic network interface* is a logical networking component in a VPC that represents a virtual network card\. It can include the following attributes:
+ A primary private IPv4 address from the IPv4 address range of your VPC
+ One or more secondary private IPv4 addresses from the IPv4 address range of your VPC
+ One Elastic IP address \(IPv4\) per private IPv4 address
+ One public IPv4 address
+ One or more IPv6 addresses
+ One or more security groups
+ A MAC address
+ A source/destination check flag
+ A description

You can create and configure network interfaces and attach them to instances in the same Availability Zone\. Your account might also have *requester\-managed* network interfaces, which are created and managed by AWS services to enable you to use other resources and services\. You cannot manage these network interfaces yourself\. For more information, see [Requester\-managed network interfaces](requester-managed-eni.md)\.

This AWS resource is referred to as a *network interface* in the AWS Management Console and the Amazon EC2 API\. Therefore, we use "network interface" in this documentation instead of "elastic network interface"\. The term "network interface" in this documentation always means "elastic network interface"\.

**Topics**
+ [Network interface basics](#eni-basics)
+ [Network cards](#network-cards)
+ [IP addresses per network interface per instance type](#AvailableIpPerENI)
+ [Work with network interfaces](#working-with-enis)
+ [Best practices for configuring network interfaces](best-practices-for-configuring-network-interfaces.md)
+ [Scenarios for network interfaces](scenarios-enis.md)
+ [Requester\-managed network interfaces](requester-managed-eni.md)

## Network interface basics<a name="eni-basics"></a>

You can create a network interface, attach it to an instance, detach it from an instance, and attach it to another instance\. The attributes of a network interface follow it as it's attached or detached from an instance and reattached to another instance\. When you move a network interface from one instance to another, network traffic is redirected to the new instance\.

**Primary network interface**  
Each instance has a default network interface, called the *primary network interface*\. You cannot detach a primary network interface from an instance\. You can create and attach additional network interfaces\. The maximum number of network interfaces that you can use varies by instance type\. For more information, see [IP addresses per network interface per instance type](#AvailableIpPerENI)\.

**Public IPv4 addresses for network interfaces**  
In a VPC, all subnets have a modifiable attribute that determines whether network interfaces created in that subnet \(and therefore instances launched into that subnet\) are assigned a public IPv4 address\. For more information, see [IP addressing behavior for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#vpc-ip-addressing-subnet) in the *Amazon VPC User Guide*\. The public IPv4 address is assigned from Amazon's pool of public IPv4 addresses\. When you launch an instance, the IP address is assigned to the primary network interface that's created\.

When you create a network interface, it inherits the public IPv4 addressing attribute from the subnet\. If you later modify the public IPv4 addressing attribute of the subnet, the network interface keeps the setting that was in effect when it was created\. If you launch an instance and specify an existing network interface as the primary network interface, the public IPv4 address attribute is determined by this network interface\.

For more information, see [Public IPv4 addresses](using-instance-addressing.md#concepts-public-addresses)\.

**Elastic IP addresses for network interface**  
If you have an Elastic IP address, you can associate it with one of the private IPv4 addresses for the network interface\. You can associate one Elastic IP address with each private IPv4 address\.

If you disassociate an Elastic IP address from a network interface, you can release it back to the address pool\. This is the only way to associate an Elastic IP address with an instance in a different subnet or VPC, as network interfaces are specific to subnets\.

**IPv6 addresses for network interfaces**  
If you associate IPv6 CIDR blocks with your VPC and subnet, you can assign one or more IPv6 addresses from the subnet range to a network interface\. Each IPv6 address can be assigned to one network interface\.

All subnets have a modifiable attribute that determines whether network interfaces created in that subnet \(and therefore instances launched into that subnet\) are automatically assigned an IPv6 address from the range of the subnet\. For more information, see [IP addressing behavior for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#vpc-ip-addressing-subnet) in the *Amazon VPC User Guide*\. When you launch an instance, the IPv6 address is assigned to the primary network interface that's created\.

For more information, see [IPv6 addresses](using-instance-addressing.md#ipv6-addressing)\.

**Prefix Delegation**  
 A Prefix Delegation prefix is a reserved private IPv4 or IPv6 CIDR range that you allocate for automatic or manual assignment to network interfaces that are associated with an instance\. By using Delegated Prefixes, you can launch services faster by assigning a range of IP addresses as a single prefix\. 

**Termination behavior**  
You can set the termination behavior for a network interface that's attached to an instance\. You can specify whether the network interface should be automatically deleted when you terminate the instance to which it's attached\.

**Source/destination checking**  
You can enable or disable source/destination checks, which ensure that the instance is either the source or the destination of any traffic that it receives\. Source/destination checks are enabled by default\. You must disable source/destination checks if the instance runs services such as network address translation, routing, or firewalls\.

**Monitoring IP traffic**  
You can enable a VPC flow log on your network interface to capture information about the IP traffic going to and from a network interface\. After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

## Network cards<a name="network-cards"></a>

Instances with multiple network cards provide higher network performance, including bandwidth capabilities above 100 Gbps and improved packet rate performance\. Each network interface is attached to a network card\. The primary network interface must be assigned to network card index 0\.

If you enable Elastic Fabric Adapter \(EFA\) when you launch an instance that supports multiple network cards, all network cards are available\. You can assign up to one EFA per network card\. An EFA counts as a network interface\.

The following instances support multiple network cards\. All other instance types support one network card\.


| Instance type | Number of network cards | 
| --- | --- | 
| dl1\.24xlarge | 4 | 
| p4d\.24xlarge | 4 | 

## IP addresses per network interface per instance type<a name="AvailableIpPerENI"></a>

The following table lists the maximum number of network interfaces per instance type, and the maximum number of private IPv4 addresses and IPv6 addresses per network interface\. The limit for IPv6 addresses is separate from the limit for private IPv4 addresses per network interface\. Not all instance types support IPv6 addressing\.


| Instance type | Maximum network interfaces | Private IPv4 addresses per interface | IPv6 addresses per interface | 
| --- | --- | --- | --- | 
| a1\.medium | 2 | 4 | 4 | 
| a1\.large | 3 | 10 | 10 | 
| a1\.xlarge | 4 | 15 | 15 | 
| a1\.2xlarge | 4 | 15 | 15 | 
| a1\.4xlarge | 8 | 30 | 30 | 
| a1\.metal | 8 | 30 | 30 | 
| c1\.medium |  2  |  6  | IPv6 not supported | 
| c1\.xlarge |  4  |  15  | IPv6 not supported | 
| c3\.large |  3  |  10  | 10 | 
| c3\.xlarge |  4  |  15  | 15 | 
| c3\.2xlarge |  4  |  15  | 15 | 
| c3\.4xlarge |  8  |  30  | 30 | 
| c3\.8xlarge |  8  |  30  | 30 | 
| c4\.large |  3  |  10  | 10 | 
| c4\.xlarge |  4  |  15  | 15 | 
| c4\.2xlarge |  4  |  15  | 15 | 
| c4\.4xlarge |  8  |  30  | 30 | 
| c4\.8xlarge |  8  |  30  | 30 | 
| c5\.large | 3 | 10 | 10 | 
| c5\.xlarge | 4 | 15 | 15 | 
| c5\.2xlarge | 4 | 15 | 15 | 
| c5\.4xlarge | 8 | 30 | 30 | 
| c5\.9xlarge | 8 | 30 | 30 | 
| c5\.12xlarge | 8 | 30 | 30 | 
| c5\.18xlarge | 15 | 50 | 50 | 
| c5\.24xlarge | 15 | 50 | 50 | 
| c5\.metal | 15 | 50 | 50 | 
| c5a\.large | 3 | 10 | 10 | 
| c5a\.xlarge | 4 | 15 | 15 | 
| c5a\.2xlarge | 4 | 15 | 15 | 
| c5a\.4xlarge | 8 | 30 | 30 | 
| c5a\.8xlarge | 8 | 30 | 30 | 
| c5a\.12xlarge | 8 | 30 | 30 | 
| c5a\.16xlarge | 15 | 50 | 50 | 
| c5a\.24xlarge | 15 | 50 | 50 | 
| c5ad\.large | 3 | 10 | 10 | 
| c5ad\.xlarge | 4 | 15 | 15 | 
| c5ad\.2xlarge | 4 | 15 | 15 | 
| c5ad\.4xlarge | 8 | 30 | 30 | 
| c5ad\.8xlarge | 8 | 30 | 30 | 
| c5ad\.12xlarge | 8 | 30 | 30 | 
| c5ad\.16xlarge | 15 | 50 | 50 | 
| c5ad\.24xlarge | 15 | 50 | 50 | 
| c5d\.large | 3 | 10 | 10 | 
| c5d\.xlarge | 4 | 15 | 15 | 
| c5d\.2xlarge | 4 | 15 | 15 | 
| c5d\.4xlarge | 8 | 30 | 30 | 
| c5d\.9xlarge | 8 | 30 | 30 | 
| c5d\.12xlarge | 8 | 30 | 30 | 
| c5d\.18xlarge | 15 | 50 | 50 | 
| c5d\.24xlarge | 15 | 50 | 50 | 
| c5d\.metal | 15 | 50 | 50 | 
| c5n\.large | 3 | 10 | 10 | 
| c5n\.xlarge | 4 | 15 | 15 | 
| c5n\.2xlarge | 4 | 15 | 15 | 
| c5n\.4xlarge | 8 | 30 | 30 | 
| c5n\.9xlarge | 8 | 30 | 30 | 
| c5n\.18xlarge | 15 | 50 | 50 | 
| c5n\.metal | 15 | 50 | 50 | 
| c6a\.large | 3 | 10 | 10 | 
| c6a\.xlarge | 4 | 15 | 15 | 
| c6a\.2xlarge | 4 | 15 | 15 | 
| c6a\.4xlarge | 8 | 30 | 30 | 
| c6a\.8xlarge | 8 | 30 | 30 | 
| c6a\.12xlarge | 8 | 30 | 30 | 
| c6a\.16xlarge | 15 | 50 | 50 | 
| c6a\.24xlarge | 15 | 50 | 50 | 
| c6a\.32xlarge | 15 | 50 | 50 | 
| c6a\.48xlarge | 15 | 50 | 50 | 
| c6a\.metal | 15 | 50 | 50 | 
| c6g\.medium | 2 | 4 | 4 | 
| c6g\.large | 3 | 10 | 10 | 
| c6g\.xlarge | 4 | 15 | 15 | 
| c6g\.2xlarge | 4 | 15 | 15 | 
| c6g\.4xlarge | 8 | 30 | 30 | 
| c6g\.8xlarge | 8 | 30 | 30 | 
| c6g\.12xlarge | 8 | 30 | 30 | 
| c6g\.16xlarge | 15 | 50 | 50 | 
| c6g\.metal | 15 | 50 | 50 | 
| c6gd\.medium | 2 | 4 | 4 | 
| c6gd\.large | 3 | 10 | 10 | 
| c6gd\.xlarge | 4 | 15 | 15 | 
| c6gd\.2xlarge | 4 | 15 | 15 | 
| c6gd\.4xlarge | 8 | 30 | 30 | 
| c6gd\.8xlarge | 8 | 30 | 30 | 
| c6gd\.12xlarge | 8 | 30 | 30 | 
| c6gd\.16xlarge | 15 | 50 | 50 | 
| c6gd\.metal | 15 | 50 | 50 | 
| c6gn\.medium | 2 | 4 | 4 | 
| c6gn\.large | 3 | 10 | 10 | 
| c6gn\.xlarge | 4 | 15 | 15 | 
| c6gn\.2xlarge | 4 | 15 | 15 | 
| c6gn\.4xlarge | 8 | 30 | 30 | 
| c6gn\.8xlarge | 8 | 30 | 30 | 
| c6gn\.12xlarge | 8 | 30 | 30 | 
| c6gn\.16xlarge | 15 | 50 | 50 | 
| c6i\.large | 3 | 10 | 10 | 
| c6i\.xlarge | 4 | 15 | 15 | 
| c6i\.2xlarge | 4 | 15 | 15 | 
| c6i\.4xlarge | 8 | 30 | 30 | 
| c6i\.8xlarge | 8 | 30 | 30 | 
| c6i\.12xlarge | 8 | 30 | 30 | 
| c6i\.16xlarge | 15 | 50 | 50 | 
| c6i\.24xlarge | 15 | 50 | 50 | 
| c6i\.32xlarge | 15 | 50 | 50 | 
| c6i\.metal | 15 | 50 | 50 | 
| cc2\.8xlarge |  8  |  30  | IPv6 not supported | 
| cr1\.8xlarge |  8  |  30  | IPv6 not supported | 
| d2\.xlarge |  4  |  15  | 15 | 
| d2\.2xlarge |  4  |  15  | 15 | 
| d2\.4xlarge |  8  |  30  | 30 | 
| d2\.8xlarge |  8  |  30  | 30 | 
| d3\.xlarge | 4 | 3 | 3 | 
| d3\.2xlarge | 4 | 5 | 5 | 
| d3\.4xlarge | 4 | 10 | 10 | 
| d3\.8xlarge | 3 | 20 | 20 | 
| d3en\.large | 4 | 2 | 2 | 
| d3en\.xlarge | 4 | 3 | 3 | 
| d3en\.2xlarge | 4 | 5 | 5 | 
| d3en\.4xlarge | 4 | 10 | 10 | 
| d3en\.6large | 4 | 15 | 15 | 
| d3en\.8xlarge | 4 | 20 | 20 | 
| d3en\.12xlarge | 3 | 30 | 30 | 
| dl1\.24xlarge | 15 per network card \(15, 30, 45, or 60\) | 50 | 50 | 
| f1\.2xlarge |  4  | 15  |  15  | 
| f1\.4xlarge |  8  | 30  |  30  | 
| f1\.16xlarge | 8 | 50 | 50 | 
| g2\.2xlarge |  4  |  15  | IPv6 not supported | 
| g2\.8xlarge |  8  |  30  | IPv6 not supported | 
| g3s\.xlarge | 4 | 15 | 15 | 
| g3\.4xlarge | 8 | 30 | 30 | 
| g3\.8xlarge | 8 | 30 | 30 | 
| g3\.16xlarge | 15 | 50 | 50 | 
| g4ad\.xlarge | 2 | 4 | 4 | 
| g4ad\.2xlarge | 2 | 4 | 4 | 
| g4ad\.4xlarge | 3 | 10 | 10 | 
| g4ad\.8xlarge | 4 | 15 | 15 | 
| g4ad\.16xlarge | 8 | 30 | 30 | 
| g4dn\.xlarge | 3 | 10 | 10 | 
| g4dn\.2xlarge | 3 | 10 | 10 | 
| g4dn\.4xlarge | 3 | 10 | 10 | 
| g4dn\.8xlarge | 4 | 15 | 15 | 
| g4dn\.12xlarge | 8 | 30 | 30 | 
| g4dn\.16xlarge | 4 | 15 | 15 | 
| g4dn\.metal | 15 | 50 | 50 | 
| g5\.xlarge | 4 | 15 | 15 | 
| g5\.2xlarge | 4 | 15 | 15 | 
| g5\.4xlarge | 8 | 30 | 30 | 
| g5\.8xlarge | 8 | 30 | 30 | 
| g5\.12xlarge | 15 | 50 | 50 | 
| g5\.16xlarge | 8 | 30 | 30 | 
| g5\.24xlarge | 15 | 50 | 50 | 
| g5\.48xlarge | 15 | 50 | 50 | 
| g5g\.xlarge | 4 | 15 | 15 | 
| g5g\.2xlarge | 4 | 15 | 15 | 
| g5g\.4xlarge | 8 | 30 | 30 | 
| g5g\.8xlarge | 8 | 30 | 30 | 
| g5g\.16xlarge | 15 | 50 | 50 | 
| h1\.2xlarge | 4 | 15 | 15 | 
| h1\.4xlarge | 8 | 30 | 30 | 
| h1\.8xlarge | 8 | 30 | 30 | 
| h1\.16xlarge | 15 | 50 | 50 | 
| hs1\.8xlarge |  8  |  30  | IPv6 not supported | 
| hpc6a\.48xlarge | 2 | 50 | 50 | 
| i2\.xlarge |  4  |  15  | 15 | 
| i2\.2xlarge |  4  |  15  | 15 | 
| i2\.4xlarge |  8  |  30  | 30 | 
| i2\.8xlarge |  8  |  30  | 30 | 
| i3\.large | 3 | 10 | 10 | 
| i3\.xlarge | 4 | 15 | 15 | 
| i3\.2xlarge | 4 | 15 | 15 | 
| i3\.4xlarge | 8 | 30 | 30 | 
| i3\.8xlarge | 8 | 30 | 30 | 
| i3\.16xlarge | 15 | 50 | 50 | 
| i3\.metal | 15 | 50 | 50 | 
| i3en\.large | 3 | 10 | 10 | 
| i3en\.xlarge | 4 | 15 | 15 | 
| i3en\.2xlarge | 4 | 15 | 15 | 
| i3en\.3xlarge | 4 | 15 | 15 | 
| i3en\.6xlarge | 8 | 30 | 30 | 
| i3en\.12xlarge | 8 | 30 | 30 | 
| i3en\.24xlarge | 15 | 50 | 50 | 
| i3en\.metal | 15 | 50 | 50 | 
| i4i\.large | 3 | 10 | 10 | 
| i4i\.xlarge | 4 | 15 | 15 | 
| i4i\.2xlarge | 4 | 15 | 15 | 
| i4i\.4xlarge | 8 | 30 | 30 | 
| i4i\.8xlarge | 8 | 30 | 30 | 
| i4i\.16xlarge | 15 | 50 | 50 | 
| i4i\.32xlarge | 15 | 50 | 50 | 
| im4gn\.large | 3 | 10 | 10 | 
| im4gn\.xlarge | 4 | 15 | 15 | 
| im4gn\.2xlarge | 4 | 15 | 15 | 
| im4gn\.4xlarge | 8 | 30 | 30 | 
| im4gn\.8xlarge | 8 | 30 | 30 | 
| im4gn\.16xlarge | 15 | 50 | 50 | 
| inf1\.xlarge | 4 | 10 | 10 | 
| inf1\.2xlarge | 4 | 10 | 10 | 
| inf1\.6xlarge | 8 | 30 | 30 | 
| inf1\.24xlarge | 15 | 30 | 30 | 
| is4gen\.medium | 2 | 4 | 4 | 
| is4gen\.large | 3 | 10 | 10 | 
| is4gen\.xlarge | 4 | 15 | 15 | 
| is4gen\.2xlarge | 4 | 15 | 15 | 
| is4gen\.4xlarge | 8 | 30 | 30 | 
| is4gen\.8xlarge | 8 | 30 | 30 | 
| m1\.small |  2  |  4  | IPv6 not supported | 
| m1\.medium |  2  |  6  | IPv6 not supported | 
| m1\.large |  3  |  10  | IPv6 not supported | 
| m1\.xlarge |  4  |  15  | IPv6 not supported | 
| m2\.xlarge |  4  |  15  | IPv6 not supported | 
| m2\.2xlarge |  4  |  30  | IPv6 not supported | 
| m2\.4xlarge |  8  |  30  | IPv6 not supported | 
| m3\.medium |  2  |  6  | IPv6 not supported | 
| m3\.large |  3  |  10  | IPv6 not supported | 
| m3\.xlarge |  4  |  15  | IPv6 not supported | 
| m3\.2xlarge |  4  |  30  | IPv6 not supported | 
| m4\.large | 2 | 10 | 10 | 
| m4\.xlarge | 4 | 15 | 15 | 
| m4\.2xlarge | 4 | 15 | 15 | 
| m4\.4xlarge | 8 | 30 | 30 | 
| m4\.10xlarge | 8 | 30 | 30 | 
| m4\.16xlarge | 8 | 30 | 30 | 
| m5\.large | 3 | 10 | 10 | 
| m5\.xlarge | 4 | 15 | 15 | 
| m5\.2xlarge | 4 | 15 | 15 | 
| m5\.4xlarge | 8 | 30 | 30 | 
| m5\.8xlarge | 8 | 30 | 30 | 
| m5\.12xlarge | 8 | 30 | 30 | 
| m5\.16xlarge | 15 | 50 | 50 | 
| m5\.24xlarge | 15 | 50 | 50 | 
| m5\.metal | 15 | 50 | 50 | 
| m5a\.large | 3 | 10 | 10 | 
| m5a\.xlarge | 4 | 15 | 15 | 
| m5a\.2xlarge | 4 | 15 | 15 | 
| m5a\.4xlarge | 8 | 30 | 30 | 
| m5a\.8xlarge | 8 | 30 | 30 | 
| m5a\.12xlarge | 8 | 30 | 30 | 
| m5a\.16xlarge | 15 | 50 | 50 | 
| m5a\.24xlarge | 15 | 50 | 50 | 
| m5ad\.large | 3 | 10 | 10 | 
| m5ad\.xlarge | 4 | 15 | 15 | 
| m5ad\.2xlarge | 4 | 15 | 15 | 
| m5ad\.4xlarge | 8 | 30 | 30 | 
| m5ad\.8xlarge | 8 | 30 | 30 | 
| m5ad\.12xlarge | 8 | 30 | 30 | 
| m5ad\.16xlarge | 15 | 50 | 50 | 
| m5ad\.24xlarge | 15 | 50 | 50 | 
| m5d\.large | 3 | 10 | 10 | 
| m5d\.xlarge | 4 | 15 | 15 | 
| m5d\.2xlarge | 4 | 15 | 15 | 
| m5d\.4xlarge | 8 | 30 | 30 | 
| m5d\.8xlarge | 8 | 30 | 30 | 
| m5d\.12xlarge | 8 | 30 | 30 | 
| m5d\.16xlarge | 15 | 50 | 50 | 
| m5d\.24xlarge | 15 | 50 | 50 | 
| m5d\.metal | 15 | 50 | 50 | 
| m5dn\.large | 3 | 10 | 10 | 
| m5dn\.xlarge | 4 | 15 | 15 | 
| m5dn\.2xlarge | 4 | 15 | 15 | 
| m5dn\.4xlarge | 8 | 30 | 30 | 
| m5dn\.8xlarge | 8 | 30 | 30 | 
| m5dn\.12xlarge | 8 | 30 | 30 | 
| m5dn\.16xlarge | 15 | 50 | 50 | 
| m5dn\.24xlarge | 15 | 50 | 50 | 
| m5dn\.metal | 15 | 50 | 50 | 
| m5n\.large | 3 | 10 | 10 | 
| m5n\.xlarge | 4 | 15 | 15 | 
| m5n\.2xlarge | 4 | 15 | 15 | 
| m5n\.4xlarge | 8 | 30 | 30 | 
| m5n\.8xlarge | 8 | 30 | 30 | 
| m5n\.12xlarge | 8 | 30 | 30 | 
| m5n\.16xlarge | 15 | 50 | 50 | 
| m5n\.24xlarge | 15 | 50 | 50 | 
| m5n\.metal | 15 | 50 | 50 | 
| m5zn\.large | 3 | 10 | 10 | 
| m5zn\.xlarge | 4 | 15 | 15 | 
| m5zn\.2xlarge | 4 | 15 | 15 | 
| m5zn\.3xlarge | 8 | 30 | 30 | 
| m5zn\.6xlarge | 8 | 30 | 30 | 
| m5zn\.12xlarge | 15 | 50 | 50 | 
| m5zn\.metal | 15 | 50 | 50 | 
| m6a\.large | 3 | 10 | 10 | 
| m6a\.xlarge | 4 | 15 | 15 | 
| m6a\.2xlarge | 4 | 15 | 15 | 
| m6a\.4xlarge | 8 | 30 | 30 | 
| m6a\.8xlarge | 8 | 30 | 30 | 
| m6a\.12xlarge | 8 | 30 | 30 | 
| m6a\.16xlarge | 15 | 50 | 50 | 
| m6a\.24xlarge | 15 | 50 | 50 | 
| m6a\.32xlarge | 15 | 50 | 50 | 
| m6a\.48xlarge | 15 | 50 | 50 | 
| m6a\.metal | 15 | 50 | 50 | 
| m6g\.medium | 2 | 4 | 4 | 
| m6g\.large | 3 | 10 | 10 | 
| m6g\.xlarge | 4 | 15 | 15 | 
| m6g\.2xlarge | 4 | 15 | 15 | 
| m6g\.4xlarge | 8 | 30 | 30 | 
| m6g\.8xlarge | 8 | 30 | 30 | 
| m6g\.12xlarge | 8 | 30 | 30 | 
| m6g\.16xlarge | 15 | 50 | 50 | 
| m6g\.metal | 15 | 50 | 50 | 
| m6gd\.medium | 2 | 4 | 4 | 
| m6gd\.large | 3 | 10 | 10 | 
| m6gd\.xlarge | 4 | 15 | 15 | 
| m6gd\.2xlarge | 4 | 15 | 15 | 
| m6gd\.4xlarge | 8 | 30 | 30 | 
| m6gd\.8xlarge | 8 | 30 | 30 | 
| m6gd\.12xlarge | 8 | 30 | 30 | 
| m6gd\.16xlarge | 15 | 50 | 50 | 
| m6gd\.metal | 15 | 50 | 50 | 
| m6i\.large | 3 | 10 | 10 | 
| m6i\.xlarge | 4 | 15 | 15 | 
| m6i\.2xlarge | 4 | 15 | 15 | 
| m6i\.4xlarge | 8 | 30 | 30 | 
| m6i\.8xlarge | 8 | 30 | 30 | 
| m6i\.12xlarge | 8 | 30 | 30 | 
| m6i\.16xlarge | 15 | 50 | 50 | 
| m6i\.24xlarge | 15 | 50 | 50 | 
| m6i\.32xlarge | 15 | 50 | 50 | 
| m6i\.metal | 15 | 50 | 50 | 
| mac1\.metal | 8 | 30 | 30 | 
| p2\.xlarge | 4 | 15 | 15 | 
| p2\.8xlarge | 8 | 30 | 30 | 
| p2\.16xlarge | 8 | 30 | 30 | 
| p3\.2xlarge | 4 | 15 | 15 | 
| p3\.8xlarge | 8 | 30 | 30 | 
| p3\.16xlarge | 8 | 30 | 30 | 
| p3dn\.24xlarge | 15 | 50 | 50 | 
| p4d\.24xlarge | 15 per network card \(15, 30, 45, or 60\) | 50 | 50 | 
| r3\.large | 3 | 10 | 10 | 
| r3\.xlarge | 4 | 15 | 15 | 
| r3\.2xlarge | 4 | 15 | 15 | 
| r3\.4xlarge | 8 | 30 | 30 | 
| r3\.8xlarge | 8 | 30 | 30 | 
| r4\.large | 3 | 10 | 10 | 
| r4\.xlarge | 4 | 15 | 15 | 
| r4\.2xlarge | 4 | 15 | 15 | 
| r4\.4xlarge | 8 | 30 | 30 | 
| r4\.8xlarge | 8 | 30 | 30 | 
| r4\.16xlarge | 15 | 50 | 50 | 
| r5\.large | 3 | 10 | 10 | 
| r5\.xlarge | 4 | 15 | 15 | 
| r5\.2xlarge | 4 | 15 | 15 | 
| r5\.4xlarge | 8 | 30 | 30 | 
| r5\.8xlarge | 8 | 30 | 30 | 
| r5\.12xlarge | 8 | 30 | 30 | 
| r5\.16xlarge | 15 | 50 | 50 | 
| r5\.24xlarge | 15 | 50 | 50 | 
| r5\.metal | 15 | 50 | 50 | 
| r5a\.large | 3 | 10 | 10 | 
| r5a\.xlarge | 4 | 15 | 15 | 
| r5a\.2xlarge | 4 | 15 | 15 | 
| r5a\.4xlarge | 8 | 30 | 30 | 
| r5a\.8xlarge | 8 | 30 | 30 | 
| r5a\.12xlarge | 8 | 30 | 30 | 
| r5a\.16xlarge | 15 | 50 | 50 | 
| r5a\.24xlarge | 15 | 50 | 50 | 
| r5ad\.large | 3 | 10 | 10 | 
| r5ad\.xlarge | 4 | 15 | 15 | 
| r5ad\.2xlarge | 4 | 15 | 15 | 
| r5ad\.4xlarge | 8 | 30 | 30 | 
| r5ad\.8xlarge | 8 | 30 | 30 | 
| r5ad\.12xlarge | 8 | 30 | 30 | 
| r5ad\.16xlarge | 15 | 50 | 50 | 
| r5ad\.24xlarge | 15 | 50 | 50 | 
| r5b\.large | 3 | 10 | 10 | 
| r5b\.xlarge | 4 | 15 | 15 | 
| r5b\.2xlarge | 4 | 15 | 15 | 
| r5b\.4xlarge | 8 | 30 | 30 | 
| r5b\.8xlarge | 8 | 30 | 30 | 
| r5b\.12xlarge | 8 | 30 | 30 | 
| r5b\.16xlarge | 15 | 50 | 50 | 
| r5b\.24xlarge | 15 | 50 | 50 | 
| r5b\.metal | 15 | 50 | 50 | 
| r5d\.large | 3 | 10 | 10 | 
| r5d\.xlarge | 4 | 15 | 15 | 
| r5d\.2xlarge | 4 | 15 | 15 | 
| r5d\.4xlarge | 8 | 30 | 30 | 
| r5d\.8xlarge | 8 | 30 | 30 | 
| r5d\.12xlarge | 8 | 30 | 30 | 
| r5d\.16xlarge | 15 | 50 | 50 | 
| r5d\.24xlarge | 15 | 50 | 50 | 
| r5d\.metal | 15 | 50 | 50 | 
| r5dn\.large | 3 | 10 | 10 | 
| r5dn\.xlarge | 4 | 15 | 15 | 
| r5dn\.2xlarge | 4 | 15 | 15 | 
| r5dn\.4xlarge | 8 | 30 | 30 | 
| r5dn\.8xlarge | 8 | 30 | 30 | 
| r5dn\.12xlarge | 8 | 30 | 30 | 
| r5dn\.16xlarge | 15 | 50 | 50 | 
| r5dn\.24xlarge | 15 | 50 | 50 | 
| r5dn\.metal | 15 | 50 | 50 | 
| r5n\.large | 3 | 10 | 10 | 
| r5n\.xlarge | 4 | 15 | 15 | 
| r5n\.2xlarge | 4 | 15 | 15 | 
| r5n\.4xlarge | 8 | 30 | 30 | 
| r5n\.8xlarge | 8 | 30 | 30 | 
| r5n\.12xlarge | 8 | 30 | 30 | 
| r5n\.16xlarge | 15 | 50 | 50 | 
| r5n\.24xlarge | 15 | 50 | 50 | 
| r5n\.metal | 15 | 50 | 50 | 
| r6g\.medium | 2 | 4 | 4 | 
| r6g\.large | 3 | 10 | 10 | 
| r6g\.xlarge | 4 | 15 | 15 | 
| r6g\.2xlarge | 4 | 15 | 15 | 
| r6g\.4xlarge | 8 | 30 | 30 | 
| r6g\.8xlarge | 8 | 30 | 30 | 
| r6g\.12xlarge | 8 | 30 | 30 | 
| r6g\.16xlarge | 15 | 50 | 50 | 
| r6g\.metal | 15 | 50 | 50 | 
| r6gd\.medium | 2 | 4 | 4 | 
| r6gd\.large | 3 | 10 | 10 | 
| r6gd\.xlarge | 4 | 15 | 15 | 
| r6gd\.2xlarge | 4 | 15 | 15 | 
| r6gd\.4xlarge | 8 | 30 | 30 | 
| r6gd\.8xlarge | 8 | 30 | 30 | 
| r6gd\.12xlarge | 8 | 30 | 30 | 
| r6gd\.16xlarge | 15 | 50 | 50 | 
| r6gd\.metal | 15 | 50 | 50 | 
| r6i\.large | 3 | 10 | 10 | 
| r6i\.xlarge | 4 | 15 | 15 | 
| r6i\.2xlarge | 4 | 15 | 15 | 
| r6i\.4xlarge | 8 | 30 | 30 | 
| r6i\.8xlarge | 8 | 30 | 30 | 
| r6i\.12xlarge | 8 | 30 | 30 | 
| r6i\.16xlarge | 15 | 50 | 50 | 
| r6i\.24xlarge | 15 | 50 | 50 | 
| r6i\.32xlarge | 15 | 50 | 50 | 
| r6i\.metal | 15 | 50 | 50 | 
| t1\.micro |  2  |  2  | IPv6 not supported | 
| t2\.nano |  2  |  2  | 2 | 
| t2\.micro |  2  |  2  | 2 | 
| t2\.small |  3  |  4  | 4 | 
| t2\.medium |  3  |  6  | 6 | 
| t2\.large |  3  |  12  | 12 | 
| t2\.xlarge |  3  |  15  |  15  | 
| t2\.2xlarge |  3  |  15  |  15  | 
| t3\.nano | 2 | 2 | 2 | 
| t3\.micro | 2 | 2 | 2 | 
| t3\.small | 3 | 4 | 4 | 
| t3\.medium | 3 | 6 | 6 | 
| t3\.large | 3 | 12 | 12 | 
| t3\.xlarge | 4 | 15 | 15 | 
| t3\.2xlarge | 4 | 15 | 15 | 
| t3a\.nano |  2  |  2  | 2 | 
| t3a\.micro |  2  |  2  | 2 | 
| t3a\.small |  2  |  4  | 4 | 
| t3a\.medium |  3  |  6  | 6 | 
| t3a\.large |  3  |  12  | 12 | 
| t3a\.xlarge |  4  |  15  |  15  | 
| t3a\.2xlarge |  4  |  15  |  15  | 
| t4g\.nano | 2 | 2 | 2 | 
| t4g\.micro | 2 | 2 | 2 | 
| t4g\.small | 3 | 4 | 4 | 
| t4g\.medium | 3 | 6 | 6 | 
| t4g\.large | 3 | 12 | 12 | 
| t4g\.xlarge | 4 | 15 | 15 | 
| t4g\.2xlarge | 4 | 15 | 15 | 
| u\-3tb1\.56xlarge | 8 | 30 | 30 | 
| u\-6tb1\.56xlarge | 15 | 50 | 50 | 
| u\-6tb1\.112xlarge | 15 | 50 | 50 | 
| u\-6tb1\.metal | 15 | 50 | 50 | 
| u\-9tb1\.112xlarge | 15 | 50 | 50 | 
| u\-9tb1\.metal | 15 | 50 | 50 | 
| u\-12tb1\.112xlarge | 15 | 50 | 50 | 
| u\-12tb1\.metal | 15 | 50 | 50 | 
| u\-18tb1\.metal | 15 | 50 | 50 | 
| u\-24tb1\.metal | 15 | 50 | 50 | 
| vt1\.3xlarge | 4 | 15 | 15 | 
| vt1\.6xlarge | 8 | 30 | 30 | 
| vt1\.24xlarge | 15 | 50 | 50 | 
| x1\.16xlarge | 8 | 30 | 30 | 
| x1\.32xlarge | 8 | 30 | 30 | 
| x1e\.xlarge | 3 | 10 | 10 | 
| x1e\.2xlarge | 4 | 15 | 15 | 
| x1e\.4xlarge | 4 | 15 | 15 | 
| x1e\.8xlarge | 4 | 15 | 15 | 
| x1e\.16xlarge | 8 | 30 | 30 | 
| x1e\.32xlarge | 8 | 30 | 30 | 
| x2gd\.medium | 2 | 4 | 4 | 
| x2gd\.large | 3 | 10 | 10 | 
| x2gd\.xlarge | 4 | 15 | 15 | 
| x2gd\.2xlarge | 4 | 15 | 15 | 
| x2gd\.4xlarge | 8 | 30 | 30 | 
| x2gd\.8xlarge | 8 | 30 | 30 | 
| x2gd\.12xlarge | 8 | 30 | 30 | 
| x2gd\.16xlarge | 15 | 50 | 50 | 
| x2gd\.metal | 15 | 50 | 50 | 
| x2idn\.16xlarge | 15 | 50 | 50 | 
| x2idn\.24xlarge | 15 | 50 | 50 | 
| x2idn\.32xlarge | 15 | 50 | 50 | 
| x2iedn\.xlarge | 4 | 15 | 15 | 
| x2iedn\.2xlarge | 4 | 15 | 15 | 
| x2iedn\.4xlarge | 8 | 30 | 30 | 
| x2iedn\.8xlarge | 8 | 30 | 30 | 
| x2iedn\.16xlarge | 15 | 50 | 50 | 
| x2iedn\.24xlarge | 15 | 50 | 50 | 
| x2iedn\.32xlarge | 15 | 50 | 50 | 
| x2iezn\.2xlarge | 4 | 15 | 15 | 
| x2iezn\.4xlarge | 8 | 30 | 30 | 
| x2iezn\.6xlarge | 8 | 30 | 30 | 
| x2iezn\.8xlarge | 8 | 30 | 30 | 
| x2iezn\.12xlarge | 15 | 50 | 50 | 
| x2iezn\.metal | 15 | 50 | 50 | 
| z1d\.large | 3 | 10 | 10 | 
| z1d\.xlarge | 4 | 15 | 15 | 
| z1d\.2xlarge | 4 | 15 | 15 | 
| z1d\.3xlarge | 8 | 30 | 30 | 
| z1d\.6xlarge | 8 | 30 | 30 | 
| z1d\.12xlarge | 15 | 50 | 50 | 
| z1d\.metal | 15 | 50 | 50 | 

You can use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) AWS CLI command to display information about an instance type, such as the supported network interfaces and IP addresses per interface\. The following example displays this information for all C5 instances\.

```
aws ec2 describe-instance-types --filters "Name=instance-type,Values=c5.*" --query "InstanceTypes[].{Type: InstanceType, MaxENI: NetworkInfo.MaximumNetworkInterfaces, IPv4addr: NetworkInfo.Ipv4AddressesPerInterface}" --output table
---------------------------------------
|        DescribeInstanceTypes        |
+----------+----------+---------------+
| IPv4addr | MaxENI   |     Type      |
+----------+----------+---------------+
|  30      |  8       |  c5.4xlarge   |
|  50      |  15      |  c5.24xlarge  |
|  15      |  4       |  c5.xlarge    |
|  30      |  8       |  c5.12xlarge  |
|  10      |  3       |  c5.large     |
|  15      |  4       |  c5.2xlarge   |
|  50      |  15      |  c5.metal     |
|  30      |  8       |  c5.9xlarge   |
|  50      |  15      |  c5.18xlarge  |
+----------+----------+---------------+
```

## Work with network interfaces<a name="working-with-enis"></a>

You can work with network interfaces using the Amazon EC2 console or the command line\.

**Topics**
+ [Create a network interface](#create_eni)
+ [View details about a network interface](#view_eni_details)
+ [Attach a network interface to an instance](#attach_eni)
+ [Detach a network interface from an instance](#detach_eni)
+ [Manage IP addresses](#managing-network-interface-ip-addresses)
+ [Modify network interface attributes](#modify-network-interface-attributes)
+ [Add or edit tags](#eni_add_edit_tags)
+ [Delete a network interface](#delete_eni)

### Create a network interface<a name="create_eni"></a>

You can create a network interface in a subnet\. You can't move the network interface to another subnet after it's created\. You must attach a network interface to an instance in the same Availability Zone\.

------
#### [ New console ]

**To create a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Choose **Create network interface**\.

1. \(Optional\) For **Description**, enter a descriptive name\.

1. For **Subnet**, select a subnet\. The options available in the subsequent steps change depending on the type of subnet you select \(IPv4\-only, IPv6\-only, or dual\-stack \(IPv4 and IPv6\)\)\.

1. For **Private IPv4 address**, do one of the following:
   + Choose **Auto\-assign** to allow Amazon EC2 to select an IPv4 address from the subnet\.
   + Choose **Custom** and enter an IPv4 address that you select from the subnet\.

1. \(Subnets with IPv6 addresses only\) For **IPv6 address**, do one of the following:
   + Choose **None** if you do not want to assign an IPv6 address to the network interface\.
   + Choose **Auto\-assign** to allow Amazon EC2 to select an IPv6 address from the subnet\.
   + Choose **Custom** and enter an IPv6 address that you select from the subnet\.

1. \(Optional\) To create an Elastic Fabric Adapter, choose **Elastic Fabric Adapter**, **Enable**\.

1. For **Security groups**, select one or more security groups\.

1. \(Optional\) For each tag, choose **Add new tag** and enter a tag key and an optional tag value\.

1. Choose **Create network interface**\.

------
#### [ Old console ]

**To create a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Choose **Create Network Interface**\.

1. For **Description**, enter a descriptive name\.

1. For **Subnet**, select the subnet\.

1. For **Private IP** \(or **IPv4 Private IP**\), enter the primary private IPv4 address\. If you don't specify an IPv4 address, we select an available private IPv4 address from within the selected subnet\.

1. \(IPv6 only\) If you selected a subnet that has an associated IPv6 CIDR block, you can optionally specify an IPv6 address in the **IPv6 IP** field\. 

1. To create an Elastic Fabric Adapter, select **Elastic Fabric Adapter**\.

1. For **Security groups**, select one or more security groups\.

1. \(Optional\) Choose **Add Tag** and enter a tag key and a tag value\.

1. Choose **Yes, Create**\.

------

**To create a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) \(AWS CLI\)
+ [New\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### View details about a network interface<a name="view_eni_details"></a>

You can view all the network interfaces in your account\.

------
#### [ New console ]

**To describe a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. To view the details page for a network interface, select the ID of the network interface\. Alternatively, to view information without leaving the network interfaces page, select the checkbox for the network interface\.

------
#### [ Old console ]

**To describe a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. To view the details, choose **Details**\.

------

**To describe a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) \(AWS CLI\)
+ [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

**To describe a network interface attribute using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-network\-interface\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interface-attribute.html) \(AWS CLI\)
+ [Get\-EC2NetworkInterfaceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Attach a network interface to an instance<a name="attach_eni"></a>

You can attach a network interface to any instance in the same Availability Zone as the network interface, using either the **Instances** or **Network Interfaces** page of the Amazon EC2 console\. Alternatively, you can specify existing network interfaces when you [launch instances](launching-instance.md)\.

**Important**  
For EC2 instances in an IPv6\-only subnet, if you attach a secondary network interface to the instance, the private DNS hostname of the second network interface will resolve to the first IPv6 address on the instance's first network interface\. For more information about EC2 instance private DNS hostnames, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\.

If the public IPv4 address on your instance is released, it does not receive a new one if there is more than one network interface attached to the instance\. For more information about the behavior of public IPv4 addresses, see [Public IPv4 addresses](using-instance-addressing.md#concepts-public-addresses)\.

------
#### [ Instances page ]

**To attach a network interface to an instance using the **Instances** page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the checkbox for the instance\.

1. Choose **Actions**, **Networking**, **Attach network interface**\.

1. Select a network interface\. If the instance supports multiple network cards, you can choose a network card\.

1. Choose **Attach**\.

------
#### [ Network Interfaces page ]

**To attach a network interface to an instance using the **Network Interfaces** page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. Choose **Actions**, **Attach**\.

1. Choose an instance\. If the instance supports multiple network cards, you can choose a network card\.

1. Choose **Attach**\.

------

**To attach a network interface to an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [attach\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-network-interface.html) \(AWS CLI\)
+ [Add\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Detach a network interface from an instance<a name="detach_eni"></a>

You can detach a secondary network interface that is attached to an EC2 instance at any time, using either the **Instances** or **Network Interfaces** page of the Amazon EC2 console\.

If you try to detach a network interface that is attached to a resource from another service, such as an Elastic Load Balancing load balancer, a Lambda function, a WorkSpace, or a NAT gateway, you get an error that you do not have permission to access the resource\. To find which service created the resource attached to a network interface, check the description of the network interface\. If you delete the resource, then its network interface is deleted\.

------
#### [ Instances page ]

**To detach a network interface from an instance using the Instances page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the checkbox for the instance\. Check the **Network interfaces** section of the **Networking** tab to verify that the network interface is attached to an instance as a secondary network interface\.

1. Choose **Actions**, **Networking**, **Detach network interface**\.

1. Select the network interface and choose **Detach**\.

------
#### [ Network Interfaces page ]

**To detach a network interface from an instance using the Network Interfaces page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\. Check the **Instance details** section of the **Details** tab to verify that the network interface is attached to an instance as a secondary network interface\.

1. Choose **Actions**, **Detach**\.

1. When prompted for confirmation, choose **Detach**\.

1. If the network interface fails to detach from the instance, choose **Force detachment**, **Enable** and then try again\. We recommend that force detachment only as a last resort\. Forcing a detachment can prevent you from attaching a different network interface on the same index until you restart the instance\. It can also prevent the instance metadata from reflecting that the network interface was detached until you restart the instance\.

------

**To detach a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [detach\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/detach-network-interface.html) \(AWS CLI\)
+ [Dismount\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Manage IP addresses<a name="managing-network-interface-ip-addresses"></a>

You can manage the following IP addresses for your network interfaces:
+ Elastic IP addresses \(one per private IPv4 address\)
+ IPv4 addresses
+ IPv6 addresses

**To manage the Elastic IP addresses of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. To associate an Elastic IP address, do the following:

   1. Choose **Actions**, **Associate address**\.

   1. For **Elastic IP address**, select the Elastic IP address\.

   1. For **Private IPv4 address**, select the private IPv4 address to associate with the Elastic IP address\.

   1. \(Optional\) Choose **Allow the Elastic IP address to be reassociated** if the network interface is currently associated with another instance or network interface\.

   1. Choose **Associate**\.

1. To disassociate an Elastic IP address, do the following:

   1. Choose **Actions**, **Disassociate address**\.

   1. For **Public IP address**, select the Elastic IP address\.

   1. Choose **Disassociate**\.

**To manage the IPv4 and IPv6 addresses of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. Choose **Actions**, **Manage IP addresses**\.

1. Expand the network interface\.

1. For **IPv4 addresses**, modify the IP addresses as needed\. To assign an IPv4 address, choose **Assign new IP address** and then specify an IPv4 address from the subnet range or let AWS choose one for you\. To unassign an IPv4 address, choose **Unassign** next to the address\.

1. For **IPv6 addresses**, modify the IP addresses as needed\. To assign an IPv6 address, choose **Assign new IP address** and then specify an IPv6 address from the subnet range or let AWS choose one for you\. To unassign an IPv6 address, choose **Unassign** next to the address\.

1. Choose **Save**\.

**To manage the IP addresses of a network interface using the AWS CLI**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html)
+ [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html)
+ [disassociate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html)
+ [unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html)

**To manage the IP addresses of a network interface using the Tools for Windows PowerShell**

You can use one of the following commands\.
+ [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html)
+ [Register\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Ipv6AddressList.html)
+ [Unregister\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html)
+ [Unregister\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Ipv6AddressList.html)

### Modify network interface attributes<a name="modify-network-interface-attributes"></a>

You can change the following network interface attributes:
+ [Description](#modify-description)
+ [Security groups](#modify-groups)
+ [Delete on termination](#modify-delete-on-termination)
+ [Source/destination check](#modify-source-dest-check)<a name="modify-description"></a>

**To change the description of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. Choose **Actions**, **Change description**\.

1. For **Description**, enter a description for the network interface\.

1. Choose **Save**\.<a name="modify-groups"></a>

**To change the security groups of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. Choose **Actions**, **Change security groups**\.

1. For **Associated security groups**, select the security groups to use, and then choose **Save**\.

   The security group and network interface must be created for the same VPC\. To change the security group for interfaces owned by other services, such as Elastic Load Balancing, do so through that service\.<a name="modify-delete-on-termination"></a>

**To change the termination behavior of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. Choose **Actions**, **Change termination behavior**\.

1. Select or clear **Delete on termination**, **Enable** as needed, and then choose **Save**\.<a name="modify-source-dest-check"></a>

**To change source/destination checking for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. Choose **Actions**, **Change source/dest check**\.

1. Select or clear **Source/destination check**, **Enable** as needed, and then choose **Save**\.

**To modify network interface attributes using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-network\-interface\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-network-interface-attribute.html) \(AWS CLI\)
+ [Edit\-EC2NetworkInterfaceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Add or edit tags<a name="eni_add_edit_tags"></a>

Tags are metadata that you can add to a network interface\. Tags are private and are only visible to your account\. Each tag consists of a key and an optional value\. For more information about tags, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

------
#### [ New console ]

**To add or edit tags for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface\.

1. In **Tags** tab, choose **Manage tags**\.

1. For each tag to create, choose **Add new tag** and enter a key and optional value\. When you're done, choose **Save**\.

------
#### [ Old console ]

**To add or edit tags for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. In the details pane, choose **Tags**, **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, choose **Create Tag** for each tag to create, and enter a key and optional value\. When you're done, choose **Save**\.

------

**To add or edit tags for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

### Delete a network interface<a name="delete_eni"></a>

Deleting a network interface releases all attributes associated with the interface and releases any private IP addresses or Elastic IP addresses to be used by another instance\.

You cannot delete a network interface that is in use\. First, you must [detach the network interface](#detach_eni)\.

------
#### [ New console ]

**To delete a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the checkbox for the network interface, and then choose **Actions**, **Delete**\.

1. When prompted for confirmation, choose **Delete**\.

------
#### [ Old console ]

**To delete a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface and choose **Delete**\.

1. In the **Delete Network Interface** dialog box, choose **Yes, Delete**\.

------

**To delete a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-network-interface.html) \(AWS CLI\)
+ [Remove\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)
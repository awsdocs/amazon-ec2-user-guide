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

You can create and configure network interfaces in your account and attach them to instances in your VPC\. Your account might also have *requester\-managed* network interfaces, which are created and managed by AWS services to enable you to use other resources and services\. You cannot manage these network interfaces yourself\. For more information, see [Requester\-managed network interfaces](requester-managed-eni.md)\.

This AWS resource is referred to as a *network interface* in the AWS Management Console and the Amazon EC2 API\. Therefore, we use "network interface" in this documentation instead of "elastic network interface"\. The term "network interface" in this documentation always means "elastic network interface"\.

**Topics**
+ [Network interface basics](#eni-basics)
+ [Network cards](#network-cards)
+ [IP addresses per network interface per instance type](#AvailableIpPerENI)
+ [Working with network interfaces](#working-with-enis)
+ [Scenarios for network interfaces](scenarios-enis.md)
+ [Best practices for configuring network interfaces](best-practices-for-configuring-network-interfaces.md)
+ [Requester\-managed network interfaces](requester-managed-eni.md)

## Network interface basics<a name="eni-basics"></a>

You can create a network interface, attach it to an instance, detach it from an instance, and attach it to another instance\. The attributes of a network interface follow it as it's attached or detached from an instance and reattached to another instance\. When you move a network interface from one instance to another, network traffic is redirected to the new instance\.

**Primary network interface**  
Each instance has a default network interface, called the *primary network interface*\. You cannot detach a primary network interface from an instance\. You can create and attach additional network interfaces\. The maximum number of network interfaces that you can use varies by instance type\. For more information, see [IP addresses per network interface per instance type](#AvailableIpPerENI)\.

**Public IPv4 addresses for network interfaces**  
In a VPC, all subnets have a modifiable attribute that determines whether network interfaces created in that subnet \(and therefore instances launched into that subnet\) are assigned a public IPv4 address\. For more information, see [IP addressing behavior for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#vpc-ip-addressing-subnet) in the *Amazon VPC User Guide*\. The public IPv4 address is assigned from Amazon's pool of public IPv4 addresses\. When you launch an instance, the IP address is assigned to the primary network interface that's created\.

When you create a network interface, it inherits the public IPv4 addressing attribute from the subnet\. If you later modify the public IPv4 addressing attribute of the subnet, the network interface keeps the setting that was in effect when it was created\. If you launch an instance and specify an existing network interface as the primary network interface, the public IPv4 address attribute is determined by this network interface\.

For more information, see [Public IPv4 addresses and external DNS hostnames](using-instance-addressing.md#concepts-public-addresses)\.

**Elastic IP addresses for network interface**  
If you have an Elastic IP address, you can associate it with one of the private IPv4 addresses for the network interface\. You can associate one Elastic IP address with each private IPv4 address\.

If you disassociate an Elastic IP address from a network interface, you can release it back to the address pool\. This is the only way to associate an Elastic IP address with an instance in a different subnet or VPC, as network interfaces are specific to subnets\.

**IPv6 addresses for network interfaces**  
If you associate IPv6 CIDR blocks with your VPC and subnet, you can assign one or more IPv6 addresses from the subnet range to a network interface\. Each IPv6 address can be assigned to one network interface\.

All subnets have a modifiable attribute that determines whether network interfaces created in that subnet \(and therefore instances launched into that subnet\) are automatically assigned an IPv6 address from the range of the subnet\. For more information, see [IP addressing behavior for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#vpc-ip-addressing-subnet) in the *Amazon VPC User Guide*\. When you launch an instance, the IPv6 address is assigned to the primary network interface that's created\.

For more information, see [IPv6 addresses](using-instance-addressing.md#ipv6-addressing)\.

**Termination behavior**  
You can set the termination behavior for a network interface that's attached to an instance\. You can specify whether the network interface should be automatically deleted when you terminate the instance to which it's attached\.

**Source/destination checking**  
Disabling source/destination checking enables an instance to handle network traffic that isn't specifically destined for the instance\. For example, instances running services such as network address translation, routing, or a firewall should disable the source/destination check attribute\. This attribute is enabled by default\.

**Monitoring IP traffic**  
You can enable a VPC flow log on your network interface to capture information about the IP traffic going to and from a network interface\. After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs\. For more information, see [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) in the *Amazon VPC User Guide*\.

## Network cards<a name="network-cards"></a>

Instances with multiple network cards provide higher network performance, including bandwidth capabilities above 100 Gbps and improved packet rate performance\. Each network interface is attached to a network card\. The primary network interface must be assigned to network card index 0\.

If you enable Elastic Fabric Adapter \(EFA\) when you launch an instance that supports multiple network cards, all network cards are available\. You can assign up to one EFA per network card\. An EFA counts as a network interface\.

The following instances support multiple network cards\. All other instance types support one network card\.


| Instance type | Number of network cards | 
| --- | --- | 
| P4 | 4 | 

## IP addresses per network interface per instance type<a name="AvailableIpPerENI"></a>

The following table lists the maximum number of network interfaces per instance type, and the maximum number of private IPv4 addresses and IPv6 addresses per network interface\. The limit for IPv6 addresses is separate from the limit for private IPv4 addresses per network interface\. Not all instance types support IPv6 addressing\. Network interfaces, multiple private IPv4 addresses, and IPv6 addresses are only available for instances running in a VPC\. IPv6 addresses are public and reachable over the Internet\. For more information, see [Multiple IP addresses](MultipleIP.md)\. For more information about IPv6 in VPC, see [IP Addressing in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\.


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
| cc2\.8xlarge |  8  |  30  | IPv6 not supported | 
| cr1\.8xlarge |  8  |  30  | IPv6 not supported | 
| d2\.xlarge |  4  |  15  | 15 | 
| d2\.2xlarge |  4  |  15  | 15 | 
| d2\.4xlarge |  8  |  30  | 30 | 
| d2\.8xlarge |  8  |  30  | 30 | 
| f1\.2xlarge |  4  | 15  |  15  | 
| f1\.4xlarge |  8  | 30  |  30  | 
| f1\.16xlarge | 8 | 50 | 50 | 
| g2\.2xlarge |  4  |  15  | IPv6 not supported | 
| g2\.8xlarge |  8  |  30  | IPv6 not supported | 
| g3s\.xlarge | 4 | 15 | 15 | 
| g3\.4xlarge | 8 | 30 | 30 | 
| g3\.8xlarge | 8 | 30 | 30 | 
| g3\.16xlarge | 15 | 50 | 50 | 
| g4dn\.xlarge | 3 | 10 | 10 | 
| g4dn\.2xlarge | 3 | 10 | 10 | 
| g4dn\.4xlarge | 3 | 10 | 10 | 
| g4dn\.8xlarge | 4 | 15 | 15 | 
| g4dn\.12xlarge | 8 | 30 | 30 | 
| g4dn\.16xlarge | 4 | 15 | 15 | 
| g4dn\.metal | 15 | 50 | 50 | 
| h1\.2xlarge | 4 | 15 | 15 | 
| h1\.4xlarge | 8 | 30 | 30 | 
| h1\.8xlarge | 8 | 30 | 30 | 
| h1\.16xlarge | 15 | 50 | 50 | 
| hs1\.8xlarge |  8  |  30  | IPv6 not supported | 
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
| inf1\.xlarge | 4 | 10 | 10 | 
| inf1\.2xlarge | 4 | 10 | 10 | 
| inf1\.6xlarge | 8 | 30 | 30 | 
| inf1\.24xlarge | 15 | 30 | 30 | 
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
| m5n\.large | 3 | 10 | 10 | 
| m5n\.xlarge | 4 | 15 | 15 | 
| m5n\.2xlarge | 4 | 15 | 15 | 
| m5n\.4xlarge | 8 | 30 | 30 | 
| m5n\.8xlarge | 8 | 30 | 30 | 
| m5n\.12xlarge | 8 | 30 | 30 | 
| m5n\.16xlarge | 15 | 50 | 50 | 
| m5n\.24xlarge | 15 | 50 | 50 | 
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
| p2\.xlarge | 4 | 15 | 15 | 
| p2\.8xlarge | 8 | 30 | 30 | 
| p2\.16xlarge | 8 | 30 | 30 | 
| p3\.2xlarge | 4 | 15 | 15 | 
| p3\.8xlarge | 8 | 30 | 30 | 
| p3\.16xlarge | 8 | 30 | 30 | 
| p3dn\.24xlarge | 15 | 50 | 50 | 
| p4d\.24xlarge | 4x15 | 50 | 50 | 
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
| r5n\.large | 3 | 10 | 10 | 
| r5n\.xlarge | 4 | 15 | 15 | 
| r5n\.2xlarge | 4 | 15 | 15 | 
| r5n\.4xlarge | 8 | 30 | 30 | 
| r5n\.8xlarge | 8 | 30 | 30 | 
| r5n\.12xlarge | 8 | 30 | 30 | 
| r5n\.16xlarge | 15 | 50 | 50 | 
| r5n\.24xlarge | 15 | 50 | 50 | 
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
| t4g\.small | 2 | 4 | 4 | 
| t4g\.medium | 3 | 6 | 6 | 
| t4g\.large | 3 | 12 | 12 | 
| t4g\.xlarge | 4 | 15 | 15 | 
| t4g\.2xlarge | 4 | 15 | 15 | 
| u\-6tb1\.metal | 5 | 30 | 30 | 
| u\-9tb1\.metal | 5 | 30 | 30 | 
| u\-12tb1\.metal | 5 | 30 | 30 | 
| u\-18tb1\.metal | 15 | 50 | 50 | 
| u\-24tb1\.metal | 15 | 50 | 50 | 
| x1\.16xlarge | 8 | 30 | 30 | 
| x1\.32xlarge | 8 | 30 | 30 | 
| x1e\.xlarge | 3 | 10 | 10 | 
| x1e\.2xlarge | 4 | 15 | 15 | 
| x1e\.4xlarge | 4 | 15 | 15 | 
| x1e\.8xlarge | 4 | 15 | 15 | 
| x1e\.16xlarge | 8 | 30 | 30 | 
| x1e\.32xlarge | 8 | 30 | 30 | 
| z1d\.large | 3 | 10 | 10 | 
| z1d\.xlarge | 4 | 15 | 15 | 
| z1d\.2xlarge | 4 | 15 | 15 | 
| z1d\.3xlarge | 8 | 30 | 30 | 
| z1d\.6xlarge | 8 | 30 | 30 | 
| z1d\.12xlarge | 15 | 50 | 50 | 
| z1d\.metal | 15 | 50 | 50 | 

## Working with network interfaces<a name="working-with-enis"></a>

You can work with network interfaces using the Amazon EC2 console or the command line\.

**Topics**
+ [Creating a network interface](#create_eni)
+ [Viewing details about a network interface](#view_eni_details)
+ [Attaching a network interface to an instance](#attach_eni)
+ [Detaching a network interface from an instance](#detach_eni)
+ [Managing IP addresses](#managing-network-interface-ip-addresses)
+ [Modifying network interface attributes](#modify-network-interface-attributes)
+ [Adding or editing tags](#eni_add_edit_tags)
+ [Deleting a network interface](#delete_eni)

### Creating a network interface<a name="create_eni"></a>

You can create a network interface in a subnet\. You can't move the network interface to another subnet after it's created, and you can only attach the network interface to instances in the same Availability Zone\.

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

**To create a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) \(AWS CLI\)
+ [New\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Viewing details about a network interface<a name="view_eni_details"></a>

You can view all the network interfaces in your account\.

**To describe a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. To view the details, choose **Details**\.

**To describe a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) \(AWS CLI\)
+ [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

**To describe a network interface attribute using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-network\-interface\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interface-attribute.html) \(AWS CLI\)
+ [Get\-EC2NetworkInterfaceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Attaching a network interface to an instance<a name="attach_eni"></a>

You can attach a network interface to any of your stopped or running instances, using either the **Instances** or **Network Interfaces** pages of the Amazon EC2 console\. Alternatively, you can specify an existing network interface or attach an additional network interface when you [launch an instance](launching-instance.md)\.

If the public IPv4 address on your instance is released, it does not receive a new one if there is more than one network interface attached to the instance\. For more information about the behavior of public IPv4 addresses, see [Public IPv4 addresses and external DNS hostnames](using-instance-addressing.md#concepts-public-addresses)\.

**To attach a network interface to an instance using the **Instances** page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. Choose **Actions**, **Networking**, **Attach network interface**\.

1. Select a network interface\. If the instance supports multiple network cards, you can choose a network card\.

1. Choose **Attach**\.

**To attach a network interface to an instance using the **Network Interfaces** page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Attach**\.

1. Select an instance\. If the instance supports multiple network cards, you can choose a network card\.

1. Choose **Attach**\.

**To attach a network interface to an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [attach\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-network-interface.html) \(AWS CLI\)
+ [Add\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Detaching a network interface from an instance<a name="detach_eni"></a>

You can detach a secondary network interface that is attached to an EC2 instance at any time, using either the **Instances** or **Network Interfaces** page of the Amazon EC2 console\.

**To detach a network interface from an instance using the Instances page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. Choose **Actions**, **Networking**, **Detach network interface**\.

1. Select the network interface and choose **Detach**\.

You can't use the Amazon EC2 console to detach a network interface that is attached to a resource from another service, such as an Elastic Load Balancing load balancer, a Lambda function, a WorkSpace, or a NAT gateway\. The network interfaces for those resources are deleted when the resource is deleted\.

**To detach a network interface from an instance using the Network Interfaces page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and check the description to verify that the network interface is attached to an instance, not another type of resource\. If the resource is an EC2 instance, choose **Detach**\.

   If the network interface is the primary network interface for the instance, the **Detach** button is disabled\.

1. When prompted for confirmation, choose **Yes, Detach**\.

1. If the network interface fails to detach from the instance, choose **Force detachment** and then try again\. We recommend that you choose this option only as a last resort\. Forcing a detachment can prevent you from attaching a different network interface on the same index until you restart the instance\. It can also prevent the instance metadata from reflecting that the network interface was detached until you restart the instance\.

**To detach a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [detach\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/detach-network-interface.html) \(AWS CLI\)
+ [Dismount\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Managing IP addresses<a name="managing-network-interface-ip-addresses"></a>

You can manage the following IP addresses for your network interfaces:
+ Elastic IP addresses \(one per private IPv4 address\)
+ IPv4 addresses
+ IPv6 addresses

**To Elastic IP addresses of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. To associate an Elastic IP address, do the following:

   1. Choose **Actions**, **Associate Address**\.

   1. For **Address**, select the Elastic IP address\.

   1. For **Associate to private IP address**, select the private IPv4 address to associate with the Elastic IP address\. 

   1. Choose **Allow reassociation** to allow the Elastic IP address to be associated with the specified network interface if it's currently associated with another instance or network interface, and then choose **Associate Address**\.

1. To disassociate an Elastic IP address, do the following:

   1. Choose **Actions**, **Disassociate Address**\.

   1. In the **Disassociate IP Address** dialog box, choose **Yes, Disassociate**\.

**To manage the IPv4 and IPv6 addresses of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. Choose **Actions**, **Manage IP Addresses**\.

1. For **IPv4 Addresses**, modify the IP addresses as needed\. To assign an IPv4 address, choose **Assign new IP** and then specify an IPv4 address from the subnet range or let AWS choose one for you\. To unassign an IPv4 address, choose **Unassign** next to the address\.

1. For **IPv6 Addresses**, modify the IP addresses as needed\. To assign an IPv6 address, choose **Assign new IP** and then specify an IPv6 address from the subnet range or let AWS choose one for you\. To unassign an IPv6 address, choose **Unassign** next to the address\.

1. Choose **Yes, Update**\.

**To manage the IP addresses of a network interface using the AWS CLI**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
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

### Modifying network interface attributes<a name="modify-network-interface-attributes"></a>

You can change the following network interface attributes:
+ [Description](#modify-description)
+ [Security groups](#modify-groups)
+ [Delete on termination](#modify-delete-on-termination)
+ [Source/destination check](#modify-source-dest-check)<a name="modify-description"></a>

**To change the description of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Description**\.

1. For **Change Description**, enter a description for the network interface, and then choose **Save**\.<a name="modify-groups"></a>

**To change the security groups of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Security Groups**\.

1. For **Change Security Groups**, select the security groups to use, and then choose **Save**\.

   The security group and network interface must be created for the same VPC\. To change the security group for interfaces owned by other services, such as Elastic Load Balancing, do so through that service\.<a name="modify-delete-on-termination"></a>

**To change the termination behavior of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Termination Behavior**\.

1. In the **Change Termination Behavior** dialog box, select the **Delete on termination** check box if you want the network interface to be deleted when you terminate an instance\.<a name="modify-source-dest-check"></a>

**To change source/destination checking for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Source/Dest Check**\.

1. In the dialog box, choose **Enabled** \(if enabling\) or **Disabled** \(if disabling\), and **Save**\.

**To modify network interface attributes using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-network\-interface\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-network-interface-attribute.html) \(AWS CLI\)
+ [Edit\-EC2NetworkInterfaceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Adding or editing tags<a name="eni_add_edit_tags"></a>

Tags are metadata that you can add to a network interface\. Tags are private and are only visible to your account\. Each tag consists of a key and an optional value\. For more information about tags, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

**To add or edit tags for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. In the details pane, choose **Tags**, **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, choose **Create Tag** for each tag to create, and enter a key and optional value\. When you're done, choose **Save**\.

**To add or edit tags for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

### Deleting a network interface<a name="delete_eni"></a>

To delete an instance, you must first detach the network interface\. Deleting a network interface releases all attributes associated with the interface and releases any private IP addresses or Elastic IP addresses to be used by another instance\.

**To delete a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface and choose **Delete**\.

1. In the **Delete Network Interface** dialog box, choose **Yes, Delete**\.

**To delete a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [delete\-network\-interface](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-network-interface.html) \(AWS CLI\)
+ [Remove\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)
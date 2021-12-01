# Amazon EC2 instance IP addressing<a name="using-instance-addressing"></a>

Amazon EC2 and Amazon VPC support both the IPv4 and IPv6 addressing protocols\. By default, Amazon VPC uses the IPv4 addressing protocol; you can't disable this behavior\. When you create a VPC, you must specify an IPv4 CIDR block \(a range of private IPv4 addresses\)\. You can optionally assign an IPv6 CIDR block to your VPC and assign IPv6 addresses from that block to instances in your subnets\.

**Topics**
+ [Private IPv4 addresses](#concepts-private-addresses)
+ [Public IPv4 addresses](#concepts-public-addresses)
+ [Elastic IP addresses \(IPv4\)](#ip-addressing-eips)
+ [IPv6 addresses](#ipv6-addressing)
+ [Work with the IPv4 addresses for your instances](#working-with-ip-addresses)
+ [Work with the IPv6 addresses for your instances](#working-with-ipv6-addresses)
+ [Multiple IP addresses](MultipleIP.md)
+ [EC2 instance hostnames](#amazon-dns)

## Private IPv4 addresses<a name="concepts-private-addresses"></a>

A private IPv4 address is an IP address that's not reachable over the Internet\. You can use private IPv4 addresses for communication between instances in the same VPC\. For more information about the standards and specifications of private IPv4 addresses, see [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\. We allocate private IPv4 addresses to instances using DHCP\.

**Note**  
You can create a VPC with a publicly routable CIDR block that falls outside of the private IPv4 address ranges specified in RFC 1918\. However, for the purposes of this documentation, we refer to private IPv4 addresses \(or 'private IP addresses'\) as the IP addresses that are within the IPv4 CIDR range of your VPC\.

VPC subnets can be one of the following types:
+ IPv4\-only subnets: You can only create resources in these subnets with IPv4 addresses assigned to them\.
+ IPv6\-only subnets: You can only create resources in these subnets with IPv6 addresses assigned to them\.
+ IPv4 and IPv6 subnets: You can create resources in these subnets with either IPv4 or IPv6 addresses assigned to them\.

When you launch an EC2 instance into an IPv4\-only or dual stack \(IPv4 and IPv6\) subnet, the instance receives a primary private IP address from the IPv4 address range of the subnet\. For more information, see [VPC and subnet sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#VPC_Sizing) in the *Amazon VPC User Guide*\. If you don't specify a primary private IP address when you launch the instance, we select an available IP address in the subnet's IPv4 range for you\. Each instance has a default network interface \(eth0\) that is assigned the primary private IPv4 address\. You can also specify additional private IPv4 addresses, known as *secondary private IPv4 addresses*\. Unlike primary private IP addresses, secondary private IP addresses can be reassigned from one instance to another\. For more information, see [Multiple IP addresses](MultipleIP.md)\. 

A private IPv4 address, regardless of whether it is a primary or secondary address, remains associated with the network interface when the instance is stopped and started, or hibernated and started, and is released when the instance is terminated\.

## Public IPv4 addresses<a name="concepts-public-addresses"></a>

A public IP address is an IPv4 address that's reachable from the Internet\. You can use public addresses for communication between your instances and the Internet\.

When you launch an instance in a default VPC, we assign it a public IP address by default\. When you launch an instance into a nondefault VPC, the subnet has an attribute that determines whether instances launched into that subnet receive a public IP address from the public IPv4 address pool\. By default, we don't assign a public IP address to instances launched in a nondefault subnet\.

You can control whether your instance receives a public IP address as follows:
+ Modifying the public IP addressing attribute of your subnet\. For more information, see [Modifying the public IPv4 addressing attribute for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip) in the *Amazon VPC User Guide*\.
+ Enabling or disabling the public IP addressing feature during launch, which overrides the subnet's public IP addressing attribute\. For more information, see [Assign a public IPv4 address during instance launch](#public-ip-addresses)\.

A public IP address is assigned to your instance from Amazon's pool of public IPv4 addresses, and is not associated with your AWS account\. When a public IP address is disassociated from your instance, it is released back into the public IPv4 address pool, and you cannot reuse it\.

You cannot manually associate or disassociate a public IP \(IPv4\) address from your instance\. Instead, in certain cases, we release the public IP address from your instance, or assign it a new one: 
+ We release your instance's public IP address when it is stopped, hibernated, or terminated\. Your stopped or hibernated instance receives a new public IP address when it is started\.
+ We release your instance's public IP address when you associate an Elastic IP address with it\. When you disassociate the Elastic IP address from your instance, it receives a new public IP address\.
+ If the public IP address of your instance in a VPC has been released, it will not receive a new one if there is more than one network interface attached to your instance\. 
+ If your instance's public IP address is released while it has a secondary private IP address that is associated with an Elastic IP address, the instance does not receive a new public IP address\.

If you require a persistent public IP address that can be associated to and from instances as you require, use an Elastic IP address instead\.

If you use dynamic DNS to map an existing DNS name to a new instance's public IP address, it might take up to 24 hours for the IP address to propagate through the Internet\. As a result, new instances might not receive traffic while terminated instances continue to receive requests\. To solve this problem, use an Elastic IP address\. You can allocate your own Elastic IP address, and associate it with your instance\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. 

**Note**  
Instances that access other instances through their public NAT IP address are charged for regional or Internet data transfer, depending on whether the instances are in the same Region\.

## Elastic IP addresses \(IPv4\)<a name="ip-addressing-eips"></a>

An Elastic IP address is a public IPv4 address that you can allocate to your account\. You can associate it to and disassociate it from instances as you require\. It's allocated to your account until you choose to release it\. For more information about Elastic IP addresses and how to use them, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\.

We do not support Elastic IP addresses for IPv6\.

## IPv6 addresses<a name="ipv6-addressing"></a>

You can optionally associate an IPv6 CIDR block with your VPC and associate IPv6 CIDR blocks with your subnets\. The IPv6 CIDR block for your VPC is automatically assigned from Amazon's pool of IPv6 addresses; you cannot choose the range yourself\. For more information, see the following topics in the *Amazon VPC User Guide*:
+ [VPC and subnet sizing for IPv6](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-sizing-ipv6)
+ [Associating an IPv6 CIDR block with your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#vpc-associate-ipv6-cidr)
+ [Associating an IPv6 CIDR block with your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#subnet-associate-ipv6-cidr)

IPv6 addresses are globally unique and can be configured to remain private or reachable over the Internet\. For more information about IPv6, see [IP Addressing in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\. Your instance receives an IPv6 address if an IPv6 CIDR block is associated with your VPC and subnet, and if one of the following is true:
+ Your subnet is configured to automatically assign an IPv6 address to an instance during launch\. For more information, see [Modifying the IPv6 addressing attribute for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-ipv6)\.
+ You assign an IPv6 address to your instance during launch\.
+ You assign an IPv6 address to the primary network interface of your instance after launch\.
+ You assign an IPv6 address to a network interface in the same subnet, and attach the network interface to your instance after launch\. 

When your instance receives an IPv6 address during launch, the address is associated with the primary network interface \(eth0\) of the instance\. You can disassociate the IPv6 address from the network interface\.

An IPv6 address persists when you stop and start, or hibernate and start, your instance, and is released when you terminate your instance\. You cannot reassign an IPv6 address while it's assigned to another network interface—you must first unassign it\.

You can assign additional IPv6 addresses to your instance by assigning them to a network interface attached to your instance\. The number of IPv6 addresses you can assign to a network interface and the number of network interfaces you can attach to an instance varies per instance type\. For more information, see [IP addresses per network interface per instance type](using-eni.md#AvailableIpPerENI)\.

## Work with the IPv4 addresses for your instances<a name="working-with-ip-addresses"></a>

You can assign a public IPv4 address to your instance when you launch it\. You can view the IPv4 addresses for your in the console through either the **Instances** page or the **Network Interfaces** page\.

**Topics**
+ [View the IPv4 addresses](#using-instance-addressing-common)
+ [Assign a public IPv4 address during instance launch](#public-ip-addresses)

### View the IPv4 addresses<a name="using-instance-addressing-common"></a>

You can use the Amazon EC2 console to view the private IPv4 addresses, public IPv4 addresses, and Elastic IP addresses of your instances\. You can also determine the public IPv4 and private IPv4 addresses of your instance from within your instance by using instance metadata\. For more information, see [Instance metadata and user data](ec2-instance-metadata.md)\.

The public IPv4 address is displayed as a property of the network interface in the console, but it's mapped to the primary private IPv4 address through NAT\. Therefore, if you inspect the properties of your network interface on your instance, for example, through `ifconfig` \(Linux\) or `ipconfig` \(Windows\), the public IPv4 address is not displayed\. To determine your instance's public IPv4 address from an instance, use instance metadata\.

------
#### [ New console ]

**To view the IPv4 addresses for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. The following information is available on the **Networking** tab:
   + **Public IPv4 address** — The public IPv4 address\. If you associated an Elastic IP address with the instance or the primary network interface, this is the Elastic IP address\.
   + **Public IPv4 DNS** — The external DNS hostname\.
   + **Private IP DNS name \(IPv4 only\)** — The private IPv4 address\.
   + **Private IPv4 DNS** — The internal DNS hostname\.
   + **Secondary private IPv4 addresses** — Any secondary private IPv4 addresses\.
   + **Elastic IP addresses** — Any associated Elastic IP addresses\.

1. Alternatively, under **Network interfaces** on the **Networking** tab, choose the interface ID for the primary network interface \(for example, eni\-123abc456def78901\)\. The following information is available:
   + **Private DNS \(IPv4\)** — The internal DNS hostname\.
   + **Primary private IPv4 IP** — The primary private IPv4 address\.
   + **Secondary private IPv4 IPs** — Any secondary private IPv4 addresses\.
   + **Public DNS** — The external DNS hostname\.
   + **IPv4 Public IP** — The public IPv4 address\. If you associated an Elastic IP address with the instance or the primary network interface, this is the Elastic IP address\.
   + **Elastic IPs** — Any associated Elastic IP addresses\.

------
#### [ Old console ]

**To view the IPv4 addresses for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. The following information is available on the **Description** tab:
   + **Private DNS** — The internal DNS hostname\.
   + **Private IPs** — The private IPv4 address\.
   + **Secondary private IPs** — Any secondary private IPv4 addresses\.
   + **Public DNS** — The external DNS hostname\.
   + **IPv4 Public IP** — The public IPv4 address\. If you associated an Elastic IP address with the instance or the primary network interface, this is the Elastic IP address\.
   + **Elastic IPs** — Any associated Elastic IP addresses\.

1. Alternatively, you can view the IPv4 addresses for the instance using the primary network interface\. Under **Network interfaces** on the **Description** tab, choose **eth0**, and then choose the interface ID \(for example, eni\-123abc456def78901\)\. The following information is available:
   + **Private Ipv4 DNS** — The internal DNS hostname\.
   + **Private IPv4 address** — The primary private IPv4 address\.
   + **Public IPv4 address** — The public IPv4 address\. If you associated an Elastic IP address with the instance or the primary network interface, this is the Elastic IP address\.
   + **Public IPv4 DNS** — The external DNS hostname\.
   + **Secondary private IPv4 IPs** — Any secondary private IPv4 addresses\.
   + **Elastic IPs** — Any associated Elastic IP addresses\.

------

**To view the IPv4 addresses for an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\.

**To determine your instance's IPv4 addresses using instance metadata**

1. Connect to your instance\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

1. Use the following command to access the private IP address:

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/local-ipv4
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/local-ipv4
   ```

------

1. Use the following command to access the public IP address:

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/public-ipv4
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-ipv4
   ```

------

   If an Elastic IP address is associated with the instance, the value returned is that of the Elastic IP address\.

### Assign a public IPv4 address during instance launch<a name="public-ip-addresses"></a>

Each subnet has an attribute that determines whether instances launched into that subnet are assigned a public IP address\. By default, nondefault subnets have this attribute set to false, and default subnets have this attribute set to true\. When you launch an instance, a public IPv4 addressing feature is also available for you to control whether your instance is assigned a public IPv4 address; you can override the default behavior of the subnet's IP addressing attribute\. The public IPv4 address is assigned from Amazon's pool of public IPv4 addresses, and is assigned to the network interface with the device index of eth0\. This feature depends on certain conditions at the time you launch your instance\. 

**Considerations**
+ You can't manually disassociate the public IP address from your instance after launch\. Instead, it's automatically released in certain cases, after which you cannot reuse it\. For more information, see [Public IPv4 addresses](#concepts-public-addresses)\. If you require a persistent public IP address that you can associate or disassociate at will, assign an Elastic IP address to the instance after launch instead\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\.
+ You cannot auto\-assign a public IP address if you specify more than one network interface\. Additionally, you cannot override the subnet setting using the auto\-assign public IP feature if you specify an existing network interface for eth0\. 
+ The public IP addressing feature is only available during launch\. However, whether you assign a public IP address to your instance during launch or not, you can associate an Elastic IP address with your instance after it's launched\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. You can also modify your subnet's public IPv4 addressing behavior\. For more information, see [Modifying the public IPv4 addressing attribute for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip)\.

**To enable or disable the public IP addressing feature using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Select an AMI and an instance type, and then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, for **Network**, select a VPC\. The **Auto\-assign Public IP** list is displayed\. Choose **Enable** or **Disable** to override the default setting for the subnet\. 

1. Follow the steps on the next pages of the wizard to complete your instance's setup\. For more information about the wizard configuration options, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\. On the final **Review Instance Launch** page, review your settings, and then choose **Launch** to choose a key pair and launch your instance\.

1. On the **Instances** page, select your new instance and view its public IP address in **IPv4 Public IP** field in the details pane\.<a name="publicip-cli"></a>

**To enable or disable the public IP addressing feature using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ Use the `--associate-public-ip-address` or the `--no-associate-public-ip-address` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command \(AWS CLI\)
+ Use the `-AssociatePublicIp` parameter with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)

## Work with the IPv6 addresses for your instances<a name="working-with-ipv6-addresses"></a>

You can view the IPv6 addresses assigned to your instance, assign a public IPv6 address to your instance, or unassign an IPv6 address from your instance\. You can view these addresses in the console through either the **Instances** page or the **Network Interfaces** page\.

**Topics**
+ [View the IPv6 addresses](#view-ipv6-addresses)
+ [Assign an IPv6 address to an instance](#assign-ipv6-address)
+ [Unassign an IPv6 address from an instance](#unassign-ipv6-address)

### View the IPv6 addresses<a name="view-ipv6-addresses"></a>

You can use the Amazon EC2 console, AWS CLI, and instance metadata to view the IPv6 addresses for your instances\.

------
#### [ New console ]

**To view the IPv6 addresses for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Networking** tab, locate **IPv6 addresses**\.

1. Alternatively, under **Network interfaces** on the **Networking** tab, choose the interface ID for the network interface \(for example, eni\-123abc456def78901\)\. Locate **IPv6 IPs**\.

------
#### [ Old console ]

**To view the IPv6 addresses for an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Networking** tab, locate **IPv6 IPs**\.

1. Alternatively, under **Network interfaces** on the **Description** tab, choose **eth0**, and then choose the interface ID \(for example, eni\-123abc456def78901\)\. Locate **IPv6 IPs**\.

------

**To view the IPv6 addresses for an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\.

**To view the IPv6 addresses for an instance using instance metadata**

1. Connect to your instance\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

1. Use the following command to view the IPv6 address \(you can get the MAC address from `http://169.254.169.254/latest/meta-data/network/interfaces/macs/`\)\.

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/network/interfaces/macs/mac-address/ipv6s
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/network/interfaces/macs/mac-address/ipv6s
   ```

------

### Assign an IPv6 address to an instance<a name="assign-ipv6-address"></a>

If your VPC and subnet have IPv6 CIDR blocks associated with them, you can assign an IPv6 address to your instance during or after launch\. The IPv6 address is assigned from the IPv6 address range of the subnet, and is assigned to the network interface with the device index of eth0\.

**To assign an IPv6 address to an instance during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Select an AMI and an instance type that supports IPv6, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, for **Network**, select a VPC and for **Subnet**, select a subnet\. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. Follow the remaining steps in the wizard to launch your instance\.

**To assign an IPv6 address to an instance after launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, and choose **Actions**, **Networking**, **Manage IP addresses**\.

1. Expand the network interface\. Under **IPv6 addresses**, choose **Assign new IP address**\. Enter an IPv6 address from the range of the subnet or leave the field blank to let Amazon choose an IPv6 address for you\.

1. Choose **Save**\.<a name="assign-ipv6-cli"></a>

**To assign an IPv6 address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ Use the `--ipv6-addresses` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command \(AWS CLI\)
+ Use the `Ipv6Addresses` property for `-NetworkInterface` in the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)
+ [assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) \(AWS CLI\)
+ [Register\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)

### Unassign an IPv6 address from an instance<a name="unassign-ipv6-address"></a>

You can unassign an IPv6 address from an instance at any time\.

**To unassign an IPv6 address from an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, and choose **Actions**, **Networking**, **Manage IP addresses**\.

1. Expand the network interface\. Under **IPv6 addresses**, choose **Unassign** next to the IPv6 address\.

1. Choose **Save**\.

**To unassign an IPv6 address from an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) \(AWS CLI\)
+ [Unregister\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)\.

## EC2 instance hostnames<a name="amazon-dns"></a>

When you create an EC2 instance, AWS creates a hostname for that instance\. For more information on the types of hostnames and how they're provisioned by AWS, see [Amazon EC2 instance hostname types](ec2-instance-naming.md)\. Amazon provides a DNS server that resolves Amazon\-provided hostnames to IPv4 and IPv6 addresses\. The Amazon DNS server is located at the base of your VPC network range plus two\. For more information, see [DNS support for your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\. 
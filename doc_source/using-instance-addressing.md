# Amazon EC2 Instance IP Addressing<a name="using-instance-addressing"></a>

Amazon EC2 and Amazon VPC support both the IPv4 and IPv6 addressing protocols\. By default, Amazon EC2 and Amazon VPC use the IPv4 addressing protocol; you can't disable this behavior\. When you create a VPC, you must specify an IPv4 CIDR block \(a range of private IPv4 addresses\)\. You can optionally assign an IPv6 CIDR block to your VPC and subnets, and assign IPv6 addresses from that block to instances in your subnet\. IPv6 addresses are reachable over the Internet\. For more information about IPv6, see [IP Addressing in Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\.

**Topics**
+ [Private IPv4 Addresses and Internal DNS Hostnames](#concepts-private-addresses)
+ [Public IPv4 Addresses and External DNS Hostnames](#concepts-public-addresses)
+ [Elastic IP Addresses \(IPv4\)](#ip-addressing-eips)
+ [Amazon DNS Server](#amazon-dns-server)
+ [IPv6 Addresses](#ipv6-addressing)
+ [Working with IP Addresses for Your Instance](#working-with-ip-addresses)
+ [Multiple IP Addresses](MultipleIP.md)

## Private IPv4 Addresses and Internal DNS Hostnames<a name="concepts-private-addresses"></a>

A private IPv4 address is an IP address that's not reachable over the Internet\. You can use private IPv4 addresses for communication between instances in the same VPC\. For more information about the standards and specifications of private IPv4 addresses, see [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\. We allocate private IPv4 addresses to instances using DHCP\.

**Note**  
You can create a VPC with a publicly routable CIDR block that falls outside of the private IPv4 address ranges specified in RFC 1918\. However, for the purposes of this documentation, we refer to private IPv4 addresses \(or 'private IP addresses'\) as the IP addresses that are within the IPv4 CIDR range of your VPC\.

When you launch an instance, we allocate a primary private IPv4 address for the instance\. Each instance is also given an internal DNS hostname that resolves to the primary private IPv4 address; for example, `ip-10-251-50-12.ec2.internal`\. You can use the internal DNS hostname for communication between instances in the same VPC, but we can't resolve the internal DNS hostname outside of the VPC\.

An instance receives a primary private IP address from the IPv4 address range of the subnet\. For more information, see [VPC and Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#VPC_Sizing) in the *Amazon VPC User Guide*\. If you don't specify a primary private IP address when you launch the instance, we select an available IP address in the subnet's IPv4 range for you\. Each instance has a default network interface \(eth0\) that is assigned the primary private IPv4 address\. You can also specify additional private IPv4 addresses, known as *secondary private IPv4 addresses*\. Unlike primary private IP addresses, secondary private IP addresses can be reassigned from one instance to another\. For more information, see [Multiple IP Addresses](MultipleIP.md)\. 

A private IPv4 address, regardless of whether it is a primary or secondary address, remains associated with the network interface when the instance is stopped and restarted, and is released when the instance is terminated\.

## Public IPv4 Addresses and External DNS Hostnames<a name="concepts-public-addresses"></a>

A public IP address is an IPv4 address that's reachable from the Internet\. You can use public addresses for communication between your instances and the Internet\.

Each instance that receives a public IP address is also given an external DNS hostname; for example, `ec2-203-0-113-25.compute-1.amazonaws.com`\. We resolve an external DNS hostname to the public IP address of the instance from outside its VPC, and to the private IPv4 address of the instance from inside its VPC\. The public IP address is mapped to the primary private IP address through network address translation \(NAT\)\. For more information, see [RFC 1631: The IP Network Address Translator \(NAT\)](http://www.faqs.org/rfcs/rfc1631.html)\.

When you launch an instance in a default VPC, we assign it a public IP address by default\. When you launch an instance into a nondefault VPC, the subnet has an attribute that determines whether instances launched into that subnet receive a public IP address from the public IPv4 address pool\. By default, we don't assign a public IP address to instances launched in a nondefault subnet\.

You can control whether your instance receives a public IP address as follows:
+ Modifying the public IP addressing attribute of your subnet\. For more information, see [Modifying the Public IPv4 Addressing Attribute for Your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip) in the *Amazon VPC User Guide*\.
+ Enabling or disabling the public IP addressing feature during launch, which overrides the subnet's public IP addressing attribute\. For more information, see [Assigning a Public IPv4 Address During Instance Launch](#public-ip-addresses)\.

A public IP address is assigned to your instance from Amazon's pool of public IPv4 addresses, and is not associated with your AWS account\. When a public IP address is disassociated from your instance, it is released back into the public IPv4 address pool, and you cannot reuse it\.

You cannot manually associate or disassociate a public IP address from your instance\. Instead, in certain cases, we release the public IP address from your instance, or assign it a new one: 
+ We release your instance's public IP address when it is stopped or terminated\. Your stopped instance receives a new public IP address when it is restarted\.
+ We release your instance's public IP address when you associate an Elastic IP address with it\. When you disassociate the Elastic IP address from your instance, it receives a new public IP address\.
+ If the public IP address of your instance in a VPC has been released, it will not receive a new one if there is more than one network interface attached to your instance\. 
+ If your instance's public IP address is released while it has a secondary private IP address that is associated with an Elastic IP address, the instance does not receive a new public IP address\.

If you require a persistent public IP address that can be associated to and from instances as you require, use an Elastic IP address instead\.

If you use dynamic DNS to map an existing DNS name to a new instance's public IP address, it might take up to 24 hours for the IP address to propagate through the Internet\. As a result, new instances might not receive traffic while terminated instances continue to receive requests\. To solve this problem, use an Elastic IP address\. You can allocate your own Elastic IP address, and associate it with your instance\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. 

If you assign an Elastic IP address to an instance, it receives an IPv4 DNS hostname if DNS hostnames are enabled\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the *Amazon VPC User Guide*\.

**Note**  
Instances that access other instances through their public NAT IP address are charged for regional or Internet data transfer, depending on whether the instances are in the same Region\.

## Elastic IP Addresses \(IPv4\)<a name="ip-addressing-eips"></a>

An Elastic IP address is a public IPv4 address that you can allocate to your account\. You can associate it to and from instances as you require, and it's allocated to your account until you choose to release it\. For more information about Elastic IP addresses and how to use them, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\.

We do not support Elastic IP addresses for IPv6\.

## Amazon DNS Server<a name="amazon-dns-server"></a>

Amazon provides a DNS server that resolves Amazon\-provided IPv4 DNS hostnames to IPv4 addresses\. The Amazon DNS server is located at the base of your VPC network range plus two\. For more information, see [Amazon DNS Server](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_DHCP_Options.html#AmazonDNS) in the *Amazon VPC User Guide*\.

## IPv6 Addresses<a name="ipv6-addressing"></a>

You can optionally associate an IPv6 CIDR block with your VPC, and associate IPv6 CIDR blocks with your subnets\. The IPv6 CIDR block for your VPC is automatically assigned from Amazon's pool of IPv6 addresses; you cannot choose the range yourself\. For more information, see the following topics in the *Amazon VPC User Guide*:
+ [VPC and Subnet Sizing for IPv6](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-sizing-ipv6)
+ [Associating an IPv6 CIDR Block with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#vpc-associate-ipv6-cidr)
+ [Associating an IPv6 CIDR Block with Your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#subnet-associate-ipv6-cidr)

IPv6 addresses are globally unique, and therefore reachable over the Internet\. Your instance receives an IPv6 address if an IPv6 CIDR block is associated with your VPC and subnet, and if one of the following is true:
+ Your subnet is configured to automatically assign an IPv6 address to an instance during launch\. For more information, see [Modifying the IPv6 Addressing Attribute for Your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-ipv6)\.
+ You assign an IPv6 address to your instance during launch\.
+ You assign an IPv6 address to the primary network interface of your instance after launch\.
+ You assign an IPv6 address to a network interface in the same subnet, and attach the network interface to your instance after launch\. 

When your instance receives an IPv6 address during launch, the address is associated with the primary network interface \(eth0\) of the instance\. You can disassociate the IPv6 address from the network interface\. We do not support IPv6 DNS hostnames for your instance\.

An IPv6 address persists when you stop and start your instance, and is released when you terminate your instance\. You cannot reassign an IPv6 address while it's assigned to another network interface—you must first unassign it\.

You can assign additional IPv6 addresses to your instance by assigning them to a network interface attached to your instance\. The number of IPv6 addresses you can assign to a network interface and the number of network interfaces you can attach to an instance varies per instance type\. For more information, see [IP Addresses Per Network Interface Per Instance Type](using-eni.md#AvailableIpPerENI)\.

## Working with IP Addresses for Your Instance<a name="working-with-ip-addresses"></a>

You can view the IP addresses assigned to your instance, assign a public IPv4 address to your instance during launch, or assign an IPv6 address to your instance during launch\.

**Topics**
+ [Determining Your Public, Private, and Elastic IP Addresses](#using-instance-addressing-common)
+ [Determining Your IPv6 Addresses](#view-ipv6-addresses)
+ [Assigning a Public IPv4 Address During Instance Launch](#public-ip-addresses)
+ [Assigning an IPv6 Address to an Instance](#assign-ipv6-address)
+ [Unassigning an IPv6 Address From an Instance](#unassign-ipv6-address)

### Determining Your Public, Private, and Elastic IP Addresses<a name="using-instance-addressing-common"></a>

You can use the Amazon EC2 console to determine the private IPv4 addresses, public IPv4 addresses, and Elastic IP addresses of your instances\. You can also determine the public IPv4 and private IPv4 addresses of your instance from within your instance by using instance metadata\. For more information, see [Instance metadata and user data](ec2-instance-metadata.md)\.

**To determine your instance's private IPv4 addresses using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\. In the details pane, get the private IPv4 address from the **Private IPs** field, and get the internal DNS hostname from the **Private DNS** field\.

1. If you have one or more secondary private IPv4 addresses assigned to network interfaces that are attached to your instance, get those IP addresses from the **Secondary private IPs** field\. 

1. Alternatively, in the navigation pane, choose **Network Interfaces**, and then select the network interface that's associated with your instance\. 

1. Get the primary private IP address from the **Primary private IPv4 IP** field, and the internal DNS hostname from the **Private DNS \(IPv4\)** field\. 

1. If you've assigned secondary private IP addresses to the network interface, get those IP addresses from the **Secondary private IPv4 IPs** field\.

**To determine your instance's public IPv4 addresses using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. 

1. Select your instance\. In the details pane, get the public IP address from the **IPv4 Public IP** field, and get the external DNS hostname from the **Public DNS \(IPv4\)** field\.

1. If one or more Elastic IP addresses have been associated with the instance, get the Elastic IP addresses from the **Elastic IPs** field\. 
**Note**  
If your instance does not have a public IPv4 address, but you've associated an Elastic IP address with a network interface for the instance, the **IPv4 Public IP** field displays the Elastic IP address\.

1. Alternatively, in the navigation pane, choose **Network Interfaces**, and then select a network interface that's associated with your instance\. 

1. Get the public IP address from the **IPv4 Public IP** field\. An asterisk \(\*\) indicates the public IPv4 address or Elastic IP address that's mapped to the primary private IPv4 address\. 
**Note**  
The public IPv4 address is displayed as a property of the network interface in the console, but it's mapped to the primary private IPv4 address through NAT\. Therefore, if you inspect the properties of your network interface on your instance, for example, through `ifconfig` \(Linux\) or `ipconfig` \(Windows\), the public IPv4 address is not displayed\. To determine your instance's public IPv4 address from within the instance, you can use instance metadata\. 

**To determine your instance's IPv4 addresses using instance metadata**

1. Connect to your instance\. For more information, see [Connect to Your Linux Instance](AccessingInstances.md)\.

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

   Note that if an Elastic IP address is associated with the instance, the value returned is that of the Elastic IP address\.

### Determining Your IPv6 Addresses<a name="view-ipv6-addresses"></a>

You can use the Amazon EC2 console to determine the IPv6 addresses of your instances\.

**To determine your instance's IPv6 addresses using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\. In the details pane, get the IPv6 addresses from **IPv6 IPs**\.

**To determine your instance's IPv6 addresses using instance metadata**

1. Connect to your instance\. For more information, see [Connect to Your Linux Instance](AccessingInstances.md)\.

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

### Assigning a Public IPv4 Address During Instance Launch<a name="public-ip-addresses"></a>

Each subnet has an attribute that determines whether instances launched into that subnet are assigned a public IP address\. By default, nondefault subnets have this attribute set to false, and default subnets have this attribute set to true\. When you launch an instance, a public IPv4 addressing feature is also available for you to control whether your instance is assigned a public IPv4 address; you can override the default behavior of the subnet's IP addressing attribute\. The public IPv4 address is assigned from Amazon's pool of public IPv4 addresses, and is assigned to the network interface with the device index of eth0\. This feature depends on certain conditions at the time you launch your instance\. 

**Important**  
You can't manually disassociate the public IP address from your instance after launch\. Instead, it's automatically released in certain cases, after which you cannot reuse it\. For more information, see [Public IPv4 Addresses and External DNS Hostnames](#concepts-public-addresses)\. If you require a persistent public IP address that you can associate or disassociate at will, assign an Elastic IP address to the instance after launch instead\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\.

**To access the public IP addressing feature when launching an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Select an AMI and an instance type, and then choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, for **Network**, select a VPC\. The **Auto\-assign Public IP** list is displayed\. Choose **Enable** or **Disable** to override the default setting for the subnet\. 
**Important**  
You cannot auto\-assign a public IP address if you specify more than one network interface\. Additionally, you cannot override the subnet setting using the auto\-assign public IP feature if you specify an existing network interface for eth0\. 

1. Follow the steps on the next pages of the wizard to complete your instance's setup\. For more information about the wizard configuration options, see [Launching an instance using the Launch Instance Wizard](launching-instance.md)\. On the final **Review Instance Launch** page, review your settings, and then choose **Launch** to choose a key pair and launch your instance\.

1. On the **Instances** page, select your new instance and view its public IP address in **IPv4 Public IP** field in the details pane\.

The public IP addressing feature is only available during launch\. However, whether you assign a public IP address to your instance during launch or not, you can associate an Elastic IP address with your instance after it's launched\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. You can also modify your subnet's public IPv4 addressing behavior\. For more information, see [Modifying the Public IPv4 Addressing Attribute for Your Subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip)\.<a name="publicip-cli"></a>

**To enable or disable the public IP addressing feature using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ Use the `--associate-public-ip-address` or the `--no-associate-public-ip-address` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command \(AWS CLI\)
+ Use the `-AssociatePublicIp` parameter with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)

### Assigning an IPv6 Address to an Instance<a name="assign-ipv6-address"></a>

If your VPC and subnet have IPv6 CIDR blocks associated with them, you can assign an IPv6 address to your instance during or after launch\. The IPv6 address is assigned from the IPv6 address range of the subnet, and is assigned to the network interface with the device index of eth0\. 

IPv6 is supported on all current generation instance types and the C3, R3, and I2 previous generation instance types\.

**To assign an IPv6 address to an instance during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Select an AMI and an instance type that supports IPv6, and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, for **Network**, select a VPC and for **Subnet**, select a subnet\. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. Follow the remaining steps in the wizard to launch your instance\.

Alternatively, you can assign an IPv6 address to your instance after launch\.

**To assign an IPv6 address to your instance after launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Assign new IP**\. You can specify an IPv6 address from the range of the subnet, or leave the **Auto\-assign** value to let Amazon choose an IPv6 address for you\.

1. Choose **Save**\.

**Note**  
If you launched your instance using Amazon Linux 2016\.09\.0 or later, or Windows Server 2008 R2 or later, your instance is configured for IPv6, and no additional steps are needed to ensure that the IPv6 address is recognized on the instance\. If you launched your instance from an older AMI, you may have to configure your instance manually\. For more information, see [Configure IPv6 on Your Instances](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-migrate-ipv6.html#vpc-migrate-ipv6-dhcpv6) in the *Amazon VPC User Guide*\.<a name="assign-ipv6-cli"></a>

**To assign an IPv6 address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ Use the `--ipv6-addresses` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command \(AWS CLI\)
+ Use the `Ipv6Addresses` property for `-NetworkInterface` in the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)
+ [assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) \(AWS CLI\)
+ [Register\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)

### Unassigning an IPv6 Address From an Instance<a name="unassign-ipv6-address"></a>

You can unassign an IPv6 address from an instance using the Amazon EC2 console\.

**To unassign an IPv6 address from an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Unassign** for the IPv6 address to unassign\.

1. Choose **Yes, Update**\.

**To unassign an IPv6 address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) \(AWS CLI\)
+ [Unregister\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)\.
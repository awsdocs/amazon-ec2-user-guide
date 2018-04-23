# Elastic Network Interfaces<a name="using-eni"></a>

An elastic network interface \(referred to as a *network interface* in this documentation\) is a logical networking component in a VPC that represents a virtual network card\.

A network interface can include the following attributes:

+ A primary private IPv4 address from the IPv4 address range of your VPC

+ One or more secondary private IPv4 addresses from the IPv4 address range of your VPC

+ One Elastic IP address \(IPv4\) per private IPv4 address

+ One public IPv4 address

+ One or more IPv6 addresses

+ One or more security groups

+ A MAC address

+ A source/destination check flag

+ A description

You can create and configure network interfaces in your account and attach them to instances in your VPC\. Your account might also have *requester\-managed* network interfaces, which are created and managed by AWS services to enable you to use other resources and services\. You cannot manage these network interfaces yourself\. For more information, see [Requester\-Managed Network Interfaces](requester-managed-eni.md)\.

All network interfaces have the *eni\-xxxxxxxx* resource identifier\.

**Important**  
The term 'elastic network interface' is sometimes shortened to 'ENI'\. This is not the same as the Elastic Network Adapter \(ENA\), which is a custom interface that optimizes network performance on some instance types\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.


+ [Network Interface Basics](#eni-basics)
+ [IP Addresses Per Network Interface Per Instance Type](#AvailableIpPerENI)
+ [Scenarios for Network Interfaces](#scenarios-enis)
+ [Best Practices for Configuring Network Interfaces](#best-practices-for-configuring-network-interfaces)
+ [Working with Network Interfaces](#working-with-enis)
+ [Requester\-Managed Network Interfaces](requester-managed-eni.md)

## Network Interface Basics<a name="eni-basics"></a>

You can create a network interface, attach it to an instance, detach it from an instance, and attach it to another instance\. The attributes of a network interface follow it as it's attached or detached from an instance and reattached to another instance\. When you move a network interface from one instance to another, network traffic is redirected to the new instance\.

You can also modify the attributes of your network interface, including changing its security groups and managing its IP addresses\.

Every instance in a VPC has a default network interface, called the *primary network interface* \(eth0\)\. You cannot detach a primary network interface from an instance\. You can create and attach additional network interfaces\. The maximum number of network interfaces that you can use varies by instance type\. For more information, see [IP Addresses Per Network Interface Per Instance Type](#AvailableIpPerENI)\.

**Public IPv4 addresses for network interfaces**  
In a VPC, all subnets have a modifiable attribute that determines whether network interfaces created in that subnet \(and therefore instances launched into that subnet\) are assigned a public IPv4 address\. For more information, see [IP Addressing Behavior for Your Subnet](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-ip-addressing.html#vpc-ip-addressing-subnet) in the *Amazon VPC User Guide*\. The public IPv4 address is assigned from Amazon's pool of public IPv4 addresses\. When you launch an instance, the IP address is assigned to the primary network interface \(eth0\) that's created\.

When you create a network interface, it inherits the public IPv4 addressing attribute from the subnet\. If you later modify the public IPv4 addressing attribute of the subnet, the network interface keeps the setting that was in effect when it was created\. If you launch an instance and specify an existing network interface for eth0, the public IPv4 addressing attribute is determined by the network interface\.

For more information, see [Public IPv4 Addresses and External DNS Hostnames](using-instance-addressing.md#concepts-public-addresses)\.

**IPv6 addresses for network interfaces**  
You can associate an IPv6 CIDR block with your VPC and subnet, and assign one or more IPv6 addresses from the subnet range to a network interface\.

All subnets have a modifiable attribute that determines whether network interfaces created in that subnet \(and therefore instances launched into that subnet\) are automatically assigned an IPv6 address from the range of the subnet\. For more information, see [IP Addressing Behavior for Your Subnet](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-ip-addressing.html#vpc-ip-addressing-subnet) in the *Amazon VPC User Guide*\. When you launch an instance, the IPv6 address is assigned to the primary network interface \(eth0\) that's created\.

For more information, see [IPv6 Addresses](using-instance-addressing.md#ipv6-addressing)\.

**Monitoring IP Traffic**  
You can enable a VPC flow log on your network interface to capture information about the IP traffic going to and from a network interface\. After you've created a flow log, you can view and retrieve its data in Amazon CloudWatch Logs\. For more information, see [VPC Flow Logs](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/flow-logs.html) in the *Amazon VPC User Guide*\.

## IP Addresses Per Network Interface Per Instance Type<a name="AvailableIpPerENI"></a>

The following table lists the maximum number of network interfaces per instance type, and the maximum number of private IPv4 addresses and IPv6 addresses per network interface\. The limit for IPv6 addresses is separate from the limit for private IPv4 addresses per network interface\. Not all instance types support IPv6 addressing\. Network interfaces, multiple private IPv4 addresses, and IPv6 addresses are only available for instances running in a VPC\. For more information, see [Multiple IP Addresses](MultipleIP.md)\. For more information about IPv6 in VPC, see [IP Addressing in Your VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-ip-addressing.html) in the *Amazon VPC User Guide*\.


| Instance Type | Maximum Network Interfaces | IPv4 Addresses per Interface | IPv6 Addresses per Interface | 
| --- | --- | --- | --- | 
|  `c1.medium`  |  2  |  6  | IPv6 not supported | 
|  `c1.xlarge`  |  4  |  15  | IPv6 not supported | 
|  `c3.large`  |  3  |  10  | 10 | 
|  `c3.xlarge`  |  4  |  15  | 15 | 
|  `c3.2xlarge`  |  4  |  15  | 15 | 
|  `c3.4xlarge`  |  8  |  30  | 30 | 
|  `c3.8xlarge`  |  8  |  30  | 30 | 
|  `c4.large`  |  3  |  10  | 10 | 
|  `c4.xlarge`  |  4  |  15  | 15 | 
|  `c4.2xlarge`  |  4  |  15  | 15 | 
|  `c4.4xlarge`  |  8  |  30  | 30 | 
|  `c4.8xlarge`  |  8  |  30  | 30 | 
| `c5.large` | 3 | 10 | 10 | 
| `c5.xlarge` | 4 | 15 | 15 | 
| `c5.2xlarge` | 4 | 15 | 15 | 
| `c5.4xlarge` | 8 | 30 | 30 | 
| `c5.9xlarge` | 8 | 30 | 30 | 
| `c5.18xlarge` | 15 | 50 | 50 | 
|  `cc2.8xlarge`  |  8  |  30  | IPv6 not supported | 
|  `cr1.8xlarge`  |  8  |  30  | IPv6 not supported | 
|  `d2.xlarge`  |  4  |  15  | 15 | 
|  `d2.2xlarge`  |  4  |  15  | 15 | 
|  `d2.4xlarge`  |  8  |  30  | 30 | 
|  `d2.8xlarge`  |  8  |  30  | 30 | 
| f1\.2xlarge | 4 | 15 | 15 | 
| f1\.16xlarge | 8 | 50 | 50 | 
|  `g2.2xlarge`  |  4  |  15  | IPv6 not supported | 
|  `g2.8xlarge`  |  8  |  30  | IPv6 not supported | 
| g3\.4xlarge | 8 | 30 | 30 | 
| g3\.8xlarge | 8 | 30 | 30 | 
| g3\.16xlarge | 15 | 50 | 50 | 
| h1\.2xlarge | 4 | 15 | 15 | 
| h1\.4xlarge | 8 | 30 | 30 | 
| h1\.8xlarge | 8 | 30 | 30 | 
| h1\.16xlarge | 15 | 50 | 50 | 
|  `hs1.8xlarge`  |  8  |  30  | IPv6 not supported | 
|  `i2.xlarge`  |  4  |  15  | 15 | 
|  `i2.2xlarge`  |  4  |  15  | 15 | 
|  `i2.4xlarge`  |  8  |  30  | 30 | 
|  `i2.8xlarge`  |  8  |  30  | 30 | 
| `i3.large` | 3 | 10 | 10 | 
| `i3.xlarge` | 4 | 15 | 15 | 
| `i3.2xlarge` | 4 | 15 | 15 | 
| `i3.4xlarge` | 8 | 30 | 30 | 
| `i3.8xlarge` | 8 | 30 | 30 | 
| `i3.16xlarge` | 15 | 50 | 50 | 
|  `m1.small`  |  2  |  4  | IPv6 not supported | 
|  `m1.medium`  |  2  |  6  | IPv6 not supported | 
|  `m1.large`  |  3  |  10  | IPv6 not supported | 
|  `m1.xlarge`  |  4  |  15  | IPv6 not supported | 
|  `m2.xlarge`  |  4  |  15  | IPv6 not supported | 
|  `m2.2xlarge`  |  4  |  30  | IPv6 not supported | 
|  `m2.4xlarge`  |  8  |  30  | IPv6 not supported | 
|  `m3.medium`  |  2  |  6  | IPv6 not supported | 
|  `m3.large`  |  3  |  10  | IPv6 not supported | 
|  `m3.xlarge`  |  4  |  15  | IPv6 not supported | 
|  `m3.2xlarge`  |  4  |  30  | IPv6 not supported | 
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
| m5\.12xlarge | 8 | 30 | 30 | 
| m5\.24xlarge | 15 | 50 | 50 | 
| p2\.xlarge | 4 | 15 | 15 | 
| p2\.8xlarge | 8 | 30 | 30 | 
| p2\.16xlarge | 8 | 30 | 30 | 
| p3\.2xlarge | 4 | 15 | 15 | 
| p3\.8xlarge | 8 | 30 | 30 | 
| p3\.16xlarge | 8 | 30 | 30 | 
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
|  `t1.micro`  |  2  |  2  | IPv6 not supported | 
|  `t2.nano`  |  2  |  2  | 2 | 
|  `t2.micro`  |  2  |  2  | 2 | 
|  `t2.small`  |  2  |  4  | 4 | 
|  `t2.medium`  |  3  |  6  | 6 | 
|  `t2.large`  |  3  |  12  | 12 | 
|  `t2.xlarge`  |  3  |  15  |  15  | 
|  `t2.2xlarge`  |  3  |  15  |  15  | 
| x1\.16xlarge | 8 | 30 | 30 | 
| x1\.32xlarge | 8 | 30 | 30 | 
| x1e\.xlarge | 3 | 10 | 10 | 
| x1e\.2xlarge | 4 | 15 | 15 | 
| x1e\.4xlarge | 4 | 15 | 15 | 
| x1e\.8xlarge | 4 | 15 | 15 | 
| x1e\.16xlarge | 8 | 30 | 30 | 
| x1e\.32xlarge | 8 | 30 | 30 | 

## Scenarios for Network Interfaces<a name="scenarios-enis"></a>

Attaching multiple network interfaces to an instance is useful when you want to:

+ Create a management network\.

+ Use network and security appliances in your VPC\.

+ Create dual\-homed instances with workloads/roles on distinct subnets\.

+ Create a low\-budget, high\-availability solution\.

### Creating a Management Network<a name="creating-a-management-network"></a>

You can create a management network using network interfaces\. In this scenario, the primary network interface \(`eth0`\) on the instance handles public traffic and the secondary network interface \(`eth1`\) handles backend management traffic and is connected to a separate subnet in your VPC that has more restrictive access controls\. The public interface, which may or may not be behind a load balancer, has an associated security group that allows access to the server from the internet \(for example, allow TCP port 80 and 443 from `0.0.0.0/0`, or from the load balancer\) while the private facing interface has an associated security group allowing SSH access only from an allowed range of IP addresses either within the VPC or from the internet, a private subnet within the VPC or a virtual private gateway\.

To ensure failover capabilities, consider using a secondary private IPv4 for incoming traffic on a network interface\. In the event of an instance failure, you can move the interface and/or secondary private IPv4 address to a standby instance\.

![\[Creating a Management Network\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_ENI_management_network.png)

### Use Network and Security Appliances in Your VPC<a name="use-network-and-security-appliances-in-your-vpc"></a>

Some network and security appliances, such as load balancers, network address translation \(NAT\) servers, and proxy servers prefer to be configured with multiple network interfaces\. You can create and attach secondary network interfaces to instances in a VPC that are running these types of applications and configure the additional interfaces with their own public and private IP addresses, security groups, and source/destination checking\.

### Creating Dual\-homed Instances with Workloads/Roles on Distinct Subnets<a name="creating-dual-homed-instances-with-workloads-roles-on-distinct-subnets"></a>

You can place a network interface on each of your web servers that connects to a mid\-tier network where an application server resides\. The application server can also be dual\-homed to a backend network \(subnet\) where the database server resides\. Instead of routing network packets through the dual\-homed instances, each dual\-homed instance receives and processes requests on the front end, initiates a connection to the backend, and then sends requests to the servers on the backend network\.

### Create a Low Budget High Availability Solution<a name="create-a-low-budget-high-availability-solution"></a>

If one of your instances serving a particular function fails, its network interface can be attached to a replacement or hot standby instance pre\-configured for the same role in order to rapidly recover the service\. For example, you can use a network interface as your primary or secondary network interface to a critical service such as a database instance or a NAT instance\. If the instance fails, you \(or more likely, the code running on your behalf\) can attach the network interface to a hot standby instance\. Because the interface maintains its private IP addresses, Elastic IP addresses, and MAC address, network traffic begins flowing to the standby instance as soon as you attach the network interface to the replacement instance\. Users experience a brief loss of connectivity between the time the instance fails and the time that the network interface is attached to the standby instance, but no changes to the VPC route table or your DNS server are required\.

## Best Practices for Configuring Network Interfaces<a name="best-practices-for-configuring-network-interfaces"></a>

+ You can attach a network interface to an instance when it's running \(hot attach\), when it's stopped \(warm attach\), or when the instance is being launched \(cold attach\)\.

+ You can detach secondary \(eth*N*\) network interfaces when the instance is running or stopped\. However, you can't detach the primary \(eth0\) interface\.

+ You can attach a network interface in one subnet to an instance in another subnet in the same VPC; however, both the network interface and the instance must reside in the same Availability Zone\.

+ When launching an instance from the CLI or API, you can specify the network interfaces to attach to the instance for both the primary \(eth0\) and additional network interfaces\. 

+ Launching an Amazon Linux or Windows Server instance with multiple network interfaces automatically configures interfaces, private IPv4 addresses, and route tables on the operating system of the instance\. 

+ A warm or hot attach of an additional network interface may require you to manually bring up the second interface, configure the private IPv4 address, and modify the route table accordingly\. Instances running Amazon Linux or Windows Server automatically recognize the warm or hot attach and configure themselves\.

+ Attaching another network interface to an instance \(for example, a NIC teaming configuration\) cannot be used as a method to increase or double the network bandwidth to or from the dual\-homed instance\.

+ If you attach two or more network interfaces from the same subnet to an instance, you may encounter networking issues such as asymmetric routing\. If possible, use a secondary private IPv4 address on the primary network interface instead\. For more information, see [Assigning a Secondary Private IPv4 Address](MultipleIP.md#ManageMultipleIP)\. 

### Configuring Your Network Interface Using ec2\-net\-utils<a name="ec2-net-utils"></a>

Amazon Linux AMIs may contain additional scripts installed by AWS, known as ec2\-net\-utils\. These scripts optionally automate the configuration of your network interfaces\. These scripts are available for Amazon Linux only\.

Use the following command to install the package on Amazon Linux if it's not already installed, or update it if it's installed and additional updates are available:

```
$ yum install ec2-net-utils
```

The following components are part of ec2\-net\-utils:

udev rules \(`/etc/udev/rules.d`\)  
Identifies network interfaces when they are attached, detached, or reattached to a running instance, and ensures that the hotplug script runs \(`53-ec2-network-interfaces.rules`\)\. Maps the MAC address to a device name \(`75-persistent-net-generator.rules`, which generates `70-persistent-net.rules`\)\.

hotplug script  
Generates an interface configuration file suitable for use with DHCP \(`/etc/sysconfig/network-scripts/ifcfg-eth`*N*\)\. Also generates a route configuration file \(`/etc/sysconfig/network-scripts/route-eth`*N*\)\.

DHCP script  
Whenever the network interface receives a new DHCP lease, this script queries the instance metadata for Elastic IP addresses\. For each Elastic IP address, it adds a rule to the routing policy database to ensure that outbound traffic from that address uses the correct network interface\. It also adds each private IP address to the network interface as a secondary address\.

ec2ifup eth*N*  
Extends the functionality of the standard ifup\. After this script rewrites the configuration files `ifcfg-eth`*N* and `route-eth`*N*, it runs ifup\.

ec2ifdown eth*N*  
Extends the functionality of the standard ifdown\. After this script removes any rules for the network interface from the routing policy database, it runs ifdown\.

ec2ifscan  
Checks for network interfaces that have not been configured and configures them\.  
This script isn't available in the initial release of ec2\-net\-utils\.

To list any configuration files that were generated by ec2\-net\-utils, use the following command:

```
$ ls -l /etc/sysconfig/network-scripts/*-eth?
```

To disable the automation on a per\-instance basis, you can add `EC2SYNC=no` to the corresponding `ifcfg-eth`*N* file\. For example, use the following command to disable the automation for the eth1 interface:

```
$ sed -i -e 's/^EC2SYNC=yes/EC2SYNC=no/' /etc/sysconfig/network-scripts/ifcfg-eth1
```

To disable the automation completely, you can remove the package using the following command:

```
$ yum remove ec2-net-utils
```

## Working with Network Interfaces<a name="working-with-enis"></a>

You can work with network interfaces using the Amazon EC2 console or the command line\.


+ [Creating a Network Interface](#create_eni)
+ [Deleting a Network Interface](#delete_eni)
+ [Viewing Details about a Network Interface](#view_eni_details)
+ [Attaching a Network Interface When Launching an Instance](#attach_eni_launch)
+ [Attaching a Network Interface to a Stopped or Running Instance](#attach_eni_running_stopped)
+ [Detaching a Network Interface from an Instance](#detach_eni)
+ [Changing the Security Group](#eni_security_group)
+ [Changing the Source or Destination Checking](#change_source_dest_check)
+ [Associating an Elastic IP Address \(IPv4\)](#associate_eip)
+ [Disassociating an Elastic IP Address \(IPv4\)](#disassociate_eip)
+ [Assigning an IPv6 Address](#eni-assign-ipv6)
+ [Unassigning an IPv6 Address](#eni-unassign-ipv6)
+ [Changing Termination Behavior](#change_term_behavior)
+ [Adding or Editing a Description](#add_edit_description)
+ [Adding or Editing Tags](#eni_add_edit_tags)

### Creating a Network Interface<a name="create_eni"></a>

You can create a network interface in a subnet\. You can't move the network interface to another subnet after it's created, and you can only attach the network interface to instances in the same Availability Zone\.

**To create a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Choose **Create Network Interface**\.

1. For **Description**, enter a descriptive name\.

1. For **Subnet**, select the subnet\.

1. For **Private IP** \(or **IPv4 Private IP**\), enter the primary private IPv4 address\. If you don't specify an IPv4 address, we select an available private IPv4 address from within the selected subnet\.

1. \(IPv6 only\) If you selected a subnet that has an associated IPv6 CIDR block, you can optionally specify an IPv6 address in the **IPv6 IP** field\. 

1. For **Security groups**, select one or more security groups\.

1. Choose **Yes, Create**\.

**To create a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [create\-network\-interface](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-network-interface.html) \(AWS CLI\)

+ [New\-EC2NetworkInterface](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Deleting a Network Interface<a name="delete_eni"></a>

To delete an instance, you must first detach the network interface\. Deleting a network interface releases all attributes associated with the interface and releases any private IP addresses or Elastic IP addresses to be used by another instance\.

**To delete a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select a network interface and choose **Delete**\.

1. In the **Delete Network Interface** dialog box, choose **Yes, Delete**\.

**To delete a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [delete\-network\-interface](http://docs.aws.amazon.com/cli/latest/reference/ec2/delete-network-interface.html) \(AWS CLI\)

+ [Remove\-EC2NetworkInterface](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Viewing Details about a Network Interface<a name="view_eni_details"></a>

You can view all the network interfaces in your account\.

**To describe a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. To view the details, choose **Details**\.

**To describe a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [describe\-network\-interfaces](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) \(AWS CLI\)

+ [Get\-EC2NetworkInterface](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

**To describe a network interface attribute using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [describe\-network\-interface\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interface-attribute.html) \(AWS CLI\)

+ [Get\-EC2NetworkInterfaceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Attaching a Network Interface When Launching an Instance<a name="attach_eni_launch"></a>

You can specify an existing network interface or attach an additional network interface when you launch an instance\.

**Note**  
If an error occurs when attaching a network interface to your instance, this causes the instance launch to fail\.

**To attach a network interface when launching an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Select an AMI and instance type and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select a VPC for **Network**, and a subnet for **Subnet**\.

1. In the **Network Interfaces** section, the console enables you to specify up to two network interfaces \(new, existing, or a combination\) when you launch an instance\. You can also enter a primary IPv4 address and one or more secondary IPv4 addresses for any new interface\. 

   You can add additional network interfaces to the instance after you launch it\. The total number of network interfaces that you can attach varies by instance type\. For more information, see [IP Addresses Per Network Interface Per Instance Type](#AvailableIpPerENI)\.
**Note**  
If you specify more than one network interface, you cannot auto\-assign a public IPv4 address to your instance\. 

1. \(IPv6 only\) If you're launching an instance into a subnet that has an associated IPv6 CIDR block, you can specify IPv6 addresses for any network interfaces that you attach\. Under **IPv6 IPs**, choose **Add IP**\. To add a secondary IPv6 address, choose **Add IP** again\. You can enter an IPv6 address from the range of the subnet, or leave the default **Auto\-assign** value to let Amazon choose an IPv6 address from the subnet for you\.

1. Choose **Next: Add Storage**\.

1. On the **Add Storage** page, you can specify volumes to attach to the instance besides the volumes specified by the AMI \(such as the root device volume\), and then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify tags for the instance, such as a user\-friendly name, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, you can select a security group or create a new one\. Choose **Review and Launch**\. 
**Note**  
If you specified an existing network interface in step 5, the instance is associated with the security group for that network interface, regardless of any option that you select in this step\.

1. On the **Review Instance Launch** page, details about the primary and additional network interface are displayed\. Review the settings, and then choose **Launch** to choose a key pair and launch your instance\. If you're new to Amazon EC2 and haven't created any key pairs, the wizard prompts you to create one\.

**To attach a network interface when launching an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)

+ [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

### Attaching a Network Interface to a Stopped or Running Instance<a name="attach_eni_running_stopped"></a>

You can attach a network interface to any of your stopped or running instances in your VPC, using either the **Instances** or **Network Interfaces** pages of the Amazon EC2 console\. 

**Note**  
If the public IPv4 address on your instance is released, it does not receive a new one if there is more than one network interface attached to the instance\. For more information about the behavior of public IPv4 addresses, see [Public IPv4 Addresses and External DNS Hostnames](using-instance-addressing.md#concepts-public-addresses)\.

**To attach a network interface to an instance using the Instances page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Actions**, **Networking**, **Attach Network Interface**\.

1. In the **Attach Network Interface** dialog box, select the network interface and choose **Attach**\.

**To attach a network interface to an instance using the Network Interfaces page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Attach**\.

1. In the **Attach Network Interface** dialog box, select the instance and choose **Attach**\.

**To attach a network interface to an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [attach\-network\-interface](http://docs.aws.amazon.com/cli/latest/reference/ec2/attach-network-interface.html) \(AWS CLI\)

+ [Add\-EC2NetworkInterface](http://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Detaching a Network Interface from an Instance<a name="detach_eni"></a>

You can detach a secondary network interface at any time, using either the **Instances** or **Network Interfaces** page of the Amazon EC2 console\.

**To detach a network interface from an instance using the Instances page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Actions**, **Networking**, **Detach Network Interface**\.

1. In the **Detach Network Interface** dialog box, select the network interface and choose **Detach**\.

**To detach a network interface from an instance using the Network Interfaces page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Detach**\.

1. In the **Detach Network Interface** dialog box, choose **Yes, Detach**\. If the network interface fails to detach from the instance, choose **Force detachment**, and then try again\.

**To detach a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [detach\-network\-interface](http://docs.aws.amazon.com/cli/latest/reference/ec2/detach-network-interface.html) \(AWS CLI\)

+ [Dismount\-EC2NetworkInterface](http://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Changing the Security Group<a name="eni_security_group"></a>

You can change the security groups that are associated with a network interface\. When you create the security group, be sure to specify the same VPC as the subnet for the network interface\.

**Note**  
To change security group membership for interfaces owned by other services, such as Elastic Load Balancing, use the console or command line interface for that service\.

**To change the security group of a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Security Groups**\.

1. In the **Change Security Groups** dialog box, select the security groups to use, and choose **Save**\.

**To change the security group of a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [modify\-network\-interface\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-network-interface-attribute.html) \(AWS CLI\)

+ [Edit\-EC2NetworkInterfaceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Changing the Source or Destination Checking<a name="change_source_dest_check"></a>

The Source/Destination Check attribute controls whether source/destination checking is enabled on the instance\. Disabling this attribute enables an instance to handle network traffic that isn't specifically destined for the instance\. For example, instances running services such as network address translation, routing, or a firewall should set this value to `disabled`\. The default value is `enabled`\.

**To change source/destination checking for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Source/Dest Check**\.

1. In the dialog box, choose **Enabled** \(if enabling\) or **Disabled** \(if disabling\), and **Save**\.

**To change source/destination checking for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [modify\-network\-interface\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-network-interface-attribute.html) \(AWS CLI\)

+ [Edit\-EC2NetworkInterfaceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Associating an Elastic IP Address \(IPv4\)<a name="associate_eip"></a>

If you have an Elastic IP address \(IPv4\), you can associate it with one of the private IPv4 addresses for the network interface\. You can associate one Elastic IP address with each private IPv4 address\.

You can associate an Elastic IP address using the Amazon EC2 console or the command line\.

**To associate an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Associate Address**\.

1. In the **Associate Elastic IP Address** dialog box, select the Elastic IP address from the **Address** list\.

1. For **Associate to private IP address**, select the private IPv4 address to associate with the Elastic IP address\. 

1. Choose **Allow reassociation** to allow the Elastic IP address to be associated with the specified network interface if it's currently associated with another instance or network interface, and then choose **Associate Address**\.

**To associate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [associate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)

+ [Register\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Disassociating an Elastic IP Address \(IPv4\)<a name="disassociate_eip"></a>

If the network interface has an Elastic IP address \(IPv4\) associated with it, you can disassociate the address, and then either associate it with another network interface or release it back to the address pool\. This is the only way to associate an Elastic IP address with an instance in a different subnet or VPC using a network interface, as network interfaces are specific to a particular subnet\.

You can disassociate an Elastic IP address using the Amazon EC2 console or the command line\.

**To disassociate an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Disassociate Address**\.

1. In the **Disassociate IP Address** dialog box, choose **Yes, Disassociate**\.

**To disassociate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [disassociate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) \(AWS CLI\)

+ [Unregister\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Assigning an IPv6 Address<a name="eni-assign-ipv6"></a>

You can assign one or more IPv6 addresses to a network interface\. The network interface must be in a subnet that has an associated IPv6 CIDR block\. To assign a specific IPv6 address to the network interface, ensure that the IPv6 address is not already assigned to another network interface\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces** and select the network interface\.

1. Choose **Actions**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Assign new IP**\. Specify an IPv6 address from the range of the subnet\. To let AWS choose an address for you, leave the **Auto\-assign** value\.

1. Choose **Yes, Update**\.

**To assign an IPv6 address to a network interface using the command line**

+ You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

  + [assign\-ipv6\-addresses](http://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) \(AWS CLI\)

  + [Register\-EC2Ipv6AddressList](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)

### Unassigning an IPv6 Address<a name="eni-unassign-ipv6"></a>

You can unassign an IPv6 address from a network interface using the Amazon EC2 console\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces** and select the network interface\.

1. Choose **Actions**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Unassign** for the IPv6 address to remove\.

1. Choose **Yes, Update**\.

**To unassign an IPv6 address from a network interface using the command line**

+ You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

  + [unassign\-ipv6\-addresses](http://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) \(AWS CLI\)

  + [Unregister\-EC2Ipv6AddressList](http://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)

### Changing Termination Behavior<a name="change_term_behavior"></a>

You can set the termination behavior for a network interface that's attached to an instance\. You can specify whether the network interface should be automatically deleted when you terminate the instance to which it's attached\.

You can change the terminating behavior for a network interface using the Amazon EC2 console or the command line\.

**To change the termination behavior for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Termination Behavior**\.

1. In the **Change Termination Behavior** dialog box, select the **Delete on termination** check box if you want the network interface to be deleted when you terminate an instance\.

**To change the termination behavior for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [modify\-network\-interface\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-network-interface-attribute.html) \(AWS CLI\)

+ [Edit\-EC2NetworkInterfaceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Adding or Editing a Description<a name="add_edit_description"></a>

You can change the description for a network interface using the Amazon EC2 console or the command line\.

**To change the description for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface and choose **Actions**, **Change Description**\.

1. In the **Change Description** dialog box, enter a description for the network interface, and then choose **Save**\.

**To change the description for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [modify\-network\-interface\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-network-interface-attribute.html) \(AWS CLI\)

+ [Edit\-EC2NetworkInterfaceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2NetworkInterfaceAttribute.html) \(AWS Tools for Windows PowerShell\)

### Adding or Editing Tags<a name="eni_add_edit_tags"></a>

Tags are metadata that you can add to a network interface\. Tags are private and are only visible to your account\. Each tag consists of a key and an optional value\. For more information about tags, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.

**To add or edit tags for a network interface using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface\.

1. In the details pane, choose **Tags**, **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, choose **Create Tag** for each tag to create, and enter a key and optional value\. When you're done, choose **Save**\.

**To add or edit tags for a network interface using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [create\-tags](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)

+ [New\-EC2Tag](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

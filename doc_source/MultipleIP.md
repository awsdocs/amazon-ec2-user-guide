# Multiple IP addresses<a name="MultipleIP"></a>

You can specify multiple private IPv4 and IPv6 addresses for your instances\. The number of network interfaces and private IPv4 and IPv6 addresses that you can specify for an instance depends on the instance type\. For more information, see [IP addresses per network interface per instance type](using-eni.md#AvailableIpPerENI)\.

It can be useful to assign multiple IP addresses to an instance in your VPC to do the following:
+ Host multiple websites on a single server by using multiple SSL certificates on a single server and associating each certificate with a specific IP address\.
+ Operate network appliances, such as firewalls or load balancers, that have multiple IP addresses for each network interface\. 
+ Redirect internal traffic to a standby instance in case your instance fails, by reassigning the secondary IP address to the standby instance\. 

**Topics**
+ [How multiple IP addresses work](#MultipleIPReqs)
+ [Work with multiple IPv4 addresses](#working-with-multiple-ipv4)
+ [Work with multiple IPv6 addresses](#working-with-multiple-ipv6)

## How multiple IP addresses work<a name="MultipleIPReqs"></a>

The following list explains how multiple IP addresses work with network interfaces:
+ You can assign a secondary private IPv4 address to any network interface\. The network interface need not be attached to the instance\.
+ You can assign multiple IPv6 addresses to a network interface that's in a subnet that has an associated IPv6 CIDR block\.
+ You must choose a secondary IPv4 address from the IPv4 CIDR block range of the subnet for the network interface\.
+ You must choose IPv6 addresses from the IPv6 CIDR block range of the subnet for the network interface\.
+ You associate security groups with network interfaces, not individual IP addresses\. Therefore, each IP address you specify in a network interface is subject to the security group of its network interface\.
+ Multiple IP addresses can be assigned and unassigned to network interfaces attached to running or stopped instances\.
+ Secondary private IPv4 addresses that are assigned to a network interface can be reassigned to another one if you explicitly allow it\.
+ An IPv6 address cannot be reassigned to another network interface; you must first unassign the IPv6 address from the existing network interface\.
+ When assigning multiple IP addresses to a network interface using the command line tools or API, the entire operation fails if one of the IP addresses can't be assigned\.
+ Primary private IPv4 addresses, secondary private IPv4 addresses, Elastic IP addresses, and IPv6 addresses remain with a secondary network interface when it is detached from an instance or attached to an instance\.
+ Although you can't detach the primary network interface from an instance, you can reassign the secondary private IPv4 address of the primary network interface to another network interface\.

The following list explains how multiple IP addresses work with Elastic IP addresses \(IPv4 only\):
+ Each private IPv4 address can be associated with a single Elastic IP address, and vice versa\.
+ When a secondary private IPv4 address is reassigned to another interface, the secondary private IPv4 address retains its association with an Elastic IP address\.
+ When a secondary private IPv4 address is unassigned from an interface, an associated Elastic IP address is automatically disassociated from the secondary private IPv4 address\.

## Work with multiple IPv4 addresses<a name="working-with-multiple-ipv4"></a>

You can assign a secondary private IPv4 address to an instance, associate an Elastic IPv4 address with a secondary private IPv4 address, and unassign a secondary private IPv4 address\.

**Topics**
+ [Assign a secondary private IPv4 address](#ManageMultipleIP)
+ [Configure the operating system on your instance to recognize secondary private IPv4 addresses](#StepTwoConfigOS)
+ [Associate an Elastic IP address with the secondary private IPv4 address](#StepThreeEIP)
+ [View your secondary private IPv4 addresses](#view-privateIPs)
+ [Unassign a secondary private IPv4 address](#UnassignSPIP)

### Assign a secondary private IPv4 address<a name="ManageMultipleIP"></a>

You can assign the secondary private IPv4 address to the network interface for an instance as you launch the instance, or after the instance is running\. This section includes the following procedures\.
+ [To assign a secondary private IPv4 address when launching an instance](#assignIP-launch)
+ [To assign a secondary IPv4 address during launch using the command line](#assignIP-launch-cmd)
+ [To assign a secondary private IPv4 address to a network interface](#assignIP-existing)
+ [To assign a secondary private IPv4 to an existing instance using the command line](#assignIP-existing-cmd)<a name="assignIP-launch"></a>

**To assign a secondary private IPv4 address when launching an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. Select an AMI, then choose an instance type and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, for **Network**, select a VPC and for **Subnet**, select a subnet\.

1. In the **Network Interfaces** section, do the following, and then choose **Next: Add Storage**:
   + To add another network interface, choose **Add Device**\. The console enables you to specify up to two network interfaces when you launch an instance\. After you launch the instance, choose **Network Interfaces** in the navigation pane to add additional network interfaces\. The total number of network interfaces that you can attach varies by instance type\. For more information, see [IP addresses per network interface per instance type](using-eni.md#AvailableIpPerENI)\. 
**Important**  
When you add a second network interface, the system can no longer auto\-assign a public IPv4 address\. You will not be able to connect to the instance over IPv4 unless you assign an Elastic IP address to the primary network interface \(eth0\)\. You can assign the Elastic IP address after you complete the Launch wizard\. For more information, see [Work with Elastic IP addresses](elastic-ip-addresses-eip.md#working-with-eips)\.
   + For each network interface, under **Secondary IP addresses**, choose **Add IP**, and then enter a private IP address from the subnet range, or accept the default `Auto-assign` value to let Amazon select an address\.

1. On the next **Add Storage** page, you can specify volumes to attach to the instance besides the volumes specified by the AMI \(such as the root device volume\), and then choose **Next: Add Tags**\.

1. On the **Add Tags** page, specify tags for the instance, such as a user\-friendly name, and then choose **Next: Configure Security Group**\.

1. On the **Configure Security Group** page, select an existing security group or create a new one\. Choose **Review and Launch**\. 

1. On the **Review Instance Launch** page, review your settings, and then choose **Launch** to choose a key pair and launch your instance\. If you're new to Amazon EC2 and haven't created any key pairs, the wizard prompts you to create one\.

**Important**  
After you have added a secondary private IP address to a network interface, you must connect to the instance and configure the secondary private IP address on the instance itself\. For more information, see [Configure the operating system on your instance to recognize secondary private IPv4 addresses](#StepTwoConfigOS)\.<a name="assignIP-launch-cmd"></a>

**To assign a secondary IPv4 address during launch using the command line**
+ You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
  + The `--secondary-private-ip-addresses` option with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command \(AWS CLI\)
  + Define `-NetworkInterface` and specify the `PrivateIpAddresses` parameter with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)\.<a name="assignIP-existing"></a>

**To assign a secondary private IPv4 address to a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**, and then select the network interface attached to the instance\.

1. Choose **Actions**, **Manage IP Addresses**\.

1. Under **IPv4 Addresses**, choose **Assign new IP**\.

1. Enter a specific IPv4 address that's within the subnet range for the instance, or leave the field blank to let Amazon select an IP address for you\.

1. \(Optional\) Choose **Allow reassignment** to allow the secondary private IP address to be reassigned if it is already assigned to another network interface\.

1. Choose **Yes, Update**\.

Alternatively, you can assign a secondary private IPv4 address to an instance\. Choose **Instances** in the navigation pane, select the instance, and then choose **Actions**, **Networking**, **Manage IP Addresses**\. You can configure the same information as you did in the steps above\. The IP address is assigned to the primary network interface \(eth0\) for the instance\. <a name="assignIP-existing-cmd"></a>

**To assign a secondary private IPv4 to an existing instance using the command line**
+ You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
  + [assign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-private-ip-addresses.html) \(AWS CLI\)
  + [Register\-EC2PrivateIpAddress](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2PrivateIpAddress.html) \(AWS Tools for Windows PowerShell\)

### Configure the operating system on your instance to recognize secondary private IPv4 addresses<a name="StepTwoConfigOS"></a>

After you assign a secondary private IPv4 address to your instance, you need to configure the operating system on your instance to recognize the secondary private IP address\.
+ If you are using Amazon Linux, the ec2\-net\-utils package can take care of this step for you\. It configures additional network interfaces that you attach while the instance is running, refreshes secondary IPv4 addresses during DHCP lease renewal, and updates the related routing rules\. You can immediately refresh the list of interfaces by using the command `sudo service network restart` and then view the up\-to\-date list using `ip addr li`\. If you require manual control over your network configuration, you can remove the ec2\-net\-utils package\. For more information, see [Configure your network interface using ec2\-net\-utils](best-practices-for-configuring-network-interfaces.md#ec2-net-utils)\.
+ If you are using another Linux distribution, see the documentation for your Linux distribution\. Search for information about configuring additional network interfaces and secondary IPv4 addresses\. If the instance has two or more interfaces on the same subnet, search for information about using routing rules to work around asymmetric routing\.

For information about configuring a Windows instance, see [Configuring a secondary private IP address for your Windows instance in a VPC](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/config-windows-multiple-ip.html) in the *Amazon EC2 User Guide for Windows Instances*\.

### Associate an Elastic IP address with the secondary private IPv4 address<a name="StepThreeEIP"></a>

**To associate an Elastic IP address with a secondary private IPv4 address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose** Elastic IPs**\.

1. Choose **Actions**, and then select **Associate address**\.

1. For **Network interface**, select the network interface, and then select the secondary IP address from the **Private IP** list\.

1. Choose **Associate**\.

**To associate an Elastic IP address with a secondary private IPv4 address using the command line**
+ You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
  + [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)
  + [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### View your secondary private IPv4 addresses<a name="view-privateIPs"></a>

**To view the private IPv4 addresses assigned to a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface with private IP addresses to view\.

1. On the **Details** tab in the details pane, check the **Primary private IPv4 IP** and **Secondary private IPv4 IPs** fields for the primary private IPv4 address and any secondary private IPv4 addresses assigned to the network interface\.

**To view the private IPv4 addresses assigned to an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance with private IPv4 addresses to view\.

1. On the **Description** tab in the details pane, check the **Private IPs** and **Secondary private IPs** fields for the primary private IPv4 address and any secondary private IPv4 addresses assigned to the instance through its network interface\.

### Unassign a secondary private IPv4 address<a name="UnassignSPIP"></a>

If you no longer require a secondary private IPv4 address, you can unassign it from the instance or the network interface\. When a secondary private IPv4 address is unassigned from a network interface, the Elastic IP address \(if it exists\) is also disassociated\.

**To unassign a secondary private IPv4 address from an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select an instance, choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv4 Addresses**, choose **Unassign** for the IPv4 address to unassign\.

1. Choose **Yes, Update**\.

**To unassign a secondary private IPv4 address from a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select the network interface, choose **Actions**, **Manage IP Addresses**\.

1. Under **IPv4 Addresses**, choose **Unassign** for the IPv4 address to unassign\.

1. Choose **Yes, Update**\.

**To unassign a secondary private IPv4 address using the command line**
+ You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
  + [unassign\-private\-ip\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-private-ip-addresses.html) \(AWS CLI\)
  + [Unregister\-EC2PrivateIpAddress](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2PrivateIpAddress.html) \(AWS Tools for Windows PowerShell\)

## Work with multiple IPv6 addresses<a name="working-with-multiple-ipv6"></a>

You can assign multiple IPv6 addresses to your instance, view the IPv6 addresses assigned to your instance, and unassign IPv6 addresses from your instance\.

**Topics**
+ [Assign multiple IPv6 addresses](#assign-multiple-ipv6)
+ [View your IPv6 addresses](#view-secondary-ipv6)
+ [Unassign an IPv6 address](#unassign-secondary-ipv6)

### Assign multiple IPv6 addresses<a name="assign-multiple-ipv6"></a>

You can assign one or more IPv6 addresses to your instance during launch or after launch\. To assign an IPv6 address to an instance, the VPC and subnet in which you launch the instance must have an associated IPv6 CIDR block\. For more information, see [VPCs and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html) in the *Amazon VPC User Guide*\.

**To assign multiple IPv6 addresses during launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the dashboard, choose **Launch Instance**\.

1. Select an AMI, choose an instance type, and choose **Next: Configure Instance Details**\. Ensure that you choose an instance type that support IPv6\. For more information, see [Instance types](instance-types.md)\.

1. On the **Configure Instance Details** page, select a VPC from the **Network** list, and a subnet from the **Subnet** list\. 

1. In the **Network Interfaces** section, do the following, and then choose **Next: Add Storage**:
   + To assign a single IPv6 address to the primary network interface \(eth0\), under **IPv6 IPs**, choose **Add IP**\. To add a secondary IPv6 address, choose **Add IP** again\. You can enter an IPv6 address from the range of the subnet, or leave the default **Auto\-assign** value to let Amazon choose an IPv6 address from the subnet for you\.
   + Choose **Add Device** to add another network interface and repeat the steps above to add one or more IPv6 addresses to the network interface\. The console enables you to specify up to two network interfaces when you launch an instance\. After you launch the instance, choose **Network Interfaces** in the navigation pane to add additional network interfaces\. The total number of network interfaces that you can attach varies by instance type\. For more information, see [IP addresses per network interface per instance type](using-eni.md#AvailableIpPerENI)\. 

1. Follow the next steps in the wizard to attach volumes and tag your instance\.

1. On the **Configure Security Group** page, select an existing security group or create a new one\. If you want your instance to be reachable over IPv6, ensure that your security group has rules that allow access from IPv6 addresses\. For more information, see [Security group rules for different use cases](security-group-rules-reference.md)\. Choose **Review and Launch**\. 

1. On the **Review Instance Launch** page, review your settings, and then choose **Launch** to choose a key pair and launch your instance\. If you're new to Amazon EC2 and haven't created any key pairs, the wizard prompts you to create one\.

You can use the **Instances** screen Amazon EC2 console to assign multiple IPv6 addresses to an existing instance\. This assigns the IPv6 addresses to the primary network interface \(eth0\) for the instance\. To assign a specific IPv6 address to the instance, ensure that the IPv6 address is not already assigned to another instance or network interface\.

**To assign multiple IPv6 addresses to an existing instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Assign new IP** for each IPv6 address you want to add\. You can specify an IPv6 address from the range of the subnet, or leave the **Auto\-assign** value to let Amazon choose an IPv6 address for you\.

1. Choose **Yes, Update**\.

Alternatively, you can assign multiple IPv6 addresses to an existing network interface\. The network interface must have been created in a subnet that has an associated IPv6 CIDR block\. To assign a specific IPv6 address to the network interface, ensure that the IPv6 address is not already assigned to another network interface\.

**To assign multiple IPv6 addresses to a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select your network interface, choose **Actions**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Assign new IP** for each IPv6 address you want to add\. You can specify an IPv6 address from the range of the subnet, or leave the **Auto\-assign** value to let Amazon choose an IPv6 address for you\.

1. Choose **Yes, Update**\.

**CLI overview**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ **Assign an IPv6 address during launch**:
  + Use the `--ipv6-addresses` or `--ipv6-address-count` options with the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command \(AWS CLI\)
  + Define `-NetworkInterface` and specify the `Ipv6Addresses` or `Ipv6AddressCount` parameters with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)\.
+ **Assign an IPv6 address to a network interface**:
  + [assign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/assign-ipv6-addresses.html) \(AWS CLI\)
  + [Register\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)

### View your IPv6 addresses<a name="view-secondary-ipv6"></a>

You can view the IPv6 addresses for an instance or for a network interface\.

**To view the IPv6 addresses assigned to an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance\. In the details pane, review the **IPv6 IPs** field\.

**To view the IPv6 addresses assigned to a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select your network interface\. In the details pane, review the **IPv6 IPs** field\.

**CLI overview**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ **View the IPv6 addresses for an instance**:
  + [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
  + [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\.
+ **View the IPv6 addresses for a network interface**:
  + [describe\-network\-interfaces](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-network-interfaces.html) \(AWS CLI\)
  + [Get\-EC2NetworkInterface](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2NetworkInterface.html) \(AWS Tools for Windows PowerShell\)

### Unassign an IPv6 address<a name="unassign-secondary-ipv6"></a>

You can unassign an IPv6 address from the primary network interface of an instance, or you can unassign an IPv6 address from a network interface\.

**To unassign an IPv6 address from an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select your instance, choose **Actions**, **Networking**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Unassign** for the IPv6 address to unassign\.

1. Choose **Yes, Update**\.

**To unassign an IPv6 address from a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Select your network interface, choose **Actions**, **Manage IP Addresses**\.

1. Under **IPv6 Addresses**, choose **Unassign** for the IPv6 address to unassign\.

1. Choose **Save**\.

**CLI overview**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [unassign\-ipv6\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/unassign-ipv6-addresses.html) \(AWS CLI\)
+ [Unregister\-EC2Ipv6AddressList](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Ipv6AddressList.html) \(AWS Tools for Windows PowerShell\)\.
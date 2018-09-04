# ClassicLink<a name="vpc-classiclink"></a>

ClassicLink allows you to link your EC2\-Classic instance to a VPC in your account, within the same region\. This allows you to associate the VPC security groups with the EC2\-Classic instance, enabling communication between your EC2\-Classic instance and instances in your VPC using private IPv4 addresses\. ClassicLink removes the need to make use of public IPv4 addresses or Elastic IP addresses to enable communication between instances in these platforms\. For more information about private and public IPv4 addresses, see [IP Addressing in Your VPC](http://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html)\.

ClassicLink is available to all users with accounts that support the EC2\-Classic platform, and can be used with any EC2\-Classic instance\. To find out which platform your account supports, see [Supported Platforms](ec2-supported-platforms.md)\. For more information about the benefits of using a VPC, see [Amazon EC2 and Amazon Virtual Private Cloud](using-vpc.md)\. For more information about migrating your resources to a VPC, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.

There is no additional charge for using ClassicLink\. Standard charges for data transfer and instance usage apply\.

**Note**  
EC2\-Classic instances cannot be enabled for IPv6 communication\. You can associate an IPv6 CIDR block with your VPC and assign IPv6 address to resources in your VPC, however, communication between a ClassicLinked instance and resources in the VPC is over IPv4 only\.

**Topics**
+ [ClassicLink Basics](#classiclink-basics)
+ [ClassicLink Limitations](#classiclink-limitations)
+ [Working with ClassicLink](#working-with-classiclink)
+ [API and CLI Overview](#classiclink-api-cli)
+ [Example: ClassicLink Security Group Configuration for a Three\-Tier Web Application](#classiclink-security-groups-example)

## ClassicLink Basics<a name="classiclink-basics"></a>

There are two steps to linking an EC2\-Classic instance to a VPC using ClassicLink\. First, you must enable the VPC for ClassicLink\. By default, all VPCs in your account are not enabled for ClassicLink, to maintain their isolation\. After you've enabled the VPC for ClassicLink, you can then link any running EC2\-Classic instance in the same region in your account to that VPC\. Linking your instance includes selecting security groups from the VPC to associate with your EC2\-Classic instance\. After you've linked the instance, it can communicate with instances in your VPC using their private IP addresses, provided the VPC security groups allow it\. Your EC2\-Classic instance does not lose its private IP address when linked to the VPC\.

**Note**  
Linking your instance to a VPC is sometimes referred to as *attaching* your instance\.

A linked EC2\-Classic instance can communicate with instances in a VPC, but it does not form part of the VPC\. If you list your instances and filter by VPC, for example, through the `DescribeInstances` API request, or by using the **Instances** screen in the Amazon EC2 console, the results do not return any EC2\-Classic instances that are linked to the VPC\. For more information about viewing your linked EC2\-Classic instances, see [Viewing Your ClassicLink\-Enabled VPCs and Linked EC2\-Classic Instances](#classiclink-describe-vpcs-instances)\.

By default, if you use a public DNS hostname to address an instance in a VPC from a linked EC2\-Classic instance, the hostname resolves to the instance's public IP address\. The same occurs if you use a public DNS hostname to address a linked EC2\-Classic instance from an instance in the VPC\. If you want the public DNS hostname to resolve to the private IP address, you can enable ClassicLink DNS support for the VPC\. For more information, see [Enabling ClassicLink DNS Support](#classiclink-enable-dns-support)\.

If you no longer require a ClassicLink connection between your instance and the VPC, you can unlink the EC2\-Classic instance from the VPC\. This disassociates the VPC security groups from the EC2\-Classic instance\. A linked EC2\-Classic instance is automatically unlinked from a VPC when it's stopped\. After you've unlinked all linked EC2\-Classic instances from the VPC, you can disable ClassicLink for the VPC\.

### Using Other AWS Services in Your VPC With ClassicLink<a name="classiclink-other-services"></a>

Linked EC2\-Classic instances can access the following AWS services in the VPC: Amazon Redshift, Amazon ElastiCache, Elastic Load Balancing, and Amazon RDS\. However, instances in the VPC cannot access the AWS services provisioned by the EC2\-Classic platform using ClassicLink\.

If you use Elastic Load Balancing, you can register your linked EC2\-Classic instances with the load balancer\. You must create your load balancer in the ClassicLink\-enabled VPC and enable the Availability Zone in which the instance runs\. If you terminate the linked EC2\-Classic instance, the load balancer deregisters the instance\.

If you use Amazon EC2 Auto Scaling, you can create an Amazon EC2 Auto Scaling group with instances that are automatically linked to a specified ClassicLink\-enabled VPC at launch\. For more information, see [Linking EC2\-Classic Instances to a VPC](http://docs.aws.amazon.com/autoscaling/latest/userguide/asg-in-vpc.html#as-ClassicLink) in the *Amazon EC2 Auto Scaling User Guide*\.

If you use Amazon RDS instances or Amazon Redshift clusters in your VPC, and they are publicly accessible \(accessible from the Internet\), the endpoint you use to address those resources from a linked EC2\-Classic instance by default resolves to a public IP address\. If those resources are not publicly accessible, the endpoint resolves to a private IP address\. To address a publicly accessible RDS instance or Redshift cluster over private IP using ClassicLink, you must use their private IP address or private DNS hostname, or you must enable ClassicLink DNS support for the VPC\.

If you use a private DNS hostname or a private IP address to address an RDS instance, the linked EC2\-Classic instance cannot use the failover support available for Multi\-AZ deployments\.

You can use the Amazon EC2 console to find the private IP addresses of your Amazon Redshift, Amazon ElastiCache, or Amazon RDS resources\.

**To locate the private IP addresses of AWS resources in your VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Check the descriptions of the network interfaces in the **Description** column\. A network interface that's used by Amazon Redshift, Amazon ElastiCache, or Amazon RDS will have the name of the service in the description\. For example, a network interface that's attached to an Amazon RDS instance will have the following description: `RDSNetworkInterface`\.

1. Select the required network interface\. 

1. In the details pane, get the private IP address from the **Primary private IPv4 IP** field\. 

### Controlling the Use of ClassicLink<a name="classiclink-iam"></a>

By default, IAM users do not have permission to work with ClassicLink\. You can create an IAM policy that grants users permissions to enable or disable a VPC for ClassicLink, link or unlink an instance to a ClassicLink\-enabled VPC, and to view ClassicLink\-enabled VPCs and linked EC2\-Classic instances\. For more information about IAM policies for Amazon EC2, see [IAM Policies for Amazon EC2](iam-policies-for-amazon-ec2.md)\. 

For more information about policies for working with ClassicLink, see the following example: [7\. Working with ClassicLink](ExamplePolicies_EC2.md#iam-example-classiclink)\.

### Security Groups in ClassicLink<a name="classiclink-security-groups"></a>

Linking your EC2\-Classic instance to a VPC does not affect your EC2\-Classic security groups\. They continue to control all traffic to and from the instance\. This excludes traffic to and from instances in the VPC, which is controlled by the VPC security groups that you associated with the EC2\-Classic instance\. EC2\-Classic instances that are linked to the same VPC cannot communicate with each other through the VPC; regardless of whether they are associated with the same VPC security group\. Communication between EC2\-Classic instances is controlled by the EC2\-Classic security groups associated with those instances\. For an example of a security group configuration, see [Example: ClassicLink Security Group Configuration for a Three\-Tier Web Application](#classiclink-security-groups-example)\.

After you've linked your instance to a VPC, you cannot change which VPC security groups are associated with the instance\. To associate different security groups with your instance, you must first unlink the instance, and then link it to the VPC again, choosing the required security groups\.

### Routing for ClassicLink<a name="classiclink-routing"></a>

When you enable a VPC for ClassicLink, a static route is added to all of the VPC route tables with a destination of `10.0.0.0/8` and a target of `local`\. This allows communication between instances in the VPC and any EC2\-Classic instances that are then linked to the VPC\. If you add a custom route table to a ClassicLink\-enabled VPC, a static route is automatically added with a destination of `10.0.0.0/8` and a target of `local`\. When you disable ClassicLink for a VPC, this route is automatically deleted in all of the VPC route tables\.

VPCs that are in the `10.0.0.0/16` and `10.1.0.0/16` IP address ranges can be enabled for ClassicLink only if they do not have any existing static routes in route tables in the `10.0.0.0/8` IP address range, excluding the local routes that were automatically added when the VPC was created\. Similarly, if you've enabled a VPC for ClassicLink, you may not be able to add any more specific routes to your route tables within the `10.0.0.0/8` IP address range\. 

**Important**  
If your VPC CIDR block is a publicly routable IP address range, consider the security implications before you link an EC2\-Classic instance to your VPC\. For example, if your linked EC2\-Classic instance receives an incoming Denial of Service \(DoS\) request flood attack from a source IP address that falls within the VPC’s IP address range, the response traffic is sent into your VPC\. We strongly recommend that you create your VPC using a private IP address range as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\.

For more information about route tables and routing in your VPC, see [Route Tables](http://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html) in the *Amazon VPC User Guide*\.

### Enabling a VPC Peering Connection for ClassicLink<a name="classiclink-peering"></a>

If you have a VPC peering connection between two VPCs, and there are one or more EC2\-Classic instances that are linked to one or both of the VPCs via ClassicLink, you can extend the VPC peering connection to enable communication between the EC2\-Classic instances and the instances in the VPC on the other side of the VPC peering connection\. This enables the EC2\-Classic instances and the instances in the VPC to communicate using private IP addresses\. To do this, you can enable a local VPC to communicate with a linked EC2\-Classic instance in a peer VPC, or you can enable a local linked EC2\-Classic instance to communicate with instances in a peer VPC\.

If you enable a local VPC to communicate with a linked EC2\-Classic instance in a peer VPC, a static route is automatically added to your route tables with a destination of `10.0.0.0/8` and a target of `local`\. 

For more information and examples, see [Configurations With ClassicLink](http://docs.aws.amazon.com/vpc/latest/peering/peering-configurations-classiclink.html) in the *Amazon VPC Peering Guide*\.

## ClassicLink Limitations<a name="classiclink-limitations"></a>

To use the ClassicLink feature, you need to be aware of the following limitations:
+ You can link an EC2\-Classic instance to only one VPC at a time\.
+ If you stop your linked EC2\-Classic instance, it's automatically unlinked from the VPC and the VPC security groups are no longer associated with the instance\. You can link your instance to the VPC again after you've restarted it\.
+ You cannot link an EC2\-Classic instance to a VPC that's in a different region or a different AWS account\.
+ You cannot use ClassicLink to link a VPC instance to a different VPC, or to a EC2\-Classic resource\. To establish a private connection between VPCs, you can use a VPC peering connection\. For more information, see the [Amazon VPC Peering Guide](http://docs.aws.amazon.com/vpc/latest/peering/)\.
+ You cannot use ClassicLink to enable communication between an EC2\-Classic instance and the following instances: C5, C5d, `i3.metal`, M5, M5d, R5, R5d, T3, and z1d\.
+ You cannot associate a VPC Elastic IP address with a linked EC2\-Classic instance\.
+ VPCs with routes that conflict with the EC2\-Classic private IP address range of `10/8` cannot be enabled for ClassicLink\. This does not include VPCs with `10.0.0.0/16` and `10.1.0.0/16` IP address ranges that already have local routes in their route tables\. For more information, see [Routing for ClassicLink](#classiclink-routing)\.
+ VPCs configured for dedicated hardware tenancy cannot be enabled for ClassicLink\. Contact AWS support to request that your dedicated tenancy VPC be allowed to be enabled for ClassicLink\.
**Important**  
EC2\-Classic instances are run on shared hardware\. If you've set the tenancy of your VPC to `dedicated` because of regulatory or security requirements, then linking an EC2\-Classic instance to your VPC might not conform to those requirements, as this allows a shared tenancy resource to address your isolated resources directly using private IP addresses\. If you need to enable your dedicated VPC for ClassicLink, provide a detailed reason in your request to AWS support\.
+ If you link your EC2\-Classic instance to a VPC in the `172.16.0.0/16` range, and you have a DNS server running on the `172.16.0.23/32` IP address within the VPC, then your linked EC2\-Classic instance can't access the VPC DNS server\. To work around this issue, run your DNS server on a different IP address within the VPC\.
+ ClassicLink doesn't support transitive relationships out of the VPC\. Your linked EC2\-Classic instance doesn't have access to any VPN connection, VPC gateway endpoint, NAT gateway, or Internet gateway associated with the VPC\. Similarly, resources on the other side of a VPN connection or an Internet gateway don't have access to a linked EC2\-Classic instance\.

## Working with ClassicLink<a name="working-with-classiclink"></a>

You can use the Amazon EC2 and Amazon VPC consoles to work with the ClassicLink feature\. You can enable or disable a VPC for ClassicLink, and link and unlink EC2\-Classic instances to a VPC\.

**Note**  
The ClassicLink features are only visible in the consoles for accounts and regions that support EC2\-Classic\. 

**Topics**
+ [Enabling a VPC for ClassicLink](#classiclink-enable-vpc)
+ [Linking an Instance to a VPC](#classiclink-link-instance)
+ [Creating a VPC with ClassicLink Enabled](#classiclink-vpc-wizard)
+ [Linking an EC2\-Classic Instance to a VPC at Launch](#classiclink-launch-wizard)
+ [Viewing Your ClassicLink\-Enabled VPCs and Linked EC2\-Classic Instances](#classiclink-describe-vpcs-instances)
+ [Enabling ClassicLink DNS Support](#classiclink-enable-dns-support)
+ [Disabling ClassicLink DNS Support](#classiclink-disable-dns-support)
+ [Unlinking a EC2\-Classic Instance from a VPC](#classiclink-unlink-instance)
+ [Disabling ClassicLink for a VPC](#classiclink-disable-vpc)

### Enabling a VPC for ClassicLink<a name="classiclink-enable-vpc"></a>

To link an EC2\-Classic instance to a VPC, you must first enable the VPC for ClassicLink\. You cannot enable a VPC for ClassicLink if the VPC has routing that conflicts with the EC2\-Classic private IP address range\. For more information, see [Routing for ClassicLink](#classiclink-routing)\.

**To enable a VPC for ClassicLink**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Choose a VPC, and then choose **Actions**, **Enable ClassicLink**\.

1. In the confirmation dialog box, choose **Yes, Enable**\.

### Linking an Instance to a VPC<a name="classiclink-link-instance"></a>

After you've enabled a VPC for ClassicLink, you can link an EC2\-Classic instance to it\.

**Note**  
You can only link a running EC2\-Classic instance to a VPC\. You cannot link an instance that's in the `stopped` state\.

**To link an instance to a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. 

1. Select the running EC2\-Classic instance, choose **Actions**, **ClassicLink**, **Link to VPC**\. You can select more than one instance to link to the same VPC\.

1. In the dialog box that displays, select a VPC from the list\. Only VPCs that have been enabled for ClassicLink are displayed\.

1. Select one or more of the VPC security groups to associate with your instance\. When you are done, choose **Link to VPC**\.

### Creating a VPC with ClassicLink Enabled<a name="classiclink-vpc-wizard"></a>

You can create a new VPC and immediately enable it for ClassicLink by using the VPC wizard in the Amazon VPC console\.

**To create a VPC with ClassicLink enabled**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the Amazon VPC dashboard, choose **Start VPC Wizard**\.

1. Select one of the VPC configuration options and choose **Select**\. 

1. On the next page of the wizard, choose **Yes** for **Enable ClassicLink**\. Complete the rest of the steps in the wizard to create your VPC\. For more information about using the VPC wizard, see [Scenarios for Amazon VPC](http://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenarios.html) in the *Amazon VPC User Guide*\.

### Linking an EC2\-Classic Instance to a VPC at Launch<a name="classiclink-launch-wizard"></a>

You can use the launch wizard in the Amazon EC2 console to launch an EC2\-Classic instance and immediately link it to a ClassicLink\-enabled VPC\.

**To link an instance to a VPC at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the Amazon EC2 dashboard, choose **Launch Instance**\.

1. Select an AMI, and then choose an instance type\. On the **Configure Instance Details** page, ensure that you select **Launch into EC2\-Classic** from the **Network** list\.
**Note**  
Some instance types, such as T2 instance types, can only be launched into a VPC\. Ensure that you select an instance type that can be launched into EC2\-Classic\.

1. In the **Link to VPC \(ClassicLink\)** section, select a VPC from **Link to VPC**\. Only ClassicLink\-enabled VPCs are displayed\. Select the security groups from the VPC to associate with the instance\. Complete the other configuration options on the page, and then complete the rest of the steps in the wizard to launch your instance\. For more information about using the launch wizard, see [Launching Your Instance from an AMI](launching-instance.md#launch-instance-console)\.

### Viewing Your ClassicLink\-Enabled VPCs and Linked EC2\-Classic Instances<a name="classiclink-describe-vpcs-instances"></a>

You can view all of your ClassicLink\-enabled VPCs in the Amazon VPC console, and your linked EC2\-Classic instances in the Amazon EC2 console\.

**To view your ClassicLink\-enabled VPCs**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select a VPC, and in the **Summary** tab, look for the **ClassicLink** field\. A value of **Enabled** indicates that the VPC is enabled for ClassicLink\.

1. Alternatively, look for the **ClassicLink** column, and view the value that's displayed for each VPC \(**Enabled** or **Disabled**\)\. If the column is not visible, choose **Edit Table Columns** \(the gear\-shaped icon\), select the **ClassicLink** attribute, and then choose **Close**\. 

**To view your linked EC2\-Classic instances**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select an EC2\-Classic instance, and in the **Description** tab, look for the **ClassicLink** field\. If the instance is linked to a VPC, the field displays the ID of the VPC to which the instance is linked\. If the instance is not linked to any VPC, the field displays **Unlinked**\.

1. Alternatively, you can filter your instances to display only linked EC2\-Classic instances for a specific VPC or security group\. In the search bar, start typing `ClassicLink`, select the relevant ClassicLink resource attribute, and then select the security group ID or the VPC ID\.

### Enabling ClassicLink DNS Support<a name="classiclink-enable-dns-support"></a>

You can enable ClassicLink DNS support for your VPC so that DNS hostnames that are addressed between linked EC2\-Classic instances and instances in the VPC resolve to private IP addresses and not public IP addresses\. For this feature to work, your VPC must be enabled for DNS hostnames and DNS resolution\.

**Note**  
If you enable ClassicLink DNS support for your VPC, your linked EC2\-Classic instance can access any private hosted zone associated with the VPC\. For more information, see [Working with Private Hosted Zones](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) in the *Amazon Route 53 Developer Guide*\. 

**To enable ClassicLink DNS support**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, and choose **Actions**, **Edit ClassicLink DNS Support**\.

1. Choose **Yes** to enable ClassicLink DNS support, and choose **Save**\.

### Disabling ClassicLink DNS Support<a name="classiclink-disable-dns-support"></a>

You can disable ClassicLink DNS support for your VPC so that DNS hostnames that are addressed between linked EC2\-Classic instances and instances in the VPC resolve to public IP addresses and not private IP addresses\. 

**To disable ClassicLink DNS support**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, and choose **Actions**, **Edit ClassicLink DNS Support**\.

1. Choose **No** to disable ClassicLink DNS support, and choose **Save**\.

### Unlinking a EC2\-Classic Instance from a VPC<a name="classiclink-unlink-instance"></a>

If you no longer require a ClassicLink connection between your EC2\-Classic instance and your VPC, you can unlink the instance from the VPC\. Unlinking the instance disassociates the VPC security groups from the instance\.

**Note**  
A stopped instance is automatically unlinked from a VPC\.

**To unlink an instance from a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select your instance\.

1. In the **Actions** list, select **ClassicLink**, **Unlink Instance**\. You can select more than one instance to unlink from the same VPC\. 

1. Choose **Yes** in the confirmation dialog box\.

### Disabling ClassicLink for a VPC<a name="classiclink-disable-vpc"></a>

If you no longer require a connection between EC2\-Classic instances and your VPC, you can disable ClassicLink on the VPC\. You must first unlink all linked EC2\-Classic instances that are linked to the VPC\.

**To disable ClassicLink for a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC, then choose **Actions**, **Disable ClassicLink**\.

1. In the confirmation dialog box, choose **Yes, Disable**\.

## API and CLI Overview<a name="classiclink-api-cli"></a>

You can perform the tasks described on this page using the command line or the Query API\. For more information about the command line interfaces and a list of available API actions, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

**Enable a VPC for ClassicLink**
+ [enable\-vpc\-classic\-link](http://docs.aws.amazon.com/cli/latest/reference/ec2/enable-vpc-classic-link.html) \(AWS CLI\)
+ [Enable\-EC2VpcClassicLink](http://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VpcClassicLink.html) \(AWS Tools for Windows PowerShell\)
+ [EnableVpcClassicLink](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-EnableVpcClassicLink.html) \(Amazon EC2 Query API\)

**Link \(attach\) an EC2\-Classic instance to a VPC**
+ [attach\-classic\-link\-vpc](http://docs.aws.amazon.com/cli/latest/reference/ec2/attach-classic-link-vpc.html) \(AWS CLI\)
+ [Add\-EC2ClassicLinkVpc](http://docs.aws.amazon.com/powershell/latest/reference/items/Add-EC2ClassicLinkVpc.html) \(AWS Tools for Windows PowerShell\)
+ [AttachClassicLinkVpc](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-AttachClassicLinkVpc.html) \(Amazon EC2 Query API\)

**Unlink \(detach\) an EC2\-Classic instance from a VPC**
+ [detach\-classic\-link\-vpc](http://docs.aws.amazon.com/cli/latest/reference/ec2/detach-classic-link-vpc.html) \(AWS CLI\)
+ [Dismount\-EC2ClassicLinkVpc](http://docs.aws.amazon.com/powershell/latest/reference/items/Dismount-EC2ClassicLinkVpc.html) \(AWS Tools for Windows PowerShell\)
+ [DetachClassicLinkVpc](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DetachClassicLinkVpc.html) \(Amazon EC2 Query API\)

**Disable ClassicLink for a VPC**
+ [disable\-vpc\-classic\-link](http://docs.aws.amazon.com/cli/latest/reference/ec2/disable-vpc-classic-link.html) \(AWS CLI\)
+ [Disable\-EC2VpcClassicLink](http://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2VpcClassicLink.html) \(AWS Tools for Windows PowerShell\)
+ [DisableVpcClassicLink](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DisableVpcClassicLink.html) \(Amazon EC2 Query API\)

**Describe the ClassicLink status of VPCs**
+ [describe\-vpc\-classic\-link](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-classic-link.html) \(AWS CLI\)
+ [Get\-EC2VpcClassicLink](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcClassicLink.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeVpcClassicLink](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcClassicLink.html) \(Amazon EC2 Query API\)

**Describe linked EC2\-Classic instances**
+ [describe\-classic\-link\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-classic-link-instances.html) \(AWS CLI\)
+ [Get\-EC2ClassicLinkInstance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ClassicLinkInstance.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeClassicLinkInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeClassicLinkInstances.html) \(Amazon EC2 Query API\)

**Enable a VPC peering connection for ClassicLink**
+ [modify\-vpc\-peering\-connection\-options](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-peering-connection-options.html) \(AWS CLI\)
+ [Edit\-EC2VpcPeeringConnectionOption](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VpcPeeringConnectionOption.html) \(AWS Tools for Windows PowerShell\)
+ [ModifyVpcPeeringConnectionOptions](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyVpcPeeringConnectionOptions.html)\(Amazon EC2 Query API\)

**Enable a VPC for ClassicLink DNS support**
+ [enable\-vpc\-classic\-link\-dns\-support](http://docs.aws.amazon.com/cli/latest/reference/ec2/enable-vpc-classic-link-dns-support.html) \(AWS CLI\)
+ [Enable\-EC2VpcClassicLinkDnsSupport](http://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VpcClassicLink.html) \(AWS Tools for Windows PowerShell\)
+ [EnableVpcClassicLinkDnsSupport](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-EnableVpcClassicLinkDnsSupport.html) \(Amazon EC2 Query API\)

**Disable a VPC for ClassicLink DNS support**
+ [disable\-vpc\-classic\-link\-dns\-support](http://docs.aws.amazon.com/cli/latest/reference/ec2/disable-vpc-classic-link-dns-support.html) \(AWS CLI\)
+ [Disable\-EC2VpcClassicLinkDnsSupport](http://docs.aws.amazon.com/powershell/latest/reference/items/Disable-EC2VpcClassicLinkDnsSupport.html) \(AWS Tools for Windows PowerShell\)
+ [DisableVpcClassicLinkDnsSupport](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DisableVpcClassicLinkDnsSupport.html) \(Amazon EC2 Query API\)

**Describe ClassicLink DNS support for VPCs**
+ [describe\-vpc\-classic\-link\-dns\-support](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpc-classic-link-dns-support.html) \(AWS CLI\)
+ [Get\-EC2VpcClassicLinkDnsSupport](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VpcClassicLinkDnsSupport.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeVpcClassicLinkDnsSupport](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeVpcClassicLinkDnsSupport.html) \(Amazon EC2 Query API\)

## Example: ClassicLink Security Group Configuration for a Three\-Tier Web Application<a name="classiclink-security-groups-example"></a>

In this example, you have an application with three instances: a public\-facing web server, an application server, and a database server\. Your web server accepts HTTPS traffic from the Internet, and then communicates with your application server over TCP port 6001\. Your application server then communicates with your database server over TCP port 6004\. You're in the process of migrating your entire application to a VPC in your account\. You've already migrated your application server and your database server to your VPC\. Your web server is still in EC2\-Classic and linked to your VPC via ClassicLink\. 

You want a security group configuration that allows traffic to flow only between these instances\. You have four security groups: two for your web server \(`sg-1a1a1a1a` and `sg-2b2b2b2b`\), one for your application server \(`sg-3c3c3c3c`\), and one for your database server \(`sg-4d4d4d4d`\)\.

The following diagram displays the architecture of your instances, and their security group configuration\.

![\[Security group configuration using ClassicLink.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/classiclink-security-groups-example-diagram.png)

**Security Groups for Your Web Server \(`sg-1a1a1a1a` and `sg-2b2b2b2b`\)**  
You have one security group in EC2\-Classic, and the other in your VPC\. You associated the VPC security group with your web server instance when you linked the instance to your VPC via ClassicLink\. The VPC security group enables you to control the outbound traffic from your web server to your application server\.

The following are the security group rules for the EC2\-Classic security group \(`sg-1a1a1a1a`\)\.


|  | 
| --- |
| Inbound | 
|  Source  |  Type  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  HTTPS  |  443  |  Allows Internet traffic to reach your web server\.  | 

The following are the security group rules for the VPC security group \(`sg-2b2b2b2b`\)\.


|  | 
| --- |
| Outbound | 
|  Destination  |  Type  |  Port Range  |  Comments  | 
|  sg\-3c3c3c3c  |  TCP  |  6001  |  Allows outbound traffic from your web server to your application server in your VPC \(or to any other instance associated with `sg-3c3c3c3c`\)\.  | 

**Security Group for Your Application Server \(`sg-3c3c3c3c`\)**  
The following are the security group rules for the VPC security group that's associated with your application server\.


|  | 
| --- |
| Inbound | 
|  Source  |  Type  |  Port Range  |  Comments  | 
|  sg\-2b2b2b2b  |  TCP  |  6001  |  Allows the specified type of traffic from your web server \(or any other instance associated with `sg-2b2b2b2b`\) to reach your application server\.  | 
| Outbound | 
|  Destination  |  Type  |  Port Range  |  Comments  | 
| sg\-4d4d4d4d | TCP | 6004 | Allows outbound traffic from the application server to the database server \(or to any other instance associated with sg\-4d4d4d4d\)\. | 

**Security Group for Your Database Server \(`sg-4d4d4d4d`\)**  
The following are the security group rules for the VPC security group that's associated with your database server\.


|  | 
| --- |
| Inbound | 
|  Source  |  Type  |  Port Range  |  Comments  | 
|  sg\-3c3c3c3c  |  TCP  |  6004  |  Allows the specified type of traffic from your application server \(or any other instance associated with `sg-3c3c3c3c`\) to reach your database server\.  | 
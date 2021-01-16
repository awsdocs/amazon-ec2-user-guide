# ClassicLink<a name="vpc-classiclink"></a>

ClassicLink allows you to link EC2\-Classic instances to a VPC in your account, within the same Region\. If you associate the VPC security groups with a EC2\-Classic instance, this enables communication between your EC2\-Classic instance and instances in your VPC using private IPv4 addresses\. ClassicLink removes the need to make use of public IPv4 addresses or Elastic IP addresses to enable communication between instances in these platforms\.

ClassicLink is available to all users with accounts that support the EC2\-Classic platform, and can be used with any EC2\-Classic instance\. For more information about migrating your resources to a VPC, see [Migrate from EC2\-Classic to a VPC](vpc-migrate.md)\.

There is no additional charge for using ClassicLink\. Standard charges for data transfer and instance usage apply\.

**Topics**
+ [ClassicLink basics](#classiclink-basics)
+ [ClassicLink limitations](#classiclink-limitations)
+ [Work with ClassicLink](#working-with-classiclink)
+ [Example IAM policies for ClassicLink](#iam-example-classiclink)
+ [Example: ClassicLink security group configuration for a three\-tier web application](#classiclink-security-groups-example)

## ClassicLink basics<a name="classiclink-basics"></a>

There are two steps to linking an EC2\-Classic instance to a VPC using ClassicLink\. First, you must enable the VPC for ClassicLink\. By default, all VPCs in your account are not enabled for ClassicLink, to maintain their isolation\. After you've enabled the VPC for ClassicLink, you can then link any running EC2\-Classic instance in the same Region in your account to that VPC\. Linking your instance includes selecting security groups from the VPC to associate with your EC2\-Classic instance\. After you've linked the instance, it can communicate with instances in your VPC using their private IP addresses, provided the VPC security groups allow it\. Your EC2\-Classic instance does not lose its private IP address when linked to the VPC\.

Linking your instance to a VPC is sometimes referred to as *attaching* your instance\.

A linked EC2\-Classic instance can communicate with instances in a VPC, but it does not form part of the VPC\. If you list your instances and filter by VPC, for example, through the `DescribeInstances` API request, or by using the **Instances** screen in the Amazon EC2 console, the results do not return any EC2\-Classic instances that are linked to the VPC\. For more information about viewing your linked EC2\-Classic instances, see [View your ClassicLink\-enabled VPCs and linked instances](#classiclink-describe-vpcs-instances)\.

By default, if you use a public DNS hostname to address an instance in a VPC from a linked EC2\-Classic instance, the hostname resolves to the instance's public IP address\. The same occurs if you use a public DNS hostname to address a linked EC2\-Classic instance from an instance in the VPC\. If you want the public DNS hostname to resolve to the private IP address, you can enable ClassicLink DNS support for the VPC\. For more information, see [Enable ClassicLink DNS support](#classiclink-enable-dns-support)\.

If you no longer require a ClassicLink connection between your instance and the VPC, you can unlink the EC2\-Classic instance from the VPC\. This disassociates the VPC security groups from the EC2\-Classic instance\. A linked EC2\-Classic instance is automatically unlinked from a VPC when it's stopped\. After you've unlinked all linked EC2\-Classic instances from the VPC, you can disable ClassicLink for the VPC\.

### Use other AWS services in your VPC with ClassicLink<a name="classiclink-other-services"></a>

Linked EC2\-Classic instances can access the following AWS services in the VPC: Amazon Redshift, Amazon ElastiCache, Elastic Load Balancing, and Amazon RDS\. However, instances in the VPC cannot access the AWS services provisioned by the EC2\-Classic platform using ClassicLink\.

If you use Elastic Load Balancing, you can register your linked EC2\-Classic instances with the load balancer\. You must create your load balancer in the ClassicLink\-enabled VPC and enable the Availability Zone in which the instance runs\. If you terminate the linked EC2\-Classic instance, the load balancer deregisters the instance\.

If you use Amazon EC2 Auto Scaling, you can create an Amazon EC2 Auto Scaling group with instances that are automatically linked to a specified ClassicLink\-enabled VPC at launch\. For more information, see [Linking EC2\-Classic Instances to a VPC](https://docs.aws.amazon.com/autoscaling/latest/userguide/asg-in-vpc.html#as-ClassicLink) in the *Amazon EC2 Auto Scaling User Guide*\.

If you use Amazon RDS instances or Amazon Redshift clusters in your VPC, and they are publicly accessible \(accessible from the Internet\), the endpoint you use to address those resources from a linked EC2\-Classic instance by default resolves to a public IP address\. If those resources are not publicly accessible, the endpoint resolves to a private IP address\. To address a publicly accessible RDS instance or Redshift cluster over private IP using ClassicLink, you must use their private IP address or private DNS hostname, or you must enable ClassicLink DNS support for the VPC\.

If you use a private DNS hostname or a private IP address to address an RDS instance, the linked EC2\-Classic instance cannot use the failover support available for Multi\-AZ deployments\.

You can use the Amazon EC2 console to find the private IP addresses of your Amazon Redshift, Amazon ElastiCache, or Amazon RDS resources\.

**To locate the private IP addresses of AWS resources in your VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network Interfaces**\.

1. Check the descriptions of the network interfaces in the **Description** column\. A network interface that's used by Amazon Redshift, Amazon ElastiCache, or Amazon RDS will have the name of the service in the description\. For example, a network interface that's attached to an Amazon RDS instance will have the following description: `RDSNetworkInterface`\.

1. Select the required network interface\. 

1. In the details pane, get the private IP address from the **Primary private IPv4 IP** field\. 

### Control the use of ClassicLink<a name="classiclink-iam"></a>

By default, IAM users do not have permission to work with ClassicLink\. You can create an IAM policy that grants users permissions to enable or disable a VPC for ClassicLink, link or unlink an instance to a ClassicLink\-enabled VPC, and to view ClassicLink\-enabled VPCs and linked EC2\-Classic instances\. For more information about IAM policies for Amazon EC2, see [IAM policies for Amazon EC2](iam-policies-for-amazon-ec2.md)\. 

For more information about policies for working with ClassicLink, see the following example: [Example IAM policies for ClassicLink](#iam-example-classiclink)\.

### Security groups in ClassicLink<a name="classiclink-security-groups"></a>

Linking your EC2\-Classic instance to a VPC does not affect your EC2\-Classic security groups\. They continue to control all traffic to and from the instance\. This excludes traffic to and from instances in the VPC, which is controlled by the VPC security groups that you associated with the EC2\-Classic instance\. EC2\-Classic instances that are linked to the same VPC cannot communicate with each other through the VPC; regardless of whether they are associated with the same VPC security group\. Communication between EC2\-Classic instances is controlled by the EC2\-Classic security groups associated with those instances\. For an example of a security group configuration, see [Example: ClassicLink security group configuration for a three\-tier web application](#classiclink-security-groups-example)\.

After you've linked your instance to a VPC, you cannot change which VPC security groups are associated with the instance\. To associate different security groups with your instance, you must first unlink the instance, and then link it to the VPC again, choosing the required security groups\.

### Routing for ClassicLink<a name="classiclink-routing"></a>

When you enable a VPC for ClassicLink, a static route is added to all of the VPC route tables with a destination of `10.0.0.0/8` and a target of `local`\. This allows communication between instances in the VPC and any EC2\-Classic instances that are then linked to the VPC\. If you add a custom route table to a ClassicLink\-enabled VPC, a static route is automatically added with a destination of `10.0.0.0/8` and a target of `local`\. When you disable ClassicLink for a VPC, this route is automatically deleted in all of the VPC route tables\.

VPCs that are in the `10.0.0.0/16` and `10.1.0.0/16` IP address ranges can be enabled for ClassicLink only if they do not have any existing static routes in route tables in the `10.0.0.0/8` IP address range, excluding the local routes that were automatically added when the VPC was created\. Similarly, if you've enabled a VPC for ClassicLink, you may not be able to add any more specific routes to your route tables within the `10.0.0.0/8` IP address range\. 

**Important**  
If your VPC CIDR block is a publicly routable IP address range, consider the security implications before you link an EC2\-Classic instance to your VPC\. For example, if your linked EC2\-Classic instance receives an incoming Denial of Service \(DoS\) request flood attack from a source IP address that falls within the VPC’s IP address range, the response traffic is sent into your VPC\. We strongly recommend that you create your VPC using a private IP address range as specified in [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html)\.

For more information about route tables and routing in your VPC, see [Route Tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html) in the *Amazon VPC User Guide*\.

### Enable a VPC peering connection for ClassicLink<a name="classiclink-peering"></a>

If you have a VPC peering connection between two VPCs, and there are one or more EC2\-Classic instances that are linked to one or both of the VPCs via ClassicLink, you can extend the VPC peering connection to enable communication between the EC2\-Classic instances and the instances in the VPC on the other side of the VPC peering connection\. This enables the EC2\-Classic instances and the instances in the VPC to communicate using private IP addresses\. To do this, you can enable a local VPC to communicate with a linked EC2\-Classic instance in a peer VPC, or you can enable a local linked EC2\-Classic instance to communicate with instances in a peer VPC\.

If you enable a local VPC to communicate with a linked EC2\-Classic instance in a peer VPC, a static route is automatically added to your route tables with a destination of `10.0.0.0/8` and a target of `local`\. 

For more information and examples, see [Configurations With ClassicLink](https://docs.aws.amazon.com/vpc/latest/peering/peering-configurations-classiclink.html) in the *Amazon VPC Peering Guide*\.

## ClassicLink limitations<a name="classiclink-limitations"></a>

To use the ClassicLink feature, you need to be aware of the following limitations:
+ You can link an EC2\-Classic instance to only one VPC at a time\.
+ If you stop your linked EC2\-Classic instance, it's automatically unlinked from the VPC and the VPC security groups are no longer associated with the instance\. You can link your instance to the VPC again after you've restarted it\.
+ You cannot link an EC2\-Classic instance to a VPC that's in a different Region or a different AWS account\.
+ You cannot use ClassicLink to link a VPC instance to a different VPC, or to a EC2\-Classic resource\. To establish a private connection between VPCs, you can use a VPC peering connection\. For more information, see the [Amazon VPC Peering Guide](https://docs.aws.amazon.com/vpc/latest/peering/)\.
+ You cannot associate a VPC Elastic IP address with a linked EC2\-Classic instance\.
+ You cannot enable EC2\-Classic instances for IPv6 communication\. You can associate an IPv6 CIDR block with your VPC and assign IPv6 address to resources in your VPC, however, communication between a ClassicLinked instance and resources in the VPC is over IPv4 only\.
+ VPCs with routes that conflict with the EC2\-Classic private IP address range of `10/8` cannot be enabled for ClassicLink\. This does not include VPCs with `10.0.0.0/16` and `10.1.0.0/16` IP address ranges that already have local routes in their route tables\. For more information, see [Routing for ClassicLink](#classiclink-routing)\.
+ VPCs configured for dedicated hardware tenancy cannot be enabled for ClassicLink\. Contact AWS support to request that your dedicated tenancy VPC be allowed to be enabled for ClassicLink\.
**Important**  
EC2\-Classic instances are run on shared hardware\. If you've set the tenancy of your VPC to `dedicated` because of regulatory or security requirements, then linking an EC2\-Classic instance to your VPC might not conform to those requirements, as this allows a shared tenancy resource to address your isolated resources directly using private IP addresses\. If you need to enable your dedicated VPC for ClassicLink, provide a detailed reason in your request to AWS support\.
+ If you link your EC2\-Classic instance to a VPC in the `172.16.0.0/16` range, and you have a DNS server running on the `172.16.0.23/32` IP address within the VPC, then your linked EC2\-Classic instance can't access the VPC DNS server\. To work around this issue, run your DNS server on a different IP address within the VPC\.
+ ClassicLink doesn't support transitive relationships out of the VPC\. Your linked EC2\-Classic instance doesn't have access to any VPN connection, VPC gateway endpoint, NAT gateway, or Internet gateway associated with the VPC\. Similarly, resources on the other side of a VPN connection or an Internet gateway don't have access to a linked EC2\-Classic instance\.

## Work with ClassicLink<a name="working-with-classiclink"></a>

You can use the Amazon EC2 and Amazon VPC consoles to work with the ClassicLink feature\. You can enable or disable a VPC for ClassicLink, and link and unlink EC2\-Classic instances to a VPC\.

**Note**  
The ClassicLink features are only visible in the consoles for accounts and Regions that support EC2\-Classic\. 

**Topics**
+ [Enable a VPC for ClassicLink](#classiclink-enable-vpc)
+ [Create a VPC with ClassicLink enabled](#classiclink-vpc-wizard)
+ [Link an instance to a VPC](#classiclink-link-instance)
+ [Link an instance to a VPC at launch](#classiclink-launch-wizard)
+ [View your ClassicLink\-enabled VPCs and linked instances](#classiclink-describe-vpcs-instances)
+ [Enable ClassicLink DNS support](#classiclink-enable-dns-support)
+ [Disable ClassicLink DNS support](#classiclink-disable-dns-support)
+ [Unlink an instance from a VPC](#classiclink-unlink-instance)
+ [Disable ClassicLink for a VPC](#classiclink-disable-vpc)

### Enable a VPC for ClassicLink<a name="classiclink-enable-vpc"></a>

To link an EC2\-Classic instance to a VPC, you must first enable the VPC for ClassicLink\. You cannot enable a VPC for ClassicLink if the VPC has routing that conflicts with the EC2\-Classic private IP address range\. For more information, see [Routing for ClassicLink](#classiclink-routing)\.

**To enable a VPC for ClassicLink**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC\.

1. Choose **Actions**, **Enable ClassicLink**\.

1. When prompted for confirmation, choose **Enable ClassicLink**\.

1. \(Optional\) If you want the public DNS hostname to resolve to the private IP address, enable ClassicLink DNS support for the VPC before you link any instances\. For more information, see [Enable ClassicLink DNS support](#classiclink-enable-dns-support)\.

### Create a VPC with ClassicLink enabled<a name="classiclink-vpc-wizard"></a>

You can create a new VPC and immediately enable it for ClassicLink by using the VPC wizard in the Amazon VPC console\.

**To create a VPC with ClassicLink enabled**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the Amazon VPC dashboard, choose **Launch VPC Wizard**\.

1. Select one of the VPC configuration options and choose **Select**\. 

1. On the next page of the wizard, choose **Yes** for **Enable ClassicLink**\. Complete the rest of the steps in the wizard to create your VPC\. For more information about using the VPC wizard, see [Scenarios for Amazon VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenarios.html) in the *Amazon VPC User Guide*\.

1. \(Optional\) If you want the public DNS hostname to resolve to the private IP address, enable ClassicLink DNS support for the VPC before you link any instances\. For more information, see [Enable ClassicLink DNS support](#classiclink-enable-dns-support)\.

### Link an instance to a VPC<a name="classiclink-link-instance"></a>

After you've enabled a VPC for ClassicLink, you can link an EC2\-Classic instance to it\. The instance must be in the `running` state\.

If you want the public DNS hostname to resolve to the private IP address, enable ClassicLink DNS support for the VPC before you link the instance\. For more information, see [Enable ClassicLink DNS support](#classiclink-enable-dns-support)\.

**To link an instance to a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select one or more running EC2\-Classic instances\.

1. Choose **Actions**, **ClassicLink**, **Link to VPC**\.

1. Choose the VPC\. The console displays only VPCs that are enabled for ClassicLink\.

1. Select one or more security groups to associate with your instances\. The console displays security groups only for VPCs enabled for ClassicLink\.

1. Choose **Link**\.

### Link an instance to a VPC at launch<a name="classiclink-launch-wizard"></a>

You can use the launch wizard in the Amazon EC2 console to launch an EC2\-Classic instance and immediately link it to a ClassicLink\-enabled VPC\.

**To link an instance to a VPC at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the Amazon EC2 dashboard, choose **Launch Instance**\.

1. Select an AMI, and then choose an instance type that is supported on EC2\-Classic\. For more information, see [Instance types available in EC2\-Classic](ec2-classic-platform.md#ec2-classic-instance-types)\.

1. On the **Configure Instance Details** page, do the following:

   1. For **Network**, choose **Launch into EC2\-Classic**\. If this option is disabled, then the instance type is not supported on EC2\-Classic\.

   1. Expand **Link to VPC \(ClassicLink\)** and choose a VPC from **Link to VPC**\. The console displays only VPCs with ClassicLink enabled\.

1. Complete the rest of the steps in the wizard to launch your instance\. For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\.

### View your ClassicLink\-enabled VPCs and linked instances<a name="classiclink-describe-vpcs-instances"></a>

You can view all of your ClassicLink\-enabled VPCs in the Amazon VPC console, and your linked EC2\-Classic instances in the Amazon EC2 console\.

**To view your ClassicLink\-enabled VPCs**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC\.

1. If the value of **ClassicLink** is **Enabled**, then the VPC is enabled for ClassicLink\.

### Enable ClassicLink DNS support<a name="classiclink-enable-dns-support"></a>

You can enable ClassicLink DNS support for your VPC so that DNS hostnames that are addressed between linked EC2\-Classic instances and instances in the VPC resolve to private IP addresses and not public IP addresses\. For this feature to work, your VPC must be enabled for DNS hostnames and DNS resolution\.

**Note**  
If you enable ClassicLink DNS support for your VPC, your linked EC2\-Classic instance can access any private hosted zone associated with the VPC\. For more information, see [Working with Private Hosted Zones](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/hosted-zones-private.html) in the *Amazon Route 53 Developer Guide*\. 

**To enable ClassicLink DNS support**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC\.

1. Choose **Actions**, **Edit ClassicLink DNS Support**\.

1. For **ClassicLink DNS support**, select **Enable**\.

1. Choose **Save changes**\.

### Disable ClassicLink DNS support<a name="classiclink-disable-dns-support"></a>

You can disable ClassicLink DNS support for your VPC so that DNS hostnames that are addressed between linked EC2\-Classic instances and instances in the VPC resolve to public IP addresses and not private IP addresses\.

**To disable ClassicLink DNS support**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select the VPC\.

1. Choose **Actions**, **Edit ClassicLink DNS Support**\.

1. For **ClassicLink DNS Support**, clear **Enable**\.

1. Choose **Save changes**\.

### Unlink an instance from a VPC<a name="classiclink-unlink-instance"></a>

If you no longer require a ClassicLink connection between your EC2\-Classic instance and your VPC, you can unlink the instance from the VPC\. Unlinking the instance disassociates the VPC security groups from the instance\.

A stopped instance is automatically unlinked from a VPC\.

**To unlink an instance from a VPC**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select one or more of your instances\.

1. Choose **Actions**, **ClassicLink**, **Unlink from VPC**\.

1. When prompted for confirmation, choose **Unlink**\.

### Disable ClassicLink for a VPC<a name="classiclink-disable-vpc"></a>

If you no longer require a connection between EC2\-Classic instances and your VPC, you can disable ClassicLink on the VPC\. You must first unlink all linked EC2\-Classic instances that are linked to the VPC\.

**To disable ClassicLink for a VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Select your VPC\.

1. Choose **Actions**, **Disable ClassicLink**\.

1. When prompted for confirmation, choose **Disable ClassicLink**\.

## Example IAM policies for ClassicLink<a name="iam-example-classiclink"></a>

You can enable a VPC for ClassicLink and then link an EC2\-Classic instance to the VPC\. You can also view your ClassicLink\-enabled VPCs, and all of your EC2\-Classic instances that are linked to a VPC\. You can create policies with resource\-level permission for the `ec2:EnableVpcClassicLink`, `ec2:DisableVpcClassicLink`, `ec2:AttachClassicLinkVpc`, and `ec2:DetachClassicLinkVpc` actions to control how users are able to use those actions\. Resource\-level permissions are not supported for `ec2:Describe*` actions\.

**Topics**
+ [Full permissions to work with ClassicLink](#iam-example-classiclink-full)
+ [Enable and disable a VPC for ClassicLink](#iam-example-classiclink-enable)
+ [Link instances](#iam-example-classiclink-link)
+ [Unlink instances](#iam-example-classiclink-unlink)

### Full permissions to work with ClassicLink<a name="iam-example-classiclink-full"></a>

The following policy grants users permissions to view ClassicLink\-enabled VPCs and linked EC2\-Classic instances, to enable and disable a VPC for ClassicLink, and to link and unlink instances from a ClassicLink\-enabled VPC\. 

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": [
        "ec2:DescribeClassicLinkInstances", "ec2:DescribeVpcClassicLink",
        "ec2:EnableVpcClassicLink", "ec2:DisableVpcClassicLink",
        "ec2:AttachClassicLinkVpc", "ec2:DetachClassicLinkVpc"
      ],
      "Resource": "*"
    }
   ]
}
```

### Enable and disable a VPC for ClassicLink<a name="iam-example-classiclink-enable"></a>

The following policy allows user to enable and disable VPCs for ClassicLink that have the specific tag '`purpose=classiclink`'\. Users cannot enable or disable any other VPCs for ClassicLink\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:*VpcClassicLink",
      "Resource": "arn:aws:ec2:region:account:vpc/*",
      "Condition": {
        "StringEquals": {
          "ec2:ResourceTag/purpose":"classiclink"
        }
      }
    }
  ]
}
```

### Link instances<a name="iam-example-classiclink-link"></a>

The following policy grants users permissions to link instances to a VPC only if the instance is an `m3.large` instance type\. The second statement allows users to use the VPC and security group resources, which are required to link an instance to a VPC\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:AttachClassicLinkVpc",
      "Resource": "arn:aws:ec2:region:account:instance/*",
      "Condition": {
        "StringEquals": {
          "ec2:InstanceType":"m3.large"
        }
      }
    },
    {
      "Effect": "Allow",
      "Action": "ec2:AttachClassicLinkVpc",
      "Resource": [
        "arn:aws:ec2:region:account:vpc/*",
        "arn:aws:ec2:region:account:security-group/*"
      ]
    }
  ]
}
```

The following policy grants users permissions to link instances to a specific VPC \(`vpc-1a2b3c4d`\) only, and to associate only specific security groups from the VPC to the instance \(`sg-1122aabb` and `sg-aabb2233`\)\. Users cannot link an instance to any other VPC, and they cannot specify any other of the VPC security groups to associate with the instance in the request\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:AttachClassicLinkVpc",
      "Resource": [
         "arn:aws:ec2:region:account:vpc/vpc-1a2b3c4d",
         "arn:aws:ec2:region:account:instance/*",
         "arn:aws:ec2:region:account:security-group/sg-1122aabb",
         "arn:aws:ec2:region:account:security-group/sg-aabb2233"
       ]
    }
  ]
}
```

### Unlink instances<a name="iam-example-classiclink-unlink"></a>

The following grants users permission to unlink any linked EC2\-Classic instance from a VPC, but only if the instance has the tag "`unlink=true`"\. The second statement grants users permissions to use the VPC resource, which is required to unlink an instance from a VPC\.

```
{
   "Version": "2012-10-17",
   "Statement": [{
      "Effect": "Allow",
      "Action": "ec2:DetachClassicLinkVpc",
      "Resource": [
         "arn:aws:ec2:region:account:instance/*"
      ],
      "Condition": {
         "StringEquals": {
            "ec2:ResourceTag/unlink":"true"
         }
      }
   },
   {
      "Effect": "Allow",
      "Action": "ec2:DetachClassicLinkVpc",
      "Resource": [
         "arn:aws:ec2:region:account:vpc/*"
         ]
      }
   ]
}
```

## Example: ClassicLink security group configuration for a three\-tier web application<a name="classiclink-security-groups-example"></a>

In this example, you have an application with three instances: a public\-facing web server, an application server, and a database server\. Your web server accepts HTTPS traffic from the Internet, and then communicates with your application server over TCP port 6001\. Your application server then communicates with your database server over TCP port 6004\. You're in the process of migrating your entire application to a VPC in your account\. You've already migrated your application server and your database server to your VPC\. Your web server is still in EC2\-Classic and linked to your VPC via ClassicLink\. 

You want a security group configuration that allows traffic to flow only between these instances\. You have four security groups: two for your web server \(`sg-1a1a1a1a` and `sg-2b2b2b2b`\), one for your application server \(`sg-3c3c3c3c`\), and one for your database server \(`sg-4d4d4d4d`\)\.

The following diagram displays the architecture of your instances, and their security group configuration\.

![\[Security group configuration using ClassicLink.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/classiclink-security-groups-example-diagram.png)

**Security groups for your web server \(`sg-1a1a1a1a` and `sg-2b2b2b2b`\)**  
You have one security group in EC2\-Classic, and the other in your VPC\. You associated the VPC security group with your web server instance when you linked the instance to your VPC via ClassicLink\. The VPC security group enables you to control the outbound traffic from your web server to your application server\.

The following are the security group rules for the EC2\-Classic security group \(`sg-1a1a1a1a`\)\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Type  |  Port Range  |  Comments  | 
|  0\.0\.0\.0/0  |  HTTPS  |  443  |  Allows Internet traffic to reach your web server\.  | 

The following are the security group rules for the VPC security group \(`sg-2b2b2b2b`\)\.


| 
| 
| Outbound | 
| --- |
|  Destination  |  Type  |  Port Range  |  Comments  | 
|  sg\-3c3c3c3c  |  TCP  |  6001  |  Allows outbound traffic from your web server to your application server in your VPC \(or to any other instance associated with `sg-3c3c3c3c`\)\.  | 

**Security group for your application server \(`sg-3c3c3c3c`\)**  
The following are the security group rules for the VPC security group that's associated with your application server\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Type  |  Port Range  |  Comments  | 
|  sg\-2b2b2b2b  |  TCP  |  6001  |  Allows the specified type of traffic from your web server \(or any other instance associated with `sg-2b2b2b2b`\) to reach your application server\.  | 
| Outbound | 
| --- |
|  Destination  |  Type  |  Port Range  |  Comments  | 
| sg\-4d4d4d4d | TCP | 6004 | Allows outbound traffic from the application server to the database server \(or to any other instance associated with sg\-4d4d4d4d\)\. | 

**Security group for your database server \(`sg-4d4d4d4d`\)**  
The following are the security group rules for the VPC security group that's associated with your database server\.


| 
| 
| Inbound | 
| --- |
|  Source  |  Type  |  Port Range  |  Comments  | 
|  sg\-3c3c3c3c  |  TCP  |  6004  |  Allows the specified type of traffic from your application server \(or any other instance associated with `sg-3c3c3c3c`\) to reach your database server\.  | 
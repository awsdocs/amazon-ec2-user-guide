# Amazon EC2 instance hostname types<a name="ec2-instance-naming"></a>

This section describes the Amazon EC2 instance guest OS hostname types available when you launch instances into your VPC subnets\.

The hostname distinguishes the EC2 instances on your network\. You may use the hostname of an instance if, for example, you want to run scripts to communicate with some or all of the instances on your network\.

**Topics**
+ [Types of EC2 hostnames](#instance-naming-types)
+ [Where you see Resource name and IP name](#instance-naming-presence)
+ [How to decide whether to choose Resource name or IP name](#instance-naming-choose)
+ [Modify Hostname type and DNS Hostname configurations](#instance-naming-modify)

## Types of EC2 hostnames<a name="instance-naming-types"></a>

There are two hostname types for the guest OS hostname when EC2 instances are launched in a VPC: 
+ **IP name**: The legacy naming scheme where, when you launch an instance, the *private IPv4 address* of the instance is included in the hostname of the instance\. The IP name exists for the life of the EC2 instance\. When used as the Private DNS hostname, it will only return the private IPv4 address \(A record\)\.
+ **Resource name**: When you launch an instance, the *EC2 instance ID* is included in the hostname of the instance\. The resource name exists for the life of the EC2 instance\. When used as the Private DNS hostname, it can return both the private IPv4 address \(A record\) and/or the IPv6 Global Unicast Address \(AAAA record\)\.

The EC2 instance guest OS hostname type depends on the subnet settings:
+ If the instance is launched into an IPv4\-only subnet, you can select either IP name or resource name\. 
+ If the instance is launched into a dual\-stack \(IPv4\+IPv6\) subnet, you can select either IP name or resource name\. 
+ If the instance is launched into an IPv6\-only subnet, resource name is used automatically\.

**Topics**
+ [IP name](#instance-naming-ipbn)
+ [Resource name](#instance-naming-rbn)
+ [The difference between IP name and Resource name](#instance-naming-diff)

### IP name<a name="instance-naming-ipbn"></a>

When you launch an EC2 instance with the **Hostname type** of **IP name**, the guest OS hostname is configured to use the private IPv4 address\.
+ Format for an instance in us\-east\-1: `private-ipv4-address.ec2.internal`
+ Example: `ip-10-24-34-0.ec2.internal`
+ Format for an instance in any other AWS Region: `private-ipv4-address.region.compute.internal`
+ Example: `ip-10-24-34-0.us-west-2.compute.internal`

### Resource name<a name="instance-naming-rbn"></a>

When you launch EC2 instances in IPv6\-only subnets, the **Hostname type** of **Resource name** is selected by default\. When you launch an instance in IPv4\-only or dual\-stack \(IPv4\+IPv6\) subnets, **Resource name** is an option that you can select\. After you launch an instance, you can manage the hostname configuration\. For more information, see [Modify Hostname type and DNS Hostname configurations](#instance-naming-modify)\.

When you launch an EC2 instance with a **Hostname type** of **Resource name**, the guest OS hostname is configured to use the EC2 instance ID\.
+ Format for an instance in us\-east\-1: `ec2-instance-id.ec2.internal`
+ Example: `i-0123456789abcdef.ec2.internal`
+ Format for an instance in any other AWS Region: `ec2-instance-id.region.compute.internal`
+ Example: `i-0123456789abcdef.us-west-2.compute.internal`

### The difference between IP name and Resource name<a name="instance-naming-diff"></a>

DNS queries for both IP names and resource names coexist to ensure backward compatibility and to allow you to migrate from IP based\-naming for hostnames to resource\-based naming\. For private DNS hostnames based on IP names, you cannot configure whether a DNS A record query for the instance is responded to or not\. DNS A record queries are always responded to irrespective of the guest OS hostname settings\. In contrast, for private DNS hostnames based on resource name, you can configure whether DNS A and/or DNS AAAA queries for the instance are responded to or not\. You configure the response behavior when you launch an instance or modify a subnet\. For more information, see [Modify Hostname type and DNS Hostname configurations](#instance-naming-modify)\.

## Where you see Resource name and IP name<a name="instance-naming-presence"></a>

This section describes where you see the hostname types resource name and IP name in the EC2 console\.

**Topics**
+ [When creating an EC2 instance](#instance-naming-presence-create)
+ [When viewing the details of an existing EC2 instance](#instance-naming-presence-view)

### When creating an EC2 instance<a name="instance-naming-presence-create"></a>

When you create an EC2 instance, depending on which type of subnet you select, **Hostname type** of **Resource name** might be available or it might be selected and not be modifiable\. This section explains the scenarios in which you see the hostname types resource name and IP name\.

#### Scenario 1<a name="instance-naming-presence-create-1"></a>

You create an EC2 instance in the wizard \(see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\) and, when you configure the details, you choose a subnet that you configured to be IPv6\-only\.

In this case, the **Hostname type** of **Resource name** is selected automatically and is not modifiable\. **DNS Hostname** options of **Enable IP name IPv4 \(A record\) DNS requests** and **Enable resource\-based IPv4 \(A record\) DNS requests** are deselected automatically and are not modifiable\. **Enable resource\-based IPv6 \(AAAA record\) DNS requests** is selected by default but is modifiable\. If selected, DNS requests to the resource name will resolve to the IPv6 address \(AAAA record\) of this EC2 instance\.

#### Scenario 2<a name="instance-naming-presence-create-2"></a>

You create an EC2 instance in the wizard \(see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\) and, when you configure the details, you choose a subnet configured with an IPv4 CIDR block or both an IPv4 and IPv6 CIDR block \("dual stack"\)\.

In this case, **Enable IP name IPv4 \(A record\) DNS requests** is selected automatically and can't be changed\. This means that requests to the IP name will resolve to the IPv4 address \(A record\) of this EC2 instance\.

The options default to the configurations of the subnet, but you can modify the options for this instance depending on the subnet settings:
+ **Hostname type**: Determines whether you want the guest OS hostname of the EC2 instance to be the resource name or IP name\. The default value is **IP name**\.
+ **Enable resource\-based IPv4 \(A record\) DNS requests**: Determines whether requests to your resource name resolve to the private IPv4 address \(A record\) of this EC2 instance\. This option is not selected by default\.
+ **Enable resource\-based IPv6 \(AAAA record\) DNS requests**: Determines whether requests to your resource name resolve to the IPv6 GUA address \(AAAA record\) of this EC2 instance\. This option is not selected by default\.

### When viewing the details of an existing EC2 instance<a name="instance-naming-presence-view"></a>

You can see the hostname values for an existing EC2 instance in the **Details** tab for the EC2 instance:
+ **Hostname type**: The hostname in IP name or resource name format\.
+ **Private IP DNS name \(IPv4 only\)**: The IP name that will always resolve to the private IPv4 address of the instance\.
+ **Private resource DNS name**: The resource name that resolves to the DNS records selected for this instance\.
+ **Answer private resource DNS name**: The resource name resolves to IPv4 \(A\), IPv6 \(AAAA\) or IPv4 and IPv6 \(A and AAAA\) DNS records\.

In addition, if you connect to your EC2 instance directly over SSH and enter the `hostname` command, you'll see the hostname in either the IP name or resource name format\. 

## How to decide whether to choose Resource name or IP name<a name="instance-naming-choose"></a>

When you launch an EC2 instance \(see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\), if you choose a **Hostname type** of **Resource name**, the EC2 instance launches with a hostname in the resource name format\. In such cases, the DNS record for this EC2 instance can also point to the resource name\. This gives you the flexibility to choose whether that hostname resolves to the IPv4 address, the IPv6 address, or both the IPv4 and IPv6 address of the instance\. If you plan to use IPv6 in the future or if you are using dual\-stack subnets today, it’s best to use a **Hostname type** of **Resource name** so that you change DNS resolution for the hostnames of your instances without making any changes to the DNS records themselves\. The resource name allows you to add and remove IPv4 and IPv6 DNS resolution on an EC2 instance\.

If instead you choose a **Hostname type** of **IP name**, and use it as the DNS hostname, it can only resolve to the IPv4 address of the instance\. It will not resolve to the IPv6 address of the instance even if the instance has both an IPv4 address and an IPv6 address associated with it\.

## Modify Hostname type and DNS Hostname configurations<a name="instance-naming-modify"></a>

Follow the steps in this section to modify Hostname type and DNS Hostname configurations for subnets or EC2 instances after they've been launched\.

**Topics**
+ [Subnets](#instance-naming-modify-subnets)
+ [EC2 instances](#instance-naming-modify-instances)

### Subnets<a name="instance-naming-modify-subnets"></a>

Modify the configurations for a subnet by selecting a subnet in the VPC console and choosing **Actions**, **Edit subnet settings**\.

**Note**  
Changing the subnet settings doesn't change the configuration of EC2 instances that are already launched in the subnet\.
+ **Hostname type**: Determines whether you want the default setting of the guest OS hostname of the EC2 instance launched in the subnet to be the resource name or IP name\.
+ **Enable DNS hostname IPv4 \(A record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the private IPv4 address \(A record\) of this EC2 instance\.
+ **Enable DNS hostname IPv6 \(AAAA record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the IPv6 address \(AAAA record\) of this EC2 instance\.

### EC2 instances<a name="instance-naming-modify-instances"></a>

Follow the steps in this section to modify the Hostname type and DNS Hostname configurations for an EC2 instance\.

**Important**  
To change the **Use resource based naming as guest OS hostname** setting, you must first stop the instance\. To change the **Answer DNS hostname IPv4 \(A record\) request** or **Answer DNS hostname IPv6 \(AAAA record\) requests** settings, you don't have to stop the instance\.
To modify any of the settings for non\-EBS backed EC2 instance types, you cannot stop the instance\. You must terminate the instance and launch a new instance with the desired Hostname type and DNS Hostname configurations\.

**To modify the Hostname type and DNS Hostname configurations for an EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. If you're going to change the **Use resource based naming as guest OS hostname** setting, first stop the EC2 instance\. Otherwise, skip this step\.

   To stop the instance, select the instance and choose **Instance state**, **Stop instance**\.

1. Select the instance and choose **Actions**, **Instance settings**, **Change resource based naming options**\.
   + **Use resource based naming as guest OS hostname**: Determines whether you want the guest OS hostname of the EC2 instance to be the resource name or IP name\.
   + **Answer DNS hostname IPv4 \(A record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the private IPv4 address of this EC2 instance\.
   + **Answer DNS hostname IPv6 \(AAAA record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the IPv6 address \(AAAA record\) of this EC2 instance\.

1. Choose **Save**\.

1. If you stopped the instance, start it again\.
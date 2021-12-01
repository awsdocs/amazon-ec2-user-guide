# Amazon EC2 instance hostname types<a name="ec2-instance-naming"></a>

This section describes the Amazon EC2 instance guest OS hostname types available when you launch instances into your VPC subnets\.

**Topics**
+ [Types of EC2 hostnames](#instance-naming-types)
+ [Where you see RBN and IPBN](#instance-naming-presence)
+ [Modify RBN configurations](#instance-naming-modify)

## Types of EC2 hostnames<a name="instance-naming-types"></a>

There are two hostname types for the guest OS hostname when EC2 instances are launched in a VPC: 
+ **IP address\-based naming \(IPBN\)**: The legacy naming scheme where, when you launch an instance, the *private IPv4 address* of the instance is included in the hostname of the instance\. The IP address\-based name exists for the life of the EC2 instance\. When used as the Private DNS hostname, it will only return the private IPv4 address \(A record\)\.
+ **Resource\-based naming \(RBN\)**: When you launch an instance, the *EC2 instance ID* is included in the hostname of the instance\. The resource\-based name exists for the life of the EC2 instance\. When used as the Private DNS hostname, it can return both the Private IPv4 address \(A record\) and/or the IPv6 Global Unicast Address \(AAAA record\)\.

The EC2 instance guest OS hostname \(of type IPBN or RBN\) depends on the subnet settings:
+ If the instance is launched into an IPv4\-only subnet, you can select either IPBN or RBN\. 
+ If the instance is launched into a dual\-stack \(IPv4\+IPv6\) subnet, you can select either IPBN or RBN\. 
+ If the instance is launched into an IPv6\-only subnet, RBN is used automatically\.

**Topics**
+ [IP address\-based naming](#instance-naming-ipbn)
+ [Resource\-based naming](#instance-naming-rbn)
+ [The difference between IPBN and RBN](#instance-naming-diff)

### IP address\-based naming<a name="instance-naming-ipbn"></a>

When you launch an EC2 instance with IP address\-based naming \(IPBN\), the guest OS hostname is configured to use the private IPv4 address
+ Format for an instance in us\-east\-1: `private-ipv4-address.ec2.internal`
+ Example: `ip-10-24-34-0.ec2.internal`
+ Format for an instance in any other AWS Region: `private-ipv4-address.region.compute.internal`
+ Example: `ip-10-24-34-0.us-west-2.compute.internal`

### Resource\-based naming<a name="instance-naming-rbn"></a>

Resource\-based naming \(RBN\) is used automatically when you launch EC2 instances in IPv6\-only subnets\. RBN is not selected by default when you launch an instance in dual\-stack \(IPv4\+IPv6\) subnets, but it is an option that you can select depending on the subnet settings\. After you launch an instance, you can manage the guest OS hostname configuration\. For more information, see [Modify RBN configurations](#instance-naming-modify)\.

When you launch an EC2 instance with a resource\-based hostname type, the guest OS hostname is configured to use the EC2 instance ID\.
+ Format for an instance in us\-east\-1: `ec2-instance-id.ec2.internal`
+ Example: `i-0123456789abcdef.ec2.internal`
+ Format for an instance in any other AWS Region: `ec2-instance-id.region.compute.internal`
+ Example: `i-0123456789abcdef.us-west-2.compute.internal`

### The difference between IPBN and RBN<a name="instance-naming-diff"></a>

DNS queries for both IP address\-based naming \(IPBN\) and resource\-based naming \(RBN\) DNS hostnames coexist to ensure backward compatibility and to allow you to migrate from IPBN to RBN\. For private DNS hostnames based on IPBN, you cannot configure whether a DNS A record query for the instance is responded to or not\. DNS A record queries are always responded to\. In contrast, for private DNS hostnames based on RBN, you can configure whether DNS A and/or DNS AAAA queries for the instance are responded to or not\. You configure the response behavior when you launch an instance or modify a subnet\. You make the RBN DNS query configuration change when you launch an instance, create a subnet, or modify a subnet\. For more information, see [Modify RBN configurations](#instance-naming-modify)\.

## Where you see RBN and IPBN<a name="instance-naming-presence"></a>

This section describes where you see resource\-based naming \(RBN\) and IP address\-based naming \(IPBN\) in the EC2 console\.

**Topics**
+ [When creating an EC2 instance](#instance-naming-presence-create)
+ [When viewing the details of an existing EC2 instance](#instance-naming-presence-view)

### When creating an EC2 instance<a name="instance-naming-presence-create"></a>

When you create an EC2 instance, depending on which type of subnet you select, an RBN option might be available, or it might be selected and not modifiable\. This section explains the scenarios in which you see RBN and IPBN options\.

#### Scenario 1<a name="instance-naming-presence-create-1"></a>

You create an EC2 instance in the wizard and, when you configure the details, you choose a subnet that you configured to be IPv6\-only\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ipv6nativeV2.png)

In this case, the **Hostname type** **Resource name** is selected automatically and is not modifiable\. **DNS Hostname** options are displayed, but **Enable resource\-based IPv6 \(AAAA record\) DNS requests** is selected automatically and is not modifiable here\. This means that the hostname for your EC2 instance is an RBN, and DNS requests to the RBN will resolve to the IPv6 address \(AAAA record\) of this EC2 instance\.

#### Scenario 2<a name="instance-naming-presence-create-2"></a>

You create an EC2 instance in the wizard and, when you configure the details, you choose a subnet configured with an IPv4 CIDR block or both an IPv4 and IPv6 CIDR block \("dual stack"\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/dualstackV2.png)

In this case, the RBN options **Hostname type** and **Resource\-based DNS** are visible\. **Enable IP name IPV4 \(A record\) DNS requests** is selected automatically and can't be changed\. This means that requests to the IP name will resolve to the IPv4 address \(A record\) of this EC2 instance\.

The RBN options default to the configurations of the subnet, but you can modify the options for this instance depending on the subnet settings:
+ **Hostname type**: Determines whether you want the guest OS hostname of the EC2 instance to be the resource name \(RBN\) or IP name \(IPBN\)\.
+ **Enable resource\-based IPV4 \(A record\) DNS requests**: Determines whether requests to your resource name resolve to the private IPv4 address \(A record\) of this EC2 instance\.
+ **Enable resource\-based IPV6 \(AAAA record\) DNS requests**: Determines whether requests to your resource name resolve to the IPv6 GUA address \(AAAA record\) of this EC2 instance\.

### When viewing the details of an existing EC2 instance<a name="instance-naming-presence-view"></a>

You can see the hostname values for an existing EC2 instance in the **Details** tab for the EC2 instance\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instancedetailsV2.png)

You can see the following details related to IPBN and RBN:
+ **Hostname type**: The hostname in IPBN or RBN format\.
+ **Private IP DNS name \(IPv4 only\)**: The IPBN\-based DNS name that will always resolve to the private IPv4 address of the instance\.
+ **Private resource DNS name**: The RBN\-based DNS name that resolves to the DNS records selected for this instance\.
+ **Answer private resource DNS name**: The RBN resolves to IPv4 \(A\), IPv6 \(AAAA\) or IPv4 and IPv6 \(A and AAAA\) DNS records\.

In addition, if you connect to your EC2 instance directly over SSH and enter the `hostname` command, you'll see the hostname in either the IPBN or RBN format\. 

## Modify RBN configurations<a name="instance-naming-modify"></a>

Follow the steps in this section to modify resource\-based naming \(RBN\) configurations for subnets or EC2 instances after they've been launched\.

**Topics**
+ [Subnets](#instance-naming-modify-subnets)
+ [EC2 instances](#instance-naming-modify-instances)

### Subnets<a name="instance-naming-modify-subnets"></a>

Modify the RBN configurations for a subnet by selecting a subnet in the VPC console and choosing **Actions**, **Edit subnet settings**\.
+ **Hostname type**: Determines whether you want the default setting of the guest OS hostname of the EC2 instance launched in the subnet to be the resource name \(RBN\) or IP name \(IPBN\)\.
+ **Enable DNS hostname IPv4 \(A record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the IPv4 address \(A record\) of this EC2 instance\.
+ **Enable DNS hostname IPv6 \(AAAA record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the IPv6 address \(AAAA record\) of this EC2 instance\.

### EC2 instances<a name="instance-naming-modify-instances"></a>

Follow the steps in this section to modify the RBN configurations for an EC2 instance\.

**Important**  
To change the **Use RBN as guest OS hostname** setting, you must first stop the instance\. To change the **Answer DNS hostname IPv4 \(A record\) request** or **Answer DNS hostname IPv6 \(AAAA record\) requests** settings, you don't have to stop the instance\.
To modify any of the RBN settings for non\-EBS backed EC2 instance types, you cannot stop the instance\. You must terminate the instance and launch a new instance with the desired RBN configurations\.

**To modify the RBN configurations for an EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. If you're going to change the **Use RBN as guest OS hostname** setting, first stop the EC2 instance\. Otherwise, skip this step\.

   To stop the instance, select the instance and choose **Instance state**, **Stop instance**\.

1. Select the instance and choose **Actions**, **Instance settings**, **Change resource based naming options**\.
   + **Use RBN as guest OS hostname**: Determines whether you want the guest OS hostname of the EC2 instance to be the resource name \(RBN\) or IP name \(IPBN\)\.
   + **Answer DNS hostname IPv4 \(A record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the IPv4 address of this EC2 instance\.
   + **Answer DNS hostname IPv6 \(AAAA record\) requests**: Determines whether DNS requests/queries to your resource name resolve to the IPv6 address \(AAAA record\) of this EC2 instance\.

1. Choose **Save**\.

1. If you stopped the instance, start it again\.
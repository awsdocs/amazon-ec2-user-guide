# Regions and Zones<a name="using-regions-availability-zones"></a>

Amazon EC2 is hosted in multiple locations world\-wide\. These locations are composed of Regions, Availability Zones, Local Zones, AWS Outposts, and Wavelength Zones\. Each *Region* is a separate geographic area\. 
+ Availability Zones are multiple, isolated locations within each Region\.
+ Local Zones provide you the ability to place resources, such as compute and storage, in multiple locations closer to your end users\.
+ AWS Outposts brings native AWS services, infrastructure, and operating models to virtually any data center, co\-location space, or on\-premises facility\.
+ Wavelength Zones allow developers to build applications that deliver ultra\-low latencies to 5G devices and end users\. Wavelength deploys standard AWS compute and storage services to the edge of telecommunication carriers' 5G networks\.

AWS operates state\-of\-the\-art, highly available data centers\. Although rare, failures can occur that affect the availability of instances that are in the same location\. If you host all of your instances in a single location that is affected by a failure, none of your instances would be available\.

To help you determine which deployment is best for you, see [AWS Wavelength FAQs](http://aws.amazon.com/wavelength/faqs/)\.

**Topics**
+ [Regions](#concepts-regions)
+ [Availability Zones](#concepts-availability-zones)
+ [Local Zones](#concepts-local-zones)
+ [Wavelength Zones](#concepts-wavelength-zones)
+ [AWS Outposts](#concepts-outposts)

## Regions<a name="concepts-regions"></a>

Each Amazon EC2 Region is designed to be isolated from the other Amazon EC2 Regions\. This achieves the greatest possible fault tolerance and stability\.

The following diagram illustrates multiple AWS Regions\.

![\[Regions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/aws-region.png)

When you view your resources, you see only the resources that are tied to the Region that you specified\. This is because Regions are isolated from each other, and we don't automatically replicate resources across Regions\.

When you launch an instance, you must select an AMI that's in the same Region\. If the AMI is in another Region, you can copy the AMI to the Region you're using\. For more information, see [Copy an AMI](CopyingAMIs.md)\.

Note that there is a charge for data transfer between Regions\. For more information, see [Amazon EC2 Pricing \- Data Transfer](https://aws.amazon.com/ec2/pricing/on-demand/#Data_Transfer)\.

**Topics**
+ [Available Regions](#concepts-available-regions)
+ [Regions and endpoints](#using-regions-endpoints)
+ [Describe your Regions](#using-regions-availability-zones-describe)
+ [Get the Region name](#region-name)
+ [Specify the Region for a resource](#using-regions-availability-zones-setup)

### Available Regions<a name="concepts-available-regions"></a>

Your account determines the Regions that are available to you\.
+ An AWS account provides multiple Regions so that you can launch Amazon EC2 instances in locations that meet your requirements\. For example, you might want to launch instances in Europe to be closer to your European customers or to meet legal requirements\.
+ An AWS GovCloud \(US\-West\) account provides access to the AWS GovCloud \(US\-West\) Region and the AWS GovCloud \(US\-East\) Region\. For more information, see [AWS GovCloud \(US\)](https://aws.amazon.com/govcloud-us/)\.
+ An Amazon AWS \(China\) account provides access to the Beijing and Ningxia Regions only\. For more information, see [AWS in China](https://www.amazonaws.cn/about-aws/china/)\.

The following table lists the Regions provided by an AWS account\. You can't describe or access additional Regions from an AWS account, such as AWS GovCloud \(US\) Region or the China Regions\. To use a Region introduced after March 20, 2019, you must enable the Region\. For more information, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *AWS General Reference*\.

For information about available Wavelength Zones, see [Available Wavelength Zones](https://docs.aws.amazon.com/wavelength/latest/developerguide/wavelength-quotas.html#concepts-available-zones) in the *AWS Wavelength Developer Guide*\.


| Code | Name | Opt\-in Status | Local Zone | 
| --- | --- | --- | --- | 
| us\-east\-2 | US East \(Ohio\) | Not required | Not available | 
| us\-east\-1 | US East \(N\. Virginia\) | Not required |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)  | 
| us\-west\-1 | US West \(N\. California\) | Not required | Not available | 
| us\-west\-2 | US West \(Oregon\) | Not required | The following Local Zones are in Los Angeles:[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) | 
| af\-south\-1 | Africa \(Cape Town\) | Required | Not available | 
| ap\-east\-1 | Asia Pacific \(Hong Kong\) | Required | Not available | 
| ap\-south\-1 | Asia Pacific \(Mumbai\) | Not required | Not available | 
| ap\-northeast\-3 | Asia Pacific \(Osaka\) | Not required | Not available | 
| ap\-northeast\-2 | Asia Pacific \(Seoul\) | Not required | Not available | 
| ap\-southeast\-1 | Asia Pacific \(Singapore\) | Not required | Not available | 
| ap\-southeast\-2 | Asia Pacific \(Sydney\) | Not required | Not available | 
| ap\-northeast\-1 | Asia Pacific \(Tokyo\) | Not required | Not available | 
| ca\-central\-1 | Canada \(Central\) | Not required | Not available | 
| eu\-central\-1 | Europe \(Frankfurt\) | Not required | Not available | 
| eu\-west\-1 | Europe \(Ireland\) | Not required | Not available | 
| eu\-west\-2 | Europe \(London\) | Not required | Not available | 
| eu\-south\-1 | Europe \(Milan\) | Required | Not available | 
| eu\-west\-3 | Europe \(Paris\) | Not required | Not available | 
| eu\-north\-1 | Europe \(Stockholm\) | Not required | Not available | 
| me\-south\-1 | Middle East \(Bahrain\) | Required | Not available | 
| sa\-east\-1 | South America \(São Paulo\) | Not required | Not available | 

For more information, see [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/)\.

The number and mapping of Availability Zones per Region may vary between AWS accounts\. To get a list of the Availability Zones that are available to your account, you can use the Amazon EC2 console or the command line interface\. For more information, see [Describe your Regions](#using-regions-availability-zones-describe)\.

### Regions and endpoints<a name="using-regions-endpoints"></a>

When you work with an instance using the command line interface or API actions, you must specify its Regional endpoint\. For more information about the Regions and endpoints for Amazon EC2, see [Amazon EC2 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/ec2-service.html) in the *Amazon Web Services General Reference*\.

For more information about endpoints and protocols in AWS GovCloud \(US\-West\), see [AWS GovCloud \(US\-West\) Endpoints](https://docs.aws.amazon.com/govcloud-us/latest/UserGuide/using-govcloud-endpoints.html) in the *AWS GovCloud \(US\) User Guide*\.

### Describe your Regions<a name="using-regions-availability-zones-describe"></a>

You can use the Amazon EC2 console or the command line interface to determine which Regions are available for your account\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

**To find your Regions using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, view the options in the Region selector\.  
![\[View your Regions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EC2_select_region.png)

1. Your EC2 resources for this Region are displayed on the **EC2 Dashboard** in the **Resources** section\.

**To find your Regions using the AWS CLI**
+ Use the [describe\-regions](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-regions.html) command as follows to describe the Regions that are enabled for your account\.

  ```
  aws ec2 describe-regions
  ```

  To describe all Regions, including any Regions that are disabled for your account, add the `--all-regions` option as follows\.

  ```
  aws ec2 describe-regions --all-regions
  ```

**To find your Regions using the AWS Tools for Windows PowerShell**
+ Use the [Get\-EC2Region](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Region.html) command as follows to describe the Regions for your account\.

  ```
  PS C:\> Get-EC2Region
  ```

### Get the Region name<a name="region-name"></a>

You can use the Amazon Lightsail API to view the name of a Region\.

**To view the Region name using the AWS CLI**
+ Use the [get\-regions](https://docs.aws.amazon.com/cli/latest/reference/lightsail/get-regions.html) command as follows to describe the name of the specified Region\.

  ```
  aws lightsail get-regions --query "regions[?name=='region-name'].displayName" --output text
  ```

  The following example returns the name of the `us-east-2` Region\.

  ```
  aws lightsail get-regions --query "regions[?name=='us-east-2'].displayName" --output text
  ```

  The following is the output:

  ```
  Ohio
  ```

### Specify the Region for a resource<a name="using-regions-availability-zones-setup"></a>

Every time you create an Amazon EC2 resource, you can specify the Region for the resource\. You can specify the Region for a resource using the AWS Management Console or the command line\.

**Considerations**  
Some AWS resources might not be available in all Regions\. Ensure that you can create the resources that you need in the desired Regions before you launch an instance\.

**To specify the Region for a resource using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Use the Region selector in the navigation bar\.

**To specify the default Region using the command line**

You can set the value of an environment variable to the desired Regional endpoint \(for example, `https://ec2.us-east-2.amazonaws.com`\):
+ `AWS_DEFAULT_REGION` \(AWS CLI\)
+ `Set-AWSDefaultRegion` \(AWS Tools for Windows PowerShell\)

Alternatively, you can use the `--region` \(AWS CLI\) or `-Region` \(AWS Tools for Windows PowerShell\) command line option with each individual command\. For example, `--region us-east-2`\.

For more information about the endpoints for Amazon EC2, see [Amazon Elastic Compute Cloud Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region)\.

## Availability Zones<a name="concepts-availability-zones"></a>

Each Region has multiple, isolated locations known as *Availability Zones*\. When you launch an instance, you can select an Availability Zone or let us choose one for you\. If you distribute your instances across multiple Availability Zones and one instance fails, you can design your application so that an instance in another Availability Zone can handle requests\.

The following diagram illustrates multiple Availability Zones in an AWS Region\.

![\[Regions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/aws-az.png)

You can also use Elastic IP addresses to mask the failure of an instance in one Availability Zone by rapidly remapping the address to an instance in another Availability Zone\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. 

An Availability Zone is represented by a Region code followed by a letter identifier; for example, `us-east-1a`\. To ensure that resources are distributed across the Availability Zones for a Region, we independently map Availability Zones to names for each AWS account\. For example, the Availability Zone `us-east-1a` for your AWS account might not be the same location as `us-east-1a` for another AWS account\.

To coordinate Availability Zones across accounts, you must use the *AZ ID*, which is a unique and consistent identifier for an Availability Zone\. For example, `use1-az1` is an AZ ID for the `us-east-1` Region and it has the same location in every AWS account\.

You can view AZ IDs to determine the location of resources in one account relative to the resources in another account\. For example, if you share a subnet in the Availability Zone with the AZ ID `use-az2` with another account, this subnet is available to that account in the Availability Zone whose AZ ID is also `use-az2`\. The AZ ID for each VPC and subnet is displayed in the Amazon VPC console\. For more information, see [Working with Shared VPCs](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-sharing.html) in the *Amazon VPC User Guide*\.

As Availability Zones grow over time, our ability to expand them can become constrained\. If this happens, we might restrict you from launching an instance in a constrained Availability Zone unless you already have an instance in that Availability Zone\. Eventually, we might also remove the constrained Availability Zone from the list of Availability Zones for new accounts\. Therefore, your account might have a different number of available Availability Zones in a Region than another account\. 

**Topics**
+ [Describe your Availability Zones](#availability-zones-describe)
+ [Launch instances in an Availability Zone](#using-regions-availability-zones-launching)
+ [Migrate an instance to another Availability Zone](#migrating-instance-availability-zone)

### Describe your Availability Zones<a name="availability-zones-describe"></a>

You can use the Amazon EC2 console or the command line interface to determine which Availability Zones are available for your account\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

**To find your Availability Zones using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, view the options in the Region selector\.

1. On the navigation pane, choose **EC2 Dashboard**\.

1. The Availability Zones are listed under **Service health**, **Zone status**\.

**To find your Availability Zones using the AWS CLI**

1. Use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command as follows to describe the Availability Zones within the specified Region\.

   ```
   aws ec2 describe-availability-zones --region region-name
   ```

1. Use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command as follows to describe the Availability Zones regardless of the opt\-in status\.

   ```
   aws ec2 describe-availability-zones --all-availability-zones
   ```

**To find your Availability Zones using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2AvailabilityZone](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2AvailabilityZone.html) command as follows to describe the Availability Zones within the specified Region\.

```
PS C:\> Get-EC2AvailabilityZone -Region region-name
```

### Launch instances in an Availability Zone<a name="using-regions-availability-zones-launching"></a>

When you launch an instance, select a Region that puts your instances closer to specific customers, or meets the legal or other requirements that you have\. By launching your instances in separate Availability Zones, you can protect your applications from the failure of a single location\. 

When you launch an instance, you can optionally specify an Availability Zone in the Region that you are using\. If you do not specify an Availability Zone, we select an Availability Zone for you\. When you launch your initial instances, we recommend that you accept the default Availability Zone, because this allows us to select the best Availability Zone for you based on system health and available capacity\. If you launch additional instances, specify a Zone only if your new instances must be close to, or separated from, your running instances\.

### Migrate an instance to another Availability Zone<a name="migrating-instance-availability-zone"></a>

If necessary, you can migrate an instance from one Availability Zone to another\. For example, let's say you are trying to modify the instance type of your instance and we can't launch an instance of the new instance type in the current Availability Zone\. In this case, you can migrate the instance to an Availability Zone where we are able to launch an instance of that instance type\.

The migration process involves:
+ Creating an AMI from the original instance
+ Launching an instance in the new Availability Zone
+ Updating the configuration of the new instance, as shown in the following procedure

**To migrate an instance to another Availability Zone**

1. Create an AMI from the instance\. The procedure depends on your operating system and the type of root device volume for the instance\. For more information, see the documentation that corresponds to your operating system and root device volume:
   + [Create an Amazon EBS\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html)
   + [Create an instance store\-backed Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html)
   + [Create a custom Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html)

1. If you need to preserve the private IPv4 address of the instance, you must delete the subnet in the current Availability Zone and then create a subnet in the new Availability Zone with the same IPv4 address range as the original subnet\. Note that you must terminate all instances in a subnet before you can delete it\. Therefore, you should create AMIs from all of the instances in your subnet so that you can move all instances from the current subnet to the new subnet\.

1. Launch an instance from the AMI that you just created, specifying the new Availability Zone or subnet\. You can use the same instance type as the original instance, or select a new instance type\. For more information, see [Launch instances in an Availability Zone](#using-regions-availability-zones-launching)\.

1. If the original instance has an associated Elastic IP address, associate it with the new instance\. For more information, see [Disassociate an Elastic IP address](elastic-ip-addresses-eip.md#using-instance-addressing-eips-associating-different)\.

1. If the original instance is a Reserved Instance, change the Availability Zone for your reservation\. \(If you also changed the instance type, you can also change the instance type for your reservation\.\) For more information, see [Submit modification requests](ri-modifying.md#ri-modification-process)\.

1. \(Optional\) Terminate the original instance\. For more information, see [Terminate an instance](terminating-instances.md#terminating-instances-console)\.

## Local Zones<a name="concepts-local-zones"></a>

A Local Zone is an extension of an AWS Region in geographic proximity to your users\. Local Zones have their own connections to the internet and support AWS Direct Connect, so that resources created in a Local Zone can serve local users with low\-latency communications\. For more information, see [AWS Local Zones](http://aws.amazon.com/about-aws/global-infrastructure/localzones/)\. 

![\[Local Zones\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/aws-lz.png)

A Local Zone is represented by a Region code followed by an identifier that indicates the location, for example, `us-west-2-lax-1a`\.

To use a Local Zone, you must first enable it\. For more information, see [Opt in to Local Zones](#opt-in-local-zone)\. Next, create a subnet in the Local Zone\. Finally, launch any of the following resources in the Local Zone subnet, so that your applications are close to your end users:
+ Amazon EC2 instances
+ Amazon EBS volumes
+ Amazon ECS
+ Amazon EKS

In addition to the above list, the following resources are available in the Los Angeles Local Zones\. 
+ Amazon FSx file servers
+ Elastic Load Balancing
+ Amazon EMR
+ Amazon ElastiCache
+ Amazon Relational Database Service
+ Dedicated Hosts
+ Internet gateways

For information about the available Local Zones, see [Available Regions](#concepts-available-regions)\.

**Topics**
+ [Describe your Local Zones](#local-zones-describe)
+ [Opt in to Local Zones](#opt-in-local-zone)
+ [Launch instances in a Local Zone](#local-zones-launching)

### Describe your Local Zones<a name="local-zones-describe"></a>

You can use the Amazon EC2 console or the command line interface to determine which Local Zones are available for your account\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

**To find your Local Zones using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, view the options in the Region selector\.

1. On the navigation pane, choose **EC2 Dashboard**\.

1. The Local Zones are listed under **Service health**, **Zone status**\.

**To find your Local Zones using the AWS CLI**

1. Use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command as follows to describe the Local Zones in the specified Region\.

   ```
   aws ec2 describe-availability-zones --region region-name
   ```

1. Use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command as follows to describe the Local Zones regardless of whether they are enabled\.

   ```
   aws ec2 describe-availability-zones --all-availability-zones
   ```

**To find your Local Zones using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2AvailabilityZone](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2AvailabilityZone.html) command as follows to describe the Local Zones in the specified Region\.

```
PS C:\> Get-EC2AvailabilityZone -Region region-name
```

### Opt in to Local Zones<a name="opt-in-local-zone"></a>

Before you can specify a Local Zone for a resource or service, you must opt in to Local Zones\.

**Consideration**  
Some AWS resources might not be available in all Regions\. Make sure that you can create the resources that you need in the desired Regions or Local Zones before launching an instance in a specific Local Zone\.

**To opt in to Local Zones using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the upper\-left corner of the page, select **New EC2 Experience**\. You cannot complete this task using the old console experience\.

1. From the Region selector in the navigation bar, select the Region for the Local Zone\.

1. On the navigation pane, choose **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account attributes**, **Zones**\.

1. Choose **Manage**\.

1. For **Zone group**, choose **Enabled**\.

1. Choose **Update zone group**\.

**To opt in to Local Zones using the AWS CLI**
+ Use the [modify\-availability\-zone\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-availability-zone-group.html) command\.

### Launch instances in a Local Zone<a name="local-zones-launching"></a>

When you launch an instance, you can specify a subnet which is in a Local Zone\. You also allocate an IP address from a network border group, which is a unique set of Availability Zones, Local Zones, or Wavelength Zones from which AWS advertises IP addresses, for example, `us-west-2-lax-1a`\.

You can allocate the following IP addresses from a network border group:
+ Elastic IPv4 addresses that Amazon provides
+ IPv6 Amazon\-provided VPC addresses

**To launch an instance in a Local Zone:**

1. Enable Local Zones\. For more information, see [Opt in to Local Zones](#opt-in-local-zone)\.

1. Create a VPC in a Region that supports the Local Zone\. For more information, see [Creating a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#Create-VPC) in the *Amazon VPC User Guide*\.

1. Create a subnet\. Select the Local Zone when you create the subnet\. For more information, see [Creating a subnet in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#AddaSubnet) in the *Amazon VPC User Guide*\.

1. Launch an instance, and select the subnet you created in the Local Zone\. For more information, see [Launch your instance](LaunchingAndUsingInstances.md)\.

## Wavelength Zones<a name="concepts-wavelength-zones"></a>

AWS Wavelength enables developers to build applications that deliver ultra\-low latencies to mobile devices and end users\. Wavelength deploys standard AWS compute and storage services to the edge of telecommunication carriers' 5G networks\. Developers can extend a virtual private cloud \(VPC\) to one or more Wavelength Zones, and then use AWS resources like Amazon EC2 instances to run applications that require ultra\-low latency and a connection to AWS services in the Region\.

A Wavelength Zone is an isolated zone in the carrier location where the Wavelength infrastructure is deployed\. Wavelength Zones are tied to a Region\. A Wavelength Zone is a logical extension of a Region, and is managed by the control plane in the Region\.

![\[Wavelength Zones\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/aws-wz.png)

A Wavelength Zone is represented by a Region code followed by an identifier that indicates the Wavelength Zone, for example, `us-east-1-wl1-bos-wlz-1`\.

To use a Wavelength Zone, you must first opt in to the Zone\. For more information, see [Enable Wavelength Zones](#opt-in-wavelength-zone)\. Next, create a subnet in the Wavelength Zone\. Finally, launch your resources in the Wavelength Zones subnet, so that your applications are closer to your end users\.

Wavelength Zones are not available in every Region\. For information about the Regions that support Wavelength Zones, see [Available Wavelength Zones](https://docs.aws.amazon.com/wavelength/latest/developerguide/wavelength-quotas.html#concepts-available-zones) in the *AWS Wavelength Developer Guide*\.

**Topics**
+ [Describe your Wavelength Zones](#wavelength-zones-describe)
+ [Enable Wavelength Zones](#opt-in-wavelength-zone)
+ [Launch instances in a Wavelength Zone](#wavelength-zones-launching)

### Describe your Wavelength Zones<a name="wavelength-zones-describe"></a>

You can use the Amazon EC2 console or the command line interface to determine which Wavelength Zones are available for your account\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

**To find your Wavelength Zones using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, view the options in the Region selector\.

1. On the navigation pane, choose **EC2 Dashboard**\.

1. The Wavelength Zones are listed under **Service health**, **Zone status**\.

**To find your Wavelength Zones using the AWS CLI**

1. Use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command as follows to describe the Wavelength Zones within the specified Region\.

   ```
   aws ec2 describe-availability-zones --region region-name
   ```

1. Use the [describe\-availability\-zones](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-availability-zones.html) command as follows to describe the Wavelength Zones regardless of the opt\-in status\.

   ```
   aws ec2 describe-availability-zones --all-availability-zones
   ```

**To find your Wavelength Zone using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2AvailabilityZone](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2AvailabilityZone.html) command as follows to describe the Wavelength Zone within the specified Region\.

```
PS C:\> Get-EC2AvailabilityZone -Region region-name
```

### Enable Wavelength Zones<a name="opt-in-wavelength-zone"></a>

Before you specify a Wavelength Zone for a resource or service, you must opt in to Wavelength Zones\.

**Considerations**
+ Some AWS resources are not available in all Regions\. Make sure that you can create the resources that you need in the desired Region or Wavelength Zone before launching an instance in a specific Wavelength Zone\.

**To opt in to Wavelength Zone using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the upper\-left corner of the page, select **New EC2 Experience**\. You cannot complete this task using the old console experience\.

1. From the Region selector in the navigation bar, select the Region for the Wavelength Zone\.

1. On the navigation pane, choose **EC2 Dashboard**\.

1. In the upper\-right corner of the page, choose **Account attributes**, **Zones**\.

1. Under **Wavelength Zones**, choose **Manage** for the Wavelength Zone\.

1. Choose **Enable**\.

1. Choose **Update zone group**\.

**To enable Wavelength Zones using the AWS CLI**  
Use the [modify\-availability\-zone\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-availability-zone-group.html) command\.

### Launch instances in a Wavelength Zone<a name="wavelength-zones-launching"></a>

When you launch an instance, you can specify a subnet which is in a Wavelength Zone\. You also allocate a carrier IP address from a network border group, which is a unique set of Availability Zones, Local Zones, or Wavelength Zones from which AWS advertises IP addresses, for example, `us-east-1-wl1-bos-wlz-1`\. 

For information about how to launch an instance in a Wavelength Zone, see [Get started with AWS Wavelength](https://docs.aws.amazon.com/wavelength/latest/developerguide/get-started-wavelength.html) in the *AWS Wavelength Developer Guide*\.

## AWS Outposts<a name="concepts-outposts"></a>

AWS Outposts is a fully managed service that extends AWS infrastructure, services, APIs, and tools to customer premises\. By providing local access to AWS managed infrastructure, AWS Outposts enables customers to build and run applications on premises using the same programming interfaces as in AWS Regions, while using local compute and storage resources for lower latency and local data processing needs\.

An Outpost is a pool of AWS compute and storage capacity deployed at a customer site\. AWS operates, monitors, and manages this capacity as part of an AWS Region\. You can create subnets on your Outpost and specify them when you create AWS resources such as EC2 instances, EBS volumes, ECS clusters, and RDS instances\. Instances in Outpost subnets communicate with other instances in the AWS Region using private IP addresses, all within the same VPC\.

To begin using AWS Outposts, you must create an Outpost and order Outpost capacity\. For more information about Outposts configurations, see [our catalog](http://aws.amazon.com/outposts/pricing/)\. After your Outpost equipment is installed, the compute and storage capacity is available for you when you launch Amazon EC2 instances and create Amazon EBS volumes on your Outpost\.

### Launch instances on an Outpost<a name="outposts-instances"></a>

You can launch EC2 instances in the Outpost subnet that you created\. Security groups control inbound and outbound traffic for instances in an Outpost subnet, as they do for instances in an Availability Zone subnet\. To connect to an EC2 instance in an Outpost subnet, you can specify a key pair when you launch the instance, as you do for instances in an Availability Zone subnet\.

The root volume must be 30 GB or smaller\. You can specify data volumes in the block device mapping of the AMI or the instance to provide additional storage\. To trim unused blocks from the boot volume, see [How to Build Sparse EBS Volumes](http://aws.amazon.com/blogs/apn/how-to-build-sparse-ebs-volumes-for-fun-and-easy-snapshotting/) in the AWS Partner Network Blog\.

We recommend that you increase the NVMe timeout for the root volume\. For more information, see [I/O operation timeout](nvme-ebs-volumes.md#timeout-nvme-ebs-volumes)\.

For information about how to create an Outpost, see [Get started with AWS Outposts](https://docs.aws.amazon.com/outposts/latest/userguide/get-started-outposts.html) in the *AWS Outposts User Guide*\.

### Create a volume on an Outpost<a name="outposts-volumes"></a>

You can create EBS volumes in the Outpost subnet that you created\. When you create the volume, specify the Amazon Resource Name \(ARN\) of the Outpost\.

The following [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command creates an empty 50 GB volume on the specified Outpost\.

```
aws ec2 create-volume --availability-zone us-east-2a --outpost-arn arn:aws:outposts:us-east-2:123456789012:outpost/op-03e6fecad652a6138 --size 50
```

You can dynamically modify the size of your Amazon EBS gp2 volumes without detaching them\. For more information about modifying a volume without detaching it, see [Request modifications to your EBS volumes](requesting-ebs-volume-modifications.md)\.
# Elastic IP addresses<a name="elastic-ip-addresses-eip"></a>

An *Elastic IP address* is a static IPv4 address designed for dynamic cloud computing\. An Elastic IP address is allocated to your AWS account, and is yours until you release it\. By using an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account\. Alternatively, you can specify the Elastic IP address in a DNS record for your domain, so that your domain points to your instance\. For more information, see the documentation for your domain registrar, or [Set up dynamic DNS on Your Amazon Linux instance](dynamic-dns.md)\.

An Elastic IP address is a public IPv4 address, which is reachable from the internet\. If your instance does not have a public IPv4 address, you can associate an Elastic IP address with your instance to enable communication with the internet\. For example, this allows you to connect to your instance from your local computer\.

We currently do not support Elastic IP addresses for IPv6\.

**Topics**
+ [Elastic IP address pricing](#eip-pricing)
+ [Elastic IP address basics](#eip-basics)
+ [Work with Elastic IP addresses](#working-with-eips)
+ [Use reverse DNS for email applications](#Using_Elastic_Addressing_Reverse_DNS)
+ [Elastic IP address limit](#using-instance-addressing-limit)

## Elastic IP address pricing<a name="eip-pricing"></a>

To ensure efficient use of Elastic IP addresses, we impose a small hourly charge if an Elastic IP address is not associated with a running instance, or if it is associated with a stopped instance or an unattached network interface\. While your instance is running, you are not charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance\.

For more information, see Elastic IP Addresses on the [Amazon EC2 Pricing, On\-Demand Pricing page](http://aws.amazon.com/ec2/pricing/on-demand/)\.

## Elastic IP address basics<a name="eip-basics"></a>

The following are the basic characteristics of an Elastic IP address:
+ An Elastic IP address is static; it does not change over time\.
+ To use an Elastic IP address, you first allocate one to your account, and then associate it with your instance or a network interface\.
+ When you associate an Elastic IP address with an instance, it is also associated with the instance's primary network interface\. When you associate an Elastic IP address with a network interface that is attached to an instance, it is also associated with the instance\.
+ When you associate an Elastic IP address with an instance or its primary network interface, the instance's public IPv4 address \(if it had one\) is released back into Amazon's pool of public IPv4 addresses\. You cannot reuse a public IPv4 address, and you cannot convert a public IPv4 address to an Elastic IP address\. For more information, see [Public IPv4 addresses](using-instance-addressing.md#concepts-public-addresses)\.
+ You can disassociate an Elastic IP address from a resource, and then associate it with a different resource\. To avoid unexpected behavior, ensure that all active connections to the resource named in the existing association are closed before you make the change\. After you have associated your Elastic IP address to a different resource, you can reopen your connections to the newly associated resource\.
+ A disassociated Elastic IP address remains allocated to your account until you explicitly release it\. We impose a small hourly charge for Elastic IP addresses that are not associated with a running instance\.
+ When you associate an Elastic IP address with an instance that previously had a public IPv4 address, the public DNS host name of the instance changes to match the Elastic IP address\.
+ We resolve a public DNS host name to the public IPv4 address or the Elastic IP address of the instance outside the network of the instance, and to the private IPv4 address of the instance from within the network of the instance\.
+ An Elastic IP address comes from Amazon's pool of IPv4 addresses, or from a custom IP address pool that you have brought to your AWS account\.
+ When you allocate an Elastic IP address from an IP address pool that you have brought to your AWS account, it does not count toward your Elastic IP address limits\. For more information, see [Elastic IP address limit](#using-instance-addressing-limit)\.
+ When you allocate the Elastic IP addresses, you can associate the Elastic IP addresses with a network border group\. This is the location from which we advertise the CIDR block\. Setting the network border group limits the CIDR block to this group\. If you do not specify the network border group, we set the border group containing all of the Availability Zones in the Region \(for example, `us-west-2`\)\.
+ An Elastic IP address is for use in a specific network border group only\.
+ An Elastic IP address is for use in a specific Region only, and cannot be moved to a different Region\.

## Work with Elastic IP addresses<a name="working-with-eips"></a>

The following sections describe how you can work with Elastic IP addresses\.

**Topics**
+ [Allocate an Elastic IP address](#using-instance-addressing-eips-allocating)
+ [Describe your Elastic IP addresses](#using-instance-addressing-eips-describing)
+ [Tag an Elastic IP address](#using-instance-addressing-eips-tagging)
+ [Associate an Elastic IP address with an instance or network interface](#using-instance-addressing-eips-associating)
+ [Disassociate an Elastic IP address](#using-instance-addressing-eips-associating-different)
+ [Release an Elastic IP address](#using-instance-addressing-eips-releasing)
+ [Recover an Elastic IP address](#using-eip-recovering)

### Allocate an Elastic IP address<a name="using-instance-addressing-eips-allocating"></a>

You can allocate an Elastic IP address from Amazon's pool of public IPv4 addresses, or from a custom IP address pool that you have brought to your AWS account\. For more information about bringing your own IP address range to your AWS account, see [Bring your own IP addresses \(BYOIP\) in Amazon EC2](ec2-byoip.md)\.

You can allocate an Elastic IP address using one of the following methods\.

------
#### [ New console ]

**To allocate an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Network & Security**, **Elastic IPs**\.

1. Choose **Allocate Elastic IP address**\.

1. For **Public IPv4 address pool**, choose one of the following:
   + **Amazon's pool of IPv4 addresses**—If you want an IPv4 address to be allocated from Amazon's pool of IPv4 addresses\.
   + **Public IPv4 address that you bring to your AWS account**—If you want to allocate an IPv4 address from an IP address pool that you have brought to your AWS account\. This option is disabled if you do not have any IP address pools\.
   + **Customer owned pool of IPv4 addresses**—If you want to allocate an IPv4 address from a pool created from your on\-premises network for use with an AWS Outpost\. This option is disabled if you do not have an AWS Outpost\.

1. \(Optional\) Add or remove a tag\.

   \[Add a tag\] Choose **Add new tag** and do the following:
   + For **Key**, enter the key name\.
   + For **Value**, enter the key value\.

   \[Remove a tag\] Choose **Remove** to the right of the tag’s Key and Value\.

1. Choose **Allocate**\.

------
#### [ Old console ]

**To allocate an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\.

1. For **IPv4 address pool**, choose **Amazon pool**\.

1. Choose **Allocate**, and close the confirmation screen\.

------
#### [ AWS CLI ]

**To allocate an Elastic IP address**  
Use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) AWS CLI command\.

------
#### [ PowerShell ]

**To allocate an Elastic IP address**  
Use the [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) AWS Tools for Windows PowerShell command\.

------

### Describe your Elastic IP addresses<a name="using-instance-addressing-eips-describing"></a>

You can describe an Elastic IP address using one of the following methods\.

------
#### [ New console ]

**To describe your Elastic IP addresses**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address to view and choose **Actions**, **View details**\.

------
#### [ Old console ]

**To describe your Elastic IP addresses**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select a filter from the Resource Attribute list to begin searching\. You can use multiple filters in a single search\.

------
#### [ AWS CLI ]

**To describe your Elastic IP addresses**  
Use the [describe\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) AWS CLI command\.

------
#### [ PowerShell ]

**To describe your Elastic IP addresses**  
Use the [Get\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) AWS Tools for Windows PowerShell command\.

------

### Tag an Elastic IP address<a name="using-instance-addressing-eips-tagging"></a>

You can assign custom tags to your Elastic IP addresses to categorize them in different ways, for example, by purpose, owner, or environment\. This helps you to quickly find a specific Elastic IP address based on the custom tags that you assigned to it\.

Cost allocation tracking using Elastic IP address tags is not supported\.

You can tag an Elastic IP address using one of the following methods\.

------
#### [ New console ]

**To tag an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address to tag and choose **Actions**, **View details**\. 

1. In the **Tags** section, choose **Manage tags**\.

1. Specify a tag key and value pair\.

1. \(Optional\) Choose **Add tag** to add additional tags\.

1. Choose **Save**\.

------
#### [ Old console ]

**To tag an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address to tag and choose **Tags**\. 

1. Choose **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, choose **Create Tag**, and then specify the key and value for the tag\.

1. \(Optional\) Choose **Create Tag** to add additional tags to the Elastic IP address\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To tag an Elastic IP address**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\.

```
aws ec2 create-tags --resources eipalloc-12345678 --tags Key=Owner,Value=TeamA
```

------
#### [ PowerShell ]

**To tag an Elastic IP address**  
Use the [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) AWS Tools for Windows PowerShell command\.

The `New-EC2Tag` command needs a `Tag` parameter, which specifies the key and value pair to be used for the Elastic IP address tag\. The following commands create the `Tag` parameter\.

```
PS C:\> $tag = New-Object Amazon.EC2.Model.Tag
	PS C:\> $tag.Key = "Owner"
	PS C:\> $tag.Value = "TeamA"
```

```
PS C:\> New-EC2Tag -Resource eipalloc-12345678 -Tag $tag
```

------

### Associate an Elastic IP address with an instance or network interface<a name="using-instance-addressing-eips-associating"></a>

If you're associating an Elastic IP address with your instance to enable communication with the internet, you must also ensure that your instance is in a public subnet\. For more information, see [Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.

You can associate an Elastic IP address with an instance or network interface using one of the following methods\.

------
#### [ New console ]

**To associate an Elastic IP address with an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address to associate and choose **Actions**, **Associate Elastic IP address**\. 

1. For **Resource type**, choose **Instance**\.

1. For instance, choose the instance with which to associate the Elastic IP address\. You can also enter text to search for a specific instance\.

1. \(Optional\) For **Private IP address**, specify a private IP address with which to associate the Elastic IP address\.

1. Choose **Associate**\.

**To associate an Elastic IP address with a network interface**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address to associate and choose **Actions**, **Associate Elastic IP address**\. 

1. For **Resource type**, choose **Network interface**\.

1. For **Network interface**, choose the network interface with which to associate the Elastic IP address\. You can also enter text to search for a specific network interface\.

1. \(Optional\) For **Private IP address**, specify a private IP address with which to associate the Elastic IP address\.

1. Choose **Associate**\.

------
#### [ Old console ]

**To associate an Elastic IP address with an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select an Elastic IP address and choose **Actions**, **Associate address**\. 

1. Select the instance from **Instance** and then choose **Associate**\.

------
#### [ AWS CLI ]

**To associate an Elastic IP address**  
Use the [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) AWS CLI command\.

------
#### [ PowerShell ]

**To associate an Elastic IP address**  
Use the [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) AWS Tools for Windows PowerShell command\.

------

### Disassociate an Elastic IP address<a name="using-instance-addressing-eips-associating-different"></a>

You can disassociate an Elastic IP address from an instance or network interface at any time\. After you disassociate the Elastic IP address, you can reassociate it with another resource\.

You can disassociate an Elastic IP address using one of the following methods\.

------
#### [ New console ]

**To disassociate and reassociate an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address to disassociate, choose **Actions**, **Disassociate Elastic IP address**\.

1. Choose **Disassociate**\.

------
#### [ Old console ]

**To disassociate and reassociate an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address, choose **Actions**, and then select **Disassociate address**\. 

1. Choose **Disassociate address**\.

------
#### [ AWS CLI ]

**To disassociate an Elastic IP address**  
Use the [disassociate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) AWS CLI command\.

------
#### [ PowerShell ]

**To disassociate an Elastic IP address**  
Use the [Unregister\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) AWS Tools for Windows PowerShell command\.

------

### Release an Elastic IP address<a name="using-instance-addressing-eips-releasing"></a>

If you no longer need an Elastic IP address, we recommend that you release it using one of the following methods\. The address to release must not be currently associated with an AWS resource, such as an EC2 instance, NAT gateway, or Network Load Balancer\.

**Note**  
If you contacted AWS support to set up reverse DNS for an Elastic IP \(EIP\) address, you can remove the reverse DNS, but you can’t release the Elastic IP address because it’s been locked by AWS support\. To unlock the Elastic IP address, contact [AWS Support](https://console.aws.amazon.com/support/home#/)\. Once the Elastic IP address is unlocked, you can release the Elastic IP address\.

------
#### [ New console ]

**To release an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address to release and choose **Actions**, **Release Elastic IP addresses**\.

1. Choose **Release**\.

------
#### [ Old console ]

**To release an Elastic IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, and then select **Release addresses**\. Choose **Release** when prompted\.

------
#### [ AWS CLI ]

**To release an Elastic IP address**  
Use the [release\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) AWS CLI command\.

------
#### [ PowerShell ]

**To release an Elastic IP address**  
Use the [Remove\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Address.html) AWS Tools for Windows PowerShell command\.

------

### Recover an Elastic IP address<a name="using-eip-recovering"></a>

If you have released your Elastic IP address, you might be able to recover it\. The following rules apply:
+ You cannot recover an Elastic IP address if it has been allocated to another AWS account, or if it will result in your exceeding your Elastic IP address limit\.
+ You cannot recover tags associated with an Elastic IP address\.
+ You can recover an Elastic IP address using the Amazon EC2 API or a command line tool only\.

------
#### [ AWS CLI ]

**To recover an Elastic IP address**  
Use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) AWS CLI command and specify the IP address using the `--address` parameter as follows\.

```
aws ec2 allocate-address --domain vpc --address 203.0.113.3
```

------
#### [ PowerShell ]

**To recover an Elastic IP address**  
Use the [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) AWS Tools for Windows PowerShell command and specify the IP address using the `-Address` parameter as follows\.

```
PS C:\> New-EC2Address -Address 203.0.113.3 -Domain vpc -Region us-east-1
```

------

## Use reverse DNS for email applications<a name="Using_Elastic_Addressing_Reverse_DNS"></a>

If you intend to send email to third parties from an instance, we recommend that you provision one or more Elastic IP addresses and assign static reverse DNS records to the Elastic IP addresses that you use to send email\. This can help you avoid having your email flagged as spam by some anti\-spam organizations\. AWS works with ISPs and internet anti\-spam organizations to reduce the chance that your email sent from these addresses will be flagged as spam\.

**Considerations**
+ Before you create a reverse DNS record, you must set a corresponding forward DNS record \(record type A\) that points to your Elastic IP address\.
+ If a reverse DNS record is associated with an Elastic IP address, the Elastic IP address is locked to your account and cannot be released from your account until the record is removed\.
+ 

**AWS GovCloud \(US\) Region**  
You can't create a reverse DNS record using the console or AWS CLI\. AWS must assign the static reverse DNS records for you\. Open [Request to remove reverse DNS and email sending limitations](https://console.aws.amazon.com/support/contacts?#/rdns-limits) and provide us with your Elastic IP addresses and reverse DNS records\.

### Create a reverse DNS record<a name="eip-create-rdns-record"></a>

To create a reverse DNS record, choose the tab that matches your preferred method\.

------
#### [ Console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address and choose **Actions**, **Update reverse DNS**\.

1. For **Reverse DNS domain name**, enter the domain name\.

1. Enter **update** to confirm\.

1. Choose **Update**\.

------
#### [ AWS CLI ]

Use the [modify\-address\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-address-attribute.html) command in the AWS CLI, as shown in the following example:

```
aws ec2 modify-address-attribute --allocation-id eipalloc-abcdef01234567890  --domain-name example.com
{
    "Addresses": [
        {
            "PublicIp": "192.0.2.0",
            "AllocationId": "eipalloc-abcdef01234567890",
            "PtrRecord": "example.net."
            "PtrRecordUpdate": {
                "Value": "example.com.",
                "Status": "PENDING"
        }
    ]
}
```

------

### Remove a reverse DNS record<a name="w1544aac23c22c17b9"></a>

To remove a reverse DNS record, choose the tab that matches your preferred method\.

------
#### [ Console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address and choose **Actions**, **Update reverse DNS**\.

1. For **Reverse DNS domain name**, clear the domain name\.

1. Enter **update** to confirm\.

1. Choose **Update**\.

------
#### [ AWS CLI ]

Use the [reset\-address\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/reset-address-attribute.html) command in the AWS CLI, as shown in the following example:

```
aws ec2 reset-address-attribute --allocation-id eipalloc-abcdef01234567890 --attribute domain-name
{
    "Addresses": [
        {
            "PublicIp": "192.0.2.0",
            "AllocationId": "eipalloc-abcdef01234567890",
            "PtrRecord": "example.com."
            "PtrRecordUpdate": {
                "Value": "example.net.",
                "Status": "PENDING"
        }
    ]
}
```

**Note**  
If you receive the following error when you run the command, you can submit a [Request to remove email sending limitations](http://aws.amazon.com/forms/ec2-email-limit-rdns-request) to customer support for assistance\.  
*The address with allocation id cannot be released because it is locked to your account*\.

------

## Elastic IP address limit<a name="using-instance-addressing-limit"></a>

By default, all AWS accounts are limited to five \(5\) Elastic IP addresses per Region, because public \(IPv4\) internet addresses are a scarce public resource\. We strongly encourage you to use an Elastic IP address primarily for the ability to remap the address to another instance in the case of instance failure, and to use [DNS hostnames](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html#vpc-dns-hostnames) for all other inter\-node communication\.

**To verify how many Elastic IP addresses are in use**  
Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/) and choose **Elastic IPs** from the navigation pane\.

**To verify your current account limit for Elastic IP addresses**  
You can verify your limit in either the Amazon EC2 console or the Service Quotas console\. Do one of the following:
+ Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

  Choose **Limits** from the navigation pane, and then enter **IP** in the search field\. The limit is **EC2\-VPC Elastic IPs**\. If you have access to EC2\-Classic, there is an additional limit, **EC2\-Classic Elastic IPs**\.
+ Open the Service Quotas console at [https://console\.aws\.amazon\.com/servicequotas/](https://console.aws.amazon.com/servicequotas/)\.

  On the Dashboard, choose **Amazon Elastic Compute Cloud \(Amazon EC2\)**\. If Amazon Elastic Compute Cloud \(Amazon EC2\) is not listed on the Dashboard, choose **AWS services**, enter **EC2** in the search field, and then choose **Amazon Elastic Compute Cloud \(Amazon EC2\)**\.

  On the Amazon EC2 service quotas page, enter **IP** in the search field\. The limit is **EC2\-VPC Elastic IPs**\. If you have access to EC2\-Classic, there is an additional limit, **EC2\-Classic Elastic IPs**\. For more information, choose the limit\.

If you think your architecture warrants additional Elastic IP addresses, you can request a quota increase directly from the Service Quotas console\.
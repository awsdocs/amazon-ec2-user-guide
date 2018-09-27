# Elastic IP Addresses<a name="elastic-ip-addresses-eip"></a>

An *Elastic IP address* is a static IPv4 address designed for dynamic cloud computing\. An Elastic IP address is associated with your AWS account\. With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account\. 

An Elastic IP address is a public IPv4 address, which is reachable from the internet\. If your instance does not have a public IPv4 address, you can associate an Elastic IP address with your instance to enable communication with the internet; for example, to connect to your instance from your local computer\.

We currently do not support Elastic IP addresses for IPv6\.

**Topics**
+ [Elastic IP Address Basics](#eip-basics)
+ [Working with Elastic IP Addresses](#working-with-eips)
+ [Using Reverse DNS for Email Applications](#Using_Elastic_Addressing_Reverse_DNS)
+ [Elastic IP Address Limit](#using-instance-addressing-limit)

## Elastic IP Address Basics<a name="eip-basics"></a>

The following are the basic characteristics of an Elastic IP address:
+ To use an Elastic IP address, you first allocate one to your account, and then associate it with your instance or a network interface\.
+ When you associate an Elastic IP address with an instance or its primary network interface, the instance's public IPv4 address \(if it had one\) is released back into Amazon's pool of public IPv4 addresses\. You cannot reuse a public IPv4 address, and you cannot convert a public IPv4 address to an Elastic IP address\. For more information, see [Public IPv4 Addresses and External DNS Hostnames](using-instance-addressing.md#concepts-public-addresses)\.
+ You can disassociate an Elastic IP address from a resource, and reassociate it with a different resource\. Any open connections to an instance continue to work for a time even after you disassociate its Elastic IP address and reassociate it with another instance\. We recommend that you reopen these connections using the reassociated Elastic IP address\.
+ A disassociated Elastic IP address remains allocated to your account until you explicitly release it\.
+ To ensure efficient use of Elastic IP addresses, we impose a small hourly charge if an Elastic IP address is not associated with a running instance, or if it is associated with a stopped instance or an unattached network interface\. While your instance is running, you are not charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance\. For more information, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/#Elastic_IP_Addresses)\.
+ An Elastic IP address is for use in a specific region only\. 
+ When you associate an Elastic IP address with an instance that previously had a public IPv4 address, the public DNS hostname of the instance changes to match the Elastic IP address\. 
+ We resolve a public DNS hostname to the public IPv4 address or the Elastic IP address of the instance outside the network of the instance, and to the private IPv4 address of the instance from within the network of the instance\. 

## Working with Elastic IP Addresses<a name="working-with-eips"></a>

The following sections describe how you can work with Elastic IP addresses\.

**Topics**
+ [Allocating an Elastic IP Address](#using-instance-addressing-eips-allocating)
+ [Describing Your Elastic IP Addresses](#using-instance-addressing-eips-describing)
+ [Tagging an Elastic IP Address](#using-instance-addressing-eips-tagging)
+ [Associating an Elastic IP Address with a Running Instance](#using-instance-addressing-eips-associating)
+ [Disassociating an Elastic IP Address and Reassociating with a Different Instance](#using-instance-addressing-eips-associating-different)
+ [Releasing an Elastic IP Address](#using-instance-addressing-eips-releasing)
+ [Recovering an Elastic IP Address](#using-eip-recovering)

### Allocating an Elastic IP Address<a name="using-instance-addressing-eips-allocating"></a>

You can allocate an Elastic IP address using the Amazon EC2 console or the command line\.

**To allocate an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Choose **Allocate new address**\. 

1. Choose **Allocate**, and close the confirmation screen\.

**To allocate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) \(AWS CLI\)
+ [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Describing Your Elastic IP Addresses<a name="using-instance-addressing-eips-describing"></a>

You can describe an Elastic IP address using the Amazon EC2 or the command line\.

**To describe your Elastic IP addresses using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select a filter from the Resource Attribute list to begin searching\. You can use multiple filters in a single search\.

**To describe your Elastic IP addresses using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-addresses](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) \(AWS CLI\)
+ [Get\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Tagging an Elastic IP Address<a name="using-instance-addressing-eips-tagging"></a>

You can assign custom tags to your Elastic IP addresses to categorize them in different ways, for example, by purpose, owner, or environment\. This helps you to quickly find a specific Elastic IP address based on the custom tags you've assigned it\.

**Note**  
Cost allocation tracking using Elastic IP address tags is not supported\.

**To tag an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address to tag and choose **Tags**\. 

1. Choose **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, choose **Create Tag**, and then specify the key and value for the tag\.

1. \(Optional\) Choose **Create Tag** to add additional tags to the Elastic IP address\.

1. Choose **Save**\.

**To tag an Elastic IP address using the command line**

Use one of the following commands:
+ [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)

  ```
  aws ec2 create-tags --resources eipalloc-12345678 --tags Key=Owner,Value=TeamA
  ```
+ [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

  The `New-EC2Tag` command needs a `Tag` parameter, which specifies the key and value pair to be used for the Elastic IP address tag\. The following commands create the `Tag` parameter:

  ```
  PS C:\> $tag = New-Object Amazon.EC2.Model.Tag
  PS C:\> $tag.Key = "Owner"
  PS C:\> $tag.Value = "TeamA"
  ```

  ```
  PS C:\> New-EC2Tag -Resource eipalloc-12345678 -Tag $tag
  ```

### Associating an Elastic IP Address with a Running Instance<a name="using-instance-addressing-eips-associating"></a>

You can associate an Elastic IP address to an instance using the Amazon EC2 console or the command line\.

If you're associating an Elastic IP address with your instance to enable communication with the internet, you must also ensure that your instance is in a public subnet\. For more information, see [Internet Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.

**To associate an Elastic IP address with an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select an Elastic IP address and choose **Actions**, **Associate address**\. 

1. Select the instance from **Instance** and then choose **Associate**\.

**To associate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)
+ [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Disassociating an Elastic IP Address and Reassociating with a Different Instance<a name="using-instance-addressing-eips-associating-different"></a>

You can disassociate an Elastic IP address and then reassociate it using the Amazon EC2 console or the command line\.

**To disassociate and reassociate an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select the Elastic IP address, choose **Actions**, and then select **Disassociate address**\. 

1. Choose **Disassociate address**\.

1. Select the address that you disassociated in the previous step\. For **Actions**, choose **Associate address**\.

1. Select the new instance from **Instance**, and then choose **Associate**\. 

**To disassociate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [disassociate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) \(AWS CLI\)
+ [Unregister\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**To associate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [associate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)
+ [Register\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Releasing an Elastic IP Address<a name="using-instance-addressing-eips-releasing"></a>

If you no longer need an Elastic IP address, we recommend that you release it \(the address must not be associated with an instance\)\.

**To release an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, and then select **Release addresses**\. Choose **Release** when prompted\.

**To release an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [release\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) \(AWS CLI\)
+ [Remove\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Recovering an Elastic IP Address<a name="using-eip-recovering"></a>

If you have released your Elastic IP address, you might be able to recover it\. The following rules apply:
+ You cannot recover an Elastic IP address if it has been allocated to another AWS account, or if it will result in your exceeding your Elastic IP address limit\.
+ You cannot recover tags associated with an Elastic IP address\.
+ You can recover an Elastic IP address using the Amazon EC2 API or a command line tool only\.

**To recover an Elastic IP address using the command line**
+ \(AWS CLI\) Use the [allocate\-address](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command and specify the IP address using the `--address` parameter\.

  ```
  aws ec2 allocate-address --domain vpc --address 203.0.113.3
  ```
+ \(AWS Tools for Windows PowerShell\) Use the [New\-EC2Address](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) command and specify the IP address using the `-Address` parameter\.

  ```
  PS C:\> New-EC2Address -Address 203.0.113.3 -Domain vpc -Region us-east-1
  ```

## Using Reverse DNS for Email Applications<a name="Using_Elastic_Addressing_Reverse_DNS"></a>

If you intend to send email to third parties from an instance, we suggest you provision one or more Elastic IP addresses and provide them to us\. AWS works with ISPs and internet anti\-spam organizations to reduce the chance that your email sent from these addresses will be flagged as spam\.

In addition, assigning a static reverse DNS record to your Elastic IP address used to send email can help avoid having email flagged as spam by some anti\-spam organizations\. Note that a corresponding forward DNS record \(record type A\) pointing to your Elastic IP address must exist before we can create your reverse DNS record\. 

If a reverse DNS record is associated with an Elastic IP address, the Elastic IP address is locked to your account and cannot be released from your account until the record is removed\. 

To remove email sending limits, or to provide us with your Elastic IP addresses and reverse DNS records, go to the [Request to Remove Email Sending Limitations](https://aws.amazon.com/forms/ec2-email-limit-rdns-request) page\.

## Elastic IP Address Limit<a name="using-instance-addressing-limit"></a>

By default, all AWS accounts are limited to five \(5\) Elastic IP addresses per region, because public \(IPv4\) internet addresses are a scarce public resource\. We strongly encourage you to use an Elastic IP address primarily for the ability to remap the address to another instance in the case of instance failure, and to use DNS hostnames for all other inter\-node communication\.

If you feel your architecture warrants additional Elastic IP addresses, complete the [Amazon EC2 Elastic IP Address Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-elastic-ips)\. Describe your use case so that we can understand your need for additional addresses\.
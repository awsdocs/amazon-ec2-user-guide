# Elastic IP Addresses<a name="elastic-ip-addresses-eip"></a>

An *Elastic IP address* is a static IPv4 address designed for dynamic cloud computing\. An Elastic IP address is associated with your AWS account\. With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account\. 

An Elastic IP address is a public IPv4 address, which is reachable from the internet\. If your instance does not have a public IPv4 address, you can associate an Elastic IP address with your instance to enable communication with the internet; for example, to connect to your instance from your local computer\.

We currently do not support Elastic IP addresses for IPv6\.


+ [Elastic IP Address Basics](#eip-basics)
+ [Elastic IP Address Differences for EC2\-Classic and EC2\-VPC](#VPC_EIP_EC2_Differences)
+ [Working with Elastic IP Addresses](#working-with-eips)
+ [Using Reverse DNS for Email Applications](#Using_Elastic_Addressing_Reverse_DNS)
+ [Elastic IP Address Limit](#using-instance-addressing-limit)

## Elastic IP Address Basics<a name="eip-basics"></a>

The following are the basic characteristics of an Elastic IP address:

+ To use an Elastic IP address, you first allocate one to your account, and then associate it with your instance or a network interface\.

+ When you associate an Elastic IP address with an instance or its primary network interface, the instance's public IPv4 address \(if it had one\) is released back into Amazon's pool of public IPv4 addresses\. You cannot reuse a public IPv4 address\. For more information, see [Public IPv4 Addresses and External DNS Hostnames](using-instance-addressing.md#concepts-public-addresses)\.

+ You can disassociate an Elastic IP address from a resource, and reassociate it with a different resource\.

+ A disassociated Elastic IP address remains allocated to your account until you explicitly release it\.

+ To ensure efficient use of Elastic IP addresses, we impose a small hourly charge if an Elastic IP address is not associated with a running instance, or if it is associated with a stopped instance or an unattached network interface\. While your instance is running, you are not charged for one Elastic IP address associated with the instance, but you are charged for any additional Elastic IP addresses associated with the instance\. For more information, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/#Elastic_IP_Addresses)\.

+ An Elastic IP address is for use in a specific region only\. 

+ When you associate an Elastic IP address with an instance that previously had a public IPv4 address, the public DNS hostname of the instance changes to match the Elastic IP address\. 

+ We resolve a public DNS hostname to the public IPv4 address or the Elastic IP address of the instance outside the network of the instance, and to the private IPv4 address of the instance from within the network of the instance\. 

If your account supports EC2\-Classic, the use and behavior of Elastic IP addresses for EC2\-Classic and EC2\-VPC may differ\. For more information, see [Elastic IP Address Differences for EC2\-Classic and EC2\-VPC](#VPC_EIP_EC2_Differences)\.

## Elastic IP Address Differences for EC2\-Classic and EC2\-VPC<a name="VPC_EIP_EC2_Differences"></a>

If your account supports EC2\-Classic, there's one pool of Elastic IP addresses for use with the EC2\-Classic platform and another for use with the EC2\-VPC platform\. You can't associate an Elastic IP address that you allocated for use with a VPC with an instance in EC2\-Classic, and vice\- versa\. However, you can migrate an Elastic IP address you've allocated for use in the EC2\-Classic platform to the EC2\-VPC platform\. You cannot migrate an Elastic IP address to another region\. For more information about EC2\-Classic and EC2\-VPC, see [Supported Platforms](ec2-supported-platforms.md)\.

When you associate an Elastic IP address with an instance in EC2\-Classic, a default VPC, or an instance in a nondefault VPC in which you assigned a public IPv4 to the eth0 network interface during launch, the instance's current public IPv4 address is released back into the public IP address pool\. If you disassociate an Elastic IP address from the instance, the instance is automatically assigned a new public IPv4 address within a few minutes\. However, if you have attached a second network interface to an instance in a VPC, the instance is not automatically assigned a new public IPv4 address\. For more information about public IPv4 addresses, see [Public IPv4 Addresses and External DNS Hostnames](using-instance-addressing.md#concepts-public-addresses)\.

For information about using an Elastic IP address with an instance in a VPC, see [Elastic IP Addresses](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-ip-addressing.html#vpc-eips) in the *Amazon VPC User Guide*\.

The following table lists the differences between Elastic IP addresses on EC2\-Classic and EC2\-VPC\. For more information about the differences between private and public IP addresses, see [IP Address Differences Between EC2\-Classic and EC2\-VPC](using-instance-addressing.md#differences)\.


| Characteristic | EC2\-Classic | EC2\-VPC | 
| --- | --- | --- | 
|  Allocating an Elastic IP address  |  When you allocate an Elastic IP address, it's for use in EC2\-Classic; however, you can migrate an Elastic IP address to the EC2\-VPC platform\. For more information, see [Migrating an Elastic IP Address from EC2\-Classic to EC2\-VPC](#migrating-eip)\.  |  When you allocate an Elastic IP address, it's for use only in a VPC\.  | 
|  Associating an Elastic IP address  |  You associate an Elastic IP address with an instance\.  |  An Elastic IP address is a property of a network interface\. You can associate an Elastic IP address with an instance by updating the network interface attached to the instance\. For more information, see [Elastic Network Interfaces](using-eni.md)\.  | 
|  Reassociating an Elastic IP address  |  If you try to associate an Elastic IP address that's already associated with another instance, the address is automatically associated with the new instance\.  |  If your account supports EC2\-VPC only, and you try to associate an Elastic IP address that's already associated with another instance, the address is automatically associated with the new instance\. If you're using a VPC in an EC2\-Classic account, and you try to associate an Elastic IP address that's already associated with another instance, it succeeds only if you allowed reassociation\.  | 
| Associating an Elastic IP address with a target that has an existing Elastic IP address | The existing Elastic IP address is disassociated from the instance, but remains allocated to your account\. | If your account supports EC2\-VPC only, the existing Elastic IP address is disassociated from the instance, but remains allocated to your account\. If you're using a VPC in an EC2\-Classic account, you cannot associate an Elastic IP address with a network interface or instance that has an existing Elastic IP address\. | 
|  Stopping an instance  |  If you stop an instance, its Elastic IP address is disassociated, and you must reassociate the Elastic IP address when you restart the instance\.  |  If you stop an instance, its Elastic IP address remains associated\.  | 
|  Assigning multiple IP addresses  |  Instances support only a single private IPv4 address and a corresponding Elastic IP address\.  |  Instances support multiple IPv4 addresses, and each one can have a corresponding Elastic IP address\. For more information, see [Multiple IP Addresses](MultipleIP.md)\.  | 
|  Tagging Elastic IP addresses  |  Does not support Elastic IP address tagging\.  |  Supports Elastic IP address tagging\. This allows you to assign your own metadata to each Elastic IP address\.  | 

### Migrating an Elastic IP Address from EC2\-Classic to EC2\-VPC<a name="migrating-eip"></a>

If your account supports EC2\-Classic, you can migrate Elastic IP addresses that you've allocated for use in the EC2\-Classic platform to the EC2\-VPC platform, within the same region\. This can assist you to migrate your resources from EC2\-Classic to a VPC; for example, you can launch new web servers in your VPC, and then use the same Elastic IP addresses that you used for your web servers in EC2\-Classic for your new VPC web servers\.

After you've migrated an Elastic IP address to EC2\-VPC, you cannot use it in the EC2\-Classic platform; however, if required, you can restore it to EC2\-Classic\. After you've restored an Elastic IP address to EC2\-Classic, you cannot use it in EC2\-VPC until you migrate it again\. You can only migrate an Elastic IP address from EC2\-Classic to EC2\-VPC\. You cannot migrate an Elastic IP address that was originally allocated for use in EC2\-VPC to EC2\-Classic\. 

To migrate an Elastic IP address, it must not be associated with an instance\. For more information about disassociating an Elastic IP address from an instance, see [Disassociating an Elastic IP Address and Reassociating with a Different Instance](#using-instance-addressing-eips-associating-different)\.

You can migrate as many EC2\-Classic Elastic IP addresses as you can have in your account\. However, when you migrate an Elastic IP address to EC2\-VPC, it counts against your Elastic IP address limit for EC2\-VPC\. You cannot migrate an Elastic IP address if it will result in you exceeding your limit\. Similarly, when you restore an Elastic IP address to EC2\-Classic, it counts against your Elastic IP address limit for EC2\-Classic\. For more information, see [Elastic IP Address Limit](#using-instance-addressing-limit)\. 

You cannot migrate an Elastic IP address that has been allocated to your account for less than 24 hours\. 

For more information, see [Moving an Elastic IP Address](#using-eip-migration)\.

## Working with Elastic IP Addresses<a name="working-with-eips"></a>

The following sections describe how you can work with Elastic IP addresses\.


+ [Allocating an Elastic IP Address](#using-instance-addressing-eips-allocating)
+ [Describing Your Elastic IP Addresses](#using-instance-addressing-eips-describing)
+ [Tagging an Elastic IP Address](#using-instance-addressing-eips-tagging)
+ [Associating an Elastic IP Address with a Running Instance](#using-instance-addressing-eips-associating)
+ [Disassociating an Elastic IP Address and Reassociating with a Different Instance](#using-instance-addressing-eips-associating-different)
+ [Moving an Elastic IP Address](#using-eip-migration)
+ [Releasing an Elastic IP Address](#using-instance-addressing-eips-releasing)
+ [Recovering an Elastic IP Address](#using-eip-recovering)

### Allocating an Elastic IP Address<a name="using-instance-addressing-eips-allocating"></a>

You can allocate an Elastic IP address using the Amazon EC2 console or the command line\. If your account supports EC2\-Classic, you can allocate an address for use in EC2\-Classic or in EC2\-VPC\.

**To allocate an Elastic IP address for use in EC2\-VPC using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Choose **Allocate new address**\. 

1. \(EC2\-Classic accounts\) Choose **VPC**, and then choose **Allocate**\. Close the confirmation screen\.

1. \(VPC\-only accounts\) Choose **Allocate**, and close the confirmation screen\.

**To allocate an Elastic IP address for use in EC2\-Classic using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Choose **Allocate new address**\. 

1. Select **Classic**, and then choose **Allocate**\. Close the confirmation screen\.

**To allocate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [allocate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) \(AWS CLI\)

+ [New\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Describing Your Elastic IP Addresses<a name="using-instance-addressing-eips-describing"></a>

You can describe an Elastic IP address using the Amazon EC2 or the command line\.

**To describe your Elastic IP addresses using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select a filter from the Resource Attribute list to begin searching\. You can use multiple filters in a single search\.

**To describe your Elastic IP addresses using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [describe\-addresses](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) \(AWS CLI\)

+ [Get\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Tagging an Elastic IP Address<a name="using-instance-addressing-eips-tagging"></a>

You can assign custom tags to your Elastic IP addresses to categorize them in different ways, for example, by purpose, owner, or environment\. This helps you to quickly find a specific Elastic IP address based on the custom tags you've assigned it\.

**Note**  
Cost allocation tracking using Elastic IP address tags is not supported\.

You can tag an Elastic IP address using the Amazon EC2 console or the command line tools\.

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

+ [create\-tags](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) \(AWS CLI\)

  ```
  aws ec2 create-tags --resources eipalloc-12345678 --tags Key=Owner,Value=TeamA
  ```

+ [New\-EC2Tag](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) \(AWS Tools for Windows PowerShell\)

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

\(VPC only\) If you're associating an Elastic IP address with your instance to enable communication with the internet, you must also ensure that your instance is in a public subnet\. For more information, see [Internet Gateways](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html) in the *Amazon VPC User Guide*\.

**To associate an Elastic IP address with an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\. 

1. Select an Elastic IP address and choose **Actions**, **Associate address**\. 

1. Select the instance from **Instance** and then choose **Associate**\.

**To associate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [associate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)

+ [Register\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

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

+ [disassociate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-address.html) \(AWS CLI\)

+ [Unregister\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**To associate an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [associate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/associate-address.html) \(AWS CLI\)

+ [Register\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Moving an Elastic IP Address<a name="using-eip-migration"></a>

You can move an Elastic IP address from EC2\-Classic to EC2\-VPC using the Amazon EC2 console or the Amazon VPC console\. This option is only available if your account supports EC2\-Classic\.

**To move an Elastic IP address to EC2\-VPC using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, and choose **Actions**, **Move to VPC scope**\.

1. In the confirmation dialog box, choose **Move Elastic IP**\.

**Note**  
You can tag an Elastic IP address after you have moved it from EC2\-Classic to EC2\-VPC\.

You can restore an Elastic IP address to EC2\-Classic using the Amazon EC2 console or the Amazon VPC console\.

**To restore an Elastic IP address to EC2\-Classic using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, **Restore to EC2 scope**\.

1. In the confirmation dialog box, choose **Restore**\.

**Note**  
If you choose to restore a previously migrated Elastic IP adddress to EC2\-Classic, the tags assigned to the Elastic IP address after migration are lost\.

After you've performed the command to move or restore your Elastic IP address, the process of migrating the Elastic IP address can take a few minutes\. Use the [describe\-moving\-addresses](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-moving-addresses.html) command to check whether your Elastic IP address is still moving, or has completed moving\.

After you've moved your Elastic IP address to EC2\-VPC, you can view its allocation ID on the **Elastic IPs** page in the **Allocation ID** field\.

If the Elastic IP address is in a moving state for longer than 5 minutes, contact https://aws\.amazon\.com/premiumsupport/\.

**To move an Elastic IP address using the Amazon EC2 Query API or AWS CLI**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [move\-address\-to\-vpc](http://docs.aws.amazon.com/cli/latest/reference/ec2/move-address-to-vpc.html) \(AWS CLI\)

+ [MoveAddressToVpc](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_MoveAddressToVpc.html) \(Amazon EC2 Query API\)

+ [Move\-EC2AddressToVpc](http://docs.aws.amazon.com/powershell/latest/reference/items/Move-EC2AddressToVpc.html) \(AWS Tools for Windows PowerShell\)

**To restore an Elastic IP address to EC2\-Classic using the Amazon EC2 Query API or AWS CLI**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [restore\-address\-to\-classic](http://docs.aws.amazon.com/cli/latest/reference/ec2/restore-address-to-classic.html) \(AWS CLI\)

+ [RestoreAddressToClassic](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RestoreAddressToClassic.html) \(Amazon EC2 Query API\)

+ [Restore\-EC2AddressToClassic](http://docs.aws.amazon.com/powershell/latest/reference/items/Restore-EC2AddressToClassic.html) \(AWS Tools for Windows PowerShell\)

**To describe the status of your moving addresses using the Amazon EC2 Query API or AWS CLI**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [describe\-moving\-addresses](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-moving-addresses.html) \(AWS CLI\)

+ [DescribeMovingAddresses](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeMovingAddresses.html) \(Amazon EC2 Query API\)

+ [Get\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

**To retrieve the allocation ID for your migrated Elastic IP address in EC2\-VPC**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [describe\-addresses](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-addresses.html) \(AWS CLI\)

+ [DescribeAddresses](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeAddresses.html) \(Amazon EC2 Query API\)

+ [Get\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Releasing an Elastic IP Address<a name="using-instance-addressing-eips-releasing"></a>

If you no longer need an Elastic IP address, we recommend that you release it \(the address must not be associated with an instance\)\. You incur charges for any Elastic IP address that's allocated for use with EC2\-Classic but not associated with an instance\.

You can release an Elastic IP address using the Amazon EC2 console or the command line\.

**To release an Elastic IP address using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic IPs**\.

1. Select the Elastic IP address, choose **Actions**, and then select **Release addresses**\. Choose **Release** when prompted\.

**To release an Elastic IP address using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [release\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/release-address.html) \(AWS CLI\)

+ [Remove\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Address.html) \(AWS Tools for Windows PowerShell\)

### Recovering an Elastic IP Address<a name="using-eip-recovering"></a>

If you have released your Elastic IP address, you might be able to recover it\. The following rules apply:

+ You can only recover an Elastic IP address that was originally allocated for use in EC2\-VPC, or that was moved from EC2\-Classic to EC2\-VPC\.

+ You cannot recover an Elastic IP address if it has been allocated to another AWS account, or if it will result you in exceeding your Elastic IP address limit\.

+ You cannot recover tags associated with an Elastic IP address\.

Currently, you can recover an Elastic IP address using the Amazon EC2 API or a command line tool only\.

**To recover an Elastic IP address using the command line**

1. \(AWS CLI\) Use the [allocate\-address](http://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-address.html) command and specify the IP address using the `--address` parameter\.

   ```
   aws ec2 allocate-address --domain vpc --address 203.0.113.3
   ```

1. \(AWS Tools for Windows PowerShell\) Use the [New\-EC2Address](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Address.html) command and specify the IP address using the `-Address` parameter\.

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

If you feel your architecture warrants additional Elastic IP addresses, complete the [Amazon EC2 Elastic IP Address Request Form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-elastic-ips-ec2-classic)\. Describe your use case so that we can understand your need for additional addresses\.
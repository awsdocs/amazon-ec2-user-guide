# Buying Reserved Instances<a name="ri-market-concepts-buying"></a>

To purchase a Reserved Instance, search for *Reserved Instance offerings* from AWS and third\-party sellers, adjusting your search parameters until you find the exact match that you're looking for\.

When you search for Reserved Instances to buy, you receive a quote on the cost of the returned offerings\. When you proceed with the purchase, AWS automatically places a limit price on the purchase price\. The total cost of your Reserved Instances won't exceed the amount that you were quoted\.

If the price rises or changes for any reason, the purchase is not completed\. If, at the time of purchase, there are offerings similar to your choice but at a lower price, AWS sells you the offerings at the lower price\.

Before you confirm your purchase, review the details of the Reserved Instance that you plan to buy, and make sure that all the parameters are accurate\. After you purchase a Reserved Instance \(either from a third\-party seller in the Reserved Instance Marketplace or from AWS\), you cannot cancel your purchase\.

**Note**  
To purchase and modify Reserved Instances, ensure that your IAM user account has the appropriate permissions, such as the ability to describe Availability Zones\. For information, see [Example Policies for Working With the AWS CLI or an AWS SDK](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExamplePolicies_EC2.html#iam-example-reservedinstances) and [Example Policies for Working in the Amazon EC2 Console](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-ec2-console.html#ex-reservedinstances)\.

**Topics**
+ [Choosing a platform](#ri-choosing-platform)
+ [Queue your purchase](#ri-queued-purchase)
+ [Buy Standard Reserved Instances](#ri-buying-standard)
+ [Buy Convertible Reserved Instances](#ri-buying-convertible)
+ [Buy from the Reserved Instance Marketplace](#ri-market-buying-guide)
+ [View your Reserved Instances](#view-reserved-instances)
+ [Cancel a queued purchase](#cancel-queued-purchase)
+ [Renew a Reserved Instance](#renew-ri)

## Choosing a platform<a name="ri-choosing-platform"></a>

Amazon EC2 supports the following Linux platforms for Reserved Instances:
+ Linux/UNIX
+ Linux with SQL Server Standard
+ Linux with SQL Server Web
+ Linux with SQL Server Enterprise
+ SUSE Linux
+ Red Hat Enterprise Linux

When you purchase a Reserved Instance, you must choose an offering for a *platform* that represents the operating system for your instance\.
+ For SUSE Linux and RHEL distributions, you must choose offerings for those specific platforms, i\.e\., for the **SUSE Linux** or **Red Hat Enterprise Linux** platforms\.
+ For all other Linux distributions \(including Ubuntu\), choose an offering for the **Linux/UNIX** platform\.
+ If you bring your existing RHEL subscription, you must choose an offering for the **Linux/UNIX** platform, not an offering for the **Red Hat Enterprise Linux** platform\.

**Important**  
If you plan to purchase a Reserved Instance to apply to an On\-Demand Instance that was launched from an AWS Marketplace AMI, first check the `PlatformDetails` field of the AMI\. The `PlatformDetails` field indicates which Reserved Instance to purchase\. The platform details of the AMI must match the platform of the Reserved Instance, otherwise the Reserved Instance will not be applied to the On\-Demand Instance\. For information about how to view the platform details of the AMI, see [Understand AMI billing information](ami-billing-info.md)\.

For information about the supported platforms for Windows, see [Choosing a platform](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ri-market-concepts-buying.html#ri-choosing-platform) in the *Amazon EC2 User Guide for Windows Instances*\.

## Queue your purchase<a name="ri-queued-purchase"></a>

By default, when you purchase a Reserved Instance, the purchase is made immediately\. Alternatively, you can queue your purchases for a future date and time\. For example, you can queue a purchase for around the time that an existing Reserved Instance expires\. This can help you ensure that you have uninterrupted coverage\.

You can queue purchases for regional Reserved Instances, but not zonal Reserved Instances or Reserved Instances from other sellers\. You can queue a purchase up to three years in advance\. On the scheduled date and time, the purchase is made using the default payment method\. After the payment is successful, the billing benefit is applied\.

You can view your queued purchases in the Amazon EC2 console\. The status of a queued purchase is **queued**\. You can cancel a queued purchase any time before its scheduled time\. For details, see [Cancel a queued purchase](#cancel-queued-purchase)\.

## Buy Standard Reserved Instances<a name="ri-buying-standard"></a>

You can buy Standard Reserved Instances in a specific Availability Zone and get a capacity reservation\. Alternatively, you can forego the capacity reservation and purchase a regional Standard Reserved Instance\.

**To buy Standard Reserved Instances using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**, and then choose **Purchase Reserved Instances**\.

1. For **Offering Class**, choose **Standard** to display Standard Reserved Instances\.

1. To purchase a capacity reservation, choose **Only show offerings that reserve capacity** in the top\-right corner of the purchase screen\. To purchase a regional Reserved Instance, leave the check box unselected\.

1. Select other configurations as needed and choose **Search**\.

   To purchase a Standard Reserved Instance from the Reserved Instance Marketplace, look for **3rd Party** in the **Seller** column in the search results\. The **Term** column displays non\-standard terms\.

1. For each Reserved Instance that you want to purchase, enter the quantity, and choose **Add to Cart**\.

1. To see a summary of the Reserved Instances that you selected, choose **View Cart**\.

1. If **Order On** is **Now**, the purchase is completed immediately\. To queue a purchase, choose **Now** and select a date\. You can select a different date for each eligible offering in the cart\. The purchase is queued until 00:00 UTC on the selected date\. 

1. To complete the order, choose **Order**\.

   If, at the time of placing the order, there are offerings similar to your choice but with a lower price, AWS sells you the offerings at the lower price\.

1. Choose **Close**\.

   The status of your order is listed in the **State** column\. When your order is complete, the **State** value changes from `payment-pending` to `active`\. When the Reserved Instance is `active`, it is ready to use\.

**Note**  
If the status goes to `retired`, AWS might not have received your payment\. 

**To buy a Standard Reserved Instance using the AWS CLI**

1. Find available Reserved Instances using the [describe\-reserved\-instances\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) command\. Specify `standard` for the `--offering-class` parameter to return only Standard Reserved Instances\. You can apply additional parameters to narrow your results\. For example, if you want to purchase a regional `t2.large` Reserved Instance with a default tenancy for `Linux/UNIX` for a 1\-year term only:

   ```
   aws ec2 describe-reserved-instances-offerings \
       --instance-type t2.large \
       --offering-class standard \
       --product-description "Linux/UNIX" \
       --instance-tenancy default \
       --filters Name=duration,Values=31536000 Name=scope,Values=Region
   ```

   To find Reserved Instances on the Reserved Instance Marketplace only, use the `marketplace` filter and do not specify a duration in the request, as the term might be shorter than a 1– or 3\-year term\.

   ```
   aws ec2 describe-reserved-instances-offerings \
       --instance-type t2.large \
       --offering-class standard \
       --product-description "Linux/UNIX" \
       --instance-tenancy default \
       --filters Name=marketplace,Values=true
   ```

   When you find a Reserved Instance that meets your needs, take note of the offering ID\. For example:

   ```
   "ReservedInstancesOfferingId": "bec624df-a8cc-4aad-a72f-4f8abc34caf2"
   ```

1. Use the [purchase\-reserved\-instances\-offering](https://docs.aws.amazon.com/cli/latest/reference/ec2/purchase-reserved-instances-offering.html) command to buy your Reserved Instance\. You must specify the Reserved Instance offering ID you obtained the previous step and you must specify the number of instances for the reservation\.

   ```
   aws ec2 purchase-reserved-instances-offering \
       --reserved-instances-offering-id bec624df-a8cc-4aad-a72f-4f8abc34caf2 \
       --instance-count 1
   ```

   By default, the purchase is completed immediately\. Alternatively, to queue the purchase, add the following parameter to the previous call\.

   ```
   --purchase-time "2020-12-01T00:00:00Z"
   ```

1. Use the [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command to get the status of your Reserved Instance\.

   ```
   aws ec2 describe-reserved-instances
   ```

Alternatively, use the following AWS Tools for Windows PowerShell commands:
+ [Get\-EC2ReservedInstancesOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html)
+ [New\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2ReservedInstance.html)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html)

After the purchase is complete, if you already have a running instance that matches the specifications of the Reserved Instance, the billing benefit is immediately applied\. You do not have to restart your instances\. If you do not have a suitable running instance, launch an instance and ensure that you match the same criteria that you specified for your Reserved Instance\. For more information, see [Use your Reserved Instances](using-reserved-instances.md)\. 

For examples of how Reserved Instances are applied to your running instances, see [How Reserved Instances are applied](apply_ri.md)\.

## Buy Convertible Reserved Instances<a name="ri-buying-convertible"></a>

You can buy Convertible Reserved Instances in a specific Availability Zone and get a capacity reservation\. Alternatively, you can forego the capacity reservation and purchase a regional Convertible Reserved Instance\.

**To buy Convertible Reserved Instances using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**, and then choose **Purchase Reserved Instances**\.

1. For **Offering Class**, choose **Convertible** to display Convertible Reserved Instances\.

1. To purchase a capacity reservation, choose **Only show offerings that reserve capacity** in the top\-right corner of the purchase screen\. To purchase a regional Reserved Instance, leave the check box unselected\.

1. Select other configurations as needed and choose **Search**\.

1. For each Convertible Reserved Instance that you want to purchase, enter the quantity, and choose **Add to Cart**\.

1. To see a summary of your selection, choose **View Cart**\.

1. If **Order On** is **Now**, the purchase is completed immediately\. To queue a purchase, choose **Now** and select a date\. You can select a different date for each eligible offering in the cart\. The purchase is queued until 00:00 UTC on the selected date\. 

1. To complete the order, choose **Order**\.

   If, at the time of placing the order, there are offerings similar to your choice but with a lower price, AWS sells you the offerings at the lower price\.

1. Choose **Close**\.

   The status of your order is listed in the **State** column\. When your order is complete, the **State** value changes from `payment-pending` to `active`\. When the Reserved Instance is `active`, it is ready to use\.

**Note**  
If the status goes to `retired`, AWS might not have received your payment\. 

**To buy a Convertible Reserved Instance using the AWS CLI**

1. Find available Reserved Instances using the [describe\-reserved\-instances\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) command\. Specify `convertible` for the `--offering-class` parameter to return only Convertible Reserved Instances\. You can apply additional parameters to narrow your results; for example, if you want to purchase a regional `t2.large` Reserved Instance with a default tenancy for `Linux/UNIX`:

   ```
   aws ec2 describe-reserved-instances-offerings \
       --instance-type t2.large \
       --offering-class convertible \
       --product-description "Linux/UNIX" \
       --instance-tenancy default \
       --filters Name=scope,Values=Region
   ```

   When you find a Reserved Instance that meets your needs, take note of the offering ID\. For example:

   ```
   "ReservedInstancesOfferingId": "bec624df-a8cc-4aad-a72f-4f8abc34caf2"
   ```

1. Use the [purchase\-reserved\-instances\-offering](https://docs.aws.amazon.com/cli/latest/reference/ec2/purchase-reserved-instances-offering.html) command to buy your Reserved Instance\. You must specify the Reserved Instance offering ID you obtained the previous step and you must specify the number of instances for the reservation\.

   ```
   aws ec2 purchase-reserved-instances-offering \
       --reserved-instances-offering-id bec624df-a8cc-4aad-a72f-4f8abc34caf2 \
       --instance-count 1
   ```

   By default, the purchase is completed immediately\. Alternatively, to queue the purchase, add the following parameter to the previous call\.

   ```
   --purchase-time "2020-12-01T00:00:00Z"
   ```

1. Use the [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command to get the status of your Reserved Instance\.

   ```
   aws ec2 describe-reserved-instances
   ```

Alternatively, use the following AWS Tools for Windows PowerShell commands:
+ [Get\-EC2ReservedInstancesOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html)
+ [New\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2ReservedInstance.html)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html)

If you already have a running instance that matches the specifications of the Reserved Instance, the billing benefit is immediately applied\. You do not have to restart your instances\. If you do not have a suitable running instance, launch an instance and ensure that you match the same criteria that you specified for your Reserved Instance\. For more information, see [Use your Reserved Instances](using-reserved-instances.md)\. 

For examples of how Reserved Instances are applied to your running instances, see [How Reserved Instances are applied](apply_ri.md)\.

## Buy from the Reserved Instance Marketplace<a name="ri-market-buying-guide"></a>

You can purchase Reserved Instances from third\-party sellers who own Reserved Instances that they no longer need from the Reserved Instance Marketplace\. You can do this using the Amazon EC2 console or a command line tool\. The process is similar to purchasing Reserved Instances from AWS\. For more information, see [Buy Standard Reserved Instances](#ri-buying-standard)\.

There are a few differences between Reserved Instances purchased in the Reserved Instance Marketplace and Reserved Instances purchased directly from AWS:
+ **Term** – Reserved Instances that you purchase from third\-party sellers have less than a full standard term remaining\. Full standard terms from AWS run for one year or three years\.
+ **Upfront price** – Third\-party Reserved Instances can be sold at different upfront prices\. The usage or recurring fees remain the same as the fees set when the Reserved Instances were originally purchased from AWS\.
+ **Types of Reserved Instances** – Only Amazon EC2 Standard Reserved Instances can be purchased from the Reserved Instance Marketplace\. Convertible Reserved Instances, Amazon RDS, and Amazon ElastiCache Reserved Instances are not available for purchase on the Reserved Instance Marketplace\.

Basic information about you is shared with the seller, for example, your ZIP code and country information\.

This information enables sellers to calculate any necessary transaction taxes that they have to remit to the government \(such as sales tax or value\-added tax\) and is provided as a disbursement report\. In rare circumstances, AWS might have to provide the seller with your email address, so that they can contact you regarding questions related to the sale \(for example, tax questions\)\.

For similar reasons, AWS shares the legal entity name of the seller on the buyer's purchase invoice\. If you need additional information about the seller for tax or related reasons, contact [AWS Support](https://aws.amazon.com/contact-us/)\.

## View your Reserved Instances<a name="view-reserved-instances"></a>

You can view the Reserved Instances you've purchased using the Amazon EC2 console, or a command line tool\.

**To view your Reserved Instances in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Your active and retired Reserved Instances are listed\. The **State** column displays the state\. 

1. If you are a seller in the Reserved Instance Marketplace the **My Listings** tab displays the status of a reservation that's listed in the [Reserved Instance Marketplace](ri-market-general.md)\. For more information, see [Reserved Instance listing states](ri-market-general.md#ri-listing-states)\.

**To view your Reserved Instances using the command line**
+ [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) \(AWS CLI\)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html) \(Tools for Windows PowerShell\)

## Cancel a queued purchase<a name="cancel-queued-purchase"></a>

You can queue a purchase up to three years in advance\. You can cancel a queued purchase any time before its scheduled time\.

**To cancel a queued purchase**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Select one or more Reserved Instances\.

1. Choose **Actions**, **Delete Queued Reserved Instances**\.

1. When prompted for confirmation, choose **Yes, Delete**\.

**To cancel a queued purchase using the command line**
+ [delete\-queued\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-queued-reserved-instances.html) \(AWS CLI\)
+ [Remove\-EC2QueuedReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2QueuedReservedInstance.html) \(Tools for Windows PowerShell\)

## Renew a Reserved Instance<a name="renew-ri"></a>

You can renew a Reserved Instance before it is scheduled to expire\. Renewing a Reserved Instance queues the purchase of a Reserved Instance with the same configuration until the current Reserved Instance expires\.

**To renew an Reserved Instance using a queued purchase**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Select one or more Reserved Instances\.

1. Choose **Actions**, **Renew Reserved Instances**\.

1. To complete the order, choose **Order**\.
# Buying Reserved Instances<a name="ri-market-concepts-buying"></a>

To purchase a Reserved Instance, search for *Reserved Instance offerings* from AWS and third\-party sellers, adjusting your search parameters until you find the exact match that you're looking for\.

When you search for Reserved Instances to buy, you receive a quote on the cost of the returned offerings\. When you proceed with the purchase, AWS automatically places a limit price on the purchase price\. The total cost of your Reserved Instances won't exceed the amount that you were quoted\.

If the price rises or changes for any reason, the purchase is not completed\. If, at the time of purchase, there are offerings similar to your choice but at a lower price, AWS sells you the offerings at the lower price\.

Before you confirm your purchase, review the details of the Reserved Instance that you plan to buy, and make sure that all the parameters are accurate\. After you purchase a Reserved Instance \(either from a third\-party seller in the Reserved Instance Marketplace or from AWS\), you cannot cancel your purchase\.

**Note**  
To purchase and modify Reserved Instances, ensure that your IAM user account has the appropriate permissions, such as the ability to describe Availability Zones\. For information, see [Example Policies for Working With the AWS CLI or an AWS SDK](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExamplePolicies_EC2.html#iam-example-reservedinstances) and [Example Policies for Working in the Amazon EC2 Console](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-ec2-console.html#ex-reservedinstances)\.

**Topics**
+ [Choosing a Platform](#ri-choosing-platform)
+ [Buying Standard Reserved Instances](#ri-buying-standard)
+ [Buying Convertible Reserved Instances](#ri-buying-convertible)
+ [Viewing Your Reserved Instances](#view-reserved-instances)
+ [Using Your Reserved Instances](#reserved-instances-process)

## Choosing a Platform<a name="ri-choosing-platform"></a>

When you purchase a Reserved Instance, you must choose an offering for a *platform* that represents the operating system for your instance\.

For SUSE Linux and RHEL distributions, you must choose offerings for those specific platforms\. For all other Linux distributions \(including Ubuntu\), choose an offering for the **Linux/UNIX** platform\.

## Buying Standard Reserved Instances<a name="ri-buying-standard"></a>

You can buy Standard Reserved Instances in a specific Availability Zone and get a capacity reservation\. Alternatively, you can forego the capacity reservation and purchase a regional Standard Reserved Instance\.

**To buy Standard Reserved Instances using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**, **Purchase Reserved Instances**\.

1. For **Offering Class**, choose **Standard** to display Standard Reserved Instances\.

1. To purchase a capacity reservation, choose **Only show offerings that reserve capacity** in the top\-right corner of the purchase screen\. To purchase a regional Reserved Instance, leave the check box unselected\.

1. Select other configurations as needed and choose **Search**\.
**Note**  
To purchase a Standard Reserved Instance from the Reserved Instance Marketplace, look for **3rd Party** in the **Seller** column in the search results\. The **Term** column displays non\-standard terms\.

1. Select the Reserved Instances to purchase, enter the quantity, and choose **Add to Cart**\.

1. To see a summary of the Reserved Instances that you selected, choose **View Cart**\.

1. To complete the order, choose **Purchase**\.
**Note**  
If, at the time of purchase, there are offerings similar to your choice but with a lower price, AWS sells you the offerings at the lower price\.

1. The status of your purchase is listed in the **State** column\. When your order is complete, the **State** value changes from `payment-pending` to `active`\. When the Reserved Instance is `active`, it is ready to use\.

**Note**  
If the status goes to `retired`, AWS may not have received your payment\. 

**To buy a Standard Reserved Instance using the AWS CLI**

1. Find available Reserved Instances using the [describe\-reserved\-instances\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) command\. Specify `standard` for the `--offering-class` parameter to return only Standard Reserved Instances\. You can apply additional parameters to narrow your results; for example, if you want to purchase a regional `t2.large` Reserved Instance with a default tenancy for `Linux/UNIX` for a 1\-year term only:

   ```
   aws ec2 describe-reserved-instances-offerings --instance-type t2.large --offering-class standard --product-description "Linux/UNIX" --instance-tenancy default --filters Name=duration,Values=31536000 Name=scope,Values=Region
   ```

   ```
   {
       "ReservedInstancesOfferings": [
           {
               "OfferingClass": "standard", 
               "OfferingType": "No Upfront", 
               "ProductDescription": "Linux/UNIX", 
               "InstanceTenancy": "default", 
               "PricingDetails": [], 
               "UsagePrice": 0.0, 
               "RecurringCharges": [
                   {
                       "Amount": 0.0672, 
                       "Frequency": "Hourly"
                   }
               ], 
               "Marketplace": false, 
               "CurrencyCode": "USD", 
               "FixedPrice": 0.0, 
               "Duration": 31536000, 
               "Scope": "Region", 
               "ReservedInstancesOfferingId": "bec624df-a8cc-4aad-a72f-4f8abc34caf2", 
               "InstanceType": "t2.large"
           }, 
           {
               "OfferingClass": "standard", 
               "OfferingType": "Partial Upfront", 
               "ProductDescription": "Linux/UNIX", 
               "InstanceTenancy": "default", 
               "PricingDetails": [], 
               "UsagePrice": 0.0, 
               "RecurringCharges": [
                   {
                       "Amount": 0.032, 
                       "Frequency": "Hourly"
                   }
               ], 
               "Marketplace": false, 
               "CurrencyCode": "USD", 
               "FixedPrice": 280.0, 
               "Duration": 31536000, 
               "Scope": "Region", 
               "ReservedInstancesOfferingId": "6b15a842-3acb-4320-bd55-fa43a79f3fe3", 
               "InstanceType": "t2.large"
           }, 
           {
               "OfferingClass": "standard", 
               "OfferingType": "All Upfront", 
               "ProductDescription": "Linux/UNIX", 
               "InstanceTenancy": "default", 
               "PricingDetails": [], 
               "UsagePrice": 0.0, 
               "RecurringCharges": [], 
               "Marketplace": false, 
               "CurrencyCode": "USD", 
               "FixedPrice": 549.0, 
               "Duration": 31536000, 
               "Scope": "Region", 
               "ReservedInstancesOfferingId": "5062dc97-d284-417b-b09e-8abed1e5a183", 
               "InstanceType": "t2.large"
           }
       ]
   }
   ```

   To find Reserved Instances on the Reserved Instance Marketplace only, use the `marketplace` filter and do not specify a duration in the request, as the term may be shorter than a 1– or 3\-year term\.

   ```
   aws ec2 describe-reserved-instances-offerings --instance-type t2.large --offering-class standard --product-description "Linux/UNIX" --instance-tenancy default --filters Name=marketplace,Values=true
   ```

   When you find a Reserved Instance that meets your needs, take note of the `ReservedInstancesOfferingId`\.

1. Use the [purchase\-reserved\-instances\-offering](https://docs.aws.amazon.com/cli/latest/reference/ec2/purchase-reserved-instances-offering.html) command to buy your Reserved Instance\. You must specify the Reserved Instance offering ID you obtained the previous step and you must specify the number of instances for the reservation\.

   ```
   aws ec2 purchase-reserved-instances-offering --reserved-instances-offering-id ec06327e-dd07-46ee-9398-75b5fexample --instance-count 1
   ```

1. Use the [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command to get the status of your Reserved Instance\.

   ```
   aws ec2 describe-reserved-instances
   ```

Alternatively, use the following AWS Tools for Windows PowerShell commands:
+ [Get\-EC2ReservedInstancesOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html)
+ [New\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2ReservedInstance.html)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html)

If you already have a running instance that matches the specifications of the Reserved Instance, the billing benefit is immediately applied\. You do not have to restart your instances\. If you do not have a suitable running instance, launch an instance and ensure that you match the same criteria that you specified for your Reserved Instance\. For more information, see [Using Your Reserved Instances](#reserved-instances-process)\. 

For examples of how Reserved Instances are applied to your running instances, see [How Reserved Instances Are Applied](apply_ri.md)\.

## Buying Convertible Reserved Instances<a name="ri-buying-convertible"></a>

You can buy Convertible Reserved Instances in a specific Availability Zone and get a capacity reservation\. Alternatively, you can forego the capacity reservation and purchase a regional Convertible Reserved Instance\.

**To buy Convertible Reserved Instances using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**, **Purchase Reserved Instances**\.

1. For **Offering Class**, choose **Convertible** to display Convertible Reserved Instances\.

1. To purchase a capacity reservation, choose **Only show offerings that reserve capacity** in the top\-right corner of the purchase screen\. To purchase a regional Reserved Instance, leave the check box unselected\.

1. Select other configurations as needed and choose **Search**\.

1. Select the Convertible Reserved Instances to purchase, enter the quantity, and choose **Add to Cart**\.

1. To see a summary of your selection, choose **View Cart**\.

1. To complete the order, choose **Purchase**\.
**Note**  
If, at the time of purchase, there are offerings similar to your choice but with a lower price, AWS sells you the offerings at the lower price\.

1. The status of your purchase is listed in the **State** column\. When your order is complete, the **State** value changes from `payment-pending` to `active`\. When the Reserved Instance is `active`, it is ready to use\.

**Note**  
If the status goes to `retired`, AWS may not have received your payment\. 

**To buy a Convertible Reserved Instance using the AWS CLI**

1. Find available Reserved Instances using the [describe\-reserved\-instances\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) command\. Specify `convertible` for the `--offering-class` parameter to return only Convertible Reserved Instances\. You can apply additional parameters to narrow your results; for example, if you want to purchase a regional `t2.large` Reserved Instance with a default tenancy for `Linux/UNIX`:

   ```
   aws ec2 describe-reserved-instances-offerings --instance-type t2.large --offering-class convertible --product-description "Linux/UNIX" --instance-tenancy default --filters Name=scope,Values=Region
   ```

   ```
   {
       "ReservedInstancesOfferings": [
           {
               "OfferingClass": "convertible", 
               "OfferingType": "No Upfront", 
               "ProductDescription": "Linux/UNIX", 
               "InstanceTenancy": "default", 
               "PricingDetails": [], 
               "UsagePrice": 0.0, 
               "RecurringCharges": [
                   {
                       "Amount": 0.0556, 
                       "Frequency": "Hourly"
                   }
               ], 
               "Marketplace": false, 
               "CurrencyCode": "USD", 
               "FixedPrice": 0.0, 
               "Duration": 94608000, 
               "Scope": "Region", 
               "ReservedInstancesOfferingId": "e242e87b-b75c-4079-8e87-02d53f145204", 
               "InstanceType": "t2.large"
           }, 
           {
               "OfferingClass": "convertible", 
               "OfferingType": "Partial Upfront", 
               "ProductDescription": "Linux/UNIX", 
               "InstanceTenancy": "default", 
               "PricingDetails": [], 
               "UsagePrice": 0.0, 
               "RecurringCharges": [
                   {
                       "Amount": 0.0258, 
                       "Frequency": "Hourly"
                   }
               ], 
               "Marketplace": false, 
               "CurrencyCode": "USD", 
               "FixedPrice": 677.0, 
               "Duration": 94608000, 
               "Scope": "Region", 
               "ReservedInstancesOfferingId": "13486b92-bdd6-4b68-894c-509bcf239ccd", 
               "InstanceType": "t2.large"
           }, 
           {
               "OfferingClass": "convertible", 
               "OfferingType": "All Upfront", 
               "ProductDescription": "Linux/UNIX", 
               "InstanceTenancy": "default", 
               "PricingDetails": [], 
               "UsagePrice": 0.0, 
               "RecurringCharges": [], 
               "Marketplace": false, 
               "CurrencyCode": "USD", 
               "FixedPrice": 1327.0, 
               "Duration": 94608000, 
               "Scope": "Region", 
               "ReservedInstancesOfferingId": "e00ec34b-4674-4fb9-a0a9-213296ab93aa", 
               "InstanceType": "t2.large"
           }
       ]
   }
   ```

   When you find a Reserved Instance that meets your needs, take note of the `ReservedInstancesOfferingId`\.

1. Use the [purchase\-reserved\-instances\-offering](https://docs.aws.amazon.com/cli/latest/reference/ec2/purchase-reserved-instances-offering.html) command to buy your Reserved Instance\. You must specify the Reserved Instance offering ID you obtained the previous step and you must specify the number of instances for the reservation\.

   ```
   aws ec2 purchase-reserved-instances-offering --reserved-instances-offering-id ec06327e-dd07-46ee-9398-75b5fexample --instance-count 1
   ```

1. Use the [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command to get the status of your Reserved Instance\.

   ```
   aws ec2 describe-reserved-instances
   ```

Alternatively, use the following AWS Tools for Windows PowerShell commands:
+ [Get\-EC2ReservedInstancesOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html)
+ [New\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2ReservedInstance.html)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html)

If you already have a running instance that matches the specifications of the Reserved Instance, the billing benefit is immediately applied\. You do not have to restart your instances\. If you do not have a suitable running instance, launch an instance and ensure that you match the same criteria that you specified for your Reserved Instance\. For more information, see [Using Your Reserved Instances](#reserved-instances-process)\. 

For examples of how Reserved Instances are applied to your running instances, see [How Reserved Instances Are Applied](apply_ri.md)\.

## Viewing Your Reserved Instances<a name="view-reserved-instances"></a>

You can view the Reserved Instances you've purchased using the Amazon EC2 console, or a command line tool\.

**To view your Reserved Instances in the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Your active and retired Reserved Instances are listed\. The **State** column displays the state\. 

1. If you are a seller in the Reserved Instance Marketplace the **My Listings** tab displays the status of a reservation that's listed in the [Reserved Instance Marketplace](ri-market-general.md)\. For more information, see [Reserved Instance Listing States](ri-market-general.md#ri-listing-states)\.

**To view your Reserved Instances using the command line**
+ [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) \(AWS CLI\)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html) \(Tools for Windows PowerShell\)

## Using Your Reserved Instances<a name="reserved-instances-process"></a>

Reserved Instances are automatically applied to running On\-Demand Instances provided that the specifications match\. If you have no running On\-Demand Instances that match the specifications of your Reserved Instance, the Reserved Instance is unused until you launch an instance with the required specifications\. 

If you're launching an instance to take advantage of the billing benefit of a Reserved Instance, ensure that you specify the following information during launch:
+ Platform: You must choose an Amazon Machine Image \(AMI\) that matches the platform \(product description\) of your Reserved Instance\. For example, if you specified `Linux/UNIX`, you can launch an instance from an Amazon Linux AMI or an Ubuntu AMI\.
+ Instance type: Specify the same instance type as your Reserved Instance; for example, `t2.large`\.
+ Availability Zone: If you purchased a Reserved Instance for a specific Availability Zone, you must launch the instance into the same Availability Zone\. If you purchased a regional Reserved Instance, you can launch your instance into any Availability Zone\.
+ Tenancy: The tenancy of your instance must match the tenancy of the Reserved Instance; for example, `dedicated` or `shared`\. For more information, see [Dedicated Instances](dedicated-instance.md)\.

For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\. For examples of how Reserved Instances are applied to your running instances, see [How Reserved Instances Are Applied](apply_ri.md)\.

You can use Amazon EC2 Auto Scaling or other AWS services to launch the On\-Demand Instances that use your Reserved Instance benefits\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/WhatIsAutoScaling.html)\.
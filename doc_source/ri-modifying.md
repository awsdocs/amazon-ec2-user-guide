# Modifying Reserved Instances<a name="ri-modifying"></a>

When your computing needs change, you can modify your Standard or Convertible Reserved Instances and continue to benefit from the billing benefit\. You can modify the Availability Zone, scope, network platform, or instance size \(within the same instance type\) of your Reserved Instance\. To modify a Reserved Instance, you specify the Reserved Instances that you want to modify, and you specify one or more target configurations\.

**Note**  
You can also exchange a Convertible Reserved Instance for another Convertible Reserved Instance with a different configuration, including instance family\. For more information, see [Exchanging Convertible Reserved Instances](ri-convertible-exchange.md)\.

You can modify all or a subset of your Reserved Instances\. You can separate your original Reserved Instances into two or more new Reserved Instances\. For example, if you have a reservation for 10 instances in `us-east-1a` and decide to move 5 instances to `us-east-1b`, the modification request results in two new reservations: one for 5 instances in `us-east-1a` and the other for 5 instances in `us-east-1b`\.

You can also *merge* two or more Reserved Instances into a single Reserved Instance\. For example, if you have four `t2.small` Reserved Instances of one instance each, you can merge them to create one `t2.large` Reserved Instance\. For more information, see [Modifying the Instance Size of Your Reservations](#ri-modification-instancemove)\.

After modification, the benefit of the Reserved Instances is applied only to instances that match the new parameters\. For example, if you change the Availability Zone of a reservation, the capacity reservation and pricing benefits are automatically applied to instance usage in the new Availability Zone\. Instances that no longer match the new parameters are charged at the On\-Demand rate unless your account has other applicable reservations\. 

If your modification request succeeds:
+ The modified reservation becomes effective immediately and the pricing benefit is applied to the new instances beginning at the hour of the modification request\. For example, if you successfully modify your reservations at 9:15PM, the pricing benefit transfers to your new instance at 9:00PM\. \(You can get the `effective date` of the modified Reserved Instances by using the [DescribeReservedInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeReservedInstances.html) API action or the [describe\-reserved\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) command \(AWS CLI\)\.
+ The original reservation is retired\. Its end date is the start date of the new reservation, and the end date of the new reservation is the same as the end date of the original Reserved Instance\. If you modify a three\-year reservation that had 16 months left in its term, the resulting modified reservation is a 16\-month reservation with the same end date as the original one\.
+ The modified reservation lists a $0 fixed price and not the fixed price of the original reservation\.

**Note**  
The fixed price of the modified reservation does not affect the discount pricing tier calculations applied to your account, which are based on the fixed price of the original reservation\. 

If your modification request fails, your Reserved Instances maintain their original configuration, and are immediately available for another modification request\.

There is no fee for modification, and you do not receive any new bills or invoices\.

You can modify your reservations as frequently as you like, but you cannot change or cancel a pending modification request after you submit it\. After the modification has completed successfully, you can submit another modification request to roll back any changes you made, if needed\.

**Topics**
+ [Requirements and Restrictions for Modification](#ri-modification-limits)
+ [Modifying the Instance Size of Your Reservations](#ri-modification-instancemove)
+ [Submitting Modification Requests](#ri-modification-process)
+ [Troubleshooting Modification Requests](#ri-modification-process-messages)

## Requirements and Restrictions for Modification<a name="ri-modification-limits"></a>

Not all attributes of a Reserved Instance can be modified, and restrictions may apply\.


| Modifiable attribute | Supported platforms | Limitations | 
| --- | --- | --- | 
|  Change **Availability Zones** within the same region  |  Linux and Windows  | \- | 
|  Change the **scope** from Availability Zone to Region and vice versa  |  Linux and Windows  |  If you change the scope from Availability Zone to region, you lose the capacity reservation benefit\. If you change the scope from region to Availability Zone, you lose Availability Zone flexibility and instance size flexibility \(if applicable\)\. For more information, see [How Reserved Instances Are Applied](apply_ri.md)\.  | 
|  Change the **network platform** between EC2\-VPC and EC2\-Classic  |  Linux and Windows  |  Only applicable if your account supports EC2\-Classic\.  | 
|  Change the **instance size** within the same instance type  |  Linux only  |  Some instance types are not supported, because there are no other sizes available\. For more information, see [Modifying the Instance Size of Your Reservations](#ri-modification-instancemove)\.  | 

Amazon EC2 processes your modification request if there is sufficient capacity for your target configuration \(if applicable\), and if the following conditions are met\.

The Reserved Instances that you want to modify must be:
+ Active
+ Not pending another modification request
+ Not listed in the Reserved Instance Marketplace
**Note**  
To modify your Reserved Instances that are listed in the Reserved Instance Marketplace, cancel the listing, request modification, and then list them again\. 
+ Terminating in the same hour \(but not minutes or seconds\)
+ Already purchased by you \(you cannot modify an offering before or at the same time that you purchase it\)

Your modification request must meet the following conditions:
+ There must be a match between the instance size footprint of the active reservation and the target configuration\. For more information, see [Modifying the Instance Size of Your Reservations](#ri-modification-instancemove)\.
+ The input Reserved Instances must be either Standard Reserved Instances or Convertible Reserved Instances, but not a combination of both\.

## Modifying the Instance Size of Your Reservations<a name="ri-modification-instancemove"></a>

If you have Amazon Linux reservations in an instance type with multiple sizes, you can modify the instance size of your Reserved Instances\. 

**Note**  
Instances are grouped by family \(based on storage, or CPU capacity\); type \(designed for specific use cases\); and size\. For example, the `c4` instance type is in the Compute optimized instance family and is available in multiple sizes\. While `c3` instances are in the same family, you can't modify `c4` instances into `c3` instances because they have different hardware specifications\. For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

You cannot modify the instance size of the Reserved Instances for the following instance types, because only one size is available for each of these instance types\.
+ `cc2.8xlarge`
+ `cr1.8xlarge`
+ `hs1.8xlarge`
+ `i3.metal`
+ `t1.micro`

Each Reserved Instance has an *instance size footprint*, which is determined by the normalization factor of the instance type and the number of instances in the reservation\. When you modify a Reserved Instance, the footprint of the target configuration must match that of the original configuration, otherwise the modification request is not processed\.

The normalization factor is based on instance size within the instance type \(for example, `m1.xlarge` instances within the `m1` instance type\)\. This is only meaningful within the same instance type\. Instance types cannot be modified from one type to another\. In the Amazon EC2 console, this is measured in units\. The following table illustrates the normalization factor that applies within an instance type\.


| Instance size | Normalization factor | 
| --- | --- | 
|  nano  |  0\.25  | 
|  micro  |  0\.5  | 
|  small  |  1  | 
|  medium  |  2  | 
|  large  |  4  | 
|  xlarge  |  8  | 
|  2xlarge  |  16  | 
|  4xlarge  |  32  | 
|  8xlarge  |  64  | 
|  9xlarge  |  72  | 
|  10xlarge  |  80  | 
|  12xlarge  |  96  | 
|  16xlarge  |  128  | 
|  18xlarge  |  144  | 
|  24xlarge  |  192  | 
|  32xlarge  |  256  | 

To calculate the instance size footprint of a Reserved Instance, multiply the number of instances by the normalization factor\. For example, an `t2.medium` has a normalization factor of 2 so a reservation for four `t2.medium` instances has a footprint of 8 units\.

You can allocate your reservations into different instance sizes across the same instance type as long as the instance size footprint of your reservation remains the same\. For example, you can divide a reservation for one `t2.large` \(1 x 4\) instance into four `t2.small` \(4 x 1\) instances, or you can combine a reservation for four `t2.small` instances into one `t2.large` instance\. However, you cannot change your reservation for two `t2.small` \(2 x 1\) instances into one `t2.large` \(1 x 4\) instance\. This is because the existing instance size footprint of your current reservation is smaller than the proposed reservation\. 

In the following example, you have a reservation with two `t2.micro` instances \(giving you a footprint of 1\) and a reservation with one `t2.small` instance \(giving you a footprint of 1\)\. You merge both reservations to a single reservation with one `t2.medium` instance—the combined instance size footprint of the two original reservations equals the footprint of the modified reservation\.

![\[Modifying Reserved Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-modify-merge.png)

You can also modify a reservation to divide it into two or more reservations\. In the following example, you have a reservation with a `t2.medium` instance\. You divide the reservation into a reservation with two `t2.nano` instances and a reservation with three `t2.micro` instances\.

![\[Modifying Reserved Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-modify-divide.png)

## Submitting Modification Requests<a name="ri-modification-process"></a>

You can modify your Reserved Instances using the Amazon EC2 console, the Amazon EC2 API, or a command line tool\.

### Amazon EC2 Console<a name="ri-modification-process-console"></a>

Before you modify your Reserved Instances, ensure that you have read the applicable [restrictions](#ri-modification-limits)\. If you are modifying instance size, ensure that you've calculated the total [instance size footprint](#ri-modification-instancemove) of the reservations that you want to modify and ensure that it matches the total instance size footprint of your target configurations\.

**To modify your Reserved Instances using the AWS Management Console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Reserved Instances** page, select one or more Reserved Instances to modify, and choose **Modify Reserved Instances**\.
**Note**  
If your Reserved Instances are not in the active state or cannot be modified, **Modify Reserved Instances** is disabled\.

1. The first entry in the modification table displays attributes of selected Reserved Instances, and at least one target configuration beneath it\. The **Units** column displays the total instance size footprint\. Choose **Add** for each new configuration to add\. Modify the attributes as needed for each configuration, and choose **Continue** when you're done:
   + **Network**: Choose whether the Reserved Instance applies to EC2\-Classic or EC2\-VPC\. This option is only available if your account supports EC2\-Classic\.
   + **Scope**: Choose whether the Reserved Instance applies to an Availability Zone or to the whole region\.
   + **Availability Zone**: Choose the required Availability Zone\. Not applicable for regional Reserved Instances\.
   + **Instance Type**: Select the required instance type\. Only available for supported platforms\. For more information, see [Requirements and Restrictions for Modification](#ri-modification-limits)\.
   + **Count**: Specify the number of instances to be covered by the reservation\.
**Note**  
If your combined target configurations are larger or smaller than the instance size footprint of your original Reserved Instances, the allocated total in the **Units** column displays in red\.

1. To confirm your modification choices when you finish specifying your target configurations, choose **Submit Modifications**\. If you change your mind at any point, choose **Cancel** to exit the wizard\.

You can determine the status of your modification request by looking at the **State** column in the Reserved Instances screen\. The following table illustrates the possible **State** values\.


| State | Description | 
| --- | --- | 
|  **active* \(pending modification\)***  |  Transition state for original Reserved Instances\.   | 
|  **retired* \(pending modification\)***  |  Transition state for original Reserved Instances while new Reserved Instances are being created\.   | 
|  **retired**  |  Reserved Instances successfully modified and replaced\.  | 
|  **active**  |  New Reserved Instances created from a successful modification request\. \-Or\- Original Reserved Instances after a failed modification request\.  | 

### Amazon EC2 API or Command Line Tool<a name="ri-modification-process-CLI"></a>

To modify your Reserved Instances, you can use one of the following:
+ [modify\-reserved\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-reserved-instances.html) \(AWS CLI\)
+ [Edit\-EC2ReservedInstance](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2ReservedInstance.html) \(AWS Tools for Windows PowerShell\)
+ [ModifyReservedInstances](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyReservedInstances.html) \(Amazon EC2 API\)

To get the status of your modification, use one of the following:
+ [describe\-reserved\-instances\-modifications](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-modifications.html) \(AWS CLI\)
+  [Get\-EC2ReservedInstancesModifications](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesModifications.html) \(AWS Tools for Windows PowerShell\)
+ [DescribeReservedInstancesModifications](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeReservedInstancesModifications.html) \(Amazon EC2 API\)

The state returned shows your request as `processing`, `fulfilled`, or `failed`\.

## Troubleshooting Modification Requests<a name="ri-modification-process-messages"></a>

If the target configuration settings that you requested were unique, you receive a message that your request is being processed\. At this point, Amazon EC2 has only determined that the parameters of your modification request are valid\. Your modification request can still fail during processing due to unavailable capacity\.

In some situations, you might get a message indicating incomplete or failed modification requests instead of a confirmation\. Use the information in such messages as a starting point for resubmitting another modification request\. Ensure that you have read the applicable [restrictions](#ri-modification-limits) before submitting the request\.

**Not all selected Reserved Instances can be processed for modification**  
Amazon EC2 identifies and lists the Reserved Instances that cannot be modified\. If you receive a message like this, go to the **Reserved Instances** page in the Amazon EC2 console and check the information for the Reserved Instances\.

**Error in processing your modification request**  
You submitted one or more Reserved Instances for modification and none of your requests can be processed\. Depending on the number of reservations you are modifying, you can get different versions of the message\. 

Amazon EC2 displays the reasons why your request cannot be processed\. For example, you might have specified the same target configuration—a combination of Availability Zone and platform—for one or more subsets of the Reserved Instances you are modifying\. Try submitting the modification requests again, but ensure that the instance details of the reservations match, and that the target configurations for all subsets being modified are unique\.
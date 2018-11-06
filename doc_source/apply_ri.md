# How Reserved Instances Are Applied<a name="apply_ri"></a>

If you purchase a Reserved Instance and you already have a running instance that matches the specifications of the Reserved Instance, the billing benefit is immediately applied\. You do not have to restart your instances\. If you do not have an eligible running instance, launch an instance and ensure that you match the same criteria that you specified for your Reserved Instance\. For more information, see [Using Your Reserved Instances](ri-market-concepts-buying.md#reserved-instances-process)\. 

Reserved Instances apply to usage in the same manner, irrespective of the offering type \(Standard or Convertible\), and are automatically applied to running On\-Demand Instances with matching attributes\.

## How Zonal Reserved Instances Are Applied<a name="apply-zonal-ri"></a>

Reserved Instances assigned to a specific Availability Zone provide the Reserved Instance discount to matching instance usage in that Availability Zone\. For example, if you purchase two `c4.xlarge` default tenancy Linux/Unix Standard Reserved Instances in Availability Zone us\-east\-1a, then up to two `c4.xlarge` default tenancy Linux/Unix instances running in the Availability Zone us\-east\-1a can benefit from the Reserved Instance discount\. The attributes \(tenancy, platform, Availability Zone, instance type, and instance size\) of the running instances must match that of the Reserved Instances\.

## How Regional Reserved Instances Are Applied<a name="apply-regional-ri"></a>

Reserved Instances purchased for a Region \(regional Reserved Instances\) provide Availability Zone flexibility—the Reserved Instance discount applies to instance usage in any Availability Zone in that Region\. 

Regional Reserved Instances on the Linux/Unix platform with default tenancy also provide instance size flexibility, where the Reserved Instance discount applies to instance usage within that instance type, regardless of size\.

**Note**  
Instance size flexibility does not apply to Reserved Instances that are purchased for a specific Availability Zone, bare metal instances, Reserved Instances with dedicated tenancy, and Reserved Instances for Windows, Windows with SQL Standard, Windows with SQL Server Enterprise, Windows with SQL Server Web, RHEL, and SLES\.

Instance size flexibility is determined by the normalization factor of the instance size\. The discount applies either fully or partially to running instances of the same instance type, depending on the instance size of the reservation, in any Availability Zone in the Region\. The only attributes that must be matched are the instance type, tenancy, and platform\.

Instance size flexibility is applied from the smallest to the largest instance size within the instance family based on the normalization factor\. 

The table below describes the different sizes within an instance type, and corresponding normalization factor per hour\. This scale is used to apply the discounted rate of Reserved Instances to the normalized usage of the instance type\.


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

For example, a `t2.medium` instance has a normalization factor of 2\. If you purchase a `t2.medium` default tenancy Amazon Linux/Unix Reserved Instance in the US East \(N\. Virginia\) and you have two running `t2.small` instances in your account in that Region, the billing benefit is applied in full to both instances\. 

![\[Applying a Regional Reserved Instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-instance-flex-full.png)

Or, if you have one `t2.large` instance running in your account in the US East \(N\. Virginia\) Region, the billing benefit is applied to 50% of the usage of the instance\.

![\[Applying a Regional Reserved Instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-instance-flex-partial.png)

**Note**  
The normalization factor is also applied when modifying Reserved Instances\. For more information, see [Modifying Reserved Instances](ri-modifying.md)\.

## Examples of Applying Reserved Instances<a name="ri-usage-examples"></a>

The following scenarios cover the ways in which Reserved Instances are applied\.

**Example Scenario 1: Reserved Instances in a Single Account**  
You are running the following On\-Demand Instances in account A:  
+ 4 x `m3.large` Linux, default tenancy instances in Availability Zone us\-east\-1a
+ 2 x `m4.xlarge` Amazon Linux, default tenancy instances in Availability Zone us\-east\-1b
+ 1 x `c4.xlarge` Amazon Linux, default tenancy instances in Availability Zone us\-east\-1c
You purchase the following Reserved Instances in account A:  
+ 4 x `m3.large` Linux, default tenancy Reserved Instances in Availability Zone us\-east\-1a \(capacity is reserved\)
+ 4 x `m4.large` Amazon Linux, default tenancy Reserved Instances in Region us\-east\-1
+ 1 x `c4.large` Amazon Linux, default tenancy Reserved Instances in Region us\-east\-1
The Reserved Instance benefits are applied in the following way:  
+ The discount and capacity reservation of the four `m3.large` zonal Reserved Instances is used by the four `m3.large` instances because the attributes \(instance size, Region, platform, tenancy\) between them match\.
+ The `m4.large` regional Reserved Instances provide Availability Zone and instance size flexibility, because they are regional Amazon Linux Reserved Instances with default tenancy\.

  An `m4.large` is equivalent to 4 normalized units/hour\.

  You've purchased four `m4.large` regional Reserved Instances, and in total, they are equal to 16 normalized units/hour \(4x4\)\. Account A has two `m4.xlarge` instances running, which is equivalent to 16 normalized units/hour \(2x8\)\. In this case, the four `m4.large` regional Reserved Instances provide the billing benefit to an entire hour of usage of the two `m4.xlarge` instances\.
+ The `c4.large` regional Reserved Instance in us\-east\-1 provides Availability Zone and instance size flexibility, because it is a regional Amazon Linux Reserved Instance with default tenancy, and applies to the `c4.xlarge` instance\. A `c4.large` instance is equivalent to 4 normalized units/hour and a `c4.xlarge` is equivalent to 8 normalized units/hour\.

  In this case, the `c4.large` regional Reserved Instance provides partial benefit to `c4.xlarge` usage\. This is because the `c4.large` Reserved Instance is equivalent to 4 normalized units/hour of usage, but the `c4.xlarge` instance requires 8 normalized units/hour\. Therefore, the `c4.large` Reserved Instance billing discount applies to 50% of `c4.xlarge` usage\. The remaining `c4.xlarge` usage is charged at the On\-Demand rate\.

**Example Scenario 2: Regional Reserved Instances in Linked Accounts**  
Reserved Instances are first applied to usage within the purchasing account, followed by qualifying usage in any other account in the organization\. For more information, see [Reserved Instances and Consolidated Billing](concepts-reserved-instances-application.md#concepts-reserved-instances-billing)\. For regional Reserved Instances that offer instance size flexibility, the benefit is applied from the smallest to the largest instance size within the instance family\.  
You're running the following On\-Demand Instances in account A \(the purchasing account\):  
+ 2 x `m4.xlarge` Linux, default tenancy instances in Availability Zone us\-east\-1a
+ 1 x `m4.2xlarge` Linux, default tenancy instances in Availability Zone us\-east\-1b
+ 2 x `c4.xlarge` Linux, default tenancy instances in Availability Zone us\-east\-1a
+ 1x `c4.2xlarge` Linux, default tenancy instances in Availability Zone us\-east\-1b
Another customer is running the following On\-Demand Instances in account B—a linked account:  
+ 2 x `m4.xlarge` Linux, default tenancy instances in Availability Zone us\-east\-1a
You purchase the following regional Reserved Instances in account A:  
+ 4 x `m4.xlarge` Linux, default tenancy Reserved Instances in Region us\-east\-1
+ 2 x `c4.xlarge` Linux, default tenancy Reserved Instances in Region us\-east\-1
The regional Reserved Instance benefits are applied in the following way:  
+ The discount of the four `m4.xlarge` Reserved Instances is used by the two `m4.xlarge` instances in account A and the `m4.2xlarge` instance in account A\. All three instances match the attributes \(instance family, Region, platform, tenancy\)\. There is no capacity reservation\.
+ The discount of the two `c4.xlarge` Reserved Instances applies to the two `c4.xlarge` instances, because they are a smaller instance size than the `c4.2xlarge` instance\. There is no capacity reservation\.

**Example Scenario 3: Zonal Reserved Instances in a Linked Account**  
In general, Reserved Instances that are owned by an account are applied first to usage in that account\. However, if there are qualifying, unused Reserved Instances for a specific Availability Zone \(zonal Reserved Instances\) in other accounts in the organization, they are applied to the account before regional Reserved Instances owned by the account\. This is done to ensure maximum Reserved Instance utilization and a lower bill\. For billing purposes, all the accounts in the organization are treated as one account\. The following example may help explain this\.  
You're running the following On\-Demand Instance in account A \(the purchasing account\):  
+ 1 x `m4.xlarge` Linux, default tenancy instance in Availability Zone us\-east\-1a
A customer is running the following On\-Demand Instance in linked account B:  
+ 1 x `m4.xlarge` Linux, default tenancy instance in Availability Zone us\-east\-1b
You purchase the following regional Reserved Instances in account A:  
+ 1 x `m4.xlarge` Linux, default tenancy Reserved Instance in Region us\-east\-1
A customer also purchases the following zonal Reserved Instances in linked account C:  
+ 1 x `m4.xlarge` Linux, default tenancy Reserved Instances in Availability Zone us\-east\-1a
The Reserved Instance benefits are applied in the following way:  
+ The discount of the `m4.xlarge` zonal Reserved Instance owned by account C is applied to the `m4.xlarge` usage in account A\.
+ The discount of the `m4.xlarge` regional Reserved Instance owned by account A is applied to the `m4.xlarge` usage in account B\.
+ If the regional Reserved Instance owned by account A was first applied to the usage in account A, the zonal Reserved Instance owned by account C remains unused and usage in account B is charged at On\-Demand rates\.

For more information, see [Reserved Instances in the Billing and Cost Management Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports.html#enhanced-RI)\.
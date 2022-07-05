# Regional and zonal Reserved Instances \(scope\)<a name="reserved-instances-scope"></a>

When you purchase a Reserved Instance, you determine the scope of the Reserved Instance\. The scope is either regional or zonal\. 
+ **Regional**: When you purchase a Reserved Instance for a Region, it's referred to as a *regional* Reserved Instance\.
+ **Zonal**: When you purchase a Reserved Instance for a specific Availability Zone, it's referred to as a *zonal* Reserved Instance\.

The scope does not affect the price\. You pay the same price for a regional or zonal Reserved Instance\. For more information about Reserved Instance pricing, see [Key variables that determine Reserved Instance pricing](ec2-reserved-instances.md#ri-key-pricing-variables) and [Amazon EC2 Reserved Instances Pricing](http://aws.amazon.com/ec2/pricing/reserved-instances/pricing/)\.

For more information about how to specify the scope of a Reserved Instance, see [RI Attributes](http://aws.amazon.com/ec2/pricing/reserved-instances/#riattributes), specifically the **Availability Zone** bullet\.

## Differences between regional and zonal Reserved Instances<a name="reserved-instances-regional-zonal-differences"></a>

The following table highlights some key differences between regional Reserved Instances and zonal Reserved Instances:


|  | Regional Reserved Instances | Zonal Reserved Instances | 
| --- | --- | --- | 
|  Ability to reserve capacity  |  A regional Reserved Instance does *not* reserve capacity\.  |  A zonal Reserved Instance reserves capacity in the specified Availability Zone\.  | 
|  Availability Zone flexibility  |  The Reserved Instance discount applies to instance usage in any Availability Zone in the specified Region\.  |  No Availability Zone flexibility—the Reserved Instance discount applies to instance usage in the specified Availability Zone only\.  | 
|  Instance size flexibility  |  The Reserved Instance discount applies to instance usage within the instance family, regardless of size\. Only supported on Amazon Linux/Unix Reserved Instances with default tenancy\. For more information, see [Instance size flexibility determined by normalization factor](apply_ri.md#ri-normalization-factor)\. |  No instance size flexibility—the Reserved Instance discount applies to instance usage for the specified instance type and size only\.  | 
|  Queuing a purchase  |  You can queue purchases for regional Reserved Instances\.  |  You can't queue purchases for zonal Reserved Instances\.  | 

For more information and examples, see [How Reserved Instances are applied](apply_ri.md)\.
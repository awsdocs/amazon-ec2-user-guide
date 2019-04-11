# Regional and Zonal Reserved Instances \(Scope\)<a name="reserved-instances-scope"></a>

When you purchase a Reserved Instance, you determine the scope of the Reserved Instance\. The scope is either regional or zonal\. 
+ **Regional**: When you purchase a Reserved Instance for a Region, it's referred to as a *regional* Reserved Instance\.
+ **Zonal**: When you purchase a Reserved Instance for a specific Availability Zone, it's referred to as a *zonal* Reserved Instance\.

## Differences between Regional and Zonal Reserved Instances<a name="reserved-instances-regional-zonal-differences"></a>

The following table highlights some key differences between regional Reserved Instances and zonal Reserved Instances:


|  | Regional Reserved Instances | Zonal Reserved Instances | 
| --- | --- | --- | 
| Availability Zone flexibility | The Reserved Instance discount applies to instance usage in any Availability Zone in the specified Region\. | No Availability Zone flexibility—the Reserved Instance discount applies to instance usage in the specified Availability Zone only\. | 
| Capacity reservation | No capacity reservation—a regional Reserved Instance does not provide a capacity reservation\.  | A zonal Reserved Instance provides a capacity reservation in the specified Availability Zone\. | 
| Instance size flexibility | The Reserved Instance discount applies to instance usage within the instance family, regardless of size\. Only supported on Amazon Linux/Unix Reserved Instances with default tenancy\. For more information, see [Instance Size Flexibility Determined by Normalization Factor](apply_ri.md#ri-normalization-factor)\. | No instance size flexibility—the Reserved Instance discount applies to instance usage for the specified instance type and size only\. | 

For more information and examples, see [How Reserved Instances Are Applied](apply_ri.md)\.
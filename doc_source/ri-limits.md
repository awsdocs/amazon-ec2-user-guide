# Reserved Instance quotas<a name="ri-limits"></a>

You can purchase new Reserved Instances each month\. The number of new Reserved Instances that you can purchase each month is determined by your monthly quota, as follows:


****  

| Quota description | Default quota | 
| --- | --- | 
|  New [regional](apply_ri.md#apply-regional-ri) Reserved Instances  | 20 per Region per month | 
|  New [zonal](apply_ri.md#apply-zonal-ri) Reserved Instances  | 20 per Availability Zone per month | 

For example, in a Region with three Availability Zones, the default quota is 80 new Reserved Instances per month, calculated as follows:
+ 20 regional Reserved Instances for the Region
+ Plus 60 zonal Reserved Instances \(20 for each of the three Availability Zones\)

## View the number of Reserved Instances you have purchased<a name="view-number-of-purchased-reserved-instances"></a>

The number of Reserved Instances that you purchase is indicated by the **Instance count** field \(console\) or the `InstanceCount` parameter \(AWS CLI\)\. When you purchase new Reserved Instances, the quota is measured against the total instance count\. For example, if you purchase a single Reserved Instance configuration with an instance count of 10, the purchase counts towards your quota as 10, not 1\.

You can view how many Reserved Instances you have purchased by using the Amazon EC2 or the AWS CLI\.

------
#### [ Console ]

**To view the number of Reserved Instances you have purchased**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Reserved Instances**\.

1. Select a Reserved Instance configuration from the table, and check the **Instance count** field\.

   In the following screenshot, the selected line represents a single Reserved Instance configuration for a `t3.micro` instance type\. The **Instance count** column in the table view and the **Instance count** field in the detail view \(outlined in the screenshot\) indicate that there are 10 Reserved Instances for this configuration\.  
![\[This image shows the Reserved Instances screen in the Amazon EC2 console. The Instance count field is outlined in the screenshot.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-instance-count.png)

------
#### [ AWS CLI ]

**To view the number of Reserved Instances you have purchased**  
Use the [ describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) CLI command and specify the ID of the Reserved Instance configuration\.

```
aws ec2 describe-reserved-instances \
    --reserved-instances-ids 2fbf16dd-98b6-4a3a-955f-83f87790f04b \
    --output table
```

Example output – The `InstanceCount` field indicates that there are 10 Reserved Instances for this configuration\.

```
-------------------------------------------------------------------
|                    DescribeReservedInstances                    |
+-----------------------------------------------------------------+
||                       ReservedInstances                       ||
|+----------------------+----------------------------------------+|
||  CurrencyCode        |  USD                                   ||
||  Duration            |  31536000                              ||
||  End                 |  2023-08-27T13:29:44+00:00             ||
||  FixedPrice          |  59.0                                  ||
||  InstanceCount       |  10                                    ||
||  InstanceTenancy     |  default                               ||
||  InstanceType        |  t3.micro                              ||
||  OfferingClass       |  standard                              ||
||  OfferingType        |  All Upfront                           ||
||  ProductDescription  |  Linux/UNIX                            ||
||  ReservedInstancesId |  2fbf16dd-98b6-4a3a-955f-83f87790f04b  ||
||  Scope               |  Region                                ||
||  Start               |  2022-08-27T13:29:45.938000+00:00      ||
||  State               |  active                                ||
||  UsagePrice          |  0.0                                   ||
|+----------------------+----------------------------------------+|
|||                      RecurringCharges                       |||
||+----------------------------------+--------------------------+||
|||  Amount                          |  0.0                     |||
|||  Frequency                       |  Hourly                  |||
||+----------------------------------+--------------------------+||
```

------

## Considerations<a name="ri-quota-considerations"></a>

A regional Reserved Instance applies a discount to a running On\-Demand Instance\. The default On\-Demand Instance limit is 20\. You cannot exceed your running On\-Demand Instance limit by purchasing regional Reserved Instances\. For example, if you already have 20 running On\-Demand Instances, and you purchase 20 regional Reserved Instances, the 20 regional Reserved Instances are used to apply a discount to the 20 running On\-Demand Instances\. If you purchase more regional Reserved Instances, you will not be able to launch more instances because you have reached your On\-Demand Instance limit\.

Before purchasing regional Reserved Instances, make sure your On\-Demand Instance limit matches or exceeds the number of regional Reserved Instances you intend to own\. If required, make sure you request an increase to your On\-Demand Instance limit *before* purchasing more regional Reserved Instances\.

A zonal Reserved Instance—a Reserved Instance that is purchased for a specific Availability Zone—provides a capacity reservation as well as a discount\. You *can exceed* your running On\-Demand Instance limit by purchasing zonal Reserved Instances\. For example, if you already have 20 running On\-Demand Instances, and you purchase 20 zonal Reserved Instances, you can launch a further 20 On\-Demand Instances that match the specifications of your zonal Reserved Instances, giving you a total of 40 running instances\.

## View your Reserved Instance quotas and request a quota increase<a name="view-ri-quotas"></a>

The Amazon EC2 console provides quota information\. You can also request an increase in your quotas\. For more information, see [View your current limits](ec2-resource-limits.md#view-limits) and [Request an increase](ec2-resource-limits.md#request-increase)\.
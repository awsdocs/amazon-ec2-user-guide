# Reserved Instances<a name="ec2-reserved-instances"></a>

Reserved Instances provide you with significant savings on your Amazon EC2 costs compared to On\-Demand Instance pricing\. Reserved Instances are not physical instances, but rather a billing discount applied to the use of On\-Demand Instances in your account\. These On\-Demand Instances must match certain attributes, such as instance type and Region, in order to benefit from the billing discount\.

Savings Plans also offer significant savings on your Amazon EC2 costs compared to On\-Demand Instance pricing\. With Savings Plans, you make a commitment to a consistent usage amount, measured in USD per hour\. This provides you with the flexibility to use the instance configurations that best meet your needs and continue to save money, instead of making a commitment to a specific instance configuration\. For more information, see the [AWS Savings Plans User Guide](https://docs.aws.amazon.com/savingsplans/latest/userguide/)\.

## Reserved Instance Overview<a name="ri-overview"></a>

The following diagram shows a basic overview of purchasing and using Reserved Instances\.

![\[Purchasing Reserved Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-basics.png)

In this scenario, you have a running On\-Demand Instance \(T2\) in your account, for which you're currently paying On\-Demand rates\. You purchase a Reserved Instance that matches the attributes of your running instance, and the billing benefit is immediately applied\. Next, you purchase a Reserved Instance for a C4 instance\. You do not have any running instances in your account that match the attributes of this Reserved Instance\. In the final step, you launch an instance that matches the attributes of the C4 Reserved Instance, and the billing benefit is immediately applied\.

## Key Variables That Determine Reserved Instance Pricing<a name="ri-key-pricing-variables"></a>

The Reserved Instance pricing is determined by the following key variables\.

### Instance Attributes<a name="ri-pricing-variable-instance-attributes"></a>

A Reserved Instance has four instance attributes that determine its price\. The attributes also determine how the Reserved Instance is applied to a running instance in your account\.
+ **Instance type**: For example, `m4.large`\. This is composed of the instance family \(`m4`\) and the instance size \(`large`\)\.
+ **Region**: The Region in which the Reserved Instance is purchased\.
+ **Tenancy**: Whether your instance runs on shared \(default\) or single\-tenant \(dedicated\) hardware\. For more information, see [Dedicated Instances](dedicated-instance.md)\. 
+ **Platform**: The operating system; for example, Windows or Linux/Unix\. For more information, see [Choosing a Platform](ri-market-concepts-buying.md#ri-choosing-platform)\.

Reserved Instances do not renew automatically; when they expire, you can continue using the EC2 instance without interruption, but you are charged On\-Demand rates\. In the above example, when the Reserved Instances that cover the T2 and C4 instances expire, you go back to paying the On\-Demand rates until you terminate the instances or purchase new Reserved Instances that match the instance attributes\.

### Term Commitment<a name="ri-pricing-variable-term-commitment"></a>

You can purchase a Reserved Instance for a one\-year or three\-year commitment, with the three\-year commitment offering a bigger discount\.
+ **One\-year**: A year is defined as 31536000 seconds \(365 days\)\. 
+ **Three\-year**: Three years is defined as 94608000 seconds \(1095 days\)\.

### Payment Options<a name="ri-payment-options"></a>

The following payment options are available for Reserved Instances:
+ **All Upfront**: Full payment is made at the start of the term, with no other costs or additional hourly charges incurred for the remainder of the term, regardless of hours used\.
+ **Partial Upfront**: A portion of the cost must be paid upfront and the remaining hours in the term are billed at a discounted hourly rate, regardless of whether the Reserved Instance is being used\.
+ **No Upfront**: You are billed a discounted hourly rate for every hour within the term, regardless of whether the Reserved Instance is being used\. No upfront payment is required\.
**Note**  
No Upfront Reserved Instances are based on a contractual obligation to pay monthly for the entire term of the reservation\. For this reason, a successful billing history is required before you can purchase No Upfront Reserved Instances\.

Generally speaking, you can save more money making a higher upfront payment for Reserved Instances\. You can also find Reserved Instances offered by third\-party sellers at lower prices and shorter term lengths on the Reserved Instance Marketplace\. For more information, see [Reserved Instance Marketplace](ri-market-general.md)\. 

### Offering Class<a name="ri-pricing-variable-offering-class"></a>

If your computing needs change, you may be able to modify or exchange your Reserved Instance, depending on the offering class\.
+ **Standard**: These provide the most significant discount, but can only be modified\.
+ **Convertible**: These provide a lower discount than Standard Reserved Instances, but can be exchanged for another Convertible Reserved Instance with different instance attributes\. Convertible Reserved Instances can also be modified\.

For more information, see [Types of Reserved Instances \(Offering Classes\)](reserved-instances-types.md)\.

After you purchase a Reserved Instance, you cannot cancel your purchase\. However, you may be able to [modify](ri-modifying.md), [exchange](ri-convertible-exchange.md), or [sell](ri-market-general.md) your Reserved Instance if your needs change\.

For more information about pricing, see [Amazon EC2 Reserved Instances Pricing](https://aws.amazon.com/ec2/pricing/reserved-instances/pricing/)\.

## Reserved Instance Limits<a name="ri-limits"></a>

There is a limit to the number of Reserved Instances that you can purchase per month\. For each Region you can purchase 20 [regional](apply_ri.md#apply-regional-ri) Reserved Instances per month plus an additional 20 [zonal](apply_ri.md#apply-zonal-ri) Reserved Instances per month for each Availability Zone\.

For example, in a Region with three Availability Zones, the limit is 80 Reserved Instances per month: 20 regional Reserved Instances for the Region plus 20 zonal Reserved Instances for each of the three Availability Zones \(20x3=60\)\.

A regional Reserved Instance applies a discount to a running On\-Demand Instance\. The default On\-Demand Instance limit is 20\. You cannot exceed your running On\-Demand Instance limit by purchasing regional Reserved Instances\. For example, if you already have 20 running On\-Demand Instances, and you purchase 20 regional Reserved Instances, the 20 regional Reserved Instances are used to apply a discount to the 20 running On\-Demand Instances\. If you purchase more regional Reserved Instances, you will not be able to launch more instances because you have reached your On\-Demand Instance limit\.

Before purchasing regional Reserved Instances, make sure your On\-Demand Instance limit matches or exceeds the number of regional Reserved Instances you intend to own\. If required, make sure you request an increase to your On\-Demand Instance limit *before* purchasing more regional Reserved Instances\.

A zonal Reserved Instance—a Reserved Instance that is purchased for a specific Availability Zone— provides capacity reservation as well as a discount\. You *can exceed* your running On\-Demand Instance limit by purchasing zonal Reserved Instances\. For example, if you already have 20 running On\-Demand Instances, and you purchase 20 zonal Reserved Instances, you can launch a further 20 On\-Demand Instances that match the specifications of your zonal Reserved Instances, giving you a total of 40 running instances\.

The Amazon EC2 console provides limit information\. For more information, see [Viewing Your Current Limits](ec2-resource-limits.md#view-limits)\.
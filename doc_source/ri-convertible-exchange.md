# Exchange Convertible Reserved Instances<a name="ri-convertible-exchange"></a>

You can exchange one or more Convertible Reserved Instances for another Convertible Reserved Instance with a different configuration, including instance family, operating system, and tenancy\. There are no limits to how many times you perform an exchange, as long as the new Convertible Reserved Instance is of an equal or higher value than the original Convertible Reserved Instances that you are exchanging\.

When you exchange your Convertible Reserved Instance, the number of instances for your current reservation is exchanged for a number of instances that cover the equal or higher value of the configuration of the new Convertible Reserved Instance\. Amazon EC2 calculates the number of Reserved Instances that you can receive as a result of the exchange\.

You can't exchange Standard Reserved Instances, but you can modify them\. For more information, see [Modify Reserved Instances](ri-modifying.md) \.

**Topics**
+ [Requirements for exchanging Convertible Reserved Instances](#riconvertible-exchange-limits)
+ [Calculate Convertible Reserved Instances exchanges](#riconvertible-exchange-cost)
+ [Merge Convertible Reserved Instances](#ri-merge-convertible)
+ [Exchange a portion of a Convertible Reserved Instance](#ri-split-convertible)
+ [Submit exchange requests](#ri-exchange-process)

## Requirements for exchanging Convertible Reserved Instances<a name="riconvertible-exchange-limits"></a>

If the following conditions are met, Amazon EC2 processes your exchange request\. Your Convertible Reserved Instance must be:
+ Active
+ Not pending a previous exchange request

The following rules apply:
+ Convertible Reserved Instances can only be exchanged for other Convertible Reserved Instances currently offered by AWS\.
+ Convertible Reserved Instances are associated with a specific Region, which is fixed for the duration of the reservation's term\. You cannot exchange a Convertible Reserved Instance for a Convertible Reserved Instance in a different Region\.
+ You can exchange one or more Convertible Reserved Instances at a time for one Convertible Reserved Instance only\.
+ To exchange a portion of a Convertible Reserved Instance, you can modify it into two or more reservations, and then exchange one or more of the reservations for a new Convertible Reserved Instance\. For more information, see [Exchange a portion of a Convertible Reserved Instance](#ri-split-convertible)\. For more information about modifying your Reserved Instances, see [Modify Reserved Instances](ri-modifying.md)\.
+ All Upfront Convertible Reserved Instances can be exchanged for Partial Upfront Convertible Reserved Instances, and vice versa\.
**Note**  
If the total upfront payment required for the exchange \(true\-up cost\) is less than $0\.00, AWS automatically gives you a quantity of instances in the Convertible Reserved Instance that ensures that true\-up cost is $0\.00 or more\.
**Note**  
If the total value \(upfront price \+ hourly price \* number of remaining hours\) of the new Convertible Reserved Instance is less than the total value of the exchanged Convertible Reserved Instance, AWS automatically gives you a quantity of instances in the Convertible Reserved Instance that ensures that the total value is the same or higher than that of the exchanged Convertible Reserved Instance\.
+ To benefit from better pricing, you can exchange a No Upfront Convertible Reserved Instance for an All Upfront or Partial Upfront Convertible Reserved Instance\.
+ You cannot exchange All Upfront and Partial Upfront Convertible Reserved Instances for No Upfront Convertible Reserved Instances\.
+ You can exchange a No Upfront Convertible Reserved Instance for another No Upfront Convertible Reserved Instance only if the new Convertible Reserved Instance's hourly price is the same or higher than the exchanged Convertible Reserved Instance's hourly price\. 
**Note**  
If the total value \(hourly price \* number of remaining hours\) of the new Convertible Reserved Instance is less than the total value of the exchanged Convertible Reserved Instance, AWS automatically gives you a quantity of instances in the Convertible Reserved Instance that ensures that the total value is the same or higher than that of the exchanged Convertible Reserved Instance\.
+ If you exchange multiple Convertible Reserved Instances that have different expiration dates, the expiration date for the new Convertible Reserved Instance is the date that's furthest in the future\.
+ If you exchange a single Convertible Reserved Instance, it must have the same term \(1\-year or 3\-years\) as the new Convertible Reserved Instance\. If you merge multiple Convertible Reserved Instances with different term lengths, the new Convertible Reserved Instance has a 3\-year term\. For more information, see [Merge Convertible Reserved Instances](#ri-merge-convertible)\.
+ After you exchange a Convertible Reserved Instance, the original reservation is retired\. Its end date is the start date of the new reservation, and the end date of the new reservation is the same as the end date of the original Convertible Reserved Instance\. For example, if you modify a three\-year reservation that had 16 months left in its term, the resulting modified reservation is a 16\-month reservation with the same end date as the original one\.

## Calculate Convertible Reserved Instances exchanges<a name="riconvertible-exchange-cost"></a>

Exchanging Convertible Reserved Instances is free\. However, you might be required to pay a true\-up cost, which is a prorated upfront cost of the difference between the original Convertible Reserved Instances that you had and the new Convertible Reserved Instances that you receive from the exchange\.

Each Convertible Reserved Instance has a list value\. This list value is compared to the list value of the Convertible Reserved Instances that you want in order to determine how many instance reservations you can receive from the exchange\.

For example: You have 1 x $35\-list value Convertible Reserved Instance that you want to exchange for a new instance type with a list value of $10\.

```
$35/$10 = 3.5
```

You can exchange your Convertible Reserved Instance for three $10 Convertible Reserved Instances\. It's not possible to purchase half reservations; therefore you must purchase an additional Convertible Reserved Instance to cover the remainder:

```
3.5 = 3 whole Convertible Reserved Instances + 1 additional Convertible Reserved Instance
```

The fourth Convertible Reserved Instance has the same end date as the other three\. If you are exchanging Partial or All Upfront Convertible Reserved Instances, you pay the true\-up cost for the fourth reservation\. If the remaining upfront cost of your Convertible Reserved Instances is $500, and the new reservation would normally cost $600 on a prorated basis, you are charged $100\.

```
$600 prorated upfront cost of new reservations - $500 remaining upfront cost of original reservations = $100 difference
```

## Merge Convertible Reserved Instances<a name="ri-merge-convertible"></a>

If you merge two or more Convertible Reserved Instances, the term of the new Convertible Reserved Instance must be the same as the original Convertible Reserved Instances, or the highest of the original Convertible Reserved Instances\. The expiration date for the new Convertible Reserved Instance is the expiration date that's furthest in the future\.

For example, you have the following Convertible Reserved Instances in your account:


| Reserved Instance ID | Term | Expiration date | 
| --- | --- | --- | 
| aaaa1111 | 1\-year | 2018\-12\-31 | 
| bbbb2222 | 1\-year | 2018\-07\-31 | 
| cccc3333 | 3\-year | 2018\-06\-30 | 
| dddd4444 | 3\-year | 2019\-12\-31 | 
+ You can merge `aaaa1111` and `bbbb2222` and exchange them for a 1\-year Convertible Reserved Instance\. You cannot exchange them for a 3\-year Convertible Reserved Instance\. The expiration date of the new Convertible Reserved Instance is 2018\-12\-31\.
+ You can merge `bbbb2222` and `cccc3333` and exchange them for a 3\-year Convertible Reserved Instance\. You cannot exchange them for a 1\-year Convertible Reserved Instance\. The expiration date of the new Convertible Reserved Instance is 2018\-07\-31\.
+ You can merge `cccc3333` and `dddd4444` and exchange them for a 3\-year Convertible Reserved Instance\. You cannot exchange them for a 1\-year Convertible Reserved Instance\. The expiration date of the new Convertible Reserved Instance is 2019\-12\-31\.

## Exchange a portion of a Convertible Reserved Instance<a name="ri-split-convertible"></a>

You can use the modification process to split your Convertible Reserved Instance into smaller reservations, and then exchange one or more of the new reservations for a new Convertible Reserved Instance\. The following examples demonstrate how you can do this\.

**Example: Convertible Reserved Instance with multiple instances**  
In this example, you have a `t2.micro` Convertible Reserved Instance with four instances in the reservation\. To exchange two `t2.micro` instances for an `m4.xlarge` instance:  

1. Modify the `t2.micro` Convertible Reserved Instance by splitting it into two `t2.micro` Convertible Reserved Instances with two instances each\.

1. Exchange one of the new `t2.micro` Convertible Reserved Instances for an `m4.xlarge` Convertible Reserved Instance\.

![\[Modifying and exchange Reserved Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-split-cri-multiple.png)

**Example: Convertible Reserved Instance with a single instance**  
In this example, you have a `t2.large` Convertible Reserved Instance\. To change it to a smaller `t2.medium` instance and a `m3.medium` instance:  

1. Modify the `t2.large` Convertible Reserved Instance by splitting it into two `t2.medium` Convertible Reserved Instances\. A single `t2.large` instance has the same instance size footprint as two `t2.medium` instances\.

1. Exchange one of the new `t2.medium` Convertible Reserved Instances for an `m3.medium` Convertible Reserved Instance\.

![\[Modify and exchange Reserved Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ri-split-cri-single.png)
For more information, see [Support for modifying instance sizes](ri-modifying.md#ri-modification-instancemove) and [Submit exchange requests](#ri-exchange-process)\.

## Submit exchange requests<a name="ri-exchange-process"></a>

You can exchange your Convertible Reserved Instances using the Amazon EC2 console or a command line tool\.

### Exchange a Convertible Reserved Instance using the console<a name="ri-exchange-process-console"></a>

You can search for Convertible Reserved Instances offerings and select your new configuration from the choices provided\.

**To exchange Convertible Reserved Instances using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Reserved Instances**, select the Convertible Reserved Instances to exchange, and choose **Actions**, **Exchange Reserved Instance**\.

1. Select the attributes of the desired configuration using the drop\-down menus, and choose **Find Offering**\. 

1. Select a new Convertible Reserved Instance The **Instance Count** column displays the number of Reserved Instances that you receive for the exchange\. When you have selected a Convertible Reserved Instance that meets your needs, choose **Exchange**\.

The Reserved Instances that were exchanged are retired, and the new Reserved Instances are displayed in the Amazon EC2 console\. This process can take a few minutes to propagate\.

### Exchange a Convertible Reserved Instance using the command line interface<a name="ri-exchange-process-CLI"></a>

To exchange a Convertible Reserved Instance, first find a new Convertible Reserved Instance that meets your needs:
+ [describe\-reserved\-instances\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) \(AWS CLI\)
+ [Get\-EC2ReservedInstancesOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html) \(Tools for Windows PowerShell\)

Get a quote for the exchange, which includes the number of Reserved Instances you get from the exchange, and the true\-up cost for the exchange:
+ [get\-reserved\-instances\-exchange\-quote](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-reserved-instances-exchange-quote.html) \(AWS CLI\)
+ [GetEC2\-ReservedInstancesExchangeQuote](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesExchangeQuote.html) \(Tools for Windows PowerShell\)

Finally, perform the exchange:
+ [accept\-reserved\-instances\-exchange\-quote](https://docs.aws.amazon.com/cli/latest/reference/ec2/accept-reserved-instances-exchange-quote.html) \(AWS CLI\)
+ [Confirm\-EC2ReservedInstancesExchangeQuote](https://docs.aws.amazon.com/powershell/latest/reference/items/Confirm-EC2ReservedInstancesExchangeQuote.html) \(Tools for Windows PowerShell\)
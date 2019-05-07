# Capacity Reservation Pricing and Billing<a name="capacity-reservations-pricing-biling"></a>

## Pricing<a name="capacity-reservations-pricing"></a>

When the Capacity Reservation is active, you are charged the equivalent On\-Demand rate whether you run the instances or not\. If you do not use the reservation, this shows up as unused reservation on your EC2 bill\. When you run an instance that matches the attributes of a reservation, you just pay for the instance and nothing for the reservation\. There are no upfront or additional charges\. 

For example, if you create a Capacity Reservation for 20 m4\.large Linux instances and run 15 m4\.large Linux instances in the same Availability Zone, you will be charged for 15 instances and for 5 unused spots in the reservation\.

**Note**  
Regional RIs billing discounts apply to Capacity Reservations\. AWS automatically applies your active Regional RIs to active and unused Capacity Reservations that have matching attributes\. For more information about Regional RIs, see [Reserved Instances](ec2-reserved-instances.md)\.

For more information about Amazon EC2 pricing, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

## Billing<a name="capacity-reservations-billing"></a>

Capacity Reservations are billed at per\-second granularity\. This means that you are charged for partial hours\. For example, if a reservation remains active in your account for 24 hours and 15 minutes, you will be billed for 24\.25 reservation hours\.

The following example shows how a Capacity Reservation is billed\. The Capacity Reservation is created for one m4\.large Linux instance, which has an On\-Demand rate of $0\.10 per usage hour\. In this example, the Capacity Reservation is active in the account for five hours\. The Capacity Reservation is unused for the first hour, so it is billed for one unused hour at the m4\.large instance type's standard On\-Demand rate\. In hours two through five, the Capacity Reservation is occupied by an m4\.large instance\. During this time, the Capacity Reservation accrues no charges, and the account is instead billed for the m4\.large instance occupying it\. In the sixth hour, the Capacity Reservation is canceled and the m4\.large instance runs normally outside of the reserved capacity\. For that hour, it is charged at the On\-Demand rate of the m4\.large instance type\.

![\[Capacity Reservation billing example\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/cr-billing-example.png)

## Billing Discounts<a name="capacity-reservations-discounts"></a>

Regional RIs billing discounts apply to Capacity Reservations\. AWS automatically applies your active Regional RIs to active Capacity Reservations that have matching attributes\. For more information about Regional RIs, see [Reserved Instances](ec2-reserved-instances.md)\.

**Note**  
Zonal RI billing discounts do not apply to Capacity Reservations\.

When your instance\-hours and reservation\-hours combined exceed your total eligible discounted Regional RI hours, discounts are preferentially applied to instance\-hours first and then to unused reservation\-hours\.

## Viewing Your Bill<a name="capacity-reservations-viewing-bill"></a>

You can find out about the charges and fees to your account by viewing the AWS Billing and Cost Management console\.
+ The **Dashboard** displays a spend summary for your account\.
+ On the **Bills** page, under **Details**, expand the **Elastic Compute Cloud** section and the region to get billing information about your Capacity Reservations\.

You can view the charges online, or you can download a CSV file\. For more information, see [Capacity Reservation Line Items](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage-cr.html) in the *AWS Billing and Cost Management User Guide*\.
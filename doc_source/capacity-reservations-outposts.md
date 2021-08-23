# Capacity Reservations on AWS Outposts<a name="capacity-reservations-outposts"></a>

AWS Outposts is a fully managed service that extends AWS infrastructure, services, APIs, and tools to customer premises\. By providing local access to AWS managed infrastructure, AWS Outposts enables customers to build and run applications on premises using the same programming interfaces as in AWS Regions, while using local compute and storage resources for lower latency and local data processing needs\.

An Outpost is a pool of AWS compute and storage capacity deployed at a customer site\. AWS operates, monitors, and manages this capacity as part of an AWS Region\. 

You can create Capacity Reservations on Outposts that you have created in your account\. This allows you to reserve compute capacity on an Outpost at your site\. You create and use Capacity Reservations on Outposts in the same way that you create and use Capacity Reservations in regular Availability Zones\. The same features and instance matching behavior apply\.

You can also share Capacity Reservations on Outposts with other AWS accounts within your organization using AWS Resource Access Manager\. For more information about sharing Capacity Reservations, see [Work with shared Capacity Reservations](capacity-reservation-sharing.md)\.

**Prerequisite**  
You must have an Outpost installed at your site\. For more information, see [ Create an Outpost and order Outpost capacity](https://docs.aws.amazon.com/outposts/latest/userguide/order-outpost-capacity.html) in the *AWS Outposts User Guide*\.

**Considerations**
+ You can't use Capacity Reservation groups on an Outpost\.

**To use a Capacity Reservation on an Outpost**

1. Create a subnet on the Outpost\. For more information, see [Create a subnet](https://docs.aws.amazon.com/outposts/latest/userguide/launch-instance.html#create-subnet) in the *AWS Outposts User Guide*\.

1. Create a Capacity Reservation on the Outpost\.

   1. Open the AWS Outposts console at [https://console\.aws\.amazon\.com/outposts/](https://console.aws.amazon.com/outposts/home)\.

   1. In the navigation pane, choose **Outposts**, and then choose **Actions**, **Create Capacity Reservation**\.

   1. Configure the Capacity Reservation as needed and then choose **Create**\. For more information, see [Create a Capacity Reservation](capacity-reservations-using.md#capacity-reservations-create)\.
**Note**  
The **Instance Type** drop\-down lists only instance types that are supported by the selected Outpost, and the **Availability Zone** drop\-down lists only the Availability Zone with which the selected Outpost is associated\.

1. Launch an instance into the Capacity Reservation\. For **Subnet** choose the subnet that you created in Step 1, and for **Capacity Reservation**, select the Capacity Reservation that you created in Step 2\. For more information, see [Launch an instance on the Outpost](https://docs.aws.amazon.com/outposts/latest/userguide/launch-instance.html#launch-instances) in the *AWS Outposts User Guide*\.
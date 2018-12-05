# Working with Capacity Reservations<a name="capacity-reservations-using"></a>

To start using Capacity Reservations, you need to create the capacity reservation in the required Availability Zone\. After you created a Capacity Reservation, you can launch instances into the reserved capacity, view its capacity utilization in real time, and increase or decrease its capacity as needed\. 

By default, Capacity Reservations automatically match new instances and running instances that have matching attributes \(instance type, platform, and Availability Zone\)\. In other words, instances that have matching attributes automatically run in the Capacity Reservation's capacity\. However, you can also target a Capacity Reservation for specific workloads\. This enables you to explicitly control which instances are allowed to run in that reserved capacity\.

**Contents**
+ [Creating a Capacity Reservation](#capacity-reservations-create)
+ [Launching an Instance into an Existing Capacity Reservation](#capacity-reservations-launch)
+ [Modifying a Capacity Reservation](#capacity-reservations-modify)
+ [Modifying an Instance's Capacity Reservation Settings](#capacity-reservations-modify-instance)
+ [Viewing a Capacity Reservation](#capacity-reservations-view)
+ [Cancelling a Capacity Reservation](#capacity-reservations-release)

## Creating a Capacity Reservation<a name="capacity-reservations-create"></a>

Creating a Capacity Reservation in your account creates a capacity reservation in a specific Availability Zone\. After it is created, you can launch instances into the reserved capacity as needed\.

**Note**  
Your request to create a Capacity Reservation could fail if Amazon EC2 does not have sufficient capacity to fulfil the request\. If your request fails due to Amazon EC2 capacity constraints, either try again at a later time, try in a different Availability Zone, or request a smaller capacity reservation\. If your application is flexible across instance types and sizes, try to create a Capacity Reservation with different instance attributes\.  
Your request could also fail if the requested quantity exceeds your On\-Demand Instance limit for the selected instance type\. If your request fails due to limit constraints, increase your On\-Demand Instance limit for the required instance type and try again\. For more information about increasing your instance limits, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.

After you create the Capacity Reservation, the capacity is available immediately\. The capacity remains reserved for your use as long as the Capacity Reservation is active, and you can launch instances into it at any time\. If the Capacity Reservation is `open`, new instances and existing instances that have matching attributes automatically run in the Capacity Reservation's capacity\. If the Capacity Reservation is `targeted`, instances must specifically target it to run in the reserved capacity\.

You can create a Capacity Reservation using the Amazon EC2 console or the AWS CLI\.

**To create a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations**, choose **Create Capacity Reservation**\.

1. On the Create a Capacity Reservation page, configure the following settings in the **Instance details** section:

   1. **Instance Type**—Specify the type of instance to launch into the reserved capacity\.

   1. **Launch EBS\-optimized instances**—Specify whether to reserve the capacity for EBS\-optimized instances\. This option is selected by default for some instance types\. For more information about EBS\-optimized instances, see [Amazon Elastic Block Store \(Amazon EBS\)](AmazonEBS.md)\.

   1. **Attach instance store at launch**—Indicate whether instances launched into the Capacity Reservation use temporary block\-level storage\. The data on an instance store volume persists only during the life of the associated instance\.

   1. **Platform**—Specify the operating system for your intended instances\.

   1. **Availability Zone**—Specify the Availability Zone in which to reserve the capacity\.

   1. **Quantity**—Specify the number instances for which to reserve capacity\. If you specify a quantity that exceeds your remaining On\-Demand Instance limit for the selected instance type, the request will be denied\.

1. Configure the following settings in the **Reservation details** section:

   1. **Reservation Ends**—Choose one of the following options:
      + **Manually**—Reserve the capacity until you explicitly cancel it\.
      + **Specific time**—Cancels the capacity reservation automatically\. The capacity reservation is released automatically at the specified date and time\. The Capacity Reservation is cancelled within an hour from the specified time\. For example, if you specify 5/31/2019, 13:30:55, the Capacity Reservation is guaranteed to end between 13:30:55 and 14:30:55 on 5/31/2019\.
**Note**  
After the reservation ends, you can no longer target instances to the Capacity Reservation\. Instances running in the reserved capacity continue to run uninterrupted\. If instances targeting a Capacity Reservation are stopped, you cannot restart them until you remove their Capacity Reservation targeting preference or configure them to target a different Capacity Reservation\.

   1. **Instance eligibility**—Choose one of the following options:
      + **open**—\(Default\) The Capacity Reservation matches any instance that has matching attributes \(instance type, platform, and Availability Zone\)\. If you launch an instance with matching attributes, it is placed into the reserved capacity automatically\.
      + **targeted**—The Capacity Reservation only accepts instances that have matching attributes \(instance type, platform, and Availability Zone\), and explicitly target the reservation\.

1. Choose **Request reservation**\.

**To create a Capacity Reservation using the AWS CLI**  
Use the [create\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-capacity-reservation.html) command:

```
$  aws ec2 create-capacity-reservation --instance-type instance_type --instance-platform platform_type --availability-zone az --instance-count quantity
```

## Launching an Instance into an Existing Capacity Reservation<a name="capacity-reservations-launch"></a>

You can launch an instance into a Capacity Reservation if it has matching attributes \(instance type, platform, and Availability Zone\) and sufficient capacity\. Launching an instance into a Capacity Reservation reduces its available capacity by the number of instances launched\. For example, if you launch three instances, the Capacity Reservation's available capacity is reduced by three\.

You can launch an instance into a Capacity Reservation that you previously created using the Amazon EC2 console or the command line\.

**To launch an instance into an existing Capacity Reservation using the console**

1. Open the Launch Instance wizard by doing one of the following:
   + Choose **Instances**, **Launch Instance**\.
   + Choose **Capacity Reservations**, **Launch Instance**\.

1. Complete the instance details to suit your requirements\.

1. On the **Configure Instance Details** page, for **Capacity Reservation**, do one of the following:
   + Choose **Open** to launch the instance into any `open` Capacity Reservation that has matching attributes \(instance type, platform, and Availability Zone\) and sufficient capacity\.
**Note**  
If you do not have a matching `open` Capacity Reservation with sufficient capacity, the instance launches into On\-Demand capacity\.
   + Choose **None** to prevent the instance from launching into a Capacity Reservation\.
   + Choose the specific Capacity Reservation into which to launch the instance\.
**Note**  
If the selected Capacity Reservation does not have sufficient capacity, the instance launch fails\.

1. Choose **Review and Launch**, **Launch**\.

1. When prompted, select an existing key pair or create a new one, and choose **Launch Instances**\.

**To launch an instance into an existing Capacity Reservation using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and specify the `--capacity-reservation-specification` parameter\.

The following example launches a `t2.micro` instance into any `open` Capacity Reservation that has matching attributes and available capacity:

```
$  aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --availability-zone us-east-1b --capacity-reservation-specification CapacityReservationPreference=open
```

The following example launches a t2\.micro instance into a `targeted` Capacity Reservation:

```
$  aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --availability-zone us-east-1b --capacity-reservation-specification CapacityReservationTarget=[{CapacityReservationId=cr-a1234567}]
```

## Modifying a Capacity Reservation<a name="capacity-reservations-modify"></a>

You can change an active Capacity Reservation's attributes after you have created it\. You cannot modify a Capacity Reservation after it has expired or after you have explicitly cancelled it\.

When modifying a Capacity Reservation, you can only increase or decrease the quantity and change the way in which it is released\. You cannot change a Capacity Reservation's instance type, EBS optimization, instance store settings, platform, Availability Zone, or instance eligibility\. If you need to modify any of these attributes, we recommend that you cancel the reservation, and then create a new one with the required attributes\.

You can modify a Capacity Reservation using the Amazon EC2 console and the AWS CLI\.

**To modify a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations**, select the Capacity Reservation to modify, and then choose **Edit**\.

1. Modify the **Quantity** or **Reservation ends** options as needed, and choose **Save changes**\.
**Note**  
If you specify a new quantity that exceeds your remaining On\-Demand Instance limit for the selected instance type, the update fails\.

**To modify a Capacity Reservation using the AWS CLI**  
Use the [modify\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-capacity-reservations.html) command:

```
$  aws ec2 modify-capacity-reservation --capacity-reservation-id reservation_id --instance-count quantity --end-date-type limited|unlimited --end-date expiration_date
```

## Modifying an Instance's Capacity Reservation Settings<a name="capacity-reservations-modify-instance"></a>

You can modify an existing instance's Capacity Reservation settings at any time\. You can modify a stopped instance to do the following:
+ Target a specific Capacity Reservation\. The instance cannot launch outside of the targeted Capacity Reservation\.
+ Launch on any Capacity Reservation that has matching attributes \(instance type, platform, and Availability Zone\) and available capacity\.
+ Avoid launching in a Capacity Reservation\. The instance is prevented from launching in any Capacity Reservation, even if the reservation is open and has matching attributes \(instance type, platform, and Availability Zone\)\.

**Note**  
You can only modify an instance's Capacity Reservation settings while it is stopped\.

You can modify an instance's Capacity Reservation settings using the Amazon EC2 console and the AWS CLI\.

**To modify an instance's Capacity Reservation settings using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances**, select the instance to modify, and then choose **Actions**, **Modify Capacity Reservation Settings**\.

1. For **Capacity Reservation**, do one of the following:
   + Choose **Open** to configure the instance to run in any `open` Capacity Reservation that has matching attributes \(instance type, platform, and Availability Zone\) and sufficient capacity\.
**Note**  
If you do not have a matching `open` Capacity Reservation with sufficient capacity, the instance launches into On\-Demand capacity\.
   + Choose **None** to prevent the instance from launching into a Capacity Reservation\.
   + Choose the specific Capacity Reservation in which the instance should run\.
**Note**  
If the instance attributes \(instance type, platform, and Availability Zone\) do not match those of the selected Capacity Reservation, or if the selected Capacity Reservation does not have sufficient capacity, the instance launch fails\.

**To modify an instance's Capacity Reservation settings using the AWS CLI**  
Use the [modify\-instance\-capacity\-reservation\-attributes](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-capacity-reservation-attributes.html) command:

```
$  aws ec2 modify-instance-capacity-reservation-attributes --instance-id instance_id --capacity-reservation-specification 'CapacityReservationPreference=none|open'
```

## Viewing a Capacity Reservation<a name="capacity-reservations-view"></a>

Capacity Reservations have three possible states:
+ `active`—The Capacity Reservation is active and the capacity is available for your use\.
+ `expired`—The Capacity Reservation expired automatically at the date and time specified in your reservation request\. The reserved capacity is no longer available for your use\.
+ `cancelled`—The Capacity Reservation was manually cancelled\. The reserved capacity is no longer available for your use\.
+ `pending`—The Capacity Reservation request was successful but the capacity provisioning is still pending\.
+ `failed`—The Capacity Reservation request has failed\. A request might fail due to invalid request parameters, capacity constraints, or instance limit constraints\. Failed requests are retained for 60 minutes\.

You can view your active Capacity Reservations using the Amazon EC2 console and the AWS CLI\.

**To view your Capacity Reservations using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations** and select a Capacity Reservation to view\.

1. Choose **View launched instances for this reservation**\.

**To view your Capacity Reservations using the AWS CLI**  
Use the [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) command:

```
$  aws ec2 describe-capacity-reservations
```

## Cancelling a Capacity Reservation<a name="capacity-reservations-release"></a>

You can cancel a Capacity Reservation at any time if you no longer need the reserved capacity\. When you cancel a Capacity Reservation, the capacity is released immediately, and it is no longer reserved for your use\.

You can cancel empty Capacity Reservations and Capacity Reservations that have running instances\. If you cancel a Capacity Reservation that has running instances, the instances continue to run normally outside of the capacity reservation at standard On\-Demand Instance rates or at a discounted rate if you have an active matching Regional RI\.

After you cancel a Capacity Reservation, instances that target it can no longer launch\. Modify these instances so that they either target a different Capacity Reservation, launch into any 'open' Capacity Reservation with matching attributes and sufficient capacity, or avoid launching into a Capacity Reservation\. For more information, see [Modifying an Instance's Capacity Reservation Settings](#capacity-reservations-modify-instance)\.

You can cancel a Capacity Reservation using the Amazon EC2 console and the AWS CLI\.

**To cancel a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations** and select the Capacity Reservation to cancel\.

1. Choose **Cancel reservation**, **Cancel reservation**\.

**To cancel a Capacity Reservation using the AWS CLI**  
Use the [cancel\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-capacity-reservation.html) command:

```
$  aws ec2 cancel-capacity-reservation --capacity-reservation-id reservation_id
```
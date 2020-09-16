# Working with shared Capacity Reservations<a name="capacity-reservation-sharing"></a>

Capacity Reservation sharing enables Capacity Reservation owners to share their reserved capacity with other AWS accounts or within an AWS organization\. This enables you to create and manage Capacity Reservations centrally, and share the reserved capacity across multiple AWS accounts or within your AWS organization\.

In this model, the AWS account that owns the Capacity Reservation \(owner\) shares it with other AWS accounts \(consumers\)\. Consumers can launch instances into Capacity Reservations that are shared with them in the same way that they launch instances into Capacity Reservations that they own in their own account\. The Capacity Reservation owner is responsible for managing the Capacity Reservation and the instances that they launch into it\. Owners cannot modify instances that consumers launch into Capacity Reservations that they have shared\. Consumers are responsible for managing the instances that they launch into Capacity Reservations shared with them\. Consumers cannot view or modify instances owned by other consumers or by the Capacity Reservation owner\.

A Capacity Reservation owner can share a Capacity Reservation with:
+ Specific AWS accounts inside or outside of its AWS organization
+ An organizational unit inside its AWS organization
+ Its entire AWS organization

**Topics**
+ [Prerequisites for sharing Capacity Reservations](#sharing-cr-prereq)
+ [Related services](#cr-sharing-related)
+ [Sharing across Availability Zones](#cr-sharing-azs)
+ [Sharing a Capacity Reservation](#sharing-cr)
+ [Stop sharing a Capacity Reservation](#unsharing-cr)
+ [Identifying a shared Capacity Reservation](#identifying-shared-cr)
+ [Viewing shared Capacity Reservation usage](#shared-cr-usage)
+ [Shared Capacity Reservation permissions](#shared-cr-perms)
+ [Billing and metering](#shared-cr-billing)
+ [Instance limits](#shared-cr-limits)

## Prerequisites for sharing Capacity Reservations<a name="sharing-cr-prereq"></a>
+ To share a Capacity Reservation, you must own it in your AWS account\. You cannot share a Capacity Reservation that has been shared with you\.
+ You can only share Capacity Reservations for shared tenancy instances\. You cannot share Capacity Reservations for dedicated tenancy instances\.
+ Capacity Reservation sharing is not available to new AWS accounts or AWS accounts that have a limited billing history\. New accounts that are linked to a qualified master \(payer\) account or are linked through an AWS organization are exempt from this restriction\.
+ To share a Capacity Reservation with your AWS organization or an organizational unit in your AWS organization, you must enable sharing with AWS Organizations\. For more information, see [Enable Sharing with AWS Organizations](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html) in the *AWS RAM User Guide*\.

## Related services<a name="cr-sharing-related"></a>

Capacity Reservation sharing integrates with AWS Resource Access Manager \(AWS RAM\)\. AWS RAM is a service that enables you to share your AWS resources with any AWS account or through AWS Organizations\. With AWS RAM, you share resources that you own by creating a *resource share*\. A resource share specifies the resources to share, and the consumers with whom to share them\. Consumers can be individual AWS accounts, or organizational units or an entire organization from AWS Organizations\.

For more information about AWS RAM, see the *[AWS RAM User Guide](https://docs.aws.amazon.com/ram/latest/userguide/)*\.

## Sharing across Availability Zones<a name="cr-sharing-azs"></a>

To ensure that resources are distributed across the Availability Zones for a Region, we independently map Availability Zones to names for each account\. This could lead to Availability Zone naming differences across accounts\. For example, the Availability Zone `us-east-1a` for your AWS account might not have the same location as `us-east-1a` for another AWS account\.

To identify the location of your Capacity Reservations relative to your accounts, you must use the *Availability Zone ID* \(AZ ID\)\. The AZ ID is a unique and consistent identifier for an Availability Zone across all AWS accounts\. For example, `use1-az1` is an AZ ID for the `us-east-1` Region and it is the same location in every AWS account\.

**To view the AZ IDs for the Availability Zones in your account**

1. Open the AWS RAM console at [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/)\.

1. The AZ IDs for the current Region are displayed in the **Your AZ ID** panel on the right\-hand side of the screen\.

## Sharing a Capacity Reservation<a name="sharing-cr"></a>

When you share a Capacity Reservation that you own with other AWS accounts, you enable them to launch instances into your reserved capacity\. If you share an open Capacity Reservation, keep the following in mind as it could lead to unintended Capacity Reservation usage:
+ If consumers have running instances that match the attributes of the Capacity Reservation, have the `CapacityReservationPreference` parameter set to `open`, and are not yet running in reserved capacity, they automatically use the shared Capacity Reservation\.
+ If consumers launch instances that have matching attributes \(instance type, platform, and Availability Zone\) and have the `CapacityReservationPreference` parameter set to `open`, they automatically launch into the shared Capacity Reservation\.

To share a Capacity Reservation, you must add it to a resource share\. A resource share is an AWS RAM resource that lets you share your resources across AWS accounts\. A resource share specifies the resources to share, and the consumers with whom they are shared\. When you share a Capacity Reservation using the Amazon EC2 console, you add it to an existing resource share\. To add the Capacity Reservation to a new resource share, you must create the resource share using the [AWS RAM console](https://console.aws.amazon.com/ram)\.

If you are part of an organization in AWS Organizations and sharing within your organization is enabled, consumers in your organization are automatically granted access to the shared Capacity Reservation\. Otherwise, consumers receive an invitation to join the resource share and are granted access to the shared Capacity Reservation after accepting the invitation\.

You can share a Capacity Reservation that you own using the Amazon EC2 console, AWS RAM console, or the AWS CLI\.

**To share a Capacity Reservation that you own using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Capacity Reservations**\.

1. Choose the Capacity Reservation to share and choose **Actions**, **Share reservation**\.

1. Select the resource share to which to add the Capacity Reservation and choose **Share Capacity Reservation**\.

   It could take a few minutes for consumers to get access to the shared Capacity Reservation\.

**To share a Capacity Reservation that you own using the AWS RAM console**  
See [Creating a Resource Share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html#working-with-sharing-create) in the *AWS RAM User Guide*\.

**To share a Capacity Reservation that you own using the AWS CLI**  
Use the [create\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html) command\.

## Stop sharing a Capacity Reservation<a name="unsharing-cr"></a>

The Capacity Reservation owner can stop sharing a Capacity Reservation at any time\. The following rules apply:
+ Instances owned by consumers that were running in the shared capacity at the time sharing stops continue to run normally outside of the reserved capacity, and the capacity is restored to the Capacity Reservation subject to Amazon EC2 capacity availability\.
+ Consumers with whom the Capacity Reservation was shared can no longer launch new instances into the reserved capacity\.

To stop sharing a Capacity Reservation that you own, you must remove it from the resource share\. You can do this using the Amazon EC2 console, AWS RAM console, or the AWS CLI\.

**To stop sharing a Capacity Reservation that you own using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Capacity Reservations**\.

1. Select the Capacity Reservation and choose the **Sharing** tab\.

1. The **Sharing** tab lists the resource shares to which the Capacity Reservation has been added\. Select the resource share from which to remove the Capacity Reservation and choose **Remove from resource share**\.

**To stop sharing a Capacity Reservation that you own using the AWS RAM console**  
See [Updating a Resource Share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html#working-with-sharing-update) in the *AWS RAM User Guide*\.

**To stop sharing a Capacity Reservation that you own using the AWS CLI**  
Use the [disassociate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html) command\.

## Identifying a shared Capacity Reservation<a name="identifying-shared-cr"></a>

Owners and consumers can identify shared Capacity Reservations using the Amazon EC2 console and AWS CLI

**To identify a shared Capacity Reservation using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Capacity Reservations**\. The screen lists Capacity Reservations that you own and Capacity Reservations that are shared with you\. The **Owner** column shows the AWS account ID of the Capacity Reservation owner\. `(me)` next to the AWS account ID indicates that you are the owner\.

**To identify a shared Capacity Reservation using the AWS CLI**  
Use the [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) command\. The command returns the Capacity Reservations that you own and Capacity Reservations that are shared with you\. `OwnerId` shows the AWS account ID of the Capacity Reservation owner\.

## Viewing shared Capacity Reservation usage<a name="shared-cr-usage"></a>

The owner of a shared Capacity Reservation can view its usage at any time using the Amazon EC2 console and the AWS CLI\.

**To view Capacity Reservation usage using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Capacity Reservations**\.

1. Select the Capacity Reservation for which to view the usage and choose the **Usage** tab\.

   The **AWS account ID** column shows the account IDs of the consumers currently using the Capacity Reservation\. The **Launched instances** column shows the number of instances each consumer currently has running in the reserved capacity\.

**To view Capacity Reservation usage using the AWS CLI**  
Use the [get\-capacity\-reservation\-usage](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-capacity-reservation-usage.html) command\. `AccountId` shows the account ID of the account using the Capacity Reservation\. `UsedInstanceCount` shows the number of instances the consumer currently has running in the reserved capacity\.

## Shared Capacity Reservation permissions<a name="shared-cr-perms"></a>

### Permissions for owners<a name="shared-cr-perms-owner"></a>

Owners are responsible for managing and canceling their shared Capacity Reservations\. Owners cannot modify instances running in the shared Capacity Reservation that are owned by other accounts\. Owners remain responsible for managing instances that they launch into the shared Capacity Reservation\.

### Permissions for consumers<a name="shared-cr-perms-consumer"></a>

Consumers are responsible for managing their instances that are running the shared Capacity Reservation\. Consumers cannot modify the shared Capacity Reservation in any way, and they cannot view or modify instances that are owned by other consumers or the Capacity Reservation owner\.

## Billing and metering<a name="shared-cr-billing"></a>

There are no additional charges for sharing Capacity Reservations\.

The Capacity Reservation owner is billed for instances that they run inside the Capacity Reservation and for unused reserved capacity\. Consumers are billed for the instances that they run inside the shared Capacity Reservation\.

## Instance limits<a name="shared-cr-limits"></a>

All Capacity Reservation usage counts toward the Capacity Reservation owner's On\-Demand Instance limits\. This includes:
+ Unused reserved capacity
+ Usage by instances owned by the Capacity Reservation owner
+ Usage by instances owned by consumers

Instances launched into the shared capacity by consumers count towards the Capacity Reservation owner's On\-Demand Instance limit\. Consumers' instance limits are a sum of their own On\-Demand Instance limits and the capacity available in the shared Capacity Reservations to which they have access\.
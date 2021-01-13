# Work with shared Dedicated Hosts<a name="dh-sharing"></a>

Dedicated Host sharing enables Dedicated Host owners to share their Dedicated Hosts with other AWS accounts or within an AWS organization\. This enables you to create and manage Dedicated Hosts centrally, and share the Dedicated Host across multiple AWS accounts or within your AWS organization\.

In this model, the AWS account that owns the Dedicated Host \(*owner*\) shares it with other AWS accounts \(*consumers*\)\. Consumers can launch instances onto Dedicated Hosts that are shared with them in the same way that they would launch instances onto Dedicated Hosts that they allocate in their own account\. The owner is responsible for managing the Dedicated Host and the instances that they launch onto it\. Owners can't modify instances that consumers launch onto shared Dedicated Hosts\. Consumers are responsible for managing the instances that they launch onto Dedicated Hosts shared with them\. Consumers can't view or modify instances owned by other consumers or by the Dedicated Host owner, and they can't modify Dedicated Hosts that are shared with them\.

A Dedicated Host owner can share a Dedicated Host with:
+ Specific AWS accounts inside or outside of its AWS organization
+ An organizational unit inside its AWS organization
+ Its entire AWS organization

**Topics**
+ [Prerequisites for sharing Dedicated Hosts](#dh-sharing-prereq)
+ [Limitations for sharing Dedicated Hosts](#dh-sharing-limitation)
+ [Related services](#dh-sharing-related)
+ [Share across Availability Zones](#dh-sharing-azs)
+ [Share a Dedicated Host](#sharing-dh)
+ [Unshare a shared Dedicated Host](#unsharing-dh)
+ [Identify a shared Dedicated Host](#identifying-shared-dh)
+ [View instances running on a shared Dedicated Host](#shared-dh-usage)
+ [Shared Dedicated Host permissions](#shared-dh-perms)
+ [Billing and metering](#shared-dh-billing)
+ [Dedicated Host limits](#shared-dh-limits)
+ [Host recovery and Dedicated Host sharing](#dh-sharing-retirement)

## Prerequisites for sharing Dedicated Hosts<a name="dh-sharing-prereq"></a>
+ To share a Dedicated Host, you must own it in your AWS account\. You can't share a Dedicated Host that has been shared with you\.
+ To share a Dedicated Host with your AWS organization or an organizational unit in your AWS organization, you must enable sharing with AWS Organizations\. For more information, see [Enable Sharing with AWS Organizations](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html) in the *AWS RAM User Guide*\.

## Limitations for sharing Dedicated Hosts<a name="dh-sharing-limitation"></a>

You can't share Dedicated Hosts that have been allocated for the following instance types: `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, and `u-24tb1.metal`\.

## Related services<a name="dh-sharing-related"></a>

### AWS Resource Access Manager<a name="related-ram"></a>

Dedicated Host sharing integrates with AWS Resource Access Manager \(AWS RAM\)\. AWS RAM is a service that enables you to share your AWS resources with any AWS account or through AWS Organizations\. With AWS RAM, you share resources that you own by creating a *resource share*\. A resource share specifies the resources to share, and the consumers with whom to share them\. Consumers can be individual AWS accounts, or organizational units or an entire organization from AWS Organizations\.

For more information about AWS RAM, see the *[AWS RAM User Guide](https://docs.aws.amazon.com/ram/latest/userguide/)*\.

## Share across Availability Zones<a name="dh-sharing-azs"></a>

To ensure that resources are distributed across the Availability Zones for a Region, we independently map Availability Zones to names for each account\. This could lead to Availability Zone naming differences across accounts\. For example, the Availability Zone `us-east-1a` for your AWS account might not have the same location as `us-east-1a` for another AWS account\.

To identify the location of your Dedicated Hosts relative to your accounts, you must use the *Availability Zone ID* \(AZ ID\)\. The Availability Zone ID is a unique and consistent identifier for an Availability Zone across all AWS accounts\. For example, `use1-az1` is an Availability Zone ID for the `us-east-1` Region and it is the same location in every AWS account\.

**To view the Availability Zone IDs for the Availability Zones in your account**

1. Open the AWS RAM console at [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/)\.

1. The Availability Zone IDs for the current Region are displayed in the **Your AZ ID** panel on the right\-hand side of the screen\.

## Share a Dedicated Host<a name="sharing-dh"></a>

When an owner shares a Dedicated Host, it enables consumers to launch instances on the host\. Consumers can launch as many instances onto the shared host as its available capacity allows\.

**Important**  
Note that you are responsible for ensuring that you have appropriate license rights to share any BYOL licenses on your Dedicated Hosts\.

If you share a Dedicated Host with auto\-placement enabled, keep the following in mind as it could lead to unintended Dedicated Host usage:
+ If consumers launch instances with Dedicated Host tenancy and they do not have capacity on a Dedicated Host that they own in their account, the instance is automatically launched onto the shared Dedicated Host\.

To share a Dedicated Host, you must add it to a resource share\. A resource share is an AWS RAM resource that lets you share your resources across AWS accounts\. A resource share specifies the resources to share, and the consumers with whom they are shared\. You can add the Dedicated Host to an existing resource, or you can add it to a new resource share\.

If you are part of an organization in AWS Organizations and sharing within your organization is enabled, consumers in your organization are automatically granted access to the shared Dedicated Host\. Otherwise, consumers receive an invitation to join the resource share and are granted access to the shared Dedicated Host after accepting the invitation\.

**Note**  
After you share a Dedicated Host, it could take a few minutes for consumers to have access to it\.

You can share a Dedicated Host that you own by using one of the following methods\.

------
#### [ Amazon EC2 console ]

**To share a Dedicated Host that you own using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Choose the Dedicated Host to share and choose **Actions**, **Share host**\.

1. Select the resource share to which to add the Dedicated Host and choose **Share host**\.

   It could take a few minutes for consumers to get access to the shared host\.

------
#### [ AWS RAM console ]

**To share a Dedicated Host that you own using the AWS RAM console**  
See [Creating a Resource Share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html#working-with-sharing-create) in the *AWS RAM User Guide*\.

------
#### [ AWS CLI ]

**To share a Dedicated Host that you own using the AWS CLI**  
Use the [create\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html) command\.

------

## Unshare a shared Dedicated Host<a name="unsharing-dh"></a>

The Dedicated Host owner can unshare a shared Dedicated Host at any time\. When you unshare a shared Dedicated Host, the following rules apply:
+ Consumers with whom the Dedicated Host was shared can no longer launch new instances onto it\.
+ Instances owned by consumers that were running on the Dedicated Host at the time of unsharing continue to run but are scheduled for [ retirement](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-instances-status-check_sched.html#schedevents_actions_retire)\. Consumers receive retirement notifications for the instances and they have two weeks to take action on the notifications\. However, if the Dedicated Host is reshared with the consumer within the retirement notice period, the instance retirements are cancelled\.

To unshare a shared Dedicated Host that you own, you must remove it from the resource share\. You can do this by using one of the following methods\.

------
#### [ Amazon EC2 console ]

**To unshare a shared Dedicated Host that you own using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Choose the Dedicated Host to unshare and choose the **Sharing** tab\.

1. The **Sharing** tab lists the resource shares to which the Dedicated Host has been added\. Select the resource share from which to remove the Dedicated Host and choose **Remove host from resource share**\.

------
#### [ AWS RAM console ]

**To unshare a shared Dedicated Host that you own using the AWS RAM console**  
See [Updating a Resource Share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing.html#working-with-sharing-update) in the *AWS RAM User Guide*\.

------
#### [ Command line ]

**To unshare a shared Dedicated Host that you own using the AWS CLI**  
Use the [ disassociate\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html) command\.

------

## Identify a shared Dedicated Host<a name="identifying-shared-dh"></a>

Owners and consumers can identify shared Dedicated Hosts using one of the following methods\.

------
#### [ Amazon EC2 console ]

**To identify a shared Dedicated Host using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\. The screen lists Dedicated Hosts that you own and Dedicated Hosts that are shared with you\. The **Owner** column shows the AWS account ID of the Dedicated Host owner\. 

------
#### [ Command line ]

**To identify a shared Dedicated Host using the AWS CLI**  
Use the [ describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) command\. The command returns the Dedicated Hosts that you own and Dedicated Hosts that are shared with you\.

------

## View instances running on a shared Dedicated Host<a name="shared-dh-usage"></a>

Owners and consumers can view the instances running on a shared Dedicated Host at any time using one of the following methods\.

------
#### [ Amazon EC2 console ]

**To view the instances running on a shared Dedicated Host using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host for which to view the instances and choose **Instances**\. The tab lists the instances that are running on the host\. Owners see all of the instances running on the host, including instances launched by consumers\. Consumers only see running instances that they launched onto the host\. The **Owner** column shows the AWS account ID of the account that launched the instance\.

------
#### [ Command line ]

**To view the instances running on a shared Dedicated Host using the AWS CLI**  
Use the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) command\. The command returns the instances running on each Dedicated Host\. Owners see all of the instances running on the host\. Consumers only see running instances that they launched on the shared hosts\. `InstanceOwnerId` shows the AWS account ID of the instance owner\.

------

## Shared Dedicated Host permissions<a name="shared-dh-perms"></a>

### Permissions for owners<a name="shared-dh-perms-owner"></a>

Owners are responsible for managing their shared Dedicated Hosts and the instances that they launch onto them\. Owners can view all instances running on the shared Dedicated Host, including those launched by consumers\. However, owners can't take any action on running instances that were launched by consumers\.

### Permissions for consumers<a name="shared-dh-perms-consumer"></a>

Consumers are responsible for managing the instances that they launch onto a shared Dedicated Host\. Consumers can't modify the shared Dedicated Host in any way, and they can't view or modify instances that were launched by other consumers or the Dedicated Host owner\.

## Billing and metering<a name="shared-dh-billing"></a>

There are no additional charges for sharing Dedicated Hosts\.

Owners are billed for Dedicated Hosts that they share\. Consumers are not billed for instances that they launch onto shared Dedicated Hosts\.

Dedicated Host Reservations continue to provide billing discounts for shared Dedicated Hosts\. Only Dedicated Host owners can purchase Dedicated Host Reservations for shared Dedicated Hosts that they own\.

## Dedicated Host limits<a name="shared-dh-limits"></a>

Shared Dedicated Hosts count towards the owner's Dedicated Hosts limits only\. Consumer's Dedicated Hosts limits are not affected by Dedicated Hosts that have been shared with them\. Similarly, instances that consumers launch onto shared Dedicated Hosts do not count towards their instance limits\.

## Host recovery and Dedicated Host sharing<a name="dh-sharing-retirement"></a>

Host recovery recovers instances launched by the Dedicated Host owner and the consumers with whom it has been shared\. The replacement Dedicated Host is allocated to the owner's account\. It is added to the same resource shares as the original Dedicated Host, and it is shared with the same consumers\.

For more information, see [Host recovery](dedicated-hosts-recovery.md)\.
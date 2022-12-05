# Share a placement group<a name="share-placement-group"></a>

Placement group sharing allows you to influence the placement of interdependent instances that are owned by separate AWS accounts\. You can share a placement group across multiple AWS accounts or within your AWS Organizations\. You can launch instances in a shared placement group\.

A placement group owner can share a placement group with:
+ Specific AWS accounts inside or outside of its AWS organization
+ An organizational unit inside its AWS organization
+ Its entire AWS organization

**Note**  
 The AWS account from which you want to share a placement group must have the following permissions in the IAM policy\.   
`ec2:PutResourcePolicy`
`ec2:DeleteResourcePolicy`

**Topics**
+ [Rules and limitations](#share-placement-group-limitations)
+ [Share across Availability Zones](#share-placement-group-sharing-azs)
+ [Share a placement group](#share-placement-group-share)
+ [Identify a shared placement group](#share-placement-group-identify)
+ [Launch an instance in a shared placement group](#share-placement-group-launch)
+ [Unshare a shared placement group](#share-placement-group-unshare)

## Rules and limitations<a name="share-placement-group-limitations"></a>

The following rules and limitations apply when you share a placement group or when a placement group is shared with you\.
+ To share a placement group, you must own it in your AWS account\. You cannot share a placement group that has been shared with you\.
+ When you share a partition or spread placement group, the placement group limits do not change\. A shared partition placement group supports a maximum of seven partitions per Availability Zone, and a shared spread placement group supports a maximum of seven running instances per Availability Zone\.
+ To share a placement group with your AWS organization or an organizational unit in your AWS organization, you must enable sharing with AWS Organizations\. For more information, see [Sharing your AWS resources](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html)\.
+ You are responsible for managing the instances owned by you in a shared placement group\.
+ You cannot view or modify instances and capacity reservations that are associated with a shared placement group but not owned by you\.

## Share across Availability Zones<a name="share-placement-group-sharing-azs"></a>

To ensure that resources are distributed across the Availability Zones for a Region, we independently map Availability Zones to names for each account\. This could lead to Availability Zone naming differences across accounts\. For example, the Availability Zone `us-east-1a` for your AWS account might not have the same location as `us-east-1a` for another AWS account\.

To identify the location of your Dedicated Hosts relative to your accounts, you must use the *Availability Zone ID* \(AZ ID\)\. The Availability Zone ID is a unique and consistent identifier for an Availability Zone across all AWS accounts\. For example, `use1-az1` is an Availability Zone ID for the `us-east-1` Region and it is the same location in every AWS account\.

**To view the Availability Zone IDs for the Availability Zones in your account:**

1. Open the AWS RAM console at [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/)\.

1. The Availability Zone IDs for the current Region are displayed under **Your AZ ID** in the right panel\.

## Share a placement group<a name="share-placement-group-share"></a>

To share a placement group, you must add it to a resource share\. A resource share is an AWS RAM resource that lets you share your resources across AWS accounts\. A resource share specifies the resources to share, and the consumers with whom they are shared\.

If you are part of an organization in AWS Organizations sharing within your organization is enabled, consumers in your organization are granted access to the shared placement group\.

If the placement group is shared with an AWS account outside of your organization, the AWS account owner will receive an invitation to join the resource share\. They can access the shared placement group after accepting the invitation\.

You can share a placement group across AWS accounts using [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/) or AWS CLI\.

------
#### [ AWS RAM console ]

To **share a placement group** you own using [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/), see [Creating a resource share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing-create.html)\.

------
#### [ AWS CLI ]

To **share a placement group** you own, use the **[create\-resource\-share](https://docs.aws.amazon.com/cli/latest/reference/ram/create-resource-share.html)** command\.

------

## Identify a shared placement group<a name="share-placement-group-identify"></a>

You can identify a placement group shared that is shared with you via [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/) or AWS CLI\.

------
#### [ Amazon EC2 console ]

1. Open [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Placement Groups**\.

1. The **Placement Groups** screen on the right lists all the placement groups owned by you and shared with you\. The **Amazon Resource Name \(ARN\)** of a placement group contains the details of its owner\.

------
#### [ AWS CLI ]

The [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-placement-groups.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-placement-groups.html) command lists all the placement groups owned by you and shared with you\. The **Amazon Resource Name \(ARN\)** of a placement group contains the details of its owner\.

------

## Launch an instance in a shared placement group<a name="share-placement-group-launch"></a>

**Important**  
You must specify the Placement Group **Group Id** to launch an instance in a shared placement group\.

You can use the Placement Group Name only if you are the owner of the placement group being shared\. We recommend using the Placement Group **Group Id** to avoid potential placement group name collisions between AWS accounts\.

You can find the Group Id of a placement group using the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-placement-groups.html ](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-placement-groups.html ) command or in the [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/) on the **Placement Groups** screen under **Network & Security**\.

------
#### [ Amazon EC2 Console ]<a name="launch_cluster_instance"></a>

**To launch instances into a placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Choose **Launch Instance**\. Complete the wizard as directed, taking care to do the following:
   + On the **Choose an Instance Type** page, select an instance type that can be launched into a placement group\.
   + On the **Configure Instance Details** page, the following fields are applicable to placement groups:
     + For **Number of instances**, enter the total number of instances that you need in this placement group, because you might not be able to add instances to the placement group later\.
     + For **Placement group**, select the **Add instance to placement group** check box\. If you do not see **Placement group** on this page, verify that you have selected an instance type that can be launched into a placement group\. Otherwise, this option is not available\.
     + For **Placement group name**, you can choose to add the instances to an existing placement group or to a new placement group that you create\.
     + For **Placement group strategy**, choose the appropriate strategy\. If you choose **partition**, for **Target partition**, choose **Auto distribution** to have Amazon EC2 do a best effort to distribute the instances evenly across all the partitions in the group\. Alternatively, specify the partition in which to launch the instances\.

------
#### [ AWS CLI ]

**To launch instances in a shared placement group**

Use the `[run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)` command and specify the placement group **Group Id**\.

```
aws ec2 run-instances —placement "GroupId = pg-01234567891011121"
```

**To launch instances into a specific partition of a shared partition placement group**

Use the `[run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)` command and specify the **Group Id** and **PartitionNumber** of the shared placement group\.

```
aws ec2 run-instances —placement "GroupId = pg-01234567891011121, PartitionNumber = 3"
```

------

**Tip**  
Use VPC peering to connect instances owned by separate AWS accounts and get the full latency benefits offered by shared cluster placement groups\. For more information, see [What is VPC peering?](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)

## Unshare a shared placement group<a name="share-placement-group-unshare"></a>

The placement group owner can unshare a shared placement group at any time\.

When you unshare a shared placement group, the following changes will take effect\.
+ The AWS accounts with which a placement group was shared will no longer be able to launch instances or reserve capacity\.
+ If your instances were running in a shared placement group, they will be disassociated from the placement group but continue to run normally in your AWS account\.
+ If you had capacity reservations in a shared placement group, they will be disassociated from the placement group but you will continue to have access to them in your AWS account\.

You can unshare a shared placement group using one of the following methods\.

------
#### [ AWS RAM console ]

To unshare a shared placement group using [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/), see [Deleting a resource share](https://docs.aws.amazon.com/ram/latest/userguide/working-with-sharing-delete.html)\.

------
#### [ AWS CLI ]

To unshare a shared placement group using AWS Command Line Interface, use the [https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html](https://docs.aws.amazon.com/cli/latest/reference/ram/disassociate-resource-share.html) command\.

------
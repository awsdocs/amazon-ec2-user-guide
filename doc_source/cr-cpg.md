# Capacity Reservations in cluster placement groups<a name="cr-cpg"></a>

You can create Capacity Reservations in a cluster placement group to reserve Amazon EC2 compute capacity for your workloads\. Cluster placement groups offer the benefit of low network latency and high network throughput\.

Creating a Capacity Reservation in a cluster placement group ensures that you have access to compute capacity in your cluster placement groups when you need it, for as long as you need it\. This is ideal for reserving capacity for high\-performance \(HPC\) workloads that require compute scaling\. It allows you to scale your cluster down while ensuring that the capacity remains available for your use so that you can scale back up when needed\.

**Topics**
+ [Limitations](#cr-cpg-limitations)
+ [Work with Capacity Reservations in cluster placement groups](#work-with-crs-cpgs)

## Limitations<a name="cr-cpg-limitations"></a>

Keep the following in mind when creating Capacity Reservations in cluster placement groups:
+ You can't modify an existing Capacity Reservation that is not in a placement group to reserve capacity in a placement group\. To reserve capacity in a placement group, you must create the Capacity Reservation in the placement group\.
+ After you create a Capacity Reservation in a placement group, you can't modify it to reserve capacity outside of the placement group\.
+ You can increase your reserved capacity in a placement group by modifying an existing Capacity Reservation in the placement group, or by creating additional Capacity Reservations in the placement group\. However, you increase your chances of getting an insufficient capacity error\.
+ You can't share Capacity Reservations that have been created in a cluster placement group\.
+ You can't delete a cluster placement group that has `active` Capacity Reservations\. You must cancel all Capacity Reservations in the cluster placement group before you can delete it\.

## Work with Capacity Reservations in cluster placement groups<a name="work-with-crs-cpgs"></a>

To start using Capacity Reservations with cluster placement groups, perform the following steps\.

**Note**  
If you want to create a Capacity Reservation in an existing cluster placement group, skip Step 1\. Then for Steps 2 and 3, specify the ARN of the existing cluster placement group\. For more information about how to find the ARN of your existing cluster placement group, see [Describe a placement group](placement-groups.md#describe-placement-group)\.

**Topics**
+ [Step 1: \(*Conditional*\) Create a cluster placement group for use with a Capacity Reservation](#create-cpg)
+ [Step 2: Create a Capacity Reservation in a cluster placement group](#create-cr-in-cpg)
+ [Step 3: Launch instances into the cluster placement group](#launch-instance-into-cpg)

### Step 1: \(*Conditional*\) Create a cluster placement group for use with a Capacity Reservation<a name="create-cpg"></a>

Perform this step only if you need to create a new cluster placement group\. To use an existing cluster placement group, skip this step and then for Steps 2 and 3, use the ARN of that cluster placement group\. For more information about how to find the ARN of your existing cluster placement group, see [Describe a placement group](placement-groups.md#describe-placement-group)\.

You can create the cluster placement group using one of the following methods\.

------
#### [ Console ]

**To create a cluster placement group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Placement Groups**, and then choose **Create placement group**\.

1. For **Name**, specify a descriptive name for the placement group\.

1. For **Placement strategy**, choose **Cluster**\.

1. Choose **Create group**\.

1. Find the ARN of the cluster placement group that you created\.

   ```
   $ aws ec2 describe-placement-groups --group-names placement_group_name
   ```

   Make a note of the placement group ARN returned in the command output, because you'll need it for the next step\.

------
#### [ AWS CLI ]

**To create a cluster placement group using the AWS CLI**  
Use the [create\-placement\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-placement-group.html) command\. For `--group-name`, specify a descriptive name for the placement group, and for `--strategy`, specify `cluster`\.

The following example creates a placement group named `MyPG` that uses the `cluster` placement strategy\.

```
$ aws ec2 create-placement-group \
--group-name MyPG \
--strategy cluster
```

Make a note of the placement group ARN returned in the command output, because you'll need it for the next step\.

------

### Step 2: Create a Capacity Reservation in a cluster placement group<a name="create-cr-in-cpg"></a>

You create a Capacity Reservation in a cluster placement group in the same way that you create any Capacity Reservation\. However, you must also specify the ARN of the cluster placement group in which to create the Capacity Reservation\. For more information, see [Create a Capacity Reservation](capacity-reservations-using.md#capacity-reservations-create)\.

**Considerations**
+ The specified cluster placement group must be in the `available` state\. If the cluster placement group is in the `pending`, `deleting`, or `deleted` state, the request fails\.
+ The Capacity Reservation and the cluster placement group must be in the same Availability Zone\. If the request to create the Capacity Reservation specifies an Availability Zone that is different from that of the cluster placement group, the request fails\.
+ You can create Capacity Reservations only for instance types that are supported by cluster placement groups\. If you specify an unsupported instance type, the request fails\. For more information, see [Cluster placement group rules and limitations](placement-groups.md#placement-groups-limitations-cluster)\.
+ If you create an `open` Capacity Reservation in a cluster placement group and there are existing running instances that have matching attributes \(placement group ARN, instance type, Availability Zone, platform, and tenancy\), those instances automatically run in the Capacity Reservation\.
+ Your request to create a Capacity Reservation could fail if one of the following is true:
  + Amazon EC2 does not have sufficient capacity to fulfill the request\. Either try again at a later time, try a different Availability Zone, or try a smaller capacity\. If your workload is flexible across instance types and sizes, try different instance attributes\.
  + The requested quantity exceeds your On\-Demand Instance limit for the selected instance family\. Increase your On\-Demand Instance limit for the instance family and try again\. For more information, see [On\-Demand Instance limits](ec2-on-demand-instances.md#ec2-on-demand-instances-limits)\.

You can create the Capacity Reservation in the cluster placement group using one of the following methods\.

------
#### [ Console ]

**To create a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations**, and then choose **Create Capacity Reservation**\.

1. On the Create a Capacity Reservation page, configure the instance type, platform, Availability Zone, Tenancy, quantity, and end date as needed\.

1. For **Placement group ARN**, specify the ARN of the cluster placement group in which to create the Capacity Reservation\.

1. Choose **Create**\.

For more information, see [Create a Capacity Reservation](capacity-reservations-using.md#capacity-reservations-create)\.

------
#### [ AWS CLI ]

**To create a Capacity Reservation using the AWS CLI**  
Use the [create\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-capacity-reservation.html) command\. For `--placement-group-arn`, specify the ARN of the cluster placement group in which to create the Capacity Reservation\.

```
$ aws ec2 create-capacity-reservation \
--instance-type instance_type \
--instance-platform platform \
--availability-zone az \
--instance-count quantity \
--placement-group-arn placement_group_ARN
```

For more information, see [Create a Capacity Reservation](capacity-reservations-using.md#capacity-reservations-create)\.

------

### Step 3: Launch instances into the cluster placement group<a name="launch-instance-into-cpg"></a>

You launch an instance into a Capacity Reservation in a cluster placement group in the same way that you launch an instance into any Capacity Reservation\. However, you must also specify the ARN of the cluster placement group in which to launch the instance\. For more information, see [Create a Capacity Reservation](capacity-reservations-using.md#capacity-reservations-launch)\.

**Considerations**
+ If the Capacity Reservation is `open`, you do not need to specify the Capacity Reservation in the instance launch request\. If the instance has attributes \(placement group ARN, instance type, Availability Zone, platform, and tenancy\) that match a Capacity Reservation in the specified placement group, the instance automatically runs in the Capacity Reservation\.
+ If the Capacity Reservation accepts only targeted instance launches, you must specify the target Capacity Reservation in addition to the cluster placement group in the request\.
+ If the Capacity Reservation is in a Capacity Reservation group, you must specify the target Capacity Reservation group in addition to the cluster placement group in the request\. For more information, see [Work with Capacity Reservation groups](create-cr-group.md)\.

You can launch an instance into a Capacity Reservation in a cluster placement group using one of the following methods\.

------
#### [ Console ]

**To launch instances into an existing Capacity Reservation using the console**

1. Open the Launch Instance wizard by choosing **Launch Instances** from the **Dashboard** or from the **Instances** screen\.

1. Select an Amazon Machine Image \(AMI\) and an instance type\.

1. Complete the **Configure Instance Details** page:

   1. For **Placement group**, select **Add instance to placement group**, choose **Add to existing placement group**, and then select the cluster placement group in which to launch the instance\.

   1. For **Capacity Reservation**, choose one of the following options depending on the configuration of the Capacity Reservation:
      + **Open** — To launch the instances into any `open` Capacity Reservation in the cluster placement group that has matching attributes and sufficient capacity\.
      + **Target by ID** — To launch the instances into a Capacity Reservation that accepts only targeted instance launches\.
      + **Target by group** — To launch the instances into any Capacity Reservation with matching attributes and available capacity in the selected Capacity Reservation group\.

1. Complete the remaining steps to launch the instances\.

For more information, see [Launch instances into an existing Capacity Reservation](capacity-reservations-using.md#capacity-reservations-launch)\.

------
#### [ AWS CLI ]

**To launch instances into an existing Capacity Reservation using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. If you need to target a specific Capacity Reservation or a Capacity Reservation group, specify the `--capacity-reservation-specification` parameter\. For `--placement`, specify the `GroupName` parameter and then specify the name of the placement group that you created in the previous steps\.

The following command launches an instance into a `targeted` Capacity Reservation in a cluster placement group\.

```
$ aws ec2 run-instances \
--image-id ami_id \
--count quantity \
--instance-type instance_type \
--key-name key_pair_name \
--subnet-id subnetid \
--capacity-reservation-specification CapacityReservationTarget={CapacityReservationId=capacity_reservation_id} \
--placement "GroupName=cluster_placement_group_name"
```

For more information, see [Launch instances into an existing Capacity Reservation](capacity-reservations-using.md#capacity-reservations-launch)\.

------
# Work with Capacity Reservations<a name="capacity-reservations-using"></a>

To start using Capacity Reservations, you create the capacity reservation in the required Availability Zone\. Then, you can launch instances into the reserved capacity, view its capacity utilization in real time, and increase or decrease its capacity as needed\. 

By default, Capacity Reservations automatically match new instances and running instances that have matching attributes \(instance type, platform, and Availability Zone\)\. This means that any instance with matching attributes automatically runs in the Capacity Reservation\. However, you can also target a Capacity Reservation for specific workloads\. This enables you to explicitly control which instances are allowed to run in that reserved capacity\.

You can specify how the reservation ends\. You can choose to cancel the Capacity Reservation or end it automatically at a specified time\. If you specify an end time, the Capacity Reservation is canceled within an hour of the specified time\. For example, if you specify 5/31/2019, 13:30:55, the Capacity Reservation is guaranteed to end between 13:30:55 and 14:30:55 on 5/31/2019\. After a reservation ends, you can no longer target instances to the Capacity Reservation\. Instances running in the reserved capacity continue to run uninterrupted\. If instances targeting a Capacity Reservation are stopped, you cannot restart them until you remove their Capacity Reservation targeting preference or configure them to target a different Capacity Reservation\.

**Contents**
+ [Create a Capacity Reservation](#capacity-reservations-create)
+ [Work with Capacity Reservation groups](#create-cr-group)
+ [Launch instances into an existing Capacity Reservation](#capacity-reservations-launch)
+ [Modify a Capacity Reservation](#capacity-reservations-modify)
+ [Modify an instance's Capacity Reservation settings](#capacity-reservations-modify-instance)
+ [View a Capacity Reservation](#capacity-reservations-view)
+ [Cancel a Capacity Reservation](#capacity-reservations-release)

## Create a Capacity Reservation<a name="capacity-reservations-create"></a>

After you create the Capacity Reservation, the capacity is available immediately\. The capacity remains reserved for your use as long as the Capacity Reservation is active, and you can launch instances into it at any time\. If the Capacity Reservation is open, new instances and existing instances that have matching attributes automatically run in the capacity of the Capacity Reservation\. If the Capacity Reservation is `targeted`, instances must specifically target it to run in the reserved capacity\.

Your request to create a Capacity Reservation could fail if one of the following is true:
+ Amazon EC2 does not have sufficient capacity to fulfill the request\. Either try again at a later time, try a different Availability Zone, or try a smaller capacity\. If your application is flexible across instance types and sizes, try different instance attributes\.
+ The requested quantity exceeds your On\-Demand Instance limit for the selected instance family\. Increase your On\-Demand Instance limit for the instance family and try again\. For more information, see [On\-Demand Instance limits](ec2-on-demand-instances.md#ec2-on-demand-instances-limits)\.

**To create a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations**, and then choose **Create Capacity Reservation**\.

1. On the Create a Capacity Reservation page, configure the following settings in the **Instance details** section\. The instance type, platform, and Availability Zone of the instances that you launch must match the instance type, platform, and Availability Zone that you specify here or the Capacity Reservation is not applied\. For example, if an open Capacity Reservation doesn't match, an instance launch that targets that Capacity Reservation explicitly will fail\.

   1. **Instance Type**—The type of instance to launch into the reserved capacity\.

   1. **Launch EBS\-optimized instances**—Specify whether to reserve the capacity for EBS\-optimized instances\. This option is selected by default for some instance types\. For more information about EBS\-optimized instances, see [Amazon Elastic Block Store \(Amazon EBS\)](AmazonEBS.md)\.

   1. **Attach instance store at launch**—Specify whether instances launched into the Capacity Reservation use temporary block\-level storage\. The data on an instance store volume persists only during the life of the associated instance\.

   1. **Platform**—The operating system for your instances\.  For more information, see [Supported platforms](ec2-capacity-reservations.md#capacity-reservations-platforms)\. For more information about the supported Windows platforms, see [ Supported platforms](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-capacity-reservations.html#capacity-reservations-platforms) in the *Amazon EC2 User Guide for Windows Instances*\.

   1. **Availability Zone**—The Availability Zone in which to reserve the capacity\.

   1. **Tenancy**—Specify whether to run on shared hardware \(default\) or a dedicated instance\.

   1. **Quantity**—The number of instances for which to reserve capacity\. If you specify a quantity that exceeds your remaining On\-Demand Instance limit for the selected instance type, the request is denied\.

1. Configure the following settings in the **Reservation details** section:

   1. **Reservation Ends**—Choose one of the following options:
      + **Manually**—Reserve the capacity until you explicitly cancel it\.
      + **Specific time**—Cancel the capacity reservation automatically at the specified date and time\.

   1. **Instance eligibility**—Choose one of the following options:
      + **open**—\(Default\) The Capacity Reservation matches any instance that has matching attributes \(instance type, platform, and Availability Zone\)\. If you launch an instance with matching attributes, it is placed into the reserved capacity automatically\.
      + **targeted**—The Capacity Reservation only accepts instances that have matching attributes \(instance type, platform, and Availability Zone\), and that explicitly target the reservation\.

1. Choose **Request reservation**\.

**To create a Capacity Reservation using the AWS CLI**  
Use the [create\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-capacity-reservation.html) command\. For more information, see [Supported platforms](ec2-capacity-reservations.md#capacity-reservations-platforms)\. For more information about the supported Windows platforms, see [ Supported platforms](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-capacity-reservations.html#capacity-reservations-platforms) in the *Amazon EC2 User Guide for Windows Instances*\.

For example, the following command creates a Capacity Reservation that reserves capacity for three `m5.2xlarge` instances running Red Hat Enterprise Linux AMIs in the `us-east-1a` Availability Zone\.

```
aws ec2 create-capacity-reservation --instance-type m5.2xlarge --instance-platform Red Hat Enterprise Linux --availability-zone us-east-1a --instance-count 3
```

## Work with Capacity Reservation groups<a name="create-cr-group"></a>

You can use AWS Resource Groups to create logical collections of Capacity Reservations, called *resource groups*\. A resource group is a logical grouping of AWS resources that are all in the same AWS Region\. You can include multiple Capacity Reservations that have different attributes \(instance type, platform, and Availability Zone\) in a single resource group\.

When you create resource groups for your Capacity Reservations, you can target instances to a group of Capacity Reservations instead of an individual Capacity Reservation\. Instances that target a group of Capacity Reservations match with any Capacity Reservation in the group that has matching attributes \(instance type, platform, and Availability Zone\) and available capacity\. If the group does not have a Capacity Reservation with matching attributes and available capacity, the instances run using On\-Demand capacity\. If a matching Capacity Reservation is added to the targeted group at a later stage, the instance is automatically matched with and moved into its reserved capacity\.

To prevent unintended use of Capacity Reservations in a group, configure the Capacity Reservations in the group to accept only instances that explicitly target the capacity reservation\. To do this, set **Instance eligibility** to **targeted** \(old console\) or **Only instances that specify this reservation** \(new console\) when creating the Capacity Reservation using the Amazon EC2 console\. When using the AWS CLI, specify `--instance-match-criteria targeted` when creating the Capacity Reservation\. Doing this ensures that only instances that explicitly target the group, or a Capacity Reservation in the group, can run in the group\.

If a Capacity Reservation in a group is canceled or expires while it has running instances, the instances are automatically moved to another Capacity Reservation in the group that has matching attributes and available capacity\. If there are no remaining Capacity Reservations in the group that have matching attributes and available capacity, the instances run in On\-Demand capacity\. If a matching Capacity Reservation is added to the targeted group at a later stage, the instance is automatically moved into its reserved capacity\.

**To create a group for your Capacity Reservations**  
Use the [create\-group](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/create-group.html) AWS CLI command\. For `name`, provide a descriptive name for the group, and for `configuration`, specify two `Type` request parameters:
+ `AWS::EC2::CapacityReservationPool` to ensure that the resource group can be targeted for instance launches
+ `AWS::ResourceGroups::Generic` with `allowed-resource-types` set to `AWS::EC2::CapacityReservation` to ensure that the resource group accepts Capacity Reservations only

For example, the following command creates a group named `MyCRGroup`\.

```
$ aws resource-groups create-group --name MyCRGroup --configuration '{"Type":"AWS::EC2::CapacityReservationPool"}' '{"Type":"AWS::ResourceGroups::Generic", "Parameters": [{"Name": "allowed-resource-types", "Values": ["AWS::EC2::CapacityReservation"]}]}'
```

The following shows example output\.

```
{
    "GroupConfiguration": {
        "Status": "UPDATE_COMPLETE",
        "Configuration": [
            {
                "Type": "AWS::EC2::CapacityReservationPool"
            },
            {
                "Type": "AWS::ResourceGroups::Generic",
                "Parameters": [
                    {
                        "Values": [
                            "AWS::EC2::CapacityReservation"
                        ],
                        "Name": "allowed-resource-types"
                    }
                ]
            }
        ]
    },
    "Group": {
        "GroupArn": "arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup",
        "Name": "MyCRGroup"
    }
}
```

**To add a Capacity Reservation to a group**  
Use the [group\-resources](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/group-resources.html) AWS CLI command\. For `group`, specify the name of the group to which to add the Capacity Reservations, and for `resources`, specify ARNs of the Capacity Reservations to add\. To add multiple Capacity Reservations, separate the ARNs with a space\. To get the ARNs of the Capacity Reservations to add, use the [ describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) AWS CLI command and specify the IDs of the Capacity Reservations\.

For example, the following command adds two Capacity Reservations to a group named `MyCRGroup`\.

```
$ aws resource-groups group-resources --group MyCRGroup --resource-arns arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-1234567890abcdef1 arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-54321abcdef567890
```

The following shows example output\.

```
{
    "Failed": [],
    "Succeeded": [
        "arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-1234567890abcdef1",
        "arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-54321abcdef567890"
    ]
}
```

**To view the Capacity Reservations in a specific group**  
Use the [list\-group\-resources](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/list-group-resources.html) AWS CLI command\. For `group`, specify the name of the group\.

For example, the following command lists the Capacity Reservations in a group named `MyCRGroup`\.

```
$ aws resource-groups list-group-resources --group MyCRGroup
```

The following shows example output\.

```
{
    "QueryErrors": [],
    "ResourceIdentifiers": [
        {
            "ResourceType": "AWS::EC2::CapacityReservation",
            "ResourceArn": "arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-1234567890abcdef1"
        },
        {
            "ResourceType": "AWS::EC2::CapacityReservation",
            "ResourceArn": "arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-54321abcdef567890"
        }
    ]
}
```

**To view the groups to which a specific Capacity Reservation has been added \(AWS CLI\)**  
Use the [get\-groups\-for\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-groups-for-capacity-reservation.html) AWS CLI command\. 

For example, the following command lists the groups to which Capacity Reservation `cr-1234567890abcdef1` has been added\.

```
$ aws ec2 get-groups-for-capacity-reservation --capacity-reservation-id cr-1234567890abcdef1
```

The following shows example output\.

```
{
    "CapacityReservationGroups": [
        {
            "OwnerId": "123456789012",
            "GroupArn": "arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup"
        }
    ]
}
```

**To view the groups to which a specific Capacity Reservation has been added \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Capacity Reservations**, select the Capacity Reservation to view, and then choose **View**\.

   The groups to which the Capacity Reservation has been added are listed in the **Groups** card\.

**To remove a Capacity Reservation from a group**  
Use the [ungroup\-resources](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/ungroup-resources.html) AWS CLI command\. For `group`, specify the ARN of the group from which to remove the Capacity Reservation, and for `resources` specify the ARNs of the Capacity Reservations to remove\. To remove multiple Capacity Reservations, separate the ARNs with a space\.

The following example removes two Capacity Reservations from a group named `MyCRGroup`\.

```
$ aws resource-groups ungroup-resources --group MyCRGroup --resource-arns arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-0e154d26a16094dd arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-54321abcdef567890
```

The following shows example output\.

```
{
    "Failed": [],
    "Succeeded": [
        "arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-0e154d26a16094dd",
        "arn:aws:ec2:sa-east-1:123456789012:capacity-reservation/cr-54321abcdef567890"
    ]
}
```

**To delete a group**  
Use the [delete\-group](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/delete-group.html) AWS CLI command\. For `group`, provide the name of the group to delete\.

For example, the following command deletes a group named `MyCRGroup`\.

```
$ aws resource-groups delete-group --group MyCRGroup
```

The following shows example output\.

```
{
    "Group": {
        "GroupArn": "arn:aws:resource-groups:sa-east-1:123456789012:group/MyCRGroup",
        "Name": "MyCRGroup"
    }
}
```

## Launch instances into an existing Capacity Reservation<a name="capacity-reservations-launch"></a>

When you launch an instance, you can specify whether to launch the instance into any `open` Capacity Reservation, into a specific Capacity Reservation, or into a group of Capacity Reservations\. You can only launch an instance into a Capacity Reservation that has matching attributes \(instance type, platform, and Availability Zone\) and sufficient capacity\. Alternatively, you can configure the instance to avoid running in a Capacity Reservation, even if you have an `open` Capacity Reservation that has matching attributes and available capacity\. 

Launching an instance into a Capacity Reservation reduces its available capacity by the number of instances launched\. For example, if you launch three instances, the available capacity of the Capacity Reservation is reduced by three\.

**To launch instances into an existing Capacity Reservation using the console**

1. Open the Launch Instance wizard by choosing **Launch Instances** from **Dashboard** or **Instances**\.

1. Select an Amazon Machine Image \(AMI\) and an instance type\.

1. Complete the **Configure Instance Details** page\. For **Capacity Reservation**, choose one of the following options:
   + **None** — Prevents the instances from launching into a Capacity Reservation\. The instances run in On\-Demand capacity\.
   + **Open** — Launches the instances into any Capacity Reservation that has matching attributes and sufficient capacity for the number of instances you selected\. If there is no matching Capacity Reservation with sufficient capacity, the instance uses On\-Demand capacity\.
   + **Target by ID** — Launches the instances into the selected Capacity Reservation\. If the selected Capacity Reservation does not have sufficient capacity for the number of instances you selected, the instance launch fails\.
   + **Target by group** — Launches the instances into any Capacity Reservation with matching attributes and available capacity in the selected Capacity Reservation group\. If the selected group does not have a Capacity Reservation with matching attributes and available capacity, the instances launch into On\-Demand capacity\.

1. Complete the remaining steps to launch the instances\.

**To launch an instance into an existing Capacity Reservation using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and specify the `--capacity-reservation-specification` parameter\.

The following example launches a `t2.micro` instance into any open Capacity Reservation that has matching attributes and available capacity:

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --subnet-id subnet-1234567890abcdef1 --capacity-reservation-specification CapacityReservationPreference=open
```

The following example launches a `t2.micro` instance into a `targeted` Capacity Reservation:

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --subnet-id subnet-1234567890abcdef1 --capacity-reservation-specification CapacityReservationTarget={CapacityReservationId=cr-a1234567}
```

The following example launches a `t2.micro` instance into a Capacity Reservation group:

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --subnet-id subnet-1234567890abcdef1 --capacity-reservation-specification CapacityReservationTarget={CapacityReservationResourceGroupArn=arn:aws:resource-groups:us-west-1:123456789012:group/my-cr-group}
```

## Modify a Capacity Reservation<a name="capacity-reservations-modify"></a>

You can change the attributes of an active Capacity Reservation after you have created it\. You cannot modify a Capacity Reservation after it has expired or after you have explicitly canceled it\.

When modifying a Capacity Reservation, you can only increase or decrease the quantity and change the way in which it is released\. You cannot change the instance type, EBS optimization, instance store settings, platform, Availability Zone, or instance eligibility of a Capacity Reservation\. If you need to modify any of these attributes, we recommend that you cancel the reservation, and then create a new one with the required attributes\.

If you specify a new quantity that exceeds your remaining On\-Demand Instance limit for the selected instance type, the update fails\.

**To modify a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations**, select the Capacity Reservation to modify, and then choose **Edit**\.

1. Modify the **Quantity** or **Reservation ends** options as needed, and choose **Save changes**\.

**To modify a Capacity Reservation using the AWS CLI**  
Use the [modify\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-capacity-reservations.html) command:

For example, the following command modifies a Capacity Reservation to reserve capacity for eight instances\.

```
aws ec2 modify-capacity-reservation --capacity-reservation-id cr-1234567890abcdef0 --instance-count 8
```

## Modify an instance's Capacity Reservation settings<a name="capacity-reservations-modify-instance"></a>

You can modify the following Capacity Reservation settings for a stopped instance at any time:
+ Start in any Capacity Reservation that has matching attributes \(instance type, platform, and Availability Zone\) and available capacity\.
+ Start the instance in a specific Capacity Reservation\.
+ Start in any Capacity Reservation that has matching attributes and available capacity in a Capacity Reservation group
+ Prevent the instance from starting in a Capacity Reservation\.

**To modify an instance's Capacity Reservation settings using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances** and select the instance to modify\. Stop the instance if it is not already stopped\.

1. Choose **Actions**, **Modify Capacity Reservation Settings**\.

1. For **Capacity Reservation**, choose one of the following options:
   + **Open** — Launches the instances into any Capacity Reservation that has matching attributes and sufficient capacity for the number of instances you selected\. If there is no matching Capacity Reservation with sufficient capacity, the instance uses On\-Demand capacity\.
   + **None** — Prevents the instances from launching into a Capacity Reservation\. The instances run in On\-Demand capacity\.
   + **Specify Capacity Reservation** — Launches the instances into the selected Capacity Reservation\. If the selected Capacity Reservation does not have sufficient capacity for the number of instances you selected, the instance launch fails\.
   + **Specify Capacity Reservation group** — Launches the instances into any Capacity Reservation with matching attributes and available capacity in the selected Capacity Reservation group\. If the selected group does not have a Capacity Reservation with matching attributes and available capacity, the instances launch into On\-Demand capacity\.

**To modify an instance's Capacity Reservation settings using the AWS CLI**  
Use the [modify\-instance\-capacity\-reservation\-attributes](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-capacity-reservation-attributes.html) command\.

For example, the following command changes an instance's Capacity Reservation setting to `open` or `none`\.

```
aws ec2 modify-instance-capacity-reservation-attributes --instance-id i-1234567890abcdef0 --capacity-reservation-specification CapacityReservationPreference=none|open
```

For example, the following command modifies an instance to target a specific Capacity Reservation\.

```
aws ec2 modify-instance-capacity-reservation-attributes --instance-id i-1234567890abcdef0 --capacity-reservation-specification CapacityReservationTarget={CapacityReservationId=cr-1234567890abcdef0}
```

For example, the following command modifies an instance to target a specific Capacity Reservation group\.

```
aws ec2 modify-instance-capacity-reservation-attributes --instance-id i-1234567890abcdef0 --capacity-reservation-specification CapacityReservationTarget={CapacityReservationResourceGroupArn=arn:aws:resource-groups:us-west-1:123456789012:group/my-cr-group}
```

## View a Capacity Reservation<a name="capacity-reservations-view"></a>

Capacity Reservations have the following possible states:
+ `active`—The capacity is available for use\.
+ `expired`—The Capacity Reservation expired automatically at the date and time specified in your reservation request\. The reserved capacity is no longer available for your use\.
+ `cancelled`—The Capacity Reservation was canceled\. The reserved capacity is no longer available for your use\.
+ `pending`—The Capacity Reservation request was successful but the capacity provisioning is still pending\.
+ `failed`—The Capacity Reservation request has failed\. A request can fail due to request parameters that are not valid, capacity constraints, or instance limit constraints\. You can view a failed request for 60 minutes\.

**To view your Capacity Reservations using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations** and select a Capacity Reservation to view\.

1. Choose **View launched instances for this reservation**\.

**To view your Capacity Reservations using the AWS CLI**  
Use the [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) command:

For example, the following command describes all Capacity Reservations\.

```
aws ec2 describe-capacity-reservations
```

## Cancel a Capacity Reservation<a name="capacity-reservations-release"></a>

You can cancel a Capacity Reservation at any time if you no longer need the reserved capacity\. When you cancel a Capacity Reservation, the capacity is released immediately, and it is no longer reserved for your use\.

You can cancel empty Capacity Reservations and Capacity Reservations that have running instances\. If you cancel a Capacity Reservation that has running instances, the instances continue to run normally outside of the capacity reservation at standard On\-Demand Instance rates or at a discounted rate if you have a matching Savings Plan or Regional Reserved Instance\.

After you cancel a Capacity Reservation, instances that target it can no longer launch\. Modify these instances so that they either target a different Capacity Reservation, launch into any open Capacity Reservation with matching attributes and sufficient capacity, or avoid launching into a Capacity Reservation\. For more information, see [Modify an instance's Capacity Reservation settings](#capacity-reservations-modify-instance)\.

**To cancel a Capacity Reservation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Capacity Reservations** and select the Capacity Reservation to cancel\.

1. Choose **Cancel reservation**, **Cancel reservation**\.

**To cancel a Capacity Reservation using the AWS CLI**  
Use the [cancel\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-capacity-reservation.html) command:

For example, the following command cancels a Capacity Reservation with an ID of `cr-1234567890abcdef0`\.

```
aws ec2 cancel-capacity-reservation --capacity-reservation-id cr-1234567890abcdef0
```
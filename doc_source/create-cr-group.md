# Work with Capacity Reservation groups<a name="create-cr-group"></a>

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
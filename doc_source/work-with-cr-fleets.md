# Work with Capacity Reservation Fleets<a name="work-with-cr-fleets"></a>



**Topics**
+ [Before you begin](#before-you-begin)
+ [Capacity Reservation Fleet states](#states)
+ [Create a Capacity Reservation Fleet](#create-crfleet)
+ [View a Capacity Reservation Fleet](#view-crfleet)
+ [Modify a Capacity Reservation Fleet](#modify-crfleet)
+ [Cancel a Capacity Reservation Fleet](#cancle-crfleet)

## Before you begin<a name="before-you-begin"></a>

Before you create a Capacity Reservation Fleet:

1. Determine the amount of compute capacity that is needed by your workload\.

1. Decide on the instance types and Availability Zones that you want to use\.

1. Assign each instance type a priority based on your needs and preferences\. For more information, see [Instance type priority](crfleet-concepts.md#instance-priority)\.

1. Create a capacity weighting system that makes sense for your workload\. Assign a weight to each instance type and determine your total target capacity\. For more information, see [Instance type weight](crfleet-concepts.md#instance-weight) and [Total target capacity](crfleet-concepts.md#target-capacity)\.

1. Determine whether you need the Capacity Reservation indefinitely or only for a specific period of time\.

## Capacity Reservation Fleet states<a name="states"></a>

A Capacity Reservation Fleet can be in one of the following states:
+ `submitted` — The Capacity Reservation Fleet request has been submitted and Amazon EC2 is preparing to create the Capacity Reservations\.
+ `modifying` — The Capacity Reservation Fleet is being modified\. The Fleet remains in this state until the modification is complete\.
+ `active` — The Capacity Reservation Fleet has fulfilled its total target capacity and it is attempting to maintain this capacity\. The Fleet remains in this state until it is modified or deleted\.
+ `partially_fulfilled` — The Capacity Reservation Fleet has partially fulfilled its total target capacity\. There is insufficient Amazon EC2 capacity to fulfill the total target capacity\. The Fleet is attempting to asynchronously fulfill its total target capacity\.
+ `expiring` — The Capacity Reservation Fleet has reached its end date and it is in the process of expiring\. One or more of its Capacity Reservations might still be active\.
+ `expired` — The Capacity Reservation Fleet has reached its end date\. The Fleet and its Capacity Reservations are expired\. The Fleet can't create new Capacity Reservations\.
+ `cancelling` — The Capacity Reservation Fleet is in the process of being cancelled\. One or more of its Capacity Reservations might still be active\.
+ `cancelled` — The Capacity Reservation Fleet has been manually cancelled\. The Fleet and its Capacity Reservations are cancelled and the Fleet can't create new Capacity Reservations\.
+ `failed` — The Capacity Reservation Fleet failed to reserve capacity for the specified instance types\.

## Create a Capacity Reservation Fleet<a name="create-crfleet"></a>

When you create a Capacity Reservation Fleet it automatically creates Capacity Reservations for the instance types specified in the Fleet request, up to the specified total target capacity\. The number of instances for which the Capacity Reservation Fleet reserves capacity depends on the total target capacity and instance type weights that you specify in the request\. For more information, see [Instance type weight](crfleet-concepts.md#instance-weight) and [Total target capacity](crfleet-concepts.md#target-capacity)\.

When you create the Fleet, you must specify the instance types to use and a priority for each of those instance types\. For more information, see [Allocation strategy](crfleet-concepts.md#allocation-strategy) and [Instance type priority](crfleet-concepts.md#instance-priority)\.

**Note**  
The **AWSServiceRoleForEC2CapacityReservationFleet** service\-linked role is automatically created in your account the first time that you create a Capacity Reservation Fleet\. For more information, see [Using Service\-Linked Roles for Capacity Reservation Fleet](using-service-linked-roles.md)\.

Currently, Capacity Reservation Fleets support the `open` instance matching criteria only\.

You can create a Capacity Reservation Fleet using the command line only\.

**To create a Capacity Reservation Fleet**  
Use the [ create\-capacity\-reservation\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-capacity-reservation-fleet.html) AWS CLI command\.

```
$ aws ec2 create-capacity-reservation-fleet \
--total-target-capacity capacity_units \
--allocation-strategy prioritized \
--instance-match-criteria open \
--tenancy dedicated|default \
--end-date yyyy-mm-ddThh:mm:ss.000Z \
--instance-type-specifications file://instanceTypeSpecification.json
```

The following is the contents of `instanceTypeSpecification.json`\.

```
{             
    "InstanceType": "instance_type",                        
    "InstancePlatform":"platform",            
    "Weight": instance_type_weight
    "AvailabilityZone":"availability_zone",
    "AvailabilityZoneId" : "az_id",             
    "EbsOptimized": true|false,            
    "Priority" : instance_type_priority
}
```

Expected output\.

```
{
    "Status": "status", 
    "TotalFulfilledCapacity": fulfilled_capacity, 
    "CapacityReservationFleetId": "cr_fleet_id", 
    "TotalTargetCapacity": capacity_units
}
```

**Example**

```
$ aws ec2 create-capacity-reservation-fleet \
--total-target-capacity 24 \
--allocation-strategy prioritized \
--instance-match-criteria open \
--tenancy default \
--end-date 2021-12-31T23:59:59.000Z \
--instance-type-specifications file://instanceTypeSpecification.json
```

`instanceTypeSpecification.json`

```
[
  {
    "InstanceType": "m5.xlarge",
    "InstancePlatform": "Linux/UNIX",
    "Weight": 3.0,
    "AvailabilityZone":"us-east-1a",
    "EbsOptimized": true,
    "Priority" : 1
  }
]
```

Example output\.

```
{
    "Status": "submitted", 
    "TotalFulfilledCapacity": 0.0, 
    "CapacityReservationFleetId": "crf-abcdef01234567890", 
    "TotalTargetCapacity": 24
}
```

## View a Capacity Reservation Fleet<a name="view-crfleet"></a>

You can view configuration and capacity information for a Capacity Reservation Fleet at any time\. Viewing a Fleet also provides details about the individual Capacity Reservations that are inside the Fleet\.

You can view a Capacity Reservation Fleet using the command line only\.

**To view a Capacity Reservation Fleet**  
Use the [ describe\-capacity\-reservation\-fleets](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservation-fleets.html) AWS CLI command\.

```
$ aws ec2 describe-capacity-reservation-fleets \
--capacity-reservation-fleet-ids cr_fleet_ids
```

Expected output

```
{
    "CapacityReservationFleets": [
        {
            "Status": "status", 
            "EndDate": "yyyy-mm-ddThh:mm:ss.000Z", 
            "InstanceMatchCriteria": "open", 
            "Tags": [], 
            "CapacityReservationFleetId": "cr_fleet_id", 
            "Tenancy": "dedicated|default", 
            "InstanceTypeSpecifications": [
                {
                    "CapacityReservationId": "cr1_id", 
                    "AvailabilityZone": "cr1_availability_zone", 
                    "FulfilledCapacity": cr1_used_capacity, 
                    "Weight": cr1_instance_type_weight, 
                    "CreateDate": "yyyy-mm-ddThh:mm:ss.000Z", 
                    "InstancePlatform": "cr1_platform", 
                    "TotalInstanceCount": cr1_number of instances, 
                    "Priority": cr1_instance_type_priority, 
                    "EbsOptimized": true|false, 
                    "InstanceType": "cr1_instance_type"
                },
				{
                    "CapacityReservationId": "cr2_id", 
                    "AvailabilityZone": "cr2_availability_zone", 
                    "FulfilledCapacity": cr2_used_capacity, 
                    "Weight": cr2_instance_type_weight, 
                    "CreateDate": "yyyy-mm-ddThh:mm:ss.000Z", 
                    "InstancePlatform": "cr2_platform", 
                    "TotalInstanceCount": cr2_number of instances, 
                    "Priority": cr2_instance_type_priority, 
                    "EbsOptimized": true|false, 
                    "InstanceType": "cr2_instance_type"
                },
            ], 
            "TotalTargetCapacity": total_target_capacity, 
            "TotalFulfilledCapacity": total_target_capacity, 
            "CreateTime": "yyyy-mm-ddThh:mm:ss.000Z", 
            "AllocationStrategy": "prioritized"
        }
    ]
}
```

**Example**

```
$ aws ec2 describe-capacity-reservation-fleets \
--capacity-reservation-fleet-ids crf-abcdef01234567890
```

Example output

```
{
    "CapacityReservationFleets": [
        {
            "Status": "active", 
            "EndDate": "2021-12-31T23:59:59.000Z", 
            "InstanceMatchCriteria": "open", 
            "Tags": [], 
            "CapacityReservationFleetId": "crf-abcdef01234567890", 
            "Tenancy": "default", 
            "InstanceTypeSpecifications": [
                {
                    "CapacityReservationId": "cr-1234567890abcdef0", 
                    "AvailabilityZone": "us-east-1a", 
                    "FulfilledCapacity": 5.0, 
                    "Weight": 1.0, 
                    "CreateDate": "2021-07-02T08:34:33.398Z", 
                    "InstancePlatform": "Linux/UNIX", 
                    "TotalInstanceCount": 5, 
                    "Priority": 1, 
                    "EbsOptimized": true, 
                    "InstanceType": "m5.xlarge"
                }
            ], 
            "TotalTargetCapacity": 5, 
            "TotalFulfilledCapacity": 5.0, 
            "CreateTime": "2021-07-02T08:34:33.397Z", 
            "AllocationStrategy": "prioritized"
        }
    ]
}
```

## Modify a Capacity Reservation Fleet<a name="modify-crfleet"></a>

You can modify the total target capacity and date of a Capacity Reservation Fleet at any time\. When you modify the total target capacity of a Capacity Reservation Fleet, the Fleet automatically creates new Capacity Reservations, or modifies or cancels existing Capacity Reservations in the Fleet to meet the new total target capacity\. When you modify the end date for the Fleet, the end dates for all of the individual Capacity Reservations are updated accordingly\.

After you modify a Fleet, its status transitions to `modifying`\. You can't attempt additional modifications to a Fleet while it is in the `modifying` state\.

You can't modify the tenancy, Availability Zone, instance types, instance platforms, priorities, or weights used by a Capacity Reservation Fleet\. If you need to change any of these parameters, you might need to cancel the existing Fleet and create a new one with the required parameters\.

You can modify a Capacity Reservation Fleet using the command line only\.

**To modify a Capacity Reservation Fleet**  
Use the [ modify\-capacity\-reservation\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-capacity-reservation-fleet.html) AWS CLI command\.

**Note**  
You can't specify `--end-date` and `--remove-end-date` in the same command\.

```
$ aws ec2 modify-capacity-reservation-fleet \
--capacity-reservation-fleet-id cr_fleet_ids \
--total-target-capacity capacity_units \
--end-date yyyy-mm-ddThh:mm:ss.000Z \
--remove-end-date
```

Expected output

```
{
    "Return": true
}
```

**Example: Modify total target capacity**

```
$ aws ec2 modify-capacity-reservation-fleet \
--capacity-reservation-fleet-id crf-01234567890abcedf \
--total-target-capacity 160
```

**Example: Modify end date**

```
$ aws ec2 modify-capacity-reservation-fleet \
--capacity-reservation-fleet-id crf-01234567890abcedf \
--end-date 2021-07-04T23:59:59.000Z
```

**Example: Remove end date**

```
$ aws ec2 modify-capacity-reservation-fleet \
--capacity-reservation-fleet-id crf-01234567890abcedf \
--remove-end-date
```

Example output

```
{
    "Return": true
}
```

## Cancel a Capacity Reservation Fleet<a name="cancle-crfleet"></a>

When you no longer need a Capacity Reservation Fleet and the capacity it reserves, you can cancel it\. When you cancel a Fleet, its status changes to `cancelled` and it can no longer create new Capacity Reservations\. Additionally, all of the individual Capacity Reservations in the Fleet are cancelled and the instances that were previously running in the reserved capacity continue to run normally in shared capacity\.

You can cancel a Capacity Reservation Fleet using the command line only\.

**To cancel a Capacity Reservation Fleet**  
Use the [ cancel\-capacity\-reservation\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/cancel-capacity-reservation-fleet.html) AWS CLI command\.

```
$ aws ec2 cancel-capacity-reservation-fleets \
--capacity-reservation-fleet-ids cr_fleet_ids
```

Expected output

```
{
    "SuccessfulFleetCancellations": [
        {
            "CurrentFleetState": "state",
            "PreviousFleetState": "state",
            "CapacityReservationFleetId": "cr_fleet_id_1"
        },
        {
            "CurrentFleetState": "state",
            "PreviousFleetState": "state",
            "CapacityReservationFleetId": "cr_fleet_id_2"
        }
    ],
    "FailedFleetCancellations": [
        {
	        "CapacityReservationFleetId": "cr_fleet_id_3",
	        "CancelCapacityReservationFleetError": [
	            {
		            "Code": "code",
                    "Message": "message"
		        }
	        ]
	    }
    ]
}
```

**Example: Successful cancellation**

```
$ aws ec2 cancel-capacity-reservation-fleets \
--capacity-reservation-fleet-ids crf-abcdef01234567890
```

Example output

```
{
    "SuccessfulFleetCancellations": [
        {
            "CurrentFleetState": "cancelling", 
            "PreviousFleetState": "active", 
            "CapacityReservationFleetId": "crf-abcdef01234567890"
        }
    ], 
    "FailedFleetCancellations": []
}
```
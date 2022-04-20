# Capacity Reservation Fleet concepts<a name="crfleet-concepts"></a>

This topic describes some of the concepts of Capacity Reservation Fleets\.

**Topics**
+ [Total target capacity](#target-capacity)
+ [Allocation strategy](#allocation-strategy)
+ [Instance type weight](#instance-weight)
+ [Instance type priority](#instance-priority)

## Total target capacity<a name="target-capacity"></a>

The *total target capacity* defines the total amount of compute capacity that the Capacity Reservation Fleet reserves\. You specify the total target capacity when you create the Capacity Reservation Fleet\. After the Fleet has been created, Amazon EC2 automatically creates Capacity Reservations to reserve capacity up to the total target capacity\.

The number of instances for which the Capacity Reservation Fleet reserves capacity is determined by the total target capacity and the *instance type weight* that you specify for each instance type in the Capacity Reservation Fleet \(`total target capacity`/`instance type weight`=`number of instances`\)\.

You can assign a total target capacity based on units that are meaningful to your workload\. For example, if your workload requires a certain number of vCPUs, you can assign the total target capacity based on the number of vCPUs required\. If your workload requires `2048` vCPUs, specify a total target capacity of `2048` and then assign instance type weights based on the number of vCPUs provided by the instance types in the Fleet\. For an example, see [Instance type weight](#instance-weight)\.

## Allocation strategy<a name="allocation-strategy"></a>

The allocation strategy for your Capacity Reservation Fleet determines how it fulfills your request for reserved capacity from the instance type specifications in the Capacity Reservation Fleet configuration\.

Currently, only the `prioritized` allocation strategy is supported\. With this strategy, the Capacity Reservation Fleet creates Capacity Reservations using the priorities that you have assigned to each of the instance type specifications in the Capacity Reservation Fleet configuration\. Lower priority values indicate higher priority for use\. For example, say you create a Capacity Reservation Fleet that uses the following instance types and priorities:
+ `m4.16xlarge` — priority = 1
+ `m5.16xlarge` — priority = 3
+ `m5.24xlarge` — priority = 2

The Fleet first attempts to create Capacity Reservations for `m4.16xlarge`\. If Amazon EC2 has insufficient `m4.16xlarge` capacity, the Fleet attempts to create Capacity Reservations for `m5.24xlarge`\. If Amazon EC2 has insufficient `m5.24xlarge` capacity, the Fleet creates Capacity Reservations for `m5.16xlarge`\.

## Instance type weight<a name="instance-weight"></a>

The *instance type weight* is a weight that you assign to each instance type in the Capacity Reservation Fleet\. The weight determines how many units of capacity each instance of that specific instance type counts toward the Fleet's *total target capacity*\.

You can assign weights based on units that are meaningful to your workload\. For example, if your workload requires a certain number of vCPUs, you can assign weights based on the number of vCPUs provided by each instance type in the Capacity Reservation Fleet\. In this case, if you create a Capacity Reservation Fleet using `m4.16xlarge` and `m5.24xlarge` instances, you would assign weights that correspond to the number of vCPUs for each instance as follows:
+ `m4.16xlarge` — `64` vCPUs, weight = `64` units
+ `m5.24xlarge` — `96` vCPUs, weight = `96` units

The instance type weight determines the number of instances for which the Capacity Reservation Fleet reserves capacity\. For example, if a Capacity Reservation Fleet with a total target capacity of `384` units uses the instance types and weights in the preceding example, the Fleet could reserve capacity for `6` `m4.16xlarge` instances \(384 total target capacity/64 instance type weight=6 instances\), or `4` `m5.24xlarge` instances \(384 / 96 = 4\)\.

If you do not assign instance type weights, or if you assign an instance type weight of `1`, the total target capacity is based purely on instance count\. For example, if a Capacity Reservation Fleet with a total target capacity of `384` units uses the instance types in the preceding example, but omits the weights or specifies a weight of `1` for both instance types, the Fleet could reserve capacity for either `384` `m4.16xlarge` instances or `384` `m5.24xlarge` instances\.

## Instance type priority<a name="instance-priority"></a>

The *instance type priority* is a value that you assign to the instance types in the Fleet\. The priorities are used to determine which of the instance types specified for the Fleet should be prioritized for use\.

Lower priority values indicate a higher priority for use\.
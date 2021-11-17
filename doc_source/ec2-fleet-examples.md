# EC2 Fleet example configurations<a name="ec2-fleet-examples"></a>

The following examples show launch configurations that you can use with the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command to create an EC2 Fleet\. For more information about the parameters, see [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) in the *AWS CLI Command Reference*\.

**Topics**
+ [Example 1: Launch Spot Instances as the default purchasing option](#ec2-fleet-config1)
+ [Example 2: Launch On\-Demand Instances as the default purchasing option](#ec2-fleet-config2)
+ [Example 3: Launch On\-Demand Instances as the primary capacity](#ec2-fleet-config3)
+ [Example 4: Launch Spot Instances using the `lowest-price` allocation strategy](#ec2-fleet-config4)
+ [Example 5: Launch On\-Demand Instances using multiple Capacity Reservations](#ec2-fleet-config5)
+ [Example 6: Launch On\-Demand Instances using Capacity Reservations when the total target capacity exceeds the number of unused Capacity Reservations](#ec2-fleet-config6)
+ [Example 7: Launch On\-Demand Instances using targeted Capacity Reservations](#ec2-fleet-config7)
+ [Example 8: Configure Capacity Rebalancing to launch replacement Spot Instances](#ec2-fleet-config9)
+ [Example 9: Launch Spot Instances in a capacity\-optimized fleet](#ec2-fleet-config10)
+ [Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities](#ec2-fleet-config11)

## Example 1: Launch Spot Instances as the default purchasing option<a name="ec2-fleet-config1"></a>

The following example specifies the minimum parameters required in an EC2 Fleet: a launch template, target capacity, and default purchasing option\. The launch template is identified by its launch template ID and version number\. The target capacity for the fleet is 2 instances, and the default purchasing option is `spot`, which results in the fleet launching 2 Spot Instances\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-0e8c754449b27161c",
                "Version": "1"
            }

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 2,
        "DefaultTargetCapacityType": "spot"
    }
}
```

## Example 2: Launch On\-Demand Instances as the default purchasing option<a name="ec2-fleet-config2"></a>

The following example specifies the minimum parameters required in an EC2 Fleet: a launch template, target capacity, and default purchasing option\. The launch template is identified by its launch template ID and version number\. The target capacity for the fleet is 2 instances, and the default purchasing option is `on-demand`, which results in the fleet launching 2 On\-Demand Instances\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-0e8c754449b27161c",
                "Version": "1"
            }

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 2,
        "DefaultTargetCapacityType": "on-demand"
    }
}
```

## Example 3: Launch On\-Demand Instances as the primary capacity<a name="ec2-fleet-config3"></a>

The following example specifies the total target capacity of 2 instances for the fleet, and a target capacity of 1 On\-Demand Instance\. The default purchasing option is `spot`\. The fleet launches 1 On\-Demand Instance as specified, but needs to launch one more instance to fulfill the total target capacity\. The purchasing option for the difference is calculated as `TotalTargetCapacity` – `OnDemandTargetCapacity` = `DefaultTargetCapacityType`, which results in the fleet launching 1 Spot Instance\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-0e8c754449b27161c",
                "Version": "1"
            }

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 2,
        "OnDemandTargetCapacity": 1,
        "DefaultTargetCapacityType": "spot"
    }
}
```

## Example 4: Launch Spot Instances using the `lowest-price` allocation strategy<a name="ec2-fleet-config4"></a>

If the allocation strategy for Spot Instances is not specified, the default allocation strategy, which is `lowest-price`, is used\. The following example uses the `lowest-price` allocation strategy\. The three launch specifications, which override the launch template, have different instance types but the same weighted capacity and subnet\. The total target capacity is 2 instances and the default purchasing option is `spot`\. The EC2 Fleet launches 2 Spot Instances using the instance type of the launch specification with the lowest price\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-0e8c754449b27161c",
                "Version": "1"
            }
		"Overrides": [
        {
          "InstanceType": "c4.large",
          "WeightedCapacity": 1,
          "SubnetId": "subnet-a4f6c5d3"
        },
        {
          "InstanceType": "c3.large",
          "WeightedCapacity": 1,
          "SubnetId": "subnet-a4f6c5d3"
        },
        {
          "InstanceType": "c5.large",
          "WeightedCapacity": 1,
          "SubnetId": "subnet-a4f6c5d3"
        }
      ]

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 2,
        "DefaultTargetCapacityType": "spot"
    }
}
```

## Example 5: Launch On\-Demand Instances using multiple Capacity Reservations<a name="ec2-fleet-config5"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. This example demonstrates how the fleet selects the Capacity Reservations to use when there are more Capacity Reservations than are needed to fulfil the target capacity\.

In this example, the fleet configuration is as follows:
+ Target capacity: 12 On\-Demand Instances
+ Total unused Capacity Reservations: 15 \(more than the fleet's target capacity of 12 On\-Demand Instances\)
+ Number of Capacity Reservation pools: 3 \(`m5.large`, `m4.xlarge`, and `m4.2xlarge`\)
+ Number of Capacity Reservations per pool: 5
+ On\-Demand allocation strategy: `lowest-price` \(When there are multiple unused Capacity Reservations in multiple instance pools, the fleet determines the pools in which to launch the On\-Demand Instances based on the On\-Demand allocation strategy\.\)

  Note that you can also use the `prioritized` allocation strategy instead of the `lowest-price` allocation strategy\.

**Capacity Reservations**

The account has the following 15 unused Capacity Reservations in 3 different pools\. The number of Capacity Reservations in each pool is indicated by `AvailableInstanceCount`\.

```
{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "m5.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-222", 
    "InstanceType": "m4.xlarge", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-333", 
    "InstanceType": "m4.2xlarge", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount":5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
```

**Fleet configuration**

The following fleet configuration shows only the pertinent configurations for this example\. The total target capacity is 12, and the default target capacity type is `on-demand`\. The On\-Demand allocation strategy is `lowest-price`\. The usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. 

In this example, the On\-Demand Instance price is:
+ `m5.large` – $0\.096 per hour
+ `m4.xlarge` – $0\.20 per hour
+ `m4.2xlarge` – $0\.40 per hour

**Note**  
The fleet type must be of type `instant`\. Other fleet types do not support `use-capacity-reservations-first`\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-abc1234567example",
                "Version": "1"
            }
            "Overrides": [
                {
                  "InstanceType": "m5.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1
                },
                {
                  "InstanceType": "m4.xlarge",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1
                },
                {
                  "InstanceType": "m4.2xlarge",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1
                }
              ]

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 12,
        "DefaultTargetCapacityType": "on-demand"
    },
    "OnDemandOptions": {
        "AllocationStrategy": "lowest-price"
        "CapacityReservationOptions": {
            "UsageStrategy": "use-capacity-reservations-first"
        }
    },
    "Type": "instant",
}
```

After you create the `instant` fleet using the preceding configuration, the following 12 instances are launched to meet the target capacity:
+ 5 `m5.large` On\-Demand Instances in `us-east-1a` – `m5.large` in `us-east-1a` is the lowest price, and there are 5 available unused `m5.large` Capacity Reservations
+ 5 `m4.xlarge` On\-Demand Instances in us\-east\-1a – `m4.xlarge` in `us-east-1a` is the next lowest price, and there are 5 available unused `m4.xlarge` Capacity Reservations
+ 2 `m4.2xlarge` On\-Demand Instances in us\-east\-1a – `m4.2xlarge` in `us-east-1a` is the third lowest price, and there are 5 available unused `m4.2xlarge` Capacity Reservations of which only 2 are needed to meet the target capacity

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the `m5.large` and `m4.xlarge` Capacity Reservations were used, with 3 `m4.2xlarge` Capacity Reservations remaining unused\.

```
{
    "CapacityReservationId": "cr-111",
    "InstanceType": "m5.large",  
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-222",
    "InstanceType": "m4.xlarge", 
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-333",
    "InstanceType": "m4.2xlarge", 
    "AvailableInstanceCount": 3
}
```

## Example 6: Launch On\-Demand Instances using Capacity Reservations when the total target capacity exceeds the number of unused Capacity Reservations<a name="ec2-fleet-config6"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. This example demonstrates how the fleet selects the instance pools in which to launch On\-Demand Instances when the total target capacity exceeds the number of available unused Capacity Reservations\.

In this example, the fleet configuration is as follows:
+ Target capacity: 16 On\-Demand Instances
+ Total unused Capacity Reservations: 15 \(less than the fleet's target capacity of 16 On\-Demand Instances\)
+ Number of Capacity Reservation pools: 3 \(`m5.large`, `m4.xlarge`, and `m4.2xlarge`\)
+ Number of Capacity Reservations per pool: 5
+ On\-Demand allocation strategy: `lowest-price` \(When the number of unused Capacity Reservations is less than the On\-Demand target capacity, the fleet determines the pools in which to launch the remaining On\-Demand capacity based on the On\-Demand allocation strategy\.\)

  Note that you can also use the `prioritized` allocation strategy instead of the `lowest-price` allocation strategy\.

**Capacity Reservations**

The account has the following 15 unused Capacity Reservations in 3 different pools\. The number of Capacity Reservations in each pool is indicated by `AvailableInstanceCount`\.

```
{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "m5.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-222", 
    "InstanceType": "m4.xlarge", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-333", 
    "InstanceType": "m4.2xlarge", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount":5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
```

**Fleet configuration**



The following fleet configuration shows only the pertinent configurations for this example\. The total target capacity is 16, and the default target capacity type is `on-demand`\. The On\-Demand allocation strategy is `lowest-price`\. The usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. 

In this example, the On\-Demand Instance price is:
+ m5\.large – $0\.096 per hour
+ m4\.xlarge – $0\.20 per hour
+ m4\.2xlarge – $0\.40 per hour

**Note**  
The fleet type must be `instant`\. Other fleet types do not support `use-capacity-reservations-first`\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-0e8c754449b27161c",
                "Version": "1"
            }
            "Overrides": [
                {
                  "InstanceType": "m5.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1
                },
                {
                  "InstanceType": "m4.xlarge",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1
                },
                {
                  "InstanceType": "m4.2xlarge",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1
                }
              ]

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 16,
        "DefaultTargetCapacityType": "on-demand"
    },
    "OnDemandOptions": {
        "AllocationStrategy": "lowest-price"
        "CapacityReservationOptions": {
            "UsageStrategy": "use-capacity-reservations-first"
        }
    },
    "Type": "instant",
}
```

After you create the `instant` fleet using the preceding configuration, the following 16 instances are launched to meet the target capacity:
+ 6 `m5.large` On\-Demand Instances in `us-east-1a` – `m5.large` in `us-east-1a` is the lowest price, and there are 5 available unused `m5.large` Capacity Reservations\. The Capacity Reservations are used first to launch 5 On\-Demand Instances\. After the remaining `m4.xlarge` and `m4.2xlarge` Capacity Reservations are used, to meet the target capacity an additional On\-Demand Instance is launched according to the On\-Demand allocation strategy, which is `lowest-price` in this example\.
+ 5 `m4.xlarge` On\-Demand Instances in `us-east-1a` – `m4.xlarge` in `us-east-1a` is the next lowest price, and there are 5 available unused `m4.xlarge` Capacity Reservations
+ 5 `m4.2xlarge` On\-Demand Instances in `us-east-1a` – `m4.2xlarge` in `us-east-1a` is the third lowest price, and there are 5 available unused `m4.2xlarge` Capacity Reservations

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the Capacity Reservations in all of the pools were used\.

```
{
    "CapacityReservationId": "cr-111",
    "InstanceType": "m5.large",  
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-222",
    "InstanceType": "m4.xlarge", 
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-333",
    "InstanceType": "m4.2xlarge", 
    "AvailableInstanceCount": 0
}
```

## Example 7: Launch On\-Demand Instances using targeted Capacity Reservations<a name="ec2-fleet-config7"></a>

You can configure a fleet to use `targeted` On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. This example demonstrates how to launch On\-Demand Instances into `targeted` Capacity Reservations, where the attributes of the Capacity Reservations are the same except for their Availability Zones \(`us-east-1a` and `us-east-1b`\)\. It also demonstrates how the fleet selects the instance pools in which to launch On\-Demand Instances when the total target capacity exceeds the number of available unused Capacity Reservations\.

In this example, the fleet configuration is as follows:
+ Target capacity: 10 On\-Demand Instances
+ Total unused `targeted` Capacity Reservations: 6 \(less than the fleet's On\-Demand target capacity of 10 On\-Demand Instances\)
+ Number of Capacity Reservation pools: 2 \(`us-east-1a` and `us-east-1b`\)
+ Number of Capacity Reservations per pool: 3
+ On\-Demand allocation strategy: `lowest-price` \(When the number of unused Capacity Reservations is less than the On\-Demand target capacity, the fleet determines the pools in which to launch the remaining On\-Demand capacity based on the On\-Demand allocation strategy\.\)

  Note that you can also use the `prioritized` allocation strategy instead of the `lowest-price` allocation strategy\.

For a walkthrough of the procedures that you must perform to accomplish this example, see [Tutorial: Launch On\-Demand Instances using targeted Capacity Reservations](ec2-fleet-launch-on-demand-instances-using-targeted-capacity-reservations-walkthrough.md)\.

**Capacity Reservations**

The account has the following 6 unused Capacity Reservations in 2 different pools\. In this example, the pools differ by their Availability Zones\. The number of Capacity Reservations in each pool is indicated by `AvailableInstanceCount`\.

```
{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "c5.xlarge", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 3, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-222", 
    "InstanceType": "c5.xlarge", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1b", 
    "AvailableInstanceCount": 3, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
```

**Fleet configuration**

The following fleet configuration shows only the pertinent configurations for this example\. The total target capacity is 10, and the default target capacity type is `on-demand`\. The On\-Demand allocation strategy is `lowest-price`\. The usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. 

In this example, the On\-Demand Instance price for `c5.xlarge` in `us-east-1` is $0\.17 per hour\.

**Note**  
The fleet type must be `instant`\. Other fleet types do not support `use-capacity-reservations-first`\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateName": "my-launch-template",
                "Version": "1"
            },
            "Overrides": [
               {
                   "InstanceType": "c5.xlarge",
                   "AvailabilityZone": "us-east-1a"
               },
               {
                    "InstanceType": "c5.xlarge",
                    "AvailabilityZone": "us-east-1b"
               }
            ]
        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 10,
        "DefaultTargetCapacityType": "on-demand"
    },
    "OnDemandOptions": {
        "AllocationStrategy": "lowest-price",
        "CapacityReservationOptions": {
             "UsageStrategy": "use-capacity-reservations-first"
         }
    },
    "Type": "instant"
}
```

After you create the `instant` fleet using the preceding configuration, the following 10 instances are launched to meet the target capacity:
+ The Capacity Reservations are used first to launch 6 On\-Demand Instances as follows:
  + 3 On\-Demand Instances are launched into the 3 `c5.xlarge` `targeted` Capacity Reservations in `us-east-1a`
  + 3 On\-Demand Instances are launched into the 3 `c5.xlarge` `targeted` Capacity Reservations in `us-east-1b`
+ To meet the target capacity, 4 additional On\-Demand Instances are launched into regular On\-Demand capacity according to the On\-Demand allocation strategy, which is `lowest-price` in this example\. However, because the pools are the same price \(because price is per Region and not per Availability Zone\), the fleet launches the remaining 4 On\-Demand Instances into either of the pools\.

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the Capacity Reservations in all of the pools were used\.

```
{
    "CapacityReservationId": "cr-111",
    "InstanceType": "c5.xlarge",  
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-222",
    "InstanceType": "c5.xlarge", 
    "AvailableInstanceCount": 0
}
```

## Example 8: Configure Capacity Rebalancing to launch replacement Spot Instances<a name="ec2-fleet-config9"></a>

The following example configures the EC2 Fleet to launch a replacement Spot Instance when Amazon EC2 emits a rebalance recommendation for a Spot Instance in the fleet\. To configure the automatic replacement of Spot Instances, for `ReplacementStrategy`, specify `launch-before-terminate`\. To configure the time delay from when the new replacement Spot Instances are launched to when the old Spot Instances are automatically deleted, for `termination-delay`, specify a value in seconds\. For more information, see [Configuration options](ec2-fleet-capacity-rebalance.md#ec2-fleet-capacity-rebalance-config-options)\.

**Note**  
We recommend using `launch-before-terminate` only if you can predict how long your instance shutdown procedures will take to complete so that the old instances are only terminated after these procedures are completed\. You are charged for all instances while they are running\.

The effectiveness of the Capacity Rebalancing strategy depends on the number of Spot capacity pools specified in the EC2 Fleet request\. We recommend that you configure the fleet with a diversified set of instance types and Availability Zones, and for `AllocationStrategy`, specify `capacity-optimized`\. For more information about what you should consider when configuring an EC2 Fleet for Capacity Rebalancing, see [Capacity Rebalancing](ec2-fleet-capacity-rebalance.md)\.

```
{
    "ExcessCapacityTerminationPolicy": "termination",
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateName": "LaunchTemplate",
                "Version": "1"
            },
                 "Overrides": [
                       {
                           "InstanceType": "c3.large",
                           "WeightedCapacity": 1,
                            "Placement": {
                               "AvailabilityZone": "us-east-1a"
                           }
                       },
                       {
                           "InstanceType": "c4.large",
                           "WeightedCapacity": 1,
                            "Placement": {
                               "AvailabilityZone": "us-east-1a"
                           }
                       },
                       {
                           "InstanceType": "c5.large",
                           "WeightedCapacity": 1,
                            "Placement": {
                               "AvailabilityZone": "us-east-1a"
                           }
                       }
                ] 
          }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 5,
        "DefaultTargetCapacityType": "spot"
    },
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        "MaintenanceStrategies": {
            "CapacityRebalance": {
                "ReplacementStrategy": "launch-before-terminate",
                "TerminationDelay": "720"
            }
        }
    }
}
```

## Example 9: Launch Spot Instances in a capacity\-optimized fleet<a name="ec2-fleet-config10"></a>

The following example demonstrates how to configure an EC2 Fleet with a Spot allocation strategy that optimizes for capacity\. To optimize for capacity, you must set `AllocationStrategy` to `capacity-optimized`\.

In the following example, the three launch specifications specify three Spot capacity pools\. The target capacity is 50 Spot Instances\. The EC2 Fleet attempts to launch 50 Spot Instances into the Spot capacity pool with optimal capacity for the number of instances that are launching\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        },
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateName": "my-launch-template",
                "Version": "1"
            },
                 "Overrides": [
                       {
                           "InstanceType": "r4.2xlarge",
                           "Placement": {
                               "AvailabilityZone": "us-west-2a"
                           },
                      },
                       {
                           "InstanceType": "m4.2xlarge",
                           "Placement": {
                               "AvailabilityZone": "us-west-2b"
                           },
                       }, 
                       {
                           "InstanceType": "c5.2xlarge",
                           "Placement": {
                               "AvailabilityZone": "us-west-2b"
                           }
                       }
                 ] 
           }
    ],
    "TargetCapacitySpecification": {
            "TotalTargetCapacity": 50,
            "DefaultTargetCapacityType": "spot"
}
```

## Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities<a name="ec2-fleet-config11"></a>

The following example demonstrates how to configure an EC2 Fleet with a Spot allocation strategy that optimizes for capacity while using priority on a best\-effort basis\.

When using the `capacity-optimized-prioritized` allocation strategy, you can use the `Priority` parameter to specify the priorities of the Spot capacity pools, where the lower the number the higher priority\. You can also set the same priority for several Spot capacity pools if you favor them equally\. If you do not set a priority for a pool, the pool will be considered last in terms of priority\.

To prioritize Spot capacity pools, you must set `AllocationStrategy` to `capacity-optimized-prioritized`\. EC2 Fleet will optimize for capacity first, but will honor the priorities on a best\-effort basis \(for example, if honoring the priorities will not significantly affect EC2 Fleet's ability to provision optimal capacity\)\. This is a good option for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\.

In the following example, the three launch specifications specify three Spot capacity pools\. Each pool is prioritized, where the lower the number the higher priority\. The target capacity is 50 Spot Instances\. The EC2 Fleet attempts to launch 50 Spot Instances into the Spot capacity pool with the highest priority on a best\-effort basis, but optimizes for capacity first\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized-prioritized"
        },
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateName": "my-launch-template",
                "Version": "1"
            },
                 "Overrides": [
                        {
                           "InstanceType": "r4.2xlarge",    
                           "Priority": 1,
                           "Placement": {
                               "AvailabilityZone": "us-west-2a"
                           },
                      },
                       {
                           "InstanceType": "m4.2xlarge",
                           "Priority": 2,
                           "Placement": {
                               "AvailabilityZone": "us-west-2b"
                           },
                       }, 
                       {
                           "InstanceType": "c5.2xlarge",
                           "Priority": 3,
                           "Placement": {
                               "AvailabilityZone": "us-west-2b"
                           }
                       }
                  ] 
             }
    ],
    "TargetCapacitySpecification": {
            "TotalTargetCapacity": 50,
            "DefaultTargetCapacityType": "spot"
}
```
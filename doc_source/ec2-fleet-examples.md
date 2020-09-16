# EC2 Fleet example configurations<a name="ec2-fleet-examples"></a>

The following examples show launch configurations that you can use with the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command to create an EC2 Fleet\. For more information about the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) parameters, see the [EC2 Fleet JSON configuration file reference](manage-ec2-fleet.md#ec2-fleet-json-reference)\.

**Topics**
+ [Example 1: Launch Spot Instances as the default purchasing option](#ec2-fleet-config1)
+ [Example 2: Launch On\-Demand Instances as the default purchasing option](#ec2-fleet-config2)
+ [Example 3: Launch On\-Demand Instances as the primary capacity](#ec2-fleet-config3)
+ [Example 4: Launch Spot Instances using the `lowest-price` allocation strategy](#ec2-fleet-config4)
+ [Example 5: Launch On\-Demand Instances using Capacity Reservations and the `prioritized` allocation strategy](#ec2-fleet-config5)
+ [Example 6: Launch On\-Demand Instances using Capacity Reservations and the `prioritized` allocation strategy when the total target capacity is more than the number of unused Capacity Reservations](#ec2-fleet-config6)
+ [Example 7: Launch On\-Demand Instances using Capacity Reservations and the `lowest-price` allocation strategy](#ec2-fleet-config7)
+ [Example 8: Launch On\-Demand Instances using Capacity Reservations and the `lowest-price` allocation strategy when the total target capacity is more than the number of unused Capacity Reservations](#ec2-fleet-config8)

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
	"OnDemandTargetCapacity":1,
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

## Example 5: Launch On\-Demand Instances using Capacity Reservations and the `prioritized` allocation strategy<a name="ec2-fleet-config5"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. And if multiple instance pools have unused Capacity Reservations, the chosen On\-Demand allocation strategy is applied\. In this example, the On\-Demand allocation strategy is `prioritized`\.

In this example, there are 15 available unused Capacity Reservations\. This is more than the fleet's target On\-Demand capacity of 12 On\-Demand Instances\.

The account has the following 15 unused Capacity Reservations in 3 different pools\. The number of Capacity Reservations in each pool is indicated by `AvailableInstanceCount`\.

```
{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "c4.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-222", 
    "InstanceType": "c3.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-333", 
    "InstanceType": "c5.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount":5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
```

The following fleet configuration shows only the pertinent configurations for this example\. The On\-Demand allocation strategy is `prioritized`, and the usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. The total target capacity is 12, and the default target capacity type is `on-demand`\.

**Note**  
The fleet type must be `instant`\. Capacity Reservations are not supported for other fleet types\.

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-1234567890abcdefg",
                "Version": "1"
            }
            "Overrides": [
                {
                  "InstanceType": "c4.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1,
                  "Priority": 1.0
                },
                {
                  "InstanceType": "c3.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1,
                  "Priority": 2.0
                },
                {
                  "InstanceType": "c5.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1,
                  "Priority": 3.0
                }
              ]
        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 12,
        "DefaultTargetCapacityType": "on-demand"
    },
    "OnDemandOptions": {
        "AllocationStrategy": "prioritized"
        "CapacityReservationOptions": {
            "UsageStrategy": "use-capacity-reservations-first"
        }
    },
    "Type": "instant", 
}
```

After you create the `instant` fleet using the preceding configuration, the following 12 instances are launched to meet the target capacity:
+ 5 c4\.large On\-Demand Instances in us\-east\-1a – c4\.large in us\-east\-1a is prioritized first, and there are 5 available unused c4\.large Capacity Reservations
+ 5 c3\.large On\-Demand Instances in us\-east\-1a – c3\.large in us\-east\-1a is prioritized second, and there are 5 available unused c3\.large Capacity Reservations
+ 2 c5\.large On\-Demand Instances in us\-east\-1a – c5\.large in us\-east\-1a is prioritized third, and there are 5 available unused c5\.large Capacity Reservations of which only 2 are needed to meet the target capacity

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the c4\.large and c3\.large Capacity Reservations were used, with 3 c5\.large Capacity Reservations remaining unused\.

```
{
    "CapacityReservationId": "cr-111",
    "InstanceType": "c4.large",  
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-222",
    "InstanceType": "c3.large", 
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-333",
    "InstanceType": "c5.large", 
    "AvailableInstanceCount": 3
}
```

## Example 6: Launch On\-Demand Instances using Capacity Reservations and the `prioritized` allocation strategy when the total target capacity is more than the number of unused Capacity Reservations<a name="ec2-fleet-config6"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. And if the number of unused Capacity Reservations is less than the On\-Demand target capacity, the remaining On\-Demand target capacity is launched according to the chosen On\-Demand allocation strategy\. In this example, the On\-Demand allocation strategy is `prioritized`\.

In this example, there are 15 available unused Capacity Reservations\. This is less than the fleet's On\-Demand target capacity of 16 On\-Demand Instances\.

The account has the following 15 unused Capacity Reservations in 3 different pools\. The number of Capacity Reservations in each pool is indicated by `AvailableInstanceCount`\.

```
{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "c4.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "c3.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}

{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "c5.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount":5, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
```

The following fleet configuration shows only the pertinent configurations for this example\. The On\-Demand allocation strategy is `prioritized`, and the usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. The total target capacity is 16, and the default target capacity type is `on-demand`\.

**Note**  
The fleet type must be `instant`\. Capacity Reservations are not supported for other fleet types\.

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
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1,
                  "Priority": 1.0
                },
                {
                  "InstanceType": "c3.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1,
                  "Priority": 2.0
                },
                {
                  "InstanceType": "c5.large",
                  "AvailabilityZone": "us-east-1a",
                  "WeightedCapacity": 1,
                  "Priority": 3.0
                }
              ]

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 16,
        "DefaultTargetCapacityType": "on-demand"
    },
    "OnDemandOptions": {
        "AllocationStrategy": "prioritized"
        "CapacityReservationOptions": {
            "UsageStrategy": "use-capacity-reservations-first"
        }
    },
    "Type": "instant", 
}
```

After you create the `instant` fleet using the preceding configuration, the following 16 instances are launched to meet the target capacity:
+ 6 c4\.large On\-Demand Instances in us\-east\-1a – c4\.large in us\-east\-1a is prioritized first, and there are 5 available unused c4\.large Capacity Reservations\. The Capacity Reservations are used first to launch 5 On\-Demand Instances plus an additional On\-Demand Instance is launched according to the On\-Demand allocation strategy, which is `prioritized` in this example\.
+ 5 c3\.large On\-Demand Instances in us\-east\-1a – c3\.large in us\-east\-1a is prioritized second, and there are 5 available unused c3\.large Capacity Reservations
+ 5 c5\.large On\-Demand Instances in us\-east\-1a – c5\.large in us\-east\-1a is prioritized third, and there are 5 available unused c5\.large Capacity Reservations

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the Capacity Reservations in all of the pools were used\.

```
{
    "CapacityReservationId": "cr-111",
    "InstanceType": "c4.large",  
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-222",
    "InstanceType": "c3.large", 
    "AvailableInstanceCount": 0
}

{
    "CapacityReservationId": "cr-333",
    "InstanceType": "c5.large", 
    "AvailableInstanceCount": 0
}
```

## Example 7: Launch On\-Demand Instances using Capacity Reservations and the `lowest-price` allocation strategy<a name="ec2-fleet-config7"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. And if multiple instance pools have unused Capacity Reservations, the chosen On\-Demand allocation strategy is applied\. In this example, the On\-Demand allocation strategy is `lowest-price`\.

In this example, there are 15 available unused Capacity Reservations\. This is more than the fleet's target On\-Demand capacity of 12 On\-Demand Instances\.

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

The following fleet configuration shows only the pertinent configurations for this example\. The On\-Demand allocation strategy is `lowest-price`, and the usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. The total target capacity is 12, and the default target capacity type is `on-demand`\.

In this example, the On\-Demand Instance price is:
+ m5\.large – $0\.096 per hour
+ m4\.xlarge – $0\.20 per hour
+ m4\.2xlarge – $0\.40 per hour

**Note**  
The fleet type must be `instant`\. Capacity Reservations are not supported for other fleet types\.

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
+ 5 m5\.large On\-Demand Instances in us\-east\-1a – m5\.large in us\-east\-1a is the lowest price, and there are 5 available unused m5\.large Capacity Reservations
+ 5 m4\.xlarge On\-Demand Instances in us\-east\-1a – m4\.xlarge in us\-east\-1a is the next lowest price, and there are 5 available unused m4\.xlarge Capacity Reservations
+ 2 m4\.2xlarge On\-Demand Instances in us\-east\-1a – m4\.2xlarge in us\-east\-1a is the third lowest price, and there are 5 available unused m4\.2xlarge Capacity Reservations of which only 2 are needed to meet the target capacity

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the m5\.large and m4\.xlarge Capacity Reservations were used, with 3 m4\.2xlarge Capacity Reservations remaining unused\.

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

## Example 8: Launch On\-Demand Instances using Capacity Reservations and the `lowest-price` allocation strategy when the total target capacity is more than the number of unused Capacity Reservations<a name="ec2-fleet-config8"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching On\-Demand Instances by setting the usage strategy for Capacity Reservations to `use-capacity-reservations-first`\. And if the number of unused Capacity Reservations is less than the On\-Demand target capacity, the remaining On\-Demand target capacity is launched according to the chosen On\-Demand allocation strategy\. In this example, the On\-Demand allocation strategy is `lowest-price`\.

In this example, there are 15 available unused Capacity Reservations\. This is less than the fleet's On\-Demand target capacity of 16 On\-Demand Instances\.

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

The following fleet configuration shows only the pertinent configurations for this example\. The On\-Demand allocation strategy is `lowest-price`, and the usage strategy for Capacity Reservations is `use-capacity-reservations-first`\. The total target capacity is 16, and the default target capacity type is `on-demand`\.

In this example, the On\-Demand Instance price is:
+ m5\.large – $0\.096 per hour
+ m4\.xlarge – $0\.20 per hour
+ m4\.2xlarge – $0\.40 per hour

**Note**  
The fleet type must be `instant`\. Capacity Reservations are not supported for other fleet types\.

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
+ 6 m5\.large On\-Demand Instances in us\-east\-1a – m5\.large in us\-east\-1a is the lowest price, and there are 5 available unused m5\.large Capacity Reservations\. The Capacity Reservations are used first to launch 5 On\-Demand Instances plus an additional On\-Demand Instance is launched according to the On\-Demand allocation strategy, which is `lowest-price` in this example\.
+ 5 m4\.xlarge On\-Demand Instances in us\-east\-1a – m4\.xlarge in us\-east\-1a is the next lowest price, and there are 5 available unused m4\.xlarge Capacity Reservations
+ 5 m4\.2xlarge On\-Demand Instances in us\-east\-1a – m4\.2xlarge in us\-east\-1a is the third lowest price, and there are 5 available unused m4\.2xlarge Capacity Reservations

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
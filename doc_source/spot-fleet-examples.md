# Spot Fleet example configurations<a name="spot-fleet-examples"></a>

The following examples show launch configurations that you can use with the [request\-spot\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/request-spot-fleet.html) command to create a Spot Fleet request\. For more information, see [Create a Spot Fleet request](work-with-spot-fleets.md#create-spot-fleet)\.

**Note**  
For Spot Fleet, you can't specify a network interface ID in a launch template or launch specification\. Make sure you omit the `NetworkInterfaceID` parameter in your launch template or launch specification\.

**Topics**
+ [Example 1: Launch Spot Instances using the lowest\-priced Availability Zone or subnet in the Region](#fleet-config1)
+ [Example 2: Launch Spot Instances using the lowest\-priced Availability Zone or subnet in a specified list](#fleet-config2)
+ [Example 3: Launch Spot Instances using the lowest\-priced instance type in a specified list](#fleet-config3)
+ [Example 4\. Override the price for the request](#fleet-config4)
+ [Example 5: Launch a Spot Fleet using the diversified allocation strategy](#fleet-config5)
+ [Example 6: Launch a Spot Fleet using instance weighting](#fleet-config6)
+ [Example 7: Launch a Spot Fleet with On\-Demand capacity](#fleet-config7)
+ [Example 8: Configure Capacity Rebalancing to launch replacement Spot Instances](#fleet-config8)
+ [Example 9: Launch Spot Instances in a capacity\-optimized fleet](#fleet-config9)
+ [Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities](#fleet-config10)
+ [Example 11: Launch Spot Instances in a priceCapacityOptimized fleet](#fleet-config11)
+ [Example 12: Configure attribute\-based instance type selection](#fleet-config12)

## Example 1: Launch Spot Instances using the lowest\-priced Availability Zone or subnet in the Region<a name="fleet-config1"></a>

The following example specifies a single launch specification without an Availability Zone or subnet\. The Spot Fleet launches the instances in the lowest\-priced Availability Zone that has a default subnet\. The price you pay does not exceed the On\-Demand price\.

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "m3.medium",
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
          }
      }
  ]
}
```

## Example 2: Launch Spot Instances using the lowest\-priced Availability Zone or subnet in a specified list<a name="fleet-config2"></a>

The following examples specify two launch specifications with different Availability Zones or subnets, but the same instance type and AMI\.

**Availability Zones**

The Spot Fleet launches the instances in the default subnet of the lowest\-priced Availability Zone that you specified\.

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "m3.medium",
          "Placement": {
              "AvailabilityZone": "us-west-2a, us-west-2b"
          },
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
          }
      }
  ]
}
```

**Subnets**

You can specify default subnets or nondefault subnets, and the nondefault subnets can be from a default VPC or a nondefault VPC\. The Spot service launches the instances in whichever subnet is in the lowest\-priced Availability Zone\.

You can't specify different subnets from the same Availability Zone in a Spot Fleet request\.

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "m3.medium",
          "SubnetId": "subnet-a61dafcf, subnet-65ea5f08",
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::123456789012:instance-profile/my-iam-role"
          }
      }
  ]
}
```

If the instances are launched in a default VPC, they receive a public IPv4 address by default\. If the instances are launched in a nondefault VPC, they do not receive a public IPv4 address by default\. Use a network interface in the launch specification to assign a public IPv4 address to instances launched in a nondefault VPC\. When you specify a network interface, you must include the subnet ID and security group ID using the network interface\.

```
  ...       
      {
          "ImageId": "ami-1a2b3c4d",
          "KeyName": "my-key-pair",
          "InstanceType": "m3.medium",
          "NetworkInterfaces": [
              {
                  "DeviceIndex": 0,
                  "SubnetId": "subnet-1a2b3c4d",
                  "Groups": [ "sg-1a2b3c4d" ],
                  "AssociatePublicIpAddress": true
              }
          ],
          "IamInstanceProfile": {
              "Arn": "arn:aws:iam::880185128111:instance-profile/my-iam-role"
          }
      }
  ...
```

## Example 3: Launch Spot Instances using the lowest\-priced instance type in a specified list<a name="fleet-config3"></a>

The following examples specify two launch configurations with different instance types, but the same AMI and Availability Zone or subnet\. The Spot Fleet launches the instances using the specified instance type with the lowest price\.

**Availability Zone**

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "cc2.8xlarge",
          "Placement": {
            "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "r3.8xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      }
  ]
}
```

**Subnet**

```
{
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "cc2.8xlarge",
          "SubnetId": "subnet-1a2b3c4d"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "SecurityGroups": [
              {
                  "GroupId": "sg-1a2b3c4d"
              }
          ],
          "InstanceType": "r3.8xlarge",
          "SubnetId": "subnet-1a2b3c4d"
      }
  ]
}
```

## Example 4\. Override the price for the request<a name="fleet-config4"></a>

We recommended that you use the default maximum price, which is the On\-Demand price\. If you prefer, you can specify a maximum price for the fleet request and maximum prices for individual launch specifications\.

The following examples specify a maximum price for the fleet request and maximum prices for two of the three launch specifications\. The maximum price for the fleet request is used for any launch specification that does not specify a maximum price\. The Spot Fleet launches the instances using the instance type with the lowest price\.

**Availability Zone**

```
{
  "SpotPrice": "1.00",
  "TargetCapacity": 30,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "SpotPrice": "0.10"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.4xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "SpotPrice": "0.20"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.8xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      }
    ]
}
```

**Subnet**

```
{
  "SpotPrice": "1.00",
  "TargetCapacity": 30,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.2xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "SpotPrice": "0.10"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.4xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "SpotPrice": "0.20"
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.8xlarge",
          "SubnetId": "subnet-1a2b3c4d"
      }
  ]
}
```

## Example 5: Launch a Spot Fleet using the diversified allocation strategy<a name="fleet-config5"></a>

The following example uses the `diversified` allocation strategy\. The launch specifications have different instance types but the same AMI and Availability Zone or subnet\. The Spot Fleet distributes the 30 instances across the three launch specifications, such that there are 10 instances of each type\. For more information, see [Allocation strategies for Spot Instances](spot-fleet-allocation-strategy.md)\.

**Availability Zone**

```
{
  "SpotPrice": "0.70", 
  "TargetCapacity": 30,
  "AllocationStrategy": "diversified",
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c4.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "m3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      }
  ]
}
```

**Subnet**

```
{
    "SpotPrice": "0.70", 
    "TargetCapacity": 30,
    "AllocationStrategy": "diversified",
    "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
    "LaunchSpecifications": [
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "c4.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        },
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "m3.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        },
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "r3.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        }
    ]
}
```

A best practice to increase the chance that a spot request can be fulfilled by EC2 capacity in the event of an outage in one of the Availability Zones is to diversify across zones\. For this scenario, include each Availability Zone available to you in the launch specification\. And, instead of using the same subnet each time, use three unique subnets \(each mapping to a different zone\)\. 

**Availability Zone**

```
{
  "SpotPrice": "0.70", 
  "TargetCapacity": 30,
  "AllocationStrategy": "diversified",
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c4.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2a"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "m3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          }
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2c"
          }
      }
  ]
}
```

**Subnet**

```
{
    "SpotPrice": "0.70", 
    "TargetCapacity": 30,
    "AllocationStrategy": "diversified",
    "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
    "LaunchSpecifications": [
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "c4.2xlarge",
            "SubnetId": "subnet-1a2b3c4d"
        },
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "m3.2xlarge",
            "SubnetId": "subnet-2a2b3c4d"
        },
        {
            "ImageId": "ami-1a2b3c4d",
            "InstanceType": "r3.2xlarge",
            "SubnetId": "subnet-3a2b3c4d"
        }
    ]
}
```

## Example 6: Launch a Spot Fleet using instance weighting<a name="fleet-config6"></a>

The following examples use instance weighting, which means that the price is per unit hour instead of per instance hour\. Each launch configuration lists a different instance type and a different weight\. The Spot Fleet selects the instance type with the lowest price per unit hour\. The Spot Fleet calculates the number of Spot Instances to launch by dividing the target capacity by the instance weight\. If the result isn't an integer, the Spot Fleet rounds it up to the next integer, so that the size of your fleet is not below its target capacity\.

If the `r3.2xlarge` request is successful, Spot provisions 4 of these instances\. Divide 20 by 6 for a total of 3\.33 instances, then round up to 4 instances\.

If the `c3.xlarge` request is successful, Spot provisions 7 of these instances\. Divide 20 by 3 for a total of 6\.66 instances, then round up to 7 instances\.

For more information, see [Spot Fleet instance weighting](spot-instance-weighting.md)\.

**Availability Zone**

```
{
  "SpotPrice": "0.70",
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "WeightedCapacity": 6
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.xlarge",
          "Placement": {
              "AvailabilityZone": "us-west-2b"
          },
          "WeightedCapacity": 3
      }
    ]
}
```

**Subnet**

```
{
  "SpotPrice": "0.70",
  "TargetCapacity": 20,
  "IamFleetRole": "arn:aws:iam::123456789012:role/aws-ec2-spot-fleet-tagging-role",
  "LaunchSpecifications": [
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "r3.2xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "WeightedCapacity": 6
      },
      {
          "ImageId": "ami-1a2b3c4d",
          "InstanceType": "c3.xlarge",
          "SubnetId": "subnet-1a2b3c4d",
          "WeightedCapacity": 3
      }
  ]
}
```

## Example 7: Launch a Spot Fleet with On\-Demand capacity<a name="fleet-config7"></a>

To ensure that you always have instance capacity, you can include a request for On\-Demand capacity in your Spot Fleet request\. If there is capacity, the On\-Demand request is always fulfilled\. The balance of the target capacity is fulfilled as Spot if there is capacity and availability\.

The following example specifies the desired target capacity as 10, of which 5 must be On\-Demand capacity\. Spot capacity is not specified; it is implied in the balance of the target capacity minus the On\-Demand capacity\. Amazon EC2 launches 5 capacity units as On\-Demand, and 5 capacity units \(10\-5=5\) as Spot if there is available Amazon EC2 capacity and availability\. 

For more information, see [On\-Demand in Spot Fleet](on-demand-in-spot.md)\.

```
{
  "IamFleetRole": "arn:aws:iam::781603563322:role/aws-ec2-spot-fleet-tagging-role",
  "AllocationStrategy": "lowestPrice",
  "TargetCapacity": 10,
  "SpotPrice": null,
  "ValidFrom": "2018-04-04T15:58:13Z",
  "ValidUntil": "2019-04-04T15:58:13Z",
  "TerminateInstancesWithExpiration": true,
  "LaunchSpecifications": [],
  "Type": "maintain",
  "OnDemandTargetCapacity": 5,
  "LaunchTemplateConfigs": [
    {
      "LaunchTemplateSpecification": {
        "LaunchTemplateId": "lt-0dbb04d4a6cca5ad1",
        "Version": "2"
      },
      "Overrides": [
        {
          "InstanceType": "t2.medium",
          "WeightedCapacity": 1,
          "SubnetId": "subnet-d0dc51fb"
        }
      ]
    }
  ]
}
```

## Example 8: Configure Capacity Rebalancing to launch replacement Spot Instances<a name="fleet-config8"></a>

The following example configures the Spot Fleet to launch a replacement Spot Instance when Amazon EC2 emits a rebalance recommendation for a Spot Instance in the fleet\. To configure the automatic replacement of Spot Instances, for `ReplacementStrategy`, specify `launch-before-terminate`\. To configure the time delay from the launch of the new replacement Spot Instances to the automatic deletion of the old Spot Instances, for `termination-delay`, specify a value in seconds\. For more information, see [Configuration options](spot-fleet-capacity-rebalance.md#spot-fleet-capacity-rebalance-config-options)\.

**Note**  
We recommend using `launch-before-terminate` only if you can predict how long your instance shutdown procedures will take to complete\. This ensures that the old instances are terminated only after the shutdown procedures are completed\. You are charged for all instances while they are running\.

The effectiveness of the Capacity Rebalancing strategy depends on the number of Spot capacity pools specified in the Spot Fleet request\. We recommend that you configure the fleet with a diversified set of instance types and Availability Zones, and for `AllocationStrategy`, specify `capacityOptimized`\. For more information about what you should consider when configuring a Spot Fleet for Capacity Rebalancing, see [Capacity Rebalancing](spot-fleet-capacity-rebalance.md)\.

```
{
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "capacityOptimized",
        "IamFleetRole": "arn:aws:iam::000000000000:role/aws-ec2-spot-fleet-tagging-role",
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
        "TargetCapacity": 5,
        "SpotMaintenanceStrategies": {
            "CapacityRebalance": {
                "ReplacementStrategy": "launch-before-terminate",
                "TerminationDelay": "720"
            }
        }
    }
}
```

## Example 9: Launch Spot Instances in a capacity\-optimized fleet<a name="fleet-config9"></a>

The following example demonstrates how to configure a Spot Fleet with a Spot allocation strategy that optimizes for capacity\. To optimize for capacity, you must set `AllocationStrategy` to `capacityOptimized`\.

In the following example, the three launch specifications specify three Spot capacity pools\. The target capacity is 50 Spot Instances\. The Spot Fleet attempts to launch 50 Spot Instances into the Spot capacity pool with optimal capacity for the number of instances that are launching\.

```
{
    "TargetCapacity": "50",
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "capacityOptimized",
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
                           "AvailabilityZone": "us-west-2a"
                      },
                       {
                           "InstanceType": "m4.2xlarge",
                           "AvailabilityZone": "us-west-2b"
                       }, 
                       {
                           "InstanceType": "c5.2xlarge",
                           "AvailabilityZone": "us-west-2b"
                       }
                 ] 
           }
      ]
}
```

## Example 10: Launch Spot Instances in a capacity\-optimized fleet with priorities<a name="fleet-config10"></a>

The following example demonstrates how to configure a Spot Fleet with a Spot allocation strategy that optimizes for capacity while using priority on a best\-effort basis\.

When using the `capacityOptimizedPrioritized` allocation strategy, you can use the `Priority` parameter to specify the priorities of the Spot capacity pools, where the lower the number the higher priority\. You can also set the same priority for several Spot capacity pools if you favor them equally\. If you do not set a priority for a pool, the pool will be considered last in terms of priority\.

To prioritize Spot capacity pools, you must set `AllocationStrategy` to `capacityOptimizedPrioritized`\. Spot Fleet will optimize for capacity first, but will honor the priorities on a best\-effort basis \(for example, if honoring the priorities will not significantly affect Spot Fleet's ability to provision optimal capacity\)\. This is a good option for workloads where the possibility of disruption must be minimized and the preference for certain instance types matters\.

In the following example, the three launch specifications specify three Spot capacity pools\. Each pool is prioritized, where the lower the number the higher priority\. The target capacity is 50 Spot Instances\. The Spot Fleet attempts to launch 50 Spot Instances into the Spot capacity pool with the highest priority on a best\-effort basis, but optimizes for capacity first\.

```
{
    "TargetCapacity": "50",
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "capacityOptimizedPrioritized"
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
                           "AvailabilityZone": "us-west-2a"
                      },
                       {
                           "InstanceType": "m4.2xlarge",
                           "Priority": 2,
                           "AvailabilityZone": "us-west-2b"
                       }, 
                       {
                           "InstanceType": "c5.2xlarge",
                           "Priority": 3,
                           "AvailabilityZone": "us-west-2b"
                       }
                  ] 
             }
       ]
}
```

## Example 11: Launch Spot Instances in a priceCapacityOptimized fleet<a name="fleet-config11"></a>

The following example demonstrates how to configure a Spot Fleet with a Spot allocation strategy that optimizes for both capacity and lowest price\. To optimize for capacity while taking price into consideration, you must set the Spot `AllocationStrategy` to `priceCapacityOptimized`\.

In the following example, the three launch specifications specify three Spot capacity pools\. The target capacity is 50 Spot Instances\. The Spot Fleet attempts to launch 50 Spot Instances into the Spot capacity pool with optimal capacity for the number of instances that are launching while also choosing the pool that is the lowest priced\.

```
{
    "SpotFleetRequestConfig": {
        "AllocationStrategy": "priceCapacityOptimized",
        "OnDemandAllocationStrategy": "lowestPrice",
        "ExcessCapacityTerminationPolicy": "default",
        "IamFleetRole": "arn:aws:iam::111111111111:role/aws-ec2-spot-fleet-tagging-role",
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateId": "lt-0123456789example",
                    "Version": "1"
                },
                "Overrides": [
                     {
                           "InstanceType": "r4.2xlarge",  
                           "AvailabilityZone": "us-west-2a"
                      },
                      {
                           "InstanceType": "m4.2xlarge",
                           "AvailabilityZone": "us-west-2b"
                      }, 
                      {
                           "InstanceType": "c5.2xlarge",
                           "AvailabilityZone": "us-west-2b"
                      }
                ]
            }
        ],
        "TargetCapacity": 50,
        "Type": "request"
    }
}
```

## Example 12: Configure attribute\-based instance type selection<a name="fleet-config12"></a>

The following example demonstrates how to configure a Spot Fleet to use attribute\-based instance type selection for identifying instance types\. To specify the required instance attributes, you specify the attributes in the `InstanceRequirements` structure\.

In the following example, two instance attributes are specified:
+ `VCpuCount` – A minimum of 2 vCPUs is specified\. Because no maximum is specified, there is no maximum limit\.
+ `MemoryMiB` – A minimum of 4 MiB of memory is specified\. Because no maximum is specified, there is no maximum limit\.

Any instance types that have 2 or more vCPUs and 4 MiB or more of memory will be identified\. However, price protection and the allocation strategy might exclude some instance types when [Spot Fleet provisions the fleet](spot-fleet-attribute-based-instance-type-selection.md#how-sf-uses-abs)\.

For a list and descriptions of all the possible attributes that you can specify, see [InstanceRequirements](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_InstanceRequirements.html) in the *Amazon EC2 API Reference*\.

```
{
	"AllocationStrategy": "priceCapacityOptimized",
	"TargetCapacity": 20,
	"Type": "request",
	"LaunchTemplateConfigs": [{
		"LaunchTemplateSpecification": {
			"LaunchTemplateName": "my-launch-template",
			"Version": "1"
		},
		"Overrides": [{
			"InstanceRequirements": {
				"VCpuCount": {
					"Min": 2
				},
				"MemoryMiB": {
					"Min": 4
				}
			}
		}]
	}]
}
```
# Attribute\-based instance type selection for EC2 Fleet<a name="ec2-fleet-attribute-based-instance-type-selection"></a>

When you create an EC2 Fleet, you must specify one or more instance types for configuring the On\-Demand Instances and Spot Instances in the fleet\. As an alternative to manually specifying the instance types, you can specify the attributes that an instance must have, and Amazon EC2 will identify all the instance types with those attributes\. This is known as *attribute\-based instance type selection*\. For example, you can specify the minimum and maximum number of vCPUs required for your instances, and EC2 Fleet will launch the instances using any available instance types that meet those vCPU requirements\. 

Attribute\-based instance type selection is ideal for workloads and frameworks that can be flexible about what instance types they use, such as when running containers or web ﬂeets, processing big data, and implementing continuous integration and deployment \(CI/CD\) tooling\.

**Benefits**

Attribute\-based instance type selection has the following benefits:
+ With so many instance types available, finding the right instance types for your workload can be time consuming\. When you specify instance attributes, the instance types will automatically have the required attributes for your workload\.
+ To manually specify multiple instance types for an EC2 Fleet, you must create a separate launch template override for each instance type\. But with attribute\-based instance type selection, to provide multiple instance types, you need only specify the instance attributes in the launch template or in a launch template override\.
+ When you specify instance attributes rather than instance types, your fleet can use newer generation instance types as they’re released, "future proofing" the fleet's configuration\. 
+ When you specify instance attributes rather than instance types, EC2 Fleet can select from a wide range of instance types for launching Spot Instances, which adheres to the [Spot best practice of instance type flexibility](spot-best-practices.md#be-instance-type-flexible)\.

**Topics**
+ [How attribute\-based instance type selection works](#ec2fleet-abs-how-it-works)
+ [Considerations](#ec2fleet-abs-considerations)
+ [Create an EC2 Fleet with attribute\-based instance type selection](#abs-create-ec2-fleet)
+ [Examples of configurations that are valid and not valid](#ec2fleet-abs-example-configs)
+ [Preview instance types with specified attributes](#ec2fleet-get-instance-types-from-instance-requirements)

## How attribute\-based instance type selection works<a name="ec2fleet-abs-how-it-works"></a>

To use attribute\-based instance type selection in your fleet configuration, you replace the list of instance types with a list of instance attributes that your instances require\. EC2 Fleet will launch instances on any available instance types that have the specified instance attributes\.

**Topics**
+ [Types of instance attributes](#ef-abs-instance-attribute-types)
+ [Where to configure attribute\-based instance type selection](#ef-abs-where-to-configure)
+ [How EC2 Fleet uses attribute\-based instance type selection when provisioning a fleet](#how-ef-uses-abs)
+ [Price protection](#ec2fleet-abs-price-protection)

### Types of instance attributes<a name="ef-abs-instance-attribute-types"></a>

There are several instance attributes that you can specify to express your compute requirements\. For a description of each attribute and the default values, see [InstanceRequirements](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_InstanceRequirements.html) in the *Amazon EC2 API Reference*\.

### Where to configure attribute\-based instance type selection<a name="ef-abs-where-to-configure"></a>

Depending on whether you use the console or the AWS CLI, you can specify the instance attributes for attribute\-based instance type selection as follows:

In the console, you can specify the instance attributes in one or both of the following fleet configuration components:
+ In a launch template, and reference the launch template in the fleet request
+ In the fleet request

In the AWS CLI, you can specify the instance attributes in one or all of the following fleet configuration components:
+ In a launch template, and reference the launch template in the fleet request
+ In a launch template override

  If you want a mix of instances that use different AMIs, you can specify instance attributes in multiple launch template overrides\. For example, different instance types can use x86 and Arm\-based processors\.
+ In a launch specification

### How EC2 Fleet uses attribute\-based instance type selection when provisioning a fleet<a name="how-ef-uses-abs"></a>

EC2 Fleet provisions a fleet in the following way:
+ EC2 Fleet identifies the instance types that have the specified attributes\.
+ EC2 Fleet uses price protection to determine which instance types to exclude\.
+ EC2 Fleet determines the capacity pools from which it will consider launching the instances based on the AWS Regions or Availability Zones that have matching instance types\.
+ EC2 Fleet applies the specified allocation strategy to determine from which capacity pools to launch the instances\.

  Note that attribute\-based instance type selection does not pick the capacity pools from which to provision the fleet; that's the job of the allocation strategies\. There might be a large number of instance types with the specified attributes, and some of them might be expensive\. The default allocation strategy of `lowest-price` for Spot and On\-Demand guarantees that EC2 Fleet will launch instances from the least expensive capacity pools\.

  If you specify an allocation strategy, EC2 Fleet will launch instances according to the specified allocation strategy\.
  + For Spot Instances, attribute\-based instance type selection supports the `capacity-optimized` and `lowest-price` allocation strategies\.
  + For On\-Demand Instances, attribute\-based instance type selection supports the `lowest-price` allocation strategy\.
+ If there is no capacity for the instance types with the specified instance attributes, no instances can be launched, and the fleet returns an error\.

### Price protection<a name="ec2fleet-abs-price-protection"></a>

Price protection is a feature that prevents your EC2 Fleet from using instance types that you would consider too expensive even if they happen to fit the attributes that you specified\. When you create a fleet with attribute\-based instance type selection, price protection is enabled by default, with separate thresholds for On\-Demand Instances and Spot Instances\. When Amazon EC2 selects instance types with your attributes, it excludes instance types priced above your threshold\. The thresholds represent the maximum you'll pay, expressed as a percentage above the least expensive M, C, or R instance type with your specified attributes\.

If you don't specify a threshold, the following thresholds are used by default:
+ For On\-Demand Instances, the price protection threshold is set at 20 percent\.
+ For Spot Instances, the price protection threshold is set at 100 percent\.

**To specify the price protection threshold**

While creating the EC2 Fleet, configure the fleet for attribute\-based instance type selection, and then do the following:
+ To specify the On\-Demand Instance price protection threshold, in the JSON configuration file, in the `InstanceRequirements` structure, for `OnDemandMaxPricePercentageOverLowestPrice`, enter the price protection threshold as a percentage\.
+ To specify the Spot Instance price protection threshold, in the JSON configuration file, in the `InstanceRequirements` structure, for `SpotMaxPricePercentageOverLowestPrice`, enter the price protection threshold as a percentage\.

For more information about creating the fleet, see [Create an EC2 Fleet with attribute\-based instance type selection](#abs-create-ec2-fleet)\.

**Note**  
When creating the EC2 Fleet, if you set `TargetCapacityUnitType` to `vcpu` or `memory-mib`, the price protection threshold is applied based on the per\-vCPU or per\-memory price instead of the per\-instance price\.

## Considerations<a name="ec2fleet-abs-considerations"></a>
+ You can specify either instance types or instance attributes in an EC2 Fleet, but not both at the same time\.

  When using the CLI, the launch template overrides will override the launch template\. For example, if the launch template contains an instance type and the launch template override contains instance attributes, the instances that are identified by the instance attributes will override the instance type in the launch template\.
+ When using the CLI, when you specify instance attributes as overrides, you can't also specify weights or priorities\.
+ You can specify a maximum of three `InstanceRequirements` structures in a request configuration\.

## Create an EC2 Fleet with attribute\-based instance type selection<a name="abs-create-ec2-fleet"></a>

You can configure a fleet to use attribute\-based instance type selection by using the AWS CLI\.

### Create an EC2 Fleet using the AWS CLI<a name="abs-create-ec2-fleet-cli"></a>

**To create an EC2 Fleet \(AWS CLI\)**
+ Use the [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) \(AWS CLI\) command to create an EC2 Fleet\. Specify the fleet configuration in a JSON file\.

```
aws ec2 create-fleet \
    --region us-east-1 \
    --cli-input-json file://file_name.json
```

The following JSON file contains all of the parameters that can be specified when configuring an EC2 Fleet\. The parameters for attribute\-based instance type selection are located in the `InstanceRequirements` structure\. For a description of each attribute and the default values, see [InstanceRequirements](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_InstanceRequirements.html) in the *Amazon EC2 API Reference*\.

**Note**  
When `InstanceRequirements` is included in the fleet configuration, `InstanceType` and `WeightedCapacity` must be excluded; they cannot determine the fleet configuration at the same time as instance attributes\.

```
{
    "DryRun": true,
    "ClientToken": "",
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        "MaintenanceStrategies": {
            "CapacityRebalance": {
                "ReplacementStrategy": "launch"
            }
        },
        "InstanceInterruptionBehavior": "stop",
        "InstancePoolsToUseCount": 0,
        "SingleInstanceType": true,
        "SingleAvailabilityZone": true,
        "MinTargetCapacity": 0,
        "MaxTotalPrice": ""
    },
    "OnDemandOptions": {
        "AllocationStrategy": "prioritized",
        "CapacityReservationOptions": {
            "UsageStrategy": "use-capacity-reservations-first"
        },
        "SingleInstanceType": true,
        "SingleAvailabilityZone": true,
        "MinTargetCapacity": 0,
        "MaxTotalPrice": ""
    },
    "ExcessCapacityTerminationPolicy": "no-termination",
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "",
                "LaunchTemplateName": "",
                "Version": ""
            },
            "Overrides": [
                {
                    "InstanceType": "r5ad.large",
                    "MaxPrice": "",
                    "SubnetId": "",
                    "AvailabilityZone": "",
                    "WeightedCapacity": 0.0,
                    "Priority": 0.0,
                    "Placement": {
                        "AvailabilityZone": "",
                        "Affinity": "",
                        "GroupName": "",
                        "PartitionNumber": 0,
                        "HostId": "",
                        "Tenancy": "host",
                        "SpreadDomain": "",
                        "HostResourceGroupArn": ""
                    },
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 0
                        },
                        "MemoryMiB": {
                            "Min": 0,
                            "Max": 0
                        },
                        "CpuManufacturers": [
                            "amd"
                        ],
                        "MemoryGiBPerVCpu": {
                            "Min": 0.0,
                            "Max": 0.0
                        },
                        "ExcludedInstanceTypes": [
                            ""
                        ],
                        "InstanceGenerations": [
                            "previous"
                        ],
                        "SpotMaxPricePercentageOverLowestPrice": 0,
                        "OnDemandMaxPricePercentageOverLowestPrice": 0,
                        "BareMetal": "excluded",
                        "BurstablePerformance": "required",
                        "RequireHibernateSupport": true,
                        "NetworkInterfaceCount": {
                            "Min": 0,
                            "Max": 0
                        },
                        "LocalStorage": "required",
                        "LocalStorageTypes": [
                            "hdd"
                        ],
                        "TotalLocalStorageGB": {
                            "Min": 0.0,
                            "Max": 0.0
                        },
                        "BaselineEbsBandwidthMbps": {
                            "Min": 0,
                            "Max": 0
                        },
                        "AcceleratorTypes": [
                            "fpga"
                        ],
                        "AcceleratorCount": {
                            "Min": 0,
                            "Max": 0
                        },
                        "AcceleratorManufacturers": [
                            "xilinx"
                        ],
                        "AcceleratorNames": [
                            "vu9p"
                        ],
                        "AcceleratorTotalMemoryMiB": {
                            "Min": 0,
                            "Max": 0
                        }
                    }
                }
            ]
        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 0,
        "OnDemandTargetCapacity": 0,
        "SpotTargetCapacity": 0,
        "DefaultTargetCapacityType": "spot",
        "TargetCapacityUnitType": "vcpu"
    },
    "TerminateInstancesWithExpiration": true,
    "Type": "instant",
    "ValidFrom": "1970-01-01T00:00:00",
    "ValidUntil": "1970-01-01T00:00:00",
    "ReplaceUnhealthyInstances": true,
    "TagSpecifications": [
        {
            "ResourceType": "route-table",
            "Tags": [
                {
                    "Key": "",
                    "Value": ""
                }
            ]
        }
    ],
    "Context": ""
}
```

## Examples of configurations that are valid and not valid<a name="ec2fleet-abs-example-configs"></a>

If you use the AWS CLI to create an EC2 Fleet, you must make sure that your fleet configuration is valid\. The following examples show configurations that are valid and not valid\.

Configurations are considered not valid when they contain the following:
+ A single `Overrides` structure with both `InstanceRequirements` and `InstanceType`
+ Two `Overrides` structures, one with `InstanceRequirements` and the other with `InstanceType`
+ Two `InstanceRequirements` structures with overlapping attribute values within the same `LaunchTemplateSpecification`

**Topics**
+ [Valid configuration: Single launch template with overrides](#ef-abs-example-config1)
+ [Valid configuration: Single launch template with multiple InstanceRequirements](#ef-abs-example-config3)
+ [Valid configuration: Two launch templates, each with overrides](#ef-abs-example-config2)
+ [Configuration not valid: `Overrides` contain `InstanceRequirements` and `InstanceType`](#ef-abs-example-config4)
+ [Configuration not valid: Two `Overrides` contain `InstanceRequirements` and `InstanceType`](#ef-abs-example-config5)
+ [Valid configuration: Only `InstanceRequirements` specified, no overlapping attribute values](#ef-abs-example-config6)
+ [Configuration not valid: Overlapping attribute values](#ef-abs-example-config7)

### Valid configuration: Single launch template with overrides<a name="ef-abs-example-config1"></a>

The following configuration is valid\. It contains one launch template and one `Overrides` structure containing one `InstanceRequirements` structure\. A text explanation of the example configuration follows\.

```
{
        "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateName": "My-launch-template",
                "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 2,
                            "Max": 8
                        },
                        "MemoryMib": {
                            "Min": 0,
                            "Max": 10240
                        },
                        "MemoryGiBPerVCpu": {
                            "Max": 10000
                        },
                        "RequireHibernateSupport": true
                    }
                }
            ]
        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 5000,
        "DefaultTargetCapacityType": "spot",
        "TargetCapacityUnitType": "vcpu"
        }
    }
}
```

****`InstanceRequirements`****  
To use attribute\-based instance selection, you must include the `InstanceRequirements` structure in your fleet configuration, and specify the desired attributes for the instances in the fleet\.

In the preceding example, the following instance attributes are specified:
+ `VCpuCount` – The instance types must have a minimum of 2 and a maximum of 8 vCPUs\.
+ `MemoryMiB` – The instance types must have a maximum of 10240 MiB of memory\. A minimum of 0 indicates no minimum limit\.
+ `MemoryGiBPerVCpu` – The instance types must have a maximum of 10,000 GiB of memory per vCPU\. The `Min` parameter is optional\. By omitting it, you indicate no minimum limit\.

**`TargetCapacityUnitType`**  
The `TargetCapacityUnitType` parameter specifies the unit for the target capacity\. In the example, the target capacity is `5000` and the target capacity unit type is `vcpu`, which together specify a desired target capacity of 5,000 vCPUs\. EC2 Fleet will launch enough instances so that the total number of vCPUs in the fleet is 5,000 vCPUs\.

### Valid configuration: Single launch template with multiple InstanceRequirements<a name="ef-abs-example-config3"></a>

The following configuration is valid\. It contains one launch template and one `Overrides` structure containing two `InstanceRequirements` structures\. The attributes specified in `InstanceRequirements` are valid because the values do not overlap—the first `InstanceRequirements` structure specifies a `VCpuCount` of 0\-2 vCPUs, while the second `InstanceRequirements` structure specifies 4\-8 vCPUs\.

```
{
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                },
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 4,
                            "Max": 8
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                }
              ]
            }
        ],
        "TargetCapacitySpecification": {
            "TotalTargetCapacity": 1,
            "DefaultTargetCapacityType": "spot"
        }
    }
}
```

### Valid configuration: Two launch templates, each with overrides<a name="ef-abs-example-config2"></a>

The following configuration is valid\. It contains two launch templates, each with one `Overrides` structure containing one `InstanceRequirements` structure\. This configuration is useful for `arm` and `x86` architecture support in the same fleet\.

```
{
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "armLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                },
                {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "x86LaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                }
              ]
            }
        ],
         "TargetCapacitySpecification": {
            "TotalTargetCapacity": 1,
            "DefaultTargetCapacityType": "spot"
        }
    }
}
```

### Configuration not valid: `Overrides` contain `InstanceRequirements` and `InstanceType`<a name="ef-abs-example-config4"></a>

The following configuration is not valid\. The `Overrides` structure contains both `InstanceRequirements` and `InstanceType`\. For the `Overrides`, you can specify either `InstanceRequirements` or `InstanceType`, but not both\.

```
{
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                },
                {
                    "InstanceType": "m5.large"
                }
              ]
            }
        ],
        "TargetCapacitySpecification": {
            "TotalTargetCapacity": 1,
            "DefaultTargetCapacityType": "spot"
        }
    }
}
```

### Configuration not valid: Two `Overrides` contain `InstanceRequirements` and `InstanceType`<a name="ef-abs-example-config5"></a>

The following configuration is not valid\. The `Overrides` structures contain both `InstanceRequirements` and `InstanceType`\. You can specify either `InstanceRequirements` or `InstanceType`, but not both, even if they're in different `Overrides` structures\.

```
{
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                }
              ]
            },
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyOtherLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceType": "m5.large"
                }
              ]
            }
        ],
         "TargetCapacitySpecification": {
            "TotalTargetCapacity": 1,
            "DefaultTargetCapacityType": "spot"
        }
    }
}
```

### Valid configuration: Only `InstanceRequirements` specified, no overlapping attribute values<a name="ef-abs-example-config6"></a>

The following configuration is valid\. It contains two `LaunchTemplateSpecification` structures, each with a launch template and an `Overrides` structure containing an `InstanceRequirements` structure\. The attributes specified in `InstanceRequirements` are valid because the values do not overlap—the first `InstanceRequirements` structure specifies a `VCpuCount` of 0\-2 vCPUs, while the second `InstanceRequirements` structure specifies 4\-8 vCPUs\.

```
{
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                }
              ]
            },
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyOtherLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 4,
                            "Max": 8
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    }
                }
              ]
            }
        ],
        "TargetCapacitySpecification": {
            "TotalTargetCapacity": 1,
            "DefaultTargetCapacityType": "spot"
        }
    }
}
```

### Configuration not valid: Overlapping attribute values<a name="ef-abs-example-config7"></a>

The following configuration is not valid\. The two `InstanceRequirements` structures each contain `"VCpuCount": {"Min": 0, "Max": 2}`\. The values for these attributes overlap, which will result in duplicate capacity pools\.

```
{
        "LaunchTemplateConfigs": [
            {
                "LaunchTemplateSpecification": {
                    "LaunchTemplateName": "MyLaunchTemplate",
                    "Version": "1"
                },
                "Overrides": [
                {
                    "InstanceRequirements": {
                        "VCpuCount": {
                            "Min": 0,
                            "Max": 2
                        },
                        "MemoryMiB": {
                            "Min": 0
                        }
                    },
                    {
                      "InstanceRequirements": {
                          "VCpuCount": {
                              "Min": 0,
                              "Max": 2
                          },
                          "MemoryMiB": {
                              "Min": 0
                          }
                      }
                  }
                }
              ]
            }
        ],
         "TargetCapacitySpecification": {
            "TotalTargetCapacity": 1,
            "DefaultTargetCapacityType": "spot"
        }
    }
}
```

## Preview instance types with specified attributes<a name="ec2fleet-get-instance-types-from-instance-requirements"></a>

You can use the [get\-instance\-types\-from\-instance\-requirements](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-instance-types-from-instance-requirements.html     ) AWS CLI command to preview the instance types that match the attributes that you specify\. This is especially useful for working out what attributes to specify in your request configuration without launching any instances\. Note that the command does not consider available capacity\.

**To preview a list of instance types by specifying attributes using the AWS CLI**

1. \(Optional\) To generate all of the possible attributes that can be specified, use the [get\-instance\-types\-from\-instance\-requirements](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-instance-types-from-instance-requirements.html    ) command and the `--generate-cli-skeleton` parameter\. You can optionally direct the output to a file to save it by using `input > attributes.json`\.

   ```
   aws ec2 get-instance-types-from-instance-requirements \
       --region us-east-1 \
       --generate-cli-skeleton input > attributes.json
   ```

   Expected output

   ```
   {
       "DryRun": true,
       "ArchitectureTypes": [
           "x86_64_mac"
       ],
       "VirtualizationTypes": [
           "paravirtual"
       ],
       "InstanceRequirements": {
           "VCpuCount": {
               "Min": 0,
               "Max": 0
           },
           "MemoryMiB": {
               "Min": 0,
               "Max": 0
           },
           "CpuManufacturers": [
               "intel"
           ],
           "MemoryGiBPerVCpu": {
               "Min": 0.0,
               "Max": 0.0
           },
           "ExcludedInstanceTypes": [
               ""
           ],
           "InstanceGenerations": [
               "current"
           ],
           "SpotMaxPricePercentageOverLowestPrice": 0,
           "OnDemandMaxPricePercentageOverLowestPrice": 0,
           "BareMetal": "included",
           "BurstablePerformance": "excluded",
           "RequireHibernateSupport": true,
           "NetworkInterfaceCount": {
               "Min": 0,
               "Max": 0
           },
           "LocalStorage": "required",
           "LocalStorageTypes": [
               "hdd"
           ],
           "TotalLocalStorageGB": {
               "Min": 0.0,
               "Max": 0.0
           },
           "BaselineEbsBandwidthMbps": {
               "Min": 0,
               "Max": 0
           },
           "AcceleratorTypes": [
               "inference"
           ],
           "AcceleratorCount": {
               "Min": 0,
               "Max": 0
           },
           "AcceleratorManufacturers": [
               "xilinx"
           ],
           "AcceleratorNames": [
               "t4"
           ],
           "AcceleratorTotalMemoryMiB": {
               "Min": 0,
               "Max": 0
           }
       },
       "MaxResults": 0,
       "NextToken": ""
   }
   ```

1. Create a JSON configuration file using the output from the previous step, and configure it as follows:
**Note**  
You must provide values for `ArchitectureTypes`, `VirtualizationTypes`, `VCpuCount`, and `MemoryMiB`\. You can omit the other attributes; when omitted, the default values are used\.  
For a description of each attribute and their default values, see [get\-instance\-types\-from\-instance\-requirements](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-instance-types-from-instance-requirements.html    ) in the *Amazon EC2 Command Line Reference*\.

   1. For `ArchitectureTypes`, specify one or more types of processor architecture\.

   1. For `VirtualizationTypes`, specify one or more types of virtualization\.

   1. For `VCpuCount`, specify the minimum and maximum number of vCPUs\. To specify no minimum limit, for `Min`, specify `0`\. To specify no maximum limit, omit the `Max` parameter\.

   1. For `MemoryMiB`, specify the minimum and maximum amount of memory in MiB\. To specify no minimum limit, for `Min`, specify `0`\. To specify no maximum limit, omit the `Max` parameter\.

   1. You can optionally specify one or more of the other attributes to further constrain the list of instance types that are returned\.

1. To preview the instance types that have the attributes that you specified in the JSON file, use the [get\-instance\-types\-from\-instance\-requirements](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-instance-types-from-instance-requirements.html      ) command, and specify the name and path to your JSON file by using the `--cli-input-json` parameter\. You can optionally format the output to appear in a table format\.

   ```
   aws ec2 get-instance-types-from-instance-requirements \
       --cli-input-json file://attributes.json \
       --output table
   ```

   Example *attributes\.json* file

   In this example, the required attributes are included in the JSON file\. They are `ArchitectureTypes`, `VirtualizationTypes`, `VCpuCount`, and `MemoryMiB`\. In addition, the optional `InstanceGenerations` attribute is also included\. Note that for `MemoryMiB`, the `Max` value can be omitted to indicate that there is no limit\.

   ```
   {
       
       "ArchitectureTypes": [
           "x86_64"
       ],
       "VirtualizationTypes": [
           "hvm"
       ],
       "InstanceRequirements": {
           "VCpuCount": {
               "Min": 4,
               "Max": 6
           },
           "MemoryMiB": {
               "Min": 2048
           },
           "InstanceGenerations": [
               "current"
           ]
       }
   }
   ```

   Example output

   ```
   ------------------------------------------
   |GetInstanceTypesFromInstanceRequirements|
   +----------------------------------------+
   ||             InstanceTypes            ||
   |+--------------------------------------+|
   ||             InstanceType             ||
   |+--------------------------------------+|
   ||  c4.xlarge                           ||
   ||  c5.xlarge                           ||
   ||  c5a.xlarge                          ||
   ||  c5ad.xlarge                         ||
   ||  c5d.xlarge                          ||
   ||  c5n.xlarge                          ||
   ||  d2.xlarge                           ||
   ...
   ```

1. After identifying instance types that meet your needs, make note of the instance attributes that you used so that you can use them when configuring your fleet request\.
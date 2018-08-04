# EC2 Fleet Example Configurations<a name="ec2-fleet-examples"></a>

The following examples show launch configurations that you can use with the [create\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command to create an EC2 Fleet\. For more information, see the [EC2 Fleet JSON Configuration File Reference](manage-ec2-fleet.md#ec2-fleet-json-reference)\.

1. [Launch Spot Instances as the default purchasing model](#ec2-fleet-config1)

1. [Launch On\-Demand Instances as the default purchasing model](#ec2-fleet-config2)

1. [Launch On\-Demand Instances as the primary capacity](#ec2-fleet-config3)

1. [Launch Spot Instances using the lowestPrice allocation strategy](#ec2-fleet-config4)

## Example 1: Launch Spot Instances as the Default Purchasing Model<a name="ec2-fleet-config1"></a>

The following example specifies the minimum parameters required in an EC2 Fleet: a launch template, target capacity, and default purchasing model\. The launch template is identified by its launch template ID and version number\. The target capacity for the fleet is 2 instances, and the default purchasing model is `spot`, which results in the fleet launching 2 Spot Instances\.

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

## Example 2: Launch On\-Demand Instances as the Default Purchasing Model<a name="ec2-fleet-config2"></a>

The following example specifies the minimum parameters required in an EC2 Fleet: a launch template, target capacity, and default purchasing model\. The launch template is identified by its launch template ID and version number\. The target capacity for the fleet is 2 instances, and the default purchasing model is `on-demand`, which results in the fleet launching 2 On\-Demand Instances\.

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

## Example 3: Launch On\-Demand Instances as the Primary Capacity<a name="ec2-fleet-config3"></a>

The following example specifies the total target capacity of 2 instances for the fleet, and a target capacity of 1 On\-Demand Instance\. The default purchasing model is `spot`\. The fleet launches 1 On\-Demand Instance as specified, but needs to launch one more instance to fulfil the total target capacity\. The purchasing model for the difference is calculated as `TotalTargetCapacity` – `OnDemandTargetCapacity` = `DefaultTargetCapacityType`, which results in the fleet launching 1 Spot Instance\.

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

## Example 4: Launch Spot Instances Using the Lowest Price Allocation Strategy<a name="ec2-fleet-config4"></a>

If the allocation strategy for Spot Instances is not specified, the default allocation strategy, which is `lowestPrice`, is used\. The following example uses the `lowestPrice` allocation strategy\. The three launch specifications, which override the launch template, have different instance types but the same weighted capacity and subnet\. The total target capacity is 2 instances and the default purchasing model is `spot`\. The EC2 Fleet launches 2 Spot Instances using the instance type of the launch specification with the lowest price\.

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
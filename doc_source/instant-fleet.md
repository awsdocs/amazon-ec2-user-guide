# Use an EC2 Fleet of type 'instant'<a name="instant-fleet"></a>

The EC2 Fleet of type *instant* is a synchronous one\-time request that makes only one attempt to launch your desired capacity\. The API response lists the instances that launched, along with errors for those instances that could not be launched\. There are several benefits to using an EC2 Fleet of type *instant*, which are described in this article\. Example configurations are provided at the end of the article\.

For workloads that need a launch\-only API to launch EC2 instances, you can use the RunInstances API\. However, with RunInstances, you can only launch On\-Demand Instances or Spot Instances, but not both in the same request\. Furthermore, when you use RunInstances to launch Spot Instances, your Spot Instance request is limited to one instance type and one Availability Zone\. This targets a single Spot capacity pool \(a set of unused instances with the same instance type and Availability Zone\)\. If the Spot capacity pool does not have sufficient Spot Instance capacity for your request, the RunInstances call fails\.

Instead of using RunInstances to launch Spot Instances, we recommend that you rather use the CreateFleet API with the `type` parameter set to `instant` for the following benefits: 
+ **Launch On\-Demand Instances and Spot Instances in one request\.** An EC2 Fleet can launch On\-Demand Instances, Spot Instances, or both\. The request for Spot Instances is fulfilled if there is available capacity and the maximum price per hour for your request exceeds the Spot price\.
+ **Increase the availability of Spot Instances\.** By using an EC2 Fleet of type `instant`, you can launch Spot Instances following [ Spot best practices](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-best-practices.html) with the resulting benefits:
  + **Spot best practice: Be flexible about instance types and Availability Zones\.**

    Benefit: By specifying several instance types and Availability Zones, you increase the number of Spot capacity pools\. This gives the Spot service a better chance of finding and allocating your desired Spot compute capacity\. A good rule of thumb is to be flexible across at least 10 instance types for each workload and make sure that all Availability Zones are configured for use in your VPC\.
  + **Spot best practice: Use the capacity\-optimized allocation strategy\.**

    Benefit: The `capacity-optimized` allocation strategy automatically provisions instances from the most\-available Spot capacity pools\. Because your Spot Instance capacity is sourced from pools with optimal capacity, this decreases the possibility that your Spot Instances will be interrupted when Amazon EC2 needs the capacity back\.
+ **Get access to a wider set of capabilities\.** For workloads that need a launch\-only API, and where you prefer to manage the lifecycle of your instance rather than let EC2 Fleet manage it for you, use the EC2 Fleet of type `instant` instead of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API\. EC2 Fleet provides a wider set of capabilities than RunInstances, as demonstrated in the following examples\. For all other workloads, you should use Amazon EC2 Auto Scaling because it supplies a more comprehensive feature set for a wide variety of workloads, like ELB\-backed applications, containerized workloads, and queue processing jobs\.

AWS services like Amazon EC2 Auto Scaling and Amazon EMR use EC2 Fleet of type *instant* to launch EC2 instances\.

## Prerequisites for EC2 Fleet of type instant<a name="instant-fleet-prerequisites"></a>

For the prerequisites for creating an EC2 Fleet, see [EC2 Fleet prerequisites](manage-ec2-fleet.md#ec2-fleet-prerequisites)\.

## How instant EC2 Fleet works<a name="how-instant-fleet-works"></a>

When working with an EC2 Fleet of type `instant`, the sequence of events is as follows:

1. Configure the [CreateFleet](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFleet.html) request type as `instant`\. For more information, see [Create an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\. Note that after you make the API call, you can't modify it\.

1. When you make the API call, EC2 Fleet places a synchronous one\-time request for your desired capacity\.

1. The API response lists the instances that launched, along with errors for those instances that could not be launched\.

1. You can describe your EC2 Fleet, list the instances associated with your EC2 Fleet, and view the history of your EC2 Fleet\.

1. After your instances have launched, you can [delete the fleet request](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/manage-ec2-fleet.html#delete-fleet)\. When deleting the fleet request, you can also choose to terminate the associated instances, or leave them running\.

1. You can terminate the instances at any time\.

## Examples<a name="instant-fleet-examples"></a>

The following examples show how to use EC2 Fleet of type `instant` for different use cases\. For more information about using the EC2 CreateFleet API parameters, see [CreateFleet](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateFleet.html) in the *Amazon EC2 API Reference*\.

**Topics**
+ [Example 1: Launch Spot Instances with the capacity\-optimized allocation strategy](#instant-fleet-example-1)
+ [Example 2: Launch a single Spot Instance with the capacity\-optimized allocation strategy](#instant-fleet-example-2)
+ [Example 3: Launch Spot Instances using instance weighting](#instant-fleet-example-3)
+ [Example 4: Launch Spot Instances within single Availability zone](#instant-fleet-example-4)
+ [Example 5: Launch Spot Instances of single instance type within single Availability zone](#instant-fleet-example-5)
+ [Example 6: Launch Spot Instances only if minimum target capacity can be launched](#instant-fleet-example-6)
+ [Example 7: Launch Spot Instances only if minimum target capacity can be launched of same Instance Type in a single Availability Zone](#instant-fleet-example-7)
+ [Example 8: Launch instances with multiple Launch Templates](#instant-fleet-example-8)
+ [Example 9: Launch Spot Instance with a base of On\-Demand Instances](#instant-fleet-example-9)
+ [Example 10: Launch Spot Instances using capacity\-optimized allocation strategy with a base of On\-Demand Instances using Capacity Reservations and the prioritized allocation strategy](#instant-fleet-example-10)
+ [Example 11: Launch Spot Instances using capacity\-optimized\-prioritized allocation strategy](#instant-fleet-example-11)

### Example 1: Launch Spot Instances with the capacity\-optimized allocation strategy<a name="instant-fleet-example-1"></a>

The following example specifies the parameters required in an EC2 Fleet of type `instant`: a launch template, target capacity, default purchasing option, and launch template overrides\.
+ The launch template is identified by its launch template name and version number\.
+ The 12 launch template overrides specify 4 different instance types and 3 different subnets, each in a separate Availability Zone\. Each instance type and subnet combination defines a Spot capacity pool, resulting in 12 Spot capacity pools\.
+ The target capacity for the fleet is 20 instances\.
+ The default purchasing option is `spot`, which results in the fleet attempting to launch 20 Spot Instances into the Spot capacity pool with optimal capacity for the number of instances that are launching\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized"
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 2: Launch a single Spot Instance with the capacity\-optimized allocation strategy<a name="instant-fleet-example-2"></a>

You can optimally launch one Spot Instance at a time by making multiple EC2 Fleet API calls of type `instant`, by setting the TotalTargetCapacity to 1\.

The following example specifies the parameters required in an EC2 Fleet of type instant: a launch template, target capacity, default purchasing option, and launch template overrides\. The launch template is identified by its launch template name and version number\. The 12 launch template overrides have 4 different instance types and 3 different subnets, each in a separate Availability Zone\. The target capacity for the fleet is 1 instance, and the default purchasing option is spot, which results in the fleet attempting to launch a Spot Instance from one of the 12 Spot capacity pools based on the capacity\-optimized allocation strategy, to launch a Spot Instance from the most\-available capacity pool\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized"
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 1,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 3: Launch Spot Instances using instance weighting<a name="instant-fleet-example-3"></a>

The following examples use instance weighting, which means that the price is per unit hour instead of per instance hour\. Each launch configuration lists a different instance type and a different weight based on how many units of the workload can run on the instance assuming a unit of the workload requires a 15 GB of memory and 4 vCPUs\. For example an m5\.xlarge \(4 vCPUs and 16 GB of memory\) can run one unit and is weighted 1, m5\.2xlarge \(8 vCPUs and 32 GB of memory\) can run 2 units and is weighted 2, and so on\. The total target capacity is set to 40 units\. The default purchasing option is spot, and the allocation strategy is capacity\-optimized, which results in either 40 m5\.xlarge \(40 divided by 1\), 20 m5\.2xlarge \(40 divided by 2\), 10 m5\.4xlarge \(40 divided by 4\), 5 m5\.8xlarge \(40 divided by 8\), or a mix of the instance types with weights adding up to the desired capacity based on the capacity\-optimized allocation strategy\.

For more information, see [EC2 Fleet instance weighting](ec2-fleet-configuration-strategies.md#ec2-fleet-instance-weighting)\.

```
{
   "SpotOptions":{
      "AllocationStrategy":"capacity-optimized"
   },
   "LaunchTemplateConfigs":[
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"m5.xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":1
            },
            {
               "InstanceType":"m5.xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":1
            },
            {
               "InstanceType":"m5.xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":1
            },
            {
               "InstanceType":"m5.2xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":2
            },
            {
               "InstanceType":"m5.2xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":2
            },
            {
               "InstanceType":"m5.2xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":2
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":4
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":4
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":4
            },
            {
               "InstanceType":"m5.8xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":8
            },
            {
               "InstanceType":"m5.8xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":8
            },
            {
               "InstanceType":"m5.8xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":8
            }
         ]
      }
   ],
   "TargetCapacitySpecification":{
      "TotalTargetCapacity":40,
      "DefaultTargetCapacityType":"spot"
   },
   "Type":"instant"
}
```

### Example 4: Launch Spot Instances within single Availability zone<a name="instant-fleet-example-4"></a>

You can configure a fleet to launch all instances in a single Availability Zone by setting the Spot options SingleAvailabilityZone to true\.

The 12 launch template overrides have different instance types and subnets \(each in a separate Availability Zone\) but the same weighted capacity\. The total target capacity is 20 instances, the default purchasing option is spot, and the Spot allocation strategy is capacity\-optimized\. The EC2 Fleet launches 20 Spot Instances all in a single AZ, from the Spot capacity pool\(s\) with optimal capacity using the launch specifications\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        "SingleAvailabilityZone": true
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 5: Launch Spot Instances of single instance type within single Availability zone<a name="instant-fleet-example-5"></a>

You can configure a fleet to launch all instances of the same instance type and in a single Availability Zone by setting the SpotOptions SingleInstanceType to true and SingleAvailabilityZone to true\.

The 12 launch template overrides have different instance types and subnets \(each in a separate Availability Zone\) but the same weighted capacity\. The total target capacity is 20 instances, the default purchasing option is spot, the Spot allocation strategy is capacity\-optimized\. The EC2 Fleet launches 20 Spot Instances of the same instance type all in a single AZ from the Spot Instance pool with optimal capacity using the launch specifications\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        "SingleInstanceType": true,
        "SingleAvailabilityZone": true
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 6: Launch Spot Instances only if minimum target capacity can be launched<a name="instant-fleet-example-6"></a>

You can configure a fleet to launch instances only if the minimum target capacity can be launched by setting the Spot options MinTargetCapacity to the minimum target capacity you want to launch together\. 

The 12 launch template overrides have different instance types and subnets \(each in a separate Availability Zone\) but the same weighted capacity\. The total target capacity and the minimum target capacity are both set to 20 instances, the default purchasing option is spot, the Spot allocation strategy is capacity\-optimized\. The EC2 Fleet launches 20 Spot Instances from the Spot capacity pool with optimal capacity using the launch template overrides, only if it can launch all 20 instances at the same time\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        "MinTargetCapacity": 20
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 7: Launch Spot Instances only if minimum target capacity can be launched of same Instance Type in a single Availability Zone<a name="instant-fleet-example-7"></a>

You can configure a fleet to launch instances only if the minimum target capacity can be launched with a single instance type in a single Availability Zone by setting the Spot options MinTargetCapacity to the minimum target capacity you want to launch together along with SingleInstanceType and SingleAvailabilityZone options\.

The 12 launch specifications which override the launch template, have different instance types and subnets \(each in a separate Availability Zone\) but the same weighted capacity\. The total target capacity and the minimum target capacity are both set to 20 instances, the default purchasing option is spot, the Spot allocation strategy is capacity\-optimized, the SingleInstanceType is true and SingleAvailabilityZone is true\. The EC2 Fleet launches 20 Spot Instances of the same Instance type all in a single AZ from the Spot capacity pool with optimal capacity using the launch specifications, only if it can launch all 20 instances at the same time\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized",
        "SingleInstanceType": true,
        "SingleAvailabilityZone": true,
        "MinTargetCapacity": 20
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.4xlarge",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.4xlarge",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 8: Launch instances with multiple Launch Templates<a name="instant-fleet-example-8"></a>

You can configure a fleet to launch instances with different launch specifications for different instance types or a group of instance types, by specifying multiple launch templates\. In this example we want have different EBS volume sizes for different instance types and we have that configured in the launch templates ec2\-fleet\-lt\-4xl, ec2\-fleet\-lt\-9xl and ec2\-fleet\-lt\-18xl\.

In this example, we are using 3 different launch templates for the 3 instance types based on their size\. The launch specification overrides on all the launch templates use instance weights based on the vCPUs on the instance type\. The total target capacity is 144 units, the default purchasing option is spot, and the Spot allocation strategy is capacity\-optimized\. The EC2 Fleet can either launch 9 c5n\.4xlarge \(144 divided by 16\) using the launch template ec2\-fleet\-4xl or 4 c5n\.9xlarge \(144 divided by 36\) using the launch template ec2\-fleet\-9xl, or 2 c5n\.18xlarge \(144 divided by 72\) using the launch template ec2\-fleet\-18xl, or a mix of the instance types with weights adding up to the desired capacity based on the capacity\-optimized allocation strategy\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized"
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt-18xl",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5n.18xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":72
            },
            {
               "InstanceType":"c5n.18xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":72
            },
            {
               "InstanceType":"c5n.18xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":72
            }
         ]
      },
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt-9xl",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5n.9xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":36
            },
            {
               "InstanceType":"c5n.9xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":36
            },
            {
               "InstanceType":"c5n.9xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":36
            }
         ]
      },
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt-4xl",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5n.4xlarge",
               "SubnetId":"subnet-fae8c380",
               "WeightedCapacity":16
            },
            {
               "InstanceType":"c5n.4xlarge",
               "SubnetId":"subnet-e7188bab",
               "WeightedCapacity":16
            },
            {
               "InstanceType":"c5n.4xlarge",
               "SubnetId":"subnet-49e41922",
               "WeightedCapacity":16
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 144,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 9: Launch Spot Instance with a base of On\-Demand Instances<a name="instant-fleet-example-9"></a>

The following example specifies the total target capacity of 20 instances for the fleet, and a target capacity of 5 On\-Demand Instances\. The default purchasing option is spot\. The fleet launches 5 On\-Demand Instance as specified, but needs to launch 15 more instances to fulfill the total target capacity\. The purchasing option for the difference is calculated as TotalTargetCapacity – OnDemandTargetCapacity = DefaultTargetCapacityType, which results in the fleet launching 15 Spot Instances form one of the 12 Spot capacity pools based on the capacity\-optimized allocation strategy\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized"
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-49e41922"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-fae8c380"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-e7188bab"
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-49e41922"
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "OnDemandTargetCapacity": 5,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

### Example 10: Launch Spot Instances using capacity\-optimized allocation strategy with a base of On\-Demand Instances using Capacity Reservations and the prioritized allocation strategy<a name="instant-fleet-example-10"></a>

You can configure a fleet to use On\-Demand Capacity Reservations first when launching a base of On\-Demand Instances with the default target capacity type as spot by setting the usage strategy for Capacity Reservations to use\-capacity\-reservations\-first\. And if multiple instance pools have unused Capacity Reservations, the chosen On\-Demand allocation strategy is applied\. In this example, the On\-Demand allocation strategy is prioritized\.

In this example, there are 6 available unused Capacity Reservations\. This is less than the fleet's target On\-Demand capacity of 10 On\-Demand Instances\.

The account has the following 6 unused Capacity Reservations in 2 pools\. The number of Capacity Reservations in each pool is indicated by AvailableInstanceCount\.

```
{
    "CapacityReservationId": "cr-111", 
    "InstanceType": "m5.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 3, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
 
{
    "CapacityReservationId": "cr-222", 
    "InstanceType": "c5.large", 
    "InstancePlatform": "Linux/UNIX", 
    "AvailabilityZone": "us-east-1a", 
    "AvailableInstanceCount": 3, 
    "InstanceMatchCriteria": "open", 
    "State": "active"
}
```

The following fleet configuration shows only the pertinent configurations for this example\. The On\-Demand allocation strategy is prioritized, and the usage strategy for Capacity Reservations is use\-capacity\-reservations\-first\. The Spot allocation strategy is capacity\-optimized\. The total target capacity is 20, the On\-Demand target capacity is 10, and the default target capacity type is spot\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized"
    },
    "OnDemandOptions":{
       "CapacityReservationOptions": {
         "UsageStrategy": "use-capacity-reservations-first"
       },
       "AllocationStrategy":"prioritized"
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 1.0
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 2.0
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 3.0
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 4.0
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 5.0
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 6.0
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 7.0
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 8.0
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 9.0
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 10.0
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 11.0
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 12.0
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "OnDemandTargetCapacity": 10,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```

After you create the instant fleet using the preceding configuration, the following 20 instances are launched to meet the target capacity:
+ 7 c5\.large On\-Demand Instances in us\-east\-1a – c5\.large in us\-east\-1a is prioritized first, and there are 3 available unused c5\.large Capacity Reservations\. The Capacity Reservations are used first to launch 3 On\-Demand Instances plus 4 additional On\-Demand Instances are launched according to the On\-Demand allocation strategy, which is prioritized in this example\.
+ 3 m5\.large On\-Demand Instances in us\-east\-1a – m5\.large in us\-east\-1a is prioritized second, and there are 3 available unused c3\.large Capacity Reservations\.
+ 10 Spot Instances from one of the 12 Spot capacity pools that has the optimal capacity according to the capacity\-optimized allocation strategy\.

After the fleet is launched, you can run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the c5\.large and m5\.large Capacity Reservations were used\.

```
{
    "CapacityReservationId": "cr-111",
    "InstanceType": "m5.large",  
    "AvailableInstanceCount": 0
}
 
{
    "CapacityReservationId": "cr-222",
    "InstanceType": "c5.large", 
    "AvailableInstanceCount": 0
}
```

### Example 11: Launch Spot Instances using capacity\-optimized\-prioritized allocation strategy<a name="instant-fleet-example-11"></a>

The following example specifies the parameters required in an EC2 Fleet of type instant: a launch template, target capacity, default purchasing option, and launch template overrides\. The launch template is identified by its launch template name and version number\. The 12 launch specifications which override the launch template have 4 different instance types with a priority assigned, and 3 different subnets, each in a separate Availability Zone\. The target capacity for the fleet is 20 instances, and the default purchasing option is spot, which results in the fleet attempting to launch 20 Spot Instances from one of the 12 Spot capacity pools based on the capacity\-optimized\-prioritized allocation strategy, which implements priorities on a best\-effort basis, but optimizes for capacity first\.

```
{
    "SpotOptions": {
        "AllocationStrategy": "capacity-optimized-prioritized"
    },
    "LaunchTemplateConfigs": [
      {
         "LaunchTemplateSpecification":{
            "LaunchTemplateName":"ec2-fleet-lt1",
            "Version":"$Latest"
         },
         "Overrides":[
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 1.0
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 1.0
            },
            {
               "InstanceType":"c5.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 1.0
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 2.0
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 2.0
            },
            {
               "InstanceType":"c5d.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 2.0
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 3.0
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 3.0
            },
            {
               "InstanceType":"m5.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 3.0
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-fae8c380",
               "Priority": 4.0
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-e7188bab",
               "Priority": 4.0
            },
            {
               "InstanceType":"m5d.large",
               "SubnetId":"subnet-49e41922",
               "Priority": 4.0
            }
         ]
      }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
        "DefaultTargetCapacityType": "spot"
    },
    "Type": "instant"
}
```
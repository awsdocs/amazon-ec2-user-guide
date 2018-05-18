# EC2 Fleet Configuration Strategies<a name="ec2-fleet-configuration-strategies"></a>

An *EC2 Fleet* is a group of On\-Demand Instances and Spot Instances\. 

The EC2 Fleet attempts to launch the number of On\-Demand Instances and Spot Instances to meet the specified target capacity\. The request for Spot Instances is fulfilled if the specified Spot price exceeds the current Spot price and there is available capacity\. The fleet also attempts to maintain its target capacity if your Spot Instances are interrupted due to a change in Spot prices or available capacity\.

A *Spot Instance pool* is a set of unused EC2 instances with the same instance type, operating system, Availability Zone, and network platform \(EC2\-Classic or EC2\-VPC\)\. When you create an EC2 Fleet, you can include multiple launch specifications, which vary by instance type, Availability Zone, subnet, and maximum price\. The fleet selects the Spot Instance pools that are used to fulfill the request, based on the launch specifications included in your request, and the configuration of the request\. The Spot Instances come from the selected pools\.

An EC2 Fleet enables you to provision large amounts of EC2 capacity that makes sense for your application based on number of cores or instances, or amount of memory\. For example, you can specify an EC2 Fleet to launch a target capacity of 200 instances, of which 130 are On\-Demand Instances and the rest are Spot Instances\. Or you can request 1000 cores with a minimum of 2 GB of RAM per core\. The fleet determines the combination of Amazon EC2 options to launch that capacity at the absolute lowest cost\.

Use the appropriate configuration strategies to create an EC2 Fleet that meets your needs\.

**Topics**
+ [Planning an EC2 Fleet](#plan-ec2-fleet)
+ [EC2 Fleet Request Types](#ec2-fleet-request-type)
+ [Allocation Strategy for Spot Instances](#ec2-fleet-allocation-strategy)
+ [Configuring EC2 Fleet for On\-Demand Backup](#ec2-fleet-on-demand-backup)
+ [Maximum Price Overrides](#ec2-fleet-price-overrides)
+ [EC2 Fleet Instance Weighting](#ec2-fleet-instance-weighting)
+ [Walkthrough: Using EC2 Fleet with Instance Weighting](#ec2-fleet-instance-weighting-walkthrough)
+ [Walkthrough: Using EC2 Fleet with On\-Demand as the Primary Capacity](#ec2-fleet-on-demand-walkthrough)

## Planning an EC2 Fleet<a name="plan-ec2-fleet"></a>

When planning your EC2 Fleet, we recommend that you do the following:
+ Determine whether you want to create an EC2 Fleet that submits a one\-time request for the desired target capacity, or one that maintains a target capacity over time\. For more information, see [EC2 Fleet Request Types](#ec2-fleet-request-type)\.
+ Determine the instance types that meet your application requirements\.
+ If you plan to include Spot Instances in your EC2 Fleet, review [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices) before you create the fleet\. Use these best practices when you plan your fleet so that you can provision the instances at the lowest possible price\.
+ Determine the target capacity for your EC2 Fleet\. You can set target capacity in instances or in custom units\. For more information, see [EC2 Fleet Instance Weighting](#ec2-fleet-instance-weighting)\.
+ Determine what portion of the EC2 Fleet target capacity must be On\-Demand capacity and Spot capacity\. You can specify 0 for On\-Demand capacity or Spot capacity, or both\.
+ Determine your price per unit, if you are using instance weighting\. To calculate the price per unit, divide the price per instance hour by the number of units \(or weight\) that this instance represents\. If you are not using instance weighting, the default price per unit is the price per instance hour\.
+ Review the possible options for your EC2 Fleet\. For more information, see the [EC2 Fleet JSON Configuration File Reference](manage-ec2-fleet.md#ec2-fleet-json-reference)\. For EC2 Fleet configuration examples, see [EC2 Fleet Example Configurations](ec2-fleet-examples.md)\.

## EC2 Fleet Request Types<a name="ec2-fleet-request-type"></a>

There are two types of EC2 Fleet requests: `request` and `maintain`\. You can create a fleet to submit a one\-time request for your desired capacity, or require it to maintain the target capacity over time\.

If you configure the fleet type as `request`, EC2 Fleet places a one\-time request for the target capacity, and does not attempt to replenish Spot Instances if capacity is diminished\. If capacity is not available, the fleet does not submit requests in alternative Spot Instance pools\.

If you configure the fleet type as `maintain`, EC2 Fleet places requests to meet the target capacity, and automatically replenishes any interrupted Spot Instances\.

You cannot modify the target capacity of a one\-time request after it's been submitted\. To change the target capacity of a one\-time request, delete the EC2 Fleet and create a new one\.

Both types of requests benefit from an allocation strategy\. For more information, see [Allocation Strategy for Spot Instances](#ec2-fleet-allocation-strategy)\.

## Allocation Strategy for Spot Instances<a name="ec2-fleet-allocation-strategy"></a>

The allocation strategy for your EC2 Fleet determines how it fulfills your request for Spot Instances from the possible Spot Instance pools represented by its launch specifications\. The following are the allocation strategies that you can specify in your fleet:

`lowestPrice`  
The Spot Instances come from the pool with the lowest price\. This is the default strategy\.

`diversified`  
The Spot Instances are distributed across all pools\.

### Maintaining Target Capacity<a name="ec2-fleet-maintain-fleet-capacity"></a>

After Spot Instances are terminated due to a change in the Spot price or available capacity of a Spot Instance pool, the EC2 Fleet launches replacement Spot Instances\. If the allocation strategy is `lowestPrice`, the fleet launches replacement instances in the pool where the Spot price is currently the lowest\. If the allocation strategy is `diversified`, the fleet distributes the replacement Spot Instances across the remaining pools\.

### Configuring EC2 Fleet for Cost Optimization<a name="ec2-fleet-strategy-cost-optimization"></a>

To optimize the costs for your use of Spot Instances, specify the `lowestPrice` allocation strategy so that EC2 Fleet automatically deploys the cheapest combination of instance types and Availability Zones based on the current Spot price\.

For On\-Demand Instance target capacity, EC2 Fleet always selects the cheapest instance type based on the public On\-Demand price, while continuing to follow the allocation strategy \(either `lowestPrice` or `diversified`\) for Spot Instances\.

### Choosing the Appropriate Allocation Strategy<a name="ec2-fleet-allocation-use-cases"></a>

You can optimize your fleet based on your use case\.

If your fleet is small or runs for a short time, the probability that your Spot Instances will be interrupted is low, even with all the instances in a single Spot Instance pool\. Therefore, the `lowestPrice` strategy is likely to meet your needs while providing the lowest cost\.

If your fleet is large or runs for a long time, you can improve the availability of your fleet by distributing the Spot Instances across multiple pools\. For example, if your EC2 Fleet specifies 10 pools and a target capacity of 100 instances, the fleet launches 10 Spot Instances in each pool\. If the Spot price for one pool exceeds your maximum price for this pool, only 10% of your fleet is affected\. Using this strategy also makes your fleet less sensitive to increases in the Spot price in any one pool over time\.

With the `diversified` strategy, the EC2 Fleet does not launch Spot Instances into any pools with a Spot price that is equal to or higher than the [On\-Demand price](https://aws.amazon.com/ec2/pricing/)\.

## Configuring EC2 Fleet for On\-Demand Backup<a name="ec2-fleet-on-demand-backup"></a>

If you have urgent, unpredictable scaling needs, such as a news website that must scale during a major news event or game launch, we recommend that you specify alternative instance types for your On\-Demand Instances, in the event that your preferred option does not have sufficient available capacity\. For example, you might prefer `c5.2xlarge` On\-Demand Instances, but if there is insufficient available capacity, you'd be willing to use some `c4.2xlarge` instances during peak load\. In this case, EC2 Fleet attempts to fulfill all your target capacity using `c5.2xlarge` instances, but if there is insufficient capacity, it automatically launches `c4.2xlarge` instances to fulfill the target capacity\.

## Maximum Price Overrides<a name="ec2-fleet-price-overrides"></a>

Each EC2 Fleet can include a global maximum price, or use the default \(the On\-Demand price\)\. The fleet uses this as the default maximum price for each of its launch specifications\.

You can optionally specify a maximum price in one or more launch specifications\. This price is specific to the launch specification\. If a launch specification includes a specific price, the EC2 Fleet uses this maximum price, overriding the global maximum price\. Any other launch specifications that do not include a specific maximum price still use the global maximum price\.

## EC2 Fleet Instance Weighting<a name="ec2-fleet-instance-weighting"></a>

When you create an EC2 Fleet, you can define the capacity units that each instance type would contribute to your application's performance, and adjust your maximum price for each launch specification accordingly using *instance weighting*\.

By default, the price that you specify is *per instance hour*\. When you use the instance weighting feature, the price that you specify is *per unit hour*\. You can calculate your price per unit hour by dividing your price for an instance type by the number of units that it represents\. EC2 Fleet calculates the number of instances to launch by dividing the target capacity by the instance weight\. If the result isn't an integer, the fleet rounds it up to the next integer, so that the size of your fleet is not below its target capacity\. The fleet can select any pool that you specify in your launch specification, even if the capacity of the instances launched exceeds the requested target capacity\.

The following table includes examples of calculations to determine the price per unit for an EC2 Fleet with a target capacity of 10\.


****  

| Instance type | Instance weight | Target capacity | Number of instances launched | Price per instance hour | Price per unit hour | 
| --- | --- | --- | --- | --- | --- | 
|  `r3.xlarge`  |  2  | 10 |  5 \(10 divided by 2\)  |  $0\.05  |  \.025 \(\.05 divided by 2\)  | 
|  `r3.8xlarge`  |  8  | 10 |  2 \(10 divided by 8, result rounded up\)  |  $0\.10  |  \.0125 \(\.10 divided by 8\)  | 

Use EC2 Fleet instance weighting as follows to provision the target capacity that you want in the pools with the lowest price per unit at the time of fulfillment:

1. Set the target capacity for your EC2 Fleet either in instances \(the default\) or in the units of your choice, such as virtual CPUs, memory, storage, or throughput\.

1. Set the price per unit\.

1. For each launch specification, specify the weight, which is the number of units that the instance type represents toward the target capacity\.

**Instance Weighting Example**  
Consider an EC2 Fleet request with the following configuration:
+ A target capacity of 24
+ A launch specification with an instance type `r3.2xlarge` and a weight of 6
+ A launch specification with an instance type `c3.xlarge` and a weight of 5

The weights represent the number of units that instance type represents toward the target capacity\. If the first launch specification provides the lowest price per unit \(price for `r3.2xlarge` per instance hour divided by 6\), the EC2 Fleet would launch four of these instances \(24 divided by 6\)\.

If the second launch specification provides the lowest price per unit \(price for `c3.xlarge` per instance hour divided by 5\), the EC2 Fleet would launch five of these instances \(24 divided by 5, result rounded up\)\.

**Instance Weighting and Allocation Strategy**  
Consider an EC2 Fleet request with the following configuration:
+ A target capacity of 30 Spot Instances
+ A launch specification with an instance type `c3.2xlarge` and a weight of 8
+ A launch specification with an instance type `m3.xlarge` and a weight of 8
+ A launch specification with an instance type `r3.xlarge` and a weight of 8

The EC2 Fleet would launch four instances \(30 divided by 8, result rounded up\)\. With the `lowestPrice` strategy, all four instances come from the pool that provides the lowest price per unit\. With the `diversified` strategy, the fleet launches one instance in each of the three pools, and the fourth instance in whichever of the three pools provides the lowest price per unit\.

## Walkthrough: Using EC2 Fleet with Instance Weighting<a name="ec2-fleet-instance-weighting-walkthrough"></a>

This walkthrough uses a fictitious company called Example Corp to illustrate the process of requesting an EC2 Fleet using instance weighting\.

### Objective<a name="ec2-fleet-instance-weighting-walkthrough-objective"></a>

Example Corp, a pharmaceutical company, wants to use the computational power of Amazon EC2 for screening chemical compounds that might be used to fight cancer\.

### Planning<a name="ec2-fleet-instance-weighting-walkthrough-planning"></a>

Example Corp first reviews [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices)\. Next, Example Corp determines the requirements for their EC2 Fleet\.

**Instance Types**  
Example Corp has a compute\- and memory\-intensive application that performs best with at least 60 GB of memory and eight virtual CPUs \(vCPUs\)\. They want to maximize these resources for the application at the lowest possible price\. Example Corp decides that any of the following EC2 instance types would meet their needs:


| Instance type | Memory \(GiB\) | vCPUs | 
| --- | --- | --- | 
|  r3\.2xlarge  |  61  |  8  | 
|  r3\.4xlarge  |  122  |  16  | 
|  r3\.8xlarge  |  244  |  32  | 

**Target Capacity in Units**  
With instance weighting, target capacity can equal a number of instances \(the default\) or a combination of factors such as cores \(vCPUs\), memory \(GiBs\), and storage \(GBs\)\. By considering the base for their application \(60 GB of RAM and eight vCPUs\) as one unit, Example Corp decides that 20 times this amount would meet their needs\. So the company sets the target capacity of their EC2 Fleet request to 20\.

**Instance Weights**  
After determining the target capacity, Example Corp calculates instance weights\. To calculate the instance weight for each instance type, they determine the units of each instance type that are required to reach the target capacity as follows:
+ r3\.2xlarge \(61\.0 GB, 8 vCPUs\) = 1 unit of 20
+ r3\.4xlarge \(122\.0 GB, 16 vCPUs\) = 2 units of 20
+ r3\.8xlarge \(244\.0 GB, 32 vCPUs\) = 4 units of 20

Therefore, Example Corp assigns instance weights of 1, 2, and 4 to the respective launch configurations in their EC2 Fleet request\.

**Price Per Unit Hour**  
Example Corp uses the [On\-Demand price](https://aws.amazon.com/ec2/pricing/) per instance hour as a starting point for their price\. They could also use recent Spot prices, or a combination of the two\. To calculate the price per unit hour, they divide their starting price per instance hour by the weight\. For example:


| Instance type | On\-Demand price | Instance weight | Price per unit hour | 
| --- | --- | --- | --- | 
|  r3\.2xLarge  |  $0\.7  |  1  |  $0\.7  | 
|  r3\.4xLarge  |  $1\.4  |  2  |  $0\.7  | 
|  r3\.8xLarge  |  $2\.8  |  4  |  $0\.7  | 

Example Corp could use a global price per unit hour of $0\.7 and be competitive for all three instance types\. They could also use a global price per unit hour of $0\.7 and a specific price per unit hour of $0\.9 in the `r3.8xlarge` launch specification\.

### Verifying Permissions<a name="ec2-fleet-instance-weighting-walkthrough-permissions"></a>

Before creating an EC2 Fleet, Example Corp verifies that it has an IAM role with the required permissions\. For more information, see [EC2 Fleet Prerequisites](manage-ec2-fleet.md#ec2-fleet-prerequisites)\.

### Creating the EC2 Fleet<a name="ec2-fleet-instance-weighting-walkthrough-request"></a>

Example Corp creates a file, `config.json`, with the following configuration for its EC2 Fleet:

```
{ 
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-07b3bc7625cdab851", 
                "Version": "1"
            }, 
            "Overrides": [
                {
                    "InstanceType": "r3.2xlarge", 
                    "SubnetId": "subnet-482e4972", 
                    "WeightedCapacity": 1
                }
                {
                    "InstanceType": "r3.4xlarge", 
                    "SubnetId": "subnet-482e4972", 
                    "WeightedCapacity": 2
                }
                {
                    "InstanceType": "r3.8xlarge", 
                    "MaxPrice": "0.90", 
                    "SubnetId": "subnet-482e4972", 
                    "WeightedCapacity": 4
                }
            ]
        }
    ], 
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20, 
        "DefaultTargetCapacityType": "spot"
    }, 
    ]
}
```

Example Corp creates the EC2 Fleet using the following [create\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command:

```
aws ec2 create-fleet --cli-input-json file://config.json
```

For more information, see [Creating an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.

### Fulfillment<a name="ec2-fleet-instance-weighting-walkthrough-fulfillment"></a>

The allocation strategy determines which Spot Instance pools your Spot Instances come from\.

With the `lowestPrice` strategy \(which is the default strategy\), the Spot Instances come from the pool with the lowest price per unit at the time of fulfillment\. To provide 20 units of capacity, the EC2 Fleet launches either 20 `r3.2xlarge` instances \(20 divided by 1\), 10 `r3.4xlarge` instances \(20 divided by 2\), or 5 `r3.8xlarge` instances \(20 divided by 4\)\.

If Example Corp used the `diversified` strategy, the Spot Instances would come from all three pools\. The EC2 Fleet would launch 6 `r3.2xlarge` instances \(which provide 6 units\), 3 `r3.4xlarge` instances \(which provide 6 units\), and 2 `r3.8xlarge` instances \(which provide 8 units\), for a total of 20 units\.

## Walkthrough: Using EC2 Fleet with On\-Demand as the Primary Capacity<a name="ec2-fleet-on-demand-walkthrough"></a>

This walkthrough uses a fictitious company called ABC Online to illustrate the process of requesting an EC2 Fleet with On\-Demand as the primary capacity, and Spot capacity if available\.

### Objective<a name="ec2-fleet-on-demand-walkthrough-objective"></a>

ABC Online, a restaurant delivery company, wants to be able to provision Amazon EC2 capacity across EC2 instance types and purchase models to achieve their desired scale, performance, and cost\.

### Planning<a name="ec2-fleet-on-demand-walkthrough-planning"></a>

ABC Online requires a fixed capacity to operate during peak periods, but would like to benefit from increased capacity at a lower price\. ABC Online determines the following requirements for their EC2 Fleet:
+ On\-Demand Instance capacity – ABC Online requires 15 On\-Demand Instances to ensure they can accommodate traffic at peak periods\.
+ Spot Instance capacity – ABC Online would like to improve performance, but at a lower price, by provisioning 5 Spot Instances\.

### Verifying Permissions<a name="ec2-fleet-on-demand-walkthrough-permissions"></a>

Before creating an EC2 Fleet, ABC Online verifies that it has an IAM role with the required permissions\. For more information, see [EC2 Fleet Prerequisites](manage-ec2-fleet.md#ec2-fleet-prerequisites)\.

### Creating the EC2 Fleet<a name="ec2-fleet-on-demand-walkthrough-request"></a>

ABC Online creates a file, `config.json`, with the following configuration for its EC2 Fleet:

```
{
    "LaunchTemplateConfigs": [
        {
            "LaunchTemplateSpecification": {
                "LaunchTemplateId": "lt-07b3bc7625cdab851",
                "Version": "2"
            }

        }
    ],
    "TargetCapacitySpecification": {
        "TotalTargetCapacity": 20,
	    "OnDemandTargetCapacity":15,
        "DefaultTargetCapacityType": "spot"
    }
}
```

ABC Online creates the EC2 Fleet using the following [create\-fleet](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command:

```
aws ec2 create-fleet --cli-input-json file://config.json
```

For more information, see [Creating an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.

### Fulfillment<a name="ec2-fleet-on-demand-walkthrough-fulfillment"></a>

The allocation strategy determines that the On\-Demand capacity is always fulfilled, while the balance of the target capacity is fulfilled as Spot if there is capacity and availability\.
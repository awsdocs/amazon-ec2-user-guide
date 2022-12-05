# Tutorial: Use EC2 Fleet with instance weighting<a name="ec2-fleet-instance-weighting-walkthrough"></a>

This tutorial uses a fictitious company called Example Corp to illustrate the process of requesting an EC2 Fleet using instance weighting\.

## Objective<a name="ec2-fleet-instance-weighting-walkthrough-objective"></a>

Example Corp, a pharmaceutical company, wants to use the computational power of Amazon EC2 for screening chemical compounds that might be used to fight cancer\.

## Planning<a name="ec2-fleet-instance-weighting-walkthrough-planning"></a>

Example Corp first reviews [Spot Best Practices](https://aws.amazon.com/ec2/spot/getting-started/#bestpractices)\. Next, Example Corp determines the requirements for their EC2 Fleet\.

**Instance types**  
Example Corp has a compute\- and memory\-intensive application that performs best with at least 60 GB of memory and eight virtual CPUs \(vCPUs\)\. They want to maximize these resources for the application at the lowest possible price\. Example Corp decides that any of the following EC2 instance types would meet their needs:


| Instance type | Memory \(GiB\) | vCPUs | 
| --- | --- | --- | 
|  r3\.2xlarge  |  61  |  8  | 
|  r3\.4xlarge  |  122  |  16  | 
|  r3\.8xlarge  |  244  |  32  | 

**Target capacity in units**  
With instance weighting, target capacity can equal a number of instances \(the default\) or a combination of factors such as cores \(vCPUs\), memory \(GiBs\), and storage \(GBs\)\. By considering the base for their application \(60 GB of RAM and eight vCPUs\) as one unit, Example Corp decides that 20 times this amount would meet their needs\. So the company sets the target capacity of their EC2 Fleet request to 20\.

**Instance weights**  
After determining the target capacity, Example Corp calculates instance weights\. To calculate the instance weight for each instance type, they determine the units of each instance type that are required to reach the target capacity as follows:
+ r3\.2xlarge \(61\.0 GB, 8 vCPUs\) = 1 unit of 20
+ r3\.4xlarge \(122\.0 GB, 16 vCPUs\) = 2 units of 20
+ r3\.8xlarge \(244\.0 GB, 32 vCPUs\) = 4 units of 20

Therefore, Example Corp assigns instance weights of 1, 2, and 4 to the respective launch configurations in their EC2 Fleet request\.

**Price per unit hour**  
Example Corp uses the [On\-Demand price](https://aws.amazon.com/ec2/pricing/) per instance hour as a starting point for their price\. They could also use recent Spot prices, or a combination of the two\. To calculate the price per unit hour, they divide their starting price per instance hour by the weight\. For example:


| Instance type | On\-Demand price | Instance weight | Price per unit hour | 
| --- | --- | --- | --- | 
|  r3\.2xLarge  |  $0\.7  |  1  |  $0\.7  | 
|  r3\.4xLarge  |  $1\.4  |  2  |  $0\.7  | 
|  r3\.8xLarge  |  $2\.8  |  4  |  $0\.7  | 

Example Corp could use a global price per unit hour of $0\.7 and be competitive for all three instance types\. They could also use a global price per unit hour of $0\.7 and a specific price per unit hour of $0\.9 in the `r3.8xlarge` launch specification\.

## Verify permissions<a name="ec2-fleet-instance-weighting-walkthrough-permissions"></a>

Before creating an EC2 Fleet, Example Corp verifies that it has an IAM role with the required permissions\. For more information, see [EC2 Fleet prerequisites](manage-ec2-fleet.md#ec2-fleet-prerequisites)\.

## Create a launch template<a name="ec2-fleet-instance-weighting-create-launch-template"></a>

Next, Example Corp creates a launch template\. The launch template ID is used in the following step\. For more information, see [Create a launch template](create-launch-template.md)\.

## Create the EC2 Fleet<a name="ec2-fleet-instance-weighting-walkthrough-request"></a>

Example Corp creates a file, `config.json`, with the following configuration for its EC2 Fleet\. In the following example, replace the resource identifiers with your own resource identifiers\.

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
                },
                {
                    "InstanceType": "r3.4xlarge", 
                    "SubnetId": "subnet-482e4972", 
                    "WeightedCapacity": 2
                },
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
    }
}
```

Example Corp creates the EC2 Fleet using the following [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command\.

```
aws ec2 create-fleet \
    --cli-input-json file://config.json
```

For more information, see [Create an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.

## Fulfillment<a name="ec2-fleet-instance-weighting-walkthrough-fulfillment"></a>

The allocation strategy determines which Spot capacity pools your Spot Instances come from\.

With the `lowest-price` strategy \(which is the default strategy\), the Spot Instances come from the pool with the lowest price per unit at the time of fulfillment\. To provide 20 units of capacity, the EC2 Fleet launches either 20 `r3.2xlarge` instances \(20 divided by 1\), 10 `r3.4xlarge` instances \(20 divided by 2\), or 5 `r3.8xlarge` instances \(20 divided by 4\)\.

If Example Corp used the `diversified` strategy, the Spot Instances would come from all three pools\. The EC2 Fleet would launch 6 `r3.2xlarge` instances \(which provide 6 units\), 3 `r3.4xlarge` instances \(which provide 6 units\), and 2 `r3.8xlarge` instances \(which provide 8 units\), for a total of 20 units\.
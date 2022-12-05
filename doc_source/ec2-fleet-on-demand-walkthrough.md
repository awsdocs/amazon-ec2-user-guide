# Tutorial: Use EC2 Fleet with On\-Demand as the primary capacity<a name="ec2-fleet-on-demand-walkthrough"></a>

This tutorial uses a fictitious company called ABC Online to illustrate the process of requesting an EC2 Fleet with On\-Demand as the primary capacity, and Spot capacity if available\.

## Objective<a name="ec2-fleet-on-demand-walkthrough-objective"></a>

ABC Online, a restaurant delivery company, wants to be able to provision Amazon EC2 capacity across EC2 instance types and purchasing options to achieve their desired scale, performance, and cost\.

## Plan<a name="ec2-fleet-on-demand-walkthrough-planning"></a>

ABC Online requires a fixed capacity to operate during peak periods, but would like to benefit from increased capacity at a lower price\. ABC Online determines the following requirements for their EC2 Fleet:
+ On\-Demand Instance capacity – ABC Online requires 15 On\-Demand Instances to ensure that they can accommodate traffic at peak periods\.
+ Spot Instance capacity – ABC Online would like to improve performance, but at a lower price, by provisioning 5 Spot Instances\.

## Verify permissions<a name="ec2-fleet-on-demand-walkthrough-permissions"></a>

Before creating an EC2 Fleet, ABC Online verifies that it has an IAM role with the required permissions\. For more information, see [EC2 Fleet prerequisites](manage-ec2-fleet.md#ec2-fleet-prerequisites)\.

## Create a launch template<a name="ec2-fleet-on-demand-walkthrough-create-launch-template"></a>

Next, ABC Online creates a launch template\. The launch template ID is used in the following step\. For more information, see [Create a launch template](create-launch-template.md)\.

## Create the EC2 Fleet<a name="ec2-fleet-on-demand-walkthrough-request"></a>

ABC Online creates a file, `config.json`, with the following configuration for its EC2 Fleet\. In the following example, replace the resource identifiers with your own resource identifiers\.

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

ABC Online creates the EC2 Fleet using the following [create\-fleet](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-fleet.html) command\.

```
aws ec2 create-fleet \
    --cli-input-json file://config.json
```

For more information, see [Create an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.

## Fulfillment<a name="ec2-fleet-on-demand-walkthrough-fulfillment"></a>

The allocation strategy determines that the On\-Demand capacity is always fulfilled, while the balance of the target capacity is fulfilled as Spot if there is capacity and availability\.
# Tutorial: Launch On\-Demand Instances using targeted Capacity Reservations<a name="ec2-fleet-launch-on-demand-instances-using-targeted-capacity-reservations-walkthrough"></a>

This tutorial walks you through all the steps that you must perform so that your EC2 Fleet launches On\-Demand Instances into `targeted` Capacity Reservations\.

You will learn how to configure a fleet to use `targeted` On\-Demand Capacity Reservations first when launching On\-Demand Instances\. You will also learn how to configure the fleet so that, when the total On\-Demand target capacity exceeds the number of available unused Capacity Reservations, the fleet uses the specified allocation strategy for selecting the instance pools in which to launch the remaining target capacity\.

**EC2 Fleet configuration**

In this tutorial, the fleet configuration is as follows:
+ Target capacity: 10 On\-Demand Instances
+ Total unused `targeted` Capacity Reservations: 6 \(less than the fleet's On\-Demand target capacity of 10 On\-Demand Instances\)
+ Number of Capacity Reservation pools: 2 \(`us-east-1a` and `us-east-1b`\)
+ Number of Capacity Reservations per pool: 3
+ On\-Demand allocation strategy: `lowest-price` \(When the number of unused Capacity Reservations is less than the On\-Demand target capacity, the fleet determines the pools in which to launch the remaining On\-Demand capacity based on the On\-Demand allocation strategy\.\)

  Note that you can also use the `prioritized` allocation strategy instead of the `lowest-price` allocation strategy\.

**To launch On\-Demand Instances into `targeted` Capacity Reservations, you must perform a number of steps, as follows:**
+ [Step 1: Create Capacity Reservations](#ec2-fleet-odcr-step1)
+ [Step 2: Create a Capacity Reservation resource group](#ec2-fleet-odcr-step2)
+ [Step 3: Add the Capacity Reservations to the Capacity Reservation resource group](#ec2-fleet-odcr-step3)
+ [\(Optional\) Step 4: View the Capacity Reservations in the resource group](#ec2-fleet-odcr-step4)
+ [Step 5: Create a launch template that specifies that the Capacity Reservation targets a specific resource group](#ec2-fleet-odcr-step5)
+ [\(Optional\) Step 6: Describe the launch template](#ec2-fleet-odcr-step6)
+ [Step 7: Create an EC2 Fleet](#ec2-fleet-odcr-step7)
+ [\(Optional\) Step 8: View the number of remaining unused Capacity Reservations](#ec2-fleet-odcr-step8)

## Step 1: Create Capacity Reservations<a name="ec2-fleet-odcr-step1"></a>

Use the [create\-capacity\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-capacity-reservation.html) command to create the Capacity Reservations, three for `us-east-1a` and another three for `us-east-1b`\. Except for the Availability Zone, the other attributes of the Capacity Reservations are identical\.

**3 Capacity Reservations in `us-east-1a`**

```
aws ec2 create-capacity-reservation \
    --availability-zone us-east-1a\
    --instance-type c5.xlarge\
    --instance-platform Linux/UNIX \
    --instance-count 3 \
    --instance-match-criteria targeted
```

Example of resulting Capacity Reservation ID

```
cr-1234567890abcdef1
```

**3 Capacity Reservations in `us-east-1b`**

```
aws ec2 create-capacity-reservation \
    --availability-zone us-east-1b\
    --instance-type c5.xlarge\
    --instance-platform Linux/UNIX \
    --instance-count 3 \
    --instance-match-criteria targeted
```

Example of resulting Capacity Reservation ID

```
cr-54321abcdef567890
```

## Step 2: Create a Capacity Reservation resource group<a name="ec2-fleet-odcr-step2"></a>

Use the `resource-groups` service and the [create\-group](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/create-group.html) command to create a Capacity Reservation resource group\. In this example, the resource group is named `my-cr-group`\. For information about why you must create a resource group, see [Use Capacity Reservations for On\-Demand Instances](ec2-fleet-on-demand-backup.md#ec2-fleet-on-demand-capacity-reservations)\.

```
aws resource-groups create-group \
    --name my-cr-group \
    --configuration '{"Type":"AWS::EC2::CapacityReservationPool"}' '{"Type":"AWS::ResourceGroups::Generic", "Parameters": [{"Name": "allowed-resource-types", "Values": ["AWS::EC2::CapacityReservation"]}]}'
```

## Step 3: Add the Capacity Reservations to the Capacity Reservation resource group<a name="ec2-fleet-odcr-step3"></a>

Use the `resource-groups` service and the [group\-resources](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/group-resources.html) command to add the Capacity Reservations that you created in Step 1 to the Capacity Reservations resource group\. Note that you must reference the On\-Demand Capacity Reservations by their ARNs\.

```
aws resource-groups group-resources \
    --group my-cr-group \
    --resource-arns \
      arn:aws:ec2:us-east-1:123456789012:capacity-reservation/cr-1234567890abcdef1 \
      arn:aws:ec2:us-east-1:123456789012:capacity-reservation/cr-54321abcdef567890
```

Example output

```
{
   "Failed": [], 
   "Succeeded": [ 
   "arn:aws:ec2:us-east-1:123456789012:capacity-reservation/cr-1234567890abcdef1", 
   "arn:aws:ec2:us-east-1:123456789012:capacity-reservation/cr-54321abcdef567890" 
   ] 
}
```

## \(Optional\) Step 4: View the Capacity Reservations in the resource group<a name="ec2-fleet-odcr-step4"></a>

Use the `resource-groups` service and the [list\-group\-resources](https://docs.aws.amazon.com/cli/latest/reference/resource-groups/list-group-resources.html) command to optionally describe the resource group to view its Capacity Reservations\.

```
aws resource-groups list-group-resources --group my-cr-group
```

Example output

```
{
    "ResourceIdentifiers": [
        {
            "ResourceType": "AWS::EC2::CapacityReservation",
            "ResourceArn": "arn:aws:ec2:us-east-1:123456789012:capacity-reservation/cr-1234567890abcdef1"
        },
        {
            "ResourceType": "AWS::EC2::CapacityReservation",
            "ResourceArn": "arn:aws:ec2:us-east-1:123456789012:capacity-reservation/cr-54321abcdef567890"
        }
    ]
}
```

## Step 5: Create a launch template that specifies that the Capacity Reservation targets a specific resource group<a name="ec2-fleet-odcr-step5"></a>

Use the [create\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) command to create a launch template in which to specify the Capacity Reservations to use\. In this example, the fleet will use `targeted` Capacity Reservations, which have been added to a resource group\. Therefore, the launch template data specifies that the Capacity Reservation targets a specific resource group\. In this example, the launch template is named `my-launch-template`\. 

```
aws ec2 create-launch-template \
    --launch-template-name my-launch-template \
    --launch-template-data \
        '{"ImageId": "ami-0123456789example",
          "CapacityReservationSpecification": 
            {"CapacityReservationTarget": 
                { "CapacityReservationResourceGroupArn": "arn:aws:resource-groups:us-east-1:123456789012:group/my-cr-group" }
            }
        }'
```

## \(Optional\) Step 6: Describe the launch template<a name="ec2-fleet-odcr-step6"></a>

Use the [describe\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-launch-template.html) command to optionally describe the launch template to view its configuration\.

```
aws ec2 describe-launch-template-versions --launch-template-name my-launch-template
```

Example output

```
{
    "LaunchTemplateVersions": [
        {
            "LaunchTemplateId": "lt-01234567890example",
            "LaunchTemplateName": "my-launch-template",
            "VersionNumber": 1,
            "CreateTime": "2021-01-19T20:50:19.000Z",
            "CreatedBy": "arn:aws:iam::123456789012:user/Admin",
            "DefaultVersion": true,
            "LaunchTemplateData": {
                "ImageId": "ami-0947d2ba12ee1ff75",
                "CapacityReservationSpecification": {
                    "CapacityReservationTarget": {
                        "CapacityReservationResourceGroupArn": "arn:aws:resource-groups:us-east-1:123456789012:group/my-cr-group"
                    }
                }
            }
        }
    ]
}
```

## Step 7: Create an EC2 Fleet<a name="ec2-fleet-odcr-step7"></a>

Create an EC2 Fleet that specifies the configuration information for the instances that it will launch\. The following EC2 Fleet configuration shows only the pertinent configurations for this example\. The launch template `my-launch-template` is the launch template you created in Step 5\. There are two instance pools, each with the same instance type \(`c5.xlarge`\), but with different Availability Zones \(`us-east-1a` and `us-east-1b`\)\. The price of the instance pools is the same because pricing is defined for the Region, not per Availability Zone\. The total target capacity is 10, and the default target capacity type is `on-demand`\. The On\-Demand allocation strategy is `lowest-price`\. The usage strategy for Capacity Reservations is `use-capacity-reservations-first`\.

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

## \(Optional\) Step 8: View the number of remaining unused Capacity Reservations<a name="ec2-fleet-odcr-step8"></a>

After the fleet is launched, you can optionally run [describe\-capacity\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-capacity-reservations.html) to see how many unused Capacity Reservations are remaining\. In this example, you should see the following response, which shows that all of the Capacity Reservations in all of the pools were used\.

```
{ "CapacityReservationId":  "cr-111",
     "InstanceType":  "c5.xlarge",  
     "AvailableInstanceCount":  0
}

 { "CapacityReservationId":  "cr-222",
     "InstanceType":  "c5.xlarge", 
     "AvailableInstanceCount":  0
}
```
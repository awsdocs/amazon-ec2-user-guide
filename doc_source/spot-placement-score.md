# Spot placement score<a name="spot-placement-score"></a>

The Spot placement score feature can recommend an AWS Region or Availability Zone based on your Spot capacity requirements\. Spot capacity fluctuates, and you can't be sure that you'll always get the capacity that you need\. A Spot placement score indicates how likely it is that a Spot request will succeed in a Region or Availability Zone\.

**Note**  
A Spot placement score does not provide any guarantees in terms of available capacity or risk of interruption\. A Spot placement score serves only as a recommendation\.

**Benefits**

You can use the Spot placement score feature for the following:
+ To relocate and scale Spot compute capacity in a different Region, as needed, in response to increased capacity needs or decreased available capacity in the current Region\.
+ To identify the most optimal Availability Zone in which to run single\-Availability Zone workloads\.
+ To simulate future Spot capacity needs so that you can pick an optimal Region for the expansion of your Spot\-based workloads\.
+ To find an optimal combination of instance types to fulfill your Spot capacity needs\.

**Topics**
+ [Costs](#sps-costs)
+ [How Spot placement score works](#how-sps-works)
+ [Limitations](#sps-limitations)
+ [Required IAM permission](#sps-iam-permission)
+ [Calculate a Spot placement score](#work-with-spot-placement-score)
+ [Example configurations](#sps-example-configs)

## Costs<a name="sps-costs"></a>

There is no additional charge for using the Spot placement score feature\.

## How Spot placement score works<a name="how-sps-works"></a>

When you use the Spot placement score feature, you first specify your compute requirements for your Spot Instances, and then Amazon EC2 returns the top 10 Regions or Availability Zones where your Spot request is likely to succeed\. Each Region or Availability Zone is scored on a scale from 1 to 10, with 10 indicating that your Spot request is highly likely to succeed, and 1 indicating that your Spot request is not likely to succeed\.

**Topics**
+ [Step 1: Specify your Spot requirements](#sps-specify-requirements)
+ [Step 2: Filter the Spot placement score response](#get-sps)
+ [Step 3: Review the recommendations](#sps-recommendations)
+ [Step 4: Use the recommendations](#sps-use-recommendations)

### Step 1: Specify your Spot requirements<a name="sps-specify-requirements"></a>

First, you specify your desired target Spot capacity and your compute requirements, as follows:

1. **Specify the target Spot capacity, and optionally the target capacity unit\.**

   You can specify your desired target Spot capacity in terms of the number of instances or vCPUs, or in terms of the amount of memory in MiB\. To specify the target capacity in number of vCPUs or amount of memory, you must specify the target capacity unit as `vcpu` or `memory-mib`\. Otherwise, it defaults to number of instances\.

   By specifying your target capacity in terms of the number of vCPUs or the amount of memory, you can use these units when counting the total capacity\. For example, if you want to use a mix of instances of different sizes, you can specify the target capacity as a total number of vCPUs\. The Spot placement score feature then considers each instance type in the request by its number of vCPUs, and counts the total number of vCPUs rather than the total number of instances when totaling up the target capacity\.

   For example, say you specify a total target capacity of 30 vCPUs, and your instance type list consists of c5\.xlarge \(4 vCPUs\), m5\.2xlarge \(8 vCPUs\), and r5\.large \(2 vCPUs\)\. To achieve a total of 30 vCPUs, you could get a mix of 2 c5\.xlarge \(2\*4 vCPUs\), 2 m5\.2xlarge \(2\*8 vCPUs\), and 3 r5\.large \(3\*2 vCPUs\)\.

1. **Specify instance types or instance attributes\.**

   You can either specify the instance types to use, or you can specify the instance attributes that you need for your compute requirements, and then let Amazon EC2 identify the instance types that have those attributes\. This is known as attribute\-based instance type selection\.

   You can't specify both instance types and instance attributes in the same Spot placement score request\.

   If you specify instance types, you must specify at least three different instance types, otherwise Amazon EC2 will return a low Spot placement score\. Similarly, if you specify instance attributes, they must resolve to at least three different instance types\.

For examples of different ways to specify your Spot requirements, see [Example configurations](#sps-example-configs)\.

### Step 2: Filter the Spot placement score response<a name="get-sps"></a>

Amazon EC2 calculates the Spot placement score for each Region or Availability Zone, and returns either the top 10 Regions or the top 10 Availability Zones where your Spot request is likely to succeed\. The default is to return a list of scored Regions\. If you plan to launch all of your Spot capacity into a single Availability Zone, then it's useful to request a list of scored Availability Zones\.

You can specify a Region filter to narrow down the Regions that will be returned in the response\.

You can combine the Region filter and a request for scored Availability Zones\. In this way, the scored Availability Zones are confined to the Regions for which you've filtered\. To find the highest\-scored Availability Zone in a Region, specify only that Region, and the response will return a scored list of all of the Availability Zones in that Region\.

### Step 3: Review the recommendations<a name="sps-recommendations"></a>

The Spot placement score for each Region or Availability Zone is calculated based on the target capacity, the composition of the instance types, the historical and current Spot usage trends, and the time of the request\. Because Spot capacity is constantly fluctuating, the same Spot placement score request can yield different scores when calculated at different times\.

Regions and Availability Zones are scored on a scale from 1 to 10\. A score of 10 indicates that your Spot request is highly likely—but not guaranteed—to succeed\. A score of 1 indicates that your Spot request is not likely to succeed at all\. The same score might be returned for different Regions or Availability Zones\.

If low scores are returned, you can edit your compute requirements and recalculate the score\. You can also request Spot placement score recommendations for the same compute requirements at different times of the day\.

### Step 4: Use the recommendations<a name="sps-use-recommendations"></a>

A Spot placement score is only relevant if your Spot request has exactly the same configuration as the Spot placement score configuration \(target capacity, target capacity unit, and instance types or instance attributes\), and is configured to use the `capacity-optimized` allocation strategy\. Otherwise, the likelihood of getting available Spot capacity will not align with the score\.

While a Spot placement score serves as a guideline, and no score guarantees that your Spot request will be fully or partially fulfilled, you can use the following information to get the best results:
+ **Use the same configuration** – The Spot placement score is relevant only if the Spot request configuration \(target capacity, target capacity unit, and instance types or instance attributes\) in your Auto Scaling group, EC2 Fleet, or Spot Fleet is the same as what you entered to get the Spot placement score\.

  If you used attribute\-based instance type selection in your Spot placement score request, you can use attribute\-based instance type selection to configure your Auto Scaling group, EC2 Fleet, or Spot Fleet\. For more information, see [Creating an Auto Scaling group with a set of requirements on the instance types used](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-instance-type-requirements.html), [Attribute\-based instance type selection for EC2 Fleet](ec2-fleet-attribute-based-instance-type-selection.md), and [Attribute\-based instance type selection for Spot Fleet](spot-fleet-attribute-based-instance-type-selection.md)\.
**Note**  
If you specified your target capacity in terms of the number of vCPUs or the amount of memory, and you specified instance types in your Spot placement score configuration, note that you can’t currently create this configuration in your Auto Scaling group, EC2 Fleet, or Spot Fleet\. Instead, you must manually set the instance weighting by using the `WeightedCapacity` parameter\.
+ **Use the `capacity-optimized` allocation strategy** – Any score assumes that your fleet request will be configured to use all Availability Zones \(for requesting capacity across Regions\) or a single Availability Zone \(if requesting capacity in one Availability Zone\) and the `capacity-optimized` Spot allocation strategy for your request for Spot capacity to succeed\. If you use other allocation strategies, such as `lowest-price`, the likelihood of getting available Spot capacity will not align with the score\.
+ **Act on a score immediately** – The Spot placement score recommendation reflects the available Spot capacity at the time of the request, and the same configuration can yield different scores when calculated at different times due to Spot capacity fluctuations\. While a score of 10 means that your Spot capacity request is highly likely—but not guaranteed—to succeed, for best results we recommend that you act on a score immediately\. We also recommend that you get a fresh score each time you attempt a capacity request\.

## Limitations<a name="sps-limitations"></a>
+ **Target capacity limit** – Your Spot placement score target capacity limit is based on your recent Spot usage, while accounting for potential usage growth\. If you have no recent Spot usage, we provide you with a low default limit aligned with your Spot request limit\.
+ **Request configurations limit** – We can limit the number of new request configurations within a 24\-hour period if we detect patterns not associated with the intended use of the Spot placement score feature\. If you reach the limit, you can retry the request configurations that you've already used, but you can't specify new request configurations until the next 24\-hour period\.
+ **Minimum number of instance types** – If you specify instance types, you must specify at least three different instance types, otherwise Amazon EC2 will return a low Spot placement score\. Similarly, if you specify instance attributes, they must resolve to at least three different instance types\. Instance types are considered different if they have a different name\. For example, m5\.8xlarge, m5a\.8xlarge, and m5\.12xlarge are all considered different\.

## Required IAM permission<a name="sps-iam-permission"></a>

By default, IAM identities \(users, roles, or groups\) don't have permission to use the Spot placement score feature\. To allow IAM identities to use the Spot placement score feature, you must create an IAM policy that grants permission to use the `ec2:GetSpotPlacementScores` EC2 API action\. You then attach the policy to the IAM identities that require this permission\.

The following is an example IAM policy that grants permission to use the `ec2:GetSpotPlacementScores` EC2 API action\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:GetSpotPlacementScores",
            "Resource": "*"
        }
    ]
}
```

For information about editing an IAM policy, see [Editing IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html) in the *IAM User Guide*\.

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

## Calculate a Spot placement score<a name="work-with-spot-placement-score"></a>

You can calculate a Spot placement score by using the Amazon EC2 console or the AWS CLI\.

**Topics**
+ [Calculate a Spot placement score by specifying instance attributes \(console\)](#sps-specify-instance-attributes-console)
+ [Calculate a Spot placement score by specifying instance types \(console\)](#sps-specify-instance-types-console)
+ [Calculate the Spot placement score \(AWS CLI\)](#calculate-sps-cli)

### Calculate a Spot placement score by specifying instance attributes \(console\)<a name="sps-specify-instance-attributes-console"></a>

**To calculate a Spot placement score by specifying instance attributes**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. Choose **Spot placement score**\.

1. Choose **Enter requirements**\.

1. For **Target capacity**, enter your desired capacity in terms of the number of **instances** or **vCPUs**, or the amount of **memory \(MiB\)**\.

1. For **Instance type requirements**, to specify your compute requirements and let Amazon EC2 identify the optimal instance types with these requirements, choose **Specify instance attributes that match your compute requirements**\.

1. For **vCPUs**, enter the desired minimum and maximum number of vCPUs\. To specify no limit, select **No minimum**, **No maximum**, or both\.

1. For **Memory \(GiB\)**, enter the desired minimum and maximum amount of memory\. To specify no limit, select **No minimum**, **No maximum**, or both\.

1. For **CPU architecture**, select the required instance architecture\.

1. \(Optional\) For **Additional instance attributes**, you can optionally specify one or more attributes to express your compute requirements in more detail\. Each additional attribute adds a further constraint to your request\. You can omit the additional attributes; when omitted, the default values are used\. For a description of each attribute and their default values, see [get\-spot\-placement\-scores](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-spot-placement-scores.html) in the *Amazon EC2 Command Line Reference*\.

1. \(Optional\) To view the instance types with your specified attributes, expand **Preview matching instance types**\. To exclude instance types from being used in the placement evaluation, select the instances and then choose **Exclude selected instance types**\.

1. Choose **Load placement scores**, and review the results\.

1. \(Optional\) To display the Spot placement score for specific Regions, for **Regions to evaluate**, select the Regions to evaluate, and then choose **Calculate placement scores**\.

1. \(Optional\) To display the Spot placement score for the Availability Zones in the displayed Regions, select the **Provide placement scores per Availability Zone** check box\. A list of scored Availability Zones is useful if you want to launch all of your Spot capacity into a single Availability Zone\.

1. \(Optional\) To edit your compute requirements and get a new placement score, choose **Edit**, make the necessary adjustments, and then choose **Calculate placement scores**\.

### Calculate a Spot placement score by specifying instance types \(console\)<a name="sps-specify-instance-types-console"></a>

**To calculate a Spot placement score by specifying instance types**

1. Open the Spot console at [https://console\.aws\.amazon\.com/ec2spot](https://console.aws.amazon.com/ec2spot)\.

1. Choose **Spot placement score**\.

1. Choose **Enter requirements**\.

1. For **Target capacity**, enter your desired capacity in terms of the number of **instances** or **vCPUs**, or the amount of **memory \(MiB\)**\.

1. For **Instance type requirements**, to specify the instance types to use, choose **Manually select instance types**\.

1. Choose **Select instance types**, select the instance types to use, and then choose **Select**\. To quickly find instance types, you can use the filter bar to filter the instance types by different properties\.

1. Choose **Load placement scores**, and review the results\.

1. \(Optional\) To display the Spot placement score for specific Regions, for **Regions to evaluate**, select the Regions to evaluate, and then choose **Calculate placement scores**\.

1. \(Optional\) To display the Spot placement score for the Availability Zones in the displayed Regions, select the **Provide placement scores per Availability Zone** check box\. A list of scored Availability Zones is useful if you want to launch all of your Spot capacity into a single Availability Zone\.

1. \(Optional\) To edit the list of instance types and get a new placement score, choose **Edit**, make the necessary adjustments, and then choose **Calculate placement scores**\.

### Calculate the Spot placement score \(AWS CLI\)<a name="calculate-sps-cli"></a>

**To calculate the Spot placement score**

1. \(Optional\) To generate all of the possible parameters that can be specified for the Spot placement score configuration, use the [get\-spot\-placement\-scores](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-spot-placement-scores.html) command and the `--generate-cli-skeleton` parameter\.

   ```
   aws ec2 get-spot-placement-scores \
       --region us-east-1 \
       --generate-cli-skeleton
   ```

   Expected output

   ```
   {
       "InstanceTypes": [
           ""
       ],
       "TargetCapacity": 0,
       "TargetCapacityUnitType": "vcpu",
       "SingleAvailabilityZone": true,
       "RegionNames": [
           ""
       ],
       "InstanceRequirementsWithMetadata": {
           "ArchitectureTypes": [
               "x86_64_mac"
           ],
           "VirtualizationTypes": [
               "hvm"
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
               "BurstablePerformance": "excluded",
               "RequireHibernateSupport": true,
               "NetworkInterfaceCount": {
                   "Min": 0,
                   "Max": 0
               },
               "LocalStorage": "included",
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
                   "amd"
               ],
               "AcceleratorNames": [
                   "vu9p"
               ],
               "AcceleratorTotalMemoryMiB": {
                   "Min": 0,
                   "Max": 0
               }
           }
       },
       "DryRun": true,
       "MaxResults": 0,
       "NextToken": ""
   }
   ```

1. Create a JSON configuration file using the output from the previous step, and configure it as follows:

   1. For `TargetCapacity`, enter your desired Spot capacity in terms of the number of instances or vCPUs, or the amount of memory \(MiB\)\.

   1. For `TargetCapacityUnitType`, enter the unit for the target capacity\. If you omit this parameter, it defaults to `units`\.

      Valid values: `units` \(which translates to number of instances\) \| `vcpu` \| `memory-mib`

   1. For `SingleAvailabilityZone`, specify `true` for a response that returns a list of scored Availability Zones\. A list of scored Availability Zones is useful if you want to launch all of your Spot capacity into a single Availability Zone\. If you omit this parameter, it defaults to `false`, and the response returns a list of scored Regions\.

   1. \(Optional\) For `RegionNames`, specify the Regions to use as a filter\. You must specify the Region code, for example, `us-east-1`\.

      With a Region filter, the response returns only the Regions that you specify\. If you specified `true` for `SingleAvailabilityZone`, the response returns only the Availability Zones in the specified Regions\.

   1. You can include either `InstanceTypes` or `InstanceRequirements`, but not both in the same configuration\.

      Specify one of the following in your JSON configuration:
      + To specify a list of instance types, specify the instance types in the `InstanceTypes` parameter\. Specify at least three different instance types\. If you specify only one or two instance types, Spot placement score returns a low score\. For the list of instance types, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\.
      + To specify the instance attributes so that Amazon EC2 will identify the instance types that match those attributes, specify the attributes that are located in the `InstanceRequirements` structure\.

        You must provide values for `VCpuCount`, `MemoryMiB`, and `CpuManufacturers`\. You can omit the other attributes; when omitted, the default values are used\. For a description of each attribute and their default values, see [get\-spot\-placement\-scores](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-spot-placement-scores.html) in the *Amazon EC2 Command Line Reference*\.

      For example configurations, see [Example configurations](#sps-example-configs)\.

1. To get the Spot placement score for the requirements that you specified in the JSON file, use the [get\-spot\-placement\-scores](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-spot-placement-scores.html) command, and specify the name and path to your JSON file by using the `--cli-input-json` parameter\.

   ```
   aws ec2 get-spot-placement-scores \
       --region us-east-1 \
       --cli-input-json file://file_name.json
   ```

   Example output if `SingleAvailabilityZone` is set to `false` or omitted \(if omitted, it defaults to `false`\) – a scored list of Regions is returned

   ```
   "SpotPlacementScores": [
       {
           "Region": "us-east-1",
           "Score": 7
       },
       {
           "Region": "us-west-1",
           "Score": 5
       },  
      ...
   ```

   Example output if `SingleAvailabilityZone` is set to `true` – a scored list of Availability Zones is returned

   ```
   "SpotPlacementScores": [
       {
           "Region": "us-east-1",
           "AvailabilityZoneId": "use1-az1"
           "Score": 8
       },
       {
           "Region": "us-east-1",
           "AvailabilityZoneId": "usw2-az3"
           "Score": 6
       },
      ...
   ```

## Example configurations<a name="sps-example-configs"></a>

When using the AWS CLI, you can use the following example configurations\.

**Topics**
+ [Example: Specify instance types and target capacity](#example-config-instance-type-override)
+ [Example: Specify instance types, and target capacity in terms of memory](#example-config-instance-type-memory-unit-override)
+ [Example: Specify attributes for attribute\-based instance type selection](#example-config-attribute-based-instance-type-selection)
+ [Example: Specify attributes for attribute\-based instance type selection and return a scored list of Availability Zones](#example-config-sps-singleAZ)

### Example: Specify instance types and target capacity<a name="example-config-instance-type-override"></a>

The following example configuration specifies three different instance types and a target Spot capacity of 500 Spot Instances\.

```
{
    "InstanceTypes": [
        "m5.4xlarge",
        "r5.2xlarge",
        "m4.4xlarge"
    ], 
    "TargetCapacity": 500
}
```

### Example: Specify instance types, and target capacity in terms of memory<a name="example-config-instance-type-memory-unit-override"></a>

The following example configuration specifies three different instance types and a target Spot capacity of 500,000 MiB of memory, where the number of Spot Instances to launch must provide a total of 500,000 MiB of memory\.

```
{
    "InstanceTypes": [
        "m5.4xlarge",
        "r5.2xlarge",
        "m4.4xlarge"
    ], 
    "TargetCapacity": 500000,
    "TargetCapacityUnitType": "memory-mib"
}
```

### Example: Specify attributes for attribute\-based instance type selection<a name="example-config-attribute-based-instance-type-selection"></a>

The following example configuration is configured for attribute\-based instance type selection, and is followed by a text explanation of the example configuration\.

```
{
    "TargetCapacity": 5000,
    "TargetCapacityUnitType": "vcpu",
    "InstanceRequirementsWithMetadata": {
        "ArchitectureTypes": ["arm64"],
        "VirtualizationTypes": ["hvm"],
        "InstanceRequirements": {
            "VCpuCount": {
                "Min": 1,
                "Max": 12
            },
            "MemoryMiB": {
                "Min": 512
            }
        }
    }
}
```

****`InstanceRequirementsWithMetadata`****  
To use attribute\-based instance type selection, you must include the `InstanceRequirementsWithMetadata` structure in your configuration, and specify the desired attributes for the Spot Instances\.

In the preceding example, the following required instance attributes are specified:
+ `ArchitectureTypes` – The architecture type of the instance types must be `arm64`\.
+ `VirtualizationTypes` – The virtualization type of the instance types must be `hvm`\.
+ `VCpuCount` – The instance types must have a minimum of 1 and a maximum of 12 vCPUs\.
+ `MemoryMiB` – The instance types must have a minimum of 512 MiB of memory\. By omitting the `Max` parameter, you are indicating that there is no maximum limit\.

Note that there are several other optional attributes that you can specify\. For the list of attributes, see [get\-spot\-placement\-scores](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-spot-placement-scores.html) in the *Amazon EC2 Command Line Reference*\.

**`TargetCapacityUnitType`**  
The `TargetCapacityUnitType` parameter specifies the unit for the target capacity\. In the example, the target capacity is `5000` and the target capacity unit type is `vcpu`, which together specify a desired target capacity of 5000 vCPUs, where the number of Spot Instances to launch must provide a total of 5000 vCPUs\.

### Example: Specify attributes for attribute\-based instance type selection and return a scored list of Availability Zones<a name="example-config-sps-singleAZ"></a>

The following example configuration is configured for attribute\-based instance type selection\. By specifying `"SingleAvailabilityZone": true`, the response will return a list of scored Availability Zones\.

```
{
    "TargetCapacity": 1000,
    "TargetCapacityUnitType": "vcpu",
    "SingleAvailabilityZone": true,
    "InstanceRequirementsWithMetadata": {
        "ArchitectureTypes": ["arm64"],
        "VirtualizationTypes": ["hvm"],
        "InstanceRequirements": {
            "VCpuCount": {
                "Min": 1,
                "Max": 12
            },
            "MemoryMiB": {
                "Min": 512
            }
        }
    }
}
```
# Finding an instance type<a name="instance-discovery"></a>

 Before you can launch an instance, you must select an instance type to use\. The instance type that you choose can differ depending on your requirements for the instances that you'll launch\. For example, you might want to consider the following requirements:
+ Region
+ The architecture: 32\-bit \(i386\), 64\-bit \(x86\_64\), or 64\-bit ARM \(arm64\)
+ Compute
+ Memory
+ Storage
+ Network performance

## Finding an instance type using the Amazon EC2 console<a name="instance-discovery-console"></a>

You can find an instance type using the Amazon EC2 console\. You can search through all available instance types using the **Instance Types** page\.

1. Open the [Amazon EC2 console](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. In the navigation pane, choose **Instance Types**\. 

1. \(Optional\) Choose the preferences icon to select which instance type attributes to display, such as the On\-Demand Linux pricing\. Alternatively, you can select an instance type from the list and view all attributes in the **Details** pane\.

1. \(Optional\) Use the **Filter** options to scope the list of displayed instance types to see only the instance types that interest you\. For example, you can list all of the instance types that have more than eight vCPUs and that support hibernation\.

1. \(Optional\) Select multiple instance types to see a side\-by\-side comparison across all attributes in the **Details** pane\.

1. \(Optional\) To save the list of instance types for further review, choose **Download list \(CSV\)**\. A comma\-separated values \(\.csv\) format file will download\. The file includes all instance types that match the filters you set, if any, and that match all attributes displayed in the table\.

## Finding an instance type using the AWS CLI<a name="instance-discovery-cli"></a>

 You can use AWS CLI commands for Amazon EC2 to list only the instance types that meet your needs\. After locating an instance type that meets your needs, make note of it so that you can use it to launch instances\. For more information, see [Launching an Instance Using the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.

 The [describe\-instance\-types](https://docs.aws.amazon.com/goto/aws-cli/ec2-2016-11-15/DescribeInstanceTypes) command supports filtering parameters\. For example, you can use the following filter to display only instance types with 48 vCPUs\.

```
aws ec2 describe-instance-types --filters "Name=vcpu-info.default-vcpus,Values=48"
```

 The [describe\-instance\-type\-offerings](https://docs.aws.amazon.com/goto/aws-cli/ec2-2016-11-15/DescribeInstanceTypeOfferings) command supports filtering parameters\. For example, you can use the `--location-type` parameter to display Availability Zone offerings\. 

```
aws ec2 describe-instance-type-offerings --location-type "availability-zone"
```

 You can add the following filter to the previous command to display only instance types that are offered in the `us-east-1a` Availability Zone\.

```
--filters "Name=location,Values=us-east-1a"
```
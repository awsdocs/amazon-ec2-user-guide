# Find an Amazon EC2 instance type<a name="instance-discovery"></a>

Before you can launch an instance, you must select an instance type to use\. The instance type that you choose might depend on your requirements for the instances that you'll launch\. For example, you might choose an instance type based on the following requirements:
+ Availability Zone or Region
+ Compute
+ Memory
+ Networking
+ Pricing
+ Storage

## Find an instance type using the console<a name="instance-discovery-console"></a>

You can find an instance type that meets your needs using the Amazon EC2 console\.

**To find an instance type using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. In the navigation pane, choose **Instance Types**\. 

1. \(Optional\) Choose the preferences \(gear\) icon to select which instance type attributes to display, such as **On\-Demand Linux pricing**, and then choose **Confirm**\. Alternatively, select an instance type and view all attributes using the **Details** pane\.

1. Use the instance type attributes to filter the list of displayed instance types to only the instance types that meet your needs\. For example, you can list all instance types that have more than eight vCPUs and also support hibernation\.

1. \(Optional\) Select multiple instance types to see a side\-by\-side comparison across all attributes in the **Details** pane\.

1. \(Optional\) To save the list of instance types to a comma\-separated values \(\.csv\) file for further review, choose **Download list CSV**\. The file includes all instance types that match the filters you set\.

1. After locating instance types that meet your needs, you can use them to launch instances\. For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\.

## Find an instance type using the AWS CLI<a name="instance-discovery-cli"></a>

You can use AWS CLI commands for Amazon EC2 to find an instance type that meet your needs\.

**To find an instance type using the AWS CLI**

1. If you have not done so already, install the AWS CLI For more information, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

1. Use the [describe\-instance\-types](https://docs.aws.amazon.com/goto/aws-cli/ec2-2016-11-15/DescribeInstanceTypes) command to filter instance types based on instance attributes\. For example, you can use the following command to display only instance types with 48 vCPUs\.

   ```
   aws ec2 describe-instance-types --filters "Name=vcpu-info.default-vcpus,Values=48"
   ```

1. Use the [describe\-instance\-type\-offerings](https://docs.aws.amazon.com/goto/aws-cli/ec2-2016-11-15/DescribeInstanceTypeOfferings) command to filter instance types offered by location \(Region or Availability Zone\)\. For example, you can use the following command to display the instance types offered in the specified Availability Zone\. 

   ```
   aws ec2 describe-instance-type-offerings --location-type "availability-zone" --filters Name=location,Values=us-east-2a --region us-east-2
   ```

1. After locating instance types that meet your needs, make note of them so that you can use these instance types when you launch instances\. For more information, see [Launching your instance](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.
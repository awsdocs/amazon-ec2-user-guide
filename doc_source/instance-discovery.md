# Find an Amazon EC2 instance type<a name="instance-discovery"></a>

Before you can launch an instance, you must select an instance type to use\. The instance type that you choose might depend on the resources that your workload requires, such as compute, memory, or storage resources\. It can be beneficial to identify several instance types that might suit your workload and evaluate their performance in a test environment\. There is no substitute for measuring the performance of your application under load\.

If you already have running EC2 instances, you can use AWS Compute Optimizer to get recommendations about the instance types that you should use to improve performance, save money, or both\. For more information, see [Get recommendations for an instance type](ec2-instance-recommendations.md)\.

**Topics**
+ [Find an instance type using the console](#instance-discovery-console)
+ [Find an instance type using the AWS CLI](#instance-discovery-cli)

## Find an instance type using the console<a name="instance-discovery-console"></a>

You can find an instance type that meets your needs using the Amazon EC2 console\.

**To find an instance type using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. In the navigation pane, choose **Instance Types**\. 

1. \(Optional\) Choose the preferences \(gear\) icon to select which instance type attributes to display, such as **On\-Demand Linux pricing**, and then choose **Confirm**\. Alternatively, select the name of an instance type to open its details page and view all attributes available through the console\. The console does not display all the attributes available through the API or the command line\.

1. Use the instance type attributes to filter the list of displayed instance types to only the instance types that meet your needs\. For example, you can filter on the following attributes:
   + **Availability zones** – The name of the Availability Zone, Local Zone, or Wavelength Zone\. For more information, see [Regions and Zones](using-regions-availability-zones.md)\.
   + **vCPUs** or **Cores** – The number of vCPUs or cores\.
   + **Memory \(GiB\)** – The memory size, in GiB\.
   + **Network performance** – The network performance, in Gigabits\.
   + **Local instance storage** – Indicates whether the instance type has local instance storage \(`true` \| `false`\)\.

1. \(Optional\) To see a side\-by\-side comparison, select the checkbox for multiple instance types\. The comparison is displayed at the bottom of the screen\.

1. \(Optional\) To save the list of instance types to a comma\-separated values \(\.csv\) file for further review, choose **Actions**, **Download list CSV**\. The file includes all instance types that match the filters you set\.

1. \(Optional\) To launch instances using an instance type that meet your needs, select the checkbox for the instance type and choose **Actions**, **Launch instance**\. For more information, see [Launch an instance using the old launch instance wizard](launching-instance.md)\.

## Find an instance type using the AWS CLI<a name="instance-discovery-cli"></a>

You can use AWS CLI commands for Amazon EC2 to find an instance type that meet your needs\.

**To find an instance type using the AWS CLI**

1. If you have not done so already, install the AWS CLI For more information, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\.

1. Use the [describe\-instance\-types](https://docs.aws.amazon.com/goto/aws-cli/ec2-2016-11-15/DescribeInstanceTypes) command to filter instance types based on instance attributes\. For example, you can use the following command to display only current generation instance types with 64 GiB \(65536 MiB\) of memory\.

   ```
   aws ec2 describe-instance-types --filters "Name=current-generation,Values=true" "Name=memory-info.size-in-mib,Values=65536" --query "InstanceTypes[*].[InstanceType]" --output text | sort
   ```

1. Use the [describe\-instance\-type\-offerings](https://docs.aws.amazon.com/goto/aws-cli/ec2-2016-11-15/DescribeInstanceTypeOfferings) command to filter instance types offered by location \(Region or Zone\)\. For example, you can use the following command to display the instance types offered in the specified Zone\. 

   ```
   aws ec2 describe-instance-type-offerings --location-type "availability-zone" --filters Name=location,Values=us-east-2a --region us-east-2 --query "InstanceTypeOfferings[*].[InstanceType]" --output text | sort
   ```

1. After locating the instance types that meet your needs, save the list so that you can use these instance types when you launch instances\. For more information, see [Launching your instance](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.
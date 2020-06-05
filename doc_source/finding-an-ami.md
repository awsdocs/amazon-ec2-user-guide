# Finding a Linux AMI<a name="finding-an-ami"></a>

Before you can launch an instance, you must select an AMI to use\. As you select an AMI, consider the following requirements you might have for the instances that you'll launch:
+ The Region
+ The operating system
+ The architecture: 32\-bit \(`i386`\), 64\-bit \(`x86_64`\), or 64\-bit ARM \(arm64\)
+ The root device type: Amazon EBS or instance store
+ The provider \(for example, Amazon Web Services\)
+ Additional software \(for example, SQL server\)

If you need to find a Windows AMI, see [Finding a Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/finding-an-ami.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Finding a Linux AMI using the Amazon EC2 console](#finding-an-ami-console)
+ [Finding an AMI using the AWS CLI](#finding-an-ami-aws-cli)
+ [Finding the latest Amazon Linux AMI using Systems Manager](#finding-an-ami-parameter-store)
+ [Using a Systems Manager parameter to find an AMI](#using-systems-manager-parameter-to-find-AMI)
+ [Finding a Quick Start AMI](#finding-quick-start-ami)

## Finding a Linux AMI using the Amazon EC2 console<a name="finding-an-ami-console"></a>

You can find Linux AMIs using the Amazon EC2 console\. You can select from the list of AMIs when you use the launch wizard to launch an instance, or you can search through all available AMIs using the **Images** page\. AMI IDs are unique to each AWS Region\.

**To find a Linux AMI using the launch wizard**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch instance**\.

1. On the **Quick Start** tab, select from one of the commonly used AMIs in the list\. If you don't see the AMI that you need, select the **My AMIs**, **AWS Marketplace**, or **Community AMIs** tab to find additional AMIs\. For more information, see [Step 1: Choose an Amazon Machine Image \(AMI\)](launching-instance.md#step-1-AMI)\.

**To find a Linux AMI using the Images page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. In the navigation pane, choose **AMIs**\.

1. \(Optional\) Use the **Filter** options to scope the list of displayed AMIs to see only the AMIs that interest you\. For example, to list all Linux AMIs provided by AWS, select **Public images**\. Choose the Search bar and select **Owner** from the menu, then select **Amazon images**\. Choose the Search bar again to select **Platform** and then the operating system from the list provided\.

1. \(Optional\) Choose the **Show/Hide Columns** icon to select which image attributes to display, such as the root device type\. Alternatively, you can select an AMI from the list and view its properties in the **Details** tab\.

1. Before you select an AMI, it's important that you check whether it's backed by instance store or by Amazon EBS and that you are aware of the effects of this difference\. For more information, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.

1. To launch an instance from this AMI, select it and then choose **Launch**\. For more information about launching an instance using the console, see [Launching your instance from an AMI](launching-instance.md#choose-an-instance-type-page)\. If you're not ready to launch the instance now, make note of the AMI ID for later\.

## Finding an AMI using the AWS CLI<a name="finding-an-ami-aws-cli"></a>

You can use AWS CLI commands for Amazon EC2 to list only the Linux AMIs that meet your needs\. After locating an AMI that meets your needs, make note of its ID so that you can use it to launch instances\. For more information, see [Launching an Instance Using the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.

The [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command supports filtering parameters\. For example, use the `--owners` parameter to display public AMIs owned by Amazon\.

```
aws ec2 describe-images --owners self amazon
```

You can add the following filter to the previous command to display only AMIs backed by Amazon EBS\.

```
--filters "Name=root-device-type,Values=ebs"
```

**Important**  
Omitting the `--owners` flag from the describe\-images command will return all images for which you have launch permissions, regardless of ownership\.

## Finding the latest Amazon Linux AMI using Systems Manager<a name="finding-an-ami-parameter-store"></a>

Amazon EC2 provides AWS Systems Manager public parameters for AWS\-maintained public AMIs that you can use when launching instances\. For example, the EC2\-provided parameter `/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2` is available in all Regions and always points to the latest version of the Amazon Linux 2 AMI in a given Region\. 

The Amazon EC2 AMI public parameters are available from the following paths:
+ `/aws/service/ami-amazon-linux-latest`
+ `/aws/service/ami-windows-latest`

You can view a list of all Linux AMIs in the current AWS Region by using the following command in the AWS CLI\.

```
aws ssm get-parameters-by-path --path /aws/service/ami-amazon-linux-latest --query Parameters[].Name
```

**To launch an instance using a public parameter**  
The following example uses the EC2\-provided public parameter to launch an `m5.xlarge` instance using the latest Amazon Linux 2 AMI\.

To specify the parameter in the command, use the following syntax: `resolve:ssm:public-parameter`, where `resolve:ssm` is the standard prefix and `public-parameter` is the path and name of the public parameter\.

In this example, the `--count` and `--security-group` parameters are not included\. For `--count`, the default is 1\. If you have a default VPC and a default security group, they are used\.

```
aws ec2 run-instances \
    --image-id resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 \
    --instance-type m5.xlarge \
    --key-name MyKeyPair
```

For more information, see [Using public parameters](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-public-parameters.html) in the *AWS Systems Manager User Guide* and [Query for the latest Amazon Linux AMI IDs Using AWS Systems Manager Parameter Store](http://aws.amazon.com/blogs/compute/query-for-the-latest-amazon-linux-ami-ids-using-aws-systems-manager-parameter-store/)\.

## Using a Systems Manager parameter to find an AMI<a name="using-systems-manager-parameter-to-find-AMI"></a>

When you launch an instance using the EC2 launch wizard in the console, you can either select an AMI from the list, or you can select an AWS Systems Manager parameter that points to an AMI ID\. If you use automation code to launch your instances, you can specify the Systems Manager parameter instead of the AMI ID\.

A Systems Manager parameter is a customer\-defined key\-value pair that you can create in Systems Manager Parameter Store\. The Parameter Store provides a central store to externalize your application configuration values\. For more information, see [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) in the *AWS Systems Manager User Guide*\.

When you create a parameter that points to an AMI ID, make sure that you specify the data type as `aws:ec2:image`\. This data type ensures that when the parameter is created or modified, the parameter value is validated as an AMI ID\. For more information, see [Native parameter support for Amazon Machine Image IDs](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-ec2-aliases.html) in the *AWS Systems Manager User Guide*\.

**Topics**
+ [Use cases](#systems-manager-parameter-use-case)
+ [Lauching an instance using a Systems Manager parameter](#systems-manager-parameter-launch-instance)
+ [Permissions](#systems-manager-permissions)
+ [Limitations](#AMI-systems-manager-parameter-limitations)

### Use cases<a name="systems-manager-parameter-use-case"></a>

By using Systems Manager parameters to point to AMI IDs, you can make it easier for your users to select the correct AMI when launching instances, and you can simplify the maintenance of automation code\.

**Easier for users**

If you require instances to be launched using a specific AMI, and if that AMI is updated regularly, we recommend that you require your users to select a Systems Manager parameter to find the AMI\. By requiring your users to select a Systems Manager parameter, you can ensure that the latest AMI is used to launch instances\.

For example, every month in your organization you might create a new version of your AMI that has the latest operating system and application patches\. You also require your users to launch instances using the latest version of your AMI\. To ensure that your users use the latest version, you can create a Systems Manager parameter \(for example, `golden-ami`\) that points to the correct AMI ID\. Each time a new version of the AMI is created, you update the AMI ID value in the parameter so that it always points to the latest AMI\. Your users don't need to know about the periodic updates to the AMI, because they continue to select the same Systems Manager parameter every time\. By having users select a Systems Manager parameter, you make it easier for them to select the correct AMI for an instance launch\.

**Simplify automation code maintenance**

If you use automation code to launch your instances, you can specify the Systems Manager parameter instead of the AMI ID\. If a new version of the AMI is created, you change the AMI ID value in the parameter so that it points to the latest AMI\. The automation code that references the parameter doesn’t need to be modified every time a new version of the AMI is created\. This greatly simplifies maintenance of automation and helps drive down deployment costs\.

**Note**  
Running instances are not affected when you change the AMI ID to which the Systems Manager parameter points\.

### Lauching an instance using a Systems Manager parameter<a name="systems-manager-parameter-launch-instance"></a>

You can launch an instance using the the console or the AWS CLI\. Instead of specifying an AMI ID, you can specify an AWS Systems Manager parameter that points to an AMI ID\.

**To find a Linux AMI using a Systems Manager parameter \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch instance**\.

1. Choose **Search by Systems Manager parameter** \(at top right\)\.

1. For **Systems Manager parameter**, select a parameter\. The corresponding AMI ID appears next to **Currently resolves to**\.

1. Choose **Search**\. The AMIs that match the AMI ID appear in the list\.

1. Select the AMI from the list, and choose **Select**\.

For more information about launching an instance from an AMI using the launch wizard, see [Step 1: Choose an Amazon Machine Image \(AMI\)](launching-instance.md#step-1-AMI)\.

**To launch an instance using an AWS Systems Manager parameter instead of an AMI ID \(AWS CLI\)**  
The following example uses the Systems Manager parameter `golden-ami` to launch an `m5.xlarge` instance\. The parameter points to an AMI ID\.

To specify the parameter in the command, use the following syntax: `resolve:ssm:/parameter-name`, where `resolve:ssm` is the standard prefix and `parameter-name` is the unique parameter name\. Note that the parameter name is case\-sensitive\. Backslashes for the parameter name are only necessary when the parameter is part of a hierarchy, for example, `/amis/production/golden-ami`\. You can omit the backslash if the parameter is not part of a hierarchy\.

In this example, the `--count` and `--security-group` parameters are not included\. For `--count`, the default is 1\. If you have a default VPC and a default security group, they are used\.

```
aws ec2 run-instances 
    --image-id resolve:ssm:/golden-ami 
    --instance-type m5.xlarge 
    ...
```

**To launch an instance using a specific version of an AWS Systems Manager parameter \(AWS CLI\)**  
Systems Manager parameters have version support\. Each iteration of a parameter is assigned a unique version number\. You can reference the version of the parameter as follows `resolve:ssm:parameter-name:version`, where `version` is the unique version number\. By default, the latest version of the parameter is used when no version is specified\.

The following example uses version 2 of the parameter\.

In this example, the `--count` and `--security-group` parameters are not included\. For `--count`, the default is 1\. If you have a default VPC and a default security group, they are used\.

```
aws ec2 run-instances 
    --image-id resolve:ssm:/golden-ami:2 
    --instance-type m5.xlarge 
    ...
```

**To launch an instance using a public parameter provided by AWS**  
Amazon EC2 provides Systems Manager public parameters for public AMIs provided by AWS\. For example, the public parameter /aws/service/ami\-amazon\-linux\-latest/amzn2\-ami\-hvm\-x86\_64\-gp2 is available in all Regions and always points to the latest version of the Amazon Linux 2 AMI in the Region\.

```
aws ec2 run-instances 
    --image-id resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 
    --instance-type m5.xlarge 
    ...
```

### Permissions<a name="systems-manager-permissions"></a>

If you use Systems Manager parameters that point to AMI IDs in the launch instance wizard, you must add `ssm:DescribeParameters` and `ssm:GetParameters` to your IAM policy\. `ssm:DescribeParameters` grants your IAM users the permission to view and select Systems Manager parameters\. `ssm:GetParameters` grants your IAM users the permission to get the values of the Systems Manager parameters\. You can also restrict access to specific Systems Manager parameters\. For more information, see [Using the EC2 launch wizard](iam-policies-ec2-console.md#ex-launch-wizard)\.

### Limitations<a name="AMI-systems-manager-parameter-limitations"></a>

AMIs and Systems Manager parameters are Region specific\. To use the same Systems Manager parameter name across Regions, create a Systems Manager parameter in each Region with the same name \(for example, `golden-ami`\)\. In each Region, point the Systems Manager parameter to an AMI in that Region\.

## Finding a Quick Start AMI<a name="finding-quick-start-ami"></a>

When you launch an instance using the Amazon EC2 console, the **Choose an Amazon Machine Image \(AMI\)** page includes a list of popular AMIs on the **Quick Start** tab\. If you want to automate launching an instance using one of these quick start AMIs, you'll need to programatically locate the ID of the current version of the AMI\.

To locate the current version of a Quick Start AMI, you can enumerate all AMIs with its AMI name, and then find the one with the most recent creation date\.

**Example Example: Find the current Amazon Linux 2 AMI**  

```
aws ec2 describe-images \
    --owners amazon \
    --filters 'Name=name,Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2' 'Name=state,Values=available' \
    --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId' \
    --output text
```

**Example Example: Find the current Amazon Linux AMI**  

```
aws ec2 describe-images \
    --owners amazon \
    --filters 'Name=name,Values=amzn-ami-hvm-????.??.?.????????-x86_64-gp2' 'Name=state,Values=available' \
    --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId' \
    --output text
```

**Example Example: Find the current Ubuntu Server 16\.04 LTS AMI**  

```
aws ec2 describe-images \
    --owners 099720109477 \
    --filters 'Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-????????' 'Name=state,Values=available' \
    --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId' \
    --output text
```

**Example Example: Find the current Red Hat Enterprise Linux 7\.5 AMI**  

```
aws ec2 describe-images \
    --owners 309956199498 \
    --filters 'Name=name,Values=RHEL-7.5_HVM_GA*' 'Name=state,Values=available' \
    --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId' \
    --output text
```

**Example Example: Find the current SUSE Linux Enterprise Server 15 AMI**  

```
aws ec2 describe-images \
    --owners amazon \
    --filters 'Name=name,Values=suse-sles-15-v????????-hvm-ssd-x86_64' 'Name=state,Values=available' \
    --query 'reverse(sort_by(Images, &CreationDate))[:1].ImageId' \
    --output text
```
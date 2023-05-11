# Find a Linux AMI<a name="finding-an-ami"></a>

Before you can launch an instance, you must select an AMI from which to launch the instance\. When you select an AMI, consider the following requirements you might have for the instances that you want to launch:
+ The Region
+ The operating system
+ The architecture: 32\-bit \(`i386`\), 64\-bit \(`x86_64`\), or 64\-bit ARM \(`arm64`\)
+ The root device type: Amazon EBS or instance store
+ The provider \(for example, Amazon Web Services\)
+ Additional software \(for example, SQL Server\)

If you want to find a Windows AMI, see [Find a Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/finding-an-ami.html) in the *Amazon EC2 User Guide for Windows Instances*\.

If you want to find an Ubuntu AMI, see their [EC2 AMI Locator](https://cloud-images.ubuntu.com/locator/ec2/)\.

If you want to find a RedHat AMI, see the RHEL [knowledgebase article](https://access.redhat.com/solutions/15356)\.

**Find a Linux AMI topics**
+ [Find a Linux AMI using the Amazon EC2 console](#finding-an-ami-console)
+ [Find an AMI using the AWS CLI](#finding-an-ami-aws-cli)
+ [Find the latest Amazon Linux AMI using Systems Manager](#finding-an-ami-parameter-store)
+ [Use a Systems Manager parameter to find an AMI](#using-systems-manager-parameter-to-find-AMI)

## Find a Linux AMI using the Amazon EC2 console<a name="finding-an-ami-console"></a>

You can find Linux AMIs using the Amazon EC2 console\. You can select from the list of AMIs when you use the launch instance wizard to launch an instance, or you can search through all available AMIs using the **Images** page\. AMI IDs are unique to each AWS Region\.

**To find a Linux AMI using the launch instance wizard**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch instance**\.

1. \(New console\) Under **Application and OS Images \(Amazon Machine Image\)**, choose **Quick Start**, choose the operating system \(OS\) for your instance, and then, from **Amazon Machine Image \(AMI\)**, select from one of the commonly used AMIs in the list\. If you don't see the AMI that you want to use, choose **Browse more AMIs** to browse the full AMI catalog\. For more information, see [Application and OS Images \(Amazon Machine Image\)](ec2-launch-instance-wizard.md#liw-ami)\.

   \(Old console\) On the **Quick Start** tab, select from one of the commonly used AMIs in the list\. If you don't see the AMI that you want to use, choose the **My AMIs**, **AWS Marketplace**, or **Community AMIs** tab to find additional AMIs\. For more information, see [Step 1: Choose an Amazon Machine Image \(AMI\)](launching-instance.md#step-1-AMI)\.

**To find a Linux AMI using the AMIs page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. In the navigation pane, choose **AMIs**\.

1. \(Optional\) Use the filter and search options to scope the list of displayed AMIs to see only the AMIs that match your criteria\. For example, to list all Linux AMIs provided by AWS, choose **Public images**\. Then use the search options to further scope the list of displayed AMIs\.

   \(New console\) Choose the **Search** bar and, from the menu, choose **Owner alias**, then the **=** operator, and then the value **amazon**\. Choose the **Search** bar again to choose **Platform**, then the **=** operator, and then the operating system from the list provided\.

   \(Old console\) Choose the **Search** bar and, from the menu, choose **Owner** and then the value **Amazon images**\. Choose the **Search** bar again to choose **Platform** and then the operating system from the list provided\.

1. \(Optional\) Choose the **Preferences** icon \(new console\) or **Show/Hide Columns** icon \(old console\) to select which image attributes to display, such as the root device type\. Alternatively, you can select an AMI from the list and view its properties on the **Details** tab\.

1. Before you select an AMI, it's important that you check whether it's backed by instance store or by Amazon EBS and that you are aware of the effects of this difference\. For more information, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.

1. To launch an instance from this AMI, select it and then choose **Launch instance from image** \(new console\) or **Launch** \(old console\)\. For more information about launching an instance using the console, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\. If you're not ready to launch the instance now, make note of the AMI ID for later\.

## Find an AMI using the AWS CLI<a name="finding-an-ami-aws-cli"></a>

You can use AWS CLI commands for Amazon EC2 to list only the Linux AMIs that match your requirements\. After locating an AMI that matches your requirements, make note of its ID so that you can use it to launch instances\. For more information, see [Launch your instance](https://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.

The [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command supports filtering parameters\. For example, use the `--owners` parameter to display public AMIs owned by Amazon\.

```
aws ec2 describe-images --owners self amazon
```

You can add the following filter to the previous command to display only AMIs backed by Amazon EBS\.

```
--filters "Name=root-device-type,Values=ebs"
```

**Important**  
Omitting the `--owners` flag from the describe\-images command returns all images for which you have launch permissions, regardless of ownership\.

## Find the latest Amazon Linux AMI using Systems Manager<a name="finding-an-ami-parameter-store"></a>

Amazon EC2 provides AWS Systems Manager public parameters for public AMIs maintained by AWS that you can use when launching instances\. For example, the EC2\-provided parameter `/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2` is available in all Regions and always points to the latest version of the Amazon Linux 2 AMI in a given Region\.

The Amazon EC2 AMI public parameters are available from the following path:

 `/aws/service/ami-amazon-linux-latest`

You can view a list of all Linux AMIs in the current AWS Region by running the following AWS CLI command\.

```
aws ssm get-parameters-by-path --path /aws/service/ami-amazon-linux-latest --query "Parameters[].Name"
```

**To launch an instance using a public parameter**  
The following example uses the EC2\-provided public parameter to launch an `m5.xlarge` instance using the latest Amazon Linux 2 AMI\.

To specify the parameter in the command, use the following syntax: `resolve:ssm:public-parameter`, where `resolve:ssm` is the standard prefix and `public-parameter` is the path and name of the public parameter\.

In this example, the `--count` and `--security-group` parameters are not included\. For `--count`, the default is 1\. If you have a default VPC and a default security group, they are used\.

```
aws ec2 run-instances 
    --image-id resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 
    --instance-type m5.xlarge 
    --key-name MyKeyPair
```

For more information, see [Using public parameters](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-public-parameters.html) in the *AWS Systems Manager User Guide* and [Query for the latest Amazon Linux AMI IDs Using AWS Systems Manager Parameter Store](http://aws.amazon.com/blogs/compute/query-for-the-latest-amazon-linux-ami-ids-using-aws-systems-manager-parameter-store/)\.

## Use a Systems Manager parameter to find an AMI<a name="using-systems-manager-parameter-to-find-AMI"></a>

When you launch an instance using the EC2 launch instance wizard in the console, you can either select an AMI from the list, or you can select an AWS Systems Manager parameter that points to an AMI ID\. If you use automation code to launch your instances, you can specify the Systems Manager parameter instead of the AMI ID\.

A Systems Manager parameter is a customer\-defined key\-value pair that you can create in Systems Manager Parameter Store\. The Parameter Store provides a central store to externalize your application configuration values\. For more information, see [AWS Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) in the *AWS Systems Manager User Guide*\.

When you create a parameter that points to an AMI ID, make sure that you specify the data type as `aws:ec2:image`\. Specifying this data type ensures that when the parameter is created or modified, the parameter value is validated as an AMI ID\. For more information, see [Native parameter support for Amazon Machine Image IDs](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-ec2-aliases.html) in the *AWS Systems Manager User Guide*\.

**Topics**
+ [Use cases](#systems-manager-parameter-use-case)
+ [Permissions](#systems-manager-permissions)
+ [Limitations](#AMI-systems-manager-parameter-limitations)
+ [Launch an instance using a Systems Manager parameter](#systems-manager-parameter-launch-instance)

### Use cases<a name="systems-manager-parameter-use-case"></a>

When you use Systems Manager parameters to point to AMI IDs, it is easier for your users to select the correct AMI when launching instances\. Systems Manager parameters can also simplify the maintenance of automation code\.

**Easier for users**

If you require instances to be launched using a specific AMI, and the AMI is regularly updated, we recommend that you require your users to select a Systems Manager parameter to find the AMI\. Requiring your users to select a Systems Manager parameter ensures that the latest AMI is used to launch instances\.

For example, every month in your organization you might create a new version of your AMI that has the latest operating system and application patches\. You also require your users to launch instances using the latest version of your AMI\. To ensure that your users use the latest version, you can create a Systems Manager parameter \(for example, `golden-ami`\) that points to the correct AMI ID\. Each time a new version of the AMI is created, you update the AMI ID value in the parameter so that it always points to the latest AMI\. Your users don't have to know about the periodic updates to the AMI because they continue to select the same Systems Manager parameter each time\. Using a Systems Manager parameter for your AMI makes it easier for them to select the correct AMI for an instance launch\.

**Simplify automation code maintenance**

If you use automation code to launch your instances, you can specify the Systems Manager parameter instead of the AMI ID\. If a new version of the AMI is created, you can change the AMI ID value in the parameter so that it points to the latest AMI\. The automation code that references the parameter doesn’t have to be modified each time a new version of the AMI is created\. This simplifies the maintenance of the automation and helps to drive down deployment costs\.

**Note**  
Running instances are not affected when you change the AMI ID pointed to by the Systems Manager parameter\.

### Permissions<a name="systems-manager-permissions"></a>

If you use Systems Manager parameters that point to AMI IDs in the launch instance wizard, you must add `ssm:DescribeParameters` and `ssm:GetParameters` to your IAM policy\. `ssm:DescribeParameters` grants your users permission to view and select Systems Manager parameters\. `ssm:GetParameters` grants your users permission to retrieve the values of the Systems Manager parameters\. You can also restrict access to specific Systems Manager parameters\. For more information, see [Use the EC2 launch instance wizard](iam-policies-ec2-console.md#ex-launch-wizard)\.

### Limitations<a name="AMI-systems-manager-parameter-limitations"></a>

AMIs and Systems Manager parameters are Region specific\. To use the same Systems Manager parameter name across Regions, create a Systems Manager parameter in each Region with the same name \(for example, `golden-ami`\)\. In each Region, point the Systems Manager parameter to an AMI in that Region\.

### Launch an instance using a Systems Manager parameter<a name="systems-manager-parameter-launch-instance"></a>

You can launch an instance using the console or the AWS CLI\. Instead of specifying an AMI ID, you can specify an AWS Systems Manager parameter that points to an AMI ID\.

------
#### [ New console ]

**To find a Linux AMI using a Systems Manager parameter \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch instance**\.

1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Browse more AMIs**\.

1. Choose the arrow button to the right of the search bar, and then choose **Search by Systems Manager parameter**\.

1. For **Systems Manager parameter**, select a parameter\. The corresponding AMI ID appears below **Currently resolves to**\.

1. Choose **Search**\. The AMIs that match the AMI ID appear in the list\.

1. Select the AMI from the list, and choose **Select**\.

For more information about launching an instance using the launch instance wizard, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\.

------
#### [ Old console ]

**To find a Linux AMI using a Systems Manager parameter \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which to launch your instances\. You can select any Region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch instance**\.

1. Choose **Search by Systems Manager parameter** \(at top right\)\.

1. For **Systems Manager parameter**, select a parameter\. The corresponding AMI ID appears next to **Currently resolves to**\.

1. Choose **Search**\. The AMIs that match the AMI ID appear in the list\.

1. Select the AMI from the list, and choose **Select**\.

For more information about launching an instance from an AMI using the launch instance wizard, see [Step 1: Choose an Amazon Machine Image \(AMI\)](launching-instance.md#step-1-AMI)\.

------

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

In this example, the `--count` and `--security-group` parameters are not included\. For `--count`, the default is `1` If you have a default VPC and a default security group, they are used\.

```
aws ec2 run-instances 
    --image-id resolve:ssm:/golden-ami:2 
    --instance-type m5.xlarge 
    ...
```

**To launch an instance using a public parameter provided by AWS**  
Amazon EC2 provides Systems Manager public parameters for public AMIs provided by AWS\. For example, the public parameter /aws/service/ami\-amazon\-linux\-latest/amzn2\-ami\-hvm\-x86\_64\-gp2 is available in all Regions, and always points to the latest version of the Amazon Linux 2 AMI in the Region\.

```
aws ec2 run-instances 
    --image-id resolve:ssm:/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2 
    --instance-type m5.xlarge 
    ...
```
# Finding a Linux AMI<a name="finding-an-ami"></a>

Before you can launch an instance, you must select an AMI to use\. As you select an AMI, consider the following requirements you might have for the instances that you'll launch:
+ The region
+ The operating system
+ The architecture: 32\-bit \(`i386`\) or 64\-bit \(`x86_64`\)
+ The root device type: Amazon EBS or instance store
+ The provider \(for example, Amazon Web Services\)
+ Additional software \(for example, SQL server\)

If you need to find a Windows AMI, see [Finding a Windows AMI](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/finding-an-ami.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Finding a Linux AMI Using the Amazon EC2 Console](#finding-an-ami-console)
+ [Finding an AMI Using the AWS CLI](#finding-an-ami-aws-cli)
+ [Finding a Quick Start AMI](#finding-quick-start-ami)

## Finding a Linux AMI Using the Amazon EC2 Console<a name="finding-an-ami-console"></a>

You can find Linux AMIs using the Amazon EC2 console\. You can search through all available AMIs using the **Images** page, or select from commonly used AMIs on the **Quick Start** tab when you use the console to launch an instance\. AMI IDs are unique to each region\.

**To find a Linux AMI using the Choose AMI page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region in which to launch your instances\. You can select any region that's available to you, regardless of your location\.

1. From the console dashboard, choose **Launch Instance**\.

1. On **Quick Start** tab, select from one of the commonly used AMIs in the list\. If you don't see the AMI that you need, select the **AWS Marketplace** or **Community AMIs** tab to find additional AMIs\.

**To find a Linux AMI using the Images page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region in which to launch your instances\. You can select any region that's available to you, regardless of your location\.

1. In the navigation pane, choose **AMIs**\.

1. \(Optional\) Use the **Filter** options to scope the list of displayed AMIs to see only the AMIs that interest you\. For example, to list all Linux AMIs provided by AWS, select **Public images**\. Choose the Search bar and select **Owner** from the menu, then select **Amazon images**\. Choose the Search bar again to select **Platform** and then the operating system from the list provided\.

1. \(Optional\) Choose the **Show/Hide Columns** icon to select which image attributes to display, such as the root device type\. Alternatively, you can select an AMI from the list and view its properties in the **Details** tab\.

1. Before you select an AMI, it's important that you check whether it's backed by instance store or by Amazon EBS and that you are aware of the effects of this difference\. For more information, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\.

1. To launch an instance from this AMI, select it and then choose **Launch**\. For more information about launching an instance using the console, see [Launching Your Instance from an AMI](launching-instance.md#choose-an-instance-type-page)\. If you're not ready to launch the instance now, make note of the AMI ID for later\.

## Finding an AMI Using the AWS CLI<a name="finding-an-ami-aws-cli"></a>

You can use AWS CLI commands for Amazon EC2 to list only the Linux AMIs that meet your needs\. After locating an AMI that meets your needs, make note of its ID so that you can use it to launch instances\. For more information, see [Launching an Instance Using the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-ec2-launch.html#launching-instances) in the *AWS Command Line Interface User Guide*\.

The [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command supports filtering parameters\. For example, use the `--owners` parameter to public AMIs owned by Amazon\.

```
aws ec2 describe-images --owners self amazon
```

You can add the following filter to the previous command to display only AMIs backed by Amazon EBS:

```
--filters "Name=root-device-type,Values=ebs"
```

**Important**  
Omitting the `--owners` flag from the describe\-images command will return all images for which you have launch permissions, regardless of ownership\.

## Finding a Quick Start AMI<a name="finding-quick-start-ami"></a>

When you launch an instance using the Amazon EC2 console, the **Choose an Amazon Machine Image \(AMI\)** page includes a list of popular AMIs on the **Quick Start** tab\. If you want to automate launching an instance using one of these quick start AMIs, you'll need to programatically locate the ID of the current version of the AMI\.

To locate the current version of a quick start AMI, you can enumerate all AMIs with its AMI name, and then find the one with the most recent creation date\.

**Example Example: Find the current Amazon Linux 2 AMI**  

```
aws ec2 describe-images --owners amazon --filters 'Name=name,Values=amzn2-ami-hvm-2.0.????????-x86_64-gp2' 'Name=state,Values=available' | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
```

**Example Example: Find the current Amazon Linux AMI**  

```
aws ec2 describe-images --owners amazon --filters 'Name=name,Values=amzn-ami-hvm-????.??.?.????????-x86_64-gp2' 'Name=state,Values=available' | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
```

**Example Example: Find the current Ubuntu Server 16\.04 LTS AMI**  

```
aws ec2 describe-images --owners 099720109477 --filters 'Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-????????' 'Name=state,Values=available' | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
```

**Example Example: Find the current Red Hat Enterprise Linux 7\.5 AMI**  

```
aws ec2 describe-images --owners amazon --filters 'Name=name,Values=RHEL-7.5_HVM_GA*' 'Name=state,Values=available' | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
```

**Example Example: Find the current SUSE Linux Enterprise Server 15 AMI**  

```
aws ec2 describe-images --owners amazon --filters 'Name=name,Values=suse-sles-15-v????????-hvm-ssd-x86_64' 'Name=state,Values=available' | jq -r '.Images | sort_by(.CreationDate) | last(.[]).ImageId'
```
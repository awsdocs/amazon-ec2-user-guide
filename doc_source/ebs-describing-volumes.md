# Viewing information about an Amazon EBS volume<a name="ebs-describing-volumes"></a>

You can view descriptive information about your EBS volumes\. For example, you can view information about all volumes in a specific Region or view detailed information about a single volume, including its size, volume type, whether the volume is encrypted, which master key was used to encrypt the volume, and the specific instance to which the volume is attached\.

You can get additional information about your EBS volumes, such as how much disk space is available, from the operating system on the instance\.

## View volume information in the console<a name="ebs-view-information-console"></a>

**To view information about an EBS volume**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. \(Optional\) Use the **Filter** options in the Search bar to display only the volumes that interest you\. For example, if you know the instance ID you want to see volumes for, go to the Search bar and choose **Instance ID** from the filter menu, then choose the instance ID you want from the list provided\. To remove a filter, choose it again\.

1. To view more information about a volume, select it\.

1. In the details pane, you can inspect the information provided about the volume\. **Attachment information** shows the instance ID this volume is attached to and the device name under which it is attached\.

1. \(Optional\) Choose the **Attachment information** link to view instance details\.

**To view the EBS volumes that are attached to an instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. To view more information about an instance, select it\.

1. In the details pane, you can inspect the information provided about root and block devices\. Choose the device name link to show information on the volume attached under that device name\.

1. \(Optional\) Choose the **EBS ID** link to view details for the volume attached to this instance under the device name chosen in the prior step\.

## View volume information from the command line<a name="ebs-view-information-cli"></a>

**To view information about an EBS volume using the command line**

You can use one of the following commands to view volume attributes\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) \(AWS CLI\)
+ [Get\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

## View volume metrics<a name="ebs-view-volume-metrics"></a>

You can get additional information about your EBS volumes from Amazon CloudWatch\. For more information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

## Viewing free disk space<a name="ebs-view-free-disk-space"></a>

You can get additional information about your EBS volumes, such as how much disk space is available, from the Linux operating system on the instance\. For example, use the following command:

```
[ec2-user ~]$ df -hT /dev/xvda1
Filesystem     Type      Size  Used Avail Use% Mounted on
/dev/xvda1     xfs       8.0G  1.2G  6.9G  15% /
```
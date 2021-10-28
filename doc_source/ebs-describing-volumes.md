# View information about an Amazon EBS volume<a name="ebs-describing-volumes"></a>

You can view descriptive information about your EBS volumes\. For example, you can view information about all volumes in a specific Region or view detailed information about a single volume, including its size, volume type, whether the volume is encrypted, which master key was used to encrypt the volume, and the specific instance to which the volume is attached\.

You can get additional information about your EBS volumes, such as how much disk space is available, from the operating system on the instance\.

## View volume information<a name="ebs-view-information-console"></a>

You can view information about a volume using one of the following methods\.

------
#### [ Console ]

**To view information about an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. \(Optional\) Use the filter options in the search field to display only the volumes that interest you\. For example, if you know the instance ID, choose **Instance ID** from the search field menu, and then choose the instance ID from the list provided\. To remove a filter, choose it again\.

1. Select the volume\.

1. In the details pane, you can inspect the information provided about the volume\. **Attachment information** shows the instance ID this volume is attached to and the device name under which it is attached\.

1. \(Optional\) Choose the **Attachment information** link to view additional details about the instance\.

**To view the EBS volumes that are attached to an instance using the **new console****

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. In the **Storage** tab, view the information provided about root and block devices\.

1. \(Optional\) Choose a link in the **Volume ID** column to view additional details for the volume\.

**To view the EBS volumes that are attached to an instance using the **old console****

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. In the **Description** tab, for **Block devices**, select the block device mapping and then choose the **EBS ID** to view additional details for the volume\.

------
#### [ AWS CLI ]

**To view information about an EBS volume using the command line**  
You can use one of the following commands to view volume attributes\. For more information, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) \(AWS CLI\)
+ [Get\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

------
#### [ Amazon EC2 Global View ]

You can use Amazon EC2 Global View to view your volumes across all Regions for which your AWS account is enabled\. For more information, see [List and filter resources across Regions using Amazon EC2 Global View](Using_Filtering.md#global-view)\.

------

### Volume state<a name="volume-state"></a>

Volume state describes the availability of an Amazon EBS volume\. You can view the volume state in the **State** column on the **Volumes** page in the console, or by using the [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) AWS CLI command\.

The possible volume states are:

`creating`  
The volume is being created\.

`available`  
The volume is not attached to an instance\.

`in-use`  
The volume is attached to an instance\.

`deleting`  
The volume is being deleted\.

`deleted`  
The volume is deleted\.

`error`  
The underlying hardware related to your EBS volume has failed, and the data associated with the volume is unrecoverable\. For information about how to restore the volume or recover the data on the volume, see [My EBS volume has a status of "error"](http://aws.amazon.com/premiumsupport/knowledge-center/ebs-error-status/)\.

## View volume metrics<a name="ebs-view-volume-metrics"></a>

You can get additional information about your EBS volumes from Amazon CloudWatch\. For more information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

## View free disk space<a name="ebs-view-free-disk-space"></a>

You can get additional information about your EBS volumes, such as how much disk space is available, from the Linux operating system on the instance\. For example, use the following command:

```
[ec2-user ~]$ df -hT /dev/xvda1
Filesystem     Type      Size  Used Avail Use% Mounted on
/dev/xvda1     xfs       8.0G  1.2G  6.9G  15% /
```

For information about viewing free disk space on a Windows instance, see [View free disk space](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-describing-volumes.html#ebs-view-free-disk-space) in the *Amazon EC2 User Guide for Windows Instances*\.
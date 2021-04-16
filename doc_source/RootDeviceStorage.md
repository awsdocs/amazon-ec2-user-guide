# Amazon EC2 instance root device volume<a name="RootDeviceStorage"></a>

When you launch an instance, the *root device volume* contains the image used to boot the instance\. When we introduced Amazon EC2, all AMIs were backed by Amazon EC2 instance store, which means the root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3\. After we introduced Amazon EBS, we introduced AMIs that are backed by Amazon EBS\. This means that the root device for an instance launched from the AMI is an Amazon EBS volume created from an Amazon EBS snapshot\.

You can choose between AMIs backed by Amazon EC2 instance store and AMIs backed by Amazon EBS\. We recommend that you use AMIs backed by Amazon EBS, because they launch faster and use persistent storage\.

**Important**  
Only the following instance types support an instance store volume as the root device: C3, D2, G2, I2, M3, and R3\.

For more information about the device names Amazon EC2 uses for your root volumes, see [Device names on Linux instances](device_naming.md)\.

**Topics**
+ [Root device storage concepts](#RootDeviceStorageConcepts)
+ [Choose an AMI by root device type](#choose-an-ami-by-root-device)
+ [Determine the root device type of your instance](#display-instance-root-device-type)
+ [Change the root volume to persist](#Using_RootDeviceStorage)
+ [Change the initial size of the root volume](#change-root-volume-initial-size)

## Root device storage concepts<a name="RootDeviceStorageConcepts"></a>

You can launch an instance from either an instance store\-backed AMI or an Amazon EBS\-backed AMI\. The description of an AMI includes which type of AMI it is; you'll see the root device referred to in some places as either `ebs` \(for Amazon EBS\-backed\) or `instance store` \(for instance store\-backed\)\. This is important because there are significant differences between what you can do with each type of AMI\. For more information about these differences, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.

**Instance store\-backed instances**  
Instances that use instance stores for the root device automatically have one or more instance store volumes available, with one volume serving as the root device volume\. When an instance is launched, the image that is used to boot the instance is copied to the root volume\. Note that you can optionally use additional instance store volumes, depending on the instance type\.

Any data on the instance store volumes persists as long as the instance is running, but this data is deleted when the instance is terminated \(instance store\-backed instances do not support the **Stop** action\) or if it fails \(such as if an underlying drive has issues\)\.

![\[Root device on an Amazon EC2 instance store-backed instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance_store_backed_instance.png)

After an instance store\-backed instance fails or terminates, it cannot be restored\. If you plan to use Amazon EC2 instance store\-backed instances, we highly recommend that you distribute the data on your instance stores across multiple Availability Zones\. You should also back up critical data from your instance store volumes to persistent storage on a regular basis\.

For more information, see [Amazon EC2 instance store](InstanceStorage.md)\.

**Amazon EBS\-backed instances**  
Instances that use Amazon EBS for the root device automatically have an Amazon EBS volume attached\. When you launch an Amazon EBS\-backed instance, we create an Amazon EBS volume for each Amazon EBS snapshot referenced by the AMI you use\. You can optionally use other Amazon EBS volumes or instance store volumes, depending on the instance type\.

![\[Root device volume and other Amazon EBS volumes of an Amazon EBS-backed instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ebs_backed_instance.png)

An Amazon EBS\-backed instance can be stopped and later restarted without affecting data stored in the attached volumes\. There are various instance– and volume\-related tasks you can do when an Amazon EBS\-backed instance is in a stopped state\. For example, you can modify the properties of the instance, change its size, or update the kernel it is using, or you can attach your root volume to a different running instance for debugging or any other purpose\. 

If an Amazon EBS\-backed instance fails, you can restore your session by following one of these methods:
+ Stop and then start again \(try this method first\)\.
+ Automatically snapshot all relevant volumes and create a new AMI\. For more information, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.
+ Attach the volume to the new instance by following these steps:

  1. Create a snapshot of the root volume\.

  1. Register a new AMI using the snapshot\.

  1. Launch a new instance from the new AMI\.

  1. Detach the remaining Amazon EBS volumes from the old instance\.

  1. Reattach the Amazon EBS volumes to the new instance\.

For more information, see [Amazon EBS volumes](ebs-volumes.md)\.

## Choose an AMI by root device type<a name="choose-an-ami-by-root-device"></a>

The AMI that you specify when you launch your instance determines the type of root device volume that your instance has\.

**To choose an Amazon EBS\-backed AMI using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **AMIs**\.

1. From the filter lists, select the image type \(such as **Public images**\)\. In the search bar choose **Platform** to select the operating system \(such as **Amazon Linux**\), and **Root Device Type** to select **EBS images**\.

1. \(Optional\) To get additional information to help you make your choice, choose the **Show/Hide Columns** icon, update the columns to display, and choose **Close**\.

1. Choose an AMI and write down its AMI ID\.

**To choose an instance store\-backed AMI using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **AMIs**\.

1. From the filter lists, select the image type \(such as **Public images**\)\. In the search bar, choose **Platform** to select the operating system \(such as **Amazon Linux**\), and **Root Device Type** to select **Instance store**\.

1. \(Optional\) To get additional information to help you make your choice, choose the **Show/Hide Columns** icon, update the columns to display, and choose **Close**\.

1. Choose an AMI and write down its AMI ID\.

**To verify the type of the root device volume of an AMI using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)
+ [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-ec2-get-amis.html#pstools-ec2-get-image) \(AWS Tools for Windows PowerShell\)

## Determine the root device type of your instance<a name="display-instance-root-device-type"></a>

------
#### [ New console ]

**To determine the root device type of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. On the **Storage** tab, under **Root device details**, check the value of **Root device type** as follows:
   + If the value is `EBS`, this is an Amazon EBS\-backed instance\.
   + If the value is `INSTANCE-STORE`, this is an instance store\-backed instance\.

------
#### [ Old console ]

**To determine the root device type of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. On the **Description** tab, check the value of **Root device type** as follows:
   + If the value is `ebs`, this is an Amazon EBS\-backed instance\.
   + If the value is `instance store`, this is an instance store\-backed instance\.

------

**To determine the root device type of an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Change the root volume to persist<a name="Using_RootDeviceStorage"></a>

By default, the root volume for an AMI backed by Amazon EBS is deleted when the instance terminates\. You can change the default behavior to ensure that the volume persists after the instance terminates\. To change the default behavior, set the `DeleteOnTermination` attribute to `false` using a block device mapping\.

**Topics**
+ [Configure the root volume to persist during instance launch](#Using_ChangeRootDeviceVolumeToPersist)
+ [Configure the root volume to persist for an existing instance](#set-deleteOnTermination-aws-cli)
+ [Confirm that a root volume is configured to persist](#Using_ConfirmRootDeviceVolumeToPersist)

### Configure the root volume to persist during instance launch<a name="Using_ChangeRootDeviceVolumeToPersist"></a>

You can configure the root volume to persist when you launch an instance using the Amazon EC2 console or the command line tools\.

**To configure the root volume to persist when you launch an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then choose **Launch instances**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select the AMI to use and choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect **Delete On Termination** for the root volume\.

1. Complete the remaining wizard pages, and then choose **Launch**\.

**To configure the root volume to persist when you launch an instance using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and include a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
$ aws ec2 run-instances --block-device-mappings file://mapping.json ...other parameters...
```

Specify the following in `mapping.json`\.

```
[
    {
        "DeviceName": "/dev/sda1",
        "Ebs": {
            "DeleteOnTermination": false
        }
    }
]
```

**To configure the root volume to persist when you launch an instance using the Tools for Windows PowerShell**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command and include a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
C:\> $ebs = New-Object Amazon.EC2.Model.EbsBlockDevice
C:\> $ebs.DeleteOnTermination = $false
C:\> $bdm = New-Object Amazon.EC2.Model.BlockDeviceMapping
C:\> $bdm.DeviceName = "dev/xvda"
C:\> $bdm.Ebs = $ebs
C:\> New-EC2Instance -ImageId ami-0abcdef1234567890 -BlockDeviceMapping $bdm ...other parameters...
```

### Configure the root volume to persist for an existing instance<a name="set-deleteOnTermination-aws-cli"></a>

You can configure the root volume to persist for a running instance using the command line tools only\. 

**To configure the root volume to persist for an existing instance using the AWS CLI**  
Use the [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) command with a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --block-device-mappings "[{\"DeviceName\": \"/dev/xvda\",\"Ebs\":{\"DeleteOnTermination\":false}}]"
```

**To configure the root volume to persist for an existing instance using the AWS Tools for Windows PowerShell**  
Use the [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) command with a block device mapping that sets the `DeleteOnTermination` attribute to `false`\.

```
C:\> $ebs = New-Object Amazon.EC2.Model.EbsInstanceBlockDeviceSpecification
C:\> $ebs.DeleteOnTermination = $false
C:\> $bdm = New-Object Amazon.EC2.Model.InstanceBlockDeviceMappingSpecification
C:\> $bdm.DeviceName = "/dev/xvda"
C:\> $bdm.Ebs = $ebs
C:\> Edit-EC2InstanceAttribute -InstanceId i-1234567890abcdef0 -BlockDeviceMapping $bdm
```

### Confirm that a root volume is configured to persist<a name="Using_ConfirmRootDeviceVolumeToPersist"></a>

You can confirm that a root volume is configured to persist using the Amazon EC2 console or the command line tools\.

------
#### [ New console ]

**To confirm that a root volume is configured to persist using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select the instance\.

1. In the **Storage** tab, under **Block devices**, locate the entry for the root volume\. If **Delete on termination** is `No`, the volume is configured to persist\.

------
#### [ Old console ]

**To confirm that a root volume is configured to persist using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select the instance\.

1. In the **Description** tab, choose the entry for **Root device**\. If **Delete on termination** is `False`, the volume is configured to persist\.

------

**To confirm that a root volume is configured to persist using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and verify that the `DeleteOnTermination` attribute in the `BlockDeviceMappings` response element is set to `false`\.

```
$ aws ec2 describe-instances --instance-id i-1234567890abcdef0
```

```
...
    "BlockDeviceMappings": [
    {
        "DeviceName": "/dev/sda1",
        "Ebs": {
            "Status": "attached",
            "DeleteOnTermination": false,
            "VolumeId": "vol-1234567890abcdef0",
            "AttachTime": "2013-07-19T02:42:39.000Z"
        }
    }              
...
```

**To confirm that a root volume is configured to persist using the AWS Tools for Windows PowerShell**  
Use the [ Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) and verify that the `DeleteOnTermination` attribute in the `BlockDeviceMappings` response element is set to `false`\.

```
C:\> (Get-EC2Instance -InstanceId i-i-1234567890abcdef0).Instances.BlockDeviceMappings.Ebs
```

## Change the initial size of the root volume<a name="change-root-volume-initial-size"></a>

By default, the size of the root volume is determined by the size of the snapshot\. You can increase the initial size of the root volume using the block device mapping of the instance as follows\.

1. Determine the device name of the root volume specified in the AMI, as described in [View the EBS volumes in an AMI block device mapping](block-device-mapping-concepts.md#view-ami-bdm)\.

1. Confirm the size of the snapshot specified in the AMI block device mapping, as described in [View Amazon EBS snapshot information](ebs-describing-snapshots.md)\.

1. Override the size of the root volume using the instance block device mapping, as described in [Update the block device mapping when launching an instance](block-device-mapping-concepts.md#Using_OverridingAMIBDM), specifying a volume size that is larger than the snapshot size\.

For example, the following entry for the instance block device mapping increases the size of the root volume, `/dev/xvda`, to 100 GiB\. You can omit the snapshot ID in the instance block device mapping because the snapshot ID is already specified in the AMI block device mapping\.

```
{
    "DeviceName": "/dev/xvda",
    "Ebs": {
      "VolumeSize": 100
    }
}
```

For more information, see [Block device mappings](block-device-mapping-concepts.md)\.
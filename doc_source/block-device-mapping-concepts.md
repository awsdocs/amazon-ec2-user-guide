# Block device mapping<a name="block-device-mapping-concepts"></a>

Each instance that you launch has an associated root device volume, which is either an Amazon EBS volume or an instance store volume\. You can use block device mapping to specify additional EBS volumes or instance store volumes to attach to an instance when it's launched\. You can also attach additional EBS volumes to a running instance; see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\. However, the only way to attach instance store volumes to an instance is to use block device mapping to attach the volumes as the instance is launched\.

For more information about root device volumes, see [Changing the root volume to persist](RootDeviceStorage.md#Using_RootDeviceStorage)\.

**Topics**
+ [Block device mapping concepts](#block-device-mapping-def)
+ [AMI block device mapping](#ami-block-device-mapping)
+ [Instance block device mapping](#instance-block-device-mapping)

## Block device mapping concepts<a name="block-device-mapping-def"></a>

A *block device* is a storage device that moves data in sequences of bytes or bits \(blocks\)\. These devices support random access and generally use buffered I/O\. Examples include hard disks, CD\-ROM drives, and flash drives\. A block device can be physically attached to a computer or accessed remotely as if it were physically attached to the computer\.

Amazon EC2 supports two types of block devices: 
+ Instance store volumes \(virtual devices whose underlying hardware is physically attached to the host computer for the instance\)
+ EBS volumes \(remote storage devices\)

A *block device mapping* defines the block devices \(instance store volumes and EBS volumes\) to attach to an instance\. You can specify a block device mapping as part of creating an AMI so that the mapping is used by all instances launched from the AMI\. Alternatively, you can specify a block device mapping when you launch an instance, so this mapping overrides the one specified in the AMI from which you launched the instance\. Note that all NVMe instance store volumes supported by an instance type are automatically enumerated and assigned a device name on instance launch; including them in your block device mapping has no effect\.

**Topics**
+ [Block device mapping entries](#parts-of-a-block-device-mapping)
+ [Block device mapping instance store caveats](#instance_store_caveats)
+ [Example block device mapping](#block-device-mapping-ex)
+ [How devices are made available in the operating system](#bdm-to-os)

### Block device mapping entries<a name="parts-of-a-block-device-mapping"></a>

When you create a block device mapping, you specify the following information for each block device that you need to attach to the instance:
+ The device name used within Amazon EC2\. The block device driver for the instance assigns the actual volume name when mounting the volume\. The name assigned can be different from the name that Amazon EC2 recommends\. For more information, see [Device naming on Linux instances](device_naming.md)\.

For Instance store volumes, you also specify the following information:
+ The virtual device: `ephemeral[0-23]`\. Note that the number and size of available instance store volumes for your instance varies by instance type\.

For NVMe instance store volumes, the following information also applies:
+ These volumes are automatically enumerated and assigned a device name; including them in your block device mapping has no effect\.

For EBS volumes, you also specify the following information:
+ The ID of the snapshot to use to create the block device \(snap\-*xxxxxxxx*\)\. This value is optional as long as you specify a volume size\.
+ The size of the volume, in GiB\. The specified size must be greater than or equal to the size of the specified snapshot\.
+ Whether to delete the volume on instance termination \(`true` or `false`\)\. The default value is `true` for the root device volume and `false` for attached volumes\. When you create an AMI, its block device mapping inherits this setting from the instance\. When you launch an instance, it inherits this setting from the AMI\.
+ The volume type, which can be `gp2` and `gp3` for General Purpose SSD, `io1` and `io2` for Provisioned IOPS SSD, `st1` for Throughput Optimized HDD, `sc1` for Cold HDD, or `standard` for Magnetic\. The default value is `gp2`\.
+ The number of input/output operations per second \(IOPS\) that the volume supports\. \(Used only with `io1` and `io2` volumes\.\)

### Block device mapping instance store caveats<a name="instance_store_caveats"></a>

There are several caveats to consider when launching instances with AMIs that have instance store volumes in their block device mappings\.
+ Some instance types include more instance store volumes than others, and some instance types contain no instance store volumes at all\. If your instance type supports one instance store volume, and your AMI has mappings for two instance store volumes, then the instance launches with one instance store volume\.
+ Instance store volumes can only be mapped at launch time\. You cannot stop an instance without instance store volumes \(such as the `t2.micro`\), change the instance to a type that supports instance store volumes, and then restart the instance with instance store volumes\. However, you can create an AMI from the instance and launch it on an instance type that supports instance store volumes, and map those instance store volumes to the instance\.
+ If you launch an instance with instance store volumes mapped, and then stop the instance and change it to an instance type with fewer instance store volumes and restart it, the instance store volume mappings from the initial launch still show up in the instance metadata\. However, only the maximum number of supported instance store volumes for that instance type are available to the instance\.
**Note**  
When an instance is stopped, all data on the instance store volumes is lost\.
+ Depending on instance store capacity at launch time, M3 instances may ignore AMI instance store block device mappings at launch unless they are specified at launch\. You should specify instance store block device mappings at launch time, even if the AMI you are launching has the instance store volumes mapped in the AMI, to ensure that the instance store volumes are available when the instance launches\. 

### Example block device mapping<a name="block-device-mapping-ex"></a>

This figure shows an example block device mapping for an EBS\-backed instance\. It maps `/dev/sdb` to `ephemeral0` and maps two EBS volumes, one to `/dev/sdh` and the other to `/dev/sdj`\. It also shows the EBS volume that is the root device volume, `/dev/sda1`\.

![\[Relationship between instance, instance store volumes, and EBS volumes.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/block_device_mapping_figure.png)

Note that this example block device mapping is used in the example commands and APIs in this topic\. You can find example commands and APIs that create block device mappings in [Specifying a block device mapping for an AMI](#create-ami-bdm) and [Updating the block device mapping when launching an instance](#Using_OverridingAMIBDM)\.

### How devices are made available in the operating system<a name="bdm-to-os"></a>

Device names like `/dev/sdh` and `xvdh` are used by Amazon EC2 to describe block devices\. The block device mapping is used by Amazon EC2 to specify the block devices to attach to an EC2 instance\. After a block device is attached to an instance, it must be mounted by the operating system before you can access the storage device\. When a block device is detached from an instance, it is unmounted by the operating system and you can no longer access the storage device\.

With a Linux instance, the device names specified in the block device mapping are mapped to their corresponding block devices when the instance first boots\. The instance type determines which instance store volumes are formatted and mounted by default\. You can mount additional instance store volumes at launch, as long as you don't exceed the number of instance store volumes available for your instance type\. For more information, see [Amazon EC2 instance store](InstanceStorage.md)\. The block device driver for the instance determines which devices are used when the volumes are formatted and mounted\. For more information, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

## AMI block device mapping<a name="ami-block-device-mapping"></a>

Each AMI has a block device mapping that specifies the block devices to attach to an instance when it is launched from the AMI\. An AMI that Amazon provides includes a root device only\. To add more block devices to an AMI, you must create your own AMI\.

**Topics**
+ [Specifying a block device mapping for an AMI](#create-ami-bdm)
+ [Viewing the EBS volumes in an AMI block device mapping](#view-ami-bdm)

### Specifying a block device mapping for an AMI<a name="create-ami-bdm"></a>

There are two ways to specify volumes in addition to the root volume when you create an AMI\. If you've already attached volumes to a running instance before you create an AMI from the instance, the block device mapping for the AMI includes those same volumes\. For EBS volumes, the existing data is saved to a new snapshot, and it's this new snapshot that's specified in the block device mapping\. For instance store volumes, the data is not preserved\.

For an EBS\-backed AMI, you can add EBS volumes and instance store volumes using a block device mapping\. For an instance store\-backed AMI, you can add instance store volumes only by modifying the block device mapping entries in the image manifest file when registering the image\.

**Note**  
For M3 instances, you must specify instance store volumes in the block device mapping for the instance when you launch it\. When you launch an M3 instance, instance store volumes specified in the block device mapping for the AMI may be ignored if they are not specified as part of the instance block device mapping\.

**To add volumes to an AMI using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **Instances**\.

1. Select an instance and choose **Actions**, **Image and templates**, **Create image**\.

1. Enter a name and a description for the image\.

1. The instance volumes appear under **Instance volumes**\. To add another volume, choose **Add volume**\.

1. For **Volume type**, choose the volume type\. For **Device** choose the device name\. For an EBS volume, you can specify additional details, such as a snapshot, volume size, volume type, IOPS, and encryption state\.

1. Choose **Create image**\.

**To add volumes to an AMI using the command line**

Use the [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) AWS CLI command to specify a block device mapping for an EBS\-backed AMI\. Use the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) AWS CLI command to specify a block device mapping for an instance store\-backed AMI\.

Specify the block device mapping using the `--block-device-mappings` parameter\. Arguments encoded in JSON can be supplied either directly on the command line or by reference to a file:

```
--block-device-mappings [mapping, ...]
--block-device-mappings [file://mapping.json]
```

To add an instance store volume, use the following mapping\.

```
{
    "DeviceName": "/dev/sdf",
    "VirtualName": "ephemeral0"
}
```

To add an empty 100 GiB `gp2` volume, use the following mapping\.

```
{
    "DeviceName": "/dev/sdg",
    "Ebs": {
      "VolumeSize": 100
    }
}
```

To add an EBS volume based on a snapshot, use the following mapping\.

```
{
    "DeviceName": "/dev/sdh",
    "Ebs": {
      "SnapshotId": "snap-xxxxxxxx"
    }
}
```

To omit a mapping for a device, use the following mapping\.

```
{
    "DeviceName": "/dev/sdj",
    "NoDevice": ""
}
```

Alternatively, you can use the `-BlockDeviceMapping` parameter with the following commands \(AWS Tools for Windows PowerShell\):
+ [New\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html)
+ [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html)

### Viewing the EBS volumes in an AMI block device mapping<a name="view-ami-bdm"></a>

You can easily enumerate the EBS volumes in the block device mapping for an AMI\.

**To view the EBS volumes for an AMI using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **AMIs**\.

1. Choose **EBS images** from the **Filter** list to get a list of EBS\-backed AMIs\. 

1. Select the desired AMI, and look at the **Details** tab\. At a minimum, the following information is available for the root device:
   + **Root Device Type** \(`ebs`\)
   + **Root Device Name** \(for example, `/dev/sda1`\)
   + **Block Devices** \(for example, `/dev/sda1=snap-1234567890abcdef0:8:true`\)

   If the AMI was created with additional EBS volumes using a block device mapping, the **Block Devices** field displays the mapping for those additional volumes as well\. \(This screen doesn't display instance store volumes\.\)

**To view the EBS volumes for an AMI using the command line**

Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\) command or [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(AWS Tools for Windows PowerShell\) command to enumerate the EBS volumes in the block device mapping for an AMI\.

## Instance block device mapping<a name="instance-block-device-mapping"></a>

By default, an instance that you launch includes any storage devices specified in the block device mapping of the AMI from which you launched the instance\. You can specify changes to the block device mapping for an instance when you launch it, and these updates overwrite or merge with the block device mapping of the AMI\.

**Limitations**
+ For the root volume, you can only modify the following: volume size, volume type, and the **Delete on Termination** flag\.
+ When you modify an EBS volume, you can't decrease its size\. Therefore, you must specify a snapshot whose size is equal to or greater than the size of the snapshot specified in the block device mapping of the AMI\.

**Topics**
+ [Updating the block device mapping when launching an instance](#Using_OverridingAMIBDM)
+ [Updating the block device mapping of a running instance](#update-instance-bdm)
+ [Viewing the EBS volumes in an instance block device mapping](#view-instance-bdm)
+ [Viewing the instance block device mapping for instance store volumes](#bdm-instance-metadata)

### Updating the block device mapping when launching an instance<a name="Using_OverridingAMIBDM"></a>

You can add EBS volumes and instance store volumes to an instance when you launch it\. Note that updating the block device mapping for an instance doesn't make a permanent change to the block device mapping of the AMI from which it was launched\.

**To add volumes to an instance using the console**

1. Open the Amazon EC2 console\.

1. From the dashboard, choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select the AMI to use and choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, you can modify the root volume, EBS volumes, and instance store volumes as follows:
   + To change the size of the root volume, locate the **Root** volume under the **Type** column, and change its **Size** field\.
   + To suppress an EBS volume specified by the block device mapping of the AMI used to launch the instance, locate the volume and click its **Delete** icon\.
   + To add an EBS volume, choose **Add New Volume**, choose **EBS** from the **Type** list, and fill in the fields \(**Device**, **Snapshot**, and so on\)\.
   + To suppress an instance store volume specified by the block device mapping of the AMI used to launch the instance, locate the volume, and choose its **Delete** icon\.
   + To add an instance store volume, choose **Add New Volume**, select **Instance Store** from the **Type** list, and select a device name from **Device**\.

1. Complete the remaining wizard pages, and choose **Launch**\.

**To add volumes to an instance using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command with the `--block-device-mappings` option to specify a block device mapping for an instance at launch\.

For example, suppose that an EBS\-backed AMI specifies the following block device mapping:
+ /dev/sdb=ephemeral0
+ /dev/sdh=snap\-1234567890abcdef0
+ /dev/sdj=:100

To prevent `/dev/sdj` from attaching to an instance launched from this AMI, use the following mapping\.

```
{
    "DeviceName": "/dev/sdj",
    "NoDevice": ""
}
```

To increase the size of `/dev/sdh` to 300 GiB, specify the following mapping\. Notice that you don't need to specify the snapshot ID for `/dev/sdh`, because specifying the device name is enough to identify the volume\.

```
{
    "DeviceName": "/dev/sdh",
    "Ebs": {
      "VolumeSize": 300
    }
}
```

To increase the size of the root volume at instance launch, first call [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) with the ID of the AMI to verify the device name of the root volume\. For example, `"RootDeviceName": "/dev/xvda"`\. To override the size of the root volume, specify the device name of the root device used by the AMI and the new volume size\.

```
{
    "DeviceName": "/dev/xvda",
    "Ebs": {
      "VolumeSize": 100
    }
}
```

To attach an additional instance store volume, `/dev/sdc`, specify the following mapping\. If the instance type doesn't support multiple instance store volumes, this mapping has no effect\. If the instance supports NVMe instance store volumes, they are automatically enumerated and assigned an NVMe device name\.

```
{
    "DeviceName": "/dev/sdc",
    "VirtualName": "ephemeral1"
}
```

**To add volumes to an instance using the AWS Tools for Windows PowerShell**  
Use the `-BlockDeviceMapping` parameter with the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command \(AWS Tools for Windows PowerShell\)\.

### Updating the block device mapping of a running instance<a name="update-instance-bdm"></a>

You can use the [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) AWS CLI command to update the block device mapping of a running instance\. You do not need to stop the instance before changing this attribute\.

```
aws ec2 modify-instance-attribute --instance-id i-1a2b3c4d --block-device-mappings file://mapping.json
```

For example, to preserve the root volume at instance termination, specify the following in `mapping.json`\.

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

Alternatively, you can use the `-BlockDeviceMapping` parameter with the [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) command \(AWS Tools for Windows PowerShell\)\.

### Viewing the EBS volumes in an instance block device mapping<a name="view-instance-bdm"></a>

You can easily enumerate the EBS volumes mapped to an instance\.

**Note**  
For instances launched before the release of the 2009\-10\-31 API, AWS can't display the block device mapping\. You must detach and reattach the volumes so that AWS can display the block device mapping\.

**To view the EBS volumes for an instance using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **Instances**\.

1. In the search box, enter **Root device type**, and then choose **EBS**\. This displays a list of EBS\-backed instances\.

1. Select the desired instance and look at the details displayed in the **Storage** tab\. At a minimum, the following information is available for the root device:
   + **Root device type** \(for example, **EBS**\)
   + **Root device name** \(for example, `/dev/xvda`\)
   + **Block devices** \(for example, `/dev/xvda`, `/dev/sdf`, and `/dev/sdj`\)

   If the instance was launched with additional EBS volumes using a block device mapping, they appear under **Block devices**\. Any instance store volumes do not appear on this tab\.

1. To display additional information about an EBS volume, choose its volume ID to go to the volume page\. For more information, see [Viewing information about an Amazon EBS volume](ebs-describing-volumes.md)\.

**To view the EBS volumes for an instance using the command line**

Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) command or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command to enumerate the EBS volumes in the block device mapping for an instance\.

### Viewing the instance block device mapping for instance store volumes<a name="bdm-instance-metadata"></a>

When you view the block device mapping for your instance, you can see only the EBS volumes, not the instance store volumes\. You can use instance metadata to query the non\-NVMe instance store volumes in the block device mapping\. NVMe instance store volumes are not included\.

The base URI for all requests for instance metadata is `http://169.254.169.254/latest/`\. For more information, see [Instance metadata and user data](ec2-instance-metadata.md)\.

First, connect to your running instance\. From the instance, use this query to get its block device mapping\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/block-device-mapping/
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/block-device-mapping/
```

------

The response includes the names of the block devices for the instance\. For example, the output for an instance store–backed `m1.small` instance looks like this\.

```
ami
ephemeral0
root
swap
```

The `ami` device is the root device as seen by the instance\. The instance store volumes are named `ephemeral[0-23]`\. The `swap` device is for the page file\. If you've also mapped EBS volumes, they appear as `ebs1`, `ebs2`, and so on\.

To get details about an individual block device in the block device mapping, append its name to the previous query, as shown here\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/block-device-mapping/ephemeral0
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/block-device-mapping/ephemeral0
```

------

The instance type determines the number of instance store volumes that are available to the instance\. If the number of instance store volumes in a block device mapping exceeds the number of instance store volumes available to an instance, the additional volumes are ignored\. To view the instance store volumes for your instance, run the lsblk command\. To learn how many instance store volumes are supported by each instance type, see [Instance store volumes](InstanceStorage.md#instance-store-volumes)\.
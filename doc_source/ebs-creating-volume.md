# Create an Amazon EBS volume<a name="ebs-creating-volume"></a>

You can create an Amazon EBS volume and then attach it to any EC2 instance in the same Availability Zone\. If you create an encrypted EBS volume, you can only attach it to supported instance types\. For more information, see [Supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.

If you are creating a volume for a high\-performance storage scenario, you should make sure to use a Provisioned IOPS SSD volume \(`io1` or `io2`\) and attach it to an instance with enough bandwidth to support your application, such as an EBS\-optimized instance\. The same advice holds for Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) volumes\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Empty EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. However, storage blocks on volumes that were created from snapshots must be initialized \(pulled down from Amazon S3 and written to the volume\) before you can access the block\. This preliminary action takes time and can cause a significant increase in the latency of an I/O operation the first time each block is accessed\. Volume performance is achieved after all blocks have been downloaded and written to the volume\. For most applications, amortizing this cost over the lifetime of the volume is acceptable\. To avoid this initial performance hit in a production environment, you can force immediate initialization of the entire volume or enable fast snapshot restore\. For more information, see [Initialize Amazon EBS volumes](ebs-initialize.md)\.

**Methods of creating a volume**
+ Create and attach EBS volumes when you launch instances by specifying a block device mapping\. For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md) and [Block device mappings](block-device-mapping-concepts.md)\.
+ Create an empty EBS volume and attach it to a running instance\. For more information, see [Create an empty volume](#ebs-create-empty-volume) below\.
+ Create an EBS volume from a previously created snapshot and attach it to a running instance\. For more information, see [Create a volume from a snapshot](#ebs-create-volume-from-snapshot) below\.

## Create an empty volume<a name="ebs-create-empty-volume"></a>

Empty volumes receive their maximum performance the moment that they are available and do not require initialization\.

You can create an empty EBS volume using one of the following methods\.

------
#### [ Console ]

**To create an empty EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which you would like to create your volume\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

1. For **Size**, enter the size of the volume, in GiB\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

1. For **IOPS**, enter the maximum number of input/output operations per second \(IOPS\) that the volume should provide\. You can specify IOPS only for `gp3`, `io1`, and `io2` volumes\.

1. For **Throughput**, enter the throughput that the volume should provide, in MiB/s\. You can specify throughput only for `gp3` volumes\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. An EBS volume must be attached to an EC2 instance that is in the same Availability Zone as the volume\.

1. \(Optional\) If the instance type supports EBS encryption and you want to encrypt the volume, select **Encrypt this volume** and choose a CMK\. If encryption by default is enabled in this Region, EBS encryption is enabled and the default CMK for EBS encryption is chosen\. You can choose a different CMK from **Master Key** or paste the full ARN of any key that you can access\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\. For more information, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

1. Choose **Create Volume**\. The volume is ready for use when the volume status is **Available**\.

1. To use your new volume, attach it to an instance, format it, and mount it\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

------
#### [ AWS CLI ]

**To create an empty EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

------

## Create a volume from a snapshot<a name="ebs-create-volume-from-snapshot"></a>

Volumes created from snapshots load lazily in the background\. This means that there is no need to wait for all of the data to transfer from Amazon S3 to your EBS volume before the instance can start accessing an attached volume and all its data\. If your instance accesses data that hasn't yet been loaded, the volume immediately downloads the requested data from Amazon S3, and then continues loading the rest of the volume data in the background\. Volume performance is achieved after all blocks are downloaded and written to the volume\. To avoid the initial performance hit in a production environment, see [Initialize Amazon EBS volumes](ebs-initialize.md)\.

New EBS volumes that are created from encrypted snapshots are automatically encrypted\. You can also encrypt a volume on\-the\-fly while restoring it from an unencrypted snapshot\. Encrypted volumes can only be attached to instance types that support EBS encryption\. For more information, see [Supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.

You can create a volume from a snapshot using one of the following methods\.

------
#### [ Console ]

**To create an EBS volume from a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region that your snapshot is located in\.

   To use the snapshot to create a volume in a different region, copy your snapshot to the new Region and then use it to create a volume in that Region\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

1. For **Snapshot ID**, start typing the ID or description of the snapshot from which you are restoring the volume, and choose it from the list of suggested options\.

1. \(Optional\) Select **Encrypt this volume** to change the encryption state of your volume\. This is optional if [encryption by default](EBSEncryption.md#encryption-by-default) is enabled\. Select a CMK from **Master Key** to specify a CMK other than the default CMK for EBS encryption\.

1. For **Size**, verify that the default size of the snapshot meets your needs or enter the size of the volume, in GiB\.

   If you specify both a volume size and a snapshot, the size must be equal to or greater than the snapshot size\. When you select a volume type and a snapshot, the minimum and maximum sizes for the volume are shown next to **Size**\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

1. For **IOPS**, enter the maximum number of input/output operations per second \(IOPS\) that the volume should provide\. You can specify IOPS only for `gp3`, `io1`, and `io2` volumes\.

1. For **Throughput**, enter the throughput that the volume should provide, in MiB/s\. You can specify throughput only for `gp3` volumes\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. An EBS volume must be attached to an EC2 instance that is in the same Availability Zone as the volume\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Volume**\.

1. To use your new volume, attach it to an instance and mount it\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

1. If you created a volume that is larger than the snapshot, you must extend the file system on the volume to take advantage of the extra space\. For more information, see [Amazon EBS Elastic Volumes](ebs-modify-volume.md)\.

------
#### [ AWS CLI ]

**To create an EBS volume from a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)

------
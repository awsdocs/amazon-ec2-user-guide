# Creating an Amazon EBS volume<a name="ebs-creating-volume"></a>

You can create an Amazon EBS volume that you can then attach to any EC2 instance within the same Availability Zone\. You can choose to create an encrypted EBS volume, but encrypted volumes can only be attached to supported instance types\. For more information, see [Supported instance types](EBSEncryption.md#EBSEncryption_supported_instances)\.

If you are creating a volume for a high\-performance storage scenario, you should make sure to use a Provisioned IOPS SSD \(`io1`\) volume and attach it to an instance with enough bandwidth to support your application, such as an EBS\-optimized instance or an instance with 10\-Gigabit network connectivity\. The same advice holds for Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) volumes\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. However, storage blocks on volumes that were restored from snapshots must be initialized \(pulled down from Amazon S3 and written to the volume\) before you can access the block\. This preliminary action takes time and can cause a significant increase in the latency of an I/O operation the first time each block is accessed\. For most applications, amortizing this cost over the lifetime of the volume is acceptable\. Performance is restored after the data is accessed once\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.

**Methods of creating a volume**
+ You can create an EBS volume and attach it to a running instance\. For more information, see the procedure below\.
+ You can create and attach EBS volumes when you launch instances by specifying a block device mapping\. For more information, see [Launching an instance using the Launch Instance Wizard](launching-instance.md) and [Block Device Mapping](block-device-mapping-concepts.md)\.
+ You can restore volumes from previously created snapshots\. For more information, see [Restoring an Amazon EBS volume from a snapshot](ebs-restoring-volume.md)\.

**To create a new \(empty\) EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the Region in which you would like to create your volume\. This choice is important because some Amazon EC2 resources can be shared between Regions, while others can't\. For more information, see [Resource locations](resources.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

1. For **Size \(GiB\)**, type the size of the volume\. For more information, see [Constraints on the size and configuration of an EBS volume](volume_constraints.md)\.

1. With a Provisioned IOPS SSD volume, for **IOPS**, type the maximum number of input/output operations per second \(IOPS\) that the volume should support\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. EBS volumes can only be attached to EC2 instances within the same Availability Zone\.

1. \(Optional\) If the instance type supports EBS encryption and you want to encrypt the volume, select **Encrypt this volume** and choose a CMK\. If encryption by default is enabled in this Region, EBS encryption is enabled and the default CMK for EBS encryption is chosen\. You can choose a different CMK from **Master Key** or paste the full ARN of any key that you can access\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\. For more information, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

1. Choose **Create Volume**\. After the volume status is **Available**, you can attach the volume to an instance\. For more information, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

**To create a new \(empty\) EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)
# Restoring an Amazon EBS Volume from a Snapshot<a name="ebs-restoring-volume"></a>

You can restore an Amazon EBS volume with data from a snapshot stored in Amazon S3\. You need to know the ID of the snapshot you want to restore your volume from and you need to have access permissions for the snapshot\. For more information on snapshots, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

EBS snapshots are the preferred backup tool on Amazon EC2 due to their speed, convenience, and cost\. When restoring a volume from a snapshot, you recreate its state at a specific point in the past with all data intact\. By attaching a restored volume to an instance, you can duplicate data across regions, create test environments, replace a damaged or corrupted production volume in its entirety, or retrieve specific files and directories and transfer them to another attached volume\. For more information, see [Amazon EBS Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSSnapshots.htm)\.

New volumes created from existing EBS snapshots load lazily in the background\. This means that after a volume is created from a snapshot, there is no need to wait for all of the data to transfer from Amazon S3 to your EBS volume before your attached instance can start accessing the volume and all its data\. If your instance accesses data that hasn't yet been loaded, the volume immediately downloads the requested data from Amazon S3, and continues loading the rest of the data in the background\.

EBS volumes that are restored from encrypted snapshots are automatically encrypted\. Encrypted volumes can only be attached to selected instance types\. For more information, see [Supported Instance Types](EBSEncryption.md#EBSEncryption_supported_instances)\.

Because of security constraints, you cannot directly restore an EBS volume from a shared encrypted snapshot that you do not own\. You must first create a copy of the snapshot, which you will own\. You can then restore a volume from that copy\. For more information, see [Amazon EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)\.

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. However, storage blocks on volumes that were restored from snapshots must be initialized \(pulled down from Amazon S3 and written to the volume\) before you can access the block\. This preliminary action takes time and can cause a significant increase in the latency of an I/O operation the first time each block is accessed\. Performance is restored after the data is accessed once\.

For most applications, amortizing the initialization cost over the lifetime of the volume is acceptable\. To ensure that your restored volume always functions at peak capacity in production, you can force the immediate initialization of the entire volume using dd or fio\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.

**To restore an EBS volume from a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that your snapshot is located in\.

   To restore the snapshot to a volume in a different region, you can copy your snapshot to the new region and then restore it to a volume in that region\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.
**Note**  
Some AWS accounts created before 2012 might have access to Availability Zones in us\-west\-1 or ap\-northeast\-1 that do not support Provisioned IOPS SSD \(`io1`\) volumes\. If you are unable to create an `io1` volume \(or launch an instance with an `io1` volume in its block device mapping\) in one of these regions, try a different Availability Zone in the region\. You can verify that an Availability Zone supports `io1` volumes by creating a 4 GiB `io1` volume in that zone\.

1. For **Snapshot**, start typing the ID or description of the snapshot from which you are restoring the volume, and choose it from the list of suggested options\.

   Volumes that are restored from encrypted snapshots can only be attached to instances that support Amazon EBS encryption\. For more information, see [Supported Instance Types](EBSEncryption.md#EBSEncryption_supported_instances)\.

1. For **Size \(GiB\)**, type the size of the volume, or verify that the default size of the snapshot is adequate\.
**Note**  
If you specify both a volume size and a snapshot, the size must be equal to or greater than the snapshot size\. When you select a volume type and a snapshot, the minimum and maximum sizes for the volume are shown next to **Size**\. Any AWS Marketplace product codes from the snapshot are propagated to the volume\.

1. With a Provisioned IOPS SSD volume, for **IOPS**, type the maximum number of input/output operations per second \(IOPS\) that the volume should support\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. EBS volumes can only be attached to EC2 instances in the same Availability Zone\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Volume**\.

1. After you've restored a volume from a snapshot, you can attach it to an instance to begin using it\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. If you restored a snapshot to a larger volume than the default for that snapshot, you must extend the file system on the volume to take advantage of the extra space\. For more information, see [Modifying the Size, Performance, or Type of an EBS Volume](ebs-modify-volume.md)\.

**To restore an EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)
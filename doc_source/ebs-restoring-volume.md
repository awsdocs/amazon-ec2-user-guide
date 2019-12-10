# Restoring an Amazon EBS Volume from a Snapshot<a name="ebs-restoring-volume"></a>

You can restore an Amazon EBS volume with data from a snapshot stored in Amazon S3\. You must know the ID of the snapshot and you must have access permissions for the snapshot\. For more information about snapshots, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

EBS snapshots are the preferred backup tool on Amazon EC2 due to their speed, convenience, and cost\. When restoring a volume from a snapshot, you recreate its state at a specific point in the past with all data intact\. By attaching a restored volume to an instance, you can duplicate data across regions, create test environments, replace a damaged or corrupted production volume in its entirety, or retrieve specific files and directories and transfer them to another attached volume\. For more information, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

New volumes created from existing EBS snapshots load lazily in the background\. This means that after a volume is created from a snapshot, there is no need to wait for all of the data to transfer from Amazon S3 to your EBS volume before your attached instance can start accessing the volume and all its data\. If your instance accesses data that hasn't yet been loaded, the volume immediately downloads the requested data from Amazon S3, and then continues loading the rest of the volume data in the background\.

## EBS Performance<a name="new-ebs-volume-performance"></a>

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\.

For volumes that were restored from snapshots, the storage blocks must be pulled down from Amazon S3 and written to the volume before you can access them\. This preliminary action takes time and can cause a significant increase in the latency of I/O operations the first time each block is accessed\. Volume performance is achieved after all blocks have been downloaded and written to the volume\.

For most applications, amortizing the initialization cost over the lifetime of the volume is acceptable\. To avoid this initial performance hit in a production environment, you can use one of the following options:
+ Force the immediate initialization of the entire volume\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.
+ Enable fast snapshot restore on a snapshot to ensure that the EBS volumes created from it are fully\-initialized at creation and instantly deliver all of their provisioned performance\. For more information, see [Amazon EBS Fast Snapshot Restore](ebs-fast-snapshot-restore.md)\.

## EBS Encryption<a name="new-volume-ebs-encryption"></a>

New EBS volumes that are restored from encrypted snapshots are automatically encrypted\. You can also encrypt a volume on\-the\-fly while restoring it from an unencrypted snapshot\. Encrypted volumes can only be attached to instance types that support EBS encryption\. For more information, see [Supported Instance Types](EBSEncryption.md#EBSEncryption_supported_instances)\.

Because of security constraints, you cannot directly restore an EBS volume from a shared encrypted snapshot that you do not own\. You must first create a copy of the snapshot, which you will own\. You can then restore a volume from that copy\. For more information, see [Encryption and Snapshot Copying](ebs-copy-snapshot.md#creating-encrypted-snapshots)\.

## Creating a Volume from a Snapshot<a name="ebs-create-volume-from-snapshot"></a>

Use the following procedure to create a volume from a snapshot\.

**To create an EBS volume from a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region that your snapshot is located in\.

   To restore the snapshot to a volume in a different region, you can copy your snapshot to the new region and then restore it to a volume in that region\. For more information, see [Copying an Amazon EBS Snapshot](ebs-copy-snapshot.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS Volume Types](ebs-volume-types.md)\.

1. For **Snapshot ID**, start typing the ID or description of the snapshot from which you are restoring the volume, and choose it from the list of suggested options\.

1. \(Optional\) Select **Encrypt this volume** to change the encryption state of your volume\. This is optional if [encryption by default](EBSEncryption.md#encryption-by-default) is enabled\. Select a CMK from **Master Key** to specify a CMK other than the default CMK for EBS encryption\.

1. For **Size \(GiB\)**, type the size of the volume, or verify that the default size of the snapshot is adequate\.

   If you specify both a volume size and a snapshot, the size must be equal to or greater than the snapshot size\. When you select a volume type and a snapshot, the minimum and maximum sizes for the volume are shown next to **Size**\. For more information, see [Constraints on the Size and Configuration of an EBS Volume](volume_constraints.md)\.

1. With a Provisioned IOPS SSD volume, for **IOPS**, type the maximum number of input/output operations per second \(IOPS\) that the volume should support\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. EBS volumes can only be attached to EC2 instances in the same Availability Zone\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Volume**\.

1. After you've restored a volume from a snapshot, you can attach it to an instance to begin using it\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. If you restored a snapshot to a larger volume than the default for that snapshot, you must extend the file system on the volume to take advantage of the extra space\. For more information, see [Amazon EBS Elastic Volumes](ebs-modify-volume.md)\.

**To create an EBS volume from a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)
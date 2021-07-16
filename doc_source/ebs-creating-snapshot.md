# Create Amazon EBS snapshots<a name="ebs-creating-snapshot"></a>

You can create a point\-in\-time snapshot of an EBS volume and use it as a baseline for new volumes or for data backup\. If you make periodic snapshots of a volume, the snapshots are incremental—the new snapshot saves only the blocks that have changed since your last snapshot\.

Snapshots occur asynchronously; the point\-in\-time snapshot is created immediately, but the status of the snapshot is `pending` until the snapshot is complete \(when all of the modified blocks have been transferred to Amazon S3\), which can take several hours for large initial snapshots or subsequent snapshots where many blocks have changed\. While it is completing, an in\-progress snapshot is not affected by ongoing reads and writes to the volume\.

You can take a snapshot of an attached volume that is in use\. However, snapshots only capture data that has been written to your Amazon EBS volume at the time the snapshot command is issued\. This might exclude any data that has been cached by any applications or the operating system\. If you can pause any file writes to the volume long enough to take a snapshot, your snapshot should be complete\. However, if you can't pause all file writes to the volume, you should unmount the volume from within the instance, issue the snapshot command, and then remount the volume to ensure a consistent and complete snapshot\. You can remount and use your volume while the snapshot status is `pending`\.

To make snapshot management easier, you can tag your snapshots during creation or add tags afterward\. For example, you can apply tags describing the original volume from which the snapshot was created, or the device name that was used to attach the original volume to an instance\. For more information, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

## Snapshot encryption<a name="ebs-create-snapshot-encrypted"></a>

Snapshots that are taken from encrypted volumes are automatically encrypted\. Volumes that are created from encrypted snapshots are also automatically encrypted\. The data in your encrypted volumes and any associated snapshots is protected both at rest and in motion\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

By default, only you can create volumes from snapshots that you own\. However, you can share your unencrypted snapshots with specific AWS accounts, or you can share them with the entire AWS community by making them public\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

You can share an encrypted snapshot only with specific AWS accounts\. For others to use your shared, encrypted snapshot, you must also share the CMK key that was used to encrypt it\. Users with access to your encrypted snapshot must create their own personal copy of it and then use that copy\. Your copy of a shared, encrypted snapshot can also be re\-encrypted using a different key\. For more information, see [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)\.

## Multi\-volume snapshots<a name="ebs-create-snapshot-multi-volume"></a>

You can create multi\-volume snapshots, which are point\-in\-time snapshots for all EBS volumes attached to an EC2 instance\. You can also create lifecycle policies to automate the creation and retention of multi\-volume snapshots\. For more information, see [Amazon Data Lifecycle Manager](snapshot-lifecycle.md)\.

After the snapshots are created, each snapshot is treated as an individual snapshot\. You can perform all snapshot operations, such as restore, delete, and copy across Regions or accounts, just as you would with a single volume snapshot\. You can also tag your multi\-volume snapshots as you would a single volume snapshot\. We recommend you tag your multiple volume snapshots to manage them collectively during restore, copy, or retention\.

Multi\-volume, crash\-consistent snapshots are typically restored as a set\. It is helpful to identify the snapshots that are in a crash\-consistent set by tagging your set with the instance ID, name, or other relevant details\. You can also choose to automatically copy tags from the source volume to the corresponding snapshots\. This helps you to set the snapshot metadata, such as access policies, attachment information, and cost allocation, to match the source volume\. 

After creating your snapshots, they appear in your EC2 console created at the exact point\-in\-time\. The snapshots are collectively managed and, therefore, if any one snapshot for the volume set fails, all of the other snapshots display an error status\.

## Amazon Data Lifecycle Manager<a name="automate-snapshots"></a>

You can create, retain, and delete snapshots manually, or you can use Amazon Data Lifecycle Manager to manage your snapshots for you\. For more information, see [Amazon Data Lifecycle Manager](snapshot-lifecycle.md)\.

## Considerations<a name="ebs-create-snapshot-limitations"></a>

The following considerations apply to creating snapshots:
+ When you create a snapshot for an EBS volume that serves as a root device, you should stop the instance before taking the snapshot\.
+ You cannot create snapshots from instances for which hibernation is enabled\.
+ You cannot create snapshots from hibernated instances\.
+ Although you can take a snapshot of a volume while a previous snapshot of that volume is in the `pending` status, having multiple `pending` snapshots of a volume can result in reduced volume performance until the snapshots complete\.
+ There is a limit of one `pending` snapshot for a single `st1` or `sc1` volume, or five `pending` snapshots for a single volume of the other volume types\. If you receive a `ConcurrentSnapshotLimitExceeded` error while trying to create multiple concurrent snapshots of the same volume, wait for one or more of the `pending` snapshots to complete before creating another snapshot of that volume\.
+ When a snapshot is created from a volume with an AWS Marketplace product code, the product code is propagated to the snapshot\.

## Create a snapshot<a name="ebs-create-snapshot"></a>

To create a snapshot from the specified volume, use one of the following methods\.

------
#### [ Console ]

**To create a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** under **Elastic Block Store** in the navigation pane\.

1. Choose **Create Snapshot**\.

1. For **Select resource type**, choose **Volume**\.

1. For **Volume**, select the volume\.

1. \(Optional\) Enter a description for the snapshot\.

1. \(Optional\) Choose **Add Tag** to add tags to your snapshot\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Snapshot**\.

------
#### [ AWS CLI ]

**To create a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) \(AWS CLI\)
+ [New\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)

------

## Create a multi\-volume snapshot<a name="ebs-create-snapshots"></a>

To create a snapshot from the volumes of an instance, use one of the following methods\.

------
#### [ Console ]

**To create multi\-volume snapshots using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** under **Elastic Block Store** in the navigation pane\.

1. Choose **Create Snapshot**\.

1. For **Select resource type**, choose **Instance**\.

1. Select the instance ID for which you want to create simultaneous backups for all of the attached EBS volumes\. Multi\-volume snapshots support up to 40 EBS volumes per instance\.

1. \(Optional\) Set **Exclude root volume**\.

1. \(Optional\) Set **Copy tags from volume** flag to automatically copy tags from the source volume to the corresponding snapshots\. This sets snapshot metadata—such as access policies, attachment information, and cost allocation—to match the source volume\.

1. \(Optional\) Choose **Add Tag** to add tags to your snapshot\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Snapshot**\.

   During snapshot creation, the snapshots are managed together\. If one of the snapshots in the volume set fails, the other snapshots are moved to error status for the volume set\. You can monitor the progress of your snapshots using [CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\. After the snapshot creation process completes, CloudWatch generates an event that contains the status and all of the relevant snapshots details for the affected instance\.

------
#### [ AWS CLI ]

**To create multi\-volume snapshots using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshots.html) \(AWS CLI\)
+ [New\-EC2SnapshotBatch](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SnapshotBatch.html) \(AWS Tools for Windows PowerShell\)

------

## Work with EBS snapshots<a name="using-snapshots"></a>

You can copy snapshots, share snapshots, and create volumes from snapshots\. For more information, see the following:
+ [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)
+ [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)
+ [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)
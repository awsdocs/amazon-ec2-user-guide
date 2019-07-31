# Creating Amazon EBS Snapshots<a name="ebs-creating-snapshot"></a>

A point\-in\-time snapshot of an EBS volume, can be used as a baseline for new volumes or for data backup\. If you make periodic snapshots of a volume, the snapshots are incremental\-only the blocks on the device that have changed after your last snapshot are saved in the new snapshot\. Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to retain only the most recent snapshot in order to restore the entire volume\.

Snapshots occur asynchronously; the point\-in\-time snapshot is created immediately, but the status of the snapshot is `pending` until the snapshot is complete \(when all of the modified blocks have been transferred to Amazon S3\), which can take several hours for large initial snapshots or subsequent snapshots where many blocks have changed\. While it is completing, an in\-progress snapshot is not affected by ongoing reads and writes to the volume\.

**Important**  
Although you can take a snapshot of a volume while a previous snapshot of that volume is in the `pending` status, having multiple `pending` snapshots of a volume can result in reduced volume performance until the snapshots complete\.  
There is a limit of five `pending` snapshots for a single `gp2`, `io1`, or Magnetic volume, and one `pending` snapshot for a single `st1` or `sc1` volume\. If you receive a `ConcurrentSnapshotLimitExceeded` error while trying to create multiple concurrent snapshots of the same volume, wait for one or more of the `pending` snapshots to complete before creating another snapshot of that volume\.

Snapshots that are taken from encrypted volumes are automatically encrypted\. Volumes that are created from encrypted snapshots are also automatically encrypted\. The data in your encrypted volumes and any associated snapshots is protected both at rest and in motion\. For more information, see [Amazon EBS Encryption](EBSEncryption.md)\.

By default, only you can create volumes from snapshots that you own\. However, you can share your unencrypted snapshots with specific AWS accounts, or you can share them with the entire AWS community by making them public\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

You can share an encrypted snapshot only with specific AWS accounts\. For others to use your shared, encrypted snapshot, you must also share the CMK key that was used to encrypt it\. Users with access to your encrypted snapshot must create their own personal copy of it and then use that copy to restore the volume\. Your copy of a shared, encrypted snapshot can also be re\-encrypted with a different key\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

When a snapshot is created from a volume with an AWS Marketplace product code, the product code is propagated to the snapshot\.

You can take a snapshot of an attached volume that is in use\. However, snapshots only capture data that has been written to your Amazon EBS volume at the time the snapshot command is issued\. This might exclude any data that has been cached by any applications or the operating system\. If you can pause any file writes to the volume long enough to take a snapshot, your snapshot should be complete\. However, if you can't pause all file writes to the volume, you should unmount the volume from within the instance, issue the snapshot command, and then remount the volume to ensure a consistent and complete snapshot\. You can remount and use your volume while the snapshot status is `pending`\.

Multi\-volume snapshots, or point\-in\-time snapshots for all EBS volumes attached to a single EC2 instance, can be created using the AWS Console, AWS CLI, or AWS SDK\. You can also create lifecycle policies to automate the creation and retention of multi\-volume snapshots\. For more information about creating EBS lifecycle policies, see [Automating the Amazon EBS Snapshot Lifecycle](snapshot-lifecycle.md)\.

To create a snapshot for an Amazon EBS volume that serves as a root device, you should stop the instance before taking the snapshot\.

To unmount the volume in Linux, use the following command, where *device\_name* is the device name \(for example, `/dev/sdh`\)\.

```
umount -d device_name
```

To make snapshot management easier, you can tag your snapshots during creation or add tags afterward\. For example, you can apply tags describing the original volume from which the snapshot was created, or the device name that was used to attach the original volume to an instance\. For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.

## Limitations for Creating Snapshots<a name="ebs-creating-snapshot-limitations"></a>

The following limitations apply to creating snapshots:
+ You cannot create snapshots from instances for which hibernation is enabled\.
+ You cannot create snapshots from hibernated instances\.

**To create a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** in the navigation pane\.

1. Choose **Create Snapshot**\.

1. On the **Create Snapshot** page, select the volume to create a snapshot for\. 

1. \(Optional\) Choose **Add tags to your snapshot**\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Snapshot**\.

**To create a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) \(AWS CLI\)
+ [New\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)

**To create multi\-volume snapshots using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Snapshots** under **Elastic Block Store** in the navigation pane\.

1. Choose **Create Snapshot**\.

1. On the **Create Snapshot** page, select **Instance** as the resource type\.

1. Select the instance ID for which you want to create simultaneous backups for all of the attached EBS volumes\. If you want to exclude the root volume, set the **Exclude root volume** flag\. Multi\-volume snapshots support up to 40 EBS volumes per instance\. 

1. \(Optional\) Set the **Copy tags from volume** flag to automatically copy tags from the source volume to the corresponding snapshots\. This sets snapshot metadata—such as access policies, attachment information, and cost allocation—to match the source volume\.

1. Choose **Create Snapshot**\.

   During snapshot creation, the snapshots are managed together\. If one of the snapshots in the volume set fails, the other snapshots are moved to error status for the volume set\. You can monitor the progress of your snapshots with [CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\. After the snapshot creation process completes, CloudWatch generates an event that contains the status and all of the relevant snapshots details for the affected instance\.

   After the snapshots are created, each snapshot is treated as an individual snapshot\. You can perform all snapshot operations, such as restore, delete, and cross\-region/account copy, just as you would with a single volume snapshot\. You can also tag your multi\-volume snapshots as you would a single volume snapshot\. We recommend you tag your multiple volume snapshots to manage them collectively during restore, copy, or retention\.

   Multi\-volume, crash\-consistent snapshots are typically restored as a set\. It is helpflul to identify the snapshots that are in a crash\-consistent set by tagging your set with instance\-id, name, or other relevant details\. You can also set the **Copy tags from volume** flag to automatically copy tags from the source volume to the corresponding snapshots\. This helps you to set the snapshot metadata, such as access policies, attachment information, and cost allocation, to match the source volume\. 

   After it's created, a multi\-volume snapshot behaves like a normal snapshot\. You can perform all operations, such as restore, delete, and copy across Regions and accounts\. You can also tag your snapshots\. We recommend that you tag your multi\-volume snapshots to collectively manage them during restore, copy, or retention\.

   After creating your snapshots, they will appear in your EC2 console created at the exact point\-in\-time\. The snapshots are collectively managed and, therefore, if any one snapshot for the volume set fails, all of the other snapshots will display an error status\.

**To create multi\-volume snapshots using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshots.html) \(AWS CLI\)
+ [New\-EC2SnapshotBatch](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SnapshotBatch.html) \(AWS Tools for Windows PowerShell\)
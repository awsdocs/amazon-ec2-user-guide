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

You can create multi\-volume snapshots, which are point\-in\-time snapshots for all, or some, of the volumes attached to an instance\.

By default, when you create multi\-volume snapshots from an instance, Amazon EBS creates snapshots of all the volumes \(root and data \(non\-root\)\) that are attached to the instance\. However, you can choose to create snapshots of a subset of the volumes that are attached to the instance\.

You can tag your multi\-volume snapshots as you would a single volume snapshot\. We recommend you tag your multiple volume snapshots to manage them collectively during restore, copy, or retention\. You can also choose to automatically copy tags from the source volume to the corresponding snapshots\. This helps you to set the snapshot metadata, such as access policies, attachment information, and cost allocation, to match the source volume\.

After the snapshots are created, each snapshot is treated as an individual snapshot\. You can perform all snapshot operations, such as restore, delete, and copy across Regions or accounts, just as you would with a single volume snapshot\. 

Multi\-volume, crash\-consistent snapshots are typically restored as a set\. It is helpful to identify the snapshots that are in a crash\-consistent set by tagging your set with the instance ID, name, or other relevant details\. 

After creating your snapshots, they appear in your EC2 console created at the exact point\-in\-time\.

If any one snapshot for the multi\-volume snapshot set fails, all of the other snapshots display an error status and a `createSnapshots` CloudWatch event with a result of `failed` is sent to your AWS account\. For more information, see [Create snapshots \(createSnapshots\)](ebs-cloud-watch-events.md#create-snapshots-complete)\.

## Amazon Data Lifecycle Manager<a name="automate-snapshots"></a>

You can create snapshot lifecycle policies to automate the creation and retention of snapshots of individual volumes and multi\-volume snapshots of instances\. For more information, see [Amazon Data Lifecycle Manager](snapshot-lifecycle.md)\.

## Considerations<a name="ebs-create-snapshot-limitations"></a>

The following considerations apply to creating snapshots:
+ When you create a snapshot for an EBS volume that serves as a root device, we recommend that you stop the instance before taking the snapshot\.
+ You cannot create snapshots from instances for which hibernation is enabled, or from hibernated instances\. If you create a snapshot or AMI from an instance that is hibernated or has hibernation enabled, you might not be able to connect to a new instance that is launched from the AMI, or from an AMI that was created from the snapshot\. 
+ Although you can take a snapshot of a volume while a previous snapshot of that volume is in the `pending` status, having multiple `pending` snapshots of a volume can result in reduced volume performance until the snapshots complete\.
+ There is a limit of one `pending` snapshot for a single `st1` or `sc1` volume, or five `pending` snapshots for a single volume of the other volume types\. If you receive a `ConcurrentSnapshotLimitExceeded` error while trying to create multiple concurrent snapshots of the same volume, wait for one or more of the `pending` snapshots to complete before creating another snapshot of that volume\.
+ When a snapshot is created from a volume with an AWS Marketplace product code, the product code is propagated to the snapshot\.
+ When creating multi\-volume snapshot sets from instances, you can specify up to 40 data \(non\-root\) volumes to exclude\.

## Create a snapshot<a name="ebs-create-snapshot"></a>

To create a snapshot from the specified volume, use one of the following methods\.

------
#### [ Console ]

**To create a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**, **Create snapshot**\.

1. For **Resource type**, choose **Volume**\.

1. For **Volume ID**, select the volume from which to create the snapshot\.

   The **Encryption** field indicates the selected volume's encryption status\. If the selected volume is encrypted, the snapshot is automatically encrypted using the same KMS key\. If the selected volume is unencrypted, the snapshot is not encrypted\.

1. \(Optional\) For **Description**, enter a brief description for the snapshot\.

1. \(Optional\) To assign custom tags to the snapshot, in the **Tags** section, choose **Add tag**, and then enter the key\-value pair\. You can add up to 50 tags\.

1. Choose **Create snapshot**\.

------
#### [ AWS CLI ]

**To create a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [create\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) \(AWS CLI\)
+ [New\-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Snapshot.html) \(AWS Tools for Windows PowerShell\)

------

## Create a multi\-volume snapshot<a name="ebs-create-snapshots"></a>

When you create a multi\-volume snapshot set from an instance, you can choose whether to copy the tags from the source volume to the corresponding snapshot\. You can specify whether to create a snapshot of the root volume\. You can also specify whether to create snapshots of all the data \(non\-root\) volumes that are attached to the instance, or whether to create snapshots of a subset of those volumes\.

**Considerations**
+ Multi\-volume snapshots support up to 40 EBS volumes for each instance\.

To create a snapshot from the volumes of an instance, use one of the following methods\.

------
#### [ Console ]

**To create multi\-volume snapshots using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**, **Create snapshot**\.

1. For **Resource type**, choose **Instance**\.

1. For **Description**, enter a brief description for the snapshots\. This description is applied to all of the snapshots\.

1. \(Optional\) By default, Amazon EBS creates a snapshot of the instance's root volume\. If you do not want to create a snapshot of the instance's root volume, select **Exclude root volume**\.

1. \(Optional\) By default, Amazon EBS creates snapshots of all the data \(non\-root\) volumes attached to the instance\. If you want to create snapshots of a subset of the data \(non\-root\) volumes attached to the instance, select **Exclude specific data volumes**\. The **Attached data volumes** section lists all of the data volumes that are currently attached to the selected instance\.

   In the **Attached data volumes** section, select the data volumes for which you do **not** want to create snapshots\. Only the volumes that remain unselected will be included in the multi\-volume snapshot set\. You can exclude up to 40 volumes\.

1. \(Optional\) To automatically copy tags from the source volumes to the corresponding snapshots, for **Copy tags from source volume**, select **Copy tags**\. This sets snapshot metadata—such as access policies, attachment information, and cost allocation—to match the source volume\.

1. \(Optional\) To assign additional custom tags to the snapshots, in the **Tags** section, choose **Add tag**, and then enter the key\-value pair\. You can add up to 50 tags\.

1. Choose **Create snapshot**\.

   During snapshot creation, the snapshots are managed together\. If one of the snapshots in the volume set fails, the other snapshots are moved to error status for the volume set\. You can monitor the progress of your snapshots using [CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\. After the snapshot creation process completes, CloudWatch generates an event that contains the status and all of the relevant snapshot details for the affected instance\.

------
#### [ Command line ]

**AWS CLI**  
To create multi\-volume snapshots using the AWS CLI, use the [create\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshots.html) command\.

If you do not want to create a snapshot of the root volume, for `--instance-specification ExcludeBootVolume`, specify `true`\. If you do not want to create snapshots of all the data \(non\-root\) volumes attached to the instance, for `--instance-specification ExcludeDataVolumes`, specify the IDs of the data volumes for which you do not want to create snapshots\. You can specify up to 40 volumes to exclude\.

**AWS Tools for Windows PowerShell**  
To create multi\-volume snapshots using the Tools for Windows PowerShell, use the [ New\-EC2SnapshotBatch](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2SnapshotBatch.html) command\.

If you do not want to create a snapshot of the root volume, for `-InstanceSpecification_ExcludeBootVolume`, specify `1`\. If you do not want to create snapshots of all the data \(non\-root\) volumes attached to the instance, for `-InstanceSpecification_ExcludeDataVolumes`, specify the IDs of the data volumes for which you do not want to create snapshots\. You can specify up to 40 volumes to exclude\.

------

If all of the snapshots complete successfully, a `createSnapshots` CloudWatch event with a result of `succeeded` is sent to your AWS account\. If any one snapshot for the multi\-volume snapshot set fails, all of the other snapshots display an error status and a `createSnapshots` CloudWatch event with a result of `failed` is sent to your AWS account\. For more information, see [Create snapshots \(createSnapshots\)](ebs-cloud-watch-events.md#create-snapshots-complete)\.

## Work with EBS snapshots<a name="using-snapshots"></a>

You can copy snapshots, share snapshots, and create volumes from snapshots\. For more information, see the following:
+ [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md)
+ [Share an Amazon EBS snapshot](ebs-modifying-snapshot-permissions.md)
+ [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)
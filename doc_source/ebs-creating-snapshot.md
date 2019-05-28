# Creating an Amazon EBS Snapshot<a name="ebs-creating-snapshot"></a>

A point\-in\-time snapshot of an EBS volume, can be used as a baseline for new volumes or for data backup\. If you make periodic snapshots of a volume, the snapshots are incremental\-only the blocks on the device that have changed after your last snapshot are saved in the new snapshot\. Even though snapshots are saved incrementally, the snapshot deletion process is designed so that you need to retain only the most recent snapshot in order to restore the entire volume\.

Snapshots occur asynchronously; the point\-in\-time snapshot is created immediately, but the status of the snapshot is `pending` until the snapshot is complete \(when all of the modified blocks have been transferred to Amazon S3\), which can take several hours for large initial snapshots or subsequent snapshots where many blocks have changed\. While it is completing, an in\-progress snapshot is not affected by ongoing reads and writes to the volume\.

**Important**  
Although you can take a snapshot of a volume while a previous snapshot of that volume is in the `pending` status, having multiple `pending` snapshots of a volume may result in reduced volume performance until the snapshots complete\.  
There is a limit of five `pending` snapshots for a single `gp2`, `io1`, or Magnetic volume, and one `pending` snapshot for a single `st1` or `sc1` volume\. If you receive a `ConcurrentSnapshotLimitExceeded` error while trying to create multiple concurrent snapshots of the same volume, wait for one or more of the `pending` snapshots to complete before creating another snapshot of that volume\.

Snapshots that are taken from encrypted volumes are automatically encrypted\. Volumes that are created from encrypted snapshots are also automatically encrypted\. The data in your encrypted volumes and any associated snapshots is protected both at rest and in motion\. For more information, see [Amazon EBS Encryption](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html)\.

By default, only you can create volumes from snapshots that you own\. However, you can share your unencrypted snapshots with specific AWS accounts, or you can share them with the entire AWS community by making them public\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

You can share an encrypted snapshot only with specific AWS accounts\. For others to use your shared, encrypted snapshot, you must also share the CMK key that was used to encrypt it\. Users with access to your encrypted snapshot must create their own personal copy of it and then use that copy to restore the volume\. Your copy of a shared, encrypted snapshot can also be re\-encrypted with a different key\. For more information, see [Sharing an Amazon EBS Snapshot](ebs-modifying-snapshot-permissions.md)\.

**Note**  
If you copy a snapshot and encrypt it to a new CMK, a complete \(non\-incremental\) copy is always created, resulting in additional delay and storage costs\.

When a snapshot is created from a volume with an AWS Marketplace product code, the product code is propagated to the snapshot\.

You can take a snapshot of an attached volume that is in use\. However, snapshots only capture data that has been written to your Amazon EBS volume at the time the snapshot command is issued\. This might exclude any data that has been cached by any applications or the operating system\. If you can pause any file writes to the volume long enough to take a snapshot, your snapshot should be complete\. However, if you can't pause all file writes to the volume, you should unmount the volume from within the instance, issue the snapshot command, and then remount the volume to ensure a consistent and complete snapshot\. You can remount and use your volume while the snapshot status is `pending`\.

To create a snapshot for an Amazon EBS volume that serves as a root device, you should stop the instance before taking the snapshot\.

To unmount the volume in Linux, use the following command, where *device\_name* is the device name \(for example, `/dev/sdh`\):

```
umount -d device_name
```

To make snapshot management easier, you can tag your snapshots during creation or add tags afterward\. For example, you can apply tags describing the original volume from which the snapshot was created, or the device name that was used to attach the original volume to an instance\. For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.

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
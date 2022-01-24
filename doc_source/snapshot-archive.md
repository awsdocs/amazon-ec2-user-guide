# Archive Amazon EBS snapshots<a name="snapshot-archive"></a>

Amazon EBS Snapshots Archive is a new storage tier that you can use for low\-cost, long\-term storage of your rarely\-accessed snapshots that do not need frequent or fast retrieval\.

By default, when you create a snapshot, it is stored in the Amazon EBS Snapshot Standard tier \(*standard tier*\)\. Snapshots stored in the standard tier are incremental\. This means that only the blocks on the volume that have changed after your most recent snapshot are saved\.

When you archive a snapshot, the incremental snapshot is converted to a full snapshot, and it is moved from the standard tier to the Amazon EBS Snapshots Archive tier \(*archive tier*\)\. Full snapshots include all of the blocks that were written to the volume at the time when the snapshot was created\.

When you need to access an archived snapshot, you can restore it from the archive tier to the standard tier, and then use it in the same way that you use any other snapshot in your account\.

Amazon EBS Snapshots Archive offers up to 75 percent lower snapshot storage costs for snapshots that you plan to store for 90 days or longer and that you rarely need to access\.

Some typical use cases include:
+ Archiving the only snapshot of a volume, such as end\-of\-project snapshots
+ Archiving full, point\-in\-time incremental snapshots for compliance reasons\.
+ Archiving monthly, quarterly, or yearly incremental snapshots\.

**Topics**
+ [Considerations and limitations](#snapshot-archive-considerations)
+ [Pricing and billing](#snapshot-archive-pricing)
+ [Quotas](#archive-quotas)
+ [Guidelines and best practices for archiving snapshots](archiving-guidelines.md)
+ [Work with snapshot archiving](working-with-snapshot-archiving.md)
+ [Monitor snapshot archiving](monitor-snapshot-archiving.md)

## Considerations and limitations<a name="snapshot-archive-considerations"></a>

**Considerations**
+ The minimum archive period is 90 days\. If you delete or permanently restore an archived snapshot before the minimum archive period of 90 days, you are billed for remaining days in the archive tier, rounded to the nearest hour\. For more information, see [Pricing and billing](#snapshot-archive-pricing)\.
+ It can take up to 72 hours to restore an archived snapshot from the archive tier to the standard tier, depending on the size of the snapshot\.
+ Archived snapshots are always full snapshots\. A full snapshot contains all the blocks written to the volume at the time the snapshot was created\. The full snapshot will likely be larger than the incremental snapshot from which it was created\. However, if you have only one incremental snapshot of a volume on the standard tier, the size of the full snapshot in the archive tier will be the same size as the snapshot in standard tier\. This is because the first snapshot taken of a volume is always a full snapshot\.
+ When a snapshot is archived, the data of the snapshot that is referenced by other snapshots in the snapshot lineage are retained in the standard tier\. Data and storage costs associated with the referenced data that is retained on the standard tier are allocated to the next snapshot in the lineage\. This ensures that subsequent snapshots in the lineage are not affected by the archival\.
+ If you delete an archived snapshot that matches a Recycle Bin retention rule, the archived snapshot is retained in the Recycle Bin for the retention period defined in the retention rule\. To use the snapshot, you must first recover it from the Recycle Bin and then restore it from the archive tier\. For more information, see [Recycle Bin for Amazon EBS snapshots](recycle-bin.md) and [Pricing and billing](#snapshot-archive-pricing)\.

**Limitations**
+ You can archive snapshots that are in the `completed` state only\.
+ You can archive only snapshots that you own in your account\. To archive a snapshot that is shared with you, first copy the snapshot to your account and then archive the snapshot copy\.
+ You can’t archive a snapshot of the root device volume of a registered AMI\.
+ You can't archive snapshots that are associated with an Amazon EBS\-backed AMI\.
+ You can't cancel the snapshot archive or snapshot restore process after it has been started\.
+ You can't share archived snapshots\. If you archive a snapshot that you have shared with other accounts, the accounts with which the snapshot is shared lose access after the snapshot is archived\.
+ You can't copy an archived snapshot\. If you need to copy an archived snapshot, you must first restore it\.
+ You can't enable fast snapshot restore for an archived snapshot\. Fast snapshot restore is automatically disabled when a snapshot is archived\. If you need to use fast snapshot restore, you must manually enable it after restoring the snapshot\.

## Pricing and billing<a name="snapshot-archive-pricing"></a>

Archived snapshots are billed at a rate of $0\.0125 per GB\-month\. For example, if you archive a 100 GiB snapshot, you are billed $1\.25 \(100 GiB \* $0\.0125\) per month\.

Snapshot restores are billed at a rate of $0\.03 per GB of data restored\. For example, if you restore a 100 GiB snapshot from the archive tier, you are billed one time for $3 \(100 GiB \* $0\.03\)\.

After the snapshot is restored to the standard tier, the snapshot is billed at the standard rate for snapshots of $0\.05 per GB\-month\. 

For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

**Billing for the minimum archive period**  
The minimum archive period is 90 days\. If you delete or permanently restore an archived snapshot before the minimum archive period of 90 days, you are billed a pro\-rated charge equal to the archive tier storage charge for the remaining days, rounded to the nearest hour\. For example, if you delete or permanently restore an archived snapshot after 40 days, you are billed for the remaining 50 days of the minimum archive period\.

**Note**  
Temporarily restoring an archived snapshot before the minimum archive period of 90 days does not incur this charge\.

**Temporary restores**  
When you temporarily restore a snapshot, the snapshot is restored from the archive tier to the standard tier, and a copy of the snapshot remains in the archive tier\. You are billed for both the snapshot in the standard tier and the snapshot copy in the archive tier for the duration of the temporary restore period\. When the temporarily restored snapshot is removed from the standard tier, you are no longer billed for it, and you are billed for the snapshot in the archive tier only\.

**Permanent restores**  
When you permanently restore a snapshot, the snapshot is restored from the archive tier to the standard tier, and the snapshot is deleted from the archive tier\. You are billed for the snapshot in the standard tier only\.

**Deleting snapshots**  


If you delete a snapshot while it is being archived, you are billed for the snapshot data that has already been moved to the archive tier\. This data is subject to the minimum archive period of 90 days and billed accordingly upon deletion\. For example, if you archive a 100 GiB snapshot, and you delete the snapshot after only 40 GiB has been archived, you are billed $1.50 the minimum archive period of 90 days for the 40 GiB that has already been archived \($0\.0125 per GB-month \* 40 GB \* (90 days \* 24 hours\) \/ (24 hours\/day \* 30-day month\)\.

If you delete a snapshot while it is being restored from the archive tier, you are billed for the snapshot restore for the full size of the snapshot \(snapshot size \* $0\.03\)\. For example, if you restore a 100 GiB snapshot from the archive tier, and you delete the snapshot at any point before the snapshot restore completes, you are billed $3 \(100 GiB snapshot size \* $0\.03\)\.

**Recycle Bin**  
Archived snapshots are billed at the rate for archived snapshots while they are in the Recycle Bin\. Archived snapshots that are in the Recycle Bin are subject to the minimum archive period of 90 days and they are billed accordingly if they are deleted by Recycle Bin before the minimum archive period\. In other words, if a retention rule deletes an archived snapshot from the Recycle Bin before the minimum period of 90 days, you are billed for the remaining days\.

If you delete a snapshot that matches a retention rule while the snapshot is being archived, the archived snapshot is retained in the Recycle Bin for the retention period defined in the retention rule\. It is billed at the rate for archived snapshots\.

If you delete a snapshot that matches a retention rule while the snapshot is being restored, the restored snapshot is retained in the Recycle Bin for the remainder of the retention period, and billed at the standard snapshot rate\. To use the restored snapshot, you must first recover it from the Recycle Bin\.

For more information, see [Recycle Bin for Amazon EBS snapshots](recycle-bin.md)\.

**Cost tracking**  
Archived snapshots appear in the AWS Cost and Usage Report with their same resource ID and Amazon Resource Name \(ARN\)\. For more information, see the [AWS Cost and Usage Report User Guide](https://docs.aws.amazon.com/cur/latest/userguide/what-is-cur.html)\.

You can use the following usage types to identify the associated costs:
+ `SnapshotArchiveStorage` — fee for monthly data storage
+ `SnapshotArchiveRetrieval` — on\-time fee for snapshot restores
+ `SnapshotArchiveEarlyDelete` — fee for deleting or permanently restoring a snapshot before the minimum archive period \(90 days\)

## Quotas<a name="archive-quotas"></a>

This section describes the default quotas for archived and in\-progress snapshots\. 


| Quota | Default quota | 
| --- | --- | 
| Archived snapshots per volume | 25 | 
| Concurrent in\-progress snapshot archives per account | 5 | 
| Concurrent in\-progress snapshot restores per account | 5 | 

If you need more than the default limits, complete the AWS Support Center [Create case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ebs) form to request a limit increase\. 

# Recycle Bin for Amazon EBS snapshots<a name="recycle-bin"></a>

Recycle Bin for Amazon EBS snapshots is a snapshot recovery feature that enables you to restore accidentally deleted snapshots\. When using Recycle Bin, if your snapshots are deleted, they are retained in the Recycle Bin for a time period that you specify\.

You can restore a snapshot from the Recycle Bin at any time before its retention period expires\. After you restore a snapshot from the Recycle Bin, the snapshot is removed from the Recycle Bin and you can use it in the same way you use any other snapshot in your account\. If the retention period expires and the snapshot is not restored, the snapshot is permanently deleted from the Recycle Bin and is no longer available for recovery\.

Using Recycle Bin ensures business continuity by protecting your business\-critical data backups against accidental deletion\.

**Topics**
+ [How does it work?](#recycle-bin-concepts)
+ [Considerations](#recycle-bin-considerations)
+ [Quotas](#recycle-bin-quotas)
+ [Related services](#recycle-bin-integrations)
+ [Pricing](#recycle-bin-pricing)
+ [Required permissions](recycle-bin-perms.md)
+ [Work with retention rules](recycle-bin-working-with-rules.md)
+ [Work with snapshots in the Recycle Bin](recycle-bin-snapshots.md)
+ [Monitoring Recycle Bin using AWS CloudTrail](recycle-bin-ct.md)

## How does it work?<a name="recycle-bin-concepts"></a>

To enable and use Recycle Bin, you must create *retention rules* in the AWS Regions in which you want to protect snapshots\. Retention rules specify the following:
+ The snapshots that you want to retain in the Recycle Bin when they are deleted\.
+ The retention period for which to retain snapshots in the Recycle Bin after deletion\.

With Recycle Bin, you can create two types of retention rules:
+ **Tag\-level retention rules** — These retention rules use resource tags to identify the snapshots that are to be retained in the Recycle Bin\. For each retention rule, you specify one or more tag key and value pairs\. Snapshots that are tagged with at least one of the tag key and value pairs that are specified in the retention rule are automatically retained in the Recycle Bin upon deletion\. Use this type of retention rule if you want to protect specific snapshots in your account based on their tags\.
+ **Region\-level retention rules** — These retention rules do not have any resource tags specified\. They apply to all of the snapshots in the Region in which they are created, even if the snapshots are not tagged\. Use this type of retention rule if you want to protect all of your snapshots in a specific Region\.

While a snapshot is in the Recycle Bin, you have the ability to restore it for use at any time\.

The snapshot remains in the Recycle Bin until one of the following happens:
+ You manually restore it for use\. When you restore a snapshot from the Recycle Bin, the snapshot is removed from the Recycle Bin and it immediately becomes available for use as a regular snapshot\. You can use restored snapshots in the same way as any other snapshot in your account\.
+ The retention period expires\. If the retention period expires, and the snapshot has not been restored from the Recycle Bin, the snapshot is permanently deleted from the Recycle Bin and it can no longer be viewed or restored\.

## Considerations<a name="recycle-bin-considerations"></a>

Keep the following in mind when working with the Recycle Bin:
+ If a snapshot is enabled for fast snapshot restore when it is deleted, fast snapshot restore is automatically disabled shortly after the snapshot is sent to the Recycle Bin\. 
  + If you restore the snapshot before fast snapshot restore is disabled for the snapshot, it remains enabled\.
  + If you restore the snapshot, after fast snapshot restore has been disabled, it remains disabled\. If needed, you must manually re\-enable fast snapshot restore\.
+ If a snapshot is shared when is deleted, it is automatically unshared when it is sent to the Recycle Bin\. If you restore the snapshot, all of the previous sharing permissions are automatically restored\.
+ If a snapshot matches more than one retention rule upon deletion, then the retention rule with the longest retention period takes precedence\. If a snapshot matches a Region\-level rule and a tag\-level rule, then the tag\-level rule takes precedence\.
+ You can't manually delete a snapshot from the Recycle Bin\. The snapshot will be automatically deleted when its retention period expires\.
+ While a snapshot is in the Recycle Bin, you can only view it, restore it, or modify its tags\. To use the snapshot in any other way, you must first restore it\.

## Quotas<a name="recycle-bin-quotas"></a>

The following quotas apply to Recycle Bin\.


| Quota | Default quota | 
| --- | --- | 
| Retention rules per Region | 250 | 
| Tag key and value pairs per retention rule | 50 | 

## Related services<a name="recycle-bin-integrations"></a>

Recycle Bin works with the following services:
+ **AWS CloudTrail** — Enables you to record events that occur in Recycle Bin\. For more information, see [Monitoring Recycle Bin using AWS CloudTrail](recycle-bin-ct.md)\.

## Pricing<a name="recycle-bin-pricing"></a>

Snapshots in the Recycle Bin are billed at the same rate as regular snapshots in your account\. There are no additional charges for using Recycle Bin and retention rules\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

**Note**  
Some snapshots might still appear in the Recycle Bin console or in the AWS CLI and API output for a short period after their retention periods have expired and they have been permanently deleted\. You are not billed for these snapshots\. Billing stops as soon as the retention period expires\.

You can use the following AWS generated cost allocation tags for cost tracking and allocation purposes when using AWS Billing and Cost Management\.
+ Key: `aws:recycle-bin:resource-in-bin`
+ Value: `true`

For more information, see [AWS\-Generated Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.html) in the *AWS Billing and Cost Management User Guide*\.
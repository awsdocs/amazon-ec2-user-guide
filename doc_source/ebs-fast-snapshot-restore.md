# Amazon EBS Fast Snapshot Restore<a name="ebs-fast-snapshot-restore"></a>

Amazon EBS fast snapshot restore enables you to create a volume from a snapshot that is fully\-initialized at creation\. This eliminates the latency of I/O operations on a block when it is accessed for the first time\. Volumes created using fast snapshot restore instantly deliver all of their provisioned performance\.

To use fast snapshot restore, enable it for specific snapshots in specific Availability Zones\. You can enable fast snapshot restore for up to five snapshots per Region\.

**Topics**
+ [Fast Snapshot Restore States](#fsr-states)
+ [Volume Creation Credits](#volume-creation-credits)
+ [Managing Fast Snapshot Restores](#manage-fsr)

## Fast Snapshot Restore States<a name="fsr-states"></a>

After you enable fast snapshot restore for a snapshot, it can be in one of the following states\.
+ `enabling` — A request was made to enable fast snapshot restore\.
+ `optimizing` — Fast snapshot restore is being enabled\. It takes 60 minutes per TiB to optimize a snapshot\.
+ `enabled` — Fast snapshot restore is enabled\.
+ `disabling` — A request was made to disable fast snapshot restore or a request to enable fast snapshot restore failed\.
+ `disabled` — Fast snapshot restore is disabled\. You can enable fast snapshot restore again as needed\.

## Volume Creation Credits<a name="volume-creation-credits"></a>

The number of volumes that receive the full performance benefit of fast snapshot restore is determined by the volume creation credits for the snapshot\. There is one credit bucket per snapshot per Availability Zone\. Each volume that you create from a snapshot with fast snapshot restore enabled consumes one credit from the credit bucket\.

The size of a credit bucket depends on the size of the snapshot, not the size of the volumes created from the snapshot\. The size of the credit bucket for each snapshot is calculated as follows:

```
MAX (1, MIN (10, FLOOR(1024/snapshot_size_gib)))
```

As you consume credits, the credit bucket is refilled over time\. The refill rate for each credit bucket is calculated as follows:

```
MIN (10, 1024/snapshot_size_gib)
```

For example, if you enable fast snapshot restore for a snapshot with a size of 100 GiB, the maximum size of its credit bucket is 10 credits and the refill rate is 10 credits per hour\. When the credit bucket is full, you can create 10 initialized volumes from this snapshot simultaneously\.

You can use Cloudwatch metrics to monitor the size of your credit buckets and the number of credits available in each bucket\. For more information, see [Fast Snapshot Restore Metrics](using_cloudwatch_ebs.md#fast-snapshot-restore-metrics)\.

After you create a volume from a snapshot with fast snapshot restore enabled, you can describe the volume using [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) and check the `fastRestored` field in the output to determine whether the volume was created as an initialized volume using fast snapshot restore\.

## Managing Fast Snapshot Restores<a name="manage-fsr"></a>

Use the following procedure to enable fast snapshot restores for a snapshot\. You must own the snapshot\. You cannot enable fast snapshot restore on a snapshot that was shared with you\.

**To manage fast snapshot restores**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot\.

1. Choose **Actions**, **Manage Fast Snapshot Restore**\.

1. Select or deselect Availability Zones, and then choose **Save**\.

1. To track the state of fast snapshot restore as it is enabled, see **Fast Snapshot Restore** on the **Description** tab\.

**To manage fast snapshot restore using the AWS CLI**
+ [enable\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-snapshot-restores.html)
+ [disable\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-fast-snapshot-restores.html)
+ [describe\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-snapshot-restores.html)
+ [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html)
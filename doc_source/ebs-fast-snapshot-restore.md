# Amazon EBS Fast Snapshot Restore<a name="ebs-fast-snapshot-restore"></a>

Amazon EBS fast snapshot restore enables you to create a volume from a snapshot that is fully\-initialized at creation\. This eliminates the latency of I/O operations on a block when it is accessed for the first time\. Volumes created using fast snapshot restore instantly deliver all of their provisioned performance\.

To get started, enable fast snapshot restore for specific snapshots in specific Availability Zones\. Each snapshot and Availability Zone pair refers to one fast snapshot restore\. You can enable up to 50 fast snapshot restores per Region\. When you create a volume from one of these snapshots in one of its enabled Availability Zones, the volume is restored using fast snapshot restore\.

**Topics**
+ [Fast Snapshot Restore States](#fsr-states)
+ [Volume Creation Credits](#volume-creation-credits)
+ [Managing Fast Snapshot Restore](#manage-fsr)
+ [View Snapshots with Fast Snapshot Restore Enabled](#view-fsr-enabled-snapshots)
+ [View Volumes Restored Using Fast Snapshot Restore](#view-fast-restored-volumes)

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

## Managing Fast Snapshot Restore<a name="manage-fsr"></a>

Use the following procedure to enable fast snapshot restore for a snapshot\. You must own the snapshot\. You cannot enable fast snapshot restore on a snapshot that was shared with you\.

**To enable or disable fast snapshot restore**

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

## View Snapshots with Fast Snapshot Restore Enabled<a name="view-fsr-enabled-snapshots"></a>

Use the following procedure to view the state of fast snapshot restore for a snapshot\.

**To view the state of fast snapshot restore using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot\.

1. On the **Description** tab, see **Fast Snapshot Restore**, which indicates the state of fast snapshot restore\. For example, "2 Availability Zones optimizing" or "2 Availability Zones enabled"\.

**To view snapshots with fast snapshot restore enabled using the AWS CLI**  
Use the [describe\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-snapshot-restores.html) command to describe the snapshots that are enabled for fast snapshot restore\.

```
aws ec2 describe-fast-snapshot-restores --filters Name=state,Values=enabled
```

The following is example output\.

```
{
    "FastSnapshotRestores": [
        {
            "SnapshotId": "snap-0e946653493cb0447",
            "AvailabilityZone": "us-east-2a",
            "State": "enabled",
            "StateTransitionReason": "Client.UserInitiated - Lifecycle state transition",
            "OwnerId": "123456789012",
            "EnablingTime": "2020-01-25T23:57:49.596Z",
            "OptimizingTime": "2020-01-25T23:58:25.573Z",
            "EnabledTime": "2020-01-25T23:59:29.852Z"
        },
        {
            "SnapshotId": "snap-0e946653493cb0447",
            "AvailabilityZone": "us-east-2b",
            "State": "enabled",
            "StateTransitionReason": "Client.UserInitiated - Lifecycle state transition",
            "OwnerId": "123456789012",
            "EnablingTime": "2020-01-25T23:57:49.596Z",
            "OptimizingTime": "2020-01-25T23:58:25.573Z",
            "EnabledTime": "2020-01-25T23:59:29.852Z"
        }
    ]
}
```

## View Volumes Restored Using Fast Snapshot Restore<a name="view-fast-restored-volumes"></a>

When you create a volume from a snapshot that is enabled for fast snapshot restore in the Availability Zone for the volume, it is restored using fast snapshot restore\.

Use the [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) command to view volumes that were created from a snapshot that is enabled for fast snapshot restore\.

```
aws ec2 describe-volumes --filters Name=fast-restored,Values=true
```

The following is example output\.

```
{
    "Volumes": [
        {
            "Attachments": [],
            "AvailabilityZone": "us-east-2a",
            "CreateTime": "2020-01-26T00:34:11.093Z",
            "Encrypted": true,
            "KmsKeyId": "arn:aws:kms:us-west-2:123456789012:key/8c5b2c63-b9bc-45a3-a87a-5513e232e843",
            "Size": 20,
            "SnapshotId": "snap-0e946653493cb0447",
            "State": "available",
            "VolumeId": "vol-0d371921d4ca797b0",
            "Iops": 100,
            "VolumeType": "gp2",
            "FastRestored": true
        }
    ]
}
```
# Amazon EBS fast snapshot restore<a name="ebs-fast-snapshot-restore"></a>

Amazon EBS fast snapshot restore \(FSR\) enables you to create a volume from a snapshot that is fully initialized at creation\. This eliminates the latency of I/O operations on a block when it is accessed for the first time\. Volumes that are created using fast snapshot restore instantly deliver all of their provisioned performance\.

To get started, enable fast snapshot restore for specific snapshots in specific Availability Zones\. Each snapshot and Availability Zone pair refers to one fast snapshot restore\. When you create a volume from one of these snapshots in one of its enabled Availability Zones, the volume is restored using fast snapshot restore\. 

Fast snapshot restore must be explicitly enabled on a per\-snapshot basis\. If you create a new snapshot from a volume that was restored from a fast snapshot restore\-enabled snapshot, the new snapshot is not automatically enabled for fast snapshot restore\. You must explicitly enable it for the new snapshot\.

The number of volumes that you can restore with the full performance benefit of fast snapshot restore is determined by volume creation credits for the snapshot\. For more information see [Volume creation credits](#volume-creation-credits)\.

You can enable fast snapshot restore for snapshots that you own and for public and private snapshots that are shared with you\.

**Topics**
+ [Volume creation credits](#volume-creation-credits)
+ [Manage fast snapshot restore](#manage-fsr)
+ [Monitor fast snapshot restore](#monitor)
+ [Fast snapshot restore quotas](#limits)
+ [Pricing and Billing](#fsr-pricing)

## Volume creation credits<a name="volume-creation-credits"></a>

The number of volumes that receive the full performance benefit of fast snapshot restore is determined by the volume creation credits for the snapshot\. There is one credit bucket per snapshot per Availability Zone\. Each volume that you create from a snapshot with fast snapshot restore enabled consumes one credit from the credit bucket\. You must have at least one credit in the bucket to create an intialized volume from the snapshot\. If you create a volume but there is less than one credit in the bucket, the volume is created without benefit of fast snapshot restore\.

When you enable fast snapshot restore for a snapshot that is shared with you, you get a separate credit bucket for the shared snapshot in your account\. If you create volumes from the shared snapshot, the credits are consumed from your credit bucket; they are not consumed from the snapshot owner's credit bucket\.

The size of a credit bucket and the rate at which it refills depends on the size of the snapshot, not the size of the volumes created from the snapshot\.

When you enable fast snapshot restore for a snapshot, the credit bucket starts with zero credits, and it gets filled at a set rate until it reaches its maximum credit capacity\. Also, as you consume credits, the credit bucket is refilled over time until it reaches its maximum credit capacity\.

 The fill rate for a credit bucket is calculated as follows:

```
MIN (10, (1024 ÷ snapshot_size_gib))
```

And the size of the credit bucket is calculated as follows:

```
MAX (1, MIN (10, (1024 ÷ snapshot_size_gib)))
```

**For example**, if you enable fast snapshot restore for a snapshot with a size of `128 GiB`, the fill rate is `0.1333` credits per minute\.

```
MIN (10, (1024 ÷ 128))
 = MIN (10, 8)        
 = 8 credits per hour
 = 0.1333 credits per minute
```

And the maximum size of the credit bucket is `8` credits\.

```
MAX (1, MIN (10, (1024 ÷ 128)))      
 = MAX (1, MIN (10, 8))
 = MAX (1, 8)
 = 8 credits
```

In this example, when you enable fast snapshot restore, the credit bucket starts with zero credits\. After 8 minutes, the credit bucket has enough credits to create one initialized volume \(`0.1333 credits × 8 minutes = 1.066 credits`\)\. When the credit bucket is full, you can create 8 initialized volumes simultaneously \(8 credits\)\. When the bucket is below its maximum capacity, it refills with `0.1333` credits per minute\.

You can use Cloudwatch metrics to monitor the size of your credit buckets and the number of credits available in each bucket\. For more information, see [Fast snapshot restore metrics](using_cloudwatch_ebs.md#fast-snapshot-restore-metrics)\.

After you create a volume from a snapshot with fast snapshot restore enabled, you can describe the volume using [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) and check the `fastRestored` field in the output to determine whether the volume was created as an initialized volume using fast snapshot restore\.

## Manage fast snapshot restore<a name="manage-fsr"></a>

**Topics**
+ [Enable or disable fast snapshot restore](#manage-fsr-enable)
+ [View the fast snapshot restore state for a snapshot](#view-fsr-enabled-snapshots)
+ [View volumes restored using fast snapshot restore](#view-fast-restored-volumes)

### Enable or disable fast snapshot restore<a name="manage-fsr-enable"></a>

Fast snapshot restore is disabled for a snapshot by default\. You can enable or disable fast snapshot restore for snapshots that you own and for snapshots that are shared with you\. When you enable or disable fast snapshot restore for a snapshot, the changes apply to your account only\.

**Note**  
When you enable fast snapshot restore for a snapshot, your account is billed for each minute that fast snapshot restore is enabled in a particular Availability Zone\. Charges are pro\-rated and have a minimum of one hour\.

When you delete a snapshot that you own, fast snapshot restore is automatically disabled for that snapshot in your account\. If you enabled fast snapshot restore for a snapshot that is shared with you, and the snapshot owner deletes or unshares it, fast snapshot restore is automatically disabled for the shared snapshot in your account\.

If you enabled fast snapshot restore for a snapshot that is shared with you, and it has been encrypted using a custom CMK, fast snapshot restore is not automatically disabled for the snapshot when the snapshot owner revokes your access to the custom CMK\. You must manually disable fast snapshot restore for that snapshot\.

Use one of the following methods to enable or disable fast snapshot restore for a snapshot that you own or for a snapshot that is shared with you\. 

------
#### [ New console ]

**To enable or disable fast snapshot restore**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot, and choose **Actions**, **Manage fast snapshot restore**\.

1. The **Fast snapshot restore settings** section lists all of the Availability Zones, Local Zones, and Wavelength Zones in which you can enable fast snapshot restore for the selected snapshot\. The **Current status** volume indicates whether fast snapshot restore is current enabled or disabled for each zone\.

   To enable fast snapshot restore in a zone where it is currently disabled, select the zone, choose **Enable**, and then to confirm, choose **Enable**\.

   To disable fast snapshot restore in a zone where it is currently enabled, select the zone, and then choose **Disable**\.

1. After you have made the required changes, choose **Close**\.

------
#### [ Old console ]

**To enable or disable fast snapshot restore**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot\.

1. Choose **Actions**, **Manage Fast Snapshot Restore**\.

1. Select or deselect Availability Zones, and then choose **Save**\.

1. To track the state of fast snapshot restore as it is enabled, see **Fast Snapshot Restore** on the **Description** tab\.

------
#### [ AWS CLI ]

**To manage fast snapshot restore using the AWS CLI**
+ [enable\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-fast-snapshot-restores.html)
+ [disable\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/disable-fast-snapshot-restores.html)
+ [describe\-fast\-snapshot\-restores](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-fast-snapshot-restores.html)

------

**Note**  
After you enable fast snapshot restore for a snapshot, it enters the `optimizing` state\. Snapshots that are in the `optimizing` state provide some performance benefits when using them to restore volumes\. They start to provide the full performance benefits of fast snapshot restore only after they enter the `enabled` state\.

### View the fast snapshot restore state for a snapshot<a name="view-fsr-enabled-snapshots"></a>

Fast snapshot restore for a snapshot can be in one of the following states\.
+ `enabling` — A request was made to enable fast snapshot restore\.
+ `optimizing` — Fast snapshot restore is being enabled\. It takes 60 minutes per TiB to optimize a snapshot\. Snapshots in this state offer some performance benefit when restoring volumes\.
+ `enabled` — Fast snapshot restore is enabled\. Snapshots in this state offer the full performance benefit when restoring volumes\.
+ `disabling` — A request was made to disable fast snapshot restore, or a request to enable fast snapshot restore failed\.
+ `disabled` — Fast snapshot restore is disabled\. You can enable fast snapshot restore again as needed\.

Use one of the following methods to view the state of fast snapshot restore for a snapshot that you own or for a snapshot that is shared with you\.

------
#### [ New console ]

**To view the state of fast snapshot restore using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot\.

1. On the **Details** tab, **Fast snapshot restore**, indicates the state of fast snapshot restore\.

------
#### [ Old console ]

**To view the state of fast snapshot restore using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot\.

1. On the **Description** tab, see **Fast Snapshot Restore**, which indicates the state of fast snapshot restore\. For example, it might show a state of "2 Availability Zones optimizing" or "2 Availability Zones enabled"\.

------
#### [ AWS CLI ]

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

------

### View volumes restored using fast snapshot restore<a name="view-fast-restored-volumes"></a>

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

## Monitor fast snapshot restore<a name="monitor"></a>

Amazon EBS emits Amazon CloudWatch events when the fast snapshot restore state for a snapshot changes\. For more information, see [EBS fast snapshot restore events](ebs-cloud-watch-events.md#fast-snapshot-restore-events)\. 

## Fast snapshot restore quotas<a name="limits"></a>

You can enable up to 50 snapshots for fast snapshot restore per Region\. The quota applies to snapshots that you own and snapshots that are shared with you\. If you enable fast snapshot restore for a snapshot that is shared with you, it counts towards your fast snapshot restore quota\. It does not count towards the snapshot owner's fast snapshot restore quota\.

## Pricing and Billing<a name="fsr-pricing"></a>

You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\. 

For example, if you enable fast snapshot restore for one snapshot in `US-East-1a` for one month \(30 days\), you are billed **$540** \(`1` snapshot x `1` AZ x `720` hours x `$0.75` per hour\)\. If you enable fast snapshot restore for two snapshots in `us-east-1a`, `us-east-1b`, and `us-east-1c` for the same period, you are billed **$3240** \(`2` snapshots x `3` AZs x `720` hours x `$0.75` per hour\)\.

If you enable fast snapshot restore for a public or private snapshot that is shared with you, your account is billed; the snapshot owner is not billed\. When a snapshot that is shared with you is deleted or unshared by the snapshot owner, fast snapshot restore is disabled for the snapshot in your account and billing is stopped\. 

For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.
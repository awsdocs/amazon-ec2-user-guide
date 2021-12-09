# Work with snapshot archiving<a name="working-with-snapshot-archiving"></a>

**Topics**
+ [Archive a snapshot](#archive-snapshot)
+ [Restore an archived snapshot](#restore-archived-snapshot)
+ [Modify the restore period or restore type for a temporarily restored snapshot](#modify-temp-restore-period)
+ [View archived snapshots](#view-archived-snapshot)

## Archive a snapshot<a name="archive-snapshot"></a>

You can archive any snapshot that is in the `completed` state and that you own in your account\. You can't archive snapshots that are in the `pending` or `error` states, or snapshots that are shared with you\. For more information, see [Considerations and limitations](snapshot-archive.md#snapshot-archive-considerations)\.

Archived snapshots retain their snapshot ID, encryption status, AWS Identity and Access Management \(IAM\) permissions, owner information, and resource tags\. However, fast snapshot restore and snapshot sharing are automatically disabled after the snapshot is archived\.

You can continue to use the snapshot while the archive is in process\. As soon as the snapshot tiering status reaches the `archival-complete` state, you can no longer use the snapshot\.

You can archive a snapshot using one of the following methods\.

------
#### [ Console ]

**To archive a snapshot**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. In the list of snapshots, select the snapshot to archive and then choose **Actions**, **Archive snapshot**\.

1. To confirm, choose **Archive snapshot**\.

------
#### [ AWS CLI ]

**To archive a snapshot**  
Use the [ modify\-snapshot\-tier](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-snapshot-tier.html) AWS CLI command\. For `--snapshot-id`, specify the ID of the snapshot to archive\. For `--storage-tier`, specify `archive`\.

```
$ aws ec2 modify-snapshot-tier \
--snapshot-id snapshot_id \
--storage-tier archive
```

For example, the following command archives snapshot `snap-01234567890abcedf`\.

```
$ aws ec2 modify-snapshot-tier \
--snapshot-id snap-01234567890abcedf \
--storage-tier archive
```

The following is the command output\. The `TieringStartTime` response parameter indicates the date and time at which the archive process was started, in UTC time format \(YYYY\-MM\-DDTHH:MM:SSZ\)\.

```
{
    "SnapshotId": "snap-01234567890abcedf",
    "TieringStartTime": "2021-09-15T16:44:37.574Z"
}
```

------

## Restore an archived snapshot<a name="restore-archived-snapshot"></a>

Before you can use an archived snapshot, you must first restore it to the standard tier\. The restored snapshot has the same snapshot ID, encryption status, IAM permissions, owner information, and resource tags that it had before it was archived\. After it is restored, you can use it in the same way that you use any other snapshot in your account\. The restored snapshot is always a full snapshot\.

When you restore a snapshot, you can choose to restore it **permanently** or **temporarily**\.

If you restore a snapshot permanently, the snapshot is moved from the archive tier to the standard tier permanently\. The snapshot remains restored and ready for use until you manually re\-archive it or you manually delete it\. When you permanently restore a snapshot, the snapshot is removed from the archive tier\.

If you restore a snapshot temporarily, the snapshot is copied from the archive tier to the standard tier for a restore period that you specify\. The snapshot remains restored and ready for use for the restore period only\. During the restore period, a copy of the snapshot remains in the archive tier\. After the period expires, the snapshot is automatically removed from the standard tier\. You can increase or decrease the restore period or change the restore type to permanent at any time during the restore period\. For more information, see [Modify the restore period or restore type for a temporarily restored snapshot](#modify-temp-restore-period)\.

You can restore an archived snapshot using one of the following methods\.

------
#### [ Console ]

**To restore a snapshot from the archive**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. In the list of snapshots, select the archived snapshot to restore, and then choose **Actions**, **Restore snapshot from archive**\.

1. Specify the type of restore to perform\. For **Restore type**, do one of the following:
   + To restore the snapshot permanently, select **Permanent**\.
   + To restore the snapshot temporarily, select **Temporary**, and then for **Temporary restore period**, enter the number of days for which to restore the snapshot\.

1. To confirm, choose **Restore snapshot**\.

------
#### [ AWS CLI ]

**To permanently restore an archived snapshot**  
Use the [ restore\-snapshot\-tier](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-snapshot-tier.html) AWS CLI command\. For `--snapshot-id`, specify the ID of the snapshot to restore, and include the `--permanent-restore` option\.

```
$ aws ec2 restore-snapshot-tier \
--snapshot-id snapshot_id \
--permanent-restore
```

For example, the following command permanently restores snapshot `snap-01234567890abcedf`\.

```
$ aws ec2 restore-snapshot-tier \
--snapshot-id snap-01234567890abcedf \
--permanent-restore
```

The following is the command output\.

```
{
    "SnapshotId": "snap-01234567890abcedf",
    "IsPermanentRestore": true
}
```

**To temporarily restore an archived snapshot**  
Use the [ restore\-snapshot\-tier](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-snapshot-tier.html) AWS CLI command\. Omit the `--permanent-restore` option\. For `--snapshot-id`, specify the ID of the snapshot to restore, and for `--temporary-restore-days`, specify the number of days for which to restore the snapshot\.

`--temporary-restore-days` must be specified in days\. The allowed range is `1` \- `180`\. If you do not specify a value, it defaults to `1` day\.

```
$ aws ec2 restore-snapshot-tier \
--snapshot-id snapshot_id \
--temporary-restore-days number_of_days
```

For example, the following command temporarily restores snapshot `snap-01234567890abcedf` for a restore period of `5` days\.

```
$ aws ec2 restore-snapshot-tier \
--snapshot-id snap-01234567890abcedf \
--temporary-restore-days 5
```

The following is the command output\.

```
{
    "SnapshotId": "snap-01234567890abcedf",
    "RestoreDuration": 5,
    "IsPermanentRestore": false
}
```

------

## Modify the restore period or restore type for a temporarily restored snapshot<a name="modify-temp-restore-period"></a>

When you restore a snapshot temporarily, you must specify the number of days for which the snapshot is to remain restored in your account\. After the restore period expires, the snapshot is automatically removed from the standard tier\.

You can change the restore period for a temporarily restored snapshot at any time\.

You can choose to either increase or decrease the restore period, or you can change the restore type from temporary to permanent\.

If you change the restore period, the new restore period is effective from the current date\. For example, if you specify a new restore period of `5` days, the snapshot will remain restored for five days from the current date\.

**Note**  
You can end a temporary restore early by setting the restore period to 1 day\.

If you change the restore type from temporary to permanent, the snapshot copy is deleted from the archive tier, and the snapshot remains available in your account until you manually re\-archive it or delete it\.

You can modify the restore period for a snapshot using one of the following methods\.

------
#### [ Console ]

**To modify the restore period or restore type**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. In the list of snapshots, select the snapshot that you previously temporarily restored, and then choose **Actions**, **Restore snapshot from archive**\.

1. For **Restore type**, do one of the following:
   + To change the restore type from temporary to permanent, select **Permanent**\.
   + To increase or decrease the restore period, keep **Temporary**, and then for **Temporary restore period**, enter the new restore period in days\. 

1. To confirm, choose **Restore snapshot**\.

------
#### [ AWS CLI ]

**To modify the restore period or change the restore type**  
Use the [ restore\-snapshot\-tier](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-snapshot-tier.html) AWS CLI command\. For `--snapshot-id`, specify the ID of the snapshot that you previously temporarily restored\. To change the restore type from temporary to permanent, specify `--permanent-restore` and omit `--temporary-restore-days`\. To increase or decrease the restore period, omit `--permanent-restore` and for `--temporary-restore-days`, specify the new restore period in days\.

**Example: Increase or decrease the restore period**  
The following command changes the restore period for snapshot `snap-01234567890abcedf` to `10` days\.

```
$ aws ec2 restore-snapshot-tier \
--snapshot-id snap-01234567890abcedf 
--temporary-restore-days 10
```

The following is the command output\.

```
{
    "SnapshotId": "snap-01234567890abcedf",
    "RestoreDuration": 10,
    "IsPermanentRestore": false
}
```

**Example: Change restore type to permanent**  
The following command changes the restore type for snapshot `snap-01234567890abcedf` from temporary to permanent\.

```
$ aws ec2 restore-snapshot-tier \
--snapshot-id snap-01234567890abcedf 
--permanent-restore
```

The following is the command output\.

```
{
    "SnapshotId": "snap-01234567890abcedf",
    "IsPermanentRestore": true
}
```

------

## View archived snapshots<a name="view-archived-snapshot"></a>

You can view storage tier information for snapshots using one of the following methods\.

------
#### [ Console ]

**To view storage tier information for a snapshot**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. In the list of snapshots, select the snapshot and choose the **Storage tier** tab\.

   The tab provides the following information:
   + **Last tier change started on** — The date and time when the last archive or restore was started\.
   + **Tier change progress** — The progress of the last archive or restore action, as a percentage\.
   + **Storage tier** — The storage tier for the snapshot\. Always `archive` for archived snapshots, and `standard` for snapshots stored on the standard tier, including temporarily restored snapshots\.
   + **Tiering status** — The status of the last archive or restore action\.
   + **Archive completed on** — The date and time when the archive completed\.
   + **Temporary restore expires on** — The date and time when a temporarily restored snapshot is set to expire\.

------
#### [ AWS CLI ]

**To view archival information about an archived snapshot**  
Use the [ describe\-snapshot\-tier\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshot-tier-status.html) AWS CLI command\. Specify the `snapshot-id` filter, and for the filter value, specify the snapshot ID\. Alternatively, to view all archived snapshots, omit the filter\.

```
$ aws ec2 describe-snapshot-tier-status --filters "Name=snapshot-id, Values=snapshot_id"
```

The output includes the following response parameters:
+ `Status` — The status of the snapshot\. Always `completed` for archived snapshots\. Only snapshots that are in the `completed` state can be archived\.
+ `LastTieringStartTime` — The date and time that the archival process started, in UTC time format \(YYYY\-MM\-DDTHH:MM:SSZ\)\.
+ `LastTieringOperationState` — The current state of the archival process\. Possible states include: `archival-in-progress` \| `archival-complete` \| `archival-failed` \| `temporary-restore-in-progress` \| `temporary-restore-complete` \| `temporary-restore-failed` \| `permanent-restore-in-progress` \| `permanent-restore-complete` \| `permanent-restore-failed`
+ `LastTieringProgress` — The progress of the snapshot archival process, as a percentage\.
+ `StorageTier` — The storage tier for the snapshot\. Always `archive` for archived snapshots, and `standard` for snapshots stored on the standard tier, including temporarily restored snapshots\.
+ `ArchivalCompleteTime` — The date and time that the archival process completed, in UTC time format \(YYYY\-MM\-DDTHH:MM:SSZ\)\.

**Example**  
The following command displays information about snapshot `snap-01234567890abcedf`\.

```
$ aws ec2 describe-snapshot-tier-status --filters "Name=snapshot-id, Values=snap-01234567890abcedf"
```

The following is the command output\.

```
{
    "SnapshotTierStatuses": [
        {
            "Status": "completed",
            "ArchivalCompleteTime": "2021-09-15T17:33:16.147Z",
            "LastTieringProgress": 100,
            "Tags": [],
            "VolumeId": "vol-01234567890abcedf",
            "LastTieringOperationState": "archival-completed",
            "StorageTier": "archive",
            "OwnerId": "123456789012",
            "SnapshotId": "snap-01234567890abcedf",
            "LastTieringStartTime": "2021-09-15T16:44:37.574Z"
        }
    ]
}
```

**To view archived and standard tier snapshots**  
Use the [describe\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshot.html) AWS CLI command\. For `--snapshot-ids`, specify the ID of the snapshot view\.

```
$ aws ec2 describe-snapshots --snapshot-ids snapshot_id
```

For example, the following command displays information about snapshot `snap-01234567890abcedf`\.

```
$ aws ec2 describe-snapshots --snapshot-ids snap-01234567890abcedf
```

The following is the command output\. The `StorageTier` response parameter indicates whether the snapshot is currently archived\. `archive` indicates that the snapshot is currently archived and stored in the archive tier, and `standard` indicates that the snapshot is currently not archived and that it is stored in the standard tier\.

In the following example output, only `Snap A` is archived\. `Snap B` and `Snap C` are not archived\.

Additionally, the `RestoreExpiryTime` response parameter is returned only for snapshots that are temporarily restored from the archive\. It indicates when temporarily restored snapshots are to be automatically removed from the standard tier\. It is **not** returned for snapshots that are permanently restored\.

In the following example output, `Snap C` is temporarily restored, and it will be automatically removed from the standard tier at 2021\-09\-19T21:00:00\.000Z \(September 19, 2021 at 21:00 UTC\)\.

```
{
    "Snapshots": [
        {
            "Description": "Snap A",
            "Encrypted": false,
            "VolumeId": "vol-01234567890aaaaaa",
            "State": "completed",
            "VolumeSize": 8,
            "StartTime": "2021-09-07T21:00:00.000Z",
            "Progress": "100%",
            "OwnerId": "123456789012",
            "SnapshotId": "snap-01234567890aaaaaa",
            "StorageTier": "archive",
            "Tags": []
        },
        {
            "Description": "Snap B",
            "Encrypted": false,
            "VolumeId": "vol-09876543210bbbbbb",
            "State": "completed",
            "VolumeSize": 10,
            "StartTime": "2021-09-14T21:00:00.000Z",
            "Progress": "100%",
            "OwnerId": "123456789012",
            "SnapshotId": "snap-09876543210bbbbbb",
            "StorageTier": "standard",           
            "RestoreExpiryTime": "2019-09-19T21:00:00.000Z",
            "Tags": []
        },
        {
            "Description": "Snap C",
            "Encrypted": false,
            "VolumeId": "vol-054321543210cccccc",
            "State": "completed",
            "VolumeSize": 12,
            "StartTime": "2021-08-01T21:00:00.000Z",
            "Progress": "100%",
            "OwnerId": "123456789012",
            "SnapshotId": "snap-054321543210cccccc",
            "StorageTier": "standard",
            "Tags": []
        }
    ]
}
```

**To view only snapshots that are stored in the archive tier or the standard tier**  
Use the [ describe\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshot.html) AWS CLI command\. Include the `--filter` option, for the filter name, specify `storage-tier`, and for the filter value specify either `archive` or `standard`\.

```
$ aws ec2 describe-snapshots --filters "Name=storage-tier,Values=archive|standard"
```

For example, the following command displays only archived snapshots\.

```
$ aws ec2 describe-snapshots --filters "Name=storage-tier,Values=archive"
```

------
# Recover snapshots from the Recycle Bin<a name="recycle-bin-working-with-snaps"></a>

Recycle Bin is a data recovery feature that enables you to restore accidentally deleted Amazon EBS snapshots and EBS\-backed AMIs\. When using Recycle Bin, if your resources are deleted, they are retained in the Recycle Bin for a time period that you specify before being permanently deleted\.

You can restore a resource from the Recycle Bin at any time before its retention period expires\. After you restore a resource from the Recycle Bin, the resource is removed from the Recycle Bin and you can use it in the same way that you use any other resource of that type in your account\. If the retention period expires and the resource is not restored, the resource is permanently deleted from the Recycle Bin and it is no longer available for recovery\.

Snapshots in the Recycle Bin are billed at the same rate as regular snapshots in your account\. There are no additional charges for using Recycle Bin and retention rules\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

For more information, see [Recycle Bin](recycle-bin.md)\.

**Topics**
+ [Permissions for working with snapshots in the Recycle Bin](#snap-perms)
+ [View snapshots in the Recycle Bin](#recycle-bin-view-snaps)
+ [Restore snapshots from the Recycle Bin](#recycle-bin-restore-snaps)

## Permissions for working with snapshots in the Recycle Bin<a name="snap-perms"></a>

By default, IAM users don't have permission to work with snapshots that are in the Recycle Bin\. To allow IAM users to work with these resources, you must create IAM policies that grant permission to use specific resources and API actions\. You then attach those policies to the IAM users or the groups that require those permissions\.

To view and recover snapshots that are in the Recycle Bin, IAM users must have the following permissions:
+ `ec2:ListSnapshotsInRecycleBin`
+ `ec2:RestoreSnapshotFromRecycleBin`

To manage tags for snapshots in the Recycle Bin, IAM users need the following additional permissions\.
+ `ec2:CreateTags`
+ `ec2:DeleteTags`

To use the Recycle Bin console, IAM users need the `ec2:DescribeTags` permission\.

The following is an example IAM policy\. It includes the `ec2:DescribeTags` permission for console users, and it includes the `ec2:CreateTags` and `ec2:CreateTags` permissions for managing tags\. If the permissions are not needed, you can remove them from the policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
            "ec2:ListSnapshotsInRecycleBin", 
            "ec2:RestoreSnapshotFromRecycleBin"
        ],
        "Resource": "*"
      },
      {
        "Effect": "Allow",
        "Action": [
            "ec2:CreateTags",
            "ec2:DeleteTags",
            "ec2:DescribeTags"
        ],
        "Resource": "arn:aws:ec2:Region:account-id:snapshot/*"
      },
    
    ]
}
```

For more information about the permissions needed to use Recycle Bin, see [Permissions for working with Recycle Bin and retention rules](recycle-bin-perms.md#rule-perms)\.

## View snapshots in the Recycle Bin<a name="recycle-bin-view-snaps"></a>

While a snapshot is in the Recycle Bin, you can view limited information about it, including:
+ The ID of the snapshot\.
+ The snapshot description\.
+ The ID of the volume from which the snapshot was created\.
+ The date and time when the snapshot was deleted and it entered Recycle Bin\.
+ The date and time when the retention period expires\. The snapshot will be permanently deleted from the Recycle Bin at this time\.

You can view the snapshots in the Recycle Bin using one of the following methods\.

------
#### [ Recycle Bin console ]

**To view snapshots in the Recycle Bin using the console**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com//rbin/home/](https://console.aws.amazon.com//rbin/home/)

1. In the navigation panel, choose **Recycle Bin**\.

1. The grid lists all of the snapshots that are currently in the Recycle Bin\. To view the details for a specific snapshot, select it in the grid and choose **Actions**, **View details**\.

------
#### [ AWS CLI ]

**To view snapshots in the Recycle Bin using the AWS CLI**  
Use the [ list\-snapshots\-in\-recycle\-bin](https://docs.aws.amazon.com/cli/latest/reference/ec2/list-snapshots-in-recycle-bin.html) AWS CLI command\. Include the `--snapshot-id` option to view a specific snapshot\. Or omit the `--snapshot-id` option to view all snapshots in the Recycle Bin\.

```
$ aws ec2 list-snapshots-in-recycle-bin --snapshot-id snapshot_id
```

For example, the following command provides information about snapshot `snap-01234567890abcdef` in the Recycle Bin\.

```
$ aws ec2 list-snapshots-in-recycle-bin --snapshot-id snap-01234567890abcdef
```

Example output:

```
{
    "SnapshotRecycleBinInfo": [
        {
            "Description": "Monthly data backup snapshot",
            "RecycleBinEnterTime": "2021-12-01T13:00:00.000Z",
            "RecycleBinExitTime": "2021-12-15T13:00:00.000Z",
            "VolumeId": "vol-abcdef09876543210",
            "SnapshotId": "snap-01234567890abcdef"
        }
    ]
}
```

------

## Restore snapshots from the Recycle Bin<a name="recycle-bin-restore-snaps"></a>

You can't use a snapshot in any way while it is in the Recycle Bin\. To use the snapshot, you must first restore it\. When you restore a snapshot from the Recycle Bin, the snapshot is immediately available for use, and it is removed from the Recycle Bin\. You can use a restored snapshot in the same way that you use any other snapshot in your account\.

You can restore a snapshot from the Recycle Bin using one of the following methods\.

------
#### [ Recycle Bin console ]

**To restore a snapshot from the Recycle Bin using the console**

1. Open the Recycle Bin console at [https://console\.aws\.amazon\.com//rbin/home/](https://console.aws.amazon.com//rbin/home/)

1. In the navigation panel, choose **Recycle Bin**\.

1. The grid lists all of the snapshots that are currently in the Recycle Bin\. Select the snapshot to restore and and choose **Recover**\.

1. When prompted, choose **Recover**\.

------
#### [ AWS CLI ]

**To restore a deleted snapshot from the Recycle Bin using the AWS CLI**  
Use the [ restore\-snapshot\-from\-recycle\-bin](https://docs.aws.amazon.com/cli/latest/reference/ec2/restore-snapshot-from-recycle-bin.html) AWS CLI command\. For `--snapshot-id`, specify the ID of the snapshot to restore\.

```
$ aws ec2 restore-snapshot-from-recycle-bin --snapshot-id snapshot_id
```

For example, the following command restores snapshot `snap-01234567890abcdef` from the Recycle Bin\.

```
$ aws restore-snapshot-from-recycle-bin --snapshot-id snap-01234567890abcdef
```

The command returns no output on success\.

------
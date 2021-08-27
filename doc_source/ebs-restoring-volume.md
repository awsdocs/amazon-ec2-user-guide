# Replace an Amazon EBS volume<a name="ebs-restoring-volume"></a>

Amazon EBS snapshots are the preferred backup tool on Amazon EC2 because of their speed, convenience, and cost\. When creating a volume from a snapshot, you recreate its state at a specific point in the past with all data intact\. By attaching a volume created from a snapshot to an instance, you can duplicate data across Regions, create test environments, replace a damaged or corrupted production volume in its entirety, or retrieve specific files and directories and transfer them to another attached volume\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

The procedure for replacing a volume differs depending on whether the volume is the root volume or a data volume\.

**Topics**
+ [Replace a root volume](#replace-root)
+ [Replace a data volume](#replace-data)

## Replace a root volume<a name="replace-root"></a>

Amazon EC2 enables you to replace the root EBS volume for an instance without stopping the instance\. You can restore the root volume for an instance to its launch state, or to a specific snapshot\. This allows you to fix issues, such as root volume corruption or guest operating system network configuration errors, while retaining the following:
+ Data stored on instance store volumes — Instance store volumes remain attached to the instance after the root volume has been replaced\.
+ Network configuration — All network interfaces remain attached to the instance and they retain their IP addresses, identifiers, and attachment IDs\. When the instance becomes available, all pending network traffic is flushed\. Additionally, the instance remains on the same physical host, so it retains its public and private IP addresses and DNS name\.
+ IAM policies — IAM profiles and policies \(such as tag\-based policies\) that are associated with the instance are retained and enforced\.

When you replace the root volume for an instance, a new volume is restored to the original volume's launch state, or using a specific snapshot\. The original volume is detached from the instance, and the new volume is attached to the instance in its place\. The original volume is not automatically deleted\. If you no longer need it, you can delete it manually after the root volume replacement task completes\. For more information about root volume replacement task states, see [View root volume replacement tasks](#view-replacement-tasks)\.

**Topics**
+ [Considerations](#considerations)
+ [Replace a root volume](#replace)
+ [View root volume replacement tasks](#view-replacement-tasks)

### Considerations<a name="considerations"></a>
+ The instance is automatically rebooted when the root volume is replaced\. The contents of the memory \(RAM\) is erased during the reboot\.
+ You can't replace the root volume if it is an instance store volume\.
+ You can't replace the root volume for metal instances\.
+ You can only use snapshots that belong to the same lineage as the instance's current root volume\. You can't use snapshot copies created from snapshots that were taken from the root volume\. Additionally, after successfully completing a root volume replacement task, snapshots taken from the previous root volume can't be used to create a root volume replacement task for the new volume\.

### Replace a root volume<a name="replace"></a>

When you replace the root volume for an instance, you can choose to restore the volume to its initial launch state, or you can choose to restore the volume to a specific snapshot\. If you choose to restore the volume to a specific snapshot, then you must select a snapshot that was taken of that root volume\. If you choose to restore the root volume to its initial launch state, the root volume is restored from the snapshot that was used to create the volume\.

You can replace the root volume for an instance using one of the following methods\. If you use the Amazon EC2 console, note that replacing the root volume is only available in the new console\.

------
#### [ Amazon EC2 console ]

**To replace the root volume**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance for which to replace the root volume and choose **Actions**, **Monitor and troubleshoot**, **Replace root volume**\.

1. In the **Replace root volume** screen, do one of the following:
   + To restore the instance's root volume to its initial launch state, choose **Create replacement task** without selecting a snapshot\.
   + To restore the instance's root volume to a specific snapshot, for **Snapshot**, select the snapshot to use, and then choose **Create replacement task**\.

------
#### [ AWS CLI ]

**To restore the root volume to the initial launch state**  
Use the [ create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. Specify the ID of the instance for which to replace the root volume and omit the `--snapshot-id` parameter\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-1234567890abcdef0
```

**To restore the root volume to a specific snapshot**  
Use the [create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. Specify the ID of the instance for which to replace the root volume and the ID of the snapshot to use\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id --snapshot-id snapshot_id
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-1234567890abcdef0 --snapshot-id snap-9876543210abcdef0
```

------

### View root volume replacement tasks<a name="view-replacement-tasks"></a>

After you start a root volume replacement task, the task enters the following states:
+ `pending` — the replacement volume is being created\.
+ `in-progress` — the original volume is being detached and the replacement volume is being attached\.
+ `succeeded` — the replacement volume has been successfully attached to the instance and the instance is available\.
+ `failing` — the replacement task is in the process of failing\.
+ `failed` — the replacement task has failed but the original root volume is still attached\.
+ `failing-detached` — the replacement task is in the process of failing\. The instance might have no root volume attached\.
+ `failed-detached` — the replacement task has failed and the instance has no root volume attached\.

You can view the root volume replacement tasks for an instance using one of the following methods\.

------
#### [ Amazon EC2 console ]

**To view the root volume replacement tasks**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance for which to view the root volume replacement tasks, and then choose the **Storage** tab\.

1. In the **Storage** tab, expand **Recent root volume replacement tasks**\.

------
#### [ AWS CLI ]

**To view the status of a root volume replacement task**  
Use the [describe\-replace\-root\-volume\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-replace-root-volume-tasks.html) command and specify the IDs of the root volume replacement tasks to view\.

```
$ aws ec2 describe-replace-root-volume-tasks --replace-root-volume-task-ids task_id_1 task_id_2
```

For example:

```
$ aws ec2 describe-replace-root-volume-tasks --replace-root-volume-task-ids replacevol-1234567890abcdef0 
```

```
{
  "ReplaceRootVolumeTasks": [
  {   
    "ReplaceRootVolumeTaskId": "replacevol-1234567890abcdef0",
    "InstanceId": "i-1234567890abcdef0",
    "TaskState": "succeeded",
    "StartTime": "2020-11-06 13:09:54.0",
    "CompleteTime": "2020-11-06 13:10:14.0"
  }]
}
```

****  


Alternatively, specify the `instance-id` filter to filter the results by instance\.

```
$ aws ec2 describe-replace-root-volume-tasks --filters Name=instance-id,Values=instance_id
```

For example:

```
$ aws ec2 describe-replace-root-volume-tasks --filters Name=instance-id,Values=i-1234567890abcdef0
```

------

## Replace a data volume<a name="replace-data"></a>

You can use the following procedure to replace a \(non\-root\) data volume with another volume created from a previous snapshot of that volume\. You must detach the current volume and then attach the new volume\.

Note that EBS volumes can only be attached to EC2 instances in the same Availability Zone\.

Use the following method\.

------
#### [ Console ]

**To replace a data volume**

1. Create a volume from the snapshot and write down the ID of the new volume\. For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

1. On the volumes page, select the check box for the volume to replace\. On the **Description** tab, find **Attachment information** and write down the device name of the volume \(for example, `/dev/sda1`\) and the ID of the instance\. 

1. With the volume still selected, choose **Actions**, **Detach Volume**\. When prompted for confirmation, choose **Yes, Detach**\. Clear the check box for this volume\.

1. Select the check box for the new volume that you created in step 1\. Choose **Actions**, **Attach Volume**\. Enter the instance ID and device name that you wrote down in step 2, and then choose **Attach**\.

1. Connect to your instance and mount the volume\. For more information, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.

------
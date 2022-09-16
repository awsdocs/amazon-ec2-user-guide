# Restore a root volume<a name="replace-root"></a>

Amazon EC2 enables you to restore the root Amazon EBS volume for a running instance to its launch state, or to a specific snapshot\. This allows you to fix issues, such as root volume corruption or guest operating system network configuration errors, while retaining the following:
+ Data stored on instance store volumes — Instance store volumes remain attached to the instance after the root volume has been restored\.
+ Network configuration — All network interfaces remain attached to the instance and they retain their IP addresses, identifiers, and attachment IDs\. When the instance becomes available, all pending network traffic is flushed\. Additionally, the instance remains on the same physical host, so it retains its public and private IP addresses and DNS name\.
+ IAM policies — IAM profiles and policies \(such as tag\-based policies\) that are associated with the instance are retained and enforced\.

When you restore the root volume for an instance, a new volume is restored to the original volume's launch state, or using a specific snapshot\. The original volume is detached from the instance, and the new \(restored\) volume is attached to the instance in its place\. The original volume is not automatically deleted\. If you no longer need it, you can delete it manually after the process has completed\.

**Topics**
+ [Considerations](#considerations)
+ [Restore a root volume](#replace)
+ [View root volume replacement tasks](#view-replacement-tasks)

## Considerations<a name="considerations"></a>
+ The instance must be in the `running` state\.
+ The instance is automatically rebooted during the process\. The contents of the memory \(RAM\) is erased during the reboot\.
+ You can't restore the root volume if it is an instance store volume\.
+ You can't restore the root volume for metal instances\.
+ You can use any snapshot that belongs to the same lineage as any of the instance's previous root volumes\.

## Restore a root volume<a name="replace"></a>

When you restore the root volume for an instance, you can choose to restore the volume to its initial launch state, or you can choose to restore the volume to a specific snapshot\. If you choose to restore the volume to a specific snapshot, then you must select a snapshot that was created from any of the instance's previous root volumes\. If you choose to restore the root volume to its initial launch state, the root volume is restored from the snapshot that was used to create the volume during instance launch\.

When you restore the root volume for an instance, a *root volume replacement task* is created\. You can use the root volume replacement task to monitor the progress and outcome of the restore process\. For more information, see [View root volume replacement tasks](#view-replacement-tasks)\.

You can restore the root volume for an instance using one of the following methods\.

**Note**  
If you use the Amazon EC2 console, the functionality is available in the new console only\.

------
#### [ New console ]

**To restore the root volume**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance for which to restore the root volume and choose **Actions**, **Monitor and troubleshoot**, **Replace root volume**\.
**Note**  
The **Replace root volume** action is disabled if the selected instance is not in the `running` state\.

1. In the **Replace root volume** screen, do one of the following:
   + To restore the instance's root volume to its initial launch state, choose **Create replacement task** without selecting a snapshot\.
   + To restore the instance's root volume to a specific snapshot, for **Snapshot**, select the snapshot to use, and then choose **Create replacement task**\.

------
#### [ AWS CLI ]

**To restore the root volume to the initial launch state**  
Use the [ create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. Specify the ID of the instance for which to restore the root volume and omit the `--snapshot-id` parameter\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-1234567890abcdef0
```

**To restore the root volume to a specific snapshot**  
Use the [create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. Specify the ID of the instance for which to restore the root volume and the ID of the snapshot to use\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id --snapshot-id snapshot_id
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-1234567890abcdef0 --snapshot-id snap-9876543210abcdef0
```

------

## View root volume replacement tasks<a name="view-replacement-tasks"></a>

When you restore the root volume for an instance, a *root volume replacement task* is created\. The root volume replacement task transitions through the following states during the process:
+ `pending` — the replacement volume is being created\.
+ `in-progress` — the original volume is being detached and the replacement volume is being attached\.
+ `succeeded` — the replacement volume has been successfully attached to the instance and the instance is available\.
+ `failing` — the replacement task is in the process of failing\.
+ `failed` — the replacement task has failed but the original root volume is still attached\.
+ `failing-detached` — the replacement task is in the process of failing\. The instance might have no root volume attached\.
+ `failed-detached` — the replacement task has failed and the instance has no root volume attached\.

You can view the root volume replacement tasks for an instance using one of the following methods\.

**Note**  
If you use the Amazon EC2 console, the functionality is available in the new console only\.

------
#### [ New console ]

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
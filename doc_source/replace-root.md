# Replace a root volume<a name="replace-root"></a>

Amazon EC2 enables you to replace the root Amazon EBS volume for a **running** instance while retaining the following:
+ Data stored on instance store volumes — Instance store volumes remain attached to the instance after the root volume has been restored\.
+ Data stored on data \(non\-root\) Amazon EBS volumes — Non\-root Amazon EBS volumes remain attached to the instance after the root volume has been restored\.
+ Network configuration — All network interfaces remain attached to the instance and they retain their IP addresses, identifiers, and attachment IDs\. When the instance becomes available, all pending network traffic is flushed\. Additionally, the instance remains on the same physical host, so it retains its public and private IP addresses and DNS name\.
+ IAM policies — IAM profiles and policies \(such as tag\-based policies\) that are associated with the instance are retained and enforced\.

**Topics**
+ [How does it work?](#replace-root-how)
+ [Replace a root volume](#replace)
+ [View root volume replacement tasks](#view-replacement-tasks)

## How does it work?<a name="replace-root-how"></a>

When you replace the root volume for an instance, a new \(replacement\) root volume is restored in one of the following ways:
+ **To the initial launch state** — the volume is restored to its initial state at instance launch\. For more information, see [Restore a root volume to its launch state](#replace-launchstate)\.
+ **From a snapshot** from the same lineage as the current root volume — this allows you to fix issues, such as root volume corruption or guest operating system network configuration errors\. For more information, see [Replace a root volume using a snapshot](#replace-snapshot)\.
+ **From an AMI** that has the same key attributes as the instance — this allows you to perform operating system and application patching or upgrades\. For more information, see [Replace a root volume using an AMI](#replace-ami)\.

The original root volume is detached from the instance, and the new root volume is attached to the instance in its place\. The instance's block device mapping is updated to reflect the ID of the replacement root volume\. You can choose whether or not to keep the original root volume after the root volume replacement process has completed\. If you choose delete the original root volume after the replacement process completes, the original root volume is automatically deleted and becomes unrecoverable\. If you choose to keep the original root volume after the process completes, the volume remains provisioned in your account; you must manually delete it when you no longer need it\.

If the root volume replacement task fails, the instance is rebooted and the original root volume remains attached to the instance\.

**Considerations for root volume replacement**
+ The instance must be in the `running` state\.
+ The instance is automatically rebooted during the process\. The contents of the memory \(RAM\) is erased during the reboot\. No manual reboots are required\.
+ You can't replace the root volume if it is an instance store volume\. Only instances with Amazon EBS root volumes are supported\. 
+ You can replace the root volume for all virtualized instance types and Amazon EC2 Mac metal instances only\. All other metal instances are not supported\.
+ You can use any snapshot that belongs to the same lineage as any of the instance's previous root volumes\.

**Topics**
+ [Restore a root volume to its launch state](#replace-launchstate)
+ [Replace a root volume using a snapshot](#replace-snapshot)
+ [Replace a root volume using an AMI](#replace-ami)

### Restore a root volume to its launch state<a name="replace-launchstate"></a>

You can perform a root volume replacement that replaces an instance's root volume with a replacement root volume that is restored to the original root volume's launch state\. The replacement volume is automatically restored from the snapshot that was used to create the original volume during the instance launch\.

The replacement root volume gets the same type, size, and delete on termination attributes as the original root volume\.

### Replace a root volume using a snapshot<a name="replace-snapshot"></a>

You can perform a root volume replacement that replaces an instance's root volume with a replacement volume that is restored to a specific snapshot\. This enables you to restore the root volume for an instance to a specific snapshot that you previously created from that root volume\.

The replacement root volume gets the same type, size, and delete on termination attributes as the original root volume\.

**Considerations for using a snapshot**
+ You can only use snapshots that belong to the same lineage as the instance's current root volume\. 
+ You can't use snapshot copies created from snapshots that were taken from the root volume\. 
+ After successfully replacing the root volume, snapshots taken from the original root volume can still be used to replace the new \(replacement\) root volume\.

### Replace a root volume using an AMI<a name="replace-ami"></a>

You can perform a root volume replacement using an AMI that you own, an AMI that is shared with you, or an AWS Marketplace AMI\. The AMI must have the same product code, billing information, architecture type, and virtualization type as that of the instance\.

If the instance is enabled for NitroTPM, ENA, or sriov\-net, then you must use an AMI that supports those features\. If the instance is not enabled for NitroTPM, ENA, or sriov\-net, then you can select an AMI that does not support those features, or you can select an AMI that does support them, in which case support is added to the instance\.

You can select an AMI with a different boot mode than that of the instance, as long as the instance supports the boot mode of the AMI\. If the instance does not support the boot mode, the request fails\. If the instance supports the boot mode, the new boot mode is propagated to the instance and its UEFI data is updated accordingly\. If you manually modified the boot order or added a private UEFI Secure Boot key to load private kernel modules, the changes are lost during root volume replacement\.

The replacement root volume gets the same volume type and delete on termination attribute as the original root volume\. It gets either the size of the original root volume or the size of the AMI's block device mapping for the root volume, whichever is larger\.

After the root volume replacement task completes, the following new and updated information is reflected when you describe the instance using the console, AWS CLI or AWS SDKs:
+ New AMI ID
+ New volume ID for the root volume
+ Updated boot mode configuration \(if changed by the AMI\)
+ Updated NitroTPM configuration \(if enabled by the AMI\)
+ Updated ENA configuration \(if enabled by the AMI\)
+ Updated sriov\-net configuration \(if enabled by the AMI\)

The new AMI ID is also reflected in the instance metadata\.

**Considerations for using an AMI:**
+ If you use an AMI that has multiple block device mappings, only the root volume of the AMI is used\. The other \(non\-root\) volumes are ignored\.
+ You can only use AMIs for which your account has launch permissions\.
+ You can only use an AMI without a product code only if the instance does not have a product code\.
+ You can't specify the encryption status for the replacement root volume\. The following table summarizes the possible encryption outcomes\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/replace-root.html)
+ The instance identity documents for the instance are automatically updated\.
+ If the instance supports NitroTPM, the NitroTPM data for the instance is reset and new keys are generated\.

## Replace a root volume<a name="replace"></a>

When you replace the root volume for an instance, a *root volume replacement task* is created\. You can use the root volume replacement task to monitor the progress and outcome of the replacement process\. For more information, see [View root volume replacement tasks](#view-replacement-tasks)\.

You can replace the root volume for an instance using one of the following methods\.

**Note**  
If you use the Amazon EC2 console, the functionality is available in the new console only\.

------
#### [ New console ]

**To replace the root volume**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance for which to replace the root volume and choose **Actions**, **Monitor and troubleshoot**, **Replace root volume**\.
**Note**  
The **Replace root volume** action is disabled if the selected instance is not in the `running` state\.

1. In the **Replace root volume** screen, do one of the following:
   + To restore the replacement root volume to its initial launch state, choose **Create replacement task** without selecting a snapshot\.
   + To restore the replacement root volume to a specific snapshot, for **Snapshot**, select the snapshot to use, and then choose **Create replacement task**\.
   + To restore the replacement root volume using an AMI, for **AMI**, select the AMI to use, and then choose **Create replacement task**\.

1. To delete the original root volume after the replacement task completes, select **Delete replaced root volume**\.

------
#### [ AWS CLI ]

**To restore the replacement root volume to the launch state**  
Use the [ create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. For `--instance-id`, specify the ID of the instance for which to replace the root volume\. Omit the `--snapshot-id` `--image-id` parameters\. To delete the original root volume after it has been replaced, include `--delete-replaced-root-volume` and specify `true`\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id --delete-replaced-root-volume true
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-1234567890abcdef0 --delete-replaced-root-volume true
```

**To restore the replacement root volume to a specific snapshot**  
Use the [create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. For `--instance-id`, specify the ID of the instance for which to replace the root volume\. For `--snapshot-id`, specify the ID of the snapshot to use\. To delete the original root volume after it has been replaced, include `--delete-replaced-root-volume` and specify `true`\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id --snapshot-id snapshot_id --delete-replaced-root-volume true
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-1234567890abcdef0 --snapshot-id snap-9876543210abcdef0 --delete-replaced-root-volume true
```

**To restore the replacement root volume using an AMI**  
Use the [ create\-replace\-root\-volume\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-replace-root-volume-task.html) command\. For `--instance-id`, specify the ID of the instance for which to replace the root volume\. For `--image-id`, specify the ID of the AMI to use\. To delete the original root volume after it has been replaced, include `--delete-replaced-root-volume` and specify `true`\.

```
$ aws ec2 create-replace-root-volume-task --instance-id instance_id --image-id ami_id --delete-replaced-root-volume true
```

For example:

```
$ aws ec2 create-replace-root-volume-task --instance-id i-01234567890abcdef --image-id ami-09876543210abcdef --delete-replaced-root-volume true
```

------

## View root volume replacement tasks<a name="view-replacement-tasks"></a>

When you replace the root volume for an instance, a *root volume replacement task* is created\. The root volume replacement task transitions through the following states during the process:
+ `pending` — the replacement volume is being created\.
+ `in-progress` — the original volume is being detached and the replacement volume is being attached\.
+ `succeeded` — the replacement volume has been successfully attached to the instance and the instance is available\.
+ `failing` — the replacement task is in the process of failing\.
+ `failed` — the replacement task has failed, but the original root volume is still attached\.
+ `failing-detached` — the replacement task is in the process of failing and the instance might not have a root volume attached\.
+ `failed-detached` — the replacement task has failed and the instance doesn't have a root volume attached\.

You can view the root volume replacement tasks for an instance using one of the following methods\.

**Note**  
If you use the Amazon EC2 console, the functionality is available in the new console only\.

------
#### [ Console ]

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
    "CompleteTime": "2020-11-06 13:10:14.0",
    "SnapshotId": "snap-01234567890abcdef",
    "DeleteReplacedRootVolume": "True"
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
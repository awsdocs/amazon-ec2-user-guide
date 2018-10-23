# Terminate Your Instance<a name="terminating-instances"></a>

You can delete your instance when you no longer need it\. This is referred to as *terminating* your instance\. As soon as the state of an instance changes to `shutting-down` or `terminated`, you stop incurring charges for that instance\.

You can't connect to or restart an instance after you've terminated it\. However, you can launch additional instances using the same AMI\. If you'd rather stop and restart your instance, see [Stop and Start Your Instance](Stop_Start.md)\. For more information, see [Differences Between Reboot, Stop, and Terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

**Topics**
+ [Instance Termination](#termination-overview)
+ [Terminating an Instance](#terminating-instances-console)
+ [Enabling Termination Protection for an Instance](#Using_ChangingDisableAPITermination)
+ [Changing the Instance Initiated Shutdown Behavior](#Using_ChangingInstanceInitiatedShutdownBehavior)
+ [Preserving Amazon EBS Volumes on Instance Termination](#preserving-volumes-on-termination)
+ [Troubleshooting](#troubleshoot-instance-terminate)

## Instance Termination<a name="termination-overview"></a>

After you terminate an instance, it remains visible in the console for a short while, and then the entry is automatically deleted\. You cannot delete the terminated instance entry yourself\. After an instance is terminated, resources such as tags and volumes are gradually disassociated from the instance, therefore may no longer be visible on the terminated instance after a short while\.

When an instance terminates, the data on any instance store volumes associated with that instance is deleted\. 

By default, Amazon EBS root device volumes are automatically deleted when the instance terminates\. However, by default, any additional EBS volumes that you attach at launch, or any EBS volumes that you attach to an existing instance persist even after the instance terminates\. This behavior is controlled by the volume's `DeleteOnTermination` attribute, which you can modify\. For more information, see [Preserving Amazon EBS Volumes on Instance Termination](#preserving-volumes-on-termination)\.

You can prevent an instance from being terminated accidentally by someone using the AWS Management Console, the CLI, and the API\. This feature is available for both Amazon EC2 instance store\-backed and Amazon EBS\-backed instances\. Each instance has a `DisableApiTermination` attribute with the default value of `false` \(the instance can be terminated through Amazon EC2\)\. You can modify this instance attribute while the instance is running or stopped \(in the case of Amazon EBS\-backed instances\)\. For more information, see [Enabling Termination Protection for an Instance](#Using_ChangingDisableAPITermination)\.

You can control whether an instance should stop or terminate when shutdown is initiated from the instance using an operating system command for system shutdown\. For more information, see [Changing the Instance Initiated Shutdown Behavior](#Using_ChangingInstanceInitiatedShutdownBehavior)\.

If you run a script on instance termination, your instance might have an abnormal termination, because we have no way to ensure that shutdown scripts run\. Amazon EC2 attempts to shut an instance down cleanly and run any system shutdown scripts; however, certain events \(such as hardware failure\) may prevent these system shutdown scripts from running\.

## Terminating an Instance<a name="terminating-instances-console"></a>

You can terminate an instance using the AWS Management Console or the command line\.

**To terminate an instance using the console**

1. Before you terminate the instance, verify that you won't lose any data by checking that your Amazon EBS volumes won't be deleted on termination and that you've copied any data that you need from your instance store volumes to Amazon EBS or Amazon S3\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and choose **Actions**, **Instance State**, **Terminate**\.

1. Choose **Yes, Terminate** when prompted for confirmation\.

**To terminate an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) \(AWS CLI\)
+ [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Enabling Termination Protection for an Instance<a name="Using_ChangingDisableAPITermination"></a>

By default, you can terminate your instance using the Amazon EC2 console, command line interface, or API\. If you want to prevent your instance from being accidentally terminated using Amazon EC2, you can enable *termination protection* for the instance\. The `DisableApiTermination` attribute controls whether the instance can be terminated using the console, CLI, or API\. By default, termination protection is disabled for your instance\. You can set the value of this attribute when you launch the instance, while the instance is running, or while the instance is stopped \(for Amazon EBS\-backed instances\)\. 

The `DisableApiTermination` attribute does not prevent you from terminating an instance by initiating shutdown from the instance \(using an operating system command for system shutdown\) when the `InstanceInitiatedShutdownBehavior` attribute is set\. For more information, see [Changing the Instance Initiated Shutdown Behavior](#Using_ChangingInstanceInitiatedShutdownBehavior)\.

**Limits**  
You can't enable termination protection for Spot instances — a Spot instance is terminated when the Spot price exceeds your bid price\. However, you can prepare your application to handle Spot instance interruptions\. For more information, see [Spot Instance Interruptions](spot-interruptions.md)\.

The `DisableApiTermination` attribute does not prevent Amazon EC2 Auto Scaling from terminating an instance\. For instances in an Auto Scaling group, use the following Amazon EC2 Auto Scaling features instead of Amazon EC2 termination protection:
+ To prevent instances that are part of an Auto Scaling group from terminating on scale in, use instance protection\. For more information, see [Instance Protection](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-instance-termination.html#instance-protection) in the *Amazon EC2 Auto Scaling User Guide*\.
+ To prevent Amazon EC2 Auto Scaling from terminating unhealthy instances, suspend the `ReplaceUnhealthy` process\. For more information, see [Suspending and Resuming Scaling Processes](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-suspend-resume-processes.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ To specify which instances Amazon EC2 Auto Scaling should terminate first, choose a termination policy\. For more information, see [Customizing the Termination Policy](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-instance-termination.html#custom-termination-policy) in the *Amazon EC2 Auto Scaling User Guide*\.

**To enable termination protection for an instance at launch time**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch Instance** and follow the directions in the wizard\.

1. On the **Configure Instance Details** page, select the **Enable termination protection** check box\.

**To enable termination protection for a running or stopped instance**

1. Select the instance, choose **Actions**, **Instance Settings**, and then choose **Change Termination Protection**\.

1. Select **Yes, Enable**\.

**To disable termination protection for a running or stopped instance**

1. Select the instance, choose **Actions**, **Instance Settings**, and then choose **Change Termination Protection**\.

1. Select **Yes, Disable**\.

**To enable or disable termination protection using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Changing the Instance Initiated Shutdown Behavior<a name="Using_ChangingInstanceInitiatedShutdownBehavior"></a>

By default, when you initiate a shutdown from an Amazon EBS\-backed instance \(using a command such as shutdown or poweroff\), the instance stops \(Note that halt does not issue a poweroff command and, if used, the instance will not terminate; instead, it will place the CPU into HLT and the instance will remain running\)\. You can change this behavior using the `InstanceInitiatedShutdownBehavior` attribute for the instance so that it terminates instead\. You can update this attribute while the instance is running or stopped\. 

You can update the `InstanceInitiatedShutdownBehavior` attribute using the Amazon EC2 console or the command line\. The `InstanceInitiatedShutdownBehavior` attribute only applies when you perform a shutdown from the operating system of the instance itself; it does not apply when you stop an instance using the `StopInstances` API or the Amazon EC2 console\.

**To change the shutdown behavior of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, select **Actions**, **Instance Settings**, and then choose **Change Shutdown Behavior**\. The current behavior is already selected\.

1. To change the behavior, select an option from the **Shutdown behavior** list, and then select **Apply**\.  
![\[The Change Shutdown Behavior dialog box\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/shutdown_behavior_dialog.png)

**To change the shutdown behavior of an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Preserving Amazon EBS Volumes on Instance Termination<a name="preserving-volumes-on-termination"></a>

When an instance terminates, Amazon EC2 uses the value of the `DeleteOnTermination` attribute for each attached Amazon EBS volume to determine whether to preserve or delete the volume\.

By default, the `DeletionOnTermination` attribute for the root volume of an instance is set to `true`\. Therefore, the default is to delete the root volume of an instance when the instance terminates\.

By default, when you attach an EBS volume to an instance, its `DeleteOnTermination` attribute is set to `false`\. Therefore, the default is to preserve these volumes\. After the instance terminates, you can take a snapshot of the preserved volume or attach it to another instance\.

To verify the value of the `DeleteOnTermination` attribute for an EBS volume that is in\-use, look at the instance's block device mapping\. For more information, see [Viewing the EBS Volumes in an Instance Block Device Mapping](block-device-mapping-concepts.md#view-instance-bdm)\.

You can change value of the `DeleteOnTermination` attribute for a volume when you launch the instance or while the instance is running\.

**Topics**
+ [Changing the Root Volume to Persist at Launch Using the Console](#delete-on-termination-console)
+ [Changing the Root Volume to Persist at Launch Using the Command Line](#delete-on-termination-cli)
+ [Changing the Root Volume of a Running Instance to Persist Using the Command Line](#delete-on-termination-running-instance)

### Changing the Root Volume to Persist at Launch Using the Console<a name="delete-on-termination-console"></a>

Using the console, you can change the `DeleteOnTermination` attribute when you launch an instance\. To change this attribute for a running instance, you must use the command line\.

**To change the root volume of an instance to persist at launch using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, select **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose an AMI and choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect the **Delete On Termination** check box for the root volume\.

1. Complete the remaining wizard pages, and then choose **Launch**\.

You can verify the setting by viewing details for the root device volume on the instance's details pane\. Next to **Block devices**, click the entry for the root device volume\. By default, **Delete on termination** is `True`\. If you change the default behavior, **Delete on termination** is `False`\.

### Changing the Root Volume to Persist at Launch Using the Command Line<a name="delete-on-termination-cli"></a>

When you launch an EBS\-backed instance, you can use one of the following commands to change the root device volume to persist\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
+ [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

For example, add the following option to your `run-instances` command:

```
--block-device-mappings file://mapping.json
```

Specify the following in `mapping.json`:

```
[
  {
    "DeviceName": "/dev/sda1",
    "Ebs": {
      "DeleteOnTermination": false,
      "SnapshotId": "snap-1234567890abcdef0",
      "VolumeType": "gp2"
    }
  }
]
```

### Changing the Root Volume of a Running Instance to Persist Using the Command Line<a name="delete-on-termination-running-instance"></a>

You can use one of the following commands to change the root device volume of a running EBS\-backed instance to persist\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

For example, use the following command:

```
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --block-device-mappings file://mapping.json
```

Specify the following in `mapping.json`:

```
[
  {
    "DeviceName": "/dev/sda1",
    "Ebs": {
      "DeleteOnTermination": false
    }
  }
]
```

## Troubleshooting<a name="troubleshoot-instance-terminate"></a>

If your instance is in the `shutting-down` state for longer than usual, it will eventually be cleaned up \(terminated\) by automated processes within the Amazon EC2 service\. For more information, see [Troubleshooting Terminating \(Shutting Down\) Your Instance](TroubleshootingInstancesShuttingDown.md)\. 
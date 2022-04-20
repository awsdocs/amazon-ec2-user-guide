# Terminate your instance<a name="terminating-instances"></a>

You can delete your instance when you no longer need it\. This is referred to as *terminating* your instance\. As soon as the state of an instance changes to `shutting-down` or `terminated`, you stop incurring charges for that instance\.

You can't connect to or start an instance after you've terminated it\. However, you can launch additional instances using the same AMI\. If you'd rather stop and start your instance, or hibernate it, see [Stop and start your instance](Stop_Start.md) or [Hibernate your On\-Demand Linux instance](Hibernate.md)\. For more information, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

**Topics**
+ [Instance termination](#termination-overview)
+ [Terminating multiple instances with termination protection across Availability Zones](#terminate-multiple)
+ [What happens when you terminate an instance](#what-happens-terminate)
+ [Terminate an instance](#terminating-instances-console)
+ [Enable termination protection](#Using_ChangingDisableAPITermination)
+ [Change the instance initiated shutdown behavior](#Using_ChangingInstanceInitiatedShutdownBehavior)
+ [Preserve Amazon EBS volumes on instance termination](#preserving-volumes-on-termination)
+ [Troubleshoot instance termination](#troubleshoot-instance-terminate)

## Instance termination<a name="termination-overview"></a>

After you terminate an instance, it remains visible in the console for a short while, and then the entry is automatically deleted\. You cannot delete the terminated instance entry yourself\. After an instance is terminated, resources such as tags and volumes are gradually disassociated from the instance and may no longer be visible on the terminated instance after a short while\.

When an instance terminates, the data on any instance store volumes associated with that instance is deleted\. 

By default, Amazon EBS root device volumes are automatically deleted when the instance terminates\. However, by default, any additional EBS volumes that you attach at launch, or any EBS volumes that you attach to an existing instance persist even after the instance terminates\. This behavior is controlled by the volume's `DeleteOnTermination` attribute, which you can modify\. For more information, see [Preserve Amazon EBS volumes on instance termination](#preserving-volumes-on-termination)\.

You can prevent an instance from being terminated accidentally by someone using the AWS Management Console, the CLI, and the API\. This feature is available for both Amazon EC2 instance store\-backed and Amazon EBS\-backed instances\. Each instance has a `DisableApiTermination` attribute with the default value of `false` \(the instance can be terminated through Amazon EC2\)\. You can modify this instance attribute while the instance is running or stopped \(in the case of Amazon EBS\-backed instances\)\. For more information, see [Enable termination protection](#Using_ChangingDisableAPITermination)\.

You can control whether an instance should stop or terminate when shutdown is initiated from the instance using an operating system command for system shutdown\. For more information, see [Change the instance initiated shutdown behavior](#Using_ChangingInstanceInitiatedShutdownBehavior)\.

If you run a script on instance termination, your instance might have an abnormal termination, because we have no way to ensure that shutdown scripts run\. Amazon EC2 attempts to shut an instance down cleanly and run any system shutdown scripts; however, certain events \(such as hardware failure\) may prevent these system shutdown scripts from running\.

## Terminating multiple instances with termination protection across Availability Zones<a name="terminate-multiple"></a>

If you terminate multiple instances across multiple Availability Zones, and one or more of the specified instances are enabled for termination protection, the request fails with the following results:
+ The specified instances that are in the same Availability Zone as the protected instance are not terminated\.
+ The specified instances that are in different Availability Zones, where no other specified instances are protected, are successfully terminated\.

For example, say you have the following instances:

[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html)

If you attempt to terminate all of these instances in the same request, the request reports failure with the following results:
+ **Instance A** and **Instance B** are successfully terminated because none of the specified instances in `us-east-1a` are enabled for termination protection\.
+ **Instance C** and **Instance D** fail to terminate because at least one of the specified instances in `us-east-1b` \(**Instance C**\) is enabled for termination protection\.

## What happens when you terminate an instance<a name="what-happens-terminate"></a>

When an EC2 instance is terminated using the `terminate-instances` command, the following is registered at the OS level:
+ The API request will send a button press event to the guest\.
+ Various system services will be stopped as a result of the button press event\. **systemd** handles a graceful shutdown of the system\. Graceful shutdown is triggered by the ACPI shutdown button press event from the hypervisor\.
+ ACPI shutdown will be initiated\.
+ The instance will shut down when the graceful shutdown process exits\. There is no configurable OS shutdown time\. 

## Terminate an instance<a name="terminating-instances-console"></a>

You can terminate an instance using the AWS Management Console or the command line\.

By default, when you initiate a shutdown from an Amazon EBS\-backed instance \(using the shutdown or poweroff commands\), the instance stops\. The halt command does not initiate a shutdown\. If used, the instance does not terminate; instead, it places the CPU into `HLT` and the instance remains running\.

------
#### [ New console ]

**To terminate an instance using the console**

1. Before you terminate an instance, verify that you won't lose any data by checking that your Amazon EBS volumes won't be deleted on termination and that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and choose **Instance state**, **Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

------
#### [ Old console ]

**To terminate an instance using the console**

1. Before you terminate an instance, verify that you won't lose any data by checking that your Amazon EBS volumes won't be deleted on termination and that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and choose **Actions**, **Instance State**, **Terminate**\.

1. Choose **Yes, Terminate** when prompted for confirmation\.

------

**To terminate an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) \(AWS CLI\)
+ [Remove\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

**To run a controlled fault injection experiment**  
You can use AWS Fault Injection Simulator to test how your application responds when your instance is terminated\. For more information, see the [AWS Fault Injection Simulator User Guide](https://docs.aws.amazon.com/fis/latest/userguide)\.

## Enable termination protection<a name="Using_ChangingDisableAPITermination"></a>

By default, you can terminate your instance using the Amazon EC2 console, command line interface, or API\. To prevent your instance from being accidentally terminated using Amazon EC2, you can enable *termination protection* for the instance\. The `DisableApiTermination` attribute controls whether the instance can be terminated using the console, CLI, or API\. By default, termination protection is disabled for your instance\. You can set the value of this attribute when you launch the instance, while the instance is running, or while the instance is stopped \(for Amazon EBS\-backed instances\)\. 

The `DisableApiTermination` attribute does not prevent you from terminating an instance by initiating shutdown from the instance \(using an operating system command for system shutdown\) when the `InstanceInitiatedShutdownBehavior` attribute is set\. For more information, see [Change the instance initiated shutdown behavior](#Using_ChangingInstanceInitiatedShutdownBehavior)\.

**Limitations**  
You can't enable termination protection for Spot Instances—a Spot Instance is terminated when the Spot price exceeds the amount you're willing to pay for Spot Instances\. However, you can prepare your application to handle Spot Instance interruptions\. For more information, see [Spot Instance interruptions](spot-interruptions.md)\.

The `DisableApiTermination` attribute does not prevent Amazon EC2 Auto Scaling from terminating an instance\. For instances in an Auto Scaling group, use the following Amazon EC2 Auto Scaling features instead of Amazon EC2 termination protection:
+ To prevent instances that are part of an Auto Scaling group from terminating on scale in, use instance scale\-in protection\. For more information, see [Using instance scale\-in protection](https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-instance-protection.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ To prevent Amazon EC2 Auto Scaling from terminating unhealthy instances, suspend the `ReplaceUnhealthy` process\. For more information, see [Suspending and Resuming Scaling Processes](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-suspend-resume-processes.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ To specify which instances Amazon EC2 Auto Scaling should terminate first, choose a termination policy\. For more information, see [Customizing the Termination Policy](https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-instance-termination.html#custom-termination-policy) in the *Amazon EC2 Auto Scaling User Guide*\.

**To enable termination protection for an instance at launch time**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch Instance** and follow the directions in the wizard\.

1. On the **Configure Instance Details** page, select the **Enable termination protection** check box\.

**To enable termination protection for a running or stopped instance**

1. Select the instance, and choose **Actions**, **Instance Settings**, **Change Termination Protection**\.

1. Choose **Yes, Enable**\.

**To disable termination protection for a running or stopped instance**

1. Select the instance, and choose **Actions**, **Instance Settings**, **Change Termination Protection**\.

1. Choose **Yes, Disable**\.

**To enable or disable termination protection using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Change the instance initiated shutdown behavior<a name="Using_ChangingInstanceInitiatedShutdownBehavior"></a>

By default, when you initiate a shutdown from an Amazon EBS\-backed instance \(using a command such as shutdown or poweroff\), the instance stops \(Note that halt does not issue a poweroff command and, if used, the instance will not terminate; instead, it will place the CPU into HLT and the instance will remain running\)\. You can change this behavior using the `InstanceInitiatedShutdownBehavior` attribute for the instance so that it terminates instead\. You can update this attribute while the instance is running or stopped\. 

You can update the `InstanceInitiatedShutdownBehavior` attribute using the Amazon EC2 console or the command line\. The `InstanceInitiatedShutdownBehavior` attribute only applies when you perform a shutdown from the operating system of the instance itself; it does not apply when you stop an instance using the `StopInstances` API or the Amazon EC2 console\.

**To change the shutdown behavior of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. Choose **Actions**, **Instance settings**, **Change shutdown behavior**\. The current behavior is selected\.

1. To change the behavior, select **Stop** or **Terminate** from **Shutdown behavior** and then choose **Apply**\.

**To change the shutdown behavior of an instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Preserve Amazon EBS volumes on instance termination<a name="preserving-volumes-on-termination"></a>

When an instance terminates, Amazon EC2 uses the value of the `DeleteOnTermination` attribute for each attached Amazon EBS volume to determine whether to preserve or delete the volume\.

The default value for the `DeleteOnTermination` attribute differs depending on whether the volume is the root volume of the instance or a non\-root volume attached to the instance\.

Root volume  
By default, the `DeleteOnTermination` attribute for the root volume of an instance is set to `true`\. Therefore, the default is to delete the root volume of the instance when the instance terminates\. The `DeleteOnTermination` attribute can be set by the creator of an AMI as well as by the person who launches an instance\. When the attribute is changed by the creator of an AMI or by the person who launches an instance, the new setting overrides the original AMI default setting\. We recommend that you verify the default setting for the `DeleteOnTermination` attribute after you launch an instance with an AMI\.

Non\-root volume  
By default, when you [attach a non\-root EBS volume to an instance](ebs-attaching-volume.md), its `DeleteOnTermination` attribute is set to `false`\. Therefore, the default is to preserve these volumes\. After the instance terminates, you can take a snapshot of the preserved volume or attach it to another instance\. You must delete a volume to avoid incurring further charges\. For more information, see [Delete an Amazon EBS volume](ebs-deleting-volume.md)\.

To verify the value of the `DeleteOnTermination` attribute for an EBS volume that is in use, look at the instance's block device mapping\. For more information, see [View the EBS volumes in an instance block device mapping](block-device-mapping-concepts.md#view-instance-bdm)\.

You can change the value of the `DeleteOnTermination` attribute for a volume when you launch the instance or while the instance is running\.

**Topics**
+ [Change the root volume to persist at launch using the console](#delete-on-termination-console)
+ [Change the root volume to persist at launch using the command line](#delete-on-termination-cli)
+ [Change the root volume of a running instance to persist using the command line](#delete-on-termination-running-instance)

### Change the root volume to persist at launch using the console<a name="delete-on-termination-console"></a>

Using the console, you can change the `DeleteOnTermination` attribute when you launch an instance\. To change this attribute for a running instance, you must use the command line\.

**To change the root volume of an instance to persist at launch using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console dashboard, select **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, choose an AMI and choose **Select**\.

1. Follow the wizard to complete the **Choose an Instance Type** and **Configure Instance Details** pages\.

1. On the **Add Storage** page, deselect the **Delete On Termination** check box for the root volume\.

1. Complete the remaining wizard pages, and then choose **Launch**\.

In the new console experience, you can verify the setting by viewing details for the root device volume on the instance's details pane\. On the **Storage** tab, under **Block devices**, scroll right to view the **Delete on termination** setting for the volume\. By default, **Delete on termination** is `Yes`\. If you change the default behavior, **Delete on termination** is `No`\.

In the old console experience, you can verify the setting by viewing details for the root device volume on the instance's details pane\. Next to **Block devices**, choose the entry for the root device volume\. By default, **Delete on termination** is `True`\. If you change the default behavior, **Delete on termination** is `False`\.

### Change the root volume to persist at launch using the command line<a name="delete-on-termination-cli"></a>

When you launch an EBS\-backed instance, you can use one of the following commands to change the root device volume to persist\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
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

### Change the root volume of a running instance to persist using the command line<a name="delete-on-termination-running-instance"></a>

You can use one of the following commands to change the root device volume of a running EBS\-backed instance to persist\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
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

## Troubleshoot instance termination<a name="troubleshoot-instance-terminate"></a>

If you terminate your instance and another instance starts, most likely you have configured automatic scaling through a feature like EC2 Fleet or Amazon EC2 Auto Scaling\.

If your instance is in the `shutting-down` state for longer than usual, it should be cleaned up \(terminated\) by automated processes within the Amazon EC2 service\. For more information, see [Delayed instance termination](TroubleshootingInstancesShuttingDown.md#instance-stuck-terminating)\. 
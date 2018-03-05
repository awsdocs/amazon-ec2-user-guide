# Stop and Start Your Instance<a name="Stop_Start"></a>

You can stop and restart your instance if it has an Amazon EBS volume as its root device\. The instance retains its instance ID, but can change as described in the [Overview](#instance_stop) section\.

When you stop an instance, we shut it down\. We don't charge usage for a stopped instance, or data transfer fees, but we do charge for the storage for any Amazon EBS volumes\. Each time you start a stopped instance we charge a minimum of one minute for usage\. After one minute, we charge only for the seconds you use\. For example, if you run an instance for 20 seconds and then stop it, we charge for a full one minute\. If you run an instance for 3 minutes and 40 seconds, we charge for exactly 3 minutes and 40 seconds of usage\.

While the instance is stopped, you can treat its root volume like any other volume, and modify it \(for example, repair file system problems or update software\)\. You just detach the volume from the stopped instance, attach it to a running instance, make your changes, detach it from the running instance, and then reattach it to the stopped instance\. Make sure that you reattach it using the storage device name that's specified as the root device in the block device mapping for the instance\.

If you decide that you no longer need an instance, you can terminate it\. As soon as the state of an instance changes to `shutting-down` or `terminated`, we stop charging for that instance\. For more information, see [Terminate Your Instance](terminating-instances.md)\.


+ [Overview](#instance_stop)
+ [Stopping and Starting Your Instances](#starting-stopping-instances)
+ [Modifying a Stopped Instance](#Using_ChangingAttributesWhileInstanceStopped)
+ [Troubleshooting](#troubleshoot-instance-stop)

## Overview<a name="instance_stop"></a>

You can only stop an Amazon EBS\-backed instance\. To verify the root device type of your instance, describe the instance and check whether the device type of its root volume is `ebs` \(Amazon EBS\-backed instance\) or `instance store` \(instance store\-backed instance\)\. For more information, see [Determining the Root Device Type of Your AMI](ComponentsAMIs.md#display-ami-root-device-type)\.

When you stop a running instance, the following happens:

+ The instance performs a normal shutdown and stops running; its status changes to `stopping` and then `stopped`\.

+ Any Amazon EBS volumes remain attached to the instance, and their data persists\.

+ Any data stored in the RAM of the host computer or the instance store volumes of the host computer is gone\.

+ In most cases, the instance is migrated to a new underlying host computer when it's started\.

+ EC2\-Classic: We release the public and private IPv4 addresses for the instance when you stop the instance, and assign new ones when you restart it\.

  EC2\-VPC: The instance retains its private IPv4 addresses and any IPv6 addresses when stopped and restarted\. We release the public IPv4 address and assign a new one when you restart it\.

+ EC2\-Classic: We disassociate any Elastic IP address that's associated with the instance\. You're charged for Elastic IP addresses that aren't associated with an instance\. When you restart the instance, you must associate the Elastic IP address with the instance; we don't do this automatically\.

  EC2\-VPC: The instance retains its associated Elastic IP addresses\. You're charged for any Elastic IP addresses associated with a stopped instance\.

+ When you stop and start a Windows instance, the EC2Config service performs tasks on the instance, such as changing the drive letters for any attached Amazon EBS volumes\. For more information about these defaults and how you can change them, see [Configuring a Windows Instance Using the EC2Config Service](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/UsingConfig_WinAMI.html) in the *Amazon EC2 User Guide for Windows Instances*\.

+ If your instance is in an Auto Scaling group, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance\. For more information, see [Health Checks for Auto Scaling Instances](http://docs.aws.amazon.com/autoscaling/latest/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\.

+ When you stop a ClassicLink instance, it's unlinked from the VPC to which it was linked\. You must link the instance to the VPC again after restarting it\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

For more information, see [Differences Between Reboot, Stop, and Terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

You can modify the following attributes of an instance only when it is stopped:

+ Instance type

+ User data

+ Kernel

+ RAM disk

If you try to modify these attributes while the instance is running, Amazon EC2 returns the `IncorrectInstanceState` error\.

## Stopping and Starting Your Instances<a name="starting-stopping-instances"></a>

You can start and stop your Amazon EBS\-backed instance using the console or the command line\.

By default, when you initiate a shutdown from an Amazon EBS\-backed instance \(using the shutdown or poweroff command\), the instance stops\. You can change this behavior so that it terminates instead\. For more information, see [Changing the Instance Initiated Shutdown Behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.

**To stop and start an Amazon EBS\-backed instance using the console**

1. In the navigation pane, choose **Instances**, and select the instance\.

1. \[EC2\-Classic\] If the instance has an associated Elastic IP address, write down the Elastic IP address and the instance ID shown in the details pane\.

1. Choose **Actions**, select **Instance State**, and then choose **Stop**\. If **Stop** is disabled, either the instance is already stopped or its root device is an instance store volume\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. Therefore, if you have any data on instance store volumes that you want to keep, be sure to back it up to persistent storage\.

1. In the confirmation dialog box, choose **Yes, Stop**\. It can take a few minutes for the instance to stop\.

   \[EC2\-Classic\] When the instance state becomes `stopped`, the **Elastic IP**, **Public DNS \(IPv4\)**, **Private DNS**, and **Private IPs** fields in the details pane are blank to indicate that the old values are no longer associated with the instance\.

1. While your instance is stopped, you can modify certain instance attributes\. For more information, see [Modifying a Stopped Instance](#Using_ChangingAttributesWhileInstanceStopped)\.

1. To restart the stopped instance, select the instance, and choose **Actions**, **Instance State**, **Start**\.

1. In the confirmation dialog box, choose **Yes, Start**\. It can take a few minutes for the instance to enter the `running` state\.

   \[EC2\-Classic\] When the instance state becomes `running`, the **Public DNS \(IPv4\)**, **Private DNS**, and **Private IPs** fields in the details pane contain the new values that we assigned to the instance\.

1. \[EC2\-Classic\] If your instance had an associated Elastic IP address, you must re\-associate it as follows:

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Select the Elastic IP address that you wrote down before you stopped the instance\.

   1. Choose **Actions**, and then select **Associate address**\.

   1. Select the instance ID that you wrote down before you stopped the instance, and then choose **Associate**\.

**To stop and start an Amazon EBS\-backed instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) and [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\)

+ [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) and [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Modifying a Stopped Instance<a name="Using_ChangingAttributesWhileInstanceStopped"></a>

You can change the instance type, user data, and EBS\-optimization attributes of a stopped instance using the AWS Management Console or the command line interface\. You can't use the AWS Management Console to modify the `DeleteOnTermination`, kernel, or RAM disk attributes\.

**To modify an instance attribute**

+ To change the instance type, see [Resizing Your Instance](ec2-instance-resize.md)\.

+ To change the user data for your instance, see [Configuring Instances with User Data](ec2-instance-metadata.md#instancedata-add-user-data)\.

+ To enable or disable EBS–optimization for your instance, see [Modifying EBS–Optimization](EBSOptimized.md#modify-ebs-optimized-attribute)\.

+ To change the `DeleteOnTermination` attribute of the root volume for your instance, see [Updating the Block Device Mapping of a Running Instance](block-device-mapping-concepts.md#update-instance-bdm)\.

**To modify an instance attribute using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.

+ [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

+ [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Troubleshooting<a name="troubleshoot-instance-stop"></a>

If you have stopped your Amazon EBS\-backed instance and it appears "stuck" in the `stopping` state, you can forcibly stop it\. For more information, see [Troubleshooting Stopping Your Instance](TroubleshootingInstancesStopping.md)\.
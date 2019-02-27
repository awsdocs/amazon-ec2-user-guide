# Hibernate Your Instance<a name="Hibernate"></a>

When you hibernate an instance, we signal the operating system to perform hibernation \(suspend\-to\-disk\), which saves the contents from the instance memory \(RAM\) to your Amazon EBS root volume\. We persist the instance's Amazon EBS root volume and any attached Amazon EBS data volumes\. When you restart your instance, the Amazon EBS root volume is restored to its previous state, the RAM contents are reloaded, and the processes that were previously running on the instance are resumed\. Previously attached data volumes are reattached and the instance retains its instance ID\. 

You can hibernate an instance only if it's [enabled for hibernation](#enabling-hibernation) and it meets the [hibernation prerequisites](#hibernating-prerequisites)\. Hibernation is currently supported only for Amazon Linux\.

If an instance or application takes a long time to bootstrap and build a memory footprint to become fully productive, you can use hibernation to "pre\-warm" the instance\. To "pre\-warm" the instance, launch it, bring it to a desired state, and then hibernate it, ready to be resumed to the same state as needed\.

We don't charge usage for a hibernated instance when it is in the `stopped` state\. We do charge for instance usage while the instance is in the `stopping` state \(unlike when you [stop an instance](Stop_Start.md) without hibernating it\) when the contents of the RAM are transferred to the Amazon EBS root volume\. We don't charge usage for data transfer fees, but we do charge for the storage for any Amazon EBS volumes, including storage for the RAM contents\.

If you no longer need an instance, you can terminate it at any time, including when it is in a `stopped` \(hibernated\) state\. For more information, see [Terminate Your Instance](terminating-instances.md)\.

**Important**  
Hibernation is currently not supported on Windows instances\.

**Topics**
+ [Overview of Hibernation](#instance_hibernate)
+ [Hibernation Prerequisites](#hibernating-prerequisites)
+ [Limitations](#hibernating-not-supported)
+ [Configuring an Existing AMI to Support Hibernation](#hibernation-enabled-AMI)
+ [Enabling Hibernation for an Instance](#enabling-hibernation)
+ [Hibernating an Instance](#hibernating-instances)
+ [Restarting a Hibernated Instance](#hibernating-resuming)
+ [Troubleshooting Hibernation](#troubleshoot-instance-hibernate)

## Overview of Hibernation<a name="instance_hibernate"></a>

The following diagram shows a basic overview of the hibernation process\.

![\[Overview of the hibernation flow\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/hibernation-flow.png)

When you hibernate a running instance, the following happens:
+ When you initiate hibernation, the instance moves to the `stopping` state\. We signal the operating system to perform hibernation \(suspend\-to\-disk\), which freezes all the processes, saves the contents of the RAM to the Amazon EBS root volume, and then performs a regular shutdown\. 
+ After the shutdown is complete, the instance moves to the `stopped` state\.
+ Any Amazon EBS volumes remain attached to the instance, and their data persists, including the saved contents of the RAM\.
+ In most cases, the instance is migrated to a new underlying host computer when it's restarted, which is the same as what happens when you stop and restart an instance\.
+ When you restart the instance, the instance boots up and the operating system reads in the contents of the RAM from the Amazon EBS root volume before unfreezing processes to resume its state\.
+ The instance retains its private IPv4 addresses and any IPv6 addresses when hibernated and restarted\. We release the public IPv4 address and assign a new one when you restart it\.
+ The instance retains its associated Elastic IP addresses\. You're charged for any Elastic IP addresses associated with a hibernated instance\. With EC2\-Classic, an Elastic IP address is dissociated from your instance when you hibernate it\. For more information, see [EC2\-Classic](ec2-classic-platform.md)\.
+ When you hibernate a ClassicLink instance, it's unlinked from the VPC to which it was linked\. You must link the instance to the VPC again after restarting it\. For more information, see [ClassicLink](vpc-classiclink.md)\.

For information about how hibernation differs from reboot, stop, and terminate, see [Differences Between Reboot, Stop, Hibernate, and Terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

## Hibernation Prerequisites<a name="hibernating-prerequisites"></a>

To hibernate an instance, the following prerequisites must be in place:
+ **Instance families:** The following instance families are supported: C3, C4, C5, M3, M4, M5, R3, R4, and R5, with less than 150 GB of RAM\. Hibernation is not supported for \*\.metal instances\.
+ **Instance RAM size:** The instance RAM size must be less than 150 GB\.
+ **Supported AMIs:** The following AMIs support hibernation: Amazon Linux AMI 2018\.03 released 2018\.11\.16 or later\.

  Support for Amazon Linux 2 is coming soon\. Only HVM AMIs support hibernation\. To configure your own AMI to support hibernation, see [Configuring an Existing AMI to Support Hibernation](#hibernation-enabled-AMI)\.
+ **Root volume type: **The instance root volume must be an Amazon EBS volume, not an instance store volume\.
+ **Amazon EBS root volume size:** The root volume must be large enough to store the RAM contents and accommodate your expected usage, for example, OS or applications\. If you enable hibernation, space is allocated on the root volume at launch to store the RAM\.
+ **Amazon EBS root volume encryption:** To use hibernation, the root volume must be encrypted to ensure the protection of sensitive content that is in memory at the time of hibernation\. When RAM data is moved to the Amazon EBS root volume, it is always encrypted\. Encryption of the root volume is enforced at instance launch\. To ensure that the root volume is an encrypted Amazon EBS volume, the AMI that you use for launching your instance must be encrypted\. For more information, see [Creating an AMI with Encrypted Root Snapshot from an Unencrypted AMI](AMIEncryption.md#create-ami-encrypted-root-snapshot)\.
+ **Enable hibernation at launch:** At launch, enable hibernation using the Amazon EC2 console or the AWS CLI\. You cannot enable hibernation on an existing instance \(running or stopped\)\. For more information, see [Enabling Hibernation for an Instance](#enabling-hibernation)\.
+ **Purchasing options:** This feature is only available for On\-Demand Instances and Reserved Instances\. For more information, see [Hibernating Interrupted Spot Instances](spot-interruptions.md#hibernate-spot-instances)\.

## Limitations<a name="hibernating-not-supported"></a>

The following actions are not supported for hibernation:
+ Changing the instance type or size of a hibernated instance
+ Creating snapshots or AMIs from instances for which hibernation is enabled
+ Creating snapshots or AMIs from hibernated instances

You can't stop or hibernate instance store\-backed instances\.\*

You can't hibernate an instance that has more than 150 GB of RAM\.

You cannot hibernate an instance that is in an Auto Scaling group or used by Amazon ECS\. If your instance is in an Auto Scaling group and you try to hibernate it, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance\. For more information, see [Health Checks for Auto Scaling Instances](https://docs.aws.amazon.com/autoscaling/latest/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\.

We do not support keeping an instance hibernated for more than 60 days\. To keep the instance for longer than 60 days, you must restart the hibernated instance, stop the instance, and restart it\.

We constantly update our platform with upgrades and security patches, which can conflict with existing hibernated instances\. We notify you about critical updates that require a restart for hibernated instances so that we can perform a shutdown or a reboot to apply the necessary upgrades and security patches\.

\*For C3 and R3 instances that are enabled for hibernation, do not use instance store volumes\.

## Configuring an Existing AMI to Support Hibernation<a name="hibernation-enabled-AMI"></a>

To hibernate an instance that was launched using your own AMI, you must first configure your AMI to support hibernation\. For more information, see [Updating Instance Software](install-updates.md)\.

If you use one of the [supported AMIs](#hibernating-prerequisites), or you create an AMI based on one of the [supported AMIs](#hibernating-prerequisites), you do not need to configure it to support hibernation\. The supported AMIs come preconfigured to support hibernation\.

**To configure an Amazon Linux AMI to support hibernation \(AWS CLI\)**

1. Update to the latest kernel to 4\.14\.77\-70\.59 or later using the following command:

   ```
   sudo yum update kernel
   ```

1. Install the `ec2-hibinit-agent` package from the repositories using the following command:

   ```
   sudo yum install ec2-hibinit-agent
   ```

1. Reboot the instance\.

1. Confirm that the kernel version is updated to 4\.14\.77\-70\.59 or greater using the following command:

   ```
   uname -a
   ```

1. Stop the instance and create an AMI\. For more information, see [Creating a Linux AMI from an Instance](creating-an-ami-ebs.md#how-to-create-ebs-ami)\.

## Enabling Hibernation for an Instance<a name="enabling-hibernation"></a>

To hibernate an instance, it must first be enabled for hibernation\. At launch, enable hibernation using the console or the command line\. You cannot enable hibernation for an existing instance \(running or stopped\)\.

**To enable hibernation \(console\)**

1. Follow the [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI that supports hibernation\. For more information about supported AMIs, see [Hibernation Prerequisites](#hibernating-prerequisites)\.

1. On the **Choose an Instance Type** page, select a supported instance type, and choose **Next: Configure Instance Details**\. For information about supported instance types, see [Hibernation Prerequisites](#hibernating-prerequisites)\.

1. On the **Configure Instance Details** page, for **Stop \- Hibernate Behavior**, select the **Enable hibernation as an additional stop behavior** check box\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To enable hibernation \(AWS CLI\)**
+ Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch an instance\. Enable hibernation using the `--hibernation-options Configured=true` parameter\.

  ```
  aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type m5.large --key-name MyKeyPair --hibernation-options Configured=true
  ```

**To view if an instance is enabled for hibernation \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, in the details pane, inspect **Stop \- Hibernation behavior**\. **Enabled** indicates that the instance is enabled for hibernation\.
**Note**  
You can't enable or disable hibernation after launch\.

**To view if an instance is enabled for hibernation \(AWS CLI\)**
+ Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--filters "Name=hibernation-options.configured,Values=true"` parameter to filter instances that are enabled for hibernation\.

  ```
  aws --region us-east-1 ec2 describe-instances --filters "Name=hibernation-options.configured,Values=true"
  ```

  The following field in the output indicates that the instance is enabled for hibernation:

  ```
  "HibernationOptions": {
      "Configured": true
  }
  ```

## Hibernating an Instance<a name="hibernating-instances"></a>

You can hibernate an instance using the console or the command line if the instance is [enabled for hibernation](#enabling-hibernation) and meets the [hibernation prerequisites](#hibernating-prerequisites)\. If an instance cannot hibernate successfully, a normal shutdown occurs\.

**To hibernate an Amazon EBS\-backed instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select an instance, and choose **Actions**, **Instance State**, **Stop \- Hibernate**\. If **Stop \- Hibernate** is disabled, the instance is already hibernated or stopped, or it can't be hibernated\. For more information, see [Hibernation Prerequisites](#hibernating-prerequisites)\.

1. In the confirmation dialog box, choose **Yes, Stop \- Hibernate**\. It can take a few minutes for the instance to hibernate\. The **Instance State** changes to **Stopping** while the instance is hibernating, and then **Stopped** when the instance has hibernated\.

**To hibernate an Amazon EBS\-backed instance \(AWS CLI\)**
+ Use the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command and specify the `--hibernate` parameter\.

  ```
  aws ec2 stop-instances --instance-ids i-1234567890abcdef0 --hibernate
  ```

**To view if hibernation was initiated on an instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, in the details pane, inspect **State transition reason message**\. **Client\.UserInitiatedHibernate: User initiated hibernate** indicates that hibernation was initiated on the instance\.

**To view if hibernation was initiated on an instance \(AWS CLI\)**
+ Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--filters "Name=state-reason-code,Values=Client.UserInitiatedHibernate"` parameter to filter instances on which hibernation was initiated\.

  ```
  aws --region us-east-1 ec2 describe-instances --filters "Name=state-reason-code,Values=Client.UserInitiatedHibernate"
  ```

  The following field in the output indicates that hibernation was initiated on the instance\.

  ```
  "StateReason": {
      "Code": Client.UserInitiatedHibernate
  }
  ```

## Restarting a Hibernated Instance<a name="hibernating-resuming"></a>

Restart a hibernated instance by starting it in the same way that you would start a stopped instance\.

**To restart a hibernated instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select a hibernated instance, and choose **Actions**, **Instance State**, **Start**\. It can take a few minutes for the instance to enter the `running` state\. During this time, the instance [status checks](monitoring-system-instance-status-check.md#types-of-instance-status-checks) show the instance in a failed state until the instance has restarted\.

**To restart a hibernated instance \(AWS CLI\)**
+ Use the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) command\.

## Troubleshooting Hibernation<a name="troubleshoot-instance-hibernate"></a>

Use this information to help you diagnose and fix issues that you might encounter when hibernating an instance\.

### Can't hibernate immediately after launch<a name="hibernate-troubleshooting-1"></a>

If you try to hibernate an instance too quickly after you've launched it, you will get an error\.

You must wait for about two minutes after launch before hibernating\.

### Takes too long to transition from `stopping` to `stopped`, and memory state not restored after start<a name="hibernate-troubleshooting-2"></a>

If it takes a long time for your hibernating instance to transition from the `stopping` state to `stopped`, and the memory state is not restored after you start, this could indicate that hibernation was not properly configured\.

Check the instance system log and look for messages that are related to hibernation\. To access the system log, [connect](AccessingInstances.md) to the instance or use the [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) command\. Find log lines from the `hibinit-agent`\. If the log lines indicate a failure or the log lines are missing, there was most likely a failure configuring hibernation at launch\.

For example, the following message indicates that the instance root volume is not large enough: `hibinit-agent: Insufficient disk space. Cannot create setup for hibernation. Please allocate a larger root device.`

If the last log line from the `hibinit-agent` is `hibinit-agent: Running: swapoff /swap`, hibernation was successfully configured\.

If you do not see any logs from these processes, your AMI might not support hibernation\. For information about supported AMIs, see [Hibernation Prerequisites](#hibernating-prerequisites)\. If you used your own AMI, make sure that you followed the instructions for [Configuring an Existing AMI to Support Hibernation](#hibernation-enabled-AMI)\.

### Instance "stuck" in the `stopping` state<a name="hibernate-troubleshooting-3"></a>

If you hibernated your instance and it appears "stuck" in the `stopping` state, you can forcibly stop it\. For more information, see [Troubleshooting Stopping Your Instance](TroubleshootingInstancesStopping.md)\.
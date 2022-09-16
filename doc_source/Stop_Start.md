# Stop and start your instance<a name="Stop_Start"></a>

You can stop and start your instance if it has an Amazon EBS volume as its root device\. The instance retains its instance ID, but can change as described in the [Overview](#instance_stop) section\.

When you stop an instance, we shut it down\. We don't charge usage for a stopped instance, or data transfer fees, but we do charge for the storage for any Amazon EBS volumes\. Each time you start a stopped instance we charge a minimum of one minute for usage\. After one minute, we charge only for the seconds you use\. For example, if you run an instance for 20 seconds and then stop it, we charge for a full one minute\. If you run an instance for 3 minutes and 40 seconds, we charge for exactly 3 minutes and 40 seconds of usage\.

While the instance is stopped, you can treat its root volume like any other volume, and modify it \(for example, repair file system problems or update software\)\. You just detach the volume from the stopped instance, attach it to a running instance, make your changes, detach it from the running instance, and then reattach it to the stopped instance\. Make sure that you reattach it using the storage device name that's specified as the root device in the block device mapping for the instance\.

If you decide that you no longer need an instance, you can terminate it\. As soon as the state of an instance changes to `shutting-down` or `terminated`, we stop charging for that instance\. For more information, see [Terminate your instance](terminating-instances.md)\. If you'd rather hibernate the instance, see [Hibernate your On\-Demand Linux instance](Hibernate.md)\. For more information, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

**Topics**
+ [Overview](#instance_stop)
+ [What happens when you stop an instance](#what-happens-stop)
+ [What happens when you start an instance](#what-happens-start)
+ [Stop and start your instances](#starting-stopping-instances)
+ [Stop and start your instances on a schedule](#stop-start-ec2-instances-on-a-schedule)
+ [Enable stop protection](#Using_StopProtection)
+ [Modify a stopped instance](#Using_ChangingAttributesWhileInstanceStopped)
+ [Troubleshoot stopping your instance](#troubleshoot-instance-stop)

## Overview<a name="instance_stop"></a>

You can only stop an Amazon EBS\-backed instance\. To verify the root device type of your instance, describe the instance and check whether the device type of its root volume is `ebs` \(Amazon EBS\-backed instance\) or `instance store` \(instance store\-backed instance\)\. For more information, see [Determine the root device type of your AMI](ComponentsAMIs.md#display-ami-root-device-type)\.

You can modify the following attributes of an instance only when it is stopped:
+ Instance type
+ User data
+ Kernel
+ RAM disk

If you try to modify these attributes while the instance is running, Amazon EC2 returns the `IncorrectInstanceState` error\.

## What happens when you stop an instance<a name="what-happens-stop"></a>

When you stop an EC2 instance by using the StopInstances API \(for example, by choosing **Instance state**, **Stop instance** in the Amazon EC2 console, or by using the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) AWS CLI command\), the following is registered at the OS level:
+ The API request sends a button press event to the guest\.
+ Various system services are stopped as a result of the button press event\. Graceful shutdown is triggered by the ACPI shutdown button press event from the hypervisor\.
+ ACPI shutdown is initiated\.
+ The instance shuts down when the graceful shutdown process exits\. There is no configurable OS shutdown time\.
+ If the instance OS does not shut down cleanly within a few minutes, a hard shutdown is performed\.
+ The instance stops running\.
+ The instance status changes to `stopping` and then `stopped`\.
+  \(Auto Scaling group\) If your instance is in an Auto Scaling group, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and might terminate it and launch a replacement instance\. For more information, see [Health checks for Auto Scaling instances](https://docs.aws.amazon.com/autoscaling/latest/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ \(Windows\) When you stop and start a Windows instance, the EC2Config service performs tasks on the instance, such as changing the drive letters for any attached Amazon EBS volumes\. For more information about these defaults and how you can change them, see [Configure a Windows instance using the EC2Config service](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2config-service.html) in the *Amazon EC2 User Guide for Windows Instances*\.
+ \(ClassicLink\) When you stop a ClassicLink instance, it's unlinked from the VPC to which it was linked\. You must link the instance to the VPC again after starting it\. For more information about ClassicLink, see [ClassicLink](vpc-classiclink.md)\.

By default, when you initiate a shutdown from an Amazon EBS\-backed instance \(for example, using the shutdown or poweroff command\), the instance stops\. You can change this behavior so that it terminates instead\. For more information, see [Change the instance initiated shutdown behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.

Using the halt command from an instance does not initiate a shutdown\. If used, the instance does not terminate; instead, it places the CPU into `HLT` and the instance remains running\.

**When you stop an instance, the following is *lost*:**
+ Data stored in the RAM\.
+ Data stored in the instance store volumes\.
+ The public IPv4 address that Amazon EC2 automatically assigned to the instance on launch or start\. \(To retain a public IPv4 address that never changes, you can associate an [Elastic IP address](elastic-ip-addresses-eip.md) with your instance\.\)
+ \(EC2\-Classic\) With EC2\-Classic, Elastic IP addresses are dissociated from your instance\. For more information, see [EC2\-Classic](ec2-classic-platform.md)\.

**When you stop an instance, the following *persists*:**
+ Data stored in the Amazon EBS volumes\. The EBS volumes remain attached to the instance\.
+ Private IPv4 addresses\.
+ IPv6 addresses\.
+ Elastic IP addresses associated with the instance\. Note that when the instance is stopped, we [start charging you for the associated Elastic IP addresses](elastic-ip-addresses-eip.md#eip-pricing)\.

For more information, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

## What happens when you start an instance<a name="what-happens-start"></a>

When you start an EC2 instance by using the StartInstances API \(for example, by choosing **Instance state**, **Start instance** in the Amazon EC2 console, or by using the [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) AWS CLI command\), the following happens:
+ In most cases, the instance is migrated to a new underlying host computer \(though in some cases, it remains on the current host\)\.
+ Amazon EC2 assigns a new public IPv4 address to the instance if the instance is configured to receive a public IPv4 address\. \(To retain a public IPv4 address that never changes, you can associate an [Elastic IP address](elastic-ip-addresses-eip.md) with your instance\.\)

For more information, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

## Stop and start your instances<a name="starting-stopping-instances"></a>

You can stop and start your Amazon EBS\-backed instance using the console or the command line\.

------
#### [ New console ]

**To stop and start an Amazon EBS\-backed instance using the console**

1. When you stop an instance, the data on any instance store volumes is erased\. Before you stop an instance, verify that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

1. In the navigation pane, choose **Instances** and select the instance\.

1. Choose **Instance state**, **Stop instance**\. If this option is disabled, either the instance is already stopped or its root device is an instance store volume\.

1. When prompted for confirmation, choose **Stop**\. It can take a few minutes for the instance to stop\.

1. \(Optional\) While your instance is stopped, you can modify certain instance attributes\. For more information, see [modify\-stopped\-instance](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html)\.

1. To start the stopped instance, select the instance, and choose **Instance state**, **Start instance**\.

1. It can take a few minutes for the instance to enter the `running` state\.

------
#### [ Old console ]

**To stop and start an Amazon EBS\-backed instance using the console**

1. When you stop an instance, the data on any instance store volumes is erased\. Before you stop an instance, verify that you've copied any data that you need from your instance store volumes to persistent storage, such as Amazon EBS or Amazon S3\.

1. In the navigation pane, choose **Instances** and select the instance\.

1. Choose **Actions**, **Instance State**, **Stop**\. If this option is disabled, either the instance is already stopped or its root device is an instance store volume\.

1. When prompted for confirmation, choose **Yes, Stop**\. It can take a few minutes for the instance to stop\.

1. \(Optional\) While your instance is stopped, you can modify certain instance attributes\. For more information, see [modify\-stopped\-instance](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html)\.

1. To start the stopped instance, select the instance, and choose **Actions**, **Instance State**, **Start**\.

1. In the confirmation dialog box, choose **Yes, Start**\. It can take a few minutes for the instance to enter the `running` state\.

------

**To stop and start an Amazon EBS\-backed instance using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) and [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\)
+ [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) and [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

**To run a controlled fault injection experiment**  
You can use AWS Fault Injection Simulator to test how your application responds when your instance is stopped and started\. For more information, see the [AWS Fault Injection Simulator User Guide](https://docs.aws.amazon.com/fis/latest/userguide)\.

## Stop and start your instances on a schedule<a name="stop-start-ec2-instances-on-a-schedule"></a>

You can schedule the stopping and starting of your EC2 instances\. The following are two options for configuring this process\.

**Use Instance Scheduler on AWS**  
You can use Instance Scheduler on AWS to automate the starting and stopping of EC2 instances\. For more information, see [How do I use Instance Scheduler with CloudFormation to schedule EC2 instances?](http://aws.amazon.com/premiumsupport/knowledge-center/stop-start-instance-scheduler/) Note that [additional charges apply](https://docs.aws.amazon.com/solutions/latest/instance-scheduler-on-aws/cost.html)\. 

**Use AWS Lambda and an Amazon EventBridge rule**  
You can use Lambda and an EventBridge rule to stop and start your instances on a schedule\. For more information, see [How do I stop and start Amazon EC2 instances at regular intervals using Lambda?](http://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-eventbridge/)

## Enable stop protection<a name="Using_StopProtection"></a>

By default, you can stop your instance using the Amazon EC2 console, command line interface, or API\. To prevent your instance from being accidentally stopped, you can enable stop protection for the instance\. Stop protection also protects your instance from accidental termination\. 

The `DisableApiStop` attribute controls whether the instance can be stopped using the Amazon EC2 console, AWS CLI, or API\. You can set the value of this attribute when you launch the instance, while the instance is running, or while the instance is stopped\.

The `DisableApiStop` attribute does not prevent you from stopping an instance by initiating shutdown from the instance \(using an operating system command for system shutdown\)\. 

**Considerations**
+ Enabling stop protection does not prevent AWS from stopping the instance when the instance has a [scheduled event](monitoring-instances-status-check_sched.md) that stops the instance\.
+ Stop protection not only prevents your instance from being accidentally stopped, but also from accidental termination when using the console, AWS CLI, or API\. However, it does not automatically change the `DisableApiTermination` attribute\. Note that when the `DisableApiStop` attribute is set to false, the `DisableApiTermination` attribute is used to determine if the instance can be terminated using the console, AWS CLI, or API\.
+ Enabling stop protection does not prevent Amazon EC2 Auto Scaling from terminating an instance when the instance is unhealthy or during scale\-in events\.
+ You cannot enable stop protection for instance store\-backed instances\.
+ You cannot enable stop protection for Spot Instances\.
+ The Amazon EC2 API follows an eventual consistency model when you enable or disable stop protection\. For more information, see [Eventual consistency](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/query-api-troubleshooting.html#eventual-consistency) in the *Amazon EC2 API Reference*\.

### Enable stop protection for an instance at launch<a name="enable-stop-protection-at-launch"></a>

You can enable stop protection for an instance when launching the instance using one of the following methods\.

------
#### [ New console ]

**To enable stop protection for an instance at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch instance**\.

1. Configure your instance in the [new launch instance wizard](ec2-launch-instance-wizard.md)\.

   To enable stop protection, under **Advanced details**, for **Stop protection**, choose **Enable**\.

------
#### [ Old console ]

**To enable stop protection for an instance at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the dashboard, choose **Launch instance**\.

1. Configure your instance in the [old launch instance wizard](launching-instance.md)\.

   To disable stop protection, on the **Configure Instance Details** page, for **Enable stop protection**, select the **Protect against accidental stoppage** check box\.

------
#### [ AWS CLI ]

**To enable stop protection for an instance at launch**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command to launch the instance, and specify the `disable-api-stop` parameter\. 

```
aws ec2 run-instances \
    --image-id ami-a1b2c3d4e5example \ 
    --instance-type t3.micro \ 
    --key-name MyKeyPair \ 
    --disable-api-stop \ 
    ...
```

------

### Enable stop protection for a running or stopped instance<a name="enable-stop-protection-on-running-or-stopped-instance"></a>

You can enable stop protection for an instance while the instance is running or stopped using one of the following methods\. Note that the *old* **Instances** console does not support enabling stop protection for a running or stopped instance\.

------
#### [ New console ]

**To enable stop protection for a running or stopped instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **Instances**\.

1. Select the instance, and then choose **Actions**, **Instance settings**, **Change stop protection**\.

1. Select the **Enable** check box, and then choose **Save**\.

------
#### [ AWS CLI ]

**To enable stop protection for a running or stopped instance**  
Use the [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) AWS CLI command and specify the `disable-api-stop` parameter\. 

```
aws ec2 modify-instance-attribute \
    --instance-id i-1234567890abcdef0 \
    --disable-api-stop
```

------

### Disable stop protection for a running or stopped instance<a name="disable-stop-protection-on-running-or-stopped-instance"></a>

You can disable stop protection for a running or stopped instance using one of the following methods\. Note that the *old* **Instances** console does not support disabling stop protection for a running or stopped instance\.

------
#### [ New console ]

**To disable stop protection for a running or stopped instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **Instances**\.

1. Select the instance, and then choose **Actions**, **Instance settings**, **Change stop protection**\.

1. Clear the **Enable** check box, and then choose **Save**\.

------
#### [ AWS CLI ]

**To disable stop protection for a running or stopped instance**  
Use the [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) AWS CLI command and specify the `no-disable-api-stop` parameter\. 

```
aws ec2 modify-instance-attribute \
    --instance-id i-1234567890abcdef0 \
    --no-disable-api-stop
```

------

## Modify a stopped instance<a name="Using_ChangingAttributesWhileInstanceStopped"></a>

You can change the instance type, user data, and EBS\-optimization attributes of a stopped instance using the AWS Management Console or the command line interface\. You can't use the AWS Management Console to modify the `DeleteOnTermination`, kernel, or RAM disk attributes\.

**To modify an instance attribute**
+ To change the instance type, see [Change the instance type](ec2-instance-resize.md)\.
+ To change the user data for your instance, see [Work with instance user data](instancedata-add-user-data.md)\.
+ To enable or disable EBS–optimization for your instance, see [Modifying EBS–Optimization](ebs-optimized.md#modify-ebs-optimized-attribute)\.
+ To change the `DeleteOnTermination` attribute of the root volume for your instance, see [Update the block device mapping of a running instance](block-device-mapping-concepts.md#update-instance-bdm)\. You are not required to stop the instance to change this attribute\. 

**To modify an instance attribute using the command line**

You can use one of the following commands\. For more information about these command line interfaces see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)
+ [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

## Troubleshoot stopping your instance<a name="troubleshoot-instance-stop"></a>

If you have stopped your Amazon EBS\-backed instance and it appears "stuck" in the `stopping` state, you can forcibly stop it\. For more information, see [Troubleshoot stopping your instance](TroubleshootingInstancesStopping.md)\.
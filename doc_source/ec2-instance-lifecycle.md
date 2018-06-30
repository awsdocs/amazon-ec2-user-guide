# Instance Lifecycle<a name="ec2-instance-lifecycle"></a>

By working with Amazon EC2 to manage your instances from the moment you launch them through their termination, you ensure that your customers have the best possible experience with the applications or sites that you host on your instances\.

The following illustration represents the transitions between instance states\. Notice that you can't stop and start an instance store\-backed instance\. For more information about instance store\-backed instances, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\.

![\[The instance lifecycle\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance_lifecycle.png)

The following table provides a brief description of each instance state and indicates whether it is billed or not\.

**Note**  
The table indicates billing for instance usage only\. Some AWS resources, such as Amazon EBS volumes and Elastic IP addresses, incur charges regardless of the instance's state\. For more information, see [Avoiding Unexpected Charges](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/checklistforunwantedcharges.html) in the *AWS Billing and Cost Management User Guide*\.


| Instance state | Description | Instance usage billing | 
| --- | --- | --- | 
|  `pending`  |  The instance is preparing to enter the `running` state\. An instance enters the `pending` state when it launches for the first time, or when it is restarted after being in the `stopped` state\.  |  Not billed  | 
|  `running`  |  The instance is running and ready for use\.  |  Billed  | 
|  `stopping`  |  The instance is preparing to be stopped\.  |  Not billed  | 
|  `stopped`  |  The instance is shut down and cannot be used\. The instance can be restarted at any time\.  |  Not billed  | 
|  `shutting-down`  |  The instance is preparing to be terminated\.  |  Not billed  | 
|  `terminated`  |  The instance has been permanently deleted and cannot be restarted\.  |  Not billed  | 

**Note**  
Rebooting an instance doesn't start a new instance billing period because the instance stays in the `running` state\.

## Instance Launch<a name="instance-launch"></a>

When you launch an instance, it enters the `pending` state\. The instance type that you specified at launch determines the hardware of the host computer for your instance\. We use the Amazon Machine Image \(AMI\) you specified at launch to boot the instance\. After the instance is ready for you, it enters the `running` state\. You can connect to your running instance and use it the way that you'd use a computer sitting in front of you\.

As soon as your instance transitions to the `running` state, you're billed for each second, with a one\-minute minimum, that you keep the instance running, even if the instance remains idle and you don't connect to it\.

For more information, see [Launch Your Instance](LaunchingAndUsingInstances.md) and [Connect to Your Linux Instance](AccessingInstances.md)\.

## Instance Stop and Start \(Amazon EBS\-Backed Instances Only\)<a name="instance-stop-start"></a>

If your instance fails a status check or is not running your applications as expected, and if the root volume of your instance is an Amazon EBS volume, you can stop and start your instance to try to fix the problem\.

When you stop your instance, it enters the `stopping` state, and then the `stopped` state\. We don't charge usage or data transfer fees for your instance after you stop it, but we do charge for the storage for any Amazon EBS volumes\. While your instance is in the `stopped` state, you can modify certain attributes of the instance, including the instance type\.

When you start your instance, it enters the `pending` state, and in most cases, we move the instance to a new host computer\. \(Your instance may stay on the same host computer if there are no problems with the host computer\.\) When you stop and start your instance, you lose any data on the instance store volumes on the previous host computer\.

If your instance is running in EC2\-Classic, it receives a new private IPv4 address, which means that an Elastic IP address associated with the private IPv4 address is no longer associated with your instance\. If your instance is running in EC2\-VPC, it retains its private IPv4 address, which means that an Elastic IP address associated with the private IPv4 address or network interface is still associated with your instance\. If your instance has an IPv6 address, it retains its IPv6 address\.

Each time you transition an instance from `stopped` to `running`, we charge per second when the instance is running, with a minimum of one minute every time you restart your instance\.

For more information, see [Stop and Start Your Instance](Stop_Start.md)\.

## Instance Reboot<a name="instance-reboot"></a>

You can reboot your instance using the Amazon EC2 console, a command line tool, and the Amazon EC2 API\. We recommend that you use Amazon EC2 to reboot your instance instead of running the operating system reboot command from your instance\.

Rebooting an instance is equivalent to rebooting an operating system; the instance remains on the same host computer and maintains its public DNS name, private IP address, and any data on its instance store volumes\. It typically takes a few minutes for the reboot to complete, but the time it takes to reboot depends on the instance configuration\.

Rebooting an instance doesn't start a new instance billing period; per second billing continues without a further one\-minute minimum charge\.

For more information, see [Reboot Your Instance](ec2-instance-reboot.md)\.

## Instance Retirement<a name="instance-retiring"></a>

An instance is scheduled to be retired when AWS detects irreparable failure of the underlying hardware hosting the instance\. When an instance reaches its scheduled retirement date, it is stopped or terminated by AWS\. If your instance root device is an Amazon EBS volume, the instance is stopped, and you can start it again at any time\. If your instance root device is an instance store volume, the instance is terminated, and cannot be used again\.

For more information, see [Instance Retirement](instance-retirement.md)\.

## Instance Termination<a name="instance-termination"></a>

When you've decided that you no longer need an instance, you can terminate it\. As soon as the status of an instance changes to `shutting-down` or `terminated`, you stop incurring charges for that instance\.

If you enable termination protection, you can't terminate the instance using the console, CLI, or API\.

After you terminate an instance, it remains visible in the console for a short while, and then the entry is automatically deleted\. You can also describe a terminated instance using the CLI and API\. Resources \(such as tags\) are gradually disassociated from the terminated instance, therefore may no longer be visible on the terminated instance after a short while\. You can't connect to or recover a terminated instance\. 

Each Amazon EBS\-backed instance supports the `InstanceInitiatedShutdownBehavior` attribute, which controls whether the instance stops or terminates when you initiate shutdown from within the instance itself \(for example, by using the shutdown command on Linux\)\. The default behavior is to stop the instance\. You can modify the setting of this attribute while the instance is running or stopped\.

Each Amazon EBS volume supports the `DeleteOnTermination` attribute, which controls whether the volume is deleted or preserved when you terminate the instance it is attached to\. The default is to delete the root device volume and preserve any other EBS volumes\.

For more information, see [Terminate Your Instance](terminating-instances.md)\.

## Differences Between Reboot, Stop, and Terminate<a name="lifecycle-differences"></a>

The following table summarizes the key differences between rebooting, stopping, and terminating your instance\.


| Characteristic | Reboot | Stop/start \(Amazon EBS\-backed instances only\) | Terminate | 
| --- | --- | --- | --- | 
|  Host computer  |  The instance stays on the same host computer  |  In most cases, we move the instance to a new host computer\. Your instance may stay on the same host computer if there are no problems with the host computer\.  |  None  | 
|  Private and public IPv4 addresses  |  These addresses stay the same  |  EC2\-Classic: The instance gets new private and public IPv4 addresses EC2\-VPC: The instance keeps its private IPv4 address\. The instance gets a new public IPv4 address, unless it has an Elastic IP address, which doesn't change during a stop/start\.  |  None  | 
|  Elastic IP addresses \(IPv4\)  |  The Elastic IP address remains associated with the instance  |  EC2\-Classic: The Elastic IP address is disassociated from the instance EC2\-VPC: The Elastic IP address remains associated with the instance  |  The Elastic IP address is disassociated from the instance  | 
| IPv6 address \(EC2\-VPC only\) | The address stays the same | The instance keeps its IPv6 address | None | 
|  Instance store volumes  |  The data is preserved  |  The data is erased  |  The data is erased  | 
|  Root device volume  |  The volume is preserved  |  The volume is preserved  |  The volume is deleted by default  | 
|  Billing  |  The instance billing hour doesn't change\.  |  You stop incurring charges for an instance as soon as its state changes to `stopping`\. Each time an instance transitions from `stopped` to `running`, we start a new instance billing period, billing a minimum of one minute every time you restart your instance\.  |  You stop incurring charges for an instance as soon as its state changes to `shutting-down`\.  | 

Operating system shutdown commands always terminate an instance store\-backed instance\. You can control whether operating system shutdown commands stop or terminate an Amazon EBS\-backed instance\. For more information, see [Changing the Instance Initiated Shutdown Behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.
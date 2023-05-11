# Instance lifecycle<a name="ec2-instance-lifecycle"></a>

An Amazon EC2 instance transitions through different states from the moment you launch it through to its termination\.

The following illustration represents the transitions between instance states\. Notice that you can't stop and start an instance store\-backed instance\. For more information about instance store\-backed instances, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.

![\[The instance lifecycle.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/instance_lifecycle.png)

The following table provides a brief description of each instance state and indicates whether it is billed\.

**Note**  
The table indicates billing for instance usage only\. Some AWS resources, such as Amazon EBS volumes and Elastic IP addresses, incur charges regardless of the instance's state\. For more information, see [Avoiding Unexpected Charges](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/checklistforunwantedcharges.html) in the *AWS Billing User Guide*\.


| Instance state | Description | Instance usage billing | 
| --- | --- | --- | 
|  `pending`  |  The instance is preparing to enter the `running` state\. An instance enters the `pending` state when it is launched or when it is started after being in the `stopped` state\.  |  Not billed  | 
|  `running`  |  The instance is running and ready for use\.  |  Billed  | 
|  `stopping`  |  The instance is preparing to be stopped\.  |  Not billed  | 
|  `stopped`  |  The instance is shut down and cannot be used\. The instance can be started at any time\.  |  Not billed  | 
|  `shutting-down`  |  The instance is preparing to be terminated\.  |  Not billed  | 
|  `terminated`  |  The instance has been permanently deleted and cannot be started\.  |  Not billed  Reserved Instances that applied to terminated instances are billed until the end of their term according to their payment option\. For more information, see [Reserved Instances](ec2-reserved-instances.md)   | 

**Note**  
Rebooting an instance doesn't start a new instance billing period because the instance stays in the `running` state\.

## Instance launch<a name="instance-launch"></a>

When you launch an instance, it enters the `pending` state\. The instance type that you specified at launch determines the hardware of the host computer for your instance\. We use the Amazon Machine Image \(AMI\) you specified at launch to boot the instance\. After the instance is ready for you, it enters the `running` state\. You can connect to your running instance and use it the way that you'd use a computer sitting in front of you\.

As soon as your instance transitions to the `running` state, you're billed for each second, with a one\-minute minimum, that you keep the instance running, even if the instance remains idle and you don't connect to it\.

For more information, see [Launch your instance](LaunchingAndUsingInstances.md) and [Connect to your Linux instance](AccessingInstances.md)\.

## Instance stop and start \(Amazon EBS\-backed instances only\)<a name="instance-stop-start"></a>

If your instance fails a status check or is not running your applications as expected, and if the root volume of your instance is an Amazon EBS volume, you can stop and start your instance to try to fix the problem\.

When you stop your instance, it enters the `stopping` state, and then the `stopped` state\. We don't charge usage or data transfer fees for your instance after you stop it, but we do charge for the storage for any Amazon EBS volumes\. While your instance is in the `stopped` state, you can modify certain attributes of the instance, including the instance type\.

When you start your instance, it enters the `pending` state, and we move the instance to a new host computer \(though in some cases, it remains on the current host\)\. When you stop and start your instance, you lose any data on the instance store volumes on the previous host computer\.

Your instance retains its private IPv4 address, which means that an Elastic IP address associated with the private IPv4 address or network interface is still associated with your instance\. If your instance has an IPv6 address, it retains its IPv6 address\.

Each time you transition an instance from `stopped` to `running`, we charge per second when the instance is running, with a minimum of one minute every time you start your instance\.

For more information, see [Stop and start your instance](Stop_Start.md)\.

## Instance hibernate \(Amazon EBS\-backed instances only\)<a name="instance-hibernate"></a>

When you hibernate an instance, we signal the operating system to perform hibernation \(suspend\-to\-disk\), which saves the contents from the instance memory \(RAM\) to your Amazon EBS root volume\. We persist the instance's Amazon EBS root volume and any attached Amazon EBS data volumes\. When you start your instance, the Amazon EBS root volume is restored to its previous state and the RAM contents are reloaded\. Previously attached data volumes are reattached and the instance retains its instance ID\.

When you hibernate your instance, it enters the `stopping` state, and then the `stopped` state\. We don't charge usage for a hibernated instance when it is in the `stopped` state, but we do charge while it is in the `stopping` state, unlike when you [stop an instance](#instance-stop-start) without hibernating it\. We don't charge usage for data transfer fees, but we do charge for the storage for any Amazon EBS volumes, including storage for the RAM data\.

When you start your hibernated instance, it enters the `pending` state, and we move the instance to a new host computer \(though in some cases, it remains on the current host\)\.

Your instance retains its private IPv4 address, which means that an Elastic IP address associated with the private IPv4 address or network interface is still associated with your instance\. If your instance has an IPv6 address, it retains its IPv6 address\.

For more information, see [Hibernate your On\-Demand Linux instance](Hibernate.md)\.

## Instance reboot<a name="instance-reboot"></a>

You can reboot your instance using the Amazon EC2 console, a command line tool, and the Amazon EC2 API\. We recommend that you use Amazon EC2 to reboot your instance instead of running the operating system reboot command from your instance\.

Rebooting an instance is equivalent to rebooting an operating system\. The instance remains on the same host computer and maintains its public DNS name, private IP address, and any data on its instance store volumes\. It typically takes a few minutes for the reboot to complete, but the time it takes to reboot depends on the instance configuration\.

Rebooting an instance doesn't start a new instance billing period; per second billing continues without a further one\-minute minimum charge\.

For more information, see [Reboot your instance](ec2-instance-reboot.md)\.

## Instance retirement<a name="instance-retiring"></a>

An instance is scheduled to be retired when AWS detects the irreparable failure of the underlying hardware hosting the instance\. When an instance reaches its scheduled retirement date, it is stopped or terminated by AWS\. If your instance root device is an Amazon EBS volume, the instance is stopped, and you can start it again at any time\. If your instance root device is an instance store volume, the instance is terminated, and cannot be used again\.

For more information, see [Instance retirement](instance-retirement.md)\.

## Instance termination<a name="instance-termination"></a>

When you've decided that you no longer need an instance, you can terminate it\. As soon as the status of an instance changes to `shutting-down` or `terminated`, you stop incurring charges for that instance\.

If you enable termination protection, you can't terminate the instance using the console, CLI, or API\.

After you terminate an instance, it remains visible in the console for a short while, and then the entry is automatically deleted\. You can also describe a terminated instance using the CLI and API\. Resources \(such as tags\) are gradually disassociated from the terminated instance, therefore may no longer be visible on the terminated instance after a short while\. You can't connect to or recover a terminated instance\. 

Each Amazon EBS\-backed instance supports the `InstanceInitiatedShutdownBehavior` attribute, which controls whether the instance stops or terminates when you initiate shutdown from within the instance itself \(for example, by using the shutdown command on Linux\)\. The default behavior is to stop the instance\. You can modify the setting of this attribute while the instance is running or stopped\.

Each Amazon EBS volume supports the `DeleteOnTermination` attribute, which controls whether the volume is deleted or preserved when you terminate the instance it is attached to\. The default is to delete the root device volume and preserve any other EBS volumes\.

For more information, see [Terminate your instance](terminating-instances.md)\.

## Differences between reboot, stop, hibernate, and terminate<a name="lifecycle-differences"></a>

The following table summarizes the key differences between rebooting, stopping, hibernating, and terminating your instance\.


| Characteristic | Reboot | Stop/start \(Amazon EBS\-backed instances only\) | Hibernate \(Amazon EBS\-backed instances only\) | Terminate | 
| --- | --- | --- | --- | --- | 
|  Host computer  |  The instance stays on the same host computer  |  We move the instance to a new host computer \(though in some cases, it remains on the current host\)\.  |  We move the instance to a new host computer \(though in some cases, it remains on the current host\)\.  |  None  | 
|  Private and public IPv4 addresses  |  These addresses stay the same  |  The instance keeps its private IPv4 address\. The instance gets a new public IPv4 address, unless it has an Elastic IP address, which doesn't change during a stop/start\.  |  The instance keeps its private IPv4 address\. The instance gets a new public IPv4 address, unless it has an Elastic IP address, which doesn't change during a stop/start\.  |  None  | 
|  Elastic IP addresses \(IPv4\)  |  The Elastic IP address remains associated with the instance  |  The Elastic IP address remains associated with the instance  |  The Elastic IP address remains associated with the instance  |  The Elastic IP address is disassociated from the instance  | 
|  IPv6 address  |  The address stays the same  |  The instance keeps its IPv6 address  |  The instance keeps its IPv6 address  |  None  | 
|  Instance store volumes  |  The data is preserved  |  The data is erased  |  The data is erased  |  The data is erased  | 
|  Root device volume  |  The volume is preserved  |  The volume is preserved  |  The volume is preserved  |  The volume is deleted by default  | 
|  RAM \(contents of memory\)  |  The RAM is erased  |  The RAM is erased  |  The RAM is saved to a file on the root volume  |  The RAM is erased  | 
|  Billing  |  The instance billing hour doesn't change\.  |  You stop incurring charges for an instance as soon as its state changes to `stopping`\. Each time an instance transitions from `stopped` to `running`, we start a new instance billing period, billing a minimum of one minute every time you start your instance\.  |  You incur charges while the instance is in the `stopping` state, but stop incurring charges when the instance is in the `stopped` state\. Each time an instance transitions from `stopped` to `running`, we start a new instance billing period, billing a minimum of one minute every time you start your instance\.  |  You stop incurring charges for an instance as soon as its state changes to `shutting-down`\.  | 

Operating system shutdown commands always terminate an instance store\-backed instance\. You can control whether operating system shutdown commands stop or terminate an Amazon EBS\-backed instance\. For more information, see [Change the instance initiated shutdown behavior](terminating-instances.md#Using_ChangingInstanceInitiatedShutdownBehavior)\.
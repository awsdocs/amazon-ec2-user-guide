# Hibernate your On\-Demand Linux instance<a name="Hibernate"></a>

When you hibernate an instance, Amazon EC2 signals the operating system to perform hibernation \(suspend\-to\-disk\)\. Hibernation saves the contents from the instance memory \(RAM\) to your Amazon Elastic Block Store \(Amazon EBS\) root volume\. Amazon EC2 persists the instance's EBS root volume and any attached EBS data volumes\. When you start your instance:
+ The EBS root volume is restored to its previous state
+ The RAM contents are reloaded
+ The processes that were previously running on the instance are resumed
+ Previously attached data volumes are reattached and the instance retains its instance ID

You can hibernate an instance only if it's [enabled for hibernation](enabling-hibernation.md) and it meets the [hibernation prerequisites](hibernating-prerequisites.md)\.

If an instance or application takes a long time to bootstrap and build a memory footprint in order to become fully productive, you can use hibernation to pre\-warm the instance\. To pre\-warm the instance, you:

1. Launch it with hibernation enabled\.

1. Bring it to a desired state\.

1. Hibernate it so that it's ready to be resumed to the desired state whenever needed\.

You're not charged for instance usage for a hibernated instance when it is in the `stopped` state\. However, you are charged for instance usage while the instance is in the `stopping` state, while the contents of the RAM are transferred to the EBS root volume\. \(This is different from when you [stop an instance](Stop_Start.md) without hibernating it\.\) You're not charged for data transfer\. However, you are charged for storage of any EBS volumes, including storage for the RAM contents\.

If you no longer need an instance, you can terminate it at any time, including when it is in a `stopped` \(hibernated\) state\. For more information, see [Terminate your instance](terminating-instances.md)\.

**Note**  
For information about using hibernation on Windows instances, see [Hibernate Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Hibernate.html) in the *Amazon EC2 User Guide for Windows Instances*\.  
For information about hibernating Spot Instances, see [Hibernate interrupted Spot Instances](hibernate-spot-instances.md)\.

**Topics**
+ [Overview of hibernation](instance-hibernate-overview.md)
+ [Hibernation prerequisites](hibernating-prerequisites.md)
+ [Limitations](instance-hibernate-limitations.md)
+ [Configure an existing AMI to support hibernation](hibernation-enabled-AMI.md)
+ [Enable hibernation for an instance](enabling-hibernation.md)
+ [Disable KASLR on an instance \(Ubuntu only\)](hibernation-disable-kaslr.md)
+ [Hibernate an instance](hibernating-instances.md)
+ [Start a hibernated instance](hibernating-resuming.md)
+ [Troubleshoot hibernation](troubleshoot-instance-hibernate.md)
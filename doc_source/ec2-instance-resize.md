# Changing the instance type<a name="ec2-instance-resize"></a>

As your needs change, you might find that your instance is over\-utilized \(the instance type is too small\) or under\-utilized \(the instance type is too large\)\. If this is the case, you can change the size of your instance\. For example, if your `t2.micro` instance is too small for its workload, you can change it to another instance type that is appropriate for the workload\.

You might also want to migrate from a previous generation instance type to a current generation instance type to take advantage of some features; for example, support for IPv6\.

If the root device for your instance is an EBS volume, you can change the size of the instance simply by changing its instance type, which is known as *resizing* it\. If the root device for your instance is an instance store volume, you must migrate your application to a new instance with the instance type that you need\. For more information about root device volumes, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\.

When you resize an instance, you must select an instance type that is compatible with the configuration of the instance\. If the instance type that you want is not compatible with the instance configuration you have, then you must migrate your application to a new instance with the instance type that you need\.

**Important**  
When you resize an instance, the resized instance usually has the same number of instance store volumes that you specified when you launched the original instance\. With instance types that support NVMe instance store volumes \(which are available by default\), the resized instance might have additional instance store volumes, depending on the AMI\. Otherwise, you can migrate your application to an instance with a new instance type manually, specifying the number of instance store volumes that you need when you launch the new instance\.

**Topics**
+ [Compatibility for resizing instances](#resize-limitations)
+ [Resizing an Amazon EBS–backed instance](#resize-ebs-backed-instance)
+ [Migrating an instance store\-backed instance](#resize-instance-store-backed-instance)
+ [Migrating to a new instance configuration](#migrate-instance-configuration)

## Compatibility for resizing instances<a name="resize-limitations"></a>

You can resize an instance only if its current instance type and the new instance type that you want are compatible in the following ways:
+ **Virtualization type**: Linux AMIs use one of two types of virtualization: paravirtual \(PV\) or hardware virtual machine \(HVM\)\. You can't resize an instance that was launched from a PV AMI to an instance type that is HVM only\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\. To check the virtualization type of your instance, see the **Virtualization** field on the details pane of the **Instances** screen in the Amazon EC2 console\.
+ **Architecture**: AMIs are specific to the architecture of the processor, so you must select an instance type with the same processor architecture as the current instance type\. For example:
  + A1 instances are the only instances that support processors based on the Arm architecture\. If you are resizing an instance type with a processor based on the Arm architecture, you are limited to the instance types that support a processor based on the Arm architecture\.
  + The following instance types are the only instance types that support 32\-bit AMIs: `t2.nano`, `t2.micro`, `t2.small`, `t2.medium`, `c3.large`, `t1.micro`, `m1.small`, `m1.medium`, and `c1.medium`\. If you are resizing a 32\-bit instance, you are limited to these instance types\.
+ **Network**: Newer instance types must be launched in a VPC\. Therefore, you can't resize an instance in the EC2\-Classic platform to a instance type that is available only in a VPC unless you have a nondefault VPC\. To check whether your instance is in a VPC, check the **VPC ID** value on the details pane of the **Instances** screen in the Amazon EC2 console\. For more information, see [Migrating from a Linux Instance in EC2\-Classic to a Linux Instance in a VPC](vpc-migrate.md)\.
+ **Enhanced networking**: Instance types that support [enhanced networking](enhanced-networking.md) require the necessary drivers installed\. For example, the A1, C5, C5d, C5n, F1, G3, G4, H1, I3, I3en, Inf1, `m4.16xlarge`, M5, M5a, M5ad, M5d, M5dn, M5n, M6g,  P2, P3, R4, R5, R5a, R5ad, R5d, R5dn, R5n, T3, T3a, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, `u-24tb1.metal`, X1, X1e, and z1d instance types require EBS\-backed AMIs with the Elastic Network Adapter \(ENA\) drivers installed\. To resize an existing instance to an instance type that supports enhanced networking, you must first install the [ENA drivers](enhanced-networking-ena.md) or [ixgbevf drivers](sriov-networking.md) on your instance, as appropriate\.
+ **NVMe**: EBS volumes are exposed as NVMe block devices on instances built on the [Nitro System](instance-types.md#ec2-nitro-instances)\. If you resize an instance from an instance type that does not support NVMe to an instance type that supports NVMe, you must first install the [NVMe drivers](nvme-ebs-volumes.md) on your instance\. Also, the device names for devices that you specify in the block device mapping are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\. Therefore, to mount file systems at boot time using `/etc/fstab`, you must use UUID/Label instead of device names\.
+ **AMI**: For information about the AMIs required by instance types that support enhanced networking and NVMe, see the Release Notes in the following documentation:
  + [General purpose instances](general-purpose-instances.md)
  + [Compute optimized instances](compute-optimized-instances.md)
  + [Memory optimized instances](memory-optimized-instances.md)
  + [Storage optimized instances](storage-optimized-instances.md)

## Resizing an Amazon EBS–backed instance<a name="resize-ebs-backed-instance"></a>

You must stop your Amazon EBS–backed instance before you can change its instance type\. When you stop and start an instance, be aware of the following:
+ We move the instance to new hardware; however, the instance ID does not change\.
+ If your instance has a public IPv4 address, we release the address and give it a new public IPv4 address\. The instance retains its private IPv4 addresses, any Elastic IP addresses, and any IPv6 addresses\.
+ If your instance is in an Auto Scaling group, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and may terminate it and launch a replacement instance\. To prevent this, you can suspend the scaling processes for the group while you're resizing your instance\. For more information, see [Suspending and Resuming Scaling Processes](https://docs.aws.amazon.com/autoscaling/latest/userguide/as-suspend-resume-processes.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ If your instance is in a [cluster placement group](placement-groups.md#placement-groups-cluster) and, after changing the instance type, the instance start fails, try the following: stop all the instances in the cluster placement group, change the instance type for the affected instance, and then restart all the instances in the cluster placement group\. 
+ Ensure that you plan for downtime while your instance is stopped\. Stopping and resizing an instance may take a few minutes, and restarting your instance may take a variable amount of time depending on your application's startup scripts\.

For more information, see [Stop and start your instance](Stop_Start.md)\.

Use the following procedure to resize an Amazon EBS–backed instance using the AWS Management Console\.

**To resize an Amazon EBS–backed instance**

1. \(Optional\) If the new instance type requires drivers that are not installed on the existing instance, you must connect to your instance and install the drivers first\. For more information, see [Compatibility for resizing instances](#resize-limitations)\.

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Instance State**, **Stop**\.

1. In the confirmation dialog box, choose **Yes, Stop**\. It can take a few minutes for the instance to stop\.

1. With the instance still selected, choose **Actions**, **Instance Settings**, **Change Instance Type**\. This action is disabled if the instance state is not `stopped`\.

1. In the **Change Instance Type** dialog box, do the following:

   1. From **Instance Type**, select the instance type that you want\. If the instance type that you want does not appear in the list, then it is not compatible with the configuration of your instance \(for example, because of virtualization type\)\. For more information, see [Compatibility for resizing instances](#resize-limitations)\.

   1. \(Optional\) If the instance type that you selected supports EBS–optimization, select **EBS\-optimized** to enable EBS–optimization or deselect **EBS\-optimized** to disable EBS–optimization\. If the instance type that you selected is EBS–optimized by default, **EBS\-optimized** is selected and you can't deselect it\.

   1. Choose **Apply** to accept the new settings\.

1. To restart the stopped instance, select the instance and choose **Actions**, **Instance State**, **Start**\.

1. In the confirmation dialog box, choose **Yes, Start**\. It can take a few minutes for the instance to enter the `running` state\.

1. \(Troubleshooting\) If your instance won't boot, it is possible that one of the requirements for the new instance type was not met\. For more information, see [Why is my Linux instance not booting after I changed its type?](https://aws.amazon.com/premiumsupport/knowledge-center/boot-error-linux-m5-c5/)

## Migrating an instance store\-backed instance<a name="resize-instance-store-backed-instance"></a>

When you want to move your application from one instance store\-backed instance to an instance store\-backed instance with a different instance type, you must migrate it by creating an image from your instance, and then launching a new instance from this image with the instance type that you need\. To ensure that your users can continue to use the applications that you're hosting on your instance uninterrupted, you must take any Elastic IP address that you've associated with your original instance and associate it with the new instance\. Then you can terminate the original instance\.

**To migrate an instance store\-backed instance**

1. Back up any data on your instance store volumes that you need to keep to persistent storage\. To migrate data on your EBS volumes that you need to keep, take a snapshot of the volumes \(see [Creating Amazon EBS snapshots](ebs-creating-snapshot.md)\) or detach the volume from the instance so that you can attach it to the new instance later \(see [Detaching an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\)\.

1. Create an AMI from your instance store\-backed instance by satisfying the prerequisites and following the procedures in [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. When you are finished creating an AMI from your instance, return to this procedure\.

1. Open the Amazon EC2 console and in the navigation pane, choose **AMIs**\. From the filter lists, choose **Owned by me**, and choose the image that you created in the previous step\. Notice that **AMI Name** is the name that you specified when you registered the image and **Source** is your Amazon S3 bucket\.
**Note**  
If you do not see the AMI that you created in the previous step, make sure that you have selected the Region in which you created your AMI\.

1. Choose **Launch**\. When you specify options for the instance, be sure to select the new instance type that you want\. If the instance type that you want can't be selected, then it is not compatible with configuration of the AMI that you created \(for example, because of virtualization type\)\. You can also specify any EBS volumes that you detached from the original instance\.

   It can take a few minutes for the instance to enter the `running` state\.

1. \(Optional\) You can terminate the instance that you started with, if it's no longer needed\. Select the instance and verify that you are about to terminate the original instance, not the new instance \(for example, check the name or launch time\)\. Choose **Actions**, **Instance State**, **Terminate**\.

## Migrating to a new instance configuration<a name="migrate-instance-configuration"></a>

If the current configuration of your instance is incompatible with the new instance type that you want, then you can't resize the instance to that instance type\. Instead, you can migrate your application to a new instance with a configuration that is compatible with the new instance type that you want\.

If you want to move from an instance launched from a PV AMI to an instance type that is HVM only, the general process is as follows:

**To migrate your application to a compatible instance**

1. Back up any data on your instance store volumes that you need to keep to persistent storage\. To migrate data on your EBS volumes that you need to keep, create a snapshot of the volumes \(see [Creating Amazon EBS snapshots](ebs-creating-snapshot.md)\) or detach the volume from the instance so that you can attach it to the new instance later \(see [Detaching an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\)\.

1. Launch a new instance, selecting the following:
   + An HVM AMI\.
   + The HVM only instance type\.
   + If you are using an Elastic IP address, select the VPC that the original instance is currently running in\.
   + Any EBS volumes that you detached from the original instance and want to attach to the new instance, or new EBS volumes based on the snapshots that you created\.
   + If you want to allow the same traffic to reach the new instance, select the security group that is associated with the original instance\.

1. Install your application and any required software on the instance\.

1. Restore any data that you backed up from the instance store volumes of the original instance\.

1. If you are using an Elastic IP address, assign it to the newly launched instance as follows:

   1. In the navigation pane, choose **Elastic IPs**\.

   1. Select the Elastic IP address that is associated with the original instance and choose **Actions**, **Disassociate address**\. When prompted for confirmation, choose **Disassociate address**\.

   1. With the Elastic IP address still selected, choose **Actions**, **Associate address**\.

   1. From **Instance**, select the new instance, and then choose **Associate**\.

1. \(Optional\) You can terminate the original instance if it's no longer needed\. Select the instance and verify that you are about to terminate the original instance, not the new instance \(for example, check the name or launch time\)\. Choose **Actions**, **Instance State**, **Terminate**\.
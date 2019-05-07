# Device Naming on Linux Instances<a name="device_naming"></a>

When you attach a volume to your instance, you include a device name for the volume\. This device name is used by Amazon EC2\. The block device driver for the instance assigns the actual volume name when mounting the volume, and the name assigned can be different from the name that Amazon EC2 uses\.

The number of volumes that your instance can support is determined by the operating system\. For more information, see [Instance Volume Limits](volume_limits.md)\.

**Topics**
+ [Available Device Names](#available-ec2-device-names)
+ [Device Name Considerations](#device-name-limits)

For information about device names on Windows instances, see [Device Naming on Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/device_naming.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Available Device Names<a name="available-ec2-device-names"></a>

There are two types of virtualization available for Linux instances: paravirtual \(PV\) and hardware virtual machine \(HVM\)\. The virtualization type of an instance is determined by the AMI used to launch the instance\. Some instance types support both PV and HVM, some support HVM only, and others support PV only\. Be sure to note the virtualization type of your AMI, because the recommended and available device names that you can use depend on the virtualization type of your instance\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

The following table lists the available device names that you can specify in a block device mapping or when attaching an EBS volume\.


| Virtualization Type | Available | Reserved for Root | Recommended for EBS Volumes | Instance Store Volumes | 
| --- | --- | --- | --- | --- | 
|  Paravirtual  |  /dev/sd\[a\-z\] /dev/sd\[a\-z\]\[1\-15\] /dev/hd\[a\-z\] /dev/hd\[a\-z\]\[1\-15\]  |  /dev/sda1  |  /dev/sd\[f\-p\] /dev/sd\[f\-p\]\[1\-6\]  |  /dev/sd\[b\-e\] /dev/sd\[b\-y\] \(hs1\.8xlarge\)  | 
| HVM |  /dev/sd\[a\-z\] /dev/xvd\[b\-c\]\[a\-z\]  |  Differs by AMI /dev/sda1 or /dev/xvda  |  /dev/sd\[f\-p\] \*  |  /dev/sd\[b\-e\] /dev/sd\[b\-h\] \(h1\.16xlarge\) /dev/sd\[b\-y\] \(d2\.8xlarge\) /dev/sd\[b\-y\] \(hs1\.8xlarge\) /dev/sd\[b\-i\] \(i2\.8xlarge\) \*\*  | 

\* The device names that you specify for NVMe EBS volumes in a block device mapping are renamed using NVMe device names \(`/dev/nvme[0-26]n1`\)\. The block device driver can assign NVMe device names in a different order than you specified for the volumes in the block device mapping\.

\*\* NVMe instance store volumes are automatically enumerated and assigned an NVMe device name\.

For more information about instance store volumes, see [Amazon EC2 Instance Store](InstanceStorage.md)\. For more information about NVMe EBS volumes, see [Amazon EBS and NVMe on Linux Instances](nvme-ebs-volumes.md)\.

## Device Name Considerations<a name="device-name-limits"></a>

Keep the following in mind when selecting a device name:
+ Although you can attach your EBS volumes using the device names used to attach instance store volumes, we strongly recommend that you don't because the behavior can be unpredictable\.
+ The number of NVMe instance store volumes for an instance depends on the size of the instance\. NVMe instance store volumes are automatically enumerated and assigned an NVMe device name \(`/dev/nvme[0-26]n1`\)\.
+ Depending on the block device driver of the kernel, the device could be attached with a different name than you specified\. For example, if you specify a device name of `/dev/sdh`, your device could be renamed `/dev/xvdh` or `/dev/hdh`\. In most cases, the trailing letter remains the same\. In some versions of Red Hat Enterprise Linux \(and its variants, such as CentOS\), even the trailing letter could change \(`/dev/sda` could become `/dev/xvde`\)\. In these cases, the trailing letter of each device name is incremented the same number of times\. For example, if `/dev/sdb` is renamed `/dev/xvdf`, then `/dev/sdc` is renamed `/dev/xvdg`\. Amazon Linux creates a symbolic link for the name you specified to the renamed device\. Other operating systems could behave differently\.
+ HVM AMIs do not support the use of trailing numbers on device names, except for `/dev/sda1`, which is reserved for the root device, and `/dev/sda2`\. While using `/dev/sda2` is possible, we do not recommend using this device mapping with HVM instances\.
+ When using PV AMIs, you cannot attach volumes that share the same device letters both with and without trailing digits\. For example, if you attach a volume as `/dev/sdc` and another volume as `/dev/sdc1`, only `/dev/sdc` is visible to the instance\. To use trailing digits in device names, you must use trailing digits on all device names that share the same base letters \(such as `/dev/sdc1`, `/dev/sdc2`, `/dev/sdc3`\)\.
+ Some custom kernels might have restrictions that limit use to `/dev/sd[f-p]` or `/dev/sd[f-p][1-6]`\. If you're having trouble using `/dev/sd[q-z]` or `/dev/sd[q-z][1-6]`, try switching to `/dev/sd[f-p]` or `/dev/sd[f-p][1-6]`\.
# Device Naming on Linux Instances<a name="device_naming"></a>

When you attach a volume to your instance, you include a device name for the volume\. This device name is used by Amazon EC2\. The block device driver for the instance assigns the actual volume name when mounting the volume, and the name assigned can be different from the name that Amazon EC2 uses\.

**Topics**
+ [Available Device Names](#available-ec2-device-names)
+ [Device Name Considerations](#device-name-limits)

For information about device names on Windows instances, see [Device Naming on Windows Instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/device_naming.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Available Device Names<a name="available-ec2-device-names"></a>

The following table lists the available device names for Linux instances\. The number of volumes that you can attach to your instance is determined by the operating system\. For more information, see [Instance Volume Limits](volume_limits.md)\.


| Virtualization Type | Available | Reserved for Root | Recommended for EBS Volumes | Instance Store Volumes | NVMe Volumes | 
| --- | --- | --- | --- | --- | --- | 
|  Paravirtual  |  /dev/sd\[a\-z\] /dev/sd\[a\-z\]\[1\-15\] /dev/hd\[a\-z\] /dev/hd\[a\-z\]\[1\-15\]  |  /dev/sda1  |  /dev/sd\[f\-p\] /dev/sd\[f\-p\]\[1\-6\]  |  /dev/sd\[b\-e\] /dev/sd\[b\-y\] \(hs1\.8xlarge\)  | Not available | 
| HVM |  /dev/sd\[a\-z\] /dev/xvd\[b\-c\]\[a\-z\]  |  Differs by AMI /dev/sda1 or /dev/xvda  |  /dev/sd\[f\-p\]  |  /dev/sd\[b\-e\] /dev/sd\[b\-h\] \(h1\.16xlarge\) /dev/sd\[b\-y\] \(d2\.8xlarge\) /dev/sd\[b\-y\] \(hs1\.8xlarge\) /dev/sd\[b\-i\] \(i2\.8xlarge\)  | /dev/nvme\[0\-26\]n1 \* | 

\* NVMe instance store volumes are automatically enumerated and assigned a device name\. There is no need to specify NVMe instance store volumes in your block device mapping\.

For more information about instance store volumes, see [Amazon EC2 Instance Store](InstanceStorage.md)\.

For more information about NVMe EBS volumes, see [Amazon EBS and NVMe](nvme-ebs-volumes.md)\.

## Device Name Considerations<a name="device-name-limits"></a>

Keep the following in mind when selecting a device name:
+ Although you can attach your EBS volumes using the device names used to attach instance store volumes, we strongly recommend that you don't because the behavior can be unpredictable\.
+ Depending on the block device driver of the kernel, the device could be attached with a different name than you specified\. For example, if you specify a device name of `/dev/sdh`, your device could be renamed `/dev/xvdh` or `/dev/hdh`\. In most cases, the trailing letter remains the same\. In some versions of Red Hat Enterprise Linux \(and its variants, such as CentOS\), even the trailing letter could change \(`/dev/sda` could become `/dev/xvde`\)\. In these cases, the trailing letter of each device name is incremented the same number of times\. For example, if `/dev/sdb` is renamed `/dev/xvdf`, then `/dev/sdc` is renamed `/dev/xvdg`\. Amazon Linux AMIs create a symbolic link for the name you specified to the renamed device\. Other AMIs could behave differently\.
+ The number of NVMe instance store volumes for an instance depends on the size of the instance\. The device names are `/dev/nvme0n1`, `/dev/nvme1n1`, and so on\.
+ There are two types of virtualization available for Linux instances: paravirtual \(PV\) and hardware virtual machine \(HVM\)\. The virtualization type of an instance is determined by the AMI used to launch the instance\. Some instance types support both PV and HVM, some support HVM only, and others support PV only\. Be sure to note the virtualization type of your AMI, because the recommended and available device names that you can use depend on the virtualization type of your instance\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.
+ You cannot attach volumes that share the same device letters both with and without trailing digits\. For example, if you attach a volume as `/dev/sdc` and another volume as `/dev/sdc1`, only `/dev/sdc` is visible to the instance\. To use trailing digits in device names, you must use trailing digits on all device names that share the same base letters \(such as `/dev/sdc1`, `/dev/sdc2`, `/dev/sdc3`\)\.
+ Hardware virtual machine \(HVM\) AMIs don't support the use of trailing numbers on device names, except for the device name that's reserved for the root device\.
+ Some custom kernels might have restrictions that limit use to `/dev/sd[f-p]` or `/dev/sd[f-p][1-6]`\. If you're having trouble using `/dev/sd[q-z]` or `/dev/sd[q-z][1-6]`, try switching to `/dev/sd[f-p]` or `/dev/sd[f-p][1-6]`\.
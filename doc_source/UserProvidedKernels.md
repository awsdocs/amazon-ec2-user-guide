# User provided kernels<a name="UserProvidedKernels"></a>

If you need a custom kernel on your Amazon EC2 instances, you can start with an AMI that is close to what you want, compile the custom kernel on your instance, and update the bootloader to point to the new kernel\. This process varies depending on the virtualization type that your AMI uses\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.

**Topics**
+ [HVM AMIs \(GRUB\)](#HVM_instances)
+ [Paravirtual AMIs \(PV\-GRUB\)](#Paravirtual_instances)

## HVM AMIs \(GRUB\)<a name="HVM_instances"></a>

HVM instance volumes are treated like actual physical disks\. The boot process is similar to that of a bare metal operating system with a partitioned disk and bootloader, which enables it to work with all currently supported Linux distributions\. The most common bootloader is GRUB or GRUB2\.

By default, GRUB does not send its output to the instance console because it creates an extra boot delay\. For more information, see [Instance console output](instance-console.md#instance-console-console-output)\. If you are installing a custom kernel, you should consider enabling GRUB output\.

You don't need to specify a fallback kernel, but we recommend that you have a fallback when you test a new kernel\. GRUB can fall back to another kernel in the event that the new kernel fails\. Having a fallback kernel enables the instance to boot even if the new kernel isn't found\.

The legacy GRUB for Amazon Linux uses `/boot/grub/menu.lst`\. GRUB2 for Amazon Linux 2 uses `/etc/default/grub`\. For more information about updating the default kernel in the bootloader, see the documentation for your Linux distribution\.

## Paravirtual AMIs \(PV\-GRUB\)<a name="Paravirtual_instances"></a>

Amazon Machine Images that use paravirtual \(PV\) virtualization use a system called *PV\-GRUB* during the boot process\. PV\-GRUB is a paravirtual bootloader that runs a patched version of GNU GRUB 0\.97\. When you start an instance, PV\-GRUB starts the boot process and then chain loads the kernel specified by your image's `menu.lst` file\.

PV\-GRUB understands standard `grub.conf` or `menu.lst` commands, which allows it to work with all currently supported Linux distributions\. Older distributions such as Ubuntu 10\.04 LTS, Oracle Enterprise Linux, or CentOS 5\.x require a special "ec2" or "xen" kernel package, while newer distributions include the required drivers in the default kernel package\.

Most modern paravirtual AMIs use a PV\-GRUB AKI by default \(including all of the paravirtual Linux AMIs available in the Amazon EC2 Launch Wizard Quick Start menu\), so there are no additional steps that you need to take to use a different kernel on your instance, provided that the kernel you want to use is compatible with your distribution\. The best way to run a custom kernel on your instance is to start with an AMI that is close to what you want and then to compile the custom kernel on your instance and modify the `menu.lst` file to boot with that kernel\.

You can verify that the kernel image for an AMI is a PV\-GRUB AKI\. Run the following [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command \(substituting your kernel image ID\) and check whether the `Name` field starts with `pv-grub`:

```
aws ec2 describe-images --filters Name=image-id,Values=aki-880531cd
```

**Topics**
+ [Limitations of PV\-GRUB](#pv-grub-limitations)
+ [Configuring GRUB](#configuringGRUB)
+ [Amazon PV\-GRUB Kernel Image IDs](#AmazonKernelImageIDs)
+ [Update PV\-GRUB](#UpdatingPV-GRUB)

### Limitations of PV\-GRUB<a name="pv-grub-limitations"></a>

PV\-GRUB has the following limitations:
+ You can't use the 64\-bit version of PV\-GRUB to start a 32\-bit kernel or vice versa\.
+ You can't specify an Amazon ramdisk image \(ARI\) when using a PV\-GRUB AKI\.
+ AWS has tested and verified that PV\-GRUB works with these file system formats: EXT2, EXT3, EXT4, JFS, XFS, and ReiserFS\. Other file system formats might not work\.
+ PV\-GRUB can boot kernels compressed using the gzip, bzip2, lzo, and xz compression formats\.
+ Cluster AMIs don't support or need PV\-GRUB, because they use full hardware virtualization \(HVM\)\. While paravirtual instances use PV\-GRUB to boot, HVM instance volumes are treated like actual disks, and the boot process is similar to the boot process of a bare metal operating system with a partitioned disk and bootloader\. 
+ PV\-GRUB versions 1\.03 and earlier don't support GPT partitioning; they support MBR partitioning only\.
+ If you plan to use a logical volume manager \(LVM\) with Amazon Elastic Block Store \(Amazon EBS\) volumes, you need a separate boot partition outside of the LVM\. Then you can create logical volumes with the LVM\.

### Configure GRUB for paravirtual AMIs<a name="configuringGRUB"></a>

To boot PV\-GRUB, a GRUB `menu.lst` file must exist in the image; the most common location for this file is `/boot/grub/menu.lst`\.

The following is an example of a `menu.lst` configuration file for booting an AMI with a PV\-GRUB AKI\. In this example, there are two kernel entries to choose from: Amazon Linux 2018\.03 \(the original kernel for this AMI\), and Vanilla Linux 4\.16\.4 \(a newer version of the Vanilla Linux kernel from [https://www\.kernel\.org/](https://www.kernel.org/)\)\. The Vanilla entry was copied from the original entry for this AMI, and the `kernel` and `initrd` paths were updated to the new locations\. The `default 0` parameter points the bootloader to the first entry it sees \(in this case, the Vanilla entry\), and the `fallback 1` parameter points the bootloader to the next entry if there is a problem booting the first\.

```
default 0
fallback 1
timeout 0
hiddenmenu

title Vanilla Linux 4.16.4
root (hd0)
kernel /boot/vmlinuz-4.16.4 root=LABEL=/ console=hvc0
initrd /boot/initrd.img-4.16.4

title Amazon Linux 2018.03 (4.14.26-46.32.amzn1.x86_64)
root (hd0)
kernel /boot/vmlinuz-4.14.26-46.32.amzn1.x86_64 root=LABEL=/ console=hvc0
initrd /boot/initramfs-4.14.26-46.32.amzn1.x86_64.img
```

You don't need to specify a fallback kernel in your `menu.lst` file, but we recommend that you have a fallback when you test a new kernel\. PV\-GRUB can fall back to another kernel in the event that the new kernel fails\. Having a fallback kernel allows the instance to boot even if the new kernel isn't found\. 

PV\-GRUB checks the following locations for `menu.lst`, using the first one it finds:
+  `(hd0)/boot/grub` 
+  `(hd0,0)/boot/grub` 
+  `(hd0,0)/grub` 
+  `(hd0,1)/boot/grub` 
+  `(hd0,1)/grub` 
+  `(hd0,2)/boot/grub` 
+  `(hd0,2)/grub` 
+  `(hd0,3)/boot/grub` 
+  `(hd0,3)/grub` 

Note that PV\-GRUB 1\.03 and earlier only check one of the first two locations in this list\.

### Amazon PV\-GRUB Kernel Image IDs<a name="AmazonKernelImageIDs"></a>

PV\-GRUB AKIs are available in all Amazon EC2 regions\. There are AKIs for both 32\-bit and 64\-bit architecture types\. Most modern AMIs use a PV\-GRUB AKI by default\.

We recommend that you always use the latest version of the PV\-GRUB AKI, as not all versions of the PV\-GRUB AKI are compatible with all instance types\. Use the following [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command to get a list of the PV\-GRUB AKIs for the current region:

```
aws ec2 describe-images --owners amazon --filters Name=name,Values=pv-grub-*.gz
```

PV\-GRUB is the only AKI available in the `ap-southeast-2` Region\. You should verify that any AMI you want to copy to this Region is using a version of PV\-GRUB that is available in this Region\.

The following are the current AKI IDs for each Region\. Register new AMIs using an hd0 AKI\.

**Note**  
We continue to provide hd00 AKIs for backward compatibility in Regions where they were previously available\.


**ap\-northeast\-1, Asia Pacific \(Tokyo\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-f975a998  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-7077ab11  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**ap\-southeast\-1, Asia Pacific \(Singapore\) Region**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-17a40074  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-73a50110  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**ap\-southeast\-2, Asia Pacific \(Sydney\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-ba5665d9  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-66506305  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**eu\-central\-1, Europe \(Frankfurt\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-1419e57b  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-931fe3fc  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**eu\-west\-1, Europe \(Ireland\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-1c9fd86f  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-dc9ed9af  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**sa\-east\-1, South America \(São Paulo\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-7cd34110  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-912fbcfd  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**us\-east\-1, US East \(N\. Virginia\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-04206613  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-5c21674b  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**us\-gov\-west\-1, AWS GovCloud \(US\-West\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-5ee9573f  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-9ee55bff  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**us\-west\-1, US West \(N\. California\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-43cf8123  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-59cc8239  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 


**us\-west\-2, US West \(Oregon\)**  

| Image ID | Image Name | 
| --- | --- | 
|  aki\-7a69931a  |  pv\-grub\-hd0\_1\.05\-i386\.gz  | 
|  aki\-70cb0e10  |  pv\-grub\-hd0\_1\.05\-x86\_64\.gz  | 

### Update PV\-GRUB<a name="UpdatingPV-GRUB"></a>

We recommend that you always use the latest version of the PV\-GRUB AKI, as not all versions of the PV\-GRUB AKI are compatible with all instance types\. Also, older versions of PV\-GRUB are not available in all regions, so if you copy an AMI that uses an older version to a Region that does not support that version, you will be unable to boot instances launched from that AMI until you update the kernel image\. Use the following procedures to check your instance's version of PV\-GRUB and update it if necessary\.

**To check your PV\-GRUB version**

1. Find the kernel ID for your instance\.

   ```
   aws ec2 describe-instance-attribute --instance-id instance_id --attribute kernel --region region
   
   {
       "InstanceId": "instance_id", 
       "KernelId": "aki-70cb0e10"
   }
   ```

   The kernel ID for this instance is `aki-70cb0e10`\.

1. View the version information of that kernel ID\.

   ```
   aws ec2 describe-images --image-ids aki-70cb0e10 --region region
   
   {
       "Images": [
           {
               "VirtualizationType": "paravirtual", 
               "Name": "pv-grub-hd0_1.05-x86_64.gz", 
               ...
               "Description": "PV-GRUB release 1.05, 64-bit"
           }
       ]
   }
   ```

   This kernel image is PV\-GRUB 1\.05\. If your PV\-GRUB version is not the newest version \(as shown in [Amazon PV\-GRUB Kernel Image IDs](#AmazonKernelImageIDs)\), you should update it using the following procedure\.

**To update your PV\-GRUB version**

If your instance is using an older version of PV\-GRUB, you should update it to the latest version\.

1. Identify the latest PV\-GRUB AKI for your Region and processor architecture from [Amazon PV\-GRUB Kernel Image IDs](#AmazonKernelImageIDs)\.

1. Stop your instance\. Your instance must be stopped to modify the kernel image used\.

   ```
   aws ec2 stop-instances --instance-ids instance_id --region region
   ```

1. Modify the kernel image used for your instance\.

   ```
   aws ec2 modify-instance-attribute --instance-id instance_id --kernel kernel_id --region region
   ```

1. Restart your instance\.

   ```
   aws ec2 start-instances --instance-ids instance_id --region region 
   ```
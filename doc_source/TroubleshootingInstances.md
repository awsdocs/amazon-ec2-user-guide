# Troubleshooting Instances with Failed Status Checks<a name="TroubleshootingInstances"></a>

The following information can help you troubleshoot issues if your instance fails a status check\. First determine whether your applications are exhibiting any problems\. If you verify that the instance is not running your applications as expected, review the status check information and the system logs\.

**Topics**
+ [Review Status Check Information](#InitialSteps)
+ [Retrieve the System Logs](#troubleshooting-retrieve-system-logs)
+ [Troubleshooting System Log Errors for Linux\-Based Instances](#system-log-errors-linux)
+ [Out of memory: kill process](#MemoryOOM)
+ [ERROR: mmu\_update failed \(Memory management update failed\)](#MemoryMMU)
+ [I/O Error \(Block Device Failure\)](#DeviceBlock)
+ [I/O ERROR: neither local nor remote disk \(Broken distributed block device\)](#DeviceDistributed)
+ [request\_module: runaway loop modprobe \(Looping legacy kernel modprobe on older Linux versions\)](#KernelLoop)
+ ["FATAL: kernel too old" and "fsck: No such file or directory while trying to open /dev" \(Kernel and AMI mismatch\)](#KernelOld)
+ ["FATAL: Could not load /lib/modules" or "BusyBox" \(Missing kernel modules\)](#KernelMissing)
+ [ERROR Invalid kernel \(EC2 incompatible kernel\)](#KernelInvalid)
+ [request\_module: runaway loop modprobe \(Looping legacy kernel modprobe on older Linux versions\)](#FilesystemUnknown)
+ [fsck: No such file or directory while trying to open\.\.\. \(File system not found\)](#FilesystemFschk)
+ [General error mounting filesystems \(Failed mount\)](#FilesystemGeneral)
+ [VFS: Unable to mount root fs on unknown\-block \(Root filesystem mismatch\)](#FilesystemKernel)
+ [Error: Unable to determine major/minor number of root device\.\.\. \(Root file system/device mismatch\)](#FilesystemError)
+ [XENBUS: Device with no driver\.\.\.](#FilesystemXenbus)
+ [\.\.\. days without being checked, check forced \(File system check required\)](#FilesystemCheck)
+ [fsck died with exit status\.\.\. \(Missing device\)](#FilesystemFschkDied)
+ [GRUB prompt \(grubdom>\)](#OpSystemGrub)
+ [Bringing up interface eth0: Device eth0 has different MAC address than expected, ignoring\. \(Hard\-coded MAC address\)](#OpSystemBringing)
+ [Unable to load SELinux Policy\. Machine is in enforcing mode\. Halting now\. \(SELinux misconfiguration\)](#OpSystemUnable)
+ [XENBUS: Timeout connecting to devices \(Xenbus timeout\)](#OpSystemXenbus)

## Review Status Check Information<a name="InitialSteps"></a>

**To investigate impaired instances using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. In the details pane, choose **Status Checks** to see the individual results for all **System Status Checks** and **Instance Status Checks**\.

If a system status check has failed, you can try one of the following options:
+ Create an instance recovery alarm\. For more information, see [Create Alarms That Stop, Terminate, Reboot, or Recover an Instance](UsingAlarmActions.md)\.
+ If you changed the instance type to a [Nitro\-based instance](instance-types.md#ec2-nitro-instances), status checks fail if you migrated from an instance that does not have the required ENA and NVMe drivers\. For more information, see [Compatibility for Resizing Instances](ec2-instance-resize.md#resize-limitations)\.
+ For an instance using an Amazon EBS\-backed AMI, stop and restart the instance\.
+ For an instance using an instance\-store backed AMI, terminate the instance and launch a replacement\.
+ Wait for Amazon EC2 to resolve the issue\.
+ Post your issue to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\.
+ If your instance is in an Auto Scaling group, the Amazon EC2 Auto Scaling service automatically launches a replacement instance\. For more information, see [Health Checks for Auto Scaling Instances](https://docs.aws.amazon.com/autoscaling/ec2/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ Retrieve the system log and look for errors\.

## Retrieve the System Logs<a name="troubleshooting-retrieve-system-logs"></a>

If an instance status check fails, you can reboot the instance and retrieve the system logs\. The logs may reveal an error that can help you troubleshoot the issue\. Rebooting clears unnecessary information from the logs\.

**To reboot an instance and retrieve the system log**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select your instance\.

1. Choose **Actions**, **Instance State**, **Reboot**\. It may take a few minutes for your instance to reboot\.

1. Verify that the problem still exists; in some cases, rebooting may resolve the problem\.

1. When the instance is in the `running` state, choose **Actions**, **Instance Settings**, **Get System Log**\. 

1. Review the log that appears on the screen, and use the list of known system log error statements below to troubleshoot your issue\.

1. If your experience differs from our check results, or if you are having an issue with your instance that our checks did not detect, choose **Submit feedback** on the **Status Checks** tab to help us improve our detection tests\.

1. If your issue is not resolved, you can post your issue to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\.

## Troubleshooting System Log Errors for Linux\-Based Instances<a name="system-log-errors-linux"></a>

For Linux\-based instances that have failed an instance status check, such as the instance reachability check, verify that you followed the steps above to retrieve the system log\. The following list contains some common system log errors and suggested actions you can take to resolve the issue for each error\.

**Memory Errors**
+ [Out of memory: kill process](#MemoryOOM)
+ [ERROR: mmu\_update failed \(Memory management update failed\)](#MemoryMMU)

**Device Errors**
+ [I/O Error \(Block Device Failure\)](#DeviceBlock)
+ [I/O ERROR: neither local nor remote disk \(Broken distributed block device\)](#DeviceDistributed)

**Kernel Errors**
+ [request\_module: runaway loop modprobe \(Looping legacy kernel modprobe on older Linux versions\)](#KernelLoop)
+ ["FATAL: kernel too old" and "fsck: No such file or directory while trying to open /dev" \(Kernel and AMI mismatch\) ](#KernelOld)
+ ["FATAL: Could not load /lib/modules" or "BusyBox" \(Missing kernel modules\)](#KernelMissing)
+ [ERROR Invalid kernel \(EC2 incompatible kernel\)](#KernelInvalid)

**File System Errors**
+ [request\_module: runaway loop modprobe \(Looping legacy kernel modprobe on older Linux versions\)](#FilesystemUnknown)
+ [fsck: No such file or directory while trying to open\.\.\. \(File system not found\)](#FilesystemFschk)
+ [General error mounting filesystems \(Failed mount\)](#FilesystemGeneral)
+ [VFS: Unable to mount root fs on unknown\-block \(Root filesystem mismatch\)](#FilesystemKernel)
+ [Error: Unable to determine major/minor number of root device\.\.\. \(Root file system/device mismatch\)](#FilesystemError)
+ [XENBUS: Device with no driver\.\.\.](#FilesystemXenbus)
+ [\.\.\. days without being checked, check forced \(File system check required\)](#FilesystemCheck)
+ [fsck died with exit status\.\.\. \(Missing device\)](#FilesystemFschkDied)

**Operating System Errors**
+ [GRUB prompt \(grubdom>\)](#OpSystemGrub)
+ [Bringing up interface eth0: Device eth0 has different MAC address than expected, ignoring\. \(Hard\-coded MAC address\) ](#OpSystemBringing)
+ [Unable to load SELinux Policy\. Machine is in enforcing mode\. Halting now\. \(SELinux misconfiguration\)](#OpSystemUnable)
+ [XENBUS: Timeout connecting to devices \(Xenbus timeout\)](#OpSystemXenbus)

## Out of memory: kill process<a name="MemoryOOM"></a>

An out\-of\-memory error is indicated by a system log entry similar to the one shown below\.

```
[115879.769795] Out of memory: kill process 20273 (httpd) score 1285879
or a child 
[115879.769795] Killed process 1917 (php-cgi) vsz:467184kB, anon-
rss:101196kB, file-rss:204kB
```

### Potential Cause<a name="MemoryOOM-potential-cause"></a>

Exhausted memory

### Suggested Actions<a name="MemoryOOM-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## ERROR: mmu\_update failed \(Memory management update failed\)<a name="MemoryMMU"></a>

Memory management update failures are indicated by a system log entry similar to the following:

```
...
Press `ESC' to enter the menu... 0   [H[J  Booting 'Amazon Linux 2011.09 (2.6.35.14-95.38.amzn1.i686)'


root (hd0)

 Filesystem type is ext2fs, using whole disk

kernel /boot/vmlinuz-2.6.35.14-95.38.amzn1.i686 root=LABEL=/ console=hvc0 LANG=

en_US.UTF-8 KEYTABLE=us

initrd /boot/initramfs-2.6.35.14-95.38.amzn1.i686.img

ERROR: mmu_update failed with rc=-22
```

### Potential Cause<a name="MemoryMMU-potential-cause"></a>

Issue with Amazon Linux

### Suggested Action<a name="MemoryMMU-suggested-actions"></a>

Post your issue to the [Developer Forums](https://forums.aws.amazon.com/) or contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.

## I/O Error \(Block Device Failure\)<a name="DeviceBlock"></a>

An input/output error is indicated by a system log entry similar to the following example:

```
[9943662.053217] end_request: I/O error, dev sde, sector 52428288
[9943664.191262] end_request: I/O error, dev sde, sector 52428168
[9943664.191285] Buffer I/O error on device md0, logical block 209713024
[9943664.191297] Buffer I/O error on device md0, logical block 209713025
[9943664.191304] Buffer I/O error on device md0, logical block 209713026
[9943664.191310] Buffer I/O error on device md0, logical block 209713027
[9943664.191317] Buffer I/O error on device md0, logical block 209713028
[9943664.191324] Buffer I/O error on device md0, logical block 209713029
[9943664.191332] Buffer I/O error on device md0, logical block 209713030
[9943664.191339] Buffer I/O error on device md0, logical block 209713031
[9943664.191581] end_request: I/O error, dev sde, sector 52428280
[9943664.191590] Buffer I/O error on device md0, logical block 209713136
[9943664.191597] Buffer I/O error on device md0, logical block 209713137
[9943664.191767] end_request: I/O error, dev sde, sector 52428288
[9943664.191970] end_request: I/O error, dev sde, sector 52428288
[9943664.192143] end_request: I/O error, dev sde, sector 52428288
[9943664.192949] end_request: I/O error, dev sde, sector 52428288
[9943664.193112] end_request: I/O error, dev sde, sector 52428288
[9943664.193266] end_request: I/O error, dev sde, sector 52428288
...
```

### Potential Causes<a name="DeviceBlock-potential-cause"></a>


| Instance type  | Potential cause | 
| --- | --- | 
|  Amazon EBS\-backed  |  A failed Amazon EBS volume   | 
|  Instance store\-backed  |  A failed physical drive   | 

### Suggested Actions<a name="DeviceBlock-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |   Terminate the instance and launch a new instance\.  Data cannot be recovered\. Recover from backups\.   It's a good practice to use either Amazon S3 or Amazon EBS for backups\. Instance store volumes are directly tied to single host and single disk failures\.   | 

## I/O ERROR: neither local nor remote disk \(Broken distributed block device\)<a name="DeviceDistributed"></a>

An input/output error on the device is indicated by a system log entry similar to the following example:

```
...
block drbd1: Local IO failed in request_timer_fn. Detaching...

Aborting journal on device drbd1-8.

block drbd1: IO ERROR: neither local nor remote disk

Buffer I/O error on device drbd1, logical block 557056

lost page write due to I/O error on drbd1

JBD2: I/O error detected when updating journal superblock for drbd1-8.
```

### Potential Causes<a name="DeviceDistributed-potential-cause"></a>


| Instance type  | Potential cause | 
| --- | --- | 
|  Amazon EBS\-backed  |  A failed Amazon EBS volume   | 
|  Instance store\-backed  |  A failed physical drive   | 

### Suggested Action<a name="DeviceDistributed-suggested-actions"></a>

Terminate the instance and launch a new instance\. 

For an Amazon EBS\-backed instance you can recover data from a recent snapshot by creating an image from it\. Any data added after the snapshot cannot be recovered\.

## request\_module: runaway loop modprobe \(Looping legacy kernel modprobe on older Linux versions\)<a name="KernelLoop"></a>

This condition is indicated by a system log similar to the one shown below\. Using an unstable or old Linux kernel \(for example, 2\.6\.16\-xenU\) can cause an interminable loop condition at startup\.

```
Linux version 2.6.16-xenU (builder@xenbat.amazonsa) (gcc version 4.0.1 
20050727 (Red Hat 4.0.1-5)) #1 SMP Mon May 28 03:41:49 SAST 2007

BIOS-provided physical RAM map:

 Xen: 0000000000000000 - 0000000026700000 (usable)

0MB HIGHMEM available.
...

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c
```

### Suggested Actions<a name="KernelLoop-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use a newer kernel, either GRUB\-based or static, using one of the following options: Option 1: Terminate the instance and launch a new instance, specifying the `–kernel` and `–ramdisk` parameters\. Option 2: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Terminate the instance and launch a new instance, specifying the `–kernel` and `–ramdisk` parameters\.   | 

## "FATAL: kernel too old" and "fsck: No such file or directory while trying to open /dev" \(Kernel and AMI mismatch\)<a name="KernelOld"></a>

This condition is indicated by a system log similar to the one shown below\.

```
Linux version 2.6.16.33-xenU (root@dom0-0-50-45-1-a4-ee.z-2.aes0.internal) 
(gcc version 4.1.1 20070105 (Red Hat 4.1.1-52)) #2 SMP Wed Aug 15 17:27:36 SAST 2007
...
FATAL: kernel too old
Kernel panic - not syncing: Attempted to kill init!
```

### Potential Causes<a name="KernelOld-potential-cause"></a>

Incompatible kernel and userland

### Suggested Actions<a name="KernelOld-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## "FATAL: Could not load /lib/modules" or "BusyBox" \(Missing kernel modules\)<a name="KernelMissing"></a>

This condition is indicated by a system log similar to the one shown below\.

```
[    0.370415] Freeing unused kernel memory: 1716k freed 
Loading, please wait...
WARNING: Couldn't open directory /lib/modules/2.6.34-4-virtual: No such file or directory
FATAL: Could not open /lib/modules/2.6.34-4-virtual/modules.dep.temp for writing: No such file or directory
FATAL: Could not load /lib/modules/2.6.34-4-virtual/modules.dep: No such file or directory
Couldn't get a file descriptor referring to the console
Begin: Loading essential drivers... ...
FATAL: Could not load /lib/modules/2.6.34-4-virtual/modules.dep: No such file or directory
FATAL: Could not load /lib/modules/2.6.34-4-virtual/modules.dep: No such file or directory
Done.
Begin: Running /scripts/init-premount ...
Done.
Begin: Mounting root file system... ...
Begin: Running /scripts/local-top ...
Done.
Begin: Waiting for root file system... ...
Done.
Gave up waiting for root device.  Common problems:
 - Boot args (cat /proc/cmdline)
   - Check rootdelay= (did the system wait long enough?)
   - Check root= (did the system wait for the right device?)
 - Missing modules (cat /proc/modules; ls /dev)
FATAL: Could not load /lib/modules/2.6.34-4-virtual/modules.dep: No such file or directory
FATAL: Could not load /lib/modules/2.6.34-4-virtual/modules.dep: No such file or directory
ALERT! /dev/sda1 does not exist. Dropping to a shell!


BusyBox v1.13.3 (Ubuntu 1:1.13.3-1ubuntu5) built-in shell (ash)
Enter 'help' for a list of built-in commands.

(initramfs)
```

### Potential Causes<a name="KernelMissing-potential-cause"></a>

One or more of the following conditions can cause this problem:
+ Missing ramdisk 
+ Missing correct modules from ramdisk
+ Amazon EBS root volume not correctly attached as `/dev/sda1`

### Suggested Actions<a name="KernelMissing-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## ERROR Invalid kernel \(EC2 incompatible kernel\)<a name="KernelInvalid"></a>

This condition is indicated by a system log similar to the one shown below\.

```
...
root (hd0)

 Filesystem type is ext2fs, using whole disk

kernel /vmlinuz root=/dev/sda1 ro

initrd /initrd.img

ERROR Invalid kernel: elf_xen_note_check: ERROR: Will only load images 
built for the generic loader or Linux images
xc_dom_parse_image returned -1

Error 9: Unknown boot failure

  Booting 'Fallback'
  
root (hd0)

 Filesystem type is ext2fs, using whole disk

kernel /vmlinuz.old root=/dev/sda1 ro

Error 15: File not found
```

### Potential Causes<a name="KernelInvalid-potential-cause"></a>

One or both of the following conditions can cause this problem:
+ Supplied kernel is not supported by GRUB 
+ Fallback kernel does not exist 

### Suggested Actions<a name="KernelInvalid-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## request\_module: runaway loop modprobe \(Looping legacy kernel modprobe on older Linux versions\)<a name="FilesystemUnknown"></a>

This condition is indicated by a system log similar to the one shown below\. Using an unstable or old Linux kernel \(for example, 2\.6\.16\-xenU\) can cause an interminable loop condition at startup\.

```
Linux version 2.6.16-xenU (builder@xenbat.amazonsa) (gcc version 4.0.1 
20050727 (Red Hat 4.0.1-5)) #1 SMP Mon May 28 03:41:49 SAST 2007

BIOS-provided physical RAM map:

 Xen: 0000000000000000 - 0000000026700000 (usable)

0MB HIGHMEM available.
...

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c

request_module: runaway loop modprobe binfmt-464c
```

### Suggested Actions<a name="FilesystemUnknown-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use a newer kernel, either GRUB\-based or static, using one of the following options: Option 1: Terminate the instance and launch a new instance, specifying the `–kernel` and `–ramdisk` parameters\. Option 2: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Terminate the instance and launch a new instance, specifying the `–kernel` and `–ramdisk` parameters\.   | 

## fsck: No such file or directory while trying to open\.\.\. \(File system not found\)<a name="FilesystemFschk"></a>

This condition is indicated by a system log similar to the one shown below\.

```
		Welcome to Fedora 
		Press 'I' to enter interactive startup.
Setting clock : Wed Oct 26 05:52:05 EDT 2011 [  OK  ]

Starting udev: [  OK  ]

Setting hostname localhost:  [  OK  ]

No devices found
Setting up Logical Volume Management: File descriptor 7 left open
  No volume groups found
[  OK  ]

Checking filesystems
Checking all file systems.
[/sbin/fsck.ext3 (1) -- /] fsck.ext3 -a /dev/sda1 
/dev/sda1: clean, 82081/1310720 files, 2141116/2621440 blocks
[/sbin/fsck.ext3 (1) -- /mnt/dbbackups] fsck.ext3 -a /dev/sdh 
fsck.ext3: No such file or directory while trying to open /dev/sdh

/dev/sdh: 
The superblock could not be read or does not describe a correct ext2
filesystem.  If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:
    e2fsck -b 8193 <device>

[FAILED]


*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
Give root password for maintenance
(or type Control-D to continue):
```

### Potential Causes<a name="FilesystemFschk-potential-cause"></a>
+ A bug exists in ramdisk filesystem definitions /etc/fstab
+ Misconfigured filesystem definitions in /etc/fstab
+ Missing/failed drive

### Suggested Actions<a name="FilesystemFschk-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html) The sixth field in the fstab defines availability requirements of the mount – a nonzero value implies that an fsck will be done on that volume and *must* succeed\. Using this field can be problematic in Amazon EC2 because a failure typically results in an interactive console prompt that is not currently available in Amazon EC2\. Use care with this feature and read the Linux man page for fstab\.  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## General error mounting filesystems \(Failed mount\)<a name="FilesystemGeneral"></a>

This condition is indicated by a system log similar to the one shown below\.

```
Loading xenblk.ko module 
xen-vbd: registered block device major 8

Loading ehci-hcd.ko module
Loading ohci-hcd.ko module
Loading uhci-hcd.ko module
USB Universal Host Controller Interface driver v3.0

Loading mbcache.ko module
Loading jbd.ko module
Loading ext3.ko module
Creating root device.
Mounting root filesystem.
kjournald starting.  Commit interval 5 seconds

EXT3-fs: mounted filesystem with ordered data mode.

Setting up other filesystems.
Setting up new root fs
no fstab.sys, mounting internal defaults
Switching to new root and running init.
unmounting old /dev
unmounting old /proc
unmounting old /sys
mountall:/proc: unable to mount: Device or resource busy
mountall:/proc/self/mountinfo: No such file or directory
mountall: root filesystem isn't mounted
init: mountall main process (221) terminated with status 1

General error mounting filesystems.
A maintenance shell will now be started.
CONTROL-D will terminate this shell and re-try.
Press enter for maintenance
(or type Control-D to continue):
```

### Potential Causes<a name="FilesystemGeneral-potential-cause"></a>


| Instance type  | Potential cause | 
| --- | --- | 
|  Amazon EBS\-backed  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

### Suggested Actions<a name="FilesystemGeneral-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Try one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## VFS: Unable to mount root fs on unknown\-block \(Root filesystem mismatch\)<a name="FilesystemKernel"></a>

This condition is indicated by a system log similar to the one shown below\.

```
Linux version 2.6.16-xenU (builder@xenbat.amazonsa) (gcc version 4.0.1 
 20050727 (Red Hat 4.0.1-5)) #1 SMP Mon May 28 03:41:49 SAST 2007
...
Kernel command line:  root=/dev/sda1 ro 4
...
Registering block device major 8
...
Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(8,1)
```

### Potential Causes<a name="FilesystemKernel-potential-cause"></a>


| Instance type  | Potential cause | 
| --- | --- | 
|  Amazon EBS\-backed  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Hardware device failure\.  | 

### Suggested Actions<a name="FilesystemKernel-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Terminate the instance and launch a new instance using a modern kernel\.   | 

## Error: Unable to determine major/minor number of root device\.\.\. \(Root file system/device mismatch\)<a name="FilesystemError"></a>

This condition is indicated by a system log similar to the one shown below\.

```
...
XENBUS: Device with no driver: device/vif/0
XENBUS: Device with no driver: device/vbd/2048
drivers/rtc/hctosys.c: unable to open rtc device (rtc0)
Initializing network drop monitor service
Freeing unused kernel memory: 508k freed
:: Starting udevd...
done.
:: Running Hook [udev]
:: Triggering uevents...<30>udevd[65]: starting version 173
done.
Waiting 10 seconds for device /dev/xvda1 ...
Root device '/dev/xvda1' doesn't exist. Attempting to create it.
ERROR: Unable to determine major/minor number of root device '/dev/xvda1'.
You are being dropped to a recovery shell
    Type 'exit' to try and continue booting
sh: can't access tty; job control turned off
[ramfs /]#
```

### Potential Causes<a name="FilesystemError-potential-cause"></a>
+ Missing or incorrectly configured virtual block device driver
+ Device enumeration clash \(sda versus xvda or sda instead of sda1\)
+ Incorrect choice of instance kernel

### Suggested Actions<a name="FilesystemError-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## XENBUS: Device with no driver\.\.\.<a name="FilesystemXenbus"></a>

This condition is indicated by a system log similar to the one shown below\.

```
XENBUS: Device with no driver: device/vbd/2048
drivers/rtc/hctosys.c: unable to open rtc device (rtc0)
Initalizing network drop monitor service
Freeing unused kernel memory: 508k freed
:: Starting udevd...
done.
:: Running Hook [udev]
:: Triggering uevents...<30>udevd[65]: starting version 173
done.
Waiting 10 seconds for device /dev/xvda1 ...
Root device '/dev/xvda1' doesn't exist. Attempting to create it.
ERROR: Unable to determine major/minor number of root device '/dev/xvda1'.
You are being dropped to a recovery shell
    Type 'exit' to try and continue booting
sh: can't access tty; job control turned off
[ramfs /]#
```

### Potential Causes<a name="FilesystemXenbus-potential-cause"></a>
+ Missing or incorrectly configured virtual block device driver
+ Device enumeration clash \(sda versus xvda\)
+ Incorrect choice of instance kernel

### Suggested Actions<a name="FilesystemXenbus-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## \.\.\. days without being checked, check forced \(File system check required\)<a name="FilesystemCheck"></a>

This condition is indicated by a system log similar to the one shown below\.

```
...
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext3 (1) -- /] fsck.ext3 -a /dev/sda1 
/dev/sda1 has gone 361 days without being checked, check forced
```

### Potential Causes<a name="FilesystemCheck-potential-cause"></a>

Filesystem check time passed; a filesystem check is being forced\.

### Suggested Actions<a name="FilesystemCheck-suggested-actions"></a>
+ Wait until the filesystem check completes\. A filesystem check can take a long time depending on the size of the root filesystem\. 
+  Modify your filesystems to remove the filesystem check \(fsck\) enforcement using tune2fs or tools appropriate for your filesystem\. 

## fsck died with exit status\.\.\. \(Missing device\)<a name="FilesystemFschkDied"></a>

This condition is indicated by a system log similar to the one shown below\.

```
Cleaning up ifupdown....
Loading kernel modules...done.
...
Activating lvm and md swap...done.
Checking file systems...fsck from util-linux-ng 2.16.2
/sbin/fsck.xfs: /dev/sdh does not exist
fsck died with exit status 8
[31mfailed (code 8).[39;49m
```

### Potential Causes<a name="FilesystemFschkDied-potential-cause"></a>
+ Ramdisk looking for missing drive
+ Filesystem consistency check forced
+ Drive failed or detached

### Suggested Actions<a name="FilesystemFschkDied-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Try one or more of the following to resolve the issue: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Try one or more of the following to resolve the issue: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## GRUB prompt \(grubdom>\)<a name="OpSystemGrub"></a>

This condition is indicated by a system log similar to the one shown below\. 

```
    GNU GRUB  version 0.97  (629760K lower / 0K upper memory)

       [ Minimal BASH-like line editing is supported.   For

         the   first   word,  TAB  lists  possible  command

         completions.  Anywhere else TAB lists the possible

         completions of a device/filename. ]

grubdom>
```

### Potential Causes<a name="OpSystem-potential-cause"></a>


| Instance type  | Potential causes | 
| --- | --- | 
|  Amazon EBS\-backed  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

### Suggested Actions<a name="OpSystem-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Option 1: Modify the AMI and relaunch the instance: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html) Option 2: Fix the existing instance: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Option 1: Modify the AMI and relaunch the instance: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html) Option 2: Terminate the instance and launch a new instance, specifying the correct kernel\.  To recover data from the existing instance, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.   | 

## Bringing up interface eth0: Device eth0 has different MAC address than expected, ignoring\. \(Hard\-coded MAC address\)<a name="OpSystemBringing"></a>

This condition is indicated by a system log similar to the one shown below\.

```
... 
Bringing up loopback interface:  [  OK  ]

Bringing up interface eth0:  Device eth0 has different MAC address than expected, ignoring.
[FAILED]

Starting auditd: [  OK  ]
```

### Potential Causes<a name="OpSystemBringing-potential-cause"></a>

There is a hardcoded interface MAC in the AMI configuration

### Suggested Actions<a name="OpSystemBringing-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html) OR Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## Unable to load SELinux Policy\. Machine is in enforcing mode\. Halting now\. \(SELinux misconfiguration\)<a name="OpSystemUnable"></a>

This condition is indicated by a system log similar to the one shown below\.

```
audit(1313445102.626:2): enforcing=1 old_enforcing=0 auid=4294967295
Unable to load SELinux Policy. Machine is in enforcing mode. Halting now.
Kernel panic - not syncing: Attempted to kill init!
```

### Potential Causes<a name="OpSystemUnable-potential-cause"></a>

SELinux has been enabled in error:
+ Supplied kernel is not supported by GRUB
+ Fallback kernel does not exist

### Suggested Actions<a name="OpSystemUnable-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Use the following procedure: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

## XENBUS: Timeout connecting to devices \(Xenbus timeout\)<a name="OpSystemXenbus"></a>

This condition is indicated by a system log similar to the one shown below\.

```
Linux version 2.6.16-xenU (builder@xenbat.amazonsa) (gcc version 4.0.1 
20050727 (Red Hat 4.0.1-5)) #1 SMP Mon May 28 03:41:49 SAST 2007
...
XENBUS: Timeout connecting to devices!
...
Kernel panic - not syncing: No init found.  Try passing init= option to kernel.
```

### Potential Causes<a name="OpSystemXenbus-potential-cause"></a>
+ The block device is not connected to the instance
+ This instance is using an old instance kernel

### Suggested Actions<a name="OpSystemXenbus-suggested-actions"></a>


| For this instance type  | Do this | 
| --- | --- | 
|  Amazon EBS\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 
|  Instance store\-backed  |  Do one of the following: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstances.html)  | 

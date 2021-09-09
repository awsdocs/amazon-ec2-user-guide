# Troubleshoot your Linux instance using GRUB<a name="grub"></a>

GNU GRUB \(short for GNU GRand Unified Bootloader, commonly referred to as GRUB\) is the default boot loader for most Linux operating systems\. From the GRUB menu, you can select which kernel to boot into, or modify menu entries to change how the kernel will boot\. This can be useful when troubleshooting a failing instance\.

The GRUB menu is displayed during the boot process\. The menu is not accessible via normal SSH, but you can access it via the EC2 Serial Console\.

**Topics**
+ [Prerequisites](#grub-prerequisites)
+ [Configure GRUB](#configure-grub)
+ [Use GRUB](#use-grub)

## Prerequisites<a name="grub-prerequisites"></a>

Before you can configure and use GRUB, you must grant access to the serial console\. For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.

## Configure GRUB<a name="configure-grub"></a>

Before you can use GRUB via the serial console, you must configure your instance to use GRUB via the serial console\.

To configure GRUB, choose one of the following procedures based on the AMI that was used to launch the instance\.

------
#### [ Amazon Linux 2 ]

**To configure GRUB on an Amazon Linux 2 instance**

1. [Connect](AccessingInstances.md) to your instance\.

1. Add or change the following options in `/etc/default/grub`:
   + Set `GRUB_TIMEOUT=1`\.
   + Add `GRUB_TERMINAL="console serial"`\.
   + Add `GRUB_SERIAL_COMMAND="serial --speed=115200"`\.

   The following is an example of `/etc/default/grub`\. You might need to change the configuration based on your system setup\.

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 rd.emergency=poweroff rd.shell=0"
   GRUB_TIMEOUT=1
   GRUB_DISABLE_RECOVERY="true"
   GRUB_TERMINAL="console serial"
   GRUB_SERIAL_COMMAND="serial --speed=115200"
   ```

1. Apply the updated configuration by running the following command\.

   ```
   [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

------
#### [ Ubuntu ]

**To configure GRUB on an Ubuntu instance**

1. [Connect](AccessingInstances.md) to your instance\.

1. Add or change the following options in `/etc/default/grub.d/50-cloudimg-settings.cfg`:
   + Set `GRUB_TIMEOUT=1`\.
   + Add `GRUB_TIMEOUT_STYLE=menu`\.
   + Add `GRUB_TERMINAL="console serial"`\.
   + Remove `GRUB_HIDDEN_TIMEOUT`\.
   + Add `GRUB_SERIAL_COMMAND="serial --speed=115200"`\.

   The following is an example of `/etc/default/grub.d/50-cloudimg-settings.cfg`\. You might need to change the configuration based on your system setup\.

   ```
   # Cloud Image specific Grub settings for Generic Cloud Images
   # CLOUD_IMG: This file was created/modified by the Cloud Image build process
   
   # Set the recordfail timeout
   GRUB_RECORDFAIL_TIMEOUT=0
   
   # Do not wait on grub prompt
   GRUB_TIMEOUT=1
   GRUB_TIMEOUT_STYLE=menu
   
   # Set the default commandline
   GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 nvme_core.io_timeout=4294967295"
   
   # Set the grub console type
   GRUB_TERMINAL="console serial"
   GRUB_SERIAL_COMMAND="serial --speed 115200"
   ```

1. Apply the updated configuration by running the following command\.

   ```
   [ec2-user ~]$ sudo update-grub
   ```

------
#### [ RHEL ]

**To configure GRUB on a RHEL instance**

1. [Connect](AccessingInstances.md) to your instance\.

1. Add or change the following options in `/etc/default/grub`:
   + Remove `GRUB_TERMINAL_OUTPUT`\.
   + Add `GRUB_TERMINAL="console serial"`\.
   + Add `GRUB_SERIAL_COMMAND="serial --speed=115200"`\.

   The following is an example of `/etc/default/grub`\. You might need to change the configuration based on your system setup\.

   ```
   GRUB_TIMEOUT=1
   GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
   GRUB_DEFAULT=saved
   GRUB_DISABLE_SUBMENU=true
   GRUB_CMDLINE_LINUX="console=ttyS0,115200n8 console=tty0 net.ifnames=0 rd.blacklist=nouveau nvme_core.io_timeout=4294967295 crashkernel=auto"
   GRUB_DISABLE_RECOVERY="true"
   GRUB_ENABLE_BLSCFG=true
   GRUB_TERMINAL="console serial"
   GRUB_SERIAL_COMMAND="serial --speed=115200"
   ```

1. Apply the updated configuration by running the following command\.

   ```
   [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

------
#### [ CentOS ]

For instances that are launched using a CentOS AMI, GRUB is configured for the serial console by default\.

The following is an example of `/etc/default/grub`\. Your configuration might be different based on your system setup\.

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial --speed=115200"
GRUB_CMDLINE_LINUX="console=tty0 crashkernel=auto console=ttyS0,115200"
GRUB_DISABLE_RECOVERY="true"
```

------

## Use GRUB<a name="use-grub"></a>

After GRUB is configured, connect to the serial console and reboot the instance with the reboot command\. During reboot, you see the GRUB menu\. Press any key when the GRUB menu appears to stop the boot process, allowing you to interact with the GRUB menu\.

**Topics**
+ [Single user mode](#single-user-mode)
+ [Emergency mode](#emergency-mode)

### Single user mode<a name="single-user-mode"></a>

Single user mode will boot the kernel at a lower runlevel\. For example, it might mount the filesystem but not activate the network, giving you the opportunity to perform the maintenance necessary to fix the instance\.

**To boot into single user mode**

1. [Connect](connect-to-serial-console.md#sc-connection-methods) to the instance's serial console\.

1. Reboot the instance using the following command\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. During reboot, when the GRUB menu appears, press any key to stop the boot process\.

1. In the GRUB menu, use the arrow keys to select the kernel to boot into, and press `e` on your keyboard\. 

1. Use the arrow keys to locate your cursor on the line containing the kernel\. The line begins with either `linux` or `linux16` depending on the AMI that was used to launch the instance\. For Ubuntu, two lines begin with `linux`, which must both be modified in the next step\.

1. At the end of the line, add the word `single`\.

   The following is an example for Amazon Linux 2\.

   ```
   linux /boot/vmlinuz-4.14.193-149.317.amzn2.aarch64 root=UUID=d33f9c9a-\
   dadd-4499-938d-ebbf42c3e499 ro  console=tty0 console=ttyS0,115200n8 net.ifname\
   s=0 biosdevname=0 nvme_core.io_timeout=4294967295 rd.emergency=poweroff rd.she\
   ll=0 single
   ```

1. Press **Ctrl\+X** to boot into single user mode\.

1. At the `login` prompt, enter the user name of the password\-based user that you [set up previously](configure-access-to-serial-console.md#set-user-password), and then press **Enter**\.

1. At the `Password` prompt, enter the password, and then press **Enter**\.

### Emergency mode<a name="emergency-mode"></a>

Emergency mode is similar to single user mode except that the kernel runs at the lowest runlevel possible\.

To boot into emergency mode, follow the steps in [Single user mode](#single-user-mode) in the preceding section, but at step 6 add the word `emergency` instead of `single`\.
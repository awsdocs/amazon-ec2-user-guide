# Troubleshoot your Linux instance using SysRq<a name="SysRq"></a>

The System Request \(SysRq\) key, which is sometimes referred to as "magic SysRq", can be used to directly send the kernel a command, outside of a shell, and the kernel will respond, regardless of what the kernel is doing\. For example, if the instance has stopped responding, you can use the SysRq key to tell the kernel to crash or reboot\. For more information, see [Magic SysRq key](https://en.wikipedia.org/wiki/Magic_SysRq_key) in Wikipedia\.

**Topics**
+ [Prerequisites](#sysrq-prerequisites)
+ [Configure SysRq](#configure-sysrq)
+ [Use SysRq](#use-sysrq)

## Prerequisites<a name="sysrq-prerequisites"></a>

Before you can configure and use SysRq, you must grant access to the serial console\. For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.

## Configure SysRq<a name="configure-sysrq"></a>

To configure SysRq, you enable the SysRq commands for the current boot cycle\. To make the configuration persistent, you can also enable the SysRq commands for subsequent boots\.

**To enable all SysRq commands for the current boot cycle**

1. [Connect](AccessingInstances.md) to your instance\.

1. Run the following command\.

   ```
   [ec2-user ~]$ sudo sysctl -w kernel.sysrq=1
   ```
**Note**  
This setting will clear on the next reboot\.

**To enable all SysRq commands for subsequent boots**

1. Create the file `/etc/sysctl.d/99-sysrq.conf` and open it in your favorite editor\.

   ```
   [ec2-user ~]$ sudo vi /etc/sysctl.d/99-sysrq.conf
   ```

1. Add the following line\.

   ```
   kernel.sysrq=1
   ```

1. Reboot the instance to apply the changes\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. At the `login` prompt, enter the user name of the password\-based user that you [set up previously](configure-access-to-serial-console.md#set-user-password), and then press Enter\.

1. At the `Password` prompt, enter the password, and then press Enter\.

## Use SysRq<a name="use-sysrq"></a>

You can use SysRq commands in the EC2 Serial Console browser\-based client or in an SSH client\. The command to send a break request is different for each client\.

To use SysRq, choose one of the following procedures based on the client that you are using\.

------
#### [ Browser\-based client ]

**To use SysRq in the serial console browser\-based client**

1. [Connect](connect-to-serial-console.md#sc-connection-methods) to the instance's serial console\.

1. To send a break request, press `CTRL+0` \(zero\)\. If your keyboard supports it, you can also send a break request using the Pause or Break key\.

   ```
   [ec2-user ~]$ CTRL+0
   ```

1. To issue a SysRq command, press the key on your keyboard that corresponds to the required command\. For example, to display a list of SysRq commands, press `h`\.

   ```
   [ec2-user ~]$ h
   ```

   The `h` command outputs something similar to the following\.

   ```
   [ 1169.389495] sysrq: HELP : loglevel(0-9) reboot(b) crash(c) terminate-all-tasks(e) memory-full-oom-kill(f) kill-all-tasks(i) thaw-filesystems
   (j) sak(k) show-backtrace-all-active-cpus(l) show-memory-usage(m) nice-all-RT-tasks(n) poweroff(o) show-registers(p) show-all-timers(q) unraw(r
   ) sync(s) show-task-states(t) unmount(u) show-blocked-tasks(w) dump-ftrace-buffer(z)
   ```

------
#### [ SSH client ]

**To use SysRq in an SSH client**

1. [Connect](connect-to-serial-console.md#sc-connection-methods) to the instance's serial console\.

1. To send a break request, press `~B` \(tilde, followed by uppercase `B`\)\.

   ```
   [ec2-user ~]$ ~B
   ```

1. To issue a SysRq command, press the key on your keyboard that corresponds to the required command\. For example, to display a list of SysRq commands, press `h`\.

   ```
   [ec2-user ~]$ h
   ```

   The `h` command outputs something similar to the following\.

   ```
   [ 1169.389495] sysrq: HELP : loglevel(0-9) reboot(b) crash(c) terminate-all-tasks(e) memory-full-oom-kill(f) kill-all-tasks(i) thaw-filesystems
   (j) sak(k) show-backtrace-all-active-cpus(l) show-memory-usage(m) nice-all-RT-tasks(n) poweroff(o) show-registers(p) show-all-timers(q) unraw(r
   ) sync(s) show-task-states(t) unmount(u) show-blocked-tasks(w) dump-ftrace-buffer(z)
   ```
**Note**  
The command that you use for sending a break request might be different depending on the SSH client that you're using\.

------
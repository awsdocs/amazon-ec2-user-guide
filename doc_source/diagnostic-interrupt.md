# Sending a diagnostic interrupt \(for advanced users\)<a name="diagnostic-interrupt"></a>

**Warning**  
Diagnostic interrupts are intended for use by advanced users\. Incorrect usage could negatively impact your instance\. Sending a diagnostic interrupt to an instance could trigger an instance to crash and reboot, which could lead to the loss of data\.

You can send a diagnostic interrupt to an unreachable or unresponsive Linux instance to manually trigger a *kernel panic*\.

Linux operating systems typically crash and reboot when a kernel panic occurs\. The specific behavior of the operating system depends on its configuration\. A kernel panic can also be used to cause the instance's operating system kernel to perform tasks, such as generating a crash dump file\. You can then use the information in the crash dump file to conduct root cause analysis and debug the instance\.

The crash dump data is generated locally by the operating system on the instance itself\.

Before sending a diagnostic interrupt to your instance, we recommend that you consult the documentation for your operating system and then make the necessary configuration changes\.

**Topics**
+ [Supported instance types](#diagnostic-interrupt-instances)
+ [Prerequisites](#diagnostic-interrupt-prereqs)
+ [Sending a diagnostic interrupt](#diagnostic-interrupt-use)

## Supported instance types<a name="diagnostic-interrupt-instances"></a>

Diagnostic interrupt is supported on all Nitro\-based instance types, except A1\. For more information, see [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

## Prerequisites<a name="diagnostic-interrupt-prereqs"></a>

Before using a diagnostic interrupt, you must configure your instance's operating system\. This ensures that it performs the actions that you need when a kernel panic occurs\.

**To configure Amazon Linux 2 to generate a crash dump when a kernel panic occurs**

1. Connect to your instance\.

1. Install **kexec** and **kdump**\.

   ```
   [ec2-user ~]$ sudo yum install kexec-tools -y
   ```

1. Configure the kernel to reserve an appropriate amount of memory for the secondary kernel\. The amount of memory to reserve depends on the total available memory of your instance\. Open the `/etc/default/grub` file using your preferred text editor, locate the line that starts with `GRUB_CMDLINE_LINUX_DEFAULT`, and then add the `crashkernel` parameter in the following format: `crashkernel=memory_to_reserve`\. For example, to reserve `160MB`, modify the `grub` file as follows:

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="crashkernel=160M console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 rd.emergency=poweroff rd.shell=0"
   GRUB_TIMEOUT=0
   GRUB_DISABLE_RECOVERY="true"
   ```

1. Save the changes and close the `grub` file\.

1. Rebuild the GRUB2 configuration file\.

   ```
   [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

1. On instances based on Intel and AMD processors, the `send-diagnostic-interrupt` command sends an *unknown non\-maskable interrupt* \(NMI\) to the instance\. You must configure the kernel to crash when it receives the unknown NMI\. Open the `/etc/sysctl.conf` file using your preferred text editor and add the following\.

   ```
   kernel.unknown_nmi_panic=1
   ```

1. Reboot and reconnect to your instance\.

1. Verify that the kernel has been booted with the correct `crashkernel` parameter\.

   ```
   $ grep crashkernel /proc/cmdline
   ```

   The following example output indicates successful configuration\.

   ```
   BOOT_IMAGE=/boot/vmlinuz-4.14.128-112.105.amzn2.x86_64 root=UUID=a1e1011e-e38f-408e-878b-fed395b47ad6 ro crashkernel=160M console=tty0 console=ttyS0,115200n8 net.ifnames=0 biosdevname=0 nvme_core.io_timeout=4294967295 rd.emergency=poweroff rd.shell=0
   ```

1. Verify that the **kdump** service is running\.

   ```
   [ec2-user ~]$ systemctl status kdump.service
   ```

   The following example output shows the result if the **kdump** service is running\.

   ```
   kdump.service - Crash recovery kernel arming
      Loaded: loaded (/usr/lib/systemd/system/kdump.service; enabled; vendor preset: enabled)
      Active: active (exited) since Fri 2019-05-24 23:29:13 UTC; 22s ago
     Process: 2503 ExecStart=/usr/bin/kdumpctl start (code=exited, status=0/SUCCESS)
    Main PID: 2503 (code=exited, status=0/SUCCESS)
   ```

**Note**  
By default, the crash dump file is saved to `/var/crash/`\. To change the location, modify the `/etc/kdump.conf` file using your preferred text editor\.

**To configure Amazon Linux to generate a crash dump when a kernel panic occurs**

1. Connect to your instance\.

1. Install **kexec** and **kdump**\.

   ```
   [ec2-user ~]$ sudo yum install kexec-tools -y
   ```

1. Configure the kernel to reserve an appropriate amount of memory for the secondary kernel\. The amount of memory to reserve depends on the total available memory of your instance\.

   ```
   $ sudo grubby --args="crashkernel=memory_to_reserve" --update-kernel=ALL
   ```

   For example, to reserve `160MB` for the crash kernel, use the following command\. 

   ```
   $ sudo grubby --args="crashkernel=160M" --update-kernel=ALL
   ```

1. On instances based on Intel and AMD processors, the `send-diagnostic-interrupt` command sends an *unknown non\-maskable interrupt* \(NMI\) to the instance\. You must configure the kernel to crash when it receives the unknown NMI\. Open the `/etc/sysctl.conf` file using your preferred text editor and add the following\.

   ```
   kernel.unknown_nmi_panic=1
   ```

1. Reboot and reconnect to your instance\.

1. Verify that the kernel has been booted with the correct `crashkernel` parameter\.

   ```
   $ grep crashkernel /proc/cmdline
   ```

   The following example output indicates successful configuration\.

   ```
   root=LABEL=/ console=tty1 console=ttyS0 selinux=0 nvme_core.io_timeout=4294967295 LANG=en_US.UTF-8 KEYTABLE=us crashkernel=160M
   ```

1. Verify that the **kdump** service is running\.

   ```
   [ec2-user ~]$ sudo service kdump status
   ```

   If the service is running, the command returns the `Kdump is operational` response\.

**Note**  
By default, the crash dump file is saved to `/var/crash/`\. To change the location, modify the `/etc/kdump.conf` file using your preferred text editor\.

**To configure SUSE Linux Enterprise, Ubuntu, or Red Hat Enterprise Linux**  
See the following websites:
+ [SUSE Linux Enterprise](https://www.suse.com/support/kb/doc/?id=3374462)
+ [Ubuntu](https://ubuntu.com/server/docs/kernel-crash-dump)
+ [ Red Hat Enterprise Linux \(RHEL\)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/kernel_administration_guide/kernel_crash_dump_guide)

**Note**  
On instances based on Intel and AMD processors, the `send-diagnostic-interrupt` command sends an *unknown non\-maskable interrupt* \(NMI\) to the instance\. You must configure the kernel to crash when it receives the unknown NMI\. Add the following to your configuration file\.  

```
kernel.unknown_nmi_panic=1
```

## Sending a diagnostic interrupt<a name="diagnostic-interrupt-use"></a>

After you have completed the necessary configuration changes, you can send a diagnostic interrupt to your instance using the AWS CLI or Amazon EC2 API\.

**To send a diagnostic interrupt to your instance \(AWS CLI\)**  
Use the [send\-diagnostic\-interrupt](https://docs.aws.amazon.com/cli/latest/reference/ec2/send-diagnostic-interrupt.html) command and specify the instance ID\.

```
aws ec2 send-diagnostic-interrupt --instance-id i-1234567890abcdef0
```
# Disable KASLR on an instance \(Ubuntu only\)<a name="hibernation-disable-kaslr"></a>

To run hibernation on a newly launched instance with Ubuntu 16\.04 LTS \- Xenial, Ubuntu 18\.04 LTS \- Bionic released with serial number 20190722\.1 or later, or Ubuntu 20\.04 LTS \- Focal released with serial number 20210820 or later, we recommend disabling KASLR \(Kernel Address Space Layout Randomization\)\. On Ubuntu 16\.04 LTS, Ubuntu 18\.04 LTS, or Ubuntu 20\.04 LTS, KASLR is enabled by default\.

KASLR is a standard Linux kernel security feature that helps to mitigate exposure to and ramifications of yet\-undiscovered memory access vulnerabilities by randomizing the base address value of the kernel\. With KASLR enabled, there is a possibility that the instance might not resume after it has been hibernated\.

To learn more about KASLR, see [Ubuntu Features](https://wiki.ubuntu.com/Security/Features)\.

**To disable KASLR on an instance launched with Ubuntu**

1. Connect to your instance using SSH\. For more information, see [Connect to your Linux instance using SSH](AccessingInstancesLinux.md)\.

1. Open the `/etc/default/grub.d/50-cloudimg-settings.cfg` file in your editor of choice\. Edit the `GRUB_CMDLINE_LINUX_DEFAULT` line to append the `nokaslr` option to its end, as shown in the following example\.

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 nvme_core.io_timeout=4294967295 nokaslr"
   ```

1. Save the file and exit your editor\.

1. Run the following command to rebuild the grub configuration\.

   ```
   [ec2-user ~]$ sudo update-grub
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Run the following command to confirm that `nokaslr` has been added\.

   ```
   [ec2-user ~]$ cat /proc/cmdline
   ```

   The output of the command should include the `nokaslr` option\.
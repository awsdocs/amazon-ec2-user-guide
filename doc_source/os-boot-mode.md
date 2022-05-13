# Determine the boot mode of the operating system<a name="os-boot-mode"></a>

The boot mode of the operating system guides EC2 on which boot mode to use to boot an instance\. To view whether the operating system of your instance is configured for UEFI, you need to connect to your instance via SSH\.

**To determine the boot mode of the instance’s operating system**

1. [Connect to your Linux instance using SSH](AccessingInstancesLinux.md)\.

1. To view the boot mode of the operating system, try one of the following:
   + Run the following command\.

     ```
     [ec2-user ~]$ sudo /usr/sbin/efibootmgr
     ```

     Expected output from an instance booted in UEFI boot mode

     ```
     BootCurrent: 0001
     Timeout: 0 seconds
     BootOrder: 0000,0001
     Boot0000* UiApp
     Boot0001* UEFI Amazon Elastic Block Store vol-xyz
     ```
   + Run the following command to verify the existence of the `/sys/firmware/efi` directory\. This directory exists only if the instance boots using UEFI\. If this directory doesn't exist, the command returns `Legacy BIOS Boot Detected`\.

     ```
     [ec2-user ~]$ [ -d /sys/firmware/efi ] && echo "UEFI Boot Detected" || echo "Legacy BIOS Boot Detected"
     ```

     Expected output from an instance booted in UEFI boot mode

     ```
     UEFI Boot Detected
     ```

     Expected output from an instance booted in Legacy BIOS boot mode

     ```
     Legacy BIOS Boot Detected
     ```
   + Run the following command to verify that EFI appears in the `dmesg` output\.

     ```
     [ec2-user ~]$ dmesg | grep -i "EFI"
     ```

     Expected output from an instance booted in UEFI boot mode

     ```
     [    0.000000] efi: Getting EFI parameters from FDT:
     [    0.000000] efi: EFI v2.70 by EDK II
     ```
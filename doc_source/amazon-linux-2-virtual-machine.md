# Running Amazon Linux 2 as a Virtual Machine On\-Premises<a name="amazon-linux-2-virtual-machine"></a>

Use the Amazon Linux 2 virtual machine \(VM\) images for on\-premises development and testing\. These images are available for use on the following virtualization platforms:
+ VMWare
+ KVM
+ VirtualBox \(Oracle VM\)
+ Microsoft Hyper\-V

To use the Amazon Linux 2 virtual machine images with one of the supported virtualization platforms, do the following:
+ [Step 1: Prepare the `seed.iso` Boot Image](#amazon-linux-2-virtual-machine-prepare)
+ [Step 2: Download the Amazon Linux 2 VM Image](#amazon-linux-2-virtual-machine-download)
+ [Step 3: Boot and Connect to Your New VM](#amazon-linux-2-virtual-machine-boot)

## Step 1: Prepare the `seed.iso` Boot Image<a name="amazon-linux-2-virtual-machine-prepare"></a>

The `seed.iso` boot image includes the initial configuration information that is needed to boot your new VM, such as the network configuration, host name, and user data\.

**Note**  
The `seed.iso` boot image includes only the configuration information required to boot the VM\. It does not include the Amazon Linux 2 operating system files\.

To generate the `seed.iso` boot image, you need two configuration files:
+ `meta-data`—This file includes the hostname and static network settings for the VM\.
+ `user-data`—This file configures user accounts, and specifies their passwords, key pairs, and access mechanisms\. By default, the Amazon Linux 2 VM image creates a `ec2-user` user account\. You use the `user-data` configuration file to set the password for the default user account\.

**To create the `seed.iso` boot disc**

1. Create a new folder named `seedconfig` and navigate into it\.

1. Create the `meta-data` configuration file\.

   1. Create a new file named `meta-data`\.

   1. Open the `meta-data` file using your preferred editor and add the following\.

      ```
      local-hostname: vm_hostname
      # eth0 is the default network interface enabled in the image. You can configure static network settings with an entry like the following.
      network-interfaces: |
        auto eth0
        iface eth0 inet static
        address 192.168.1.10
        network 192.168.1.0
        netmask 255.255.255.0
        broadcast 192.168.1.255
        gateway 192.168.1.254
      ```

      Replace *vm\_hostname* with a VM host name of your choice, and configure the network settings as required\.

   1. Save and close the `meta-data` configuration file\.

   For an example `meta-data` configuration file that specifies a VM hostname \(`amazonlinux.onprem`\), configures the default network interface \(`eth0`\), and specifies static IP addresses for the necessary network devices, see the [ sample Seed\.iso file](https://cdn.amazonlinux.com/os-images/2.0.20190612/)\.

1. Create the `user-data` configuration file\.

   1. Create a new file named `user-data`\.

   1. Open the `user-data` file using your preferred editor and add the following\.

      ```
      #cloud-config
      #vim:syntax=yaml
      users:
      # A user by the name `ec2-user` is created in the image by default.
        - default
      chpasswd:
        list: |
          ec2-user:plain_text_password
      # In the above line, do not add any spaces after 'ec2-user:'.
      ```

      Replace *plain\_text\_password* with a password of your choice for the default `ec2-user` user account\.

   1. \(Optional\) By default, cloud\-init applies network settings each time the VM boots\. Add the following to prevent cloud\-init from applying network settings at each boot, and to retain the network settings applied during the first boot\.

      ```
      # NOTE: Cloud-init applies network settings on every boot by default. To retain network settings from first
      boot, add following ‘write_files’ section:
      write_files:
        - path: /etc/cloud/cloud.cfg.d/80_disable_network_after_firstboot.cfg
          content: |
            # Disable network configuration after first boot
            network:
              config: disabled
      ```

   1. Save and close the `user-data` configuration file\.

   You can also create additional user accounts and specify their access mechanisms, passwords, and key pairs\. For more information about the supported directives, see [ Modules](http://cloudinit.readthedocs.io/en/latest/topics/modules.html)\. For an example `user-data` file that creates three additional users and specifies a custom password for the default `ec2-user` user account, see the [ sample Seed\.iso file](https://cdn.amazonlinux.com/os-images/2.0.20190612/)\.

1. Create the `seed.iso` boot image using the `meta-data` and `user-data` configuration files\.

   For Linux, use a tool such as **genisoimage**\. Navigate into the `seedconfig` folder, and execute the following command\.

   ```
   $ genisoimage -output seed.iso -volid cidata -joliet -rock user-data meta-data
   ```

   For macOS, use a tool such as **hdiutil**\. Navigate one level up from the `seedconfig` folder, and execute the following command\.

   ```
   $ hdiutil makehybrid -o seed.iso -hfs -joliet -iso -default-volume-name cidata seedconfig/
   ```

## Step 2: Download the Amazon Linux 2 VM Image<a name="amazon-linux-2-virtual-machine-download"></a>

We offer a different Amazon Linux 2 VM image for each of the supported virtualization platforms\. Download the correct VM image for your chosen platform:
+ [ VMWare](https://cdn.amazonlinux.com/os-images/latest/vmware/)
+ [ KVM](https://cdn.amazonlinux.com/os-images/latest/kvm/)
+ [ Oracle VirtualBox](https://cdn.amazonlinux.com/os-images/latest/virtualbox/)
+ [ Microsoft Hyper\-V](https://cdn.amazonlinux.com/os-images/latest/hyperv/)

## Step 3: Boot and Connect to Your New VM<a name="amazon-linux-2-virtual-machine-boot"></a>

To boot and connect to your new VM, you must have the `seed.iso` boot image \(created in Step 1\) and an Amazon Linux 2 VM image \(downloaded in Step 2\)\. The steps vary depending on your chosen VM platform\.

You must connect the `seed.iso` boot image to the VM on first boot\. `seed.iso` is evaluated only during the initial boot\.

After the VM has booted, log in using one of the user accounts that is defined in the `user-data` configuration file\. For virtualization platforms other than VMWare, you can disconnect the `seed.iso` boot image from the VM after you have logged in for the first time\.
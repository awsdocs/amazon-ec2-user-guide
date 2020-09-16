# Running Amazon Linux 2 as a virtual machine on premises<a name="amazon-linux-2-virtual-machine"></a>

Use the Amazon Linux 2 virtual machine \(VM\) images for on\-premises development and testing\. These images are available for use on the following virtualization platforms:
+ VMWare
+ KVM
+ VirtualBox \(Oracle VM\)
+ Microsoft Hyper\-V

To use the Amazon Linux 2 virtual machine images with one of the supported virtualization platforms, do the following:
+ [Step 1: Prepare the `seed.iso` boot image](#amazon-linux-2-virtual-machine-prepare)
+ [Step 2: Download the Amazon Linux 2 VM image](#amazon-linux-2-virtual-machine-download)
+ [Step 3: Boot and connect to your new VM](#amazon-linux-2-virtual-machine-boot)

## Step 1: Prepare the `seed.iso` boot image<a name="amazon-linux-2-virtual-machine-prepare"></a>

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

## Step 2: Download the Amazon Linux 2 VM image<a name="amazon-linux-2-virtual-machine-download"></a>

We offer a different Amazon Linux 2 VM image for each of the supported virtualization platforms\. Download the correct VM image for your chosen platform:
+ [ VMWare](https://cdn.amazonlinux.com/os-images/latest/vmware/)
+ [ KVM](https://cdn.amazonlinux.com/os-images/latest/kvm/)
+ [ Oracle VirtualBox](https://cdn.amazonlinux.com/os-images/latest/virtualbox/)
+ [ Microsoft Hyper\-V](https://cdn.amazonlinux.com/os-images/latest/hyperv/)

## Step 3: Boot and connect to your new VM<a name="amazon-linux-2-virtual-machine-boot"></a>

To boot and connect to your new VM, you must have the `seed.iso` boot image \(created in Step 1\) and an Amazon Linux 2 VM image \(downloaded in Step 2\)\. The steps vary depending on your chosen VM platform\.

------
#### [ VMWare vSphere ]

The VM image for VMware is made available in the OVF format\.

**To boot the VM using VMWare vSphere**

1. Create a new datastore for the `seed.iso` file, or add it to an existing datastore\.

1. Deploy the OVF template, but do not start the VM yet\.

1. In the **Navigator** panel, right\-click the new virtual machine and choose **Edit Settings**\.

1. On the **Virtual Hardware** tab, for **New device**, choose **CD/DVD Drive**, and then choose **Add**\.

1. For **New CD/DVD Drive**, choose **Datastore ISO File**\. Select the datastore to which you added the `seed.iso` file, browse to and select the `seed.iso` file, and then choose **OK**\.

1. For **New CD/DVD Drive**, select **Connect**, and then choose **OK**\.

After you have associated the datastore with the VM, you should be able to boot it\.

------
#### [ KVM ]

**To boot the VM using KVM**

1. Open the **Create new VM** wizard\.

1. For Step 1, choose **Import existing disk image**\.

1. For Step 2, browse to and select the VM image\. For **OS type** and **Version**, choose **Linux** and **Red Hat Enterprise Linux 7\.0** respectively\.

1. For Step 3, specify the amount of RAM and the number of CPUs to use\.

1. For Step 4, enter a name for the new VM and select **Customize configuration before install**, and choose **Finish**\.

1. In the Configuration window for the VM, choose **Add Hardware**\.

1. In the **Add New Virtual Hardware** window, choose **Storage**\.

1. In the Storage configuration, choose **Select or create custom storage**\. For **Device type**, choose **CDROM device**\. Choose **Manage**, **Browse Local**, and then navigate to and select the `seed.iso` file\. Choose **Finish**\.

1. Choose **Begin Installation**\.

------
#### [ Oracle VirtualBox ]

**To boot the VM using Oracle VirtualBox**

1. Open the **Create Virtual Machine** wizard\.

1. When prompted to select a **Type** and a **Version**, select **Linux** and **RHEL \(64\-bit\)** respectively\.

1. When prompted to configure the hard disk, choose **Use an existing virtual hard disk file**, select the VM image, and then choose **Create**\.

1. Before you start the VM, you must load the `seed.iso` file in the virtual machine's virtual optical drive:

   1. Select the new VM, choose **Settings**, and then choose **Storage**\.

   1. In the **Storage Devices** list, under **Controller: IDE**, choose the *empty* optical drive\.

   1. In the **Attributes** section for the optical drive, choose the browse button, select **Choose Virtual Optical Disk File**, and then select the `seed.iso` file\.

After you have added the `seed.iso` file to the virtual optical drive, you should be able to boot the VM\.

------
#### [ Microsoft Hyper\-V ]

The VM image for Microsoft Hyper\-V is compressed into a zip file\. You must extract the contents of the zip file\.

**To boot the VM using Microsoft Hyper\-V**

1. Open the new **Virtual Machine Wizard**\.

1. When prompted to select a generation, select **Generation 1**\.

1. When prompted to configure the network adapter, for **Connection** choose **External**\.

1. When prompted to connect a virtual hard disk, choose **Use an existing virtual hard disk**, choose **Browse**, and then navigate to and select the VM image\. Choose **Finish** to create the VM\.

1. Right\-click the new VM and choose **Settings**\. In the **Settings** window, under **IDE Controller 1**, choose **DVD Drive**\.

1. For the DVD drive, choose **Image file** and then browse to and select the `seed.iso` file\.

1. Apply the changes and start the VM\.

------

After the VM has booted, log in using one of the user accounts that is defined in the `user-data` configuration file\. For virtualization platforms other than VMWare, you can disconnect the `seed.iso` boot image from the VM after you have logged in for the first time\.
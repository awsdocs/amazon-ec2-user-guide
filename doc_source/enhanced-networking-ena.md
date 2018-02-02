# Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Linux Instances in a VPC<a name="enhanced-networking-ena"></a>

To prepare for enhanced networking with the ENA network adapter, set up your instance as follows:

+ Launch the instance from an HVM AMI using Linux kernel version of 3\.2 or later\. The latest Amazon Linux HVM AMIs have the modules required for enhanced networking installed and have the required attributes set\. Therefore, if you launch an Amazon EBS–backed, enhanced networking–supported instance using a current Amazon Linux HVM AMI, ENA enhanced networking is already enabled for your instance\. 

+ Launch the instance in a VPC\. \(You can't enable enhanced networking if the instance is in EC2\-Classic\.\)

+ Install and configure the [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](http://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\. Enhanced networking cannot be managed from the Amazon EC2 console\. 

+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `enaSupport` attribute, may render incompatible instances or operating systems unreachable; if you have a recent backup, your data will still be retained if this happens\.


+ [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)
+ [Enabling Enhanced Networking with ENA on Amazon Linux](#enable-enhanced-networking-ena-AL)
+ [Enabling Enhanced Networking with ENA on Ubuntu](#enhanced-networking-ena-ubuntu)
+ [Enabling Enhanced Networking with ENA on Other Linux Distributions](#enhanced-networking-ena-linux)
+ [Troubleshooting](#enhanced-networking-ena-troubleshooting)

## Testing Whether Enhanced Networking with ENA Is Enabled<a name="test-enhanced-networking-ena"></a>

To test whether enhanced networking with ENA is already enabled, verify that the `ena` module is installed on your instance and that the `enaSupport` attribute is set\. If your instance satisfies these two conditions, then the ethtool \-i eth*n* command should show that the module is in use on the network interface\.

**Kernel Module \(ena\)**  
To verify that the `ena` module is installed, use the modinfo command as follows:

```
[ec2-user ~]$ modinfo ena
filename:       /lib/modules/4.4.11-23.53.amzn1.x86_64/kernel/drivers/amazon/net/ena/ena.ko
version:        0.6.6
license:        GPL
description:    Elastic Network Adapter (ENA)
author:         Amazon.com, Inc. or its affiliates
srcversion:     3141E47566402C79D6B8284
alias:          pci:v00001D0Fd0000EC21sv*sd*bc*sc*i*
alias:          pci:v00001D0Fd0000EC20sv*sd*bc*sc*i*
alias:          pci:v00001D0Fd00001EC2sv*sd*bc*sc*i*
alias:          pci:v00001D0Fd00000EC2sv*sd*bc*sc*i*
depends:
intree:         Y
vermagic:       4.4.11-23.53.amzn1.x86_64 SMP mod_unload modversions
parm:           debug:Debug level (0=none,...,16=all) (int)
parm:           push_mode:Descriptor / header push mode (0=automatic,1=disable,3=enable)
			  0 - Automatically choose according to device capability (default)
			  1 - Don't push anything to device memory
			  3 - Push descriptors and header buffer to device memory (int)
parm:           enable_wd:Enable keepalive watchdog (0=disable,1=enable,default=1) (int)
parm:           enable_missing_tx_detection:Enable missing Tx completions. (default=1) (int)
parm:           numa_node_override_array:Numa node override map
 (array of int)
parm:           numa_node_override:Enable/Disable numa node override (0=disable)
 (int)
```

In the above Amazon Linux case, the `ena` module is installed\.

```
ubuntu:~$ modinfo ena
ERROR: modinfo: could not find module ena
```

In the above Ubuntu instance, the module is not installed, so you must first install it\. For more information, see [Enabling Enhanced Networking with ENA on Ubuntu](#enhanced-networking-ena-ubuntu)\.

**Instance Attribute \(enaSupport\)**

To check whether an instance has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.

+ [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

  ```
  aws ec2 describe-instances --instance-ids instance_id --query 'Reservations[].Instances[].EnaSupport'
  ```

+ [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Instance -InstanceId instance-id).Instances.EnaSupport
  ```

**Image Attribute \(enaSupport\)**  
To check whether an AMI has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.

+ [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)

  ```
  aws ec2 describe-images --image-id ami_id --query 'Images[].EnaSupport'
  ```

+ [Get\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Image -ImageId ami_id).EnaSupport
  ```

**Network Interface Driver**  
Use the following command to verify that the `ena` module is being used on a particular interface, substituting the interface name that you wish to check\. If you are using a single interface \(default\), it will be `eth0`\.

```
[ec2-user ~]$ ethtool -i eth0
driver: vif
version:
firmware-version:
bus-info: vif-0
supports-statistics: yes
supports-test: no
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: no
```

In the above case, the `ena` module is not loaded, because the listed driver is `vif`\.

```
[ec2-user ~]$ ethtool -i eth0
driver: ena
version: 0.6.6
firmware-version:
bus-info: 0000:00:03.0
supports-statistics: yes
supports-test: no
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: no
```

In this case, the `ena` module is loaded and at the minimum recommended version\. This instance has enhanced networking properly configured\.

## Enabling Enhanced Networking with ENA on Amazon Linux<a name="enable-enhanced-networking-ena-AL"></a>

The latest Amazon Linux HVM AMIs have the module required for enhanced networking with ENA installed and have the required `enaSupport` attribute set\. Therefore, if you launch an instance with the latest Amazon Linux HVM AMI on a supported instance type, enhanced networking with ENA is already enabled for your instance\. For more information, see [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)\.

If you launched your instance using an older Amazon Linux AMI and it does not have enhanced networking enabled already, use the following procedure to enable enhanced networking\.

**To enable enhanced networking with ENA \(EBS\-backed instances\)**

1. Connect to your instance\.

1. From the instance, run the following command to update your instance with the newest kernel and kernel modules, including `ena`:

   ```
   [ec2-user ~]$ sudo yum update
   ```

1. From your local computer, reboot your instance using the Amazon EC2 console or one of the following commands: [reboot\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/reboot-instances.html) \(AWS CLI\), [Restart\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Restart-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\.

1. Connect to your instance again and verify that the `ena` module is installed and at the minimum recommended version using the modinfo ena command from [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)\.

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.
**Important**  
If you are using an instance store\-backed instance, you can't stop the instance\. Instead, proceed to [To enable enhanced networking with ENA \(instance store\-backed instances\)](#enhanced-networking-ena-instance-store-AL)\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:

   + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
     ```

   + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance-id -EnaSupport $true
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\. The AMI inherits the enhanced networking `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking with ENA enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. Connect to your instance and verify that the `ena` module is installed and loaded on your network interface using the ethtool \-i eth*n* command from [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)\.

   If you are unable to connect to your instance after enabling enhanced networking with ENA, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.

**To enable enhanced networking with ENA \(instance store\-backed instances\)**  
If your instance is an instance store–backed instance, follow [[ERROR] BAD/MISSING LINK TEXT](#amazon-linux-enhanced-networking-ena-start-step-AL) through [[ERROR] BAD/MISSING LINK TEXT](#amazon-linux-enhanced-networking-ena-stop-step-AL) in the previous procedure, and then create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-instance-store.html)\. Be sure to enable the enhanced networking `enaSupport` attribute when you register the AMI\.

+ [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --ena-support ...
  ```

+ [Register\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -EnaSupport $true ...
  ```

## Enabling Enhanced Networking with ENA on Ubuntu<a name="enhanced-networking-ena-ubuntu"></a>

The latest Ubuntu HVM AMIs have the module required for enhanced networking with ENA installed and have the required `enaSupport` attribute set\. Therefore, if you launch an instance with the latest Ubuntu HVM AMI on a supported instance type, enhanced networking with ENA is already enabled for your instance\. For more information, see [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)\.

If you launched your instance using an older AMI and it does not have enhanced networking enabled already, the following are the general steps to enable enhanced networking with ENA on an Ubuntu instance\.

**To enable enhanced networking with ENA on Ubuntu \(EBS\-backed instances\)**

1. Connect to your instance\.

1. Update the package cache and packages\.

   ```
   ubuntu:~$ sudo apt-get update && sudo apt-get upgrade -y
   ```
**Important**  
If during the update process you are prompted to install `grub`, use `/dev/xvda` to install `grub` onto, and then choose to keep the current version of `/boot/grub/menu.lst`\.

1. Install the `build-essential` packages to compile the kernel module and the `dkms` package so that your `ena` module is rebuilt every time your kernel is updated\.

   ```
   ubuntu:~$ sudo apt-get install -y build-essential dkms
   ```

1. Clone the source code for the `ena` module on your instance from GitHub at [https://github.com/amzn/amzn-drivers](https://github.com/amzn/amzn-drivers)\.

   ```
   ubuntu:~$ git clone https://github.com/amzn/amzn-drivers
   ```

1. Move the `amzn-drivers` package to the `/usr/src/` directory so dkms can find it and build it for each kernel update\. Append the version number \(you can find the current version number in the release notes\) of the source code to the directory name\. For example, version `1.0.0` is shown in the example below\.

   ```
   ubuntu:~$ sudo mv amzn-drivers /usr/src/amzn-drivers-1.0.0
   ```

1. Create the dkms configuration file with the following values, substituting your version of `ena`\.

   1. Create the file\.

      ```
      ubuntu:~$ sudo touch /usr/src/amzn-drivers-1.0.0/dkms.conf
      ```

   1. Edit the file and add the following values\.

      ```
      ubuntu:~$ sudo vim /usr/src/amzn-drivers-1.0.0/dkms.conf
      PACKAGE_NAME="ena"
      PACKAGE_VERSION="1.0.0"
      CLEAN="make -C kernel/linux/ena clean"
      MAKE="make -C kernel/linux/ena/ BUILD_KERNEL=${kernelver}"
      BUILT_MODULE_NAME[0]="ena"
      BUILT_MODULE_LOCATION="kernel/linux/ena"
      DEST_MODULE_LOCATION[0]="/updates"
      DEST_MODULE_NAME[0]="ena"
      AUTOINSTALL="yes"
      ```

1. Add, build, and install the `ena` module on your instance using dkms\.

   1. Add the module to dkms\.

      ```
      ubuntu:~$ sudo dkms add -m amzn-drivers -v 1.0.0
      ```

   1. Build the module using dkms\.

      ```
      ubuntu:~$ sudo dkms build -m amzn-drivers -v 1.0.0
      ```

   1. Install the module using dkms\.

      ```
      ubuntu:~$ sudo dkms install -m amzn-drivers -v 1.0.0
      ```

1. Rebuild `initramfs` so the correct module is loaded at boot time\.

   ```
   ubuntu:~$ sudo update-initramfs -c -k all
   ```

1. Verify that the `ena` module is installed using the modinfo ena command from [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena)\.

   ```
   ubuntu:~$ modinfo ena
   filename:       /lib/modules/3.13.0-74-generic/updates/dkms/ena.ko
   version:        1.0.0
   license:        GPL
   description:    Elastic Network Adapter (ENA)
   author:         Amazon.com, Inc. or its affiliates
   srcversion:     9693C876C54CA64AE48F0CA
   alias:          pci:v00001D0Fd0000EC21sv*sd*bc*sc*i*
   alias:          pci:v00001D0Fd0000EC20sv*sd*bc*sc*i*
   alias:          pci:v00001D0Fd00001EC2sv*sd*bc*sc*i*
   alias:          pci:v00001D0Fd00000EC2sv*sd*bc*sc*i*
   depends:
   vermagic:       3.13.0-74-generic SMP mod_unload modversions
   parm:           debug:Debug level (0=none,...,16=all) (int)
   parm:           push_mode:Descriptor / header push mode (0=automatic,1=disable,3=enable)
   			  0 - Automatically choose according to device capability (default)
   			  1 - Don't push anything to device memory
   			  3 - Push descriptors and header buffer to device memory (int)
   parm:           enable_wd:Enable keepalive watchdog (0=disable,1=enable,default=1) (int)
   parm:           enable_missing_tx_detection:Enable missing Tx completions. (default=1) (int)
   parm:           numa_node_override_array:Numa node override map
    (array of int)
   parm:           numa_node_override:Enable/Disable numa node override (0=disable)
    (int)
   ```

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.
**Important**  
If you are using an instance store\-backed instance, you can't stop the instance\. Instead, proceed to [To enable enhanced networking with ENA on Ubuntu \(instance store\-backed instances\)](#ubuntu-enhanced-networking-ena-instance-store)\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:

   + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
     ```

   + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance-id -EnaSupport $true
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) \. The AMI inherits the enhanced networking attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. \(Optional\) Connect to your instance and verify that the module is installed\.

   If you are unable to connect to your instance after enabling enhanced networking with ENA, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.

**To enable enhanced networking with ENA on Ubuntu \(instance store\-backed instances\)**  
If your instance is an instance store\-backed instance, follow [[ERROR] BAD/MISSING LINK TEXT](#ubuntu-enhanced-networking-ena-start-step) through [[ERROR] BAD/MISSING LINK TEXT](#ubuntu-enhanced-networking-ena-stop-step) in the previous procedure, and then create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\. Be sure to enable the enhanced networking `enaSupport` attribute when you register the AMI\.

+ [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --ena-support ...
  ```

+ [Register\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -EnaSupport $true ...
  ```

## Enabling Enhanced Networking with ENA on Other Linux Distributions<a name="enhanced-networking-ena-linux"></a>

The following procedure provides the general steps for enabling enhanced networking with ENA on a Linux distribution other than Amazon Linux or Ubuntu\. Before you begin, see [Testing Whether Enhanced Networking with ENA Is Enabled](#test-enhanced-networking-ena) to check if your instance is already enabled for enhanced networking\. For more information, such as detailed syntax for commands, file locations, or package and tool support, see the specific documentation for your Linux distribution\.

**To enable enhanced networking with ENA on Linux \(EBS\-backed instances\)**

1. Connect to your instance\.

1. Clone the source code for the `ena` module on your instance from GitHub at [https://github.com/amzn/amzn-drivers](https://github.com/amzn/amzn-drivers)\.

   ```
   ubuntu:~$ git clone https://github.com/amzn/amzn-drivers
   ```

1. Compile and install the `ena` module on your instance\. 

   If your distribution supports dkms, then you should consider configuring dkms to recompile the `ena` module whenever your system's kernel is updated\. If your distribution does not support dkms natively, you can find it in the EPEL repository \([https://fedoraproject\.org/wiki/EPEL](https://fedoraproject.org/wiki/EPEL)\) for Red Hat Enterprise Linux variants, or you can download the software at [http://linux\.dell\.com/dkms/](http://linux.dell.com/dkms/)\. Use [[ERROR] BAD/MISSING LINK TEXT](#enhanced-networking-ena-dkms-start-step) through [[ERROR] BAD/MISSING LINK TEXT](#enhanced-networking-ena-dkms-stop-step) in [To enable enhanced networking with ENA on Ubuntu \(EBS\-backed instances\)](#ubuntu-enhanced-networking-ena-procedure) for help configuring dkms\.

1. Run the sudo depmod command to update module dependencies\.

1. Update `initramfs` on your instance to ensure that the new module loads at boot time\.

1. Determine if your system uses predictable network interface names by default\. Systems that use systemd or udev versions 197 or greater can rename Ethernet devices and they do not guarantee that a single network interface will be named `eth0`\. This behavior can cause problems connecting to your instance\. For more information and to see other configuration options, see [Predictable Network Interface Names](http://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/) on the freedesktop\.org website\.

   1. You can check the systemd or udev versions on RPM\-based systems with the following command:

      ```
      [ec2-user ~]$ rpm -qa | grep -e '^systemd-[0-9]\+\|^udev-[0-9]\+'
      systemd-208-11.el7_0.2.x86_64
      ```

      In the above Red Hat Enterprise Linux 7 example, the systemd version is 208, so predictable network interface names must be disabled\.

   1. Disable predictable network interface names by adding the `net.ifnames=0` option to the `GRUB_CMDLINE_LINUX` line in `/etc/default/grub`\.

      ```
      [ec2-user ~]$ sudo sed -i '/^GRUB\_CMDLINE\_LINUX/s/\"$/\ net\.ifnames\=0\"/' /etc/default/grub
      ```

   1. Rebuild the grub configuration file\.

      ```
      [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
      ```

1. From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.
**Important**  
If you are using an instance store\-backed instance, you can't stop the instance\. Instead, proceed to [To enabled enhanced networking with ENA \(instance store–backed instances\)](#other-linux-enhanced-networking-ena-instance-store)

1. From your local computer, enable the enhanced networking `enaSupport` attribute using one of the following commands:

   + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
     ```

   + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance-id -EnaSupport $true
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) \. The AMI inherits the enhanced networking `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.
**Important**  
If your instance operating system contains an `/etc/udev/rules.d/70-persistent-net.rules` file, you must delete it before creating the AMI\. This file contains the MAC address for the Ethernet adapter of the original instance\. If another instance boots with this file, the operating system will be unable to find the device and `eth0` may fail, causing boot issues\. This file is regenerated at the next boot cycle, and any instances launched from the AMI create their own version of the file\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. \(Optional\) Connect to your instance and verify that the module is installed\.

   If you are unable to connect to your instance after enabling enhanced networking with ENA, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.

**To enabled enhanced networking with ENA \(instance store–backed instances\)**  
If your instance is an instance store–backed instance, follow the [[ERROR] BAD/MISSING LINK TEXT](#other-linux-enhanced-networking-ena-start-step) through the [[ERROR] BAD/MISSING LINK TEXT](#other-linux-enhanced-networking-ena-stop-step) in the previous procedure, and then create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\. Be sure to enable the enhanced networking `enaSupport` attribute when you register the AMI\.

+ [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --ena-support ...
  ```

+ [Register\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -EnaSupport ...
  ```

## Troubleshooting<a name="enhanced-networking-ena-troubleshooting"></a>

For additional information about troubleshooting your ENA adapter, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.
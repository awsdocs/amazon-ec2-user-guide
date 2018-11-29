# Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Linux Instances<a name="enhanced-networking-ena"></a>

Amazon EC2 provides enhanced networking capabilities through the Elastic Network Adapter \(ENA\)\.

**Topics**
+ [Requirements](#ena-requirements)
+ [Testing Whether Enhanced Networking Is Enabled](#test-enhanced-networking-ena)
+ [Enabling Enhanced Networking on the Amazon Linux AMI](#enable-enhanced-networking-ena-AL)
+ [Enabling Enhanced Networking on Ubuntu](#enhanced-networking-ena-ubuntu)
+ [Enabling Enhanced Networking on Linux](#enhanced-networking-ena-linux)
+ [Troubleshooting](#enhanced-networking-ena-troubleshooting)

## Requirements<a name="ena-requirements"></a>

To prepare for enhanced networking using the ENA, set up your instance as follows:
+ Select from the following supported instance types: A1, C5, C5d, C5n, F1, G3, H1, I3, `m4.16xlarge`, M5, M5a, M5d, P2, P3, R4, R5, R5a, R5d, T3, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, X1, X1e, and z1d\.
+ Launch the instance using a supported version of the Linux kernel and a supported distribution, so that ENA enhanced networking is enabled for your instance automatically\. For more information, see [ENA Linux Kernel Driver Release Notes](https://github.com/amzn/amzn-drivers/blob/ena_linux_1.6.0/kernel/linux/ena/RELEASENOTES.md)\.
+ Ensure that the instance has internet connectivity\.
+ Install and configure the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](https://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\. Enhanced networking cannot be managed from the Amazon EC2 console\.
+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `enaSupport` attribute, might render incompatible instances or operating systems unreachable; if you have a recent backup, your data will still be retained if this happens\.

## Testing Whether Enhanced Networking Is Enabled<a name="test-enhanced-networking-ena"></a>

To test whether enhanced networking is already enabled, verify that the `ena` module is installed on your instance and that the `enaSupport` attribute is set\. If your instance satisfies these two conditions, then the ethtool \-i eth*n* command should show that the module is in use on the network interface\.

**Kernel Module \(ena\)**  
To verify that the `ena` module is installed, use the modinfo command as follows:

```
[ec2-user ~]$ modinfo ena
filename:       /lib/modules/4.14.33-59.37.amzn2.x86_64/kernel/drivers/amazon/net/ena/ena.ko
version:        1.5.0g
license:        GPL
description:    Elastic Network Adapter (ENA)
author:         Amazon.com, Inc. or its affiliates
srcversion:     692C7C68B8A9001CB3F31D0
alias:          pci:v00001D0Fd0000EC21sv*sd*bc*sc*i*
alias:          pci:v00001D0Fd0000EC20sv*sd*bc*sc*i*
alias:          pci:v00001D0Fd00001EC2sv*sd*bc*sc*i*
alias:          pci:v00001D0Fd00000EC2sv*sd*bc*sc*i*
depends:
retpoline:      Y
intree:         Y
name:           ena
...
```

In the above Amazon Linux case, the `ena` module is installed\.

```
ubuntu:~$ modinfo ena
ERROR: modinfo: could not find module ena
```

In the above Ubuntu instance, the module is not installed, so you must first install it\. For more information, see [Enabling Enhanced Networking on Ubuntu](#enhanced-networking-ena-ubuntu)\.

**Instance Attribute \(enaSupport\)**

To check whether an instance has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

  ```
  aws ec2 describe-instances --instance-ids instance_id --query "Reservations[].Instances[].EnaSupport"
  ```
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(Tools for Windows PowerShell\)

  ```
  (Get-EC2Instance -InstanceId instance-id).Instances.EnaSupport
  ```

**Image Attribute \(enaSupport\)**  
To check whether an AMI has the enhanced networking `enaSupport` attribute set, use one of the following commands\. If the attribute is set, the response is true\.
+ [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)

  ```
  aws ec2 describe-images --image-id ami_id --query "Images[].EnaSupport"
  ```
+ [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(Tools for Windows PowerShell\)

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
version: 1.5.0g
firmware-version:
expansion-rom-version:
bus-info: 0000:00:05.0
supports-statistics: yes
supports-test: no
supports-eeprom-access: no
supports-register-dump: no
supports-priv-flags: no
```

In this case, the `ena` module is loaded and at the minimum recommended version\. This instance has enhanced networking properly configured\.

## Enabling Enhanced Networking on the Amazon Linux AMI<a name="enable-enhanced-networking-ena-AL"></a>

Amazon Linux 2 and the latest versions of the Amazon Linux AMI have the module required for enhanced networking installed and have the required `enaSupport` attribute set\. Therefore, if you launch an instance with an HVM version of Amazon Linux on a supported instance type, enhanced networking is already enabled for your instance\. For more information, see [Testing Whether Enhanced Networking Is Enabled](#test-enhanced-networking-ena)\.

If you launched your instance using an older Amazon Linux AMI and it does not have enhanced networking enabled already, use the following procedure to enable enhanced networking\.

**To enable enhanced networking on Amazon Linux AMI**

1. <a name="amazon-linux-enhanced-networking-ena-start-step-AL"></a>Connect to your instance\.

1. From the instance, run the following command to update your instance with the newest kernel and kernel modules, including `ena`:

   ```
   [ec2-user ~]$ sudo yum update
   ```

1. From your local computer, reboot your instance using the Amazon EC2 console or one of the following commands: [reboot\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/reboot-instances.html) \(AWS CLI\), [Restart\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Restart-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\.

1. <a name="amazon-linux-enhanced-networking-ena-stop-step-AL"></a>Connect to your instance again and verify that the `ena` module is installed and at the minimum recommended version using the modinfo ena command from [Testing Whether Enhanced Networking Is Enabled](#test-enhanced-networking-ena)\.

1. \[EBS\-backed instance\] From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

   \[Instance store\-backed instance\] You can't stop the instance to modify the attribute\. Instead, proceed to this procedure: [To enable enhanced networking on Amazon Linux AMI \(instance store\-backed instances\)](#enhanced-networking-ena-instance-store-AL)\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:
   + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
     ```
   + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance-id -EnaSupport $true
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\. The AMI inherits the enhanced networking `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. Connect to your instance and verify that the `ena` module is installed and loaded on your network interface using the ethtool \-i eth*n* command from [Testing Whether Enhanced Networking Is Enabled](#test-enhanced-networking-ena)\.

   If you are unable to connect to your instance after enabling enhanced networking, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.<a name="enhanced-networking-ena-instance-store-AL"></a>

**To enable enhanced networking on Amazon Linux AMI \(instance store\-backed instances\)**

Follow the previous procedure until the step where you stop the instance\. Create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md), making sure to enable the enhanced networking attribute when you register the AMI\.
+ [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --ena-support ...
  ```
+ [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -EnaSupport $true ...
  ```

## Enabling Enhanced Networking on Ubuntu<a name="enhanced-networking-ena-ubuntu"></a>

The latest Ubuntu HVM AMIs have the module required for enhanced networking with ENA installed and have the required `enaSupport` attribute set\. Therefore, if you launch an instance with the latest Ubuntu HVM AMI on a supported instance type, enhanced networking is already enabled for your instance\. For more information, see [Testing Whether Enhanced Networking Is Enabled](#test-enhanced-networking-ena)\.

If you launched your instance using an older AMI and it does not have enhanced networking enabled already, you can install the `linux-aws` kernel package to get the latest enhanced networking drivers and update the required attribute\.<a name="ubuntu-enhanced-networking-ena-procedure"></a>

**To install the linux\-aws kernel package**

1. <a name="ubuntu-enhanced-networking-ena-start-step"></a>Connect to your instance\.

1. Update the package cache and packages\.

   ```
   ubuntu:~$ sudo apt-get update && sudo apt-get upgrade -y linux-aws
   ```
**Important**  
If during the update process you are prompted to install `grub`, use `/dev/xvda` to install `grub` onto, and then choose to keep the current version of `/boot/grub/menu.lst`\.

1. \[EBS\-backed instance\] From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

   \[Instance store\-backed instance\] You can't stop the instance to modify the attribute\. Instead, proceed to this procedure: [To enable enhanced networking on Ubuntu \(instance store\-backed instances\)](#enhanced-networking-ena-instance-store-ubuntu)\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:
   + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
     ```
   + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance-id -EnaSupport $true
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\. The AMI inherits the enhanced networking `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.<a name="enhanced-networking-ena-instance-store-ubuntu"></a>

**To enable enhanced networking on Ubuntu \(instance store\-backed instances\)**

Follow the previous procedure until the step where you stop the instance\. Create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md), making sure to enable the enhanced networking attribute when you register the AMI\.
+ [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --ena-support ...
  ```
+ [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -EnaSupport $true ...
  ```

## Enabling Enhanced Networking on Linux<a name="enhanced-networking-ena-linux"></a>

The following procedure provides the general steps for enabling enhanced networking on a Linux distribution other than Amazon Linux AMI or Ubuntu, such as SUSE Linux Enterprise Server, Red Hat Enterprise Linux, or CentOS\. Before you begin, see [Testing Whether Enhanced Networking Is Enabled](#test-enhanced-networking-ena) to check if your instance is already enabled for enhanced networking\. For more information, such as detailed syntax for commands, file locations, or package and tool support, see the specific documentation for your Linux distribution\.

**To enable enhanced networking on Linux**

1. <a name="other-linux-enhanced-networking-ena-start-step"></a>Connect to your instance\.

1. Clone the source code for the `ena` module on your instance from GitHub at [https://github.com/amzn/amzn-drivers](https://github.com/amzn/amzn-drivers)\.

   ```
   git clone https://github.com/amzn/amzn-drivers
   ```

1. Compile and install the `ena` module on your instance\.

1. Run the sudo depmod command to update module dependencies\.

1. <a name="other-linux-enhanced-networking-ena-stop-step"></a>Update `initramfs` on your instance to ensure that the new module loads at boot time\. For example, if your distribution supports dracut, you can use the following command:

   ```
   dracut -f -v
   ```

1. Determine if your system uses predictable network interface names by default\. Systems that use systemd or udev versions 197 or greater can rename Ethernet devices and they do not guarantee that a single network interface will be named `eth0`\. This behavior can cause problems connecting to your instance\. For more information and to see other configuration options, see [Predictable Network Interface Names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/) on the freedesktop\.org website\.

   1. You can check the systemd or udev versions on RPM\-based systems with the following command:

      ```
      rpm -qa | grep -e '^systemd-[0-9]\+\|^udev-[0-9]\+'
      systemd-208-11.el7_0.2.x86_64
      ```

      In the above Red Hat Enterprise Linux 7 example, the systemd version is 208, so predictable network interface names must be disabled\.

   1. Disable predictable network interface names by adding the `net.ifnames=0` option to the `GRUB_CMDLINE_LINUX` line in `/etc/default/grub`\.

      ```
      sudo sed -i '/^GRUB\_CMDLINE\_LINUX/s/\"$/\ net\.ifnames\=0\"/' /etc/default/grub
      ```

   1. Rebuild the grub configuration file\.

      ```
      sudo grub2-mkconfig -o /boot/grub2/grub.cfg
      ```

1. \[EBS\-backed instance\] From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

   \[Instance store\-backed instance\] You can't stop the instance to modify the attribute\. Instead, proceed to this procedure: [To enable enhanced networking on Linux \(instance store–backed instances\)](#other-linux-enhanced-networking-ena-instance-store)\.

1. From your local computer, enable the enhanced networking `enaSupport` attribute using one of the following commands:
   + [modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --ena-support
     ```
   + [Edit\-EC2InstanceAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance-id -EnaSupport $true
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) \. The AMI inherits the enhanced networking `enaSupport` attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.
**Important**  
If your instance operating system contains an `/etc/udev/rules.d/70-persistent-net.rules` file, you must delete it before creating the AMI\. This file contains the MAC address for the Ethernet adapter of the original instance\. If another instance boots with this file, the operating system will be unable to find the device and `eth0` might fail, causing boot issues\. This file is regenerated at the next boot cycle, and any instances launched from the AMI create their own version of the file\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. \(Optional\) Connect to your instance and verify that the module is installed\.

   If you are unable to connect to your instance after enabling enhanced networking, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.<a name="other-linux-enhanced-networking-ena-instance-store"></a>

**To enable enhanced networking on Linux \(instance store–backed instances\)**

Follow the previous procedure until the step where you stop the instance\. Create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md), making sure to enable the enhanced networking attribute when you register the AMI\.
+ [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --ena-support ...
  ```
+ [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -EnaSupport ...
  ```

## Troubleshooting<a name="enhanced-networking-ena-troubleshooting"></a>

For additional information about troubleshooting your ENA adapter, see [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)\.
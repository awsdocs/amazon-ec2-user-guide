# Enabling Enhanced Networking with the Intel 82599 VF Interface on Linux Instances<a name="sriov-networking"></a>

Amazon EC2 provides enhanced networking capabilities to C3, C4, D2, I2, M4 \(excluding `m4.16xlarge`\), and R3 instances through the Intel 82599 VF interface, which uses the Intel `ixgbevf` driver\.

**Topics**
+ [Requirements](#ixgbevf-requirements)
+ [Testing Whether Enhanced Networking is Enabled](#test-enhanced-networking)
+ [Enabling Enhanced Networking on Amazon Linux](#enable-enhanced-networking)
+ [Enabling Enhanced Networking on Ubuntu](#enhanced-networking-ubuntu)
+ [Enabling Enhanced Networking on Other Linux Distributions](#enhanced-networking-linux)
+ [Troubleshooting Connectivity Issues](#enhanced-networking-troubleshooting)

## Requirements<a name="ixgbevf-requirements"></a>

To prepare for enhanced networking using the Intel 82599 VF interface, set up your instance as follows:
+ Launch the instance from an HVM AMI using Linux kernel version of 2\.6\.32 or later\. The latest Amazon Linux HVM AMIs have the modules required for enhanced networking installed and have the required attributes set\. Therefore, if you launch an Amazon EBS–backed, enhanced networking–supported instance using a current Amazon Linux HVM AMI, enhanced networking is already enabled for your instance\.
**Warning**  
Enhanced networking is supported only for HVM instances\. Enabling enhanced networking with a PV instance can make it unreachable\. Setting this attribute without the proper module or module version can also make your instance unreachable\.
+ Launch the instance in a VPC\. \(You can't enable enhanced networking if the instance is in EC2\-Classic\.\)
+ Install and configure the [AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) or the [AWS Tools for Windows PowerShell](http://docs.aws.amazon.com/powershell/latest/userguide/) on any computer you choose, preferably your local desktop or laptop\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\. Enhanced networking cannot be managed from the Amazon EC2 console\.
+ If you have important data on the instance that you want to preserve, you should back that data up now by creating an AMI from your instance\. Updating kernels and kernel modules, as well as enabling the `sriovNetSupport` attribute, might render incompatible instances or operating systems unreachable; if you have a recent backup, your data will still be retained if this happens\.

## Testing Whether Enhanced Networking is Enabled<a name="test-enhanced-networking"></a>

Enhanced networking with the Intel 82599 VF interface is enabled if the `ixgbevf` module is installed on your instance and the `sriovNetSupport` attribute is set\. 

**Instance Attribute \(sriovNetSupport\)**  
To check whether an instance has the enhanced networking `sriovNetSupport` attribute set, use one of the following commands:
+ [describe\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-attribute.html) \(AWS CLI\)

  ```
  aws ec2 describe-instance-attribute --instance-id instance_id --attribute sriovNetSupport
  ```
+ [Get\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

  ```
  Get-EC2InstanceAttribute -InstanceId instance-id -Attribute sriovNetSupport
  ```

If the attribute isn't set, `SriovNetSupport` is empty; otherwise, it is set as follows:

```
"SriovNetSupport": {
    "Value": "simple"
},
```

**Image Attribute \(sriovNetSupport\)**  
To check whether an AMI already has the enhanced networking `sriovNetSupport` attribute set, use one of the following commands:
+ [describe\-image\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-image-attribute.html) \(AWS CLI\)

  ```
  aws ec2 describe-image-attribute --image-id ami_id --attribute sriovNetSupport
  ```

  Note that this command only works for images that you own\. You receive an `AuthFailure` error for images that do not belong to your account\.
+ [Get\-EC2ImageAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ImageAttribute.html) \(AWS Tools for Windows PowerShell\)

  ```
  Get-EC2ImageAttribute -ImageId ami-id -Attribute sriovNetSupport
  ```

If the attribute isn't set, `SriovNetSupport` is empty; otherwise, it is set as follows:

```
"SriovNetSupport": {
    "Value": "simple"
},
```

**Network Interface Driver**  
Use the following command to verify that the module is being used on a particular interface, substituting the interface name that you wish to check\. If you are using a single interface \(default\), it will be `eth0`\.

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

In the above case, the `ixgbevf` module is not loaded, because the listed driver is `vif`\.

```
[ec2-user ~]$ ethtool -i eth0
driver: ixgbevf
version: 4.0.3
firmware-version: N/A
bus-info: 0000:00:03.0
supports-statistics: yes
supports-test: yes
supports-eeprom-access: no
supports-register-dump: yes
supports-priv-flags: no
```

In this case, the `ixgbevf` module is loaded\. This instance has enhanced networking properly configured\.

## Enabling Enhanced Networking on Amazon Linux<a name="enable-enhanced-networking"></a>

The latest Amazon Linux HVM AMIs have the `ixgbevf` module required for enhanced networking installed and have the required `sriovNetSupport` attribute set\. Therefore, if you launch a instance type using a current Amazon Linux HVM AMI, enhanced networking is already enabled for your instance\. For more information, see [Testing Whether Enhanced Networking is Enabled](#test-enhanced-networking)\.

If you launched your instance using an older Amazon Linux AMI and it does not have enhanced networking enabled already, use the following procedure to enable enhanced networking\.

**Warning**  
There is no way to disable the enhanced networking attribute after you've enabled it\.

**To enable enhanced networking**

1. <a name="amazon-linux-enhanced-networking-start-step"></a>Connect to your instance\.

1. From the instance, run the following command to update your instance with the newest kernel and kernel modules, including `ixgbevf`:

   ```
   [ec2-user ~]$ sudo yum update
   ```

1. From your local computer, reboot your instance using the Amazon EC2 console or one of the following commands: [reboot\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/reboot-instances.html) \(AWS CLI\), [Restart\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Restart-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\.

1. <a name="amazon-linux-enhanced-networking-stop-step"></a>Connect to your instance again and verify that the `ixgbevf` module is installed and at the minimum recommended version using the modinfo ixgbevf command from [Testing Whether Enhanced Networking is Enabled](#test-enhanced-networking)\.

1. \[EBS\-backed instance\] From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

   \[Instance store\-backed instance\] You can't stop the instance to modify the attribute\. Instead, proceed to this procedure: [To enable enhanced networking \(instance store\-backed instances\)](#enhanced-networking-instance-store)\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:
   + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --sriov-net-support simple
     ```
   + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance_id -SriovNetSupport "simple"
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) \. The AMI inherits the enhanced networking attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. Connect to your instance and verify that the `ixgbevf` module is installed and loaded on your network interface using the ethtool \-i eth*n* command from [Testing Whether Enhanced Networking is Enabled](#test-enhanced-networking)\.<a name="enhanced-networking-instance-store"></a>

**To enable enhanced networking \(instance store\-backed instances\)**

Follow the previous procedure until the step where you stop the instance\. Create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md), making sure to enable the enhanced networking attribute when you register the AMI\.
+ [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --sriov-net-support simple ...
  ```
+ [Register\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -SriovNetSupport "simple" ...
  ```

## Enabling Enhanced Networking on Ubuntu<a name="enhanced-networking-ubuntu"></a>

Before you begin, [check if enhanced networking is already enabled](#test-enhanced-networking) on your instance\.

The Quick Start Ubuntu HVM AMIs include the necessary drivers for enhanced networking\. If you have a version of `ixgbevf` earlier than 2\.16\.4, you can install the `linux-aws` kernel package to get the latest enhanced networking drivers\.

The following procedure provides the general steps for compiling the `ixgbevf` module on an Ubuntu instance\.<a name="ubuntu-enhanced-networking-procedure"></a>

**To install the linux\-aws kernel package**

1. <a name="ubuntu-enhanced-networking-start-step"></a>Connect to your instance\.

1. Update the package cache and packages\.

   ```
   ubuntu:~$ sudo apt-get update && sudo apt-get upgrade -y linux-aws
   ```
**Important**  
If during the update process, you are prompted to install `grub`, use `/dev/xvda` to install `grub` onto, and then choose to keep the current version of `/boot/grub/menu.lst`\.

## Enabling Enhanced Networking on Other Linux Distributions<a name="enhanced-networking-linux"></a>

Before you begin, [check if enhanced networking is already enabled](#test-enhanced-networking) on your instance\. The latest Quick Start HVM AMIs include the necessary drivers for enhanced networking, therefore you do not need to perform additional steps\. 

The following procedure provides the general steps if you need to enable enhanced networking with the Intel 82599 VF interface on a Linux distribution other than Amazon Linux or Ubuntu\. For more information, such as detailed syntax for commands, file locations, or package and tool support, see the specific documentation for your Linux distribution\.

**To enable enhanced networking on Linux**

1. <a name="other-linux-enhanced-networking-start-step"></a>Connect to your instance\.

1. Download the source for the `ixgbevf` module on your instance from Sourceforge at [https://sourceforge\.net/projects/e1000/files/ixgbevf%20stable/](https://sourceforge.net/projects/e1000/files/ixgbevf%20stable/)\.

   Versions of `ixgbevf` earlier than 2\.16\.4, including version 2\.14\.2, do not build properly on some Linux distributions, including certain versions of Ubuntu\.

1. Compile and install the `ixgbevf` module on your instance\.
**Warning**  
If you compile the `ixgbevf` module for your current kernel and then upgrade your kernel without rebuilding the driver for the new kernel, your system might revert to the distribution\-specific `ixgbevf` module at the next reboot, which could make your system unreachable if the distribution\-specific version is incompatible with enhanced networking\.

1. Run the sudo depmod command to update module dependencies\.

1. <a name="other-linux-enhanced-networking-stop-step"></a>Update `initramfs` on your instance to ensure that the new module loads at boot time\.

1. Determine if your system uses predictable network interface names by default\. Systems that use systemd or udev versions 197 or greater can rename Ethernet devices and they do not guarantee that a single network interface will be named `eth0`\. This behavior can cause problems connecting to your instance\. For more information and to see other configuration options, see [Predictable Network Interface Names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/) on the freedesktop\.org website\.

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

1. \[EBS\-backed instance\] From your local computer, stop the instance using the Amazon EC2 console or one of the following commands: [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\), [Stop\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Stop-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should stop the instance in the AWS OpsWorks console so that the instance state remains in sync\.

   \[Instance store\-backed instance\] You can't stop the instance to modify the attribute\. Instead, proceed to this procedure: [To enable enhanced networking \(instance store–backed instances\)](#other-linux-enhanced-networking-instance-store)\.

1. From your local computer, enable the enhanced networking attribute using one of the following commands:
   + [modify\-instance\-attribute](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) \(AWS CLI\)

     ```
     aws ec2 modify-instance-attribute --instance-id instance_id --sriov-net-support simple
     ```
   + [Edit\-EC2InstanceAttribute](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstanceAttribute.html) \(AWS Tools for Windows PowerShell\)

     ```
     Edit-EC2InstanceAttribute -InstanceId instance_id -SriovNetSupport "simple"
     ```

1. \(Optional\) Create an AMI from the instance, as described in [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) \. The AMI inherits the enhanced networking attribute from the instance\. Therefore, you can use this AMI to launch another instance with enhanced networking enabled by default\.
**Important**  
If your instance operating system contains an `/etc/udev/rules.d/70-persistent-net.rules` file, you must delete it before creating the AMI\. This file contains the MAC address for the Ethernet adapter of the original instance\. If another instance boots with this file, the operating system will be unable to find the device and `eth0` might fail, causing boot issues\. This file is regenerated at the next boot cycle, and any instances launched from the AMI create their own version of the file\.

1. From your local computer, start the instance using the Amazon EC2 console or one of the following commands: [start\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html) \(AWS CLI\), [Start\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Start-EC2Instance.html) \(AWS Tools for Windows PowerShell\)\. If your instance is managed by AWS OpsWorks, you should start the instance in the AWS OpsWorks console so that the instance state remains in sync\.

1. \(Optional\) Connect to your instance and verify that the module is installed\.<a name="other-linux-enhanced-networking-instance-store"></a>

**To enable enhanced networking \(instance store–backed instances\)**

Follow the previous procedure until the step where you stop the instance\. Create a new AMI as described in [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md), making sure to enable the enhanced networking attribute when you register the AMI\.
+ [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)

  ```
  aws ec2 register-image --sriov-net-support simple ...
  ```
+ [Register\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

  ```
  Register-EC2Image -SriovNetSupport "simple" ...
  ```

## Troubleshooting Connectivity Issues<a name="enhanced-networking-troubleshooting"></a>

If you lose connectivity while enabling enhanced networking, the `ixgbevf` module might be incompatible with the kernel\. Try installing the version of the `ixgbevf` module included with the distribution of Linux for your instance\.

If you enable enhanced networking for a PV instance or AMI, this can make your instance unreachable\.
# Install AMD drivers on Linux instances<a name="install-amd-driver"></a>

An instance with an attached AMD GPU, such as a G4ad instance, must have the appropriate AMD driver installed\. Depending on your requirements, you can either use an AMI with the driver preinstalled or download a driver from Amazon S3\.

To install NVIDIA drivers on an instance with an attached NVIDIA GPU, such as a G4dn instance, see [Installing NVIDIA drivers on Linux instances](install-nvidia-driver.md) instead\.

**Contents**
+ [AMD Radeon Pro Software for Enterprise Driver](#amd-radeon-pro-software-for-enterprise-driver)
+ [AMIs with the AMD driver installed](#preinstalled-amd-driver)
+ [AMD driver download](#download-amd-driver)
+ [Set up an interactive desktop](#amd-interactive-desktop)

## AMD Radeon Pro Software for Enterprise Driver<a name="amd-radeon-pro-software-for-enterprise-driver"></a>

The AMD Radeon Pro Software for Enterprise Driver is built to deliver support for professional\-grade graphics use cases\. Using the driver, you can configure your instances with two 4K displays per GPU\.

**Supported APIs**
+ OpenGL, OpenCL
+ Vulkan
+ AMD Advanced Media Framework
+ Video Acceleration API

## AMIs with the AMD driver installed<a name="preinstalled-amd-driver"></a>

AWS offers different Amazon Machine Images \(AMI\) that come with the AMD drivers installed\. Open [Marketplace offerings with the AMD driver](http://aws.amazon.com/marketplace/search/results?page=1&filters=VendorId&VendorId=e6a5002c-6dd0-4d1e-8196-0a1d1857229b&searchTerms=AMD+Radeon+Pro+Driver)\.

## AMD driver download<a name="download-amd-driver"></a>

If you aren't using an AMI with the AMD driver installed, you can download the AMD driver and install it on your instance\.

These downloads are available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the AMD Radeon Pro V520 hardware\. Upon installation of the software, you are bound by the terms of the [AMD Software End User License Agreement](https://www.amd.com/en/support/eula)\.

**Prerequisites**
+ Install the AWS CLI on your Linux instance and configure default credentials\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\.

**To install the AMD driver on your Linux instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.
   + For Amazon Linux 2:

     ```
     $ sudo amazon-linux-extras install epel -y
     $ sudo yum update -y
     ```
   + For Ubuntu:

     ```
     $ sudo dpkg --add-architecture i386
     $ sudo apt-get update -y && sudo apt upgrade -y
     ```
   + For CentOS:

     ```
     $ sudo yum install epel-release -y
     $ sudo yum update -y
     ```

1. \(Ubuntu\) Ensure that kernel 5\.4\.0\-1029\-aws is the active kernel\. If there is a newer kernel installed, remove it using the following commands:

   ```
   $ sudo apt remove linux-image-5.4.0-1030-aws
   $ sudo update-grub
   ```

1. Reboot the instance\.

   ```
   $ sudo reboot
   ```

1. Reconnect to the instance after it reboots\.

1. \(Ubuntu\) Install `linux-modules-extra` for kernel 5\.4\.0\-1029\-aws\.

   ```
   $ sudo apt-get install -y linux-modules-extra-$(uname -r) autoconf
   ```

1. Download the latest AMD driver\.

   ```
   $ aws s3 cp --recursive s3://ec2-amd-linux-drivers/latest/ .
   ```

1. Extract the file\.
   + For Amazon Linux 2 and CentOS:

     ```
     $ tar -xf amdgpu-pro-*rhel*.tar.xz
     ```
   + For Ubuntu:

     ```
     $ tar -xf amdgpu-pro-*ubuntu*.tar-1.xz
     ```

1. Change to the folder for the extracted driver\.

1. Add the GPG keys for the driver installation\.
   + For Amazon Linux 2 and CentOS:

     ```
     $ sudo rpm --import RPM-GPG-KEY-amdgpu
     ```
   + For Ubuntu:

     ```
     $ cat RPM-GPG-KEY-amdgpu | sudo apt-key add-
     ```

1. Run the self install script to install the full graphics stack\.

   ```
   $ ./amdgpu-pro-install -y --opencl=pal,legacy
   ```
   + For Ubuntu:

     ```
     $ sudo apt install linux-modules-extra-`uname -r`
     ```

1. Reboot the instance\.

   ```
   $ sudo reboot
   ```

1. Confirm that the driver is functional\.

   ```
   $ dmesg | grep amdgpu
   ```

   The response should look like the following:

   ```
   Initialized amdgpu
   ```

## Set up an interactive desktop<a name="amd-interactive-desktop"></a>

After you confirm that your instance has the AMD GPU driver installed and amdgpu is in use, you can install an interactive desktop manager\. We recommend the MATE desktop environment for the best compatibility and performance\.

**Prerequisite**  
Open a text editor and save the following as a file named `xorg.conf`\. You'll need this file on your instance\.

```
Section "ServerLayout"
    Identifier     "Layout0"
    Screen          0 "Screen0"
    InputDevice     "Keyboard0" "CoreKeyboard"
    InputDevice     "Mouse0" "CorePointer"
EndSection
Section "Files"
    ModulePath "/opt/amdgpu/lib64/xorg/modules/drivers"
    ModulePath "/opt/amdgpu/lib/xorg/modules"
    ModulePath "/opt/amdgpu-pro/lib/xorg/modules/extensions"
    ModulePath "/opt/amdgpu-pro/lib64/xorg/modules/extensions"
    ModulePath "/usr/lib64/xorg/modules"
    ModulePath "/usr/lib/xorg/modules"
EndSection
Section "InputDevice"
    # generated from default
    Identifier     "Mouse0"
    Driver         "mouse"
    Option         "Protocol" "auto"
    Option         "Device" "/dev/psaux"
    Option         "Emulate3Buttons" "no"
    Option         "ZAxisMapping" "4 5"
EndSection
Section "InputDevice"
    # generated from default
    Identifier     "Keyboard0"
    Driver         "kbd"
EndSection
Section "Monitor"
    Identifier     "Monitor0"
    VendorName     "Unknown"
    ModelName      "Unknown"
EndSection
Section "Device"
    Identifier     "Device0"
    Driver         "amdgpu"
    VendorName     "AMD"
    BoardName      "Radeon MxGPU V520"
    BusID          "PCI:0:30:0"
EndSection
Section "Extensions"
    Option         "DPMS" "Disable"
EndSection
Section "Screen"
    Identifier     "Screen0"
    Device         "Device0"
    Monitor        "Monitor0"
    DefaultDepth   24
    Option         "AllowEmptyInitialConfiguration" "True"
    SubSection "Display"
        Virtual    3840 2160
        Depth      32
    EndSubSection
EndSection
```

**To set up an interactive desktop on Amazon Linux 2**

1. Install the EPEL repository\.

   ```
   $ sudo amazon-linux-extras install epel -y
   ```

1. Install the MATE desktop\.

   ```
   $ sudo amazon-linux-extras install mate-desktop1.x -y
   $ sudo yum groupinstall "MATE Desktop" -y
   $ sudo systemctl disable firewalld
   ```

1. Copy the `xorg.conf` file to `/etc/X11/xorg.conf`\.

1. Reboot the instance\.

   ```
   $ sudo reboot
   ```

1. \(Optional\) [Install the NICE DCV server](https://docs.aws.amazon.com/dcv/latest/adminguide/setting-up-installing.html) to use NICE DCV as a high\-performance display protocol, and then [connect to a NICE DCV session](https://docs.aws.amazon.com/dcv/latest/userguide/using-connecting.html) using your preferred client\.

**To set up an interactive desktop on Ubuntu**

1. Install the MATE desktop\.

   ```
   $ sudo apt install xorg-dev ubuntu-mate-desktop -y 
   $ sudo apt purge ifupdown -y
   ```

1. Copy the `xorg.conf` file to `/etc/X11/xorg.conf`\.

1. Reboot the instance\.

   ```
   $ sudo reboot
   ```

1. \(Optional\) [Install the NICE DCV server](https://docs.aws.amazon.com/dcv/latest/adminguide/setting-up-installing.html) to use NICE DCV as a high\-performance display protocol, and then [connect to a NICE DCV session](https://docs.aws.amazon.com/dcv/latest/userguide/using-connecting.html) using your preferred client\.

**To set up an interactive desktop on CentOS**

1. Install the EPEL repository\.

   ```
   $ sudo yum update -y
   $ sudo yum install epel-release -y
   ```

1. Install the MATE desktop\.

   ```
   $ sudo yum groupinstall "MATE Desktop" -y
   $ sudo systemctl disable firewalld
   ```

1. Copy the `xorg.conf` file to `/etc/X11/xorg.conf`\.

1. Reboot the instance\.

   ```
   $ sudo reboot
   ```

1. \(Optional\) [Install the NICE DCV server](https://docs.aws.amazon.com/dcv/latest/adminguide/setting-up-installing.html) to use NICE DCV as a high\-performance display protocol, and then [connect to a NICE DCV session](https://docs.aws.amazon.com/dcv/latest/userguide/using-connecting.html) using your preferred client\.

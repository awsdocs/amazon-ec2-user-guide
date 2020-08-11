# Installing NVIDIA drivers on Linux instances<a name="install-nvidia-driver"></a>

An instance with an attached GPU, such as a P3 or G4 instance, must have the appropriate NVIDIA driver installed\. Depending on the instance type, you can either download a public NVIDIA driver, download a driver from Amazon S3 that is available only to AWS customers, or use an AMI with the driver pre\-installed\.

**Contents**
+ [Types of NVIDIA drivers](#nvidia-driver-types)
+ [Available drivers by instance type](#nvidia-driver-instance-type)
+ [Installation options](#nvidia-installation-options)
  + [Option 1: AMIs with the NVIDIA drivers installed](#preinstalled-nvidia-driver)
  + [Option 2: Public NVIDIA drivers](#public-nvidia-driver)
  + [Option 3: GRID drivers \(G3 and G4 instances\)](#nvidia-GRID-driver)
  + [Option 4: NVIDIA gaming drivers \(G4 instances\)](#nvidia-gaming-driver)

## Types of NVIDIA drivers<a name="nvidia-driver-types"></a>

The following are the main types of NVIDIA drivers that can be used with GPU\-based instances\.

Tesla drivers  
These drivers are intended primarily for compute workloads, which use GPUs for computational tasks such as parallelized floating\-point calculations for machine learning and fast Fourier transforms for high performance computing applications\.

GRID drivers  
These drivers are certified to provide optimal performance for professional visualization applications that render content such as 3D models or high\-resolution videos\. You can configure GRID drivers to support two modes\. Quadro Virtual Workstations provide access to four 4K displays per GPU\. GRID vApps provide RDSH App hosting capabilities\.

Gaming drivers  
These drivers contain optimizations for gaming and are updated frequently to provide performance enhancements\. They support a single 4K display per GPU\.

**NVIDIA control panel**  
The NVIDIA control panel is supported with GRID and Gaming drivers\. It is not supported with Tesla drivers\.

**Supported APIs for Tesla, GRID, and gaming drivers**
+ OpenCL, OpenGL, and Vulkan
+ NVIDIA CUDA and related libraries \(for example, cuDNN, TensorRT, nvJPEG, and cuBLAS\)
+ NVENC for video encoding and NVDEC for video decoding

## Available drivers by instance type<a name="nvidia-driver-instance-type"></a>

The following table summarizes the supported NVIDIA drivers for each GPU instance type\.


| Instance type | Tesla driver | GRID driver | Gaming driver | 
| --- | --- | --- | --- | 
| G2 | No | Yes | No | 
| G3 | Yes | Yes | No | 
| G4 | Yes | Yes | Yes | 
| P2 | Yes | No | No | 
| P3 | Yes | Yes † | No | 

† Using Marketplace AMIs only

## Installation options<a name="nvidia-installation-options"></a>

Use one of the following options to get the NVIDIA drivers required for your GPU instance\.

**Topics**
+ [Option 1: AMIs with the NVIDIA drivers installed](#preinstalled-nvidia-driver)
+ [Option 2: Public NVIDIA drivers](#public-nvidia-driver)
+ [Option 3: GRID drivers \(G3 and G4 instances\)](#nvidia-GRID-driver)
+ [Option 4: NVIDIA gaming drivers \(G4 instances\)](#nvidia-gaming-driver)

### Option 1: AMIs with the NVIDIA drivers installed<a name="preinstalled-nvidia-driver"></a>

AWS and NVIDIA offer different Amazon Machine Images \(AMI\) that come with the NVIDIA drivers installed\.
+ [Marketplace offerings with the Tesla driver](http://aws.amazon.com/marketplace/search/results?page=1&filters=VendorId&VendorId=e6a5002c-6dd0-4d1e-8196-0a1d1857229b%2Cc568fe05-e33b-411c-b0ab-047218431da9&searchTerms=tesla+driver)
+ [Marketplace offerings with the GRID driver](http://aws.amazon.com/marketplace/search/results?&searchTerms=NVIDIA+quadro)
+ [Marketplace offerings with the Gaming driver](http://aws.amazon.com/marketplace/search/results?searchTerms=NVIDIA+gaming)

To update the driver version installed using one of these AMIs, you must uninstall the NVIDIA packages from your instance to avoid version conflicts\. Use this command to uninstall the NVIDIA packages:

```
[ec2-user ~]$ sudo yum erase nvidia cuda
```

The CUDA toolkit package has dependencies on the NVIDIA drivers\. Uninstalling the NVIDIA packages erases the CUDA toolkit\. You must reinstall the CUDA toolkit after installing the NVIDIA driver\.

### Option 2: Public NVIDIA drivers<a name="public-nvidia-driver"></a>

The options offered by AWS come with the necessary license for the driver\. Alternatively, you can install the public drivers and bring your own license\. To install a public driver, download it from the NVIDIA site as described here\.

Alternatively, you can use the options offered by AWS instead of the public drivers\. To use a GRID driver on a P3 instance, use the AWS Marketplace AMIs as described in [Option 1](#preinstalled-nvidia-driver)\. To use a GRID driver on a G3 or G4 instance, use the AWS Marketplace AMIs, as described in Option 1 or install the NVIDIA drivers provided by AWS as described in [Option 3](#nvidia-GRID-driver)\.

**To download a public NVIDIA driver**  
Log on to your Linux instance and download the 64\-bit NVIDIA driver appropriate for the instance type from [http://www\.nvidia\.com/Download/Find\.aspx](http://www.nvidia.com/Download/Find.aspx)\. For **Product Type**, **Product Series**, and **Product**, use the options in the following table\.


| Instance | Product Type | Product Series | Product | 
| --- | --- | --- | --- | 
| G2 | GRID | GRID Series | GRID K520 | 
| G3 | Tesla | M\-Class | M60 | 
| G4 † | Tesla | T\-Series | T4 | 
| P2 | Tesla | K\-Series | K80 | 
| P3 | Tesla | V\-Series | V100 | 

† G4 instances require driver version 418\.87 or later\.

**To install the NVIDIA driver on Linux**  
For more information about installing and configuring the driver, see the [NVIDIA Driver Installation Quickstart Guide](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)\. 

### Option 3: GRID drivers \(G3 and G4 instances\)<a name="nvidia-GRID-driver"></a>

These downloads are available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA Tesla T4 or NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](https://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**Prerequisites**
+ Install the AWS CLI on your Linux instance and configure default credentials\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\.

**To install the NVIDIA GRID driver on your Linux instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get necessary package updates for your instance\.
   + For Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     [ec2-user ~]$ sudo yum update -y
     ```
   + For Ubuntu and Debian:

     ```
     $ sudo apt-get update -y
     ```

1. \(Ubuntu 16\.04 and later, with the `linux-aws` package\) Upgrade the `linux-aws` package to receive the latest version\.

   ```
   $ sudo apt-get upgrade -y linux-aws
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.
   + For Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
     ```
   + For Ubuntu and Debian:

     ```
     $ sudo apt-get install -y gcc make linux-headers-$(uname -r)
     ```

1. \[CentOS, Red Hat Enterprise Linux, Ubuntu, Debian\] Disable the `nouveau` open source driver for NVIDIA graphics cards\.

   1. Add `nouveau` to the `/etc/modprobe.d/blacklist.conf` blacklist file\. Copy the following code block and paste it into a terminal\.

      ```
      [ec2-user ~]$ cat << EOF | sudo tee --append /etc/modprobe.d/blacklist.conf
      blacklist vga16fb
      blacklist nouveau
      blacklist rivafb
      blacklist nvidiafb
      blacklist rivatv
      EOF
      ```

   1. Edit the `/etc/default/grub` file and add the following line:

      ```
      GRUB_CMDLINE_LINUX="rdblacklist=nouveau"
      ```

   1. Rebuild the Grub configuration\.
      + For CentOS and Red Hat Enterprise Linux:

        ```
        [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
      + For Ubuntu and Debian:

        ```
        $ sudo update-grub
        ```

1. Download the GRID driver installation utility using the following command:

   ```
   [ec2-user ~]$ aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/latest/ .
   ```

   Multiple versions of the GRID driver are stored in this bucket\. You can see all of the available versions using the following command\.

   ```
   [ec2-user ~]$ aws s3 ls --recursive s3://ec2-linux-nvidia-drivers/
   ```

   Starting with GRID version 11\.0, you can use the driver packages under `latest` for both G3 and G4 instances\. We will not add versions later than 11\.0 to `g4/latest`, but will keep version 11\.0 and the earlier versions specific to G4 under `g4/latest`\.

1. Add permissions to run the driver installation utility using the following command\.

   ```
   [ec2-user ~]$ chmod +x NVIDIA-Linux-x86_64*.run
   ```

1. Run the self\-install script as follows to install the GRID driver that you downloaded\. For example:

   ```
   [ec2-user ~]$ sudo /bin/sh ./NVIDIA-Linux-x86_64*.run
   ```

   When prompted, accept the license agreement and specify the installation options as required \(you can accept the default options\)\.

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the driver is functional\. The response for the following command lists the installed version of the NVIDIA driver and details about the GPUs\.

   ```
   [ec2-user ~]$ nvidia-smi -q | head
   ```

1. \(Optional\) Depending on your use case, you might complete the following optional steps\. If you do not require this functionality, do not complete these steps\.

   1. To help take advantage of the four displays of up to 4K resolution, set up the high\-performance display protocol [NICE DCV](https://docs.aws.amazon.com/dcv)\.

   1. NVIDIA Quadro Virtual Workstation mode is enabled by default\. To activate GRID Virtual Applications for RDSH Application hosting capabilities, complete the GRID Virtual Application activation steps in [Activate NVIDIA GRID Virtual Applications](activate_grid.md)\.

### Option 4: NVIDIA gaming drivers \(G4 instances\)<a name="nvidia-gaming-driver"></a>

These drivers are available to AWS customers only\. By downloading them, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA Tesla T4 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](https://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**Prerequisites**
+ Install the AWS CLI on your Linux instance and configure default credentials\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\.

**To install the NVIDIA gaming driver on your Linux instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get necessary package updates for your instance\.
   + For Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     [ec2-user ~]$ sudo yum update -y
     ```
   + For Ubuntu and Debian:

     ```
     $ sudo apt-get update -y
     ```

1. \(Ubuntu 16\.04 and later, with the `linux-aws` package\) Upgrade the `linux-aws` package to receive the latest version\.

   ```
   $ sudo apt-get upgrade -y linux-aws
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.
   + For Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
     ```
   + For Ubuntu and Debian:

     ```
     $ sudo apt-get install -y gcc make linux-headers-$(uname -r)
     ```

1. \[CentOS, Red Hat Enterprise Linux, Ubuntu, Debian\] Disable the `nouveau` open source driver for NVIDIA graphics cards\.

   1. Add `nouveau` to the `/etc/modprobe.d/blacklist.conf` blacklist file\. Copy the following code block and paste it into a terminal\.

      ```
      [ec2-user ~]$ cat << EOF | sudo tee --append /etc/modprobe.d/blacklist.conf
      blacklist vga16fb
      blacklist nouveau
      blacklist rivafb
      blacklist nvidiafb
      blacklist rivatv
      EOF
      ```

   1. Edit the `/etc/default/grub` file and add the following line:

      ```
      GRUB_CMDLINE_LINUX="rdblacklist=nouveau"
      ```

   1. Rebuild the Grub configuration\.
      + For CentOS and Red Hat Enterprise Linux:

        ```
        [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
      + For Ubuntu and Debian:

        ```
        $ sudo update-grub
        ```

1. Download the gaming driver installation utility using the following command:

   ```
   [ec2-user ~]$ aws s3 cp --recursive s3://nvidia-gaming/linux/latest/ .
   ```

   Multiple versions of the gaming driver are stored in this bucket\. You can see all of the available versions using the following command:

   ```
   [ec2-user ~]$ aws s3 ls --recursive s3://nvidia-gaming/linux/
   ```

1. Add permissions to run the driver installation utility using the following command\.

   ```
   [ec2-user ~]$ chmod +x NVIDIA-Linux-x86_64*.run
   ```

1. Run the installer using the following command:

   ```
   [ec2-user ~]$ sudo ./NVIDIA-Linux-x86_64*.run
   ```

   When prompted, accept the license agreement and specify the installation options as required \(you can accept the default options\)\.

1. Use the following command to create the required configuration file\.

   ```
   [ec2-user ~]$ cat << EOF | sudo tee -a /etc/nvidia/gridd.conf
   vGamingMarketplace=2
   EOF
   ```

1. Use the following command to download and rename the certification file\.
   + For version 440\.68 or later:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCert-Linux_2020_04.cert"
     ```
   + For earlier versions:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCert-Linux_2019_09.cert"
     ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. \(Optional\) To help take advantage of a single display of up to 4K resolution, set up the high\-performance display protocol [NICE DCV](https://docs.aws.amazon.com/dcv)\. If you do not require this functionality, do not complete this step\.
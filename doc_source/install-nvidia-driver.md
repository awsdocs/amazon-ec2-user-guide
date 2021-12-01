# Install NVIDIA drivers on Linux instances<a name="install-nvidia-driver"></a>

An instance with an attached NVIDIA GPU, such as a P3 or G4dn instance, must have the appropriate NVIDIA driver installed\. Depending on the instance type, you can either download a public NVIDIA driver, download a driver from Amazon S3 that is available only to AWS customers, or use an AMI with the driver pre\-installed\.

To install AMD drivers on a Linux instance with an attached AMD GPU, such as a G4ad instance, see [Install AMD drivers](install-amd-driver.md) instead\.  To install NVIDIA drivers on a Windows instance, see [Install NVIDIA drivers on Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/install-nvidia-driver.html)\.

**Contents**
+ [Types of NVIDIA drivers](#nvidia-driver-types)
+ [Available drivers by instance type](#nvidia-driver-instance-type)
+ [Installation options](#nvidia-installation-options)
+ [Install an additional version of CUDA](#gpu-instance-install-cuda)

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
| G2 | Yes | No | No | 
| G3 | Yes | Yes | No | 
| G4dn | Yes | Yes | Yes | 
| G5 | Yes | Yes | Yes | 
| G5g | Yes | No | No | 
| P2 | Yes | No | No | 
| P3 | Yes | Yes † | No | 
| P4d | Yes | No | No | 

† Using Marketplace AMIs only

## Installation options<a name="nvidia-installation-options"></a>

Use one of the following options to get the NVIDIA drivers required for your GPU instance\.

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

Alternatively, you can use the options offered by AWS instead of the public drivers\. To use a GRID driver on a P3 instance, use the AWS Marketplace AMIs as described in [Option 1](#preinstalled-nvidia-driver)\. To use a GRID driver on a G5, G4dn, or G3 instance, use the AWS Marketplace AMIs, as described in Option 1 or install the NVIDIA drivers provided by AWS as described in [Option 3](#nvidia-GRID-driver)\.

**To download a public NVIDIA driver**  
Log on to your Linux instance and download the 64\-bit NVIDIA driver appropriate for the instance type from [http://www\.nvidia\.com/Download/Find\.aspx](http://www.nvidia.com/Download/Find.aspx)\. For **Product Type**, **Product Series**, and **Product**, use the options in the following table\.


| Instance | Product Type | Product Series | Product | 
| --- | --- | --- | --- | 
| G2 | GRID | GRID Series | GRID K520 | 
| G3 | Tesla | M\-Class | M60 | 
| G4dn | Tesla | T\-Series | T4 | 
| G5 † | Tesla | A\-Series | A10 | 
| G5g ‡ | Tesla | T\-Series | NVIDIA T4g | 
| P2 | Tesla | K\-Series | K80 | 
| P3 | Tesla | V\-Series | V100 | 
| P4d | Tesla | A\-Series | A100 | 

† G5 instances require driver version 470\.00 or later\.

‡ G5g instances require driver version 470\.82\.01 or later\. The operating systems is Linux aarch64\.

**To install the NVIDIA driver on Linux**  
For more information about installing and configuring the driver, see the [NVIDIA Driver Installation Quickstart Guide](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)\. 

### Option 3: GRID drivers \(G5, G4dn, and G3 instances\)<a name="nvidia-GRID-driver"></a>

These downloads are available to AWS customers only\. By downloading, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA A10G, NVIDIA Tesla T4, or NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](https://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**Prerequisites**
+ Install the AWS CLI on your Linux instance and configure default credentials\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\.
+ G5 instances require GRID 13\.1 or later \(or GRID 12\.4 or later\)\.

#### Amazon Linux<a name="nvidia-grid-amazon-linux"></a>

**To install the NVIDIA GRID driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
   ```

1. Download the GRID driver installation utility using the following command:

   ```
   [ec2-user ~]$ aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/latest/ .
   ```

   Multiple versions of the GRID driver are stored in this bucket\. You can see all of the available versions using the following command\.

   ```
   [ec2-user ~]$ aws s3 ls --recursive s3://ec2-linux-nvidia-drivers/
   ```

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

#### CentOS 7 and Red Hat Enterprise Linux 7<a name="nvidia-grid-centos7-rhel7"></a>

**To install the NVIDIA GRID driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
   ```

1. Disable the `nouveau` open source driver for NVIDIA graphics cards\.

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

      ```
      [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
      ```

1. Download the GRID driver installation utility using the following command:

   ```
   [ec2-user ~]$ aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/latest/ .
   ```

   Multiple versions of the GRID driver are stored in this bucket\. You can see all of the available versions using the following command\.

   ```
   [ec2-user ~]$ aws s3 ls --recursive s3://ec2-linux-nvidia-drivers/
   ```

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

   1. Install the GUI desktop/workstation package\.

      ```
      [ec2-user ~]$ sudo yum groupinstall -y "Server with GUI"
      ```

#### CentOS Stream 8 and Red Hat Enterprise Linux 8<a name="nvidia-grid-centos8-rhel8"></a>

**To install the NVIDIA GRID driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo dnf install -y make gcc elfutils-libelf-devel libglvnd-devel kernel-devel-$(uname -r)
   ```

1. Download the GRID driver installation utility using the following command:

   ```
   [ec2-user ~]$ aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/latest/ .
   ```

   Multiple versions of the GRID driver are stored in this bucket\. You can see all of the available versions using the following command\.

   ```
   [ec2-user ~]$ aws s3 ls --recursive s3://ec2-linux-nvidia-drivers/
   ```

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

   1. Install the GUI workstation package\.

      ```
      [ec2-user ~]$ sudo dnf groupinstall -y workstation
      ```

#### Rocky Linux 8<a name="nvidia-grid-rocky-linux-8"></a>

**To install the NVIDIA GRID driver on your Linux instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo dnf install -y make gcc elfutils-libelf-devel libglvnd-devel kernel-devel-$(uname -r)
   ```

1. Download the GRID driver installation utility using the following command:

   ```
   [ec2-user ~]$ aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/latest/ .
   ```

   Multiple versions of the GRID driver are stored in this bucket\. You can see all of the available versions using the following command\.

   ```
   [ec2-user ~]$ aws s3 ls --recursive s3://ec2-linux-nvidia-drivers/
   ```

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

#### Ubuntu and Debian<a name="nvidia-grid-ubuntu-debian"></a>

**To install the NVIDIA GRID driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   $ sudo apt-get update -y
   ```

1. Upgrade the `linux-aws` package to receive the latest version\.

   ```
   $ sudo apt-get upgrade -y linux-aws
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   $ sudo apt-get install -y gcc make linux-headers-$(uname -r)
   ```

1. Disable the `nouveau` open source driver for NVIDIA graphics cards\.

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

   1. Install the GUI desktop/workstation package\.

      ```
      [ec2-user ~]$ sudo apt-get install -y lightdm ubuntu-desktop
      ```

### Option 4: NVIDIA gaming drivers \(G5 and G4dn instances\)<a name="nvidia-gaming-driver"></a>

These drivers are available to AWS customers only\. By downloading them, you agree to use the downloaded software only to develop AMIs for use with the NVIDIA A10G and NVIDIA Tesla T4 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](https://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

**Prerequisites**
+ Install the AWS CLI on your Linux instance and configure default credentials\. For more information, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.
+ IAM users must have the permissions granted by the **AmazonS3ReadOnlyAccess** policy\.

#### Amazon Linux<a name="nvidia-gaming-amazon-linux"></a>

**To install the NVIDIA gaming driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
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
   + For version 460\.39 or later:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCertLinux_2021_10_2.cert"
     ```
   + For version 440\.68 to 445\.48:

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

1. \(Optional\) To help take advantage of a single display of up to 4K resolution, set up the high\-performance display protocol [NICE DCV](https://docs.aws.amazon.com/dcv)\.

#### CentOS 7 and Red Hat Enterprise Linux 7<a name="nvidia-gaming-centos7-rhel7"></a>

**To install the NVIDIA gaming driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
   ```

1. Disable the `nouveau` open source driver for NVIDIA graphics cards\.

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

      ```
      [ec2-user ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
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
   + For version 460\.39 or later:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCertLinux_2021_10_2.cert"
     ```
   + For version 440\.68 to 445\.48:

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

#### CentOS Stream 8 and Red Hat Enterprise Linux 8<a name="nvidia-gaming-centos8-rhel8"></a>

**To install the NVIDIA gaming driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo yum install -y gcc kernel-devel-$(uname -r)
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
   + For version 460\.39 or later:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCertLinux_2021_10_2.cert"
     ```
   + For version 440\.68 to 445\.48:

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

1. \(Optional\) To help take advantage of a single display of up to 4K resolution, set up the high\-performance display protocol [NICE DCV](https://docs.aws.amazon.com/dcv)\.

#### Rocky Linux 8<a name="nvidia-gaming-rocky-linux-8"></a>

**To install the NVIDIA gaming driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   [ec2-user ~]$ sudo dnf install -y gcc make elfutils-libelf-devel libglvnd-devel kernel-devel-$(uname -r)
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
   + For version 460\.39 or later:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCertLinux_2021_10_2.cert"
     ```
   + For version 440\.68 to 445\.48:

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

1. \(Optional\) To help take advantage of a single display of up to 4K resolution, set up the high\-performance display protocol [NICE DCV](https://docs.aws.amazon.com/dcv)\.

#### Ubuntu and Debian<a name="nvidia-gaming-ubuntu-debian"></a>

**To install the NVIDIA gaming driver on your instance**

1. Connect to your Linux instance\. Install gcc and make, if they are not already installed\.

1. Update your package cache and get the package updates for your instance\.

   ```
   $ sudo apt-get update -y
   ```

1. Upgrade the `linux-aws` package to receive the latest version\.

   ```
   $ sudo apt-get upgrade -y linux-aws
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.

   ```
   $ sudo apt-get install -y gcc make linux-headers-$(uname -r)
   ```

1. Disable the `nouveau` open source driver for NVIDIA graphics cards\.

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
   + For version 460\.39 or later:

     ```
     [ec2-user ~]$ sudo curl -o /etc/nvidia/GridSwCert.txt "https://nvidia-gaming.s3.amazonaws.com/GridSwCert-Archive/GridSwCertLinux_2021_10_2.cert"
     ```
   + For version 440\.68 to 445\.48:

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

## Install an additional version of CUDA<a name="gpu-instance-install-cuda"></a>

After you install an NVIDIA graphics driver on your instance, you can install a version of CUDA other than the version that is bundled with the graphics driver\. The following procedure demonstrates how to configure multiple versions of CUDA on the instance\.

**To install the CUDA toolkit**

1. Connect to your Linux instance\.

1. Open the [NVIDIA website](https://developer.nvidia.com/cuda-downloads?target_os=Linux) and select the version of CUDA that you need\.

1. Select the architecture, distribution, and version for the operating system on your instance\. For **Installer Type**, select **runfile \(local\)**\.

1. Follow the instructions to download the install script\.

1. Add run permissions to the install script that you downloaded using the following command\.

   ```
   [ec2-user ~]$ chmod +x downloaded_installer_file
   ```

1. Run the install script as follows to install the CUDA toolkit and add the CUDA version number to the toolkit path\.

   ```
   [ec2-user ~]$ sudo downloaded_installer_file --silent --override --toolkit --samples --toolkitpath=/usr/local/cuda-version --samplespath=/usr/local/cuda --no-opengl-lib
   ```

1. \(Optional\) Set the default CUDA version as follows\.

   ```
   [ec2-user ~]$ ln -s /usr/local/cuda-version /usr/local/cuda
   ```
# Installing the NVIDIA Driver on Linux Instances<a name="install-nvidia-driver"></a>

A GPU\-based accelerated computing instance must have the appropriate NVIDIA driver\. The NVIDIA driver that you install must be compiled against the kernel that you plan to run on your instance\.

Amazon provides AMIs with updated and compatible builds of the NVIDIA kernel drivers for each official kernel upgrade in the AWS Marketplace\. If you decide to use a different NVIDIA driver version than the one that Amazon provides, or decide to use a kernel that's not an official Amazon build, you must uninstall the Amazon\-provided NVIDIA packages from your system to avoid conflicts with the versions of the drivers that you are trying to install\.

Use this command to uninstall Amazon\-provided NVIDIA packages:

```
sudo yum erase nvidia cuda
```

The Amazon\-provided CUDA toolkit package has dependencies on the NVIDIA drivers\. Uninstalling the NVIDIA packages erases the CUDA toolkit\. You must reinstall the CUDA toolkit after installing the NVIDIA driver\.

## Downloading the NVIDIA GRID Driver \(G3\)<a name="obtain-nvidia-GRID-driver-linux"></a>

For G3 instances, you can download the NVIDIA GRID driver from Amazon S3 using the AWS CLI or SDKs\. To install the AWS CLI, see [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.

**Important**  
This download is available to AWS customers only\. By downloading, you agree that you will only use the downloaded software to develop AMIs for use with the NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

Use the following AWS CLI command to download the driver:

```
aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/ .
```

If you receive an `Unable to locate credentials` error, see [Configuring the AWS CLI](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) to configure the AWS CLI to use your AWS credentials\.

## Downloading a Public NVIDIA Driver \(G2, P2, P3\)<a name="obtain-nvidia-driver-linux"></a>

For instance types other than G3, or if you are not using NVIDIA GRID functionality on a G3 instance, you can download the public NVIDIA drivers\.

Download the 64\-bit NVIDIA driver appropriate for your instance type from [http://www\.nvidia\.com/Download/Find\.aspx](http://www.nvidia.com/Download/Find.aspx)\.


| Instances | Product Type | Product Series | Product | 
| --- | --- | --- | --- | 
| G2 | GRID | GRID Series | GRID K520 | 
| P2 | Tesla | K\-Series | K\-80 | 
| P3 | Tesla | V\-Series | V100 | 

For more information about installing and configuring the driver, choose the **ADDITIONAL INFORMATION** tab on the download page for the driver on the NVIDIA website and choose the README link\.

## Installing the NVIDIA Driver Manually<a name="Cluster_GPUs_Manual_Install_Driver"></a>

**To install the driver on a Linux instance**

1. Update your package cache and get necessary package updates for your instance\.
   + For Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     sudo yum update -y
     ```
   + For Ubuntu and Debian:

     ```
     sudo apt-get update -y
     ```

1. \(Ubuntu 16\.04 and later, with the `linux-aws` package\) Upgrade the `linux-aws` package to receive the latest version\.

   ```
   sudo apt-get upgrade -y linux-aws
   ```

1. Reboot your instance to load the latest kernel version\.

   ```
   sudo reboot
   ```

1. Reconnect to your instance after it has rebooted\.

1. Install the gcc compiler and the kernel headers package for the version of the kernel you are currently running\.
   + For Amazon Linux, CentOS, and Red Hat Enterprise Linux:

     ```
     sudo yum install -y gcc kernel-devel-$(uname -r)
     ```
   + For Ubuntu and Debian:

     ```
     sudo apt-get install -y gcc make linux-headers-$(uname -r)
     ```

1. \(Graphical desktop instances only\) Disable the `nouveau` open source driver for NVIDIA graphics cards\.

   1. Add `nouveau` to the `/etc/modprobe.d/blacklist.conf` blacklist file\. Copy the following code block and paste it into a terminal\.

      ```
      cat << EOF | sudo tee --append /etc/modprobe.d/blacklist.conf
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
      + CentOS and Red Hat Enterprise Linux:

        ```
        sudo grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
      + For Ubuntu and Debian:

        ```
        sudo update-grub
        ```

1. Download the driver package that you identified earlier\.
   + For P2 instances, the following command downloads the 367\.106 version of the NVIDIA driver\.

     ```
     wget [http://us.download.nvidia.com/XFree86/Linux-x86_64/367.106/NVIDIA-Linux-x86_64-367.106.run](http://us.download.nvidia.com/XFree86/Linux-x86_64/367.106/NVIDIA-Linux-x86_64-367.106.run)
     ```
   + For G3 instances, you can download the driver from Amazon S3 using the AWS CLI or SDKs\. To install the AWS CLI, see [Installing the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\. Use the following AWS CLI command to download the driver and the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX):
**Important**  
This download is available to AWS customers only\. By downloading, you agree that you will only use the downloaded software to develop AMIs for use with the NVIDIA Tesla M60 hardware\. Upon installation of the software, you are bound by the terms of the [NVIDIA GRID Cloud End User License Agreement](http://aws-nvidia-license-agreement.s3.amazonaws.com/NvidiaGridAWSUserLicenseAgreement.DOCX)\.

     ```
     aws s3 cp --recursive s3://ec2-linux-nvidia-drivers/ .
     ```

1. Run the self\-install script to install the NVIDIA driver that you downloaded in the previous step\. For example:

   ```
   sudo /bin/bash ./NVIDIA-Linux-x86_64-367.106.run
   ```

   When prompted, accept the license agreement and specify the installation options as required \(you can accept the default options\)\.

1. Reboot the instance\.

   ```
   sudo reboot
   ```

1. Confirm that the driver is functional\. The response for the following command lists the installed NVIDIA driver version and details about the GPUs\.
**Note**  
This command may take several minutes to run\.

   ```
   nvidia-smi -q | head
   ```

1. \[G3 instances only\] To enable NVIDIA GRID Virtual Workstation or NVIDIA GRID Virtual Applications on a G3 instance, complete the GRID activation steps in [Activate NVIDIA GRID Capabilities \(G3 Instances Only\)](activate_grid.md)\.

1. \[P2, P3, and G3 instances\] Complete the optimization steps in [Optimizing GPU Settings \(P2, P3, and G3 Instances\)](optimize_gpu.md) to achieve the best performance from your GPU\.
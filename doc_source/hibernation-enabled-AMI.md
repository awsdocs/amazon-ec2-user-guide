# Configure an existing AMI to support hibernation<a name="hibernation-enabled-AMI"></a>

The following AMIs support hibernation, but to hibernate an instance that was launched with one of these AMIs, additional configuration is required before you can hibernate the instance\.

**Topics**
+ [Amazon Linux 2 released before 2019\.08\.29](#configure-AL2-for-hibernation)
+ [Amazon Linux released before 2018\.11\.16](#configure-AL-for-hibernation)
+ [CentOS version 8 or later](#configure-centos-for-hibernation)
+ [Fedora version 34 or later](#configure-fedora-for-hibernation)
+ [Red Hat Enterprise Linux version 8 or later](#configure-RHEL-for-hibernation)
+ [Ubuntu 20\.04 LTS \- Focal released before serial number 20210820](#configure-ubuntu2004-for-hibernation)
+ [Ubuntu 18\.04 \- Bionic released before serial number 20190722\.1](#configure-ubuntu1804-for-hibernation)
+ [Ubuntu 16\.04 \- Xenial](#configure-ubuntu1604-for-hibernation)

For more information, see [Update instance software on your Amazon Linux instance](install-updates.md)\.

**No additional configuration is required for the following AMIs because they're already configured to support hibernation:**
+ Amazon Linux 2 AMI released 2019\.08\.29 or later
+ Amazon Linux AMI 2018\.03 released 2018\.11\.16 or later
+ Ubuntu 20\.04 LTS \- Focal AMI released with serial number 20210820 or later
+ Ubuntu 18\.04 LTS \- Bionic AMI released with serial number 20190722\.1 or later

## Amazon Linux 2 released before 2019\.08\.29<a name="configure-AL2-for-hibernation"></a>

**To configure an Amazon Linux 2 AMI released before 2019\.08\.29 to support hibernation**

1. Update the kernel to `4.14.138-114.102` or later\.

   ```
   [ec2-user ~]$ sudo yum update kernel
   ```

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo yum install ec2-hibinit-agent
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `4.14.138-114.102` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```

1. Stop the instance and create an AMI\. For more information, see [Create a Linux AMI from an instance](creating-an-ami-ebs.md#how-to-create-ebs-ami)\.

## Amazon Linux released before 2018\.11\.16<a name="configure-AL-for-hibernation"></a>

**To configure an Amazon Linux AMI released before 2018\.11\.16 to support hibernation**

1. Update the kernel to `4.14.77-70.59` or later\.

   ```
   [ec2-user ~]$ sudo yum update kernel
   ```

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo yum install ec2-hibinit-agent
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `4.14.77-70.59` or greater\.

   ```
   [ec2-user ~]$ uname -a
   ```

1. Stop the instance and create an AMI\. For more information, see [Create a Linux AMI from an instance](creating-an-ami-ebs.md#how-to-create-ebs-ami)\.

## CentOS version 8 or later<a name="configure-centos-for-hibernation"></a>

**To configure a CentOS version 8 or later AMI to support hibernation**

1. Update the kernel to `4.18.0-305.7.1.el8_4.x86_64` or later\.

   ```
   [ec2-user ~]$ sudo yum update kernel
   ```

1. Install the Fedora Extra Packages for Enterprise Linux \(EPEL\) repository\.

   ```
   [ec2-user ~]$ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   ```

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo yum install ec2-hibinit-agent
   ```

1. Enable the hibernate agent to start on boot\.

   ```
   [ec2-user ~]$ sudo systemctl enable hibinit-agent.service
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `4.18.0-305.7.1.el8_4.x86_64` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```

## Fedora version 34 or later<a name="configure-fedora-for-hibernation"></a>

**To configure a Fedora version 34 or later AMI to support hibernation**

1. Update the kernel to `5.12.10-300.fc34.x86_64` or later\.

   ```
   [ec2-user ~]$ sudo yum update kernel
   ```

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo dnf install ec2-hibinit-agent
   ```

1. Enable the hibernate agent to start on boot\.

   ```
   [ec2-user ~]$ sudo systemctl enable hibinit-agent.service
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `5.12.10-300.fc34.x86_64` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```

## Red Hat Enterprise Linux version 8 or later<a name="configure-RHEL-for-hibernation"></a>

**To configure a Red Hat Enterprise Linux 8 AMI to support hibernation**

1. Update the kernel to `4.18.0-305.7.1.el8_4.x86_64` or later\.

   ```
   [ec2-user ~]$ sudo yum update kernel
   ```

1. Install the Fedora Extra Packages for Enterprise Linux \(EPEL\) repository\.

   ```
   [ec2-user ~]$ sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   ```

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo yum install ec2-hibinit-agent
   ```

1. Enable the hibernate agent to start on boot\.

   ```
   [ec2-user ~]$ sudo systemctl enable hibinit-agent.service
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `4.18.0-305.7.1.el8_4.x86_64` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```

## Ubuntu 20\.04 LTS \- Focal released before serial number 20210820<a name="configure-ubuntu2004-for-hibernation"></a>

**To configure an Ubuntu 20\.04 LTS \- Focal AMI released before serial number 20210820 to support hibernation**

1. Update the linux\-aws\-kernel to `5.8.0-1038.40` or later, and grub2 to `2.04-1ubuntu26.13` or later\.

   ```
   [ec2-user ~]$ sudo apt update
   [ec2-user ~]$ sudo apt dist-upgrade
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `5.8.0-1038.40` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```

1. Confirm that the grub2 version is updated to `2.04-1ubuntu26.13` or later\.

   ```
   [ec2-user ~]$ dpkg –list | grep grub2-common
   ```

## Ubuntu 18\.04 \- Bionic released before serial number 20190722\.1<a name="configure-ubuntu1804-for-hibernation"></a>

**To configure an Ubuntu 18\.04 LTS AMI released before serial number 20190722\.1 to support hibernation**

1. Update the kernel to `4.15.0-1044` or later\.

   ```
   [ec2-user ~]$ sudo apt update
   [ec2-user ~]$ sudo apt dist-upgrade
   ```

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo apt install ec2-hibinit-agent
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `4.15.0-1044` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```

## Ubuntu 16\.04 \- Xenial<a name="configure-ubuntu1604-for-hibernation"></a>

To configure Ubuntu 16\.04 LTS to support hibernation, you need to install the linux\-aws\-hwe kernel package version 4\.15\.0\-1058\-aws or later and the ec2\-hibinit\-agent\.

**To configure an Ubuntu 16\.04 LTS AMI to support hibernation**

1. Update the kernel to `4.15.0-1058-aws` or later\.

   ```
   [ec2-user ~]$ sudo apt update
   [ec2-user ~]$ sudo apt install linux-aws-hwe
   ```
**Note**  
The `linux-aws-hwe` kernel package is fully supported by Canonical\. The package will continue to receive regular updates until standard support for Ubuntu 16\.04 LTS ends in April 2021, and will receive additional security updates until the Extended Security Maintenance support ends in 2024\. For more information, see [Amazon EC2 Hibernation for Ubuntu 16\.04 LTS now available](https://ubuntu.com/blog/amazon-ec2-hibernation-for-ubuntu-16-04-lts-now-available) on the Canonical Ubuntu Blog\.

1. Install the `ec2-hibinit-agent` package from the repositories\.

   ```
   [ec2-user ~]$ sudo apt install ec2-hibinit-agent
   ```

1. Reboot the instance\.

   ```
   [ec2-user ~]$ sudo reboot
   ```

1. Confirm that the kernel version is updated to `4.15.0-1058-aws` or later\.

   ```
   [ec2-user ~]$ uname -a
   ```
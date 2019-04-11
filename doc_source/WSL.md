# Connecting to Your Linux Instance from Windows Using Windows Subsystem for Linux<a name="WSL"></a>

The following instructions explain how to connect to your instance using a Linux distribution on the Windows Subsystem for Linux \(WSL\)\. WSL is a free download and enables you to run native Linux command\-line tools directly on Windows, alongside your traditional Windows desktop, without the overhead of a virtual machine\. 

By installing WSL, you can use a native Linux environment to connect to your Linux EC2 instances instead of using PuTTY or PuTTYgen\. The Linux environment makes it easier to connect to your Linux instances because it comes with a native SSH client that you can use to connect to your Linux instances and change the permissions of the \.pem key file\. The Amazon EC2 console provides the SSH command for connecting to the Linux instance, and you can get verbose output from the SSH command for troubleshooting\. For more information, see the [Windows Subsystem for Linux Documentation](https://docs.microsoft.com/en-us/windows/wsl/about)\.

After you launch your instance, you can connect to it and use it the way that you'd use a computer sitting in front of you\.

**Note**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column on the **Instances** page\.

If you receive an error while attempting to connect to your instance, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html)\.

**Contents**
+ [Prerequisites](#ssh-prereqs)
+ [Connecting to Your Linux Instance using the Windows Subsystem for Linux](#Connect-Linux-WSL)
+ [Transferring Files to Linux Instances from Linux Using SCP](#Connect-Linux-WSL-SCP)
+ [Uninstalling Windows Subsystem for Linux](#uninstall-WSL)

**Note**  
After you've installed the WSL, all the prerequisites and steps are the same as those described in [Connecting to Your Linux Instance Using SSH](AccessingInstancesLinux.md), and the experience is just like using native Linux\.

## Prerequisites<a name="ssh-prereqs"></a>

Before you connect to your Linux instance, complete the following prerequisites:
+ **Verify the general prerequisites for connecting to your instance\.**

  For more information, see [General Prerequisites for Connecting to Your Instance](connection-prereqs.md)\.
+ **Install the Windows Subsystem for Linux \(WSL\) and a Linux distribution on your local computer\.**

  Install the WSL and a Linux distribution using the instructions in the [Windows 10 Installation Guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10)\. The example in the instructions installs the Ubuntu distribution of Linux, but you can install any distribution\. You are prompted to restart your computer for the changes to take effect\.
+ **Copy the private key from Windows to WSL\.**

  In a WSL terminal window, copy the `.pem` file \(for the key pair that you specified when you launched the instance\) from Windows to WSL\. Note the fully qualified path to the `.pem` file on WSL to use when connecting to your instance\. For information about how to specify the path to your Windows hard drive, see [How do I access my C drive?](https://docs.microsoft.com/en-us/windows/wsl/faq#how-do-i-access-my-c-drive)\.

  ```
  cp /mnt/<Windows drive letter>/path/my-key-pair.pem ~/WSL-path/my-key-pair.pem
  ```

## Connecting to Your Linux Instance using the Windows Subsystem for Linux<a name="Connect-Linux-WSL"></a>

Use the following procedure to connect to your Linux instance using the Windows Subsystem for Linux \(WSL\)\. If you receive an error while attempting to connect to your instance, see [Troubleshooting Connecting to Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html)\.

**To connect to your instance using SSH**

1. In a terminal window, use the ssh command to connect to the instance\. You specify the private key \(`.pem`\) file and *user\_name*@*public\_dns\_name*\. For example, if you used Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.

   ```
   sudo ssh -i /path/my-key-pair.pem ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com
   ```

   You see a response like the following:

   ```
   The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (10.254.142.33)'
   can't be established.
   RSA key fingerprint is 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f.
   Are you sure you want to continue connecting (yes/no)?
   ```

1. \(IPv6 only\) Alternatively, you can connect to the instance using its IPv6 address\. Specify the ssh command with the path to the private key \(\.pem\) file, the appropriate user name, and the IPv6 address\. For example, if you used Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.

   ```
   sudo ssh -i /path/my-key-pair.pem ec2-user@2001:db8:1234:1a00:9691:9503:25ad:1761
   ```

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you previously obtained in [\(Optional\) Get the Instance Fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

1. Enter `yes`\.

   You see a response like the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (RSA) 
   to the list of known hosts.
   ```

## Transferring Files to Linux Instances from Linux Using SCP<a name="Connect-Linux-WSL-SCP"></a>

One way to transfer files between your local computer and a Linux instance is to use the secure copy protocol \(SCP\)\. This section describes how to transfer files with SCP\. The procedure is similar to the procedure for connecting to an instance with SSH\. 

**Prerequisites**
+ **Verify the general prerequisites for transferring files to your instance\.**

  The general prerequisites for transferring files to an instance are the same as the general prerequisites for connecting to an instance\. For more information, see [General Prerequisites for Connecting to Your Instance](connection-prereqs.md)\.
+ **Install an SCP client**

  Most Linux, Unix, and Apple computers include an SCP client by default\. If yours doesn't, the OpenSSH project provides a free implementation of the full suite of SSH tools, including an SCP client\. For more information, see [http://www\.openssh\.org](http://www.openssh.org/)\.

The following procedure steps you through using SCP to transfer a file\. If you've already connected to the instance with SSH and have verified its fingerprints, you can start with the step that contains the SCP command \(step 4\)\.

**To use SCP to transfer a file**

1. Transfer a file to your instance using the instance's public DNS name\. For example, if the name of the private key file is `my-key-pair`, the file to transfer is `SampleFile.txt`, the user name is `ec2-user`, and the public DNS name of the instance is `ec2-198-51-100-1.compute-1.amazonaws.com`, use the following command to copy the file to the `ec2-user` home directory:

   ```
   scp -i /path/my-key-pair.pem /path/SampleFile.txt ec2-user@c2-198-51-100-1.compute-1.amazonaws.com:~
   ```

   You see a response like the following:

   ```
   The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (10.254.142.33)'
   can't be established.
   RSA key fingerprint is 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f.
   Are you sure you want to continue connecting (yes/no)?
   ```

1. \(IPv6 only\) Alternatively, you can transfer a file using the IPv6 address for the instance\. The IPv6 address must be enclosed in square brackets \(\[\]\), which must be escaped \(\\\)\.

   ```
   scp -i /path/my-key-pair.pem /path/SampleFile.txt ec2-user@\[2001:db8:1234:1a00:9691:9503:25ad:1761\]:~
   ```

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you previously obtained in [\(Optional\) Get the Instance Fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

1. Enter **yes**\.

   You see a response like the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (RSA) 
   to the list of known hosts.
   Sending file modes: C0644 20 SampleFile.txt
   Sink: C0644 20 SampleFile.txt
   SampleFile.txt                                100%   20     0.0KB/s   00:00
   ```

   If you receive a "bash: scp: command not found" error, you must first install scp on your Linux instance\. For some operating systems, this is located in the `openssh-clients` package\. For Amazon Linux variants, such as the Amazon ECS\-optimized AMI, use the following command to install scp:

   ```
   [ec2-user ~]$ sudo yum install -y openssh-clients
   ```

1. To transfer files in the other direction \(from your Amazon EC2 instance to your local computer\), reverse the order of the host parameters\. For example, to transfer the `SampleFile.txt` file from your EC2 instance back to the home directory on your local computer as `SampleFile2.txt`, use the following command on your local computer:

   ```
   scp -i /path/my-key-pair.pem ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com:~/SampleFile.txt ~/SampleFile2.txt
   ```

1. \(IPv6 only\) Alternatively, you can transfer files in the other direction using the instance's IPv6 address:

   ```
   scp -i /path/my-key-pair.pem ec2-user@\[2001:db8:1234:1a00:9691:9503:25ad:1761\]:~/SampleFile.txt ~/SampleFile2.txt
   ```

## Uninstalling Windows Subsystem for Linux<a name="uninstall-WSL"></a>

For information about uninstalling Windows Subsystem for Linux, see [How do I uninstall a WSL Distribution?](https://docs.microsoft.com/en-us/windows/wsl/faq#how-do-i-uninstall-a-wsl-distribution)\.
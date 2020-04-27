# Connecting to your Linux instance using SSH<a name="AccessingInstancesLinux"></a>

After you launch your instance, you can connect to it and use it the way that you'd use a computer sitting in front of you\.

The following instructions explain how to connect to your instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshooting connecting to your instance](TroubleshootingInstancesConnecting.md)\.

## Prerequisites<a name="ssh-prereqs"></a>

Before you connect to your Linux instance, complete the following prerequisites\.

**Verify that the instance is ready**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column on the **Instances** page\.

**Verify the general prerequisites for connecting to your instance**  
For more information, see [General prerequisites for connecting to your instance](connection-prereqs.md)\.

**Install an SSH client on your local computer as needed**  
Your local computer might have an SSH client installed by default\. You can verify this by typing ssh at the command line\. If your compute doesn't recognize the command, you can install an SSH client\.  
+ Recent versions of Windows Server 2019 and Windows 10 \- OpenSSH is included as an installable component\. For information, see [OpenSSH in Windows](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview)\.
+ Earlier versions of Windows \- Download and install OpenSSH\. For more information, see [Win32\-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/wiki)\.
+ Linux and macOS X \- Download and install OpenSSH\. For more information, see [http://www\.openssh\.com](http://www.openssh.com/)\.

## Connect to your Linux instance using an SSH client<a name="AccessingInstancesLinuxSSHClient"></a>

Use the following procedure to connect to your Linux instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshooting connecting to your instance](TroubleshootingInstancesConnecting.md)\.

**To connect to your instance using SSH**

1. In a terminal window, use the ssh command to connect to the instance\. You specify the path and file name of the private key \(`.pem`\), the user name for your AMI, and the public DNS name or IPv6 address for your instance\. For more information about how to find the private key, the user name for an AMI, and the DNS name or IPv6 address for an instance, see [Locate the private key](connection-prereqs.md#connection-prereqs-private-key) and [Get information about your instance](connection-prereqs.md#connection-prereqs-get-info-about-instance)\. To connect to your instance, do one of the following:
   + \(Public DNS\) To connect using your instance's public DNS, enter the following command\.

     ```
     ssh -i /path/my-key-pair.pem ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com
     ```
   + \(IPv6\) Alternatively, if your instance has an IPv6 address, to connect using your instance's IPv6 address, enter the following command\.

     ```
     ssh -i /path/my-key-pair.pem ec2-user@2001:db8:1234:1a00:9691:9503:25ad:1761
     ```

   You see a response like the following:

   ```
   The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (10.254.142.33)'
   can't be established.
   RSA key fingerprint is 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f.
   Are you sure you want to continue connecting (yes/no)?
   ```

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you previously obtained in [\(Optional\) Get the instance fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

1. Enter `yes`\. You see a response like the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (RSA) 
   to the list of known hosts.
   ```

## Transferring files to Linux instances from Linux using SCP<a name="AccessingInstancesLinuxSCP"></a>

One way to transfer files between your local computer and a Linux instance is to use the secure copy protocol \(SCP\)\. This section describes how to transfer files with SCP\. The procedure is similar to the procedure for connecting to an instance with SSH\. 

**Prerequisites**
+ **Verify the general prerequisites for transferring files to your instance\.**

  The general prerequisites for transferring files to an instance are the same as the general prerequisites for connecting to an instance\. For more information, see [General prerequisites for connecting to your instance](connection-prereqs.md)\.
+ **Install an SCP client**

  Most Linux, Unix, and Apple computers include an SCP client by default\. If yours doesn't, the OpenSSH project provides a free implementation of the full suite of SSH tools, including an SCP client\. For more information, see [http://www\.openssh\.org](http://www.openssh.org/)\.

The following procedure steps you through using SCP to transfer a file\. If you've already connected to the instance with SSH and have verified its fingerprints, you can start with the step that contains the SCP command \(step 4\)\.

**To use SCP to transfer a file**

1. Transfer a file to your instance using the instance's public DNS name\. For example, if the name of the private key file is `my-key-pair`, the file to transfer is `SampleFile.txt`, the user name is `ec2-user`, and the public DNS name of the instance is `ec2-198-51-100-1.compute-1.amazonaws.com`, use the following command to copy the file to the `ec2-user` home directory\.

   ```
   scp -i /path/my-key-pair.pem /path/SampleFile.txt ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com:~
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

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you previously obtained in [\(Optional\) Get the instance fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

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
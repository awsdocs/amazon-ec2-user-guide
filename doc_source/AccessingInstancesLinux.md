# Connect to your Linux instance using SSH<a name="AccessingInstancesLinux"></a>

After you launch your instance, you can connect to it and use it the way that you'd use a computer sitting in front of you\.

The following instructions explain how to connect to your instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\. For more connection options, see [Connect to your Linux instance](AccessingInstances.md)\.

## Prerequisites<a name="ssh-prereqs"></a>

Before you connect to your Linux instance, complete the following prerequisites\.

**Check your instance status**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status check** column on the **Instances** page\.

**Get the public DNS name and user name to connect to your instance**  
To find the public DNS name or IP address of your instance and the user name that you should use to connect to your instance, see [Prerequisites for connecting to your instance](connection-prereqs.md)\.

**Locate the private key and set the permissions**  
To locate the private key that is required to connect to your instance, and to set the key permissions, see [Locate the private key and set the permissions](connection-prereqs.md#connection-prereqs-private-key)\.

**Install an SSH client on your local computer as needed**  
Your local computer might have an SSH client installed by default\. You can verify this by typing ssh at the command line\. If your computer doesn't recognize the command, you can install an SSH client\.  
+ Recent versions of Windows Server 2019 and Windows 10 \- OpenSSH is included as an installable component\. For more information, see [OpenSSH in Windows](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview)\.
+ Earlier versions of Windows \- Download and install OpenSSH\. For more information, see [Win32\-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH/wiki)\.
+ Linux and macOS X \- Download and install OpenSSH\. For more information, see [https://www\.openssh\.com](https://www.openssh.com/)\.

## Connect to your Linux instance using an SSH client<a name="AccessingInstancesLinuxSSHClient"></a>

Use the following procedure to connect to your Linux instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\.

**To connect to your instance using SSH**

1. In a terminal window, use the ssh command to connect to the instance\. You specify the path and file name of the private key \(`.pem`\), the user name for your instance, and the public DNS name or IPv6 address for your instance\. For more information about how to find the private key, the user name for your instance, and the DNS name or IPv6 address for an instance, see [Locate the private key and set the permissions](connection-prereqs.md#connection-prereqs-private-key) and [Get information about your instance](connection-prereqs.md#connection-prereqs-get-info-about-instance)\. To connect to your instance, use one of the following commands\.
   + \(Public DNS\) To connect using your instance's public DNS name, enter the following command\.

     ```
     ssh -i /path/my-key-pair.pem my-instance-user-name@my-instance-public-dns-name
     ```
   + \(IPv6\) Alternatively, if your instance has an IPv6 address, to connect using your instance's IPv6 address, enter the following command\.

     ```
     ssh -i /path/my-key-pair.pem my-instance-user-name@my-instance-IPv6-address
     ```

   You see a response like the following:

   ```
   The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (198-51-100-1)' can't be established.
   ECDSA key fingerprint is l4UB/neBad9tvkgJf1QZWxheQmR59WgrgzEimCG6kZY.
   Are you sure you want to continue connecting (yes/no)?
   ```

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you previously obtained in [\(Optional\) Get the instance fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

1. Enter **yes**\.

   You see a response like the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (ECDSA) to the list of known hosts.
   ```

## Transfer files to Linux instances using an SCP client<a name="AccessingInstancesLinuxSCP"></a>

One way to transfer files between your local computer and a Linux instance is to use the secure copy protocol \(SCP\)\. This section describes how to transfer files with SCP\. The procedure is similar to the procedure for connecting to an instance with SSH\. 

**Prerequisites**
+ **Verify the general prerequisites for transferring files to your instance\.**

  The general prerequisites for transferring files to an instance are the same as the general prerequisites for connecting to an instance\. For more information, see [General prerequisites for connecting to your instance](connection-prereqs.md)\.
+ **Install an SCP client**

  Most Linux, Unix, and Apple computers include an SCP client by default\. If yours doesn't, the OpenSSH project provides a free implementation of the full suite of SSH tools, including an SCP client\. For more information, see [https://www\.openssh\.com](https://www.openssh.com)\.

The following procedure steps you through using SCP to transfer a file using the instance's public DNS name, or the IPv6 address if your instance has one\.

**To use SCP to transfer files between your computer and your instance**

1. Determine the location of the source file on your computer and the destination path on the instance\. In the following examples, the name of the private key file is `my-key-pair.pem`, the file to transfer is `my-file.txt`, the user name for the instance is ec2\-user, the public DNS name of the instance is `my-instance-public-dns-name`, and the IPv6 address of the instance is `my-instance-IPv6-address`\.
   + \(Public DNS\) To transfer a file to the destination on the instance, enter the following command from your computer\.

     ```
     scp -i /path/my-key-pair.pem /path/my-file.txt ec2-user@my-instance-public-dns-name:path/
     ```
   + \(IPv6\) To transfer a file to the destination on the instance if the instance has an IPv6 address, enter the following command from your computer\. The IPv6 address must be enclosed in square brackets \(`[ ]`\), which must be escaped \(`\`\)\.

     ```
     scp -i /path/my-key-pair.pem /path/my-file.txt ec2-user@\[my-instance-IPv6-address\]:path/
     ```

1. If you haven't already connected to the instance using SSH, you see a response like the following:

   ```
   The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (10.254.142.33)'
   can't be established.
   RSA key fingerprint is 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f.
   Are you sure you want to continue connecting (yes/no)?
   ```

   \(Optional\) You can optionally verify that the fingerprint in the security alert matches the instance fingerprint\. For more information, see [\(Optional\) Get the instance fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\.

   Enter **yes**\.

1. If the transfer is successful, the response is similar to the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (RSA) 
   to the list of known hosts.
   my-file.txt                                100%   480     24.4KB/s   00:00
   ```

1. To transfer a file in the other direction \(from your Amazon EC2 instance to your computer\), reverse the order of the host parameters\. For example, you can transfer `my-file.txt` from your EC2 instance to the a destination on your local computer as `my-file2.txt`, as shown in the following examples\.
   + \(Public DNS\) To transfer a file to a destination on your computer, enter the following command from your computer\.

     ```
     scp -i /path/my-key-pair.pem ec2-user@my-instance-public-dns-name:path/my-file.txt path/my-file2.txt
     ```
   + \(IPv6\) To transfer a file to a destination on your computer if the instance has an IPv6 address, enter the following command from your computer\. The IPv6 address must be enclosed in square brackets \(`[ ]`\), which must be escaped \(`\`\)\.

     ```
     scp -i /path/my-key-pair.pem ec2-user@\[my-instance-IPv6-address\]:path/my-file.txt path/my-file2.txt
     ```

## Troubleshoot<a name="AccessingInstancesLinux-troubleshoot"></a>

If you receive an error while attempting to connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\.
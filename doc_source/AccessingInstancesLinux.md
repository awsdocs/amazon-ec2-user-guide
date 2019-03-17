# Connecting to Your Linux Instance Using SSH<a name="AccessingInstancesLinux"></a>

The following instructions explain how to connect to your instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshooting Connecting to Your Instance](TroubleshootingInstancesConnecting.md)\.

After you launch your instance, you can connect to it and use it the way that you'd use a computer sitting in front of you\.

**Note**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status Checks** column on the **Instances** page\.

## Prerequisites<a name="ssh-prereqs"></a>

Before you connect to your Linux instance, complete the following prerequisites:
+ **Install an SSH client**

  With Windows 10 1709, you can enable the feature "OpenSSH Client \(Beta\)"\. With Linux and Mac OS X, most likely there's an SSH client installed by default\. You can check for an SSH client by typing ssh at the command line\. If your computer doesn't recognize the command, you can install an SSH client\. For more information, see [http://www\.openssh\.com](http://www.openssh.com/)\.
+ **Install the AWS CLI Tools**

  \(Optional\) If you're using a public AMI from a third party, you can use the command line tools to verify the fingerprint\. For more information about installing the AWS CLI, see [Getting Set Up](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.
+ **Get the ID of the instance**

  You can get the ID of your instance using the Amazon EC2 console \(from the **Instance ID** column\)\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **Get the public DNS name of the instance**

  You can get the public DNS for your instance using the Amazon EC2 console\. Check the **Public DNS \(IPv4\)** column\. If this column is hidden, choose the **Show/Hide** icon and select **Public DNS \(IPv4\)**\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **\(IPv6 only\) Get the IPv6 address of the instance**

  If you've assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance using the Amazon EC2 console\. Check the **IPv6 IPs** field\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\. For more information about IPv6, see [IPv6 Addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Locate the private key**** and verify permissions**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. Verify that the `.pem` file has permissions of 0400, not 0777\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.
+ **Get the default user name for the AMI that you used to launch your instance**
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a Centos AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin` or `root`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.
+ **Enable inbound SSH traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming SSH traffic from your IP address\. The default security group for the VPC does not allow incoming SSH traffic by default\. The security group created by the launch wizard enables SSH traffic by default\. For more information, see [Authorizing Inbound Traffic for Your Linux Instances](authorizing-access-to-an-instance.md)\.

## Connecting to Your Linux Instance<a name="AccessingInstancesLinuxSSHClient"></a>

Use the following procedure to connect to your Linux instance using an SSH client\. If you receive an error while attempting to connect to your instance, see [Troubleshooting Connecting to Your Instance](TroubleshootingInstancesConnecting.md)\.

**To connect to your instance using SSH**

1. \(Optional\) You can verify the RSA key fingerprint on your running instance by using one of the following commands on your local system \(not on the instance\)\. This is useful if you've launched your instance from a public AMI from a third party\. Locate the `SSH HOST KEY FINGERPRINTS` section, and note the RSA fingerprint \(for example, 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f\) and compare it to the fingerprint of the instance\. 
   + [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\)

     ```
     aws ec2 get-console-output --instance-id instance_id
     ```

   Ensure that the instance is in the `running` state, not the `pending` state\. The `SSH HOST KEY FINGERPRINTS` section is only available after the first boot of the instance\.

1. In a command\-line shell, change directories to the location of the private key file that you created when you launched the instance\.

1. Use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 /path/my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

1. Use the ssh command to connect to the instance\. You specify the private key \(`.pem`\) file and *user\_name*@*public\_dns\_name*\. For example, if you used Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.

   ```
   ssh -i /path/my-key-pair.pem ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com
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
   ssh -i /path/my-key-pair.pem ec2-user@2001:db8:1234:1a00:9691:9503:25ad:1761
   ```

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you obtained in step 1\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

1. Enter `yes`\.

   You see a response like the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (RSA) 
   to the list of known hosts.
   ```

## Transferring Files to Linux Instances from Linux Using SCP<a name="AccessingInstancesLinuxSCP"></a>

One way to transfer files between your local computer and a Linux instance is to use the secure copy protocol \(SCP\)\. This section describes how to transfer files with SCP\. The procedure is similar to the procedure for connecting to an instance with SSH\. 

**Prerequisites**
+ **Install an SCP client**

  Most Linux, Unix, and Apple computers include an SCP client by default\. If yours doesn't, the OpenSSH project provides a free implementation of the full suite of SSH tools, including an SCP client\. For more information, see [http://www\.openssh\.org](http://www.openssh.org/)\. 
+ **Get the ID of the instance**

  You can get the ID of your instance using the Amazon EC2 console \(from the **Instance ID** column\)\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **Get the public DNS name of the instance**

  You can get the public DNS for your instance using the Amazon EC2 console\. Check the **Public DNS \(IPv4\)** column\. If this column is hidden, choose the **Show/Hide** icon and select **Public DNS \(IPv4\)**\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **\(IPv6 only\) Get the IPv6 address of the instance**

  If you've assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance using the Amazon EC2 console\. Check the **IPv6 IPs** field\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\. For more information about IPv6, see [IPv6 Addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Locate the private key**** and verify permissions**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. Verify that the `.pem` file has permissions of 0400, not 0777\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.
+ **Get the default user name for the AMI that you used to launch your instance**
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a Centos AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin` or `root`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.
+ **Enable inbound SSH traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming SSH traffic from your IP address\. The default security group for the VPC does not allow incoming SSH traffic by default\. The security group created by the launch wizard enables SSH traffic by default\. For more information, see [Authorizing Inbound Traffic for Your Linux Instances](authorizing-access-to-an-instance.md)\.

The following procedure steps you through using SCP to transfer a file\. If you've already connected to the instance with SSH and have verified its fingerprints, you can start with the step that contains the SCP command \(step 4\)\.

**To use SCP to transfer a file**

1. \(Optional\) You can verify the RSA key fingerprint on your instance by using one of the following commands on your local system \(not on the instance\)\. This is useful if you've launched your instance from a public AMI from a third party\. Locate the `SSH HOST KEY FINGERPRINTS` section, and note the RSA fingerprint \(for example, 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f\) and compare it to the fingerprint of the instance\. 
   + [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\)

     ```
     aws ec2 get-console-output --instance-id instance_id
     ```

   The `SSH HOST KEY FINGERPRINTS` section is only available after the first boot of the instance\.

1. In a command shell, change directories to the location of the private key file that you specified when you launched the instance\.

1. Use the chmod command to make sure that your private key file isn't publicly viewable\. For example, if the name of your private key file is `my-key-pair.pem`, use the following command:

   ```
   chmod 400 /path/my-key-pair.pem
   ```

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

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you obtained in step 1\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, continue to the next step\.

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

1. \(IPv6 only\) Alternatively, you can transfer files in the other direction using the instance's IPv6 address\.

   ```
   scp -i /path/my-key-pair.pem ec2-user@\[2001:db8:1234:1a00:9691:9503:25ad:1761\]:~/SampleFile.txt ~/SampleFile2.txt
   ```

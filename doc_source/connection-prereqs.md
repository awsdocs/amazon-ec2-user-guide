# Set up to connect to your instance<a name="connection-prereqs"></a>

To set up to connect to a Windows instance, see [Prerequisites](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html#rdp-prereqs) in the *Amazon EC2 User Guide for Windows Instances*\.

Perform the tasks in this topic to set up to connect to your Amazon EC2 Linux instance\. For prerequisites specific to connection types, such as SSH, EC2 Instance Connect, OpenSSH, PuTTY, and more, see the following options for connecting from Linux, macOS X, or Windows\.

**Linux or macOS X**  
If your local computer operating system is Linux or macOS X, the following options to connect to your instance are supported:
+ [SSH client](AccessingInstancesLinux.md)
+ [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

**Windows**  
If your local computer operating system is Windows, the following options to connect to your instance are supported:
+ [OpenSSH](openssh.md)
+ [PuTTY](putty.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
+ [Windows Subsystem for Linux](WSL.md)

**Topics**
+ [Get information about your instance](#connection-prereqs-get-info-about-instance)
+ [Locate the private key and set the permissions](#connection-prereqs-private-key)
+ [\(Optional\) Get the instance fingerprint](#connection-prereqs-fingerprint)

**Troubleshoot connecting to your instance**  
[Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)

## Get information about your instance<a name="connection-prereqs-get-info-about-instance"></a>

To prepare to connect to an instance, get the following information from the Amazon EC2 console or by using the AWS CLI\.

![\[The Instances pane of the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/connection-prereqs-console2.png)
+ **Get the public DNS name of the instance\.**

  You can get the public DNS for your instance from the Amazon EC2 console\. Check the **Public IPv4 DNS** column of the **Instances** pane\. If this column is hidden, choose the settings icon \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \) in the top\-right corner of the screen, and select **Public IPv4 DNS**\. You can also find the public DNS in the instance information section of the **Instances** pane\. When you select the instance in the **Instances** pane of the Amazon EC2 console, information about that instance will appear on the lower half of the page\. Under the **Details** tab, look for **Public IPv4 DNS**\.

  If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) commands\.

  If no **Public IPv4 DNS** is displayed, verify that the **Instance state** is **Running**, and that you have not launched the instance in a private subnet\. If you launched your instance using the [launch instance wizard](ec2-launch-instance-wizard.md), you may have edited the **Auto\-assign public IP** field under **Network settings** and changed the value to **Disable**\. If you disable the **Auto\-assign public IP** option, the instance is not assigned a public IP address when it is launched\. 
+ **\(IPv6 only\) Get the IPv6 address of the instance\.**

  If you assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance from the Amazon EC2 console\. Check the **IPv6 IPs** column of the **Instances** pane\. Or, you can find the IPv6 address in the instance information section\. When you select the instance in the **Instances** pane of the Amazon EC2 console, information about that instance will appear on the lower half of the page\. Under the **Details** tab, look for **IPv6 address**\.

  If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) commands\. For more information about IPv6, see [IPv6 addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Get the user name for your instance\.**

  You can connect to your instance using the username for your user account or the default username for the AMI that you used to launch your instance\.
  + **Get the username for your user account\.**

    For more information about how to create a user account, see [Manage users on your Linux instance](managing-users.md)\.
  + **Get the default username for the AMI that you used to launch your instance:**    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connection-prereqs.html)

## Locate the private key and set the permissions<a name="connection-prereqs-private-key"></a>

You must know the location of your private key file to connect to your instance\. For SSH connections, you must set the permissions so that only you can read the file\.

For information about how key pairs work when using Amazon EC2, see [Amazon EC2 key pairs and Linux instances](ec2-key-pairs.md)\.
+ **Locate the private key**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. For more information, see [Identify the public key specified at launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/describe-keys.html#identify-key-pair-specified-at-launch)\. If you can't find your private key file, see [I've lost my private key\. How can I connect to my Linux instance?](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#replacing-lost-key-pair)

  If you are connecting to your instance using Putty and need to convert the `.pem` file to `.ppk`, see [Convert your private key using PuTTYgen](putty.md#putty-private-key) in the [Connect to your Linux instance from Windows using PuTTY](putty.md) topic in this section\.
+ **Set the permissions of your private key so that only you can read it**
  + **Connect from macOS or Linux**

    If you plan to use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

    ```
    chmod 400 key-pair-name.pem
    ```

    If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.
  + **Connect from Windows**

    Open File Explorer and right\-click on the `.pem` file\. Select **Properties** > **Security tab** and choose **Advanced**\. Choose **Disable inheritance**\. Remove access to all users except for the current user\. 

## \(Optional\) Get the instance fingerprint<a name="connection-prereqs-fingerprint"></a>

To protect yourself from man\-in\-the\-middle attacks, you can verify the fingerprint of your instance when you connect to it\. Verifying the fingerprint is useful if you launch your instance from a public AMI provided by a third party\.

**Task overview**  
First, get the instance fingerprint\. When you connect to the instance, you are prompted to verify the fingerprint\. Compare the fingerprint you obtained with the fingerprint displayed\. If the fingerprints don't match, someone might be attempting a man\-in\-the\-middle attack\. If they match, you can confidently connect to your instance\.

**Prerequisites to get the instance fingerprint**
+ To get the instance fingerprint, you must use the AWS CLI\. For information about installing the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.
+ The instance must not be in the `pending` state\. The fingerprint is available only after the first boot of the instance is complete\.

**To get the instance fingerprint**

1. On your local computer \(not on the instance you are connecting to\), use the [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\) command as follows to obtain the fingerprint of the instance\. You must be the instance owner to get the console output\. If the output is large, [you can pipe it to a text file](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output-format.html), where it might be easier to read\. Note that you must specify an AWS Region when you use the AWS CLI, either explicitly or by setting a default Region\. For information about how to set or specify a Region, see [Configuration basics](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html) in the *AWS Command Line Interface User Guide*\.

   ```
   aws ec2 get-console-output --instance-id instance_id --output text > temp.txt
   ```

1. The following example output shows what you should look for when you run the [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) command\. The exact output can vary by operating system, AMI version, and whether AWS created the key pairs\.

   ```
   ec2: #############################################################
   ec2: -----BEGIN SSH HOST KEY FINGERPRINTS-----
   ec2: 1024 SHA256:7HItIgTONZ/b0CH9c5Dq1ijgqQ6kFn86uQhQ5E/F9pU root@ip-10-0-2-182 (DSA)
   ec2: 256 SHA256:l4UB/neBad9tvkgJf1QZWxheQmR59WgrgzEimCG6kZY root@ip-10-0-2-182 (ECDSA)
   ec2: 256 SHA256:kpEa+rw/Uq3zxaYZN8KT501iBtJOIdHG52dFi66EEfQ no comment (ED25519)
   ec2: 2048 SHA256:L8l6pepcA7iqW/jBecQjVZClUrKY+o2cHLI0iHerbVc root@ip-10-0-2-182 (RSA)
   ec2: -----END SSH HOST KEY FINGERPRINTS-----
   ec2: #############################################################
   ```
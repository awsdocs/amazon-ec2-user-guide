# General prerequisites for connecting to your instance<a name="connection-prereqs"></a>

**Topics**
+ [Get information about your instance](#connection-prereqs-get-info-about-instance)
+ [Enable inbound traffic to your instance](#connection-prereqs-enable-inbound-traffic)
+ [Locate the private key](#connection-prereqs-private-key)
+ [\(Optional\) Get the instance fingerprint](#connection-prereqs-fingerprint)

## Get information about your instance<a name="connection-prereqs-get-info-about-instance"></a>
+ **Get the ID of the instance\.**

  You can get the ID of your instance using the Amazon EC2 console \(from the **Instance ID** column\)\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **Get the public DNS name of the instance\.**

  You can get the public DNS for your instance using the Amazon EC2 console\. Check the **Public DNS \(IPv4\)** column\. If this column is hidden, choose the settings icon \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \) in the top\-right corner of the screen and select **Public DNS \(IPv4\)**\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **\(IPv6 only\) Get the IPv6 address of the instance\.**

  If you've assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance using the Amazon EC2 console\. Check the **IPv6 IPs** field\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\. For more information about IPv6, see [IPv6 addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Get the user name for your instance\.**

  You can connect to your instance using the user name for your user account or the default user name for the AMI that you used to launch your instance\.
  + **Get the user name for your user account\.**

    For more information about how to create a user account, see [Manage user accounts on your Amazon Linux instance](managing-users.md)\.
  + **Get the default user name for the AMI that you used to launch your instance:**
    + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
    + For a CentOS AMI, the user name is `centos`\.
    + For a Debian AMI, the user name is `admin`\.
    + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
    + For a RHEL AMI, the user name is `ec2-user` or `root`\.
    + For a SUSE AMI, the user name is `ec2-user` or `root`\.
    + For an Ubuntu AMI, the user name is `ubuntu`\.
    + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

## Enable inbound traffic to your instance<a name="connection-prereqs-enable-inbound-traffic"></a>
+ **Enable inbound SSH traffic from your IP address to your instance\.**

  Ensure that the security group associated with your instance allows incoming SSH traffic from your IP address\. The default security group for the VPC does not allow incoming SSH traffic by default\. The security group created by the launch instance wizard enables SSH traffic by default\. For more information, see [Authorize inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.

## Locate the private key<a name="connection-prereqs-private-key"></a>
+ **Locate the private key**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. For more information about how you created your key pair, see [Creating a Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)\.
+ **Set the permissions of your private key**

  If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

  ```
  chmod 400 my-key-pair.pem
  ```

  If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

## \(Optional\) Get the instance fingerprint<a name="connection-prereqs-fingerprint"></a>

To protect yourself from man\-in\-the\-middle attacks, you can verify the RSA key fingerprint when you connect to your instance\. Verifying the fingerprint is useful if you've launched your instance from a public AMI from a third party\.

First you get the instance fingerprint\. Then, when you connect to the instance, you are prompted to verify the fingerprint\. You can compare the fingerprint you obtained with the fingerprint displayed for verification\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, you can confidently connect to your instance\.

**Prerequisites for getting the instance fingerprint:**
+ To get the instance fingerprint, you must use the AWS CLI\. For information about installing the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.
+ The instance must not be in the `pending` state\. The fingerprint is available only after the first boot of the instance is complete\.

**To get the instance fingerprint**

1. On your local computer \(not on the instance\), use the [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\) command as follows to obtain the fingerprint:

   ```
   aws ec2 get-console-output --instance-id instance_id --output text
   ```

1. Here is an example of what you should look for in the output\. The exact output can vary by the operating system, AMI version, and whether you had AWS create the key\.

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
# General Prerequisites for Connecting to Your Instance<a name="connection-prereqs"></a>

**Topics**
+ [Get Information About Your Instance](#connection-prereqs-get-info-about-instance)
+ [Enable Inbound Traffic to Your Instance](#connection-prereqs-enable-inbound-traffic)
+ [Locate the Private Key](#connection-prereqs-private-key)
+ [\(Optional\) Get the Instance Fingerprint](#connection-prereqs-fingerprint)

## Get Information About Your Instance<a name="connection-prereqs-get-info-about-instance"></a>
+ **Get the ID of the instance**

  You can get the ID of your instance using the Amazon EC2 console \(from the **Instance ID** column\)\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **Get the public DNS name of the instance**

  You can get the public DNS for your instance using the Amazon EC2 console\. Check the **Public DNS \(IPv4\)** column\. If this column is hidden, choose the **Show/Hide** icon and select **Public DNS \(IPv4\)**\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\.
+ **\(IPv6 only\) Get the IPv6 address of the instance**

  If you've assigned an IPv6 address to your instance, you can optionally connect to the instance using its IPv6 address instead of a public IPv4 address or public IPv4 DNS hostname\. Your local computer must have an IPv6 address and must be configured to use IPv6\. You can get the IPv6 address of your instance using the Amazon EC2 console\. Check the **IPv6 IPs** field\. If you prefer, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\) or [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\) command\. For more information about IPv6, see [IPv6 Addresses](using-instance-addressing.md#ipv6-addressing)\.
+ **Get the default user name for the AMI that you used to launch your instance**
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a CentOS AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin` or `root`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

## Enable Inbound Traffic to Your Instance<a name="connection-prereqs-enable-inbound-traffic"></a>
+ **Enable inbound SSH traffic from your IP address to your instance**

  Ensure that the security group associated with your instance allows incoming SSH traffic from your IP address\. The default security group for the VPC does not allow incoming SSH traffic by default\. The security group created by the launch wizard enables SSH traffic by default\. For more information, see [Authorizing Inbound Traffic for Your Linux Instances](authorizing-access-to-an-instance.md)\.

## Locate the Private Key<a name="connection-prereqs-private-key"></a>
+ **Locate the private key**** and verify permissions**

  Get the fully\-qualified path to the location on your computer of the `.pem` file for the key pair that you specified when you launched the instance\. For more information about how you created your key pair, see [Creating a Key Pair Using Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)\.

   Verify that the `.pem` file has permissions of 0400, not 0777\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

**To set the permissions of your private key**

1. In a command\-line shell, change directories to the location of the private key file that you created when you launched the instance\.

1. Use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 /path/my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

## \(Optional\) Get the Instance Fingerprint<a name="connection-prereqs-fingerprint"></a>

To protect yourself from man\-in\-the\-middle attacks, you can verify the RSA key fingerprint when you connect to your instance\. Verifying the fingerprint is useful if you've launched your instance from a public AMI from a third party\.

First you get the instance fingerprint\. Then, when you connect to the instance, you are prompted to verify the fingerprint\. You can compare the fingerprint you obtained with the fingerprint displayed for verification\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, you can confidently connect to your instance\.

**Prerequisites for getting the instance fingerprint:**
+ To get the instance fingerprint, you must use the AWS CLI\. For information about installing the AWS CLI, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.
+ The instance must be in the `running` state, not the `pending` state\. 

**To get the instance fingerprint**

1.  On your local computer \(not on the instance\), use the [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) \(AWS CLI\) command to obtain the fingerprint, as follows: 

   ```
   $ aws ec2 get-console-output --instance-id instance_id
   ```

   Here is an example of what you should look for:

   ```
   -----BEGIN SSH HOST KEY FINGERPRINTS -----
   ... 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f ...
   -----END SSH HOST KEY FINGERPRINTS-----
   ```

1. In the output that was generated, locate the `SSH HOST KEY FINGERPRINTS` section and note the RSA fingerprint \(for example, 1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f\)\. The `SSH HOST KEY FINGERPRINTS` section is only available after the first boot of the instance\.
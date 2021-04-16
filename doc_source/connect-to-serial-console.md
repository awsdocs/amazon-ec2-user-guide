# Connect to the EC2 Serial Console<a name="connect-to-serial-console"></a>

You can connect to the serial console of your EC2 instance by using the Amazon EC2 console or via SSH\. After connecting to the serial console, you can use it for troubleshooting boot, network configuration, and other issues\. For more information about troubleshooting, see [Troubleshoot your Linux instance using the EC2 Serial ConsoleTroubleshoot your instance using the EC2 Serial Console](troubleshoot-using-serial-console.md)\.

**Topics**
+ [Considerations](#sc-considerations)
+ [Prerequisites](#sc-prerequisites)
+ [Connect to the EC2 Serial Console](#sc-connection-methods)
+ [EC2 Serial Console fingerprints](#sc-fingerprints)

## Considerations<a name="sc-considerations"></a>
+ Only one active serial console connection is supported per instance\.
+ The serial console connection typically lasts for one hour unless you terminate it\. However, during system maintenance, Amazon EC2 will terminate the serial console session\.
+ It takes 30 seconds to tear down a session after you've disconnected from the serial console in order to allow a new session\.
+ Supported serial console port for Linux: ttyS0

When you connect to the serial console, you might observe a slight drop in your instance’s throughput\.

## Prerequisites<a name="sc-prerequisites"></a>
+ Supported AWS Regions: US East \(N\. Virginia\), US East \(Ohio\), US West \(Oregon\) Europe \(Ireland\), Europe \(Frankfurt\), Asia Pacific \(Sydney\), Asia Pacific \(Tokyo\), Asia Pacific \(Singapore\)
+ Supported instance families:
  + A1
  + C5, C5a, C5ad, C5d, C5n, C6g, C6gd
  + M5, M5a, M5ad, M5d, M5dn, M5n, M5zn, M6g, M6gd
  + R5, R5a, R5ad, R5d, R5dn, R5n, R6, R6gd
  + T3, T3a, T4g
  + Z1d
+ All IAM users who will use the serial console must have the required permissions\. For more information, see [Configure IAM policies for EC2 Serial Console access](configure-access-to-serial-console.md#serial-console-iam)\.
+ The instance must be in the `pending`, `running`, `stopping`, or `shutting-down` state\. If the instance is `terminated` or `stopped`, you can't connect to the serial console\. For more information about the instance states, see [Instance lifecycle](ec2-instance-lifecycle.md)\.

You do not need an sshd server installed or running on your instance\.

## Connect to the EC2 Serial Console<a name="sc-connection-methods"></a>

**Topics**
+ [Connect using the browser\-based client](#sc-connect-browser-based-client)
+ [Connect using your own key and SSH client](#sc-connect-SSH)

### Connect using the browser\-based client<a name="sc-connect-browser-based-client"></a>

You can connect to your EC2 instance's serial console by using the browser\-based client\. You do this by selecting the instance in the Amazon EC2 console and choosing to connect to the serial console\. The browser\-based client handles the permissions and provides a successful connection\.

EC2 serial console works from most browsers, and supports keyboard and mouse input\.

**To connect to your instance's serial port using the browser\-based client \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Connect**, **EC2 Serial Console**, **Connect**\.

   Alternatively, you can select the instance and choose **Actions**, **Monitor and troubleshoot**, **EC2 Serial Console**, **Connect**\.

   An in\-browser terminal window opens\.

1. Press **Enter**\. If a login prompt returns, you are connected to the serial console\.

   If the screen remains black, you can use the following information to help resolve issues with connecting to the serial console:
   + **Check that you have configured access to the serial console\.** For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.
   + **Use SysRq to connect to the serial console\.** SysRq does not require that you connect via the browser\-based client\. For more information, see [Troubleshoot your Linux instance using SysRq](SysRq.md)\.
   + **Restart getty\.** If you have SSH access to your instance, then connect to your instance using SSH, and restart getty using the following command\.

     ```
     [ec2-user ~]$ sudo systemctl restart serial-getty@ttyS0
     ```
   + **Reboot your instance\.** You can reboot your instance by using SysRq, the EC2 console, or the AWS CLI\. For more information, see [Troubleshoot your Linux instance using SysRq](SysRq.md) or [Reboot your instance](ec2-instance-reboot.md)\.

1. At the `login` prompt, enter the user name of the password\-based user that you [set up previously](configure-access-to-serial-console.md#set-user-password), and then press **Enter**\.

1. At the `Password` prompt, enter the password, and then press **Enter**\.

   You are now logged onto the instance and can use the serial console for troubleshooting\.

### Connect using your own key and SSH client<a name="sc-connect-SSH"></a>

You can use your own SSH key and connect to your instance from the SSH client of your choice while using the serial console API\. This enables you to benefit from the serial console capability to push a public key to the instance\.

**To connect to an instance's serial console using SSH**

1. **Push your SSH public key to the instance to start a serial console session**

   Use the [send\-serial\-console\-ssh\-public\-key](https://docs.aws.amazon.com/cli/latest/reference/ec2-instance-connect/send-serial-console-ssh-public-key.html) command to push your SSH public key to the instance\. This starts a serial console session\.

   If a serial console session has already been started for this instance, the command fails because you can only have one session open at a time\. It takes 30 seconds to tear down a session after you've disconnected from the serial console in order to allow a new session\. 

   ```
   $ aws ec2-instance-connect send-serial-console-ssh-public-key \
       --instance-id i-001234a4bf70dec41EXAMPLE \
       --serial-port 0 \
       --ssh-public-key file://my_rsa_key.pub \
       --region us-east-1
   ```

1. 

**Connect to the serial console using your private key**

   Use the ssh command to connect to the serial console before the public key is removed from the serial console service\. You have 60 seconds before it is removed\.

   Use the private key that corresponds to the public key\.

   The user name format is `instance-id.port0`, which comprises the instance ID and port 0\. In the following example, the user name is `i-001234a4bf70dec41EXAMPLE.port0`\.

   The format of the public DNS name of the serial console service is `serial-console.ec2-instance-connect.region.aws`\. In the following example, the serial console service is in the *us\-east\-1* Region\.

   ```
   $ ssh -i my_rsa_key i-001234a4bf70dec41EXAMPLE.port0@serial-console.ec2-instance-connect.us-east-1.aws
   ```

1. 

**\(Optional\) Verify the fingerprint**

   When you connect for the first time to the serial console, you are prompted to verify the fingerprint\. You can compare the serial console fingerprint with the fingerprint that's displayed for verification\. If these fingerprints don't match, someone might be attempting a "man\-in\-the\-middle" attack\. If they match, you can confidently connect to the serial console\.

   The following fingerprint is for the serial console service in the us\-east\-1 Region\. For the fingerprints for each Region, see [EC2 Serial Console fingerprints](#sc-fingerprints)\.

   ```
   SHA256:dXwn5ma/xadVMeBZGEru5l2gx+yI5LDiJaLUcz0FMmw
   ```
**Note**  
The fingerprint only appears the first time you connect to the serial console\.

1. Press **Enter**\. If a prompt returns, you are connected to the serial console\.

   If the screen remains black, you can use the following information to help resolve issues with connecting to the serial console:
   + **Check that you have configured access to the serial console\.** For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.
   + **Use SysRq to connect to the serial console\.** SysRq does not require that you connect via SSH\. For more information, see [Troubleshoot your Linux instance using SysRq](SysRq.md)\.
   + **Restart getty\.** If you have SSH access to your instance, then connect to your instance using SSH, and restart getty using the following command\.

     ```
     [ec2-user ~]$ sudo systemctl restart serial-getty@ttyS0
     ```
   + **Reboot your instance\.** You can reboot your instance by using SysRq, the EC2 console, or the AWS CLI\. For more information, see [Troubleshoot your Linux instance using SysRq](SysRq.md) or [Reboot your instance](ec2-instance-reboot.md)\.

1. At the `login` prompt, enter the user name of the password\-based user that you [set up previously](configure-access-to-serial-console.md#set-user-password), and then press **Enter**\.

1. At the `Password` prompt, enter the password, and then press **Enter**\.

   You are now logged onto the instance and can use the serial console for troubleshooting\.

## EC2 Serial Console fingerprints<a name="sc-fingerprints"></a>

The EC2 Serial Console fingerprint is unique for each AWS Region\.
+ us\-east\-2 – US East \(Ohio\)

  ```
  SHA256:EhwPkTzRtTY7TRSzz26XbB0/HvV9jRM7mCZN0xw/d/0
  ```
+ us\-east\-1 – US East \(N\. Virginia\)

  ```
  SHA256:dXwn5ma/xadVMeBZGEru5l2gx+yI5LDiJaLUcz0FMmw
  ```
+ us\-west\-2 – US West \(Oregon\)

  ```
  SHA256:EMCIe23TqKaBI6yGHainqZcMwqNkDhhAVHa1O2JxVUc
  ```
+ ap\-southeast\-1 – Asia Pacific \(Singapore\)

  ```
  SHA256:PLFNn7WnCQDHx3qmwLu1Gy/O8TUX7LQgZuaC6L45CoY
  ```
+ ap\-southeast\-2 – Asia Pacific \(Sydney\)

  ```
  SHA256:yFvMwUK9lEUQjQTRoXXzuN+cW9/VSe9W984Cf5Tgzo4
  ```
+ ap\-northeast\-1 – Asia Pacific \(Tokyo\)

  ```
  SHA256:RQfsDCZTOfQawewTRDV1t9Em/HMrFQe+CRlIOT5um4k
  ```
+ eu\-central\-1 – Europe \(Frankfurt\)

  ```
  SHA256:aCMFS/yIcOdOlkXvOl8AmZ1Toe+bBnrJJ3Fy0k0De2c
  ```
+ eu\-west\-1 – Europe \(Ireland\)

  ```
  SHA256:h2AaGAWO4Hathhtm6ezs3Bj7udgUxi2qTrHjZAwCW6E
  ```
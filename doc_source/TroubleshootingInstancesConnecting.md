# Troubleshooting Connecting to Your Instance<a name="TroubleshootingInstancesConnecting"></a>

The following are possible problems you may have and error messages you may see while trying to connect to your instance\.

**Topics**
+ [Error connecting to your instance: Connection timed out](#TroubleshootingInstancesConnectionTimeout)
+ [Error: User key not recognized by server](#TroubleshootingInstancesServerError)
+ [Error: Host key not found, Permission denied \(publickey\), *or* Authentication failed, permission denied](#TroubleshootingInstancesConnectingMindTerm)
+ [Error: Unprotected Private Key File](#troubleshoot-unprotected-key)
+ [Error: Server refused our key *or* No supported authentication methods available](#TroubleshootingInstancesConnectingPuTTY)
+ [Error Using MindTerm on Safari Browser](#troubleshoot-instance-connect-safari)
+ [Error Using macOS RDP Client](#troubleshoot-instance-connect-mac-rdp)
+ [Cannot Ping Instance](#troubleshoot-instance-ping)

For additional help with Windows instances, see [Troubleshooting Windows Instances](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-windows-instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

You can also search for answers and post questions on the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\.

## Error connecting to your instance: Connection timed out<a name="TroubleshootingInstancesConnectionTimeout"></a>

If you try to connect to your instance and get an error message `Network error: Connection timed out` or `Error connecting to [instance], reason: -> Connection timed out: connect`, try the following:
+ Check your security group rules\. You need a security group rule that allows inbound traffic from your public IPv4 address on the proper port\.

  1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

  1. In the navigation pane, choose **Instances**, and then select your instance\.

  1. In the **Description** tab, next to **Security groups**, choose **view rules** to display the list of rules that are in effect\.

  1. For Linux instances: Verify that there is a rule that allows traffic from your computer to port 22 \(SSH\)\.

     For Windows instances: Verify that there is a rule that allows traffic from your computer to port 3389 \(RDP\)\.

     If your security group has a rule that allows inbound traffic from a single IP address, this address may not be static if your computer is on a corporate network or if you are connecting through an internet service provider \(ISP\)\. Instead, specify the range of IP addresses used by client computers\. If your security group does not have a rule that allows inbound traffic as described in the previous step, add a rule to your security group\. For more information, see [Authorizing Network Access to Your Instances](authorizing-access-to-an-instance.md)\.
+ \[EC2\-VPC\] Check the route table for the subnet\. You need a route that sends all traffic destined outside the VPC to the internet gateway for the VPC\.

  1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

  1. In the navigation pane, choose **Instances**, and then select your instance\.

  1. In the **Description** tab, write down the values of **VPC ID** and **Subnet ID**\.

  1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

  1. In the navigation pane, choose **Internet Gateways**\. Verify that there is an internet gateway attached to your VPC\. Otherwise, choose **Create Internet Gateway** to create an internet gateway\. Select the internet gateway, and then choose **Attach to VPC** and follow the directions to attach it to your VPC\.

  1. In the navigation pane, choose **Subnets**, and then select your subnet\.

  1. On the **Route Table** tab, verify that there is a route with `0.0.0.0/0` as the destination and the internet gateway for your VPC as the target\. Otherwise, choose the ID of the route table \(rtb\-*xxxxxxxx*\) to navigate to the **Routes** tab for the route table, choose **Edit**, **Add another route**, enter `0.0.0.0/0` in **Destination**, select your internet gateway from **Target**, and then choose **Save**\.

     If you're connecting to your instance using its IPv6 address, verify that there is a route for all IPv6 traffic \(`::/0`\) that points to the internet gateway\. If not, add a route with `::/0` as the destination, and the internet gateway as the target\.
+ \[EC2\-VPC\] Check the network access control list \(ACL\) for the subnet\. The network ACLs must allow inbound and outbound traffic from your local IP address on the proper port\. The default network ACL allows all inbound and outbound traffic\.

  1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

  1. In the navigation pane, choose **Subnets** and select your subnet\.

  1. On the **Summary** tab, find **Network ACL**, and choose its ID \(acl\-*xxxxxxxx*\)\.

  1. Select the network ACL\. For **Inbound Rules**, verify that the rules allow traffic from your computer\. Otherwise, delete or modify the rule that is blocking traffic from your computer\.

  1. For **Outbound Rules**, verify that the rules allow traffic to your computer\. Otherwise, delete or modify the rule that is blocking traffic to your computer\.
+ If your computer is on a corporate network, ask your network administrator whether the internal firewall allows inbound and outbound traffic from your computer on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\.

  If you have a firewall on your computer, verify that it allows inbound and outbound traffic from your computer on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\.
+ Check that your instance has a public IPv4 address\. If not, you can associate an Elastic IP address with your instance\. For more information, see [Elastic IP Addresses](elastic-ip-addresses-eip.md)\. 
+ Check the CPU load on your instance; the server may be overloaded\. AWS automatically provides data such as Amazon CloudWatch metrics and instance status, which you can use to see how much CPU load is on your instance and, if necessary, adjust how your loads are handled\. For more information, see [Monitoring Your Instances Using CloudWatch](using-cloudwatch.md)\. 
  + If your load is variable, you can automatically scale your instances up or down using [Auto Scaling](https://aws.amazon.com/autoscaling/) and [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)\. 
  + If your load is steadily growing, you can move to a larger instance type\. For more information, see [Changing the Instance Type](ec2-instance-resize.md)\. 

To connect to your instance using an IPv6 address, check the following: 
+ Your subnet must be associated with a route table that has a route for IPv6 traffic \(`::/0`\) to an internet gateway\. 
+ Your security group rules must allow inbound traffic from your local IPv6 address on the proper port \(22 for Linux and 3389 for Windows\)\.
+ Your network ACL rules must allow inbound and outbound IPv6 traffic\.
+ If you launched your instance from an older AMI, it may not be configured for DHCPv6 \(IPv6 addresses are not automatically recognized on the network interface\)\. For more information, see [Configure IPv6 on Your Instances](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/vpc-migrate-ipv6.html#vpc-migrate-ipv6-dhcpv6) in the *Amazon VPC User Guide*\.
+ Your local computer must have an IPv6 address, and must be configured to use IPv6\. 

## Error: User key not recognized by server<a name="TroubleshootingInstancesServerError"></a>

**If you use SSH to connect to your instance**
+ Use `ssh -vvv` to get triple verbose debugging information while connecting:

  ```
  ssh -vvv -i [your key name].pem ec2-user@[public DNS address of your instance].compute-1.amazonaws.com
  ```

  The following sample output demonstrates what you might see if you were trying to connect to your instance with a key that was not recognized by the server:

  ```
  open/ANT/myusername/.ssh/known_hosts).
  debug2: bits set: 504/1024
  debug1: ssh_rsa_verify: signature correct
  debug2: kex_derive_keys
  debug2: set_newkeys: mode 1
  debug1: SSH2_MSG_NEWKEYS sent
  debug1: expecting SSH2_MSG_NEWKEYS
  debug2: set_newkeys: mode 0
  debug1: SSH2_MSG_NEWKEYS received
  debug1: Roaming not allowed by server
  debug1: SSH2_MSG_SERVICE_REQUEST sent
  debug2: service_accept: ssh-userauth
  debug1: SSH2_MSG_SERVICE_ACCEPT received
  debug2: key: boguspem.pem ((nil))
  debug1: Authentications that can continue: publickey
  debug3: start over, passed a different list publickey
  debug3: preferred gssapi-keyex,gssapi-with-mic,publickey,keyboard-interactive,password
  debug3: authmethod_lookup publickey
  debug3: remaining preferred: keyboard-interactive,password
  debug3: authmethod_is_enabled publickey
  debug1: Next authentication method: publickey
  debug1: Trying private key: boguspem.pem
  debug1: read PEM private key done: type RSA
  debug3: sign_and_send_pubkey: RSA 9c:4c:bc:0c:d0:5c:c7:92:6c:8e:9b:16:e4:43:d8:b2
  debug2: we sent a publickey packet, wait for reply
  debug1: Authentications that can continue: publickey
  debug2: we did not send a packet, disable method
  debug1: No more authentication methods to try.
  Permission denied (publickey).
  ```

**If you use SSH \(MindTerm\) to connect to your instance**
+ If Java is not enabled, the server does not recognize the user key\. To enable Java, go to [How do I enable Java in my web browser?](http://java.com/en/download/help/enable_browser.xml) in the Java documentation\.

**If you use PuTTY to connect to your instance**
+ Verify that your private key \(\.pem\) file has been converted to the format recognized by PuTTY \(\.ppk\)\. For more information about converting your private key, see [Connecting to Your Linux Instance from Windows Using PuTTY](putty.md)\.
**Note**  
In PuTTYgen, load your private key file and select **Save Private Key** rather than **Generate**\. 
+ Verify that you are connecting with the appropriate user name for your AMI\. Enter the user name in the **Host name** box in the **PuTTY Configuration** window\.
  + For an Amazon Linux AMI, the user name is `ec2-user`\.
  + For a Centos AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin` or `root`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu` or `root`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.
+ Verify that you have an inbound security group rule to allow inbound traffic to the appropriate port\. For more information, see [Authorizing Network Access to Your Instances](authorizing-access-to-an-instance.md)\. 

## Error: Host key not found, Permission denied \(publickey\), *or* Authentication failed, permission denied<a name="TroubleshootingInstancesConnectingMindTerm"></a>

If you connect to your instance using SSH and get any of the following errors, `Host key not found in [directory]`, `Permission denied (publickey)`, or `Authentication failed, permission denied`, verify that you are connecting with the appropriate user name for your AMI *and* that you have specified the proper private key \(`.pem)` file for your instance\. For MindTerm clients, enter the user name in the **User name** box in the **Connect To Your Instance** window\.

The appropriate user names are as follows:
+ For an Amazon Linux AMI, the user name is `ec2-user`\.
+ For a Centos AMI, the user name is `centos`\.
+ For a Debian AMI, the user name is `admin` or `root`\.
+ For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu` or `root`\.
+ Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

For example, to use an SSH client to connect to an instance launched from an Amazon Linux AMI, use the following command:

```
ssh -i /path/my-key-pair.pem ec2-user@public-dns-hostname
```

Confirm that you are using the private key file that corresponds to the key pair that you selected when you launched the instance\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Select your instance\. In the **Description** tab, verify the value of **Key pair name**\.

1. If you did not specify a key pair when you launched the instance, you can terminate the instance and launch a new instance, ensuring that you specify a key pair\. If this is an instance that you have been using but you no longer have the `.pem` file for your key pair, you can replace the key pair with a new one\. For more information, see [Connecting to Your Linux Instance if You Lose Your Private Key](ec2-key-pairs.md#replacing-lost-key-pair)\.

If you generated your own key pair, ensure that your key generator is set up to create RSA keys\. DSA keys are not accepted\.

If you get a `Permission denied (publickey)` error and none of the above applies \(for example, you were able to connect previously\), the permissions on the home directory of your instance may have been changed\. Permissions for `/home/ec2-user/.ssh/authorized_keys` must be limited to the owner only\.

**To verify the permissions on your instance**

1. Stop your instance and detach the root volume\. For more information, see [Stop and Start Your Instance](Stop_Start.md) and [Detaching an Amazon EBS Volume from an Instance](ebs-detaching-volume.md)\.

1. Launch a temporary instance in the same Availability Zone as your current instance \(use a similar or the same AMI as you used for your current instance\), and attach the root volume to the temporary instance\. For more information, see [Attaching an Amazon EBS Volume to an Instance](ebs-attaching-volume.md)\.

1. Connect to the temporary instance, create a mount point, and mount the volume that you attached\. For more information, see [Making an Amazon EBS Volume Available for Use on Linux](ebs-using-volumes.md)\.

1. From the temporary instance, check the permissions of the `/home/ec2-user/` directory of the attached volume\. If necessary, adjust the permissions as follows:

   ```
   [ec2-user ~]$ chmod 600 mount_point/home/ec2-user/.ssh/authorized_keys
   ```

   ```
   [ec2-user ~]$ chmod 700 mount_point/home/ec2-user/.ssh
   ```

   ```
   [ec2-user ~]$ chmod 700 mount_point/home/ec2-user
   ```

1. Unmount the volume, detach it from the temporary instance, and re\-attach it to the original instance\. Ensure that you specify the correct device name for the root volume; for example, `/dev/xvda`\.

1. Start your instance\. If you no longer require the temporary instance, you can terminate it\.

## Error: Unprotected Private Key File<a name="troubleshoot-unprotected-key"></a>

Your private key file must be protected from read and write operations from any other users\. If your private key can be read or written to by anyone but you, then SSH ignores your key and you see the following warning message below\.

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0777 for '.ssh/my_private_key.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
bad permissions: ignore key: .ssh/my_private_key.pem
Permission denied (publickey).
```

If you see a similar message when you try to log in to your instance, examine the first line of the error message to verify that you are using the correct public key for your instance\. The above example uses the private key `.ssh/my_private_key.pem` with file permissions of `0777`, which allow anyone to read or write to this file\. This permission level is very insecure, and so SSH ignores this key\. To fix the error, execute the following command, substituting the path for your private key file\.

```
[ec2-user ~]$ chmod 0400 .ssh/my_private_key.pem
```

## Error: Server refused our key *or* No supported authentication methods available<a name="TroubleshootingInstancesConnectingPuTTY"></a>

If you use PuTTY to connect to your instance and get either of the following errors, `Error: Server refused our key` or `Error: No supported authentication methods available`, verify that you are connecting with the appropriate user name for your AMI\. Enter the user name in the **User name** box in the **PuTTY Configuration** window\.

The appropriate user names are as follows:
+ For an Amazon Linux AMI, the user name is `ec2-user`\.
+ For a Centos AMI, the user name is `centos`\.
+ For a Debian AMI, the user name is `admin` or `root`\.
+ For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu` or `root`\.
+ Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

You should also verify that your private key \(\.pem\) file has been correctly converted to the format recognized by PuTTY \(\.ppk\)\. For more information about converting your private key, see [Connecting to Your Linux Instance from Windows Using PuTTY](putty.md)\.

## Error Using MindTerm on Safari Browser<a name="troubleshoot-instance-connect-safari"></a>

If you use MindTerm to connect to your instance, and are using the Safari web browser, you may get the following error:

```
Error connecting to your_instance_ip, reason: 
 —> Key exchange failed: Host authentication failed
```

You must update the browser's security settings to allow the AWS Management Console to run the Java plugin in unsafe mode\.

**To enable the Java plugin to run in unsafe mode**

1. In Safari, keep the Amazon EC2 console open, and choose **Safari**, **Preferences**, **Security**\.

1. Choose **Plug\-in Settings** \(or **Manage Website Settings** on older versions of Safari\)\.

1. Choose the **Java** plugin on the left\. 

1. For **Currently Open Websites**, select the AWS Management Console URL and choose **Run in Unsafe Mode**\.

1. When prompted, choose **Trust** in the warning dialog box and choose **Done**\. 

## Error Using macOS RDP Client<a name="troubleshoot-instance-connect-mac-rdp"></a>

If you are connecting to a Windows Server 2012 R2 instance using the Remote Desktop Connection client from the Microsoft website, you may get the following error:

```
Remote Desktop Connection cannot verify the identity of the computer that you want to connect to.
```

Download the Microsoft Remote Desktop app from the Apple iTunes store, and use the app to connect to your instance\.

## Cannot Ping Instance<a name="troubleshoot-instance-ping"></a>

The `ping` command is a type of ICMP traffic — if you are unable to ping your instance, ensure that your inbound security group rules allow ICMP traffic for the `Echo Request` message from all sources, or from the computer or instance from which you are issuing the command\. If you are unable to issue a `ping` command from your instance, ensure that your outbound security group rules allow ICMP traffic for the `Echo Request` message to all destinations, or to the host that you are attempting to ping\.
# Troubleshooting connecting to your instance<a name="TroubleshootingInstancesConnecting"></a>

The following information can help you troubleshoot issues with connecting to your instance\. For additional help with Windows instances, see [Troubleshooting Windows Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-windows-instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Common causes for connection issues](#TroubleshootingInstancesCommonCauses)
+ [Error connecting to your instance: Connection timed out](#TroubleshootingInstancesConnectionTimeout)
+ [Error: unable to load key \.\.\. Expecting: ANY PRIVATE KEY](#troubleshoot-instance-connect-key-file)
+ [Error: User key not recognized by server](#TroubleshootingInstancesServerError)
+ [Error: Permission denied or connection closed by \[instance\] port 22](#TroubleshootingInstancesConnectingSSH)
+ [Error: Unprotected private key file](#troubleshoot-unprotected-key)
+ [Error: Private key must begin with "\-\-\-\-\-BEGIN RSA PRIVATE KEY\-\-\-\-\-" and end with "\-\-\-\-\-END RSA PRIVATE KEY\-\-\-\-\-"](#troubleshoot-private-key-file-format)
+ [Error: Server refused our key *or* No supported authentication methods available](#TroubleshootingInstancesConnectingPuTTY)
+ [Cannot ping instance](#troubleshoot-instance-ping)
+ [Error: Server unexpectedly closed network connection](#troubleshoot-ssh)

## Common causes for connection issues<a name="TroubleshootingInstancesCommonCauses"></a>

We recommend that you begin troubleshooting by checking some common causes for issues connecting to your instance\.

**Verify the user name for your instance**  
You can connect to your instance using the user name for your user account or the default user name for the AMI that you used to launch your instance\.  
+ **Get the user name for your user account\.**

  For more information about how to create a user account, see [Managing user accounts on your Amazon Linux instance](managing-users.md)\.
+ **Get the default user name for the AMI that you used to launch your instance:**
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a CentOS AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

**Verify that your security group rules allow traffic**  
Make sure your security group rules allow inbound traffic from your public IPv4 address on the proper port\. For steps to verify, see [Error connecting to your instance: Connection timed out](#TroubleshootingInstancesConnectionTimeout)

**Verify that your instance is ready**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check your instance to make sure it is running and has passed its status checks\.  

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. Verify the following:

   1. In the **Instance state** column, verify that your instance is in the `running` state\.

   1. In the **Status check** column, verify that your instance has passed the two status checks\.

**Verify the general prerequisites for connecting to your instance**  
For more information, see [General prerequisites for connecting to your instance](connection-prereqs.md)\.

## Error connecting to your instance: Connection timed out<a name="TroubleshootingInstancesConnectionTimeout"></a>

If you try to connect to your instance and get an error message `Network error: Connection timed out` or `Error connecting to [instance], reason: -> Connection timed out: connect`, try the following:

**Check your security group rules\.**  
You need a security group rule that allows inbound traffic from your public IPv4 address on the proper port\.

------
#### [ New console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Security** tab at the bottom of the console page, under **Inbound rules**, check the list of rules that are in effect for the selected instance\.
   + For Linux instances: Verify that there is a rule that allows traffic from your computer to port 22 \(SSH\)\.
   + For Windows instances: Verify that there is a rule that allows traffic from your computer to port 3389 \(RDP\)\.

1. Each time you restart your instance, a new IP address \(and host name\) will be assigned\. If your security group has a rule that allows inbound traffic from a single IP address, this address might not be static if your computer is on a corporate network or if you are connecting through an internet service provider \(ISP\)\. Instead, specify the range of IP addresses used by client computers\. If your security group does not have a rule that allows inbound traffic as described in the previous step, add a rule to your security group\. For more information, see [Authorizing inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.

   For more information about security group rules, see [Security group rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules) in the *Amazon VPC User Guide*\.

------
#### [ Old console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. In the **Description** tab at the bottom of the console page, next to **Security groups**, select **view inbound rules** to display the list of rules that are in effect for the selected instance\.

1. For Linux instances: When you select **view inbound rules**, a window will appear that displays the port\(s\) to which traffic is allowed\. Verify that there is a rule that allows traffic from your computer to port 22 \(SSH\)\.

   For Windows instances: When you select **view inbound rules**, a window will appear that displays the port\(s\) to which traffic is allowed\. Verify that there is a rule that allows traffic from your computer to port 3389 \(RDP\)\.

   Each time you restart your instance, a new IP address \(and host name\) will be assigned\. If your security group has a rule that allows inbound traffic from a single IP address, this address may not be static if your computer is on a corporate network or if you are connecting through an internet service provider \(ISP\)\. Instead, specify the range of IP addresses used by client computers\. If your security group does not have a rule that allows inbound traffic as described in the previous step, add a rule to your security group\. For more information, see [Authorizing inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.

   For more information about security group rules, see [Security group rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules) in the *Amazon VPC User Guide*\.

------

**Check the route table for the subnet\.**  
You need a route that sends all traffic destined outside the VPC to the internet gateway for the VPC\.

------
#### [ New console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Networking** tab, make note of the values for **VPC ID** and **Subnet ID**\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Internet Gateways**\. Verify that there is an internet gateway attached to your VPC\. Otherwise, choose **Create internet gateway**, enter a name for the internet gateway, and choose **Create internet gateway**\. Then, for the internet gateway you created, choose **Actions**, **Attach to VPC**, select your VPC, and then choose **Attach internet gateway** to attach it to your VPC\.

1. In the navigation pane, choose **Subnets**, and then select your subnet\.

1. On the **Route table** tab, verify that there is a route with `0.0.0.0/0` as the destination and the internet gateway for your VPC as the target\. If you're connecting to your instance using its IPv6 address, verify that there is a route for all IPv6 traffic \(`::/0`\) that points to the internet gateway\. Otherwise, do the following:

   1. Choose the ID of the route table \(rtb\-*xxxxxxxx*\) to navigate to the route table\.

   1. On the **Routes** tab, choose **Edit routes**\. Choose **Add route**, use `0.0.0.0/0` as the destination and the internet gateway as the target\. For IPv6, choose **Add route**, use `::/0` as the destination and the internet gateway as the target\.

   1. Choose **Save routes**\.

------
#### [ Old console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. In the **Description** tab, write down the values of **VPC ID** and **Subnet ID**\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Internet Gateways**\. Verify that there is an internet gateway attached to your VPC\. Otherwise, choose **Create Internet Gateway** to create an internet gateway\. Select the internet gateway, and then choose **Attach to VPC** and follow the directions to attach it to your VPC\.

1. In the navigation pane, choose **Subnets**, and then select your subnet\.

1. On the **Route Table** tab, verify that there is a route with `0.0.0.0/0` as the destination and the internet gateway for your VPC as the target\. If you're connecting to your instance using its IPv6 address, verify that there is a route for all IPv6 traffic \(`::/0`\) that points to the internet gateway\. Otherwise, do the following:

   1. Choose the ID of the route table \(rtb\-*xxxxxxxx*\) to navigate to the route table\.

   1. On the **Routes** tab, choose **Edit routes**\. Choose **Add route**, use `0.0.0.0/0` as the destination and the internet gateway as the target\. For IPv6, choose **Add route**, use `::/0` as the destination and the internet gateway as the target\.

   1. Choose **Save routes**\.

------

**Check the network access control list \(ACL\) for the subnet\.**

The network ACLs must allow inbound and outbound traffic from your local IP address on the proper port\. The default network ACL allows all inbound and outbound traffic\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets** and select your subnet\.

1. On the **Description** tab, find **Network ACL**, and choose its ID \(acl\-*xxxxxxxx*\)\.

1. Select the network ACL\. For **Inbound Rules**, verify that the rules allow traffic from your computer\. Otherwise, delete or modify the rule that is blocking traffic from your computer\.

1. For **Outbound Rules**, verify that the rules allow traffic to your computer\. Otherwise, delete or modify the rule that is blocking traffic to your computer\.

**If your computer is on a corporate network**  
Ask your network administrator whether the internal firewall allows inbound and outbound traffic from your computer on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\.

If you have a firewall on your computer, verify that it allows inbound and outbound traffic from your computer on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\.

**Check that your instance has a public IPv4 address\.**  
If not, you can associate an Elastic IP address with your instance\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. 

**Check the CPU load on your instance; the server may be overloaded\.**  
AWS automatically provides data such as Amazon CloudWatch metrics and instance status, which you can use to see how much CPU load is on your instance and, if necessary, adjust how your loads are handled\. For more information, see [Monitoring your instances using CloudWatch](using-cloudwatch.md)\.
+ If your load is variable, you can automatically scale your instances up or down using [Auto Scaling](https://aws.amazon.com/autoscaling/) and [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)\. 
+ If your load is steadily growing, you can move to a larger instance type\. For more information, see [Changing the instance type](ec2-instance-resize.md)\. 

**To connect to your instance using an IPv6 address, check the following:**
+ Your subnet must be associated with a route table that has a route for IPv6 traffic \(`::/0`\) to an internet gateway\. 
+ Your security group rules must allow inbound traffic from your local IPv6 address on the proper port \(22 for Linux and 3389 for Windows\)\.
+ Your network ACL rules must allow inbound and outbound IPv6 traffic\.
+ If you launched your instance from an older AMI, it might not be configured for DHCPv6 \(IPv6 addresses are not automatically recognized on the network interface\)\. For more information, see [Configure IPv6 on Your Instances](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-migrate-ipv6.html#vpc-migrate-ipv6-dhcpv6) in the *Amazon VPC User Guide*\.
+ Your local computer must have an IPv6 address, and must be configured to use IPv6\. 

## Error: unable to load key \.\.\. Expecting: ANY PRIVATE KEY<a name="troubleshoot-instance-connect-key-file"></a>

If you try to connect to your instance and get the error message, `unable to load key ... Expecting: ANY PRIVATE KEY`, the file in which the private key is stored is incorrectly configured\. If the private key file ends in `.pem`, it might still be incorrectly configured\. A possible cause for an incorrectly configured private key file is a missing certificate\.

**If the private key file is incorrectly configured, follow these steps to resolve the error**

1. Create a new key pair\. For more information, see [Option 1: Create a key pair using Amazon EC2](ec2-key-pairs.md#having-ec2-create-your-key-pair)\.

1. Add the new key pair to your instance\. For more information, see [Connecting to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\.

1. Connect to your instance using the new key pair\.

## Error: User key not recognized by server<a name="TroubleshootingInstancesServerError"></a>

**If you use SSH to connect to your instance**
+ Use `ssh -vvv` to get triple verbose debugging information while connecting:

  ```
  ssh -vvv -i path/my-key-pair.pem my-instance-user-name@ec2-203-0-113-25.compute-1.amazonaws.com
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

**If you use PuTTY to connect to your instance**
+ Verify that your private key \(\.pem\) file has been converted to the format recognized by PuTTY \(\.ppk\)\. For more information about converting your private key, see [Connecting to your Linux instance from Windows using PuTTY](putty.md)\.
**Note**  
In PuTTYgen, load your private key file and select **Save Private Key** rather than **Generate**\. 
+ Verify that you are connecting with the appropriate user name for your AMI\. Enter the user name in the **Host name** box in the **PuTTY Configuration** window\.
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a CentOS AMI, the user name is `centos`\.
  + For a Debian AMI, the user name is `admin`\.
  + For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.
+ Verify that you have an inbound security group rule to allow inbound traffic to the appropriate port\. For more information, see [Authorizing Network Access to Your Instances](authorizing-access-to-an-instance.md)\. 

## Error: Permission denied or connection closed by \[instance\] port 22<a name="TroubleshootingInstancesConnectingSSH"></a>

If you connect to your instance using SSH and get any of the following errors, `Host key not found in [directory]`, `Permission denied (publickey)`, `Authentication failed, permission denied`, or `Connection closed by [instance] port 22`, verify that you are connecting with the appropriate user name for your AMI *and* that you have specified the proper private key \(`.pem)` file for your instance\.

The appropriate user names are as follows:
+ For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
+ For a CentOS AMI, the user name is `centos`\.
+ For a Debian AMI, the user name is `admin`\.
+ For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu`\.
+ Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

For example, to use an SSH client to connect to an Amazon Linux instance, use the following command:

```
ssh -i /path/my-key-pair.pem my-instance-user-name@ec2-203-0-113-25.compute-1.amazonaws.com
```

Confirm that you are using the private key file that corresponds to the key pair that you selected when you launched the instance\.

------
#### [ New console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select your instance\.

1. On the **Details** tab, under **Instance details**, verify the value of **Key pair name**\.

1. If you did not specify a key pair when you launched the instance, you can terminate the instance and launch a new instance, ensuring that you specify a key pair\. If this is an instance that you have been using but you no longer have the `.pem` file for your key pair, you can replace the key pair with a new one\. For more information, see [Connecting to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\.

------
#### [ Old console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select your instance\.

1. In the **Description** tab, verify the value of **Key pair name**\.

1. If you did not specify a key pair when you launched the instance, you can terminate the instance and launch a new instance, ensuring that you specify a key pair\. If this is an instance that you have been using but you no longer have the `.pem` file for your key pair, you can replace the key pair with a new one\. For more information, see [Connecting to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\.

------

If you generated your own key pair, ensure that your key generator is set up to create RSA keys\. DSA keys are not accepted\.

If you get a `Permission denied (publickey)` error and none of the above applies \(for example, you were able to connect previously\), the permissions on the home directory of your instance may have been changed\. Permissions for `/home/my-instance-user-name/.ssh/authorized_keys` must be limited to the owner only\.

**To verify the permissions on your instance**

1. Stop your instance and detach the root volume\. For more information, see [Stop and start your instance](Stop_Start.md) and [Detaching an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\.

1. Launch a temporary instance in the same Availability Zone as your current instance \(use a similar or the same AMI as you used for your current instance\), and attach the root volume to the temporary instance\. For more information, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

1. Connect to the temporary instance, create a mount point, and mount the volume that you attached\. For more information, see [Making an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.

1. From the temporary instance, check the permissions of the `/home/my-instance-user-name/` directory of the attached volume\. If necessary, adjust the permissions as follows:

   ```
   [ec2-user ~]$ chmod 600 mount_point/home/my-instance-user-name/.ssh/authorized_keys
   ```

   ```
   [ec2-user ~]$ chmod 700 mount_point/home/my-instance-user-name/.ssh
   ```

   ```
   [ec2-user ~]$ chmod 700 mount_point/home/my-instance-user-name
   ```

1. Unmount the volume, detach it from the temporary instance, and re\-attach it to the original instance\. Ensure that you specify the correct device name for the root volume; for example, `/dev/xvda`\.

1. Start your instance\. If you no longer require the temporary instance, you can terminate it\.

## Error: Unprotected private key file<a name="troubleshoot-unprotected-key"></a>

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

## Error: Private key must begin with "\-\-\-\-\-BEGIN RSA PRIVATE KEY\-\-\-\-\-" and end with "\-\-\-\-\-END RSA PRIVATE KEY\-\-\-\-\-"<a name="troubleshoot-private-key-file-format"></a>

If you use a third\-party tool, such as ssh\-keygen, to create an RSA key pair, it generates the private key in the OpenSSH key format\. When you connect to your instance, if you use the private key in the OpenSSH format to decrypt the password, you'll get the error `Private key must begin with "-----BEGIN RSA PRIVATE KEY-----" and end with "-----END RSA PRIVATE KEY-----"`\.

To resolve the error, the private key must be in the PEM format\. Use the following command to create the private key in the PEM format:

```
ssh-keygen -m PEM
```

## Error: Server refused our key *or* No supported authentication methods available<a name="TroubleshootingInstancesConnectingPuTTY"></a>

If you use PuTTY to connect to your instance and get either of the following errors, Error: Server refused our key or Error: No supported authentication methods available, verify that you are connecting with the appropriate user name for your AMI\. Type the user name in **User name** in the **PuTTY Configuration** window\.

The appropriate user names are as follows:
+ For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
+ For a CentOS AMI, the user name is `centos`\.
+ For a Debian AMI, the user name is `admin`\.
+ For a Fedora AMI, the user name is `ec2-user` or `fedora`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu`\.
+ Otherwise, if `ec2-user` and `root` don't work, check with the AMI provider\.

You should also verify that your private key \(\.pem\) file has been correctly converted to the format recognized by PuTTY \(\.ppk\)\. For more information about converting your private key, see [Connecting to your Linux instance from Windows using PuTTY](putty.md)\.

## Cannot ping instance<a name="troubleshoot-instance-ping"></a>

The `ping` command is a type of ICMP traffic — if you are unable to ping your instance, ensure that your inbound security group rules allow ICMP traffic for the `Echo Request` message from all sources, or from the computer or instance from which you are issuing the command\.

If you are unable to issue a `ping` command from your instance, ensure that your outbound security group rules allow ICMP traffic for the `Echo Request` message to all destinations, or to the host that you are attempting to ping\.

`Ping` commands can also be blocked by a firewall or time out due to network latency or hardware issues\. You should consult your local network or system administrator for help with further troubleshooting\.

## Error: Server unexpectedly closed network connection<a name="troubleshoot-ssh"></a>

If you are connecting to your instance with PuTTY and you receive the error "Server unexpectedly closed network connection," verify that you have enabled keepalives on the Connection page of the PuTTY Configuration to avoid being disconnected\. Some servers disconnect clients when they do not receive any data within a specified period of time\. Set the Seconds between keepalives to 59 seconds\. 

If you still experience issues after enabling keepalives, try to disable Nagle's algorithm on the Connection page of the PuTTY Configuration\. 
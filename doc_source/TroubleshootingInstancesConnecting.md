# Troubleshoot connecting to your instance<a name="TroubleshootingInstancesConnecting"></a>

The following information can help you troubleshoot issues with connecting to your instance\. For additional help with Windows instances, see [Troubleshoot Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/troubleshooting-windows-instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

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
+ [Error: Host key validation failed for EC2 Instance Connect](#troubleshoot-host-key-validation)
+ [I've lost my private key\. How can I connect to my Linux instance?](#replacing-lost-key-pair)

## Common causes for connection issues<a name="TroubleshootingInstancesCommonCauses"></a>

We recommend that you begin troubleshooting by checking some common causes for issues connecting to your instance\.

**Verify the user name for your instance**  
You can connect to your instance using the user name for your user account or the default user name for the AMI that you used to launch your instance\.  
+ **Get the user name for your user account\.**

  For more information about how to create a user account, see [Manage user accounts on your Amazon Linux instance](managing-users.md)\.
+ **Get the default user name for the AMI that you used to launch your instance:**
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a CentOS AMI, the user name is `centos` or `ec2-user`\.
  + For a Debian AMI, the user name is `admin`\.
  + For a Fedora AMI, the user name is `fedora` or `ec2-user`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + For an Oracle AMI, the user name is `ec2-user`\.
  + For a Bitnami AMI, the user name is `bitnami`\.
  + Otherwise, check with the AMI provider\.

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

1. Each time you restart your instance, a new IP address \(and host name\) will be assigned\. If your security group has a rule that allows inbound traffic from a single IP address, this address might not be static if your computer is on a corporate network or if you are connecting through an internet service provider \(ISP\)\. Instead, specify the range of IP addresses used by client computers\. If your security group does not have a rule that allows inbound traffic as described in the previous step, add a rule to your security group\. For more information, see [Authorize inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.

   For more information about security group rules, see [Security group rules](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#SecurityGroupRules) in the *Amazon VPC User Guide*\.

------
#### [ Old console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. In the **Description** tab at the bottom of the console page, next to **Security groups**, select **view inbound rules** to display the list of rules that are in effect for the selected instance\.

1. For Linux instances: When you select **view inbound rules**, a window will appear that displays the port\(s\) to which traffic is allowed\. Verify that there is a rule that allows traffic from your computer to port 22 \(SSH\)\.

   For Windows instances: When you select **view inbound rules**, a window will appear that displays the port\(s\) to which traffic is allowed\. Verify that there is a rule that allows traffic from your computer to port 3389 \(RDP\)\.

   Each time you restart your instance, a new IP address \(and host name\) will be assigned\. If your security group has a rule that allows inbound traffic from a single IP address, this address may not be static if your computer is on a corporate network or if you are connecting through an internet service provider \(ISP\)\. Instead, specify the range of IP addresses used by client computers\. If your security group does not have a rule that allows inbound traffic as described in the previous step, add a rule to your security group\. For more information, see [Authorize inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.

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

The network ACLs must allow inbound traffic from your local IP address on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\. It must also allow outbound traffic to the ephemeral ports \(1024\-65535\)\.

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Subnets**\.

1. Select your subnet\.

1. On the **Network ACL** tab, for **Inbound rules**, verify that the rules allow inbound traffic from your computer on the required port\. Otherwise, delete or modify the rule that is blocking the traffic\.

1. For **Outbound rules**, verify that the rules allow outbound traffic to your computer on the ephemeral ports\. Otherwise, delete or modify the rule that is blocking the traffic\.

**If your computer is on a corporate network**  
Ask your network administrator whether the internal firewall allows inbound and outbound traffic from your computer on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\.

If you have a firewall on your computer, verify that it allows inbound and outbound traffic from your computer on port 22 \(for Linux instances\) or port 3389 \(for Windows instances\)\.

**Check that your instance has a public IPv4 address\.**  
If not, you can associate an Elastic IP address with your instance\. For more information, see [Elastic IP addresses](elastic-ip-addresses-eip.md)\. 

**Check the CPU load on your instance; the server may be overloaded\.**  
AWS automatically provides data such as Amazon CloudWatch metrics and instance status, which you can use to see how much CPU load is on your instance and, if necessary, adjust how your loads are handled\. For more information, see [Monitor your instances using CloudWatch](using-cloudwatch.md)\.
+ If your load is variable, you can automatically scale your instances up or down using [Auto Scaling](https://aws.amazon.com/autoscaling/) and [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)\. 
+ If your load is steadily growing, you can move to a larger instance type\. For more information, see [Change the instance type](ec2-instance-resize.md)\. 

**To connect to your instance using an IPv6 address, check the following:**
+ Your subnet must be associated with a route table that has a route for IPv6 traffic \(`::/0`\) to an internet gateway\. 
+ Your security group rules must allow inbound traffic from your local IPv6 address on the proper port \(22 for Linux and 3389 for Windows\)\.
+ Your network ACL rules must allow inbound and outbound IPv6 traffic\.
+ If you launched your instance from an older AMI, it might not be configured for DHCPv6 \(IPv6 addresses are not automatically recognized on the network interface\)\. For more information, see [Configure IPv6 on Your Instances](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-migrate-ipv6.html#vpc-migrate-ipv6-dhcpv6) in the *Amazon VPC User Guide*\.
+ Your local computer must have an IPv6 address, and must be configured to use IPv6\. 

## Error: unable to load key \.\.\. Expecting: ANY PRIVATE KEY<a name="troubleshoot-instance-connect-key-file"></a>

If you try to connect to your instance and get the error message, `unable to load key ... Expecting: ANY PRIVATE KEY`, the file in which the private key is stored is incorrectly configured\. If the private key file ends in `.pem`, it might still be incorrectly configured\. A possible cause for an incorrectly configured private key file is a missing certificate\.

**If the private key file is incorrectly configured, follow these steps to resolve the error**

1. Create a new key pair\. For more information, see [Create a key pair using Amazon EC2](create-key-pairs.md#having-ec2-create-your-key-pair)\.
**Note**  
Alternatively, you can create a new key pair using a third\-party tool\. For more information, see [Create a key pair using a third\-party tool and import the public key to Amazon EC2](create-key-pairs.md#how-to-generate-your-own-key-and-import-it-to-aws)\.

1. Add the new key pair to your instance\. For more information, see [I've lost my private key\. How can I connect to my Linux instance?](#replacing-lost-key-pair)\.

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
+ Verify that your private key \(\.pem\) file has been converted to the format recognized by PuTTY \(\.ppk\)\. For more information about converting your private key, see [Connect to your Linux instance from Windows using PuTTY](putty.md)\.
**Note**  
In PuTTYgen, load your private key file and select **Save Private Key** rather than **Generate**\. 
+ Verify that you are connecting with the appropriate user name for your AMI\. Enter the user name in the **Host name** box in the **PuTTY Configuration** window\.
  + For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
  + For a CentOS AMI, the user name is `centos` or `ec2-user`\.
  + For a Debian AMI, the user name is `admin`\.
  + For a Fedora AMI, the user name is `fedora` or `ec2-user`\.
  + For a RHEL AMI, the user name is `ec2-user` or `root`\.
  + For a SUSE AMI, the user name is `ec2-user` or `root`\.
  + For an Ubuntu AMI, the user name is `ubuntu`\.
  + For an Oracle AMI, the user name is `ec2-user`\.
  + For a Bitnami AMI, the user name is `bitnami`\.
  + Otherwise, check with the AMI provider\.
+ Verify that you have an inbound security group rule to allow inbound traffic to the appropriate port\. For more information, see [Authorizing Network Access to Your Instances](authorizing-access-to-an-instance.md)\. 

## Error: Permission denied or connection closed by \[instance\] port 22<a name="TroubleshootingInstancesConnectingSSH"></a>

If you connect to your instance using SSH and get any of the following errors, `Host key not found in [directory]`, `Permission denied (publickey)`, `Authentication failed, permission denied`, or `Connection closed by [instance] port 22`, verify that you are connecting with the appropriate user name for your AMI *and* that you have specified the proper private key \(`.pem)` file for your instance\.

The appropriate user names are as follows:
+ For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\.
+ For a CentOS AMI, the user name is `centos` or `ec2-user`\.
+ For a Debian AMI, the user name is `admin`\.
+ For a Fedora AMI, the user name is `fedora` or `ec2-user`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu`\.
+ For an Oracle AMI, the user name is `ec2-user`\.
+ For a Bitnami AMI, the user name is `bitnami`\.
+ Otherwise, check with the AMI provider\.

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

1. If you did not specify a key pair when you launched the instance, you can terminate the instance and launch a new instance, ensuring that you specify a key pair\. If this is an instance that you have been using but you no longer have the `.pem` file for your key pair, you can replace the key pair with a new one\. For more information, see [I've lost my private key\. How can I connect to my Linux instance?](#replacing-lost-key-pair)\.

------
#### [ Old console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and then select your instance\.

1. In the **Description** tab, verify the value of **Key pair name**\.

1. If you did not specify a key pair when you launched the instance, you can terminate the instance and launch a new instance, ensuring that you specify a key pair\. If this is an instance that you have been using but you no longer have the `.pem` file for your key pair, you can replace the key pair with a new one\. For more information, see [I've lost my private key\. How can I connect to my Linux instance?](#replacing-lost-key-pair)\.

------

If you generated your own key pair, ensure that your key generator is set up to create RSA keys\. DSA keys are not accepted\.

If you get a `Permission denied (publickey)` error and none of the above applies \(for example, you were able to connect previously\), the permissions on the home directory of your instance may have been changed\. Permissions for `/home/my-instance-user-name/.ssh/authorized_keys` must be limited to the owner only\.

**To verify the permissions on your instance**

1. Stop your instance and detach the root volume\. For more information, see [Stop and start your instance](Stop_Start.md) and [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\.

1. Launch a temporary instance in the same Availability Zone as your current instance \(use a similar or the same AMI as you used for your current instance\), and attach the root volume to the temporary instance\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

1. Connect to the temporary instance, create a mount point, and mount the volume that you attached\. For more information, see [Make an Amazon EBS volume available for use on Linux](ebs-using-volumes.md)\.

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

If you see a similar message when you try to log in to your instance, examine the first line of the error message to verify that you are using the correct public key for your instance\. The above example uses the private key `.ssh/my_private_key.pem` with file permissions of `0777`, which allow anyone to read or write to this file\. This permission level is very insecure, and so SSH ignores this key\. 

If you are connecting from MacOS or Linux, run the following command to fix this error, substituting the path for your private key file\.

```
[ec2-user ~]$ chmod 0400 .ssh/my_private_key.pem
```

If you are connecting from Windows, perform the following steps on your local computer\.

1. Navigate to your \.pem file\.

1. Right\-click on the \.pem file and select **Properties**\.

1. Choose the **Security** tab\.

1. Select **Advanced**\.

1. Verify that you are the owner of the file\. If not, change the owner to your username\.

1. Select **Disable inheritance** and **Remove all inherited permissions from this object**\.

1. Select **Add**, **Select a principal**, enter your username, and select **OK**\.

1. From the **Permission Entry** window, grant **Read** permissions and select **OK**\.

1. Click **Apply** to ensure all settings are saved\.

1. Select **OK** to close the **Advanced Security Settings** window\.

1. Select **OK** to close the **Properties** window\.

1. You should be able to connect to your Linux instance from Windows via SSH\.

From a Windows command prompt, run the following commands\.

1. From the command prompt, navigate to the file path location of your \.pem file\.

1. Run the following command to reset and remove explicit permissions:

   ```
   icacls.exe $path /reset
   ```

1. Run the following command to grant Read permissions to the current user:

   ```
   icacls.exe $path /GRANT:R "$($env:USERNAME):(R)"
   ```

1. Run the following command to disable inheritance and remove inherited permissions\.

   ```
   icacls.exe $path /inheritance:r
   ```

1. You should be able to connect to your Linux instance from Windows via SSH\.

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
+ For a CentOS AMI, the user name is `centos` or `ec2-user`\.
+ For a Debian AMI, the user name is `admin`\.
+ For a Fedora AMI, the user name is `fedora` or `ec2-user`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu`\.
+ For an Oracle AMI, the user name is `ec2-user`\.
+ For a Bitnami AMI, the user name is `bitnami`\.
+ Otherwise, check with the AMI provider\.

You should also verify that your private key \(\.pem\) file has been correctly converted to the format recognized by PuTTY \(\.ppk\)\. For more information about converting your private key, see [Connect to your Linux instance from Windows using PuTTY](putty.md)\.

## Cannot ping instance<a name="troubleshoot-instance-ping"></a>

The `ping` command is a type of ICMP traffic — if you are unable to ping your instance, ensure that your inbound security group rules allow ICMP traffic for the `Echo Request` message from all sources, or from the computer or instance from which you are issuing the command\.

If you are unable to issue a `ping` command from your instance, ensure that your outbound security group rules allow ICMP traffic for the `Echo Request` message to all destinations, or to the host that you are attempting to ping\.

`Ping` commands can also be blocked by a firewall or time out due to network latency or hardware issues\. You should consult your local network or system administrator for help with further troubleshooting\.

## Error: Server unexpectedly closed network connection<a name="troubleshoot-ssh"></a>

If you are connecting to your instance with PuTTY and you receive the error "Server unexpectedly closed network connection," verify that you have enabled keepalives on the Connection page of the PuTTY Configuration to avoid being disconnected\. Some servers disconnect clients when they do not receive any data within a specified period of time\. Set the Seconds between keepalives to 59 seconds\. 

If you still experience issues after enabling keepalives, try to disable Nagle's algorithm on the Connection page of the PuTTY Configuration\. 

## Error: Host key validation failed for EC2 Instance Connect<a name="troubleshoot-host-key-validation"></a>

If you rotate your instance host keys, the new host keys are not automatically uploaded to the AWS trusted host keys database\. This causes host key validation to fail when you try to connect to your instance using the EC2 Instance Connect browser\-based client, and you're unable to connect to your instance\.

To resolve the error, you must run the `eic_harvest_hostkeys` script on your instance, which uploads your new host key to EC2 Instance Connect\. The script is located at `/opt/aws/bin/` on Amazon Linux 2 instances, and at `/usr/share/ec2-instance-connect/` on Ubuntu instances\.

------
#### [ Amazon Linux 2 ]

**To resolve the host key validation failed error on an Amazon Linux 2 instance**

1. Connect to your instance using SSH\.

   You can connect by using the EC2 Instance Connect CLI or by using the SSH key pair that was assigned to your instance when you launched it and the default user name of the AMI that you used to launch your instance\. For Amazon Linux 2, the default user name is `ec2-user`\.

   For example, if your instance was launched using Amazon Linux 2, your instance's public DNS name is `ec2-a-b-c-d.us-west-2.compute.amazonaws.com`, and the key pair is `my_ec2_private_key.pem`, use the following command to SSH into your instance:

   ```
   $ ssh -i my_ec2_private_key.pem ec2-user@ec2-a-b-c-d.us-west-2.compute.amazonaws.com
   ```

   For more information about connecting to your instance, see [Connect to your Linux instance using SSH](AccessingInstancesLinux.md)\.

1. Navigate to the following folder\.

   ```
   [ec2-user ~]$ cd /opt/aws/bin/
   ```

1. Run the following command on your instance\. 

   ```
   [ec2-user ~]$ ./eic_harvest_hostkeys
   ```

   Note that a successful call results in no output\.

   You can now use the EC2 Instance Connect browser\-based client to connect to your instance\.

------
#### [ Ubuntu ]

**To resolve the host key validation failed error on an Ubuntu instance**

1. Connect to your instance using SSH\.

   You can connect by using the EC2 Instance Connect CLI or by using the SSH key pair that was assigned to your instance when you launched it and the default user name of the AMI that you used to launch your instance\. For Ubuntu, the default user name is `ubuntu`\.

   For example, if your instance was launched using Ubuntu, your instance's public DNS name is `ec2-a-b-c-d.us-west-2.compute.amazonaws.com`, and the key pair is `my_ec2_private_key.pem`, use the following command to SSH into your instance:

   ```
   $ ssh -i my_ec2_private_key.pem ubuntu@ec2-a-b-c-d.us-west-2.compute.amazonaws.com
   ```

   For more information about connecting to your instance, see [Connect to your Linux instance using SSH](AccessingInstancesLinux.md)\.

1. Navigate to the following folder\.

   ```
   [ec2-user ~]$ cd /usr/share/ec2-instance-connect/
   ```

1. Run the following command on your instance\. 

   ```
   [ec2-user ~]$ ./eic_harvest_hostkeys
   ```

   Note that a successful call results in no output\.

   You can now use the EC2 Instance Connect browser\-based client to connect to your instance\.

------

## I've lost my private key\. How can I connect to my Linux instance?<a name="replacing-lost-key-pair"></a>

If you lose the private key for an EBS\-backed instance, you can regain access to your instance\. You must stop the instance, detach its root volume and attach it to another instance as a data volume, modify the `authorized_keys` file with a new public key, move the volume back to the original instance, and restart the instance\. For more information about launching, connecting to, and stopping instances, see [Instance lifecycle](ec2-instance-lifecycle.md)\.

This procedure is only supported for instances with EBS root volumes\. If the root device is an instance store volume, you cannot use this procedure to regain access to your instance; you must have the private key to connect to the instance\. To determine the root device type of your instance, open the Amazon EC2 console, choose **Instances**, select the instance, and check the value of **Root device type** in the **Description** tab in the Old console, or the **Details** tab in the New console\. The value is either `ebs` or `instance store`\.

In addition to the following steps, there are other ways to connect to your Linux instance if you lose your private key\. For more information, see [How can I connect to my Amazon EC2 instance if I lost my SSH key pair after its initial launch?](http://aws.amazon.com/premiumsupport/knowledge-center/user-data-replace-key-pair-ec2/)

**Topics**
+ [Step 1: Create a new key pair](#step-1-create-new-key-pair)
+ [Step 2: Get information about the original instance and its root volume](#step-2-get-info-about-original-instance)
+ [Step 3: Stop the original instance](#step-3-stop-original-instance)
+ [Step 4: Launch a temporary instance](#step-4-launch-temp-instance)
+ [Step 5: Detach the root volume from the original instance and attach it to the temporary instance](#step-5-detach-root-volume-and-attach-to-temp-instance)
+ [Step 6: Add the new public key to `authorized_keys` on the original volume mounted to the temporary instance](#step-6-add-new-public-key-to-authorized_keys)
+ [Step 7: Unmount and detach the original volume from the temporary instance, and reattach it to the original instance](#step-7-unmount-detach-volume-and-reattach-to-original-instance)
+ [Step 8: Connect to the original instance using the new key pair](#step-8-connect-to-original-instance)
+ [Step 9: Clean up](#step-9-clean-up)

### Step 1: Create a new key pair<a name="step-1-create-new-key-pair"></a>

Create a new key pair using either the Amazon EC2 console or a third\-party tool\. If you want to name your new key pair exactly the same as the lost private key, you must first delete the existing key pair\. For information about creating a new key pair, see [Create a key pair using Amazon EC2](create-key-pairs.md#having-ec2-create-your-key-pair) or [Create a key pair using a third\-party tool and import the public key to Amazon EC2](create-key-pairs.md#how-to-generate-your-own-key-and-import-it-to-aws)\.

### Step 2: Get information about the original instance and its root volume<a name="step-2-get-info-about-original-instance"></a>

Make note of the following information because you'll need it to complete this procedure\.

**To get information about your original instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances** in the navigation pane, and then select the instance that you'd like to connect to\. \(We'll refer to this as the *original* instance\.\)

1. On the **Details** tab, make note of the instance ID and AMI ID\.

1. On the **Networking** tab, make note of the Availability Zone\.

1. On the **Storage** tab, under **Root device name**, make note of the device name for the root volume \(for example, `/dev/xvda`\)\. Then, under **Block devices**, find this device name and make note of the volume ID \(for example, vol\-0a1234b5678c910de\)\.

### Step 3: Stop the original instance<a name="step-3-stop-original-instance"></a>

Choose **Instance state**, **Stop instance**\. If this option is disabled, either the instance is already stopped or its root device is an instance store volume\.

**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

### Step 4: Launch a temporary instance<a name="step-4-launch-temp-instance"></a>

------
#### [ New console ]

**To launch a temporary instance**

1. In the navigation panel, choose **Instances**, and then choose **Launch instances**\.

1. In the **Name and tags** section, for **Name**, enter **Temporary**\.

1. In the **Application and OS Images** section, select the same AMI that you used to launch the original instance\. If this AMI is unavailable, you can create an AMI that you can use from the stopped instance\. For more information, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md) \.

1. In the **Instance type** section, keep the default instance type\.

1. In the **Key pair** section, for **Key pair name**, select the existing key pair to use or create a new one\.

1. In the **Network settings** section, choose **Edit**, and then for **Subnet**, select a subnet in the same Availability Zone as the original instance\.

1. In the **Summary** panel, choose **Launch**\.

------
#### [ Old console ]

Choose **Launch instances**, and then use the launch wizard to launch a *temporary* instance with the following options:
+ On the **Choose an AMI** page, select the same AMI that you used to launch the original instance\. If this AMI is unavailable, you can create an AMI that you can use from the stopped instance\. For more information, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.
+ On the **Choose an Instance Type** page, leave the default instance type that the wizard selects for you\.
+ On the **Configure Instance Details** page, specify the same Availability Zone as the original instance\. If you're launching an instance in a VPC, select a subnet in this Availability Zone\.
+ On the **Add Tags** page, add the tag `Name=Temporary` to the instance to indicate that this is a temporary instance\.
+ On the **Review** page, choose **Launch**\. Choose the key pair that you created in Step 1, then choose **Launch Instances**\.

------

### Step 5: Detach the root volume from the original instance and attach it to the temporary instance<a name="step-5-detach-root-volume-and-attach-to-temp-instance"></a>

1. In the navigation pane, choose **Volumes** and select the root device volume for the original instance \(you made note of its volume ID in a previous step\)\. Choose **Actions**, **Detach Volume**, and then select **Yes, Detach**\. Wait for the state of the volume to become `available`\. \(You might need to choose the **Refresh** icon\.\)

1. With the volume still selected, choose **Actions**, and then select **Attach Volume**\. Select the instance ID of the temporary instance, make note of the device name specified under **Device** \(for example, `/dev/sdf`\), and then choose **Attach**\.
**Note**  
If you launched your original instance from an AWS Marketplace AMI and your volume contains AWS Marketplace codes, you must first stop the temporary instance before you can attach the volume\.

### Step 6: Add the new public key to `authorized_keys` on the original volume mounted to the temporary instance<a name="step-6-add-new-public-key-to-authorized_keys"></a>

1. Connect to the temporary instance\.

1. From the temporary instance, mount the volume that you attached to the instance so that you can access its file system\. For example, if the device name is `/dev/sdf`, use the following commands to mount the volume as `/mnt/tempvol`\.<a name="device-name"></a>
**Note**  
The device name might appear differently on your instance\. For example, devices mounted as `/dev/sdf` might show up as `/dev/xvdf` on the instance\. Some versions of Red Hat \(or its variants, such as CentOS\) might even increment the trailing letter by 4 characters, where `/dev/sdf` becomes `/dev/xvdk`\.

   1. Use the lsblk command to determine if the volume is partitioned\.

      ```
      [ec2-user ~]$ lsblk
      NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      xvda    202:0    0    8G  0 disk
      └─xvda1 202:1    0    8G  0 part /
      xvdf    202:80   0  101G  0 disk
      └─xvdf1 202:81   0  101G  0 part
      xvdg    202:96   0   30G  0 disk
      ```

      In the preceding example, `/dev/xvda` and `/dev/xvdf` are partitioned volumes, and `/dev/xvdg` is not\. If your volume is partitioned, you mount the partition \(`/dev/xvdf1)` instead of the raw device \(`/dev/xvdf`\) in the next steps\.

   1. Create a temporary directory to mount the volume\.

      ```
      [ec2-user ~]$ sudo mkdir /mnt/tempvol
      ```

   1. Mount the volume \(or partition\) at the temporary mount point, using the volume name or device name that you identified earlier\. The required command depends on your operating system's file system\. Note that the device name might appear differently on your instance\. See [note](#device-name) in this section for more information\.
      + Amazon Linux, Ubuntu, and Debian

        ```
        [ec2-user ~]$ sudo mount /dev/xvdf1 /mnt/tempvol
        ```
      + Amazon Linux 2, CentOS, SUSE Linux 12, and RHEL 7\.x

        ```
        [ec2-user ~]$ sudo mount -o nouuid /dev/xvdf1 /mnt/tempvol
        ```
**Note**  
If you get an error stating that the file system is corrupt, run the following command to use the **fsck** utility to check the file system and repair any issues:  

   ```
   [ec2-user ~]$ sudo fsck /dev/xvdf1
   ```

1. From the temporary instance, use the following command to update `authorized_keys` on the mounted volume with the new public key from the `authorized_keys` for the temporary instance\.
**Important**  
The following examples use the Amazon Linux user name `ec2-user`\. You might need to substitute a different user name, such as `ubuntu` for Ubuntu instances\.

   ```
   [ec2-user ~]$ cp .ssh/authorized_keys /mnt/tempvol/home/ec2-user/.ssh/authorized_keys
   ```

   If this copy succeeded, you can go to the next step\.

   \(Optional\) Otherwise, if you don't have permission to edit files in `/mnt/tempvol`, you must update the file using sudo and then check the permissions on the file to verify that you are able to log into the original instance\. Use the following command to check the permissions on the file\.

   ```
   [ec2-user ~]$ sudo ls -l /mnt/tempvol/home/ec2-user/.ssh
   total 4
   -rw------- 1 222 500 398 Sep 13 22:54 authorized_keys
   ```

   In this example output, *222* is the user ID and *500* is the group ID\. Next, use sudo to re\-run the copy command that failed\.

   ```
   [ec2-user ~]$ sudo cp .ssh/authorized_keys /mnt/tempvol/home/ec2-user/.ssh/authorized_keys
   ```

   Run the following command again to determine whether the permissions changed\.

   ```
   [ec2-user ~]$ sudo ls -l /mnt/tempvol/home/ec2-user/.ssh
   ```

   If the user ID and group ID have changed, use the following command to restore them\.

   ```
   [ec2-user ~]$ sudo chown 222:500 /mnt/tempvol/home/ec2-user/.ssh/authorized_keys
   ```

### Step 7: Unmount and detach the original volume from the temporary instance, and reattach it to the original instance<a name="step-7-unmount-detach-volume-and-reattach-to-original-instance"></a>

1. From the temporary instance, unmount the volume that you attached so that you can reattach it to the original instance\. For example, use the following command to unmount the volume at `/mnt/tempvol`\.

   ```
   [ec2-user ~]$ sudo umount /mnt/tempvol
   ```

1. Detach the volume from the temporary instance \(you unmounted it in the previous step\): From the Amazon EC2 console, select the root device volume for the original instance \(you made note of the volume ID in a previous step\), choose **Actions**, **Detach Volume**, and then choose **Yes, Detach**\. Wait for the state of the volume to become `available`\. \(You might need to choose the **Refresh** icon\.\)

1. Reattach the volume to the original instance: With the volume still selected, choose **Actions**, **Attach Volume**\. Select the instance ID of the original instance, specify the device name that you noted earlier in [Step 2](#step-2-get-info-about-original-instance) for the original root device attachment \(`/dev/sda1` or `/dev/xvda`\), and then choose **Attach**\.
**Important**  
If you don't specify the same device name as the original attachment, you cannot start the original instance\. Amazon EC2 expects the root device volume at `sda1` or `/dev/xvda`\.

### Step 8: Connect to the original instance using the new key pair<a name="step-8-connect-to-original-instance"></a>

Select the original instance, choose **Instance state**, **Start instance**\. After the instance enters the `running` state, you can connect to it using the private key file for your new key pair\.

**Note**  
If the name of your new key pair and corresponding private key file is different from the name of the original key pair, ensure that you specify the name of the new private key file when you connect to your instance\.

### Step 9: Clean up<a name="step-9-clean-up"></a>

\(Optional\) You can terminate the temporary instance if you have no further use for it\. Select the temporary instance, and choose **Instance state**, **Terminate instance**\.
# Connect using EC2 Instance Connect<a name="ec2-instance-connect-methods"></a>

The following instructions explain how to connect to your Linux instance using EC2 Instance Connect\.

**Topics**
+ [Connect using the browser\-based client](#ec2-instance-connect-connecting-console)
+ [Connect using the EC2 Instance Connect CLI](#ec2-instance-connect-connecting-ec2-cli)
+ [Connect using your own key and SSH client](#ec2-instance-connect-connecting-aws-cli)

**Limitations**
+ The following Linux distributions are supported:
  + Amazon Linux 2 \(any version\)
  + Ubuntu 16\.04 or later
+ To connect using the browser\-based client, the instance must have a public IPv4 address\.
+ If the instance does not have a public IP address, then you can only connect to the instance using the EC2 Instance Connect CLI, and only from a machine within the same VPC\.
+ EC2 Instance Connect does not support connecting using an IPv6 address\.
+ The Safari browser is currently not supported\.

**Prerequisites**
+ **Install Instance Connect on your instance\.**

  For more information, see [Set up EC2 Instance Connect](ec2-instance-connect-set-up.md)\.
+ **\(Optional\) Install an SSH client on your local computer\.**

  There is no need to install an SSH client if users only use the console or the EC2 Instance Connect CLI to connect to an instance\. Your local computer most likely has an SSH client installed by default\. You can check for an SSH client by typing ssh at the command line\. If your local computer doesn't recognize the command, you can install an SSH client\. For information about installing an SSH client on Linux or macOS X, see [http://www\.openssh\.com](http://www.openssh.com/)\. For information about installing an SSH client on Windows 10, see [OpenSSH in Windows](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview)\.
+ **\(Optional\) Install the EC2 Instance Connect CLI on your local computer\.**

  There is no need to install the EC2 Instance Connect CLI if users only use the console or an SSH client to connect to an instance\. For more information, see [Task 3: \(Optional\) Install the EC2 Instance Connect CLI](ec2-instance-connect-set-up.md#ec2-instance-connect-install-eic-CLI)\.

## Connect using the browser\-based client<a name="ec2-instance-connect-connecting-console"></a>

You can connect to an instance using the browser\-based client by selecting the instance from the Amazon EC2 console and choosing to connect using EC2 Instance Connect\. Instance Connect handles the permissions and provides a successful connection\.

**To connect to your instance using the browser\-based client from the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Connect**\.

1. Choose **EC2 Instance Connect \(browser\-based SSH connection\)**, **Connect**\.

A window opens, and you are connected to your instance\.

## Connect using the EC2 Instance Connect CLI<a name="ec2-instance-connect-connecting-ec2-cli"></a>

You can connect to an instance using the EC2 Instance Connect CLI by providing only the instance ID, while the Instance Connect CLI performs the following three actions in one call: it generates a one\-time\-use SSH public key, pushes the key to the instance where it remains for 60 seconds, and connects the user to the instance\. You can use basic SSH/SFTP commands with the Instance Connect CLI\.

**Note**  
`-i` is not supported when using mssh\. When using the mssh command to connect to your instance, you do not need to specify any kind of identity file because Instance Connect manages the key pair\.

------
#### [ Amazon Linux 2 ]

**To connect to an instance using the EC2 Instance Connect CLI**  
Use the mssh command with the instance ID as follows\. You do not need to specify the user name for the AMI\.

```
$ mssh i-001234a4bf70dec41EXAMPLE
```

------
#### [ Ubuntu ]

**To connect to an instance using the EC2 Instance Connect CLI**  
Use the mssh command with the instance ID and the default user name for the Ubuntu AMI as follows\. You must specify the user name for the AMI or you get the following error: Authentication failed\.

```
$ mssh ubuntu@i-001234a4bf70dec41EXAMPLE
```

------

## Connect using your own key and SSH client<a name="ec2-instance-connect-connecting-aws-cli"></a>

You can use your own SSH key and connect to your instance from the SSH client of your choice while using the EC2 Instance Connect API\. This enables you to benefit from the Instance Connect capability to push a public key to the instance\.

**Requirement**  
The supported RSA key types are OpenSSH and SSH2\. The supported lengths are 2048 and 4096\. For more information, see [Option 2: Import your own public key to Amazon EC2](ec2-key-pairs.md#how-to-generate-your-own-key-and-import-it-to-aws)\.

**To connect to your instance using your own key and any SSH client**

1. 

**\(Optional\) Generate new SSH private and public keys**

   You can generate new SSH private and public keys, `my_rsa_key` and `my_rsa_key.pub`, using the following command:

   ```
   $ ssh-keygen -t rsa -f my_rsa_key
   ```

1. 

**Push your SSH public key to the instance**

   Use the [send\-ssh\-public\-key](https://docs.aws.amazon.com/cli/latest/reference/ec2-instance-connect/send-ssh-public-key.html) command to push your SSH public key to the instance\. If you launched your instance using Amazon Linux 2, the default user name for the AMI is `ec2-user`\. If you launched your instance using Ubuntu, the default user name for the AMI is `ubuntu`\.

   The following example pushes the public key to the specified instance in the specified Availability Zone, to authenticate `ec2-user`:

   ```
   $ aws ec2-instance-connect send-ssh-public-key \
       --instance-id i-001234a4bf70dec41EXAMPLE \
       --availability-zone us-west-2b \
       --instance-os-user ec2-user \
       --ssh-public-key file://my_rsa_key.pub
   ```

1. 

**Connect to the instance using your private key**

   Use the ssh command to connect to the instance using the private key before the public key is removed from the instance metadata \(you have 60 seconds before it is removed\)\. Specify the private key that corresponds to the public key, the default user name for the AMI that you used to launch your instance, and the instance's public DNS name\. Add the `IdentitiesOnly=yes` option to ensure that only the files in the ssh config and the specified key are used for the connection\. 

   ```
   $ ssh -o "IdentitiesOnly=yes" -i my_rsa_key ec2-user@ec2-198-51-100-1.compute-1.amazonaws.com
   ```
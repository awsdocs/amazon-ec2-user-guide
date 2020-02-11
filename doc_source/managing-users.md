# Managing User Accounts on Your Linux Instance<a name="managing-users"></a>

Each Linux instance launches with a default Linux system user account\. The default user name is determined by the AMI that was specified when you launched the instance\. For Amazon Linux 2 or the Amazon Linux AMI, the user name is `ec2-user`\. For CentOS, the user name is `centos`\. For Debian, the user name is `admin` or `root`\. For Fedora, the user name is `ec2-user` or `fedora`\. For RHEL, the user name is `ec2-user` or `root`\. For SUSE, the user name is `ec2-user` or `root`\. For Ubuntu, the user name is `ubuntu`\. Otherwise, if `ec2-user` and `root` don't work, check with your AMI provider\.

**Note**  
Linux system users should not be confused with AWS Identity and Access Management \(IAM\) users\. For more information, see [IAM Users and Groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/Using_WorkingWithGroupsAndUsers.html) in the *IAM User Guide*\.

**Topics**
+ [Considerations](#add-user-best-practice)
+ [Creating a User Account](#create-user-account)
+ [Removing a User Account](#delete-user-acount)

## Considerations<a name="add-user-best-practice"></a>

Using the default user account is adequate for many applications\. However, you may choose to add user accounts so that individuals can have their own files and workspaces\. Furthermore, creating user accounts for new users is much more secure than granting multiple \(possibly inexperienced\) users access to the default user account, because the default user account can cause a lot of damage to a system when used improperly\. For more information, see [Tips for Securing Your EC2 Instance](http://aws.amazon.com/articles/tips-for-securing-your-ec2-instance/)\.

To enable users SSH access to your EC2 instance using a Linux system user account, you must share the SSH key with the user\. Alternatively, you can use EC2 Instance Connect to provide access to users without the need to share and manage SSH keys\. For more information, see [Connecting to Your Linux Instance Using EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md)\.

## Creating a User Account<a name="create-user-account"></a>

First create the user account, and then add the SSH public key that allows the user to connect to and log into the instance\.

**Prerequisites**
+ **Create a key pair for each user or use existing key pairs**

  Share the `.pem` files with the corresponding user\. For more information, see [Creating a Key Pair Using Amazon EC2](ec2-key-pairs.md#having-ec2-create-your-key-pair)\.
+ **Retrieve the public key from each key pair**

  For more information, see [Retrieving the Public Key for Your Key Pair on Linux](ec2-key-pairs.md#retrieving-the-public-key) or [Retrieving the Public Key for Your Key Pair on Windows](ec2-key-pairs.md#retrieving-the-public-key-windows)\.

**To create a user account**

1. Use the adduser command to create the user account and add it to the system \(with an entry in the `/etc/passwd` file\)\. The command also creates a group and a home directory for the account\. In this example, the user account is named `newuser`\.

   ```
   [ec2-user ~]$ sudo adduser newuser
   ```

   \[Linux 2\] When adding a user to Amazon Linux 2 , make sure you are logged in as root user before running the adduser command \(sudo su\)\. Then add the new user and password for the new user\.

   ```
   [ec2-user]# useradd newuser 
   ```

   ```
   [ec2-user]# passwd newuser password
   ```

   You will be prompted to enter a password for the new user\.

   \[Ubuntu\] When adding a user to an Ubuntu system, include the `--disabled-password` parameter with this command to avoid adding a password to the account\.

   ```
   [ubuntu ~]$ sudo adduser newuser --disabled-password
   ```

1. Switch to the new account so that the directory and file that you will create will have the proper ownership\.

   ```
   [ec2-user ~]$ sudo su - newuser
   [newuser ~]$
   ```

   Notice that, in this example, the prompt changes from `ec2-user` to `newuser` to indicate that you have switched the shell session to the new account\.

1. Add the SSH public key to the user account\. First create a directory in the user's home directory for the SSH key file, then create the key file, and finally paste the public key into the key file\.

   1. Create a `.ssh` directory in the `newuser` home directory and change its file permissions to `700` \(only the owner can read, write, or open the directory\)\.

      ```
      [newuser ~]$ mkdir .ssh
      [newuser ~]$ chmod 700 .ssh
      ```
**Important**  
Without these exact file permissions, the user will not be able to log in\.

   1. Create a file named `authorized_keys` in the `.ssh` directory and change its file permissions to `600` \(only the owner can read or write to the file\)\.

      ```
      [newuser ~]$ touch .ssh/authorized_keys
      [newuser ~]$ chmod 600 .ssh/authorized_keys
      ```
**Important**  
Without these exact file permissions, the user will not be able to log in\.

   1. <a name="edit_auth_keys"></a>Open the `authorized_keys` file using your favorite text editor \(such as **vim** or **nano**\)\.

      ```
      [newuser ~]$ nano .ssh/authorized_keys
      ```

      Paste the public key for the key pair into the file and save the changes\. For example:

      ```
      ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
      hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
      lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
      qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
      BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
      ```

      The user should now be able to log into the `newuser` account on your instance using the private key that corresponds to the public key that you added to the `authorized_keys` file\.

## Removing a User Account<a name="delete-user-acount"></a>

If a user account is no longer needed, you can remove that account so that it can no longer be used\.

Use the userdel command to remove the user account from the system\. When you specify the `-r` parameter, the user's home directory and mail spool are deleted\. To keep the user's home directory and mail spool, omit the `-r` parameter\.

```
[ec2-user ~]$ sudo userdel -r olduser
```
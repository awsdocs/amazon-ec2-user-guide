# Manage users on your Linux instance<a name="managing-users"></a>

Each Linux instance launches with a default Linux system user\. You can add users to your instance and delete users\.

For the default user, the [default user name](#ami-default-user-names) is determined by the AMI that was specified when you launched the instance\. 

**Note**  
By default, password authentication and root login are disabled, and sudo is enabled\. To log in to your instance, you must use a key pair\. For more information about logging in, see [Connect to your Linux instance](AccessingInstances.md)\.  
You can allow password authentication and root login for your instance\. For more information, see the documentation for your operating system\.

**Note**  
Linux system users should not be confused with IAM users\. For more information, see [IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html#id_iam-users) in the *IAM User Guide*\.

**Topics**
+ [Default user names](#ami-default-user-names)
+ [Considerations](#add-user-best-practice)
+ [Create a user](#create-user-account)
+ [Remove a user](#delete-user-acount)

## Default user names<a name="ami-default-user-names"></a>

The default user name for your EC2 instance is determined by the AMI that was specified when you launched the instance\.

The default user names are:
+ For Amazon Linux 2022, Amazon Linux 2, or the Amazon Linux AMI, the user name is `ec2-user`\.
+ For a CentOS AMI, the user name is `centos` or `ec2-user`\.
+ For a Debian AMI, the user name is `admin`\.
+ For a Fedora AMI, the user name is `fedora` or `ec2-user`\.
+ For a RHEL AMI, the user name is `ec2-user` or `root`\.
+ For a SUSE AMI, the user name is `ec2-user` or `root`\.
+ For an Ubuntu AMI, the user name is `ubuntu`\.
+ For an Oracle AMI, the user name is `ec2-user`\.
+ For a Bitnami AMI, the user name is `bitnami`\.
+ Otherwise, check with the AMI provider\.

## Considerations<a name="add-user-best-practice"></a>

Using the default user is adequate for many applications\. However, you may choose to add users so that individuals can have their own files and workspaces\. Furthermore, creating users for new users is much more secure than granting multiple \(possibly inexperienced\) users access to the default user, because the default user can cause a lot of damage to a system when used improperly\. For more information, see [Tips for Securing Your EC2 Instance](http://aws.amazon.com/articles/tips-for-securing-your-ec2-instance/)\.

To enable users SSH access to your EC2 instance using a Linux system user, you must share the SSH key with the user\. Alternatively, you can use EC2 Instance Connect to provide access to users without the need to share and manage SSH keys\. For more information, see [Connect to your Linux instance using EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md)\.

## Create a user<a name="create-user-account"></a>

First create the user, and then add the SSH public key that allows the user to connect to and log into the instance\.

**To create a user**

1. [Create a new key pair](create-key-pairs.md#having-ec2-create-your-key-pair)\. You must provide the `.pem` file to the user for whom you are creating the user\. They must use this file to connect to the instance\.

1. Retrieve the public key from the key pair that you created in the previous step\.

   ```
   $  ssh-keygen -y -f /path_to_key_pair/key-pair-name.pem
   ```

   The command returns the public key, as shown in the following example\.

   ```
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6Vhz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXrlsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZqaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3RbBQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
   ```

1. Connect to the instance\.

1. Use the adduser command to create the user and add it to the system \(with an entry in the `/etc/passwd` file\)\. The command also creates a group and a home directory for the user\. In this example, the user is named `newuser`\.
   + Amazon Linux and Amazon Linux 2

     With Amazon Linux and Amazon Linux 2, the user is created with password authentication disabled by default\.

     ```
     [ec2-user ~]$ sudo adduser newuser
     ```
   + Ubuntu

     Include the `--disabled-password` parameter to create the user with password authentication disabled\.

     ```
     [ubuntu ~]$ sudo adduser newuser --disabled-password
     ```

1. Switch to the new user so that the directory and file that you create will have the proper ownership\.

   ```
   [ec2-user ~]$ sudo su - newuser
   ```

   The prompt changes from `ec2-user` to `newuser` to indicate that you have switched the shell session to the new user\.

1. Add the SSH public key to the user\. First create a directory in the user's home directory for the SSH key file, then create the key file, and finally paste the public key into the key file, as described in the following sub\-steps\.

   1. Create a `.ssh` directory in the `newuser` home directory and change its file permissions to `700` \(only the owner can read, write, or open the directory\)\.

      ```
      [newuser ~]$ mkdir .ssh
      ```

      ```
      [newuser ~]$ chmod 700 .ssh
      ```
**Important**  
Without these exact file permissions, the user will not be able to log in\.

   1. Create a file named `authorized_keys` in the `.ssh` directory and change its file permissions to `600` \(only the owner can read or write to the file\)\.

      ```
      [newuser ~]$ touch .ssh/authorized_keys
      ```

      ```
      [newuser ~]$ chmod 600 .ssh/authorized_keys
      ```
**Important**  
Without these exact file permissions, the user will not be able to log in\.

   1. <a name="edit_auth_keys"></a>Open the `authorized_keys` file using your favorite text editor \(such as **vim** or **nano**\)\.

      ```
      [newuser ~]$ nano .ssh/authorized_keys
      ```

      Paste the public key that you retrieved in **Step 2** into the file and save the changes\.
**Important**  
Ensure that you paste the public key in one continuous line\. The public key must not be split over multiple lines\.

      The user should now be able to log into the `newuser` user on your instance, using the private key that corresponds to the public key that you added to the `authorized_keys` file\. For more information about the different methods of connecting to a Linux instance, see [Connect to your Linux instance](AccessingInstances.md)\.

## Remove a user<a name="delete-user-acount"></a>

If a user is no longer needed, you can remove that user so that it can no longer be used\.

Use the userdel command to remove the user from the system\. When you specify the `-r` parameter, the user's home directory and mail spool are deleted\. To keep the user's home directory and mail spool, omit the `-r` parameter\.

```
[ec2-user ~]$ sudo userdel -r olduser
```
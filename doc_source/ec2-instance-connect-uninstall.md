# Uninstall EC2 Instance Connect<a name="ec2-instance-connect-uninstall"></a>

To disable EC2 Instance Connect, connect to your instance and uninstall the `ec2-instance-connect` package that you installed on the OS\. If the `sshd` configuration matches what it was set to when you installed EC2 Instance Connect, uninstalling `ec2-instance-connect` also removes the `sshd` configuration\. If you modified the `sshd` configuration after installing EC2 Instance Connect, you must update it manually\.

------
#### [ Amazon Linux 2 ]

You can uninstall EC2 Instance Connect on Amazon Linux 2 2\.0\.20190618 or later, where EC2 Instance Connect is preconfigured\.

**To uninstall EC2 Instance Connect on an instance launched with Amazon Linux 2**

1. Connect to your instance using SSH\. Specify the SSH key pair you used for your instance when you launched it and the default user name for the Amazon Linux 2 AMI, which is `ec2-user`\.

   For example, the following ssh command connects to the instance with the public DNS name `ec2-a-b-c-d.us-west-2.compute.amazonaws.com`, using the key pair `my_ec2_private_key.pem`\.

   ```
   $ ssh -i my_ec2_private_key.pem ec2-user@ec2-a-b-c-d.us-west-2.compute.amazonaws.com
   ```

1. Uninstall the `ec2-instance-connect` package using the following yum command\.

   ```
   [ec2-user ~]$ sudo yum remove ec2-instance-connect
   ```

------
#### [ Ubuntu ]

**To uninstall EC2 Instance Connect on an instance launched with an Ubuntu AMI**

1. Connect to your instance using SSH\. Specify the SSH key pair you used for your instance when you launched it and the default user name for the Ubuntu AMI, which is `ubuntu`\.

   For example, the following ssh command connects to the instance with the public DNS name `ec2-a-b-c-d.us-west-2.compute.amazonaws.com`, using the key pair `my_ec2_private_key.pem`\.

   ```
   $ ssh -i my_ec2_private_key.pem ubuntu@ec2-a-b-c-d.us-west-2.compute.amazonaws.com
   ```

1. Uninstall the `ec2-instance-connect` package using the following apt\-get command\.

   ```
   ubuntu:~$ sudo apt-get remove ec2-instance-connect
   ```

------
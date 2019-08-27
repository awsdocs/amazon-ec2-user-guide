# Uninstall EC2 Instance Connect<a name="ec2-instance-connect-uninstall"></a>

To disable EC2 Instance Connect, you uninstall the `ec2-instance-connect` package that was installed on the OS\. If the sshd configuration matches what it was set to when you installed EC2 Instance Connect, uninstalling the `ec2-instance-connect` package also removes the sshd configuration\. If the sshd configuration was modified after installing EC2 Instance Connect, you must update it manually\.

You can uninstall EC2 Instance Connect on Amazon Linux 2 2\.0\.20190618 or later, which comes preconfigured with EC2 Instance Connect\.

------
#### [ Amazon Linux 2 ]

**To uninstall EC2 Instance Connect on an instance launched with Amazon Linux 2**

1. Connect to your instance using SSH\.

   Use the SSH key pair that was assigned to your instance when you launched it and the default user name of the AMI that you used to launch your instance\. For Amazon Linux 2, the default user name is `ec2-user`\.

   For example, if your instance was launched using Amazon Linux 2, your instance's public DNS is `ec2-a-b-c-d.us-west-2.compute.amazonaws.com`, and the key pair is `my_ec2_private_key.pem`, use the following command to SSH into your instance\.

   ```
   $ ssh -i my_ec2_private_key.pem ec2-user@ec2-a-b-c-d.us-west-2.compute.amazonaws.com
   ```

1. Uninstall the `ec2-instance-connect` package from your instance\.

   For Amazon Linux 2, use the yum command to remove EC2 Instance Connect\.

   ```
   [ec2-user ~]$ sudo yum remove ec2-instance-connect
   ```

------
#### [ Ubuntu ]

**To uninstall EC2 Instance Connect on an instance launched with an Ubuntu AMI**

1. Connect to your instance using SSH\.

   Use the SSH key pair that was assigned to your instance when you launched it and the default user name of the AMI that you used to launch your instance\. For Ubuntu, the default user name is `ubuntu`\.

   For example, if your instance was launched using Amazon Linux 2, your instance's public DNS is `ec2-a-b-c-d.us-west-2.compute.amazonaws.com`, and the key pair is `my_ec2_private_key.pem`, use the following command to SSH into your instance\.

   ```
   $ ssh -i my_ec2_private_key.pem ubuntu@ec2-a-b-c-d.us-west-2.compute.amazonaws.com
   ```

1. Uninstall the `ec2-instance-connect` package from your instance\.

   For Ubuntu, use the apt\-get command to remove EC2 Instance Connect\.

   ```
   ubuntu:~$ sudo apt-get remove ec2-instance-connect
   ```

------
# Connecting to Your Linux Instance Using EC2 Instance Connect<a name="Connect-using-EC2-Instance-Connect"></a>

Amazon EC2 Instance Connect provides a simple and secure way to connect to your instances using Secure Shell \(SSH\)\. With EC2 Instance Connect, you use AWS Identity and Access Management \(IAM\) policies and principals to control SSH access to your instances, removing the need to share and manage SSH keys\. All connection requests using EC2 Instance Connect are [logged to AWS CloudTrail so that you can audit connection requests](monitor-with-cloudtrail.md#ec2-instance-connect-cloudtrail)\.

You can use Instance Connect to connect to your Linux instances using a browser\-based client, the Amazon EC2 Instance Connect CLI, or the SSH client of your choice\.

When you connect to an instance using EC2 Instance Connect, the Instance Connect API pushes a one\-time\-use SSH public key to the [instance metadata](ec2-instance-metadata.md) where it remains for 60 seconds\. An IAM policy attached to your IAM user authorizes your IAM user to push the public key to the instance metadata\. The SSH daemon uses `AuthorizedKeysCommand` and `AuthorizedKeysCommandUser`, which are configured when Instance Connect is installed, to look up the public key from the instance metadata for authentication, and connects you to the instance\.

**Note**  
If you are connecting to a Linux instance from a local computer running Windows, see the following documentation instead: [Connecting to Your Linux Instance from Windows Using PuTTY](putty.md) and [Connecting to Your Linux Instance from Windows Using Windows Subsystem for Linux](WSL.md)\.

**Topics**
+ [Set Up EC2 Instance Connect](ec2-instance-connect-set-up.md)
+ [Connect Using EC2 Instance Connect](ec2-instance-connect-methods.md)
+ [Uninstall EC2 Instance Connect](ec2-instance-connect-uninstall.md)
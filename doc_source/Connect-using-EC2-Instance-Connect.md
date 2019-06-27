# Connecting to Your Linux Instance Using EC2 Instance Connect<a name="Connect-using-EC2-Instance-Connect"></a>

Amazon EC2 Instance Connect provides a simple and secure way to connect to your instances using Secure Shell \(SSH\)\. With EC2 Instance Connect, you use AWS Identity and Access Management \(IAM\) policies and principals to control SSH access to your instances, removing the need to share and manage SSH keys\. All connection requests using EC2 Instance Connect are [logged to AWS CloudTrail so that you can audit connection requests](monitor-with-cloudtrail.md#ec2-instance-connect-cloudtrail)\.

You can use the Instance Connect feature to connect to your Linux instances from the Amazon EC2 console, the Amazon EC2 Instance Connect CLI, or the Amazon EC2 API\.

**Note**  
If you are connecting to a Linux instance from a local computer running Windows, see the following documentation instead: [Connecting to Your Linux Instance from Windows Using PuTTY](putty.md) and [Connecting to Your Linux Instance from Windows Using Windows Subsystem for Linux](WSL.md)\.

**Topics**
+ [How EC2 Instance Connect Works](#ec2-instance-connect-how-it-works)
+ [Set Up EC2 Instance Connect](ec2-instance-connect-set-up.md)
+ [Connect Using EC2 Instance Connect](ec2-instance-connect-methods.md)
+ [Uninstall EC2 Instance Connect](ec2-instance-connect-uninstall.md)

## How EC2 Instance Connect Works<a name="ec2-instance-connect-how-it-works"></a>

When you connect to an instance using EC2 Instance Connect, the Instance Connect API pushes a one\-time\-use SSH public key to the [instance metadata](ec2-instance-metadata.md) where it remains for 60 seconds\. The IAM policy attached to your IAM user authorizes your IAM user to push the public key to the instance metadata\. The `AuthorizedKeysCommand` and `AuthorizedKeysCommandUser`, configured when Instance Connect is installed, tells the SSH daemon to look up the public key from the instance metadata for authentication, and connects you to the instance\.

You can use Instance Connect to connect to your instances using any SSH client of your choice or the Instance Connect CLI, or you can connect to your instances by using the new browser\-based SSH client in the Amazon EC2 console\.
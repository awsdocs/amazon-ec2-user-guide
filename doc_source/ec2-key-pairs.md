# Amazon EC2 key pairs and Linux instances<a name="ec2-key-pairs"></a>

A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance\. Amazon EC2 stores the public key on your instance, and you store the private key\. For Linux instances, the private key allows you to securely SSH into your instance\. As an alternative to key pairs, you can use [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) to connect to your instance with an interactive one\-click browser\-based shell or the AWS Command Line Interface \(AWS CLI\)\.

Anyone who possesses your private key can connect to your instances, so it's important that you store your private key in a secure place\.

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\. You can choose an existing key pair or create a new one\. When your instance boots for the first time, the public key that you specified at launch is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`\. When you connect to your Linux instance using SSH, to log in you must specify the private key that corresponds to the public key\. For more information about connecting to your instance, see [Connect to your Linux instance](AccessingInstances.md)\. For more information about key pairs and Windows instances, see [Amazon EC2 key pairs and Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Windows Instances*\.

Because Amazon EC2 doesn't keep a copy of your private key, there is no way to recover a private key if you lose it\. However, there can still be a way to connect to instances for which you've lost the private key\. For more information, see [I've lost my private key\. How can I connect to my Linux instance?](TroubleshootingInstancesConnecting.md#replacing-lost-key-pair)

You can use Amazon EC2 to create your key pairs\. You can also use a third\-party tool to create your key pairs, and then import the public keys to Amazon EC2\.

Amazon EC2 supports ED25519 and 2048\-bit SSH\-2 RSA keys for Linux instances\.

You can have up to 5,000 key pairs per Region\.

**Topics**
+ [Create key pairs](create-key-pairs.md)
+ [Tag a public key](tag-key-pair.md)
+ [Describe public keys](describe-keys.md)
+ [Delete your public key on Amazon EC2](delete-key-pair.md)
+ [Add or remove a public key on your instance](replacing-key-pair.md)
+ [Verify keys](verify-keys.md)
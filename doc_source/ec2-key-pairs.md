# Amazon EC2 Key Pairs<a name="ec2-key-pairs"></a>

Amazon EC2 uses public key cryptography to encrypt and decrypt login information\. Public key cryptography uses a public key to encrypt a piece of data, and then the recipient uses the private key to decrypt the data\. The public and private keys are known as a *key pair*\. Public key cryptography enables you to securely access your instances using a private key instead of a password\.

When you launch an instance, you specify the key pair\. You can specify an existing key pair or a new key pair that you create at launch\. At boot time, the public key content is placed on the instance in an entry within `~/.ssh/authorized_keys`\. To log in to your instance, you must specify the private key when you connect to the instance\. For more information, see [Launch Your Instance](LaunchingAndUsingInstances.md) and [Connect to Your Linux Instance](AccessingInstances.md)\.

**Creating a Key Pair**  
You can use Amazon EC2 to create your key pair\. For more information, see [Creating a Key Pair Using Amazon EC2](#having-ec2-create-your-key-pair)\.

Alternatively, you can use a third\-party tool and then import the public key to Amazon EC2\. For more information, see [Importing Your Own Public Key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)\.

Each key pair requires a name\. Be sure to choose a name that is easy to remember\. Amazon EC2 associates the public key with the name that you specify as the key name\.

Amazon EC2 stores the public key only, and you store the private key\. Anyone who possesses your private key can decrypt your login information, so it's important that you store your private keys in a secure place\.

The keys that Amazon EC2 uses are 2048\-bit SSH\-2 RSA keys\. You can have up to 5,000 key pairs per Region\.

**Launching and Connecting to Your Instance**  
When you launch an instance, you should specify the name of the key pair that you plan to use to connect to the instance\. If you don't specify the name of an existing key pair when you launch an instance, you won't be able to connect to the instance\. When you connect to the instance, you must specify the private key that corresponds to the key pair that you specified when you launched the instance\.

**Note**  
Amazon EC2 doesn't keep a copy of your private key; therefore, if you lose a private key, there is no way to recover it\. If you lose the private key for an instance store\-backed instance, you can't access the instance; you should terminate the instance and launch another instance using a new key pair\. If you lose the private key for an EBS\-backed Linux instance, you can regain access to your instance\. For more information, see [Connecting to Your Linux Instance If You Lose Your Private Key](#replacing-lost-key-pair)\.

**Key Pairs for Multiple Users**  
If you have several users that require access to a single instance, you can add user accounts to your instance\. For more information, see [Managing User Accounts on Your Linux Instance](managing-users.md)\. You can create a key pair for each user, and add the public key information from each key pair to the `.ssh/authorized_keys` file for each user on your instance\. You can then distribute the private key files to your users\. That way, you do not have to distribute the same private key file that's used for the AWS account root user to multiple users\. 

**Topics**
+ [Creating a Key Pair Using Amazon EC2](#having-ec2-create-your-key-pair)
+ [Importing Your Own Public Key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)
+ [Retrieving the Public Key for Your Key Pair on Linux](#retrieving-the-public-key)
+ [Retrieving the Public Key for Your Key Pair on Windows](#retrieving-the-public-key-windows)
+ [Retrieving the Public Key for Your Key Pair from Your Instance](#retrieving-the-public-key-instance)
+ [Verifying Your Key Pair's Fingerprint](#verify-key-pair-fingerprints)
+ [Deleting Your Key Pair](#delete-key-pair)
+ [Adding or Replacing a Key Pair for Your Instance](#replacing-key-pair)
+ [Connecting to Your Linux Instance If You Lose Your Private Key](#replacing-lost-key-pair)

## Creating a Key Pair Using Amazon EC2<a name="having-ec2-create-your-key-pair"></a>

After you create a key pair, you can specify it when you launch your instance\. You can also add the key pair to a running instance to enable another user to connect to the instance\. For more information, see [Adding or Replacing a Key Pair for Your Instance](#replacing-key-pair)\.

You can create a key pair using one of the following methods\. 

------
#### [ New console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Choose **Create key pair**\.

1. For **Name**, enter a descriptive name for the key pair\.

1. For **File format**, choose the format in which to save the private key\. To save the private key in a format that can be used with OpenSHH, choose **pem**\. To save the private key in a format that can be used with PuTTY, choose **ppk**\.

1. Choose **Create key pair**\.

------
#### [ Old console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.
**Note**  
The navigation pane is on the left side of the Amazon EC2 console\. If you do not see the pane, it might be minimized; choose the arrow to expand the pane\. 

1. Choose **Create Key Pair**\.

1. For **Key pair name**, enter a name for the new key pair, and then choose **Create**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected Private Key File](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

------
#### [ AWS CLI ]

**To create your key pair**  
Use the [create\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-key-pair.html) AWS CLI command\.

------
#### [ PowerShell ]

**To create your key pair**  
Use the [New\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------

## Importing Your Own Public Key to Amazon EC2<a name="how-to-generate-your-own-key-and-import-it-to-aws"></a>

Instead of using Amazon EC2 to create your key pair, you can create an RSA key pair using a third\-party tool and then import the public key to Amazon EC2\. For example, you can use ssh\-keygen \(a tool provided with the standard OpenSSH installation\) to create a key pair\. Alternatively, Java, Ruby, Python, and many other programming languages provide standard libraries that you can use to create an RSA key pair\.

**Requirements**
+ 

  The following formats are supported:
  + OpenSSH public key format \(the format in `~/.ssh/authorized_keys`\)\. If you connect using SSH while using the EC2 Instance Connect API, the SSH2 format is also supported\.
  + Base64 encoded DER format
  + SSH public key file format as specified in [RFC4716](http://tools.ietf.org/html/rfc4716)
  + SSH private key file format must be PEM \(for example, use `ssh-keygen -m PEM` to convert the OpenSSH key into the PEM format\)
+ Create an RSA key\. Amazon EC2 does not accept DSA keys\.
+ The supported lengths are 1024, 2048, and 4096\. If you connect using SSH while using the EC2 Instance Connect API, the supported lengths are 2048 and 4096\.

**To create a key pair using a third\-party tool**

1. Generate a key pair with a third\-party tool of your choice\.

1. Save the public key to a local file\. For example, `~/.ssh/my-key-pair.pub` \(Linux\) or  `C:\keys\my-key-pair.pub` \(Windows\)\. The file name extension for this file is not important\.

1. Save the private key to a different local file that has the `.pem` extension\. For example, `~/.ssh/my-key-pair.pem` \(Linux\) or `C:\keys\my-key-pair.pem` \(Windows\)\. Save the private key file in a safe place\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.

After you have created the key pair, use one of the following methods to import your key pair to Amazon EC2\.

------
#### [ New console ]

**To import the public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Choose **Import key pair**\.

1. For **Name**, enter a descriptive name for the key pair\.

1. Either choose **Browse** to navigate to and select your public key, or paste the contents of your public key into the **Public key contents** field\.

1. Choose **Import key pair**\.

1. Verify that the key pair you imported appears in the list of key pairs\.

------
#### [ Old console ]

**To import the public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Choose **Import Key Pair**\. 

1. In the **Import Key Pair** dialog box, choose **Browse**, and select the public key file that you saved previously\. Enter a name for the key pair in the **Key pair name** field, and choose **Import**\.

1. Verify that the key pair you imported appears in the list of key pairs\.

------
#### [ AWS CLI ]

**To import the public key**  
Use the [import\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/import-key-pair.html) AWS CLI command\.

**To verify that the key pair was imported successfully**  
Use the [describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) AWS CLI command\.

------
#### [ PowerShell ]

**To import the public key**  
Use the [Import\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Import-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

**To verify that the key pair was imported successfully**  
Use the [Get\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------

## Retrieving the Public Key for Your Key Pair on Linux<a name="retrieving-the-public-key"></a>

On your local Linux or macOS computer, you can use the ssh\-keygen command to retrieve the public key for your key pair\. Specify the path where you downloaded your private key \(the `.pem` file\)\.

```
ssh-keygen -y -f /path_to_key_pair/my-key-pair.pem
```

The command returns the public key, as shown in the following example\.

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
```

If the command fails, run the following command to ensure that you've changed the permissions on your key pair file so that only you can view it\.

```
chmod 400 my-key-pair.pem
```

## Retrieving the Public Key for Your Key Pair on Windows<a name="retrieving-the-public-key-windows"></a>

On your local Windows computer, you can use PuTTYgen to get the public key for your key pair\.

Start PuTTYgen and choose **Load**\. Select the `.ppk` or `.pem` file\. PuTTYgen displays the public key under **Public key for pasting into OpenSSH authorized\_keys file**\. You can also view the public key by choosing **Save public key**, specifying a name for the file, saving the file, and then opening the file\.

## Retrieving the Public Key for Your Key Pair from Your Instance<a name="retrieving-the-public-key-instance"></a>

The public key that you specified when you launched an instance is also available to you through its instance metadata\. To view the public key that you specified when launching the instance, use the following command from your instance:

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
```

The following is an example output\.

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
```

The following is an example output\.

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
```

------

If you change the key pair that you use to connect to the instance, we don't update the instance metadata to show the new public key\. Instead, the instance metadata continues to show the public key for the key pair that you specified when you launched the instance\. For more information, see [Retrieving Instance Metadata](instancedata-data-retrieval.md)\.

Alternatively, on a Linux instance, the public key content is placed in an entry within `~/.ssh/authorized_keys`\. You can open this file in an editor\. The following is an example entry for the key pair named **my\-key\-pair**\. It consists of the public key followed by the name of the key pair\.

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
```

## Verifying Your Key Pair's Fingerprint<a name="verify-key-pair-fingerprints"></a>

On the **Key Pairs** page in the Amazon EC2 console, the **Fingerprint** column displays the fingerprints generated from your key pairs\. AWS calculates the fingerprint differently depending on whether the key pair was generated by AWS or a third\-party tool\. If you created the key pair using AWS, the fingerprint is calculated using an SHA\-1 hash function\. If you created the key pair with a third\-party tool and uploaded the public key to AWS, or if you generated a new public key from an existing AWS\-created private key and uploaded it to AWS, the fingerprint is calculated using an MD5 hash function\.

You can use the SSH2 fingerprint that's displayed on the **Key Pairs** page to verify that the private key you have on your local machine matches the public key stored in AWS\. From the computer where you downloaded the private key file, generate an SSH2 fingerprint from the private key file\. The output should match the fingerprint that's displayed in the console\.

If you created your key pair using AWS, you can use the OpenSSL tools to generate a fingerprint as shown in the following example\.

```
$ openssl pkcs8 -in path_to_private_key -inform PEM -outform DER -topk8 -nocrypt | openssl sha1 -c
```

If you created a key pair using a third\-party tool and uploaded the public key to AWS, you can use the OpenSSL tools to generate the fingerprint as shown in the following example\.

```
$ openssl rsa -in path_to_private_key -pubout -outform DER | openssl md5 -c
```

If you created an OpenSSH key pair using OpenSSH 7\.8 or later and uploaded the public key to AWS, you can use ssh\-keygen to generate the fingerprint as shown in the following example\.

```
$ ssh-keygen -ef path_to_private_key -m PEM | openssl rsa -RSAPublicKey_in -outform DER | openssl md5 -c
```

## Deleting Your Key Pair<a name="delete-key-pair"></a>

When you delete a key pair, you are only deleting the Amazon EC2 copy of the public key\. Deleting a key pair doesn't affect the private key on your computer or the public key on any instances that already launched using that key pair\. You can't launch a new instance using a deleted key pair, but you can continue to connect to any instances that you launched using a deleted key pair, as long as you still have the private key \(`.pem`\) file\.

**Note**  
If you're using an Auto Scaling group \(for example, in an Elastic Beanstalk environment\), ensure that the key pair you're deleting is not specified in your launch configuration\. Amazon EC2 Auto Scaling launches a replacement instance if it detects an unhealthy instance; however, the instance launch fails if the key pair cannot be found\. 

You can delete a key pair using one of the following methods\.

------
#### [ New console ]

**To delete your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select the key pair to delete and choose **Delete**\.

1. In the confirmation field, enter `Delete` and then choose **Delete**\.

------
#### [ Old console ]

**To delete your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Select the key pair and choose **Delete**\.

1. When prompted, choose **Yes**\.

------
#### [ AWS CLI ]

**To delete your key pair**  
Use the [delete\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-key-pair.html) AWS CLI command\.

------
#### [ PowerShell ]

**To delete your key pair**  
Use the [Remove\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------

**Note**  
If you create a Linux AMI from an instance, and then use the AMI to launch a new instance in a different Region or account, the new instance includes the public key from the original instance\. This enables you to connect to the new instance using the same private key file as your original instance\. You can remove this public key from your instance by removing its entry from the `.ssh/authorized_keys` file using a text editor of your choice\. For more information about managing users on your instance and providing remote access using a specific key pair, see [Managing User Accounts on Your Linux Instance](managing-users.md)\.

## Adding or Replacing a Key Pair for Your Instance<a name="replacing-key-pair"></a>

You can change the key pair that is used to access the default system account of your instance\. For example, if a user in your organization requires access to the system user account using a separate key pair, you can add that key pair to your instance\. Or, if someone has a copy of the `.pem` file and you want to prevent them from connecting to your instance \(for example, if they've left your organization\), you can replace the key pair with a new one\.

**Note**  
These procedures are for modifying the key pair for the default user account, such as `ec2-user`\. For more information about adding user accounts to your instance, see [Managing User Accounts on Your Linux Instance](managing-users.md)\.

**To add or replace a key pair**

1. Create a new key pair using [the Amazon EC2 console](#having-ec2-create-your-key-pair) or a [third\-party tool](#how-to-generate-your-own-key-and-import-it-to-aws)\.

1. Retrieve the public key from your new key pair\. For more information, see [Retrieving the Public Key for Your Key Pair on Linux](#retrieving-the-public-key) or [Retrieving the Public Key for Your Key Pair on Windows](#retrieving-the-public-key-windows)\.

1. Connect to your instance using your existing private key file\.

1. Using a text editor of your choice, open the `.ssh/authorized_keys` file on the instance\. Paste the public key information from your new key pair underneath the existing public key information\. Save the file\.

1. Disconnect from your instance, and test that you can connect to your instance using the new private key file\.

1. \(Optional\) If you're replacing an existing key pair, connect to your instance and delete the public key information for the original key pair from the `.ssh/authorized_keys` file\.

**Note**  
If you're using an Auto Scaling group \(for example, in an Elastic Beanstalk environment\), ensure that the key pair you're replacing is not specified in your launch configuration\. Amazon EC2 Auto Scaling launches a replacement instance if it detects an unhealthy instance; however, the instance launch fails if the key pair cannot be found\. 

## Connecting to Your Linux Instance If You Lose Your Private Key<a name="replacing-lost-key-pair"></a>

If you lose the private key for an EBS\-backed instance, you can regain access to your instance\. You must stop the instance, detach its root volume and attach it to another instance as a data volume, modify the `authorized_keys` file, move the volume back to the original instance, and restart the instance\. For more information about launching, connecting to, and stopping instances, see [Instance Lifecycle](ec2-instance-lifecycle.md)\.

This procedure isn't supported for instance store\-backed instances\. To determine the root device type of your instance, open the Amazon EC2 console, choose **Instances**, select the instance, and check the value of **Root device type** in the details pane\. The value is either `ebs` or `instance store`\. If the root device is an instance store volume, you must have the private key in order to connect to the instance\.

**To connect to an EBS\-backed instance with a different key pair**

1. Create a new key pair using either the Amazon EC2 console or a third\-party tool\. If you want to name your new key pair exactly the same as the lost private key, you must first delete the existing key pair\. 

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances** in the navigation pane, and then select the instance that you'd like to connect to\. \(We'll refer to this as the original instance\.\)

1. From the **Description** tab, save the following information that you need to complete this procedure\.
   + Write down the instance ID, AMI ID, and Availability Zone of the original instance\.
   + In the **Root device** field, take note of the device name for the root volume \(for example, `/dev/sda1` or `/dev/xvda`\)\. Choose the link and write down the volume ID in the **EBS ID** field \(vol\-*xxxxxxxxxxxxxxxxx*\)\.

1. Choose **Actions**, select **Instance State**, and then select **Stop**\. If **Stop** is disabled, either the instance is already stopped or its root device is an instance store volume\.
**Warning**  
When you stop an instance, the data on any instance store volumes is erased\. To keep data from instance store volumes, be sure to back it up to persistent storage\.

1. Choose **Launch Instance**, and then use the launch wizard to launch a temporary instance with the following options:
   + On the **Choose an AMI** page, select the same AMI that you used to launch the original instance\. If this AMI is unavailable, you can create an AMI that you can use from the stopped instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) \.
   + On the **Choose an Instance Type** page, leave the default instance type that the wizard selects for you\.
   + On the **Configure Instance Details** page, specify the same Availability Zone as the instance you'd like to connect to\. If you're launching an instance in a VPC, select a subnet in this Availability Zone\.
   + On the **Add Tags** page, add the tag `Name=Temporary` to the instance to indicate that this is a temporary instance\.
   + On the **Review** page, choose **Launch**\. Create a new key pair, download it to a safe location on your computer, and then choose **Launch Instances**\.

1. In the navigation pane, choose **Volumes** and select the root device volume for the original instance \(you wrote down its volume ID in a previous step\)\. Choose **Actions**, **Detach Volume**, and then select **Yes, Detach**\. Wait for the state of the volume to become `available`\. \(You might need to choose the **Refresh** icon\.\)

1. With the volume still selected, choose **Actions**, and then select **Attach Volume**\. Select the instance ID of the temporary instance, write down the device name specified under **Device** \(for example, `/dev/sdf`\), and then choose **Attach**\.
**Note**  
If you launched your original instance from an AWS Marketplace AMI and your volume contains AWS Marketplace codes, you must first stop the temporary instance before you can attach the volume\.

1. Connect to the temporary instance\.

1. From the temporary instance, mount the volume that you attached to the instance so that you can access its file system\. For example, if the device name is `/dev/sdf`, use the following commands to mount the volume as `/mnt/tempvol`\.
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

   1. Mount the volume \(or partition\) at the temporary mount point, using the volume name or device name that you identified earlier\. The required command depends on your operating system's file system\.
      + Amazon Linux, Ubuntu, and Debian

        ```
        [ec2-user ~]$ sudo mount /dev/xvdf1 /mnt/tempvol
        ```
      + Amazon Linux 2, CentOS, SLES 12, and RHEL 7\.x

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

1. From the temporary instance, unmount the volume that you attached so that you can reattach it to the original instance\. For example, use the following command to unmount the volume at `/mnt/tempvol`\.

   ```
   [ec2-user ~]$ sudo umount /mnt/tempvol
   ```

1. From the Amazon EC2 console, select the volume with the volume ID that you wrote down, choose **Actions**, **Detach Volume**, and then select **Yes, Detach**\. Wait for the state of the volume to become `available`\. \(You might need to choose the **Refresh** icon\.\)

1. With the volume still selected, choose **Actions**, **Attach Volume**\. Select the instance ID of the original instance, specify the device name that you noted earlier for the original root device attachment \(`/dev/sda1` or `/dev/xvda`\), and then choose **Attach**\.
**Important**  
If you don't specify the same device name as the original attachment, you cannot start the original instance\. Amazon EC2 expects the root device volume at `sda1` or `/dev/xvda`\.

1. Select the original instance, choose **Actions**, select **Instance State**, and then choose **Start**\. After the instance enters the `running` state, you can connect to it using the private key file for your new key pair\.
**Note**  
If the name of your new key pair and corresponding private key file is different from the name of the original key pair, ensure that you specify the name of the new private key file when you connect to your instance\.

1. \(Optional\) You can terminate the temporary instance if you have no further use for it\. Select the temporary instance, choose **Actions**, select **Instance State**, and then choose **Terminate**\.
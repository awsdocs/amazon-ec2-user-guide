# Amazon EC2 key pairs and Linux instances<a name="ec2-key-pairs"></a>

A key pair, consisting of a public key and a private key, is a set of security credentials that you use to prove your identity when connecting to an Amazon EC2 instance\. Amazon EC2 stores the public key on your instance, and you store the private key\. For Linux instances, the private key allows you to securely SSH into your instance\. Anyone who possesses your private key can connect to your instances, so it's important that you store your private key in a secure place\.

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\. You can choose an existing key pair or create a new one\. When your instance boots for the first time, the public key that you specified at launch is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`\. When you connect to your Linux instance using SSH, to log in you must specify the private key that corresponds to the public key\. For more information about connecting to your instance, see [Connect to your Linux instance](AccessingInstances.md)\. For more information about key pairs and Windows instances, see [Amazon EC2 key pairs and Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Windows Instances*\.

Because Amazon EC2 doesn't keep a copy of your private key, there is no way to recover a private key if you lose it\. However, there can still be a way to connect to instances for which you've lost the private key\. For more information, see [Connect to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\.

You can use Amazon EC2 to create your key pairs\. You can also use a third\-party tool to create your key pairs, and then import the public keys to Amazon EC2\.

The keys that Amazon EC2 uses are ED25519 or 2048\-bit SSH\-2 RSA keys\.

You can have up to 5,000 key pairs per Region\.

**Topics**
+ [Create a key pair using Amazon EC2](#having-ec2-create-your-key-pair)
+ [Create a key pair using a third\-party tool and import the public key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)
+ [Tag a public key](#tag-key-pair)
+ [Retrieve the public key from the private key](#retrieving-the-public-key)
+ [Retrieve the public key through instance metadata](#retrieving-the-public-key-instance)
+ [Locate the public key on an instance](#locate-public-key-on-instance)
+ [Identify the key pair that was specified at launch](#identify-key-pair-specified-at-launch)
+ [Verify your key pair's fingerprint](#verify-key-pair-fingerprints)
+ [Add or replace a key pair for your instance](#replacing-key-pair)
+ [Delete your key pair](#delete-key-pair)
+ [Delete a public key from an instance](#delete-public-key-from-instance)
+ [Connect to your Linux instance if you lose your private key](replacing-lost-key-pair.md)

## Create a key pair using Amazon EC2<a name="having-ec2-create-your-key-pair"></a>

You can create a key pair using one of the following methods\. 

------
#### [ Console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Network & Security**, choose **Key Pairs**\.

1. Choose **Create key pair**\.

1. For **Name**, enter a descriptive name for the key pair\. Amazon EC2 associates the public key with the name that you specify as the key name\. A key name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. For **Key pair type**, choose either **RSA** or **ED25519**\. Note that **ED25519** keys are not supported for Windows instances, EC2 Instance Connect, or EC2 Serial Console\.

1. For **Private key file format**, choose the format in which to save the private key\. To save the private key in a format that can be used with OpenSSH, choose **pem**\. To save the private key in a format that can be used with PuTTY, choose **ppk**\.

   If you chose **ED25519** in the previous step, the **Private key file format** options do not appear, and the private key format defaults to **pem**\.

1. To add a tag to the public key, choose **Add tag**, and enter the key and value for the tag\. Repeat for each tag\. 

1. Choose **Create key pair**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name that you specified as the name of your key pair, and the file name extension is determined by the file format that you chose\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\.

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

------
#### [ AWS CLI ]

**To create your key pair**

1. Use the [create\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-key-pair.html) command as follows to generate the key pair and to save the private key to a `.pem` file\.

   For `--key-name`, specify a name for the public key\. The name can be up to 255 ASCII characters\.

   For `--key-type`, specify either `rsa` or `ed25519`\. If you do not include the `--key-type` parameter, an `rsa` key is created by default\. Note that ED25519 keys are not supported for Windows instances, EC2 Instance Connect, and EC2 Serial Console\.

   `--query "KeyMaterial"` prints the private key material to the output\.

   `--output text > my-key-pair.pem` saves the private key material in a file with the `.pem` extension\. The private key can have a name that's different from the public key name, but for ease of use, use the same name\.

   ```
   aws ec2 create-key-pair \
       --key-name my-key-pair \
       --key-type rsa \
       --query "KeyMaterial" \
       --output text > my-key-pair.pem
   ```

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

------
#### [ PowerShell ]

**To create your key pair**  
Use the [New\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2KeyPair.html) AWS Tools for Windows PowerShell command as follows to generate the key and save it to a `.pem` file\.

For `-KeyName`, specify a name for the public key\. The name can be up to 255 ASCII characters\.

For `-KeyType`, specify either `rsa` or `ed25519`\. If you do not include the `-KeyType` parameter, an `rsa` key is created by default\. Note that ED25519 keys are not supported for Windows instances, EC2 Instance Connect, and EC2 Serial Console\.

`KeyMaterial` prints the private key material to the output\.

`Out-File -Encoding ascii -FilePath C:\path\my-key-pair.pem` saves the private key material in a file with the `.pem` extension\. The private key can have a name that's different from the public key name, but for ease of use, use the same name\.

```
PS C:\> (New-EC2KeyPair -KeyName "my-key-pair" -KeyType "rsa").KeyMaterial | Out-File -Encoding ascii -FilePath C:\path\my-key-pair.pem
```

------

## Create a key pair using a third\-party tool and import the public key to Amazon EC2<a name="how-to-generate-your-own-key-and-import-it-to-aws"></a>

Instead of using Amazon EC2 to create your key pair, you can create an RSA or ED25519 key pair by using a third\-party tool, and then import the public key to Amazon EC2\.

**Requirements for key pairs**
+ Supported types: RSA and ED25519\. Amazon EC2 does not accept DSA keys\.
  + Note that ED25519 keys are not supported for Windows instances, EC2 Instance Connect, and EC2 Serial Console\.
+ Supported formats:
  + OpenSSH public key format \(the format in `~/.ssh/authorized_keys`\)\. If you connect using SSH while using the EC2 Instance Connect API, the SSH2 format is also supported\.
  + SSH private key file format must be PEM
  + \(RSA only\) Base64 encoded DER format
  + \(RSA only\) SSH public key file format as specified in [RFC 4716](https://www.ietf.org/rfc/rfc4716.txt)
+ Supported lengths: 1024, 2048, and 4096\. If you connect using SSH while using the EC2 Instance Connect API, the supported lengths are 2048 and 4096\.

**To create a key pair using a third\-party tool**

1. Generate a key pair with a third\-party tool of your choice\. For example, you can use ssh\-keygen \(a tool provided with the standard OpenSSH installation\)\. Alternatively, Java, Ruby, Python, and many other programming languages provide standard libraries that you can use to create an RSA or ED25519 key pair\.
**Important**  
The private key must be in the PEM format\. For example, use `ssh-keygen -m PEM` to generate the OpenSSH key in the PEM format\.

1. Save the public key to a local file\. For example, `~/.ssh/my-key-pair.pub`\. The file name extension for this file is not important\.

1. Save the private key to a local file that has the `.pem` extension\. For example, `~/.ssh/my-key-pair.pem`\.
**Important**  
Save the private key file in a safe place\. You'll need to provide the name of your public key when you launch an instance, and the corresponding private key each time you connect to the instance\.

After you have created the key pair, use one of the following methods to import your public key to Amazon EC2\.

------
#### [ Console ]

**To import the public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Choose **Import key pair**\.

1. For **Name**, enter a descriptive name for the public key\. The name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.
**Note**  
When you connect to your instance from the EC2 console, the console suggests this name for the name of your private key file\.

1. Either choose **Browse** to navigate to and select your public key, or paste the contents of your public key into the **Public key contents** field\.

1. Choose **Import key pair**\.

1. Verify that the public key that you imported appears in the list of key pairs\.

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

## Tag a public key<a name="tag-key-pair"></a>

To help categorize and manage the public keys that you've either created using Amazon EC2 or imported to Amazon EC2, you can tag them with custom metadata\. For more information about how tags work, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

You can view, add, and delete tags using one of the following methods\.

------
#### [ Console ]

**To view, add, or delete a tag for an existing public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select a public key, and then choose **Actions**, **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the public key\.
   + To add a tag, choose **Add tag**, and then enter the tag key and value\. You can add up to 50 tags per key\. For more information, see [Tag restrictions](Using_Tags.md#tag-restrictions)\.
   + To delete a tag, choose **Remove** next to the tag to delete\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To view public key tags**  
Use the [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) AWS CLI command\. In the following example, you describe the tags for all of your public keys\.

```
$ aws ec2 describe-tags --filters "Name=resource-type,Values=key-pair"
```

```
{
    "Tags": [
    {
        "Key": "Environment",
        "ResourceId": "key-0123456789EXAMPLE",
        "ResourceType": "key-pair",
        "Value": "Production"
    },
    {
        "Key": "Environment",
        "ResourceId": "key-9876543210EXAMPLE",
        "ResourceType": "key-pair",
        "Value": "Production"
    }]
}
```

**To describe the tags for a specific public key**  
Use the [ describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) AWS CLI command\.

```
$ aws ec2 describe-key-pairs --key-pair-ids key-0123456789EXAMPLE
```

```
{
    "KeyPairs": [
    {
        "KeyName": "MyKeyPair",
        "KeyFingerprint": "1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f",
        "KeyPairId": "key-0123456789EXAMPLE",
        "Tags": [
        {
            "Key": "Environment",
            "Value": "Production"
        }]			
    }]
}
```

**To tag an existing public key**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\. In the following example, the existing key is tagged with `Key=Cost-Center` and `Value=CC-123`\.

```
$ aws ec2 create-tags --resources key-0123456789EXAMPLE --tags Key=Cost-Center,Value=CC-123
```

**To delete a tag from a public key**  
Use the [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html) AWS CLI command\. For examples, see [Examples](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html#examples) in the *AWS CLI Command Reference*\.

------
#### [ PowerShell ]

**To view public key tags**  
Use the [Get\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html) command\.

**To describe the tags for a specific public key**  
Use the [Get\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) command\.

**To tag an existing public key**  
Use the [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) command\.

**To delete a tag from a public key**  
Use the [Remove\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html) command\.

------

## Retrieve the public key from the private key<a name="retrieving-the-public-key"></a>

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

If the command fails, run the following command to ensure that you've changed the permissions on your private key pair file so that only you can view it\.

```
chmod 400 my-key-pair.pem
```

## Retrieve the public key through instance metadata<a name="retrieving-the-public-key-instance"></a>

The public key that you specified when you launched an instance is also available through the instance metadata\. To view the public key that you specified when launching the instance, use the following command from your instance\.

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

If you change the key pair that you use to connect to the instance, we don't update the instance metadata to show the new public key\. Instead, the instance metadata continues to show the public key for the key pair that you specified when you launched the instance\. For more information, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

## Locate the public key on an instance<a name="locate-public-key-on-instance"></a>

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\. When your instance boots for the first time, the content of the public key that you specified at launch is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`\.

**To locate the public key on an instance**

1. [Connect to your instance\.](AccessingInstances.md)

1. In the terminal window, open the `authorized_keys` file using your favorite text editor \(such as vim or nano\)\.

   ```
   [ec2-user ~]$ nano ~/.ssh/authorized_keys
   ```

   The `authorized_keys` file opens, displaying the public key followed by the name of the key pair\. The following is an example entry for the key pair named **my\-key\-pair**\.

   ```
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
   hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
   lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
   qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
   BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
   ```

## Identify the key pair that was specified at launch<a name="identify-key-pair-specified-at-launch"></a>

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\.

**To identify the key pair that was specified at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Details** tab, under **Instance details**, the **Key pair name** field displays the name of the key pair that you specified when you launched the instance\. The value of the **Key pair name** does not change even if you change the public key on the instance, or add key pairs\.

## Verify your key pair's fingerprint<a name="verify-key-pair-fingerprints"></a>

On the **Key Pairs** page in the Amazon EC2 console, the **Fingerprint** column displays the fingerprints generated from your key pairs\. AWS calculates the fingerprint differently depending on whether the key pair was generated by AWS or a third\-party tool\. If you created the key pair using AWS, the fingerprint is calculated using an SHA\-1 hash function\. If you created the key pair with a third\-party tool and uploaded the public key to AWS, or if you generated a new public key from an existing AWS\-created private key and uploaded it to AWS, the fingerprint is calculated using an MD5 hash function\.

You can use the SSH2 fingerprint that's displayed on the **Key Pairs** page to verify that the private key you have on your local machine matches the public key stored in AWS\. From the computer where you downloaded the private key file, generate an SSH2 fingerprint from the private key file\. The output should match the fingerprint that's displayed in the console\.

If you're using a Windows local machine, you can run the following commands using the Windows Subsystem for Linux \(WSL\)\. Install the WSL and a Linux distribution using the instructions in the [Windows 10 Installation Guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10)\. The example in the instructions installs the Ubuntu distribution of Linux, but you can install any distribution\. You are prompted to restart your computer for the changes to take effect\.

If you created your key pair using AWS, you can use the OpenSSL tools to generate a fingerprint as shown in the following example\.

```
$ openssl pkcs8 -in path_to_private_key -inform PEM -outform DER -topk8 -nocrypt | openssl sha1 -c
```

If you created a key pair using a third\-party tool and uploaded the public key to AWS, you can use the OpenSSL tools to generate the fingerprint as shown in the following example\.

```
$ openssl rsa -in path_to_private_key -pubout -outform DER | openssl md5 -c
```

If you created an OpenSSH key pair using OpenSSH 7\.8 or later and uploaded the public key to AWS, you can use ssh\-keygen to generate the fingerprint as shown in the following examples\.

For RSA key pairs:

```
$ ssh-keygen -ef path_to_private_key -m PEM | openssl rsa -RSAPublicKey_in -outform DER | openssl md5 -c
```

For ED25519 key pairs:

```
$ ssh-keygen -l -f path_to_private_key.pem
```

## Add or replace a key pair for your instance<a name="replacing-key-pair"></a>

You can change the key pair that is used to access the default system account of your instance by adding a new public key on the instance, or by replacing the public key \(deleting the existing public key and adding a new one\) on the instance\. You might do this for the following reasons:
+ If a user in your organization requires access to the system user account using a separate key pair, you can add the public key to your instance\.
+ If someone has a copy of the private key \(`.pem` file\) and you want to prevent them from connecting to your instance \(for example, if they've left your organization\), you can delete the public key on the instance and replace it with a new one\.

The public keys are located in the `.ssh/authorized_keys` file on the instance\.

To add or replace a key pair, you must be able to connect to your instance\. If you've lost your existing private key or you launched your instance without a key pair, you won't be able connect to your instance and therefore won't be able to add or replace a key pair\. If you've lost your private key, you might be able to retrieve it\. For more information, see [Connect to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\. If you launched your instance without a key pair, you won't be able to connect to the instance unless you chose an AMI that is configured to allow users another way to log in\.

**Note**  
These procedures are for modifying the key pair for the default user account, such as `ec2-user`\. For information about adding user accounts to your instance, see [Manage user accounts on your Amazon Linux instance](managing-users.md)\.

**To add or replace a key pair**

1. Create a new key pair using the [Amazon EC2 console](#having-ec2-create-your-key-pair) or a [third\-party tool](#how-to-generate-your-own-key-and-import-it-to-aws)\.

1. Retrieve the public key from your new key pair\. For more information, see [Retrieve the public key from the private key](#retrieving-the-public-key)\.

1. [Connect to your instance](AccessingInstances.md) using your existing private key\.

1. Using a text editor of your choice, open the `.ssh/authorized_keys` file on the instance\. Paste the public key information from your new key pair underneath the existing public key information\. Save the file\.

1. Disconnect from your instance, and test that you can connect to your instance using the new private key file\.

1. \(Optional\) If you're replacing an existing key pair, connect to your instance and delete the public key information for the original key pair from the `.ssh/authorized_keys` file\.

**Note**  
If you're using an Auto Scaling group, ensure that the key pair you're replacing is not specified in your launch template or launch configuration\. If Amazon EC2 Auto Scaling detects an unhealthy instance, it launches a replacement instance\. However, the instance launch fails if the key pair cannot be found\. For more information, see [Launch templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html) in the *Amazon EC2 Auto Scaling User Guide*\.

## Delete your key pair<a name="delete-key-pair"></a>

When you delete a key pair using the following methods, you are only deleting the public key that you saved in Amazon EC2 when you [created](#having-ec2-create-your-key-pair) or [imported](#how-to-generate-your-own-key-and-import-it-to-aws) the key pair\. Deleting a key pair doesn't delete the public key from any instances that were previously launched using that key pair\. It also doesn't delete the private key on your local computer\. You can continue to connect to instances that you launched using a key pair that is subsequently deleted, as long as you still have the private key \(`.pem`\) file\.

**Note**  
To delete the public key from an instance, see [Delete a public key from an instance](#delete-public-key-from-instance)\.

If you're using an Auto Scaling group \(for example, in an Elastic Beanstalk environment\), ensure that the key pair you're deleting is not specified in an associated launch template or launch configuration\. If Amazon EC2 Auto Scaling detects an unhealthy instance, it launches a replacement instance\. However, the instance launch fails if the key pair cannot be found\. For more information, see [Launch templates](https://docs.aws.amazon.com/autoscaling/ec2/userguide/LaunchTemplates.html) in the *Amazon EC2 Auto Scaling User Guide*\.

You can delete a key pair using one of the following methods\.

------
#### [ Console ]

**To delete your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select the key pair to delete and choose **Delete**\.

1. In the confirmation field, enter `Delete` and then choose **Delete**\.

------
#### [ AWS CLI ]

**To delete your key pair**  
Use the [delete\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-key-pair.html) AWS CLI command\.

------
#### [ PowerShell ]

**To delete your key pair**  
Use the [Remove\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2KeyPair.html) AWS Tools for Windows PowerShell command\.

------

## Delete a public key from an instance<a name="delete-public-key-from-instance"></a>

If you create a Linux AMI from an instance, the public key information is copied from the instance to the AMI\. If you launch an instance from the AMI, the new instance includes the public key from the original instance\. To prevent someone who has the private key from connecting to the new instance, delete the public key from the original instance *before* creating the AMI\.

**To delete a public key from an instance**

1. [Connect to your instance](AccessingInstances.md)\.

1. Using a text editor of your choice, open the `.ssh/authorized_keys` file on the instance\. Delete the public key information, and then save the file\.

**Warning**  
After you delete the public key from the instance and disconnect from the instance, you can't connect to it again unless the AMI provides another way of logging in\.
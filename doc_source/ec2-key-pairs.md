# Amazon EC2 key pairs and Linux instances<a name="ec2-key-pairs"></a>

A key pair, consisting of a private key and a public key, is a set of security credentials that you use to prove your identity when connecting to an instance\. Amazon EC2 stores the public key, and you store the private key\. You use the private key, instead of a password, to securely access your instances\. Anyone who possesses your private keys can connect to your instances, so it's important that you store your private keys in a secure place\.

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\. You can choose an existing key pair or create a new one\. When your instance boots for the first time, the content of the public key that you specified at launch is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`\. When you connect to your Linux instance using SSH, to log in you must specify the private key that corresponds to the public key content\. For more information about connecting to your instance, see [Connect to your Linux instance](AccessingInstances.md)\. For more information about key pairs and Windows instances, see [Amazon EC2 key pairs and Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Windows Instances*

Because Amazon EC2 doesn't keep a copy of your private key, there is no way to recover a private key if you lose it\. However, there can still be a way to connect to instances for which you've lost the private key\. For more information, see [Connecting to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\.

The keys that Amazon EC2 uses are 2048\-bit SSH\-2 RSA keys\. You can have up to 5,000 key pairs per Region\.

**Topics**
+ [Creating or importing a key pair](#prepare-key-pair)
+ [Tagging a key pair](#tag-key-pair)
+ [Retrieving the public key for your key pair](#retrieving-the-public-key)
+ [Retrieving the public key for your key pair through instance metadata](#retrieving-the-public-key-instance)
+ [Locating the public key on an instance](#locate-public-key-on-instance)
+ [Identifying the key pair that was specified at launch](#identify-key-pair-specified-at-launch)
+ [\(Optional\) Verifying your key pair's fingerprint](#verify-key-pair-fingerprints)
+ [Adding or replacing a key pair for your instance](#replacing-key-pair)
+ [Connecting to your Linux instance if you lose your private key](replacing-lost-key-pair.md)
+ [Deleting your key pair](#delete-key-pair)

## Creating or importing a key pair<a name="prepare-key-pair"></a>

You can use Amazon EC2 to create a new key pair, or you can import an existing key pair\.

**Topics**
+ [Option 1: Create a key pair using Amazon EC2](#having-ec2-create-your-key-pair)
+ [Option 2: Import your own public key to Amazon EC2](#how-to-generate-your-own-key-and-import-it-to-aws)

### Option 1: Create a key pair using Amazon EC2<a name="having-ec2-create-your-key-pair"></a>

You can create a key pair using one of the following methods\. 

------
#### [ New console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Choose **Create key pair**\.

1. For **Name**, enter a descriptive name for the key pair\. Amazon EC2 associates the public key with the name that you specify as the key name\. A key name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. For **File format**, choose the format in which to save the private key\. To save the private key in a format that can be used with OpenSSH, choose **pem**\. To save the private key in a format that can be used with PuTTY, choose **ppk**\.

1. Choose **Create key pair**\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is determined by the file format you chose\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\.

1. If you will use an SSH client on a macOS or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   chmod 400 my-key-pair.pem
   ```

   If you do not set these permissions, then you cannot connect to your instance using this key pair\. For more information, see [Error: Unprotected private key file](TroubleshootingInstancesConnecting.md#troubleshoot-unprotected-key)\.

------
#### [ Old console ]

**To create your key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Choose **Create Key Pair**\.

1. For **Key pair name**, enter a descriptive name for the key pair, and then choose **Create**\. A key name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
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

1. Use the [create\-key\-pair](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-key-pair.html) AWS CLI command as follows to generate the key and save it to a `.pem` file\.

   ```
   aws ec2 create-key-pair --key-name my-key-pair --query "KeyMaterial" --output text > my-key-pair.pem
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

```
PS C:\> (New-EC2KeyPair -KeyName "my-key-pair").KeyMaterial | Out-File -Encoding ascii -FilePath C:\path\my-key-pair.pem
```

------

### Option 2: Import your own public key to Amazon EC2<a name="how-to-generate-your-own-key-and-import-it-to-aws"></a>

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

1. For **Name**, enter a descriptive name for the key pair\. The name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

1. Either choose **Browse** to navigate to and select your public key, or paste the contents of your public key into the **Public key contents** field\.

1. Choose **Import key pair**\.

1. Verify that the key pair you imported appears in the list of key pairs\.

------
#### [ Old console ]

**To import the public key**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **NETWORK & SECURITY**, choose **Key Pairs**\.

1. Choose **Import Key Pair**\. 

1. In the **Import Key Pair** dialog box, choose **Browse**, and select the public key file that you saved previously\. Enter a name for the key pair in the **Key pair name** field, and choose **Import**\. The name can include up to 255 ASCII characters\. It can’t include leading or trailing spaces\.

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

## Tagging a key pair<a name="tag-key-pair"></a>

To help categorize and manage your existing key pairs, you can tag them with custom metadata\. For more information about how tags work, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

You can view, add, and delete tags using the new console and the command line tools\.

------
#### [ New console ]

**To view, add, or delete a tag for an existing key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Key Pairs**\.

1. Select a key pair, and then choose **Actions**, **Manage tags**\.

1. The **Manage tags** page displays any tags that are assigned to the key pair\.
   + To add a tag, choose **Add tag**, and then enter the tag key and value\. You can add up to 50 tags per key pair\. For more information, see [Tag restrictions](Using_Tags.md#tag-restrictions)\.
   + To delete a tag, choose **Remove** next to the tag to delete\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To view key pair tags**  
Use the [describe\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) AWS CLI command\. In the following example, you describe the tags for all of your key pairs\.

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

**To describe the tags for a specific key pair**  
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

**To tag an existing key pair**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\. In the following example, the existing key pair is tagged with `Key=Cost-Center` and `Value=CC-123`\.

```
$ aws ec2 create-tags --resources key-0123456789EXAMPLE --tags Key=Cost-Center,Value=CC-123
```

**To delete a tag from a key pair**  
Use the [delete\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html) AWS CLI command\. For examples, see [Examples](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html#examples) in the *AWS CLI Command Reference*\.

------
#### [ PowerShell ]

**To view key pair tags**  
Use the [Get\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html) command\.

**To describe the tags for a specific key pair**  
Use the [Get\-EC2KeyPair](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) command\.

**To tag an existing key pair**  
Use the [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) command\.

**To delete a tag from a key pair**  
Use the [Remove\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html) command\.

------

## Retrieving the public key for your key pair<a name="retrieving-the-public-key"></a>

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

## Retrieving the public key for your key pair through instance metadata<a name="retrieving-the-public-key-instance"></a>

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

If you change the key pair that you use to connect to the instance, we don't update the instance metadata to show the new public key\. Instead, the instance metadata continues to show the public key for the key pair that you specified when you launched the instance\. For more information, see [Retrieving instance metadata](instancedata-data-retrieval.md)\.

Alternatively, on a Linux instance, the public key content is placed in an entry within `~/.ssh/authorized_keys`\. You can open this file in an editor\. The following is an example entry for the key pair named **my\-key\-pair**\. It consists of the public key followed by the name of the key pair\.

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE my-key-pair
```

## Locating the public key on an instance<a name="locate-public-key-on-instance"></a>

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\. You can choose an existing key pair or create a new one\. When your instance boots for the first time, the content of the public key that you specified at launch is placed on your Linux instance in an entry within `~/.ssh/authorized_keys`\.

**To locate the public key on an instance**

1. Connect to your instance\. For more information, see [Connect to your Linux instance](AccessingInstances.md)\.

1. In the terminal window, open the `authorized_keys` file using your favorite text editor \(such as vim or nano\)\.

   ```
   [ec2-user ~]$ nano ~/.ssh/authorized_keys
   ```

   The `authorized_keys` file opens, displaying the public key, as shown in the following example\.

   ```
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6Vhz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXrlsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZqaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3RbBQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE
   ```

## Identifying the key pair that was specified at launch<a name="identify-key-pair-specified-at-launch"></a>

When you launch an instance, you are [prompted for a key pair](launching-instance.md#step-7-review-instance-launch)\. If you plan to connect to the instance using SSH, you must specify a key pair\.

------
#### [ New console ]

**To identify the key pair that was specified at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Details** tab, under **Instance details**, the **Key pair name** field displays the name of the key pair that you specified when you launched the instance\. The value of the **Key pair name** does not change even if you change the public key on the instance, or add key pairs\.

------
#### [ Old console ]

**To identify the key pair that was specified at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Description** tab, the **Key pair name** field displays the name of the key pair that you specified when you launched the instance\. The value of the **Key pair name** does not change even if you change the public key on the instance, or add key pairs\.

------

## \(Optional\) Verifying your key pair's fingerprint<a name="verify-key-pair-fingerprints"></a>

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

## Adding or replacing a key pair for your instance<a name="replacing-key-pair"></a>

You can change the key pair that is used to access the default system account of your instance\. For example, if a user in your organization requires access to the system user account using a separate key pair, you can add that key pair to your instance\. Or, if someone has a copy of the `.pem` file and you want to prevent them from connecting to your instance \(for example, if they've left your organization\), you can replace the key pair with a new one\.

To add or replace a key pair, you must be able to connect to your instance\. If you've lost your existing private key or you launched your instance without a key pair, you won't be able connect to your instance and therefore won't be able to add or replace a key pair\. If you've lost your existing private key, you might be able to retrieve it\. For more information, see [Connecting to your Linux instance if you lose your private key](replacing-lost-key-pair.md)\. If you launched your instance without a key pair, you won't be able to connect to the instance unless you chose an AMI that is configured to allow users another way to log in\.

**Note**  
These procedures are for modifying the key pair for the default user account, such as `ec2-user`\. For more information about adding user accounts to your instance, see [Managing user accounts on your Amazon Linux instance](managing-users.md)\.

**To add or replace a key pair**

1. Create a new key pair using [the Amazon EC2 console](#having-ec2-create-your-key-pair) or a [third\-party tool](#how-to-generate-your-own-key-and-import-it-to-aws)\.

1. Retrieve the public key from your new key pair\. For more information, see [Retrieving the public key for your key pair](#retrieving-the-public-key)\.

1. Connect to your instance using your existing private key file\.

1. Using a text editor of your choice, open the `.ssh/authorized_keys` file on the instance\. Paste the public key information from your new key pair underneath the existing public key information\. Save the file\.

1. Disconnect from your instance, and test that you can connect to your instance using the new private key file\.

1. \(Optional\) If you're replacing an existing key pair, connect to your instance and delete the public key information for the original key pair from the `.ssh/authorized_keys` file\.

**Note**  
If you're using an Auto Scaling group, ensure that the key pair you're replacing is not specified in your launch template or launch configuration\. Amazon EC2 Auto Scaling launches a replacement instance if it detects an unhealthy instance; however, the instance launch fails if the key pair cannot be found\. 

## Deleting your key pair<a name="delete-key-pair"></a>

When you delete a key pair, you are only deleting the Amazon EC2 copy of the public key\. Deleting a key pair doesn't affect the private key on your computer or the public key on any instances that already launched using that key pair\. You can't launch a new instance using a deleted key pair, but you can continue to connect to any instances that you launched using a deleted key pair, as long as you still have the private key \(`.pem`\) file\.

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

If you create a Linux AMI from an instance, and then use the AMI to launch a new instance in a different Region or account, the new instance includes the public key from the original instance\. This enables you to connect to the new instance using the same private key file as your original instance\. You can remove this public key from your instance by removing its entry from the `.ssh/authorized_keys` file using a text editor of your choice\. For more information about managing users on your instance and providing remote access using a specific key pair, see [Managing user accounts on your Amazon Linux instance](managing-users.md)\.

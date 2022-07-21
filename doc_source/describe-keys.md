# Describe public keys<a name="describe-keys"></a>

You can describe the public keys that are stored in Amazon EC2\. You can also retrieve the public key material and identify the public key that was specified at launch\.

**Topics**
+ [Describe public keys](#describe-public-key)
+ [Retrieve the public key material](#retrieving-the-public-key)
+ [Identify the public key specified at launch](#identify-key-pair-specified-at-launch)

## Describe public keys<a name="describe-public-key"></a>

You can view the following information about your public keys that are stored in Amazon EC2: public key name, ID, key type, fingerprint, public key material, the date and time \(in the UTC time zone\) the key was created by Amazon EC2 \(if the key was created by a third\-party tool, then it's the date and time the key was imported to Amazon EC2\), and any tags that are associated with the public key\.

You can use the Amazon EC2 console or AWS CLI to view information about your public keys\.

------
#### [ Console ]

**To view information about your public keys**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigator, choose **Key Pairs**\.

1. You can view the information about each public key in the **Key pairs** table\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/key-pairs-describe-console.png)

1. To view a public key's tags, select the check box next to the key, and then choose **Actions**, **Manage tags**\.

------
#### [ AWS CLI ]

**To describe a public key**  
Use the [describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) command and specify the `--key-names` parameter\.

```
aws ec2 describe-key-pairs --key-names key-pair-name
```

Example output

```
{
    "KeyPairs": [
        {
            "KeyPairId": "key-0123456789example",
            "KeyFingerprint": "1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f",
            "KeyName": "key-pair-name",
            "KeyType": "rsa",
            "Tags": [],
            "CreateTime": "2022-04-28T11:37:26.000Z"
        }
    ]
}
```

Alternatively, instead of `--key-names`, you can specify the `--key-pair-ids` parameter to identify the public key\.

```
aws ec2 describe-key-pairs --key-pair-ids key-0123456789example
```

To view the public key material in the output, you must specify the `--include-public-key` parameter\.

```
aws ec2 describe-key-pairs --key-names key-pair-name --include-public-key
```

Example output – In the output, the `PublicKey` field contains the public key material\. 

```
{
    "KeyPairs": [
        {
            "KeyPairId": "key-0123456789example",
            "KeyFingerprint": "1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f",
            "KeyName": "key-pair-name",
            "KeyType": "rsa",
            "Tags": [],
            "PublicKey": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIj7azlDjVHAsSxgcpCRZ3oWnTm0nAFM64y9jd22ioI/ my-key-pair",
            "CreateTime": "2022-04-28T11:37:26.000Z"
        }
    ]
}
```

------

## Retrieve the public key material<a name="retrieving-the-public-key"></a>

You can use various methods to get access to the public key material\. You can retrieve the public key material from the matching private key on your local computer, or from the instance metadata or the `authorized_keys` file on the instance that was launched with the public key, or by using the `describe-key-pairs` AWS CLI command\.

Use one of the following methods to retrieve the public key material\.

------
#### [ From the private key ]

**To retrieve the public key material from the private key**  
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
chmod 400 key-pair-name.pem
```

------
#### [ From the instance metadata ]

You can use Instance Metadata Service Version 2 or Instance Metadata Service Version 1 to retrieve the public key from the instance metadata\.

**Note**  
If you change the key pair that you use to connect to the instance, Amazon EC2 does not update the instance metadata to show the new public key\. The instance metadata continues to show the public key for the key pair that you specified when you launched the instance\.

**To retrieve the public key material from the instance metadata**  
Use one of the following commands from your instance\.

**IMDSv2**

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
```

**IMDSv1**

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
```

Example output

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE key-pair-name
```

For more information about instance metadata, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

------
#### [ From the instance ]

If you specify a key pair when launching a Linux instance, when the instance boots for the first time, the content of the public key is placed on the instance in an entry within `~/.ssh/authorized_keys`\.

**To retrieve the public key material from an instance**

1. [Connect to your instance\.](AccessingInstances.md)

1. In the terminal window, open the `authorized_keys` file using your favorite text editor \(such as vim or nano\)\.

   ```
   [ec2-user ~]$ nano ~/.ssh/authorized_keys
   ```

   The `authorized_keys` file opens, displaying the public key followed by the name of the key pair\. The following is an example entry for the key pair named *`key-pair-name`*\.

   ```
   ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQClKsfkNkuSevGj3eYhCe53pcjqP3maAhDFcvBS7O6V
   hz2ItxCih+PnDSUaw+WNQn/mZphTk/a/gU8jEzoOWbkM4yxyb/wB96xbiFveSFJuOp/d6RJhJOI0iBXr
   lsLnBItntckiJ7FbtxJMXLvvwJryDUilBMTjYtwB+QhYXUMOzce5Pjz5/i8SeJtjnV3iAoG/cQk+0FzZ
   qaeJAAHco+CY/5WrUBkrHmFJr6HcXkvJdWPkYQS3xqC0+FmUZofz221CBt5IMucxXPkX4rWi+z7wB3Rb
   BQoQzd8v7yeb7OzlPnWOyN0qFU0XA246RA8QFYiCNYwI3f05p6KLxEXAMPLE key-pair-name
   ```

------
#### [ From describe\-key\-pairs ]

**To retrieve the public key material from the `describe-key-pairs`AWS CLI command**  
Use the [describe\-key\-pairs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) command and specify the `--key-names` parameter to identify the public key\. To include the public key material in the output, specify the `--include-public-key` parameter\.

```
aws ec2 describe-key-pairs --key-names key-pair-name --include-public-key
```

Example output – In the output, the `PublicKey` field contains the public key material\. 

```
{
    "KeyPairs": [
        {
            "KeyPairId": "key-0123456789example",
            "KeyFingerprint": "1f:51:ae:28:bf:89:e9:d8:1f:25:5d:37:2d:7d:b8:ca:9f:f5:f1:6f",
            "KeyName": "key-pair-name",
            "KeyType": "rsa",
            "Tags": [],
            "PublicKey": "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIj7azlDjVHAsSxgcpCRZ3oWnTm0nAFM64y9jd22ioI/ my-key-pair",
            "CreateTime": "2022-04-28T11:37:26.000Z"
        }
    ]
}
```

Alternatively, instead of `--key-names`, you can specify the `--key-pair-ids` parameter to identify the public key\.

```
aws ec2 describe-key-pairs --key-pair-ids key-0123456789example --include-public-key
```

------

## Identify the public key specified at launch<a name="identify-key-pair-specified-at-launch"></a>

If you specify a public key when you launch an instance, the public key name is recorded by the instance\.

**To identify the public key that was specified at launch**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Details** tab, under **Instance details**, the **Key pair name** field displays the name of the public key that you specified when you launched the instance\.

**Note**  
The value of the **Key pair name** field does not change even if you change the public key on the instance, or add public keys\.
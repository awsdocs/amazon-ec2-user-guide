# Tag a public key<a name="tag-key-pair"></a>

To help categorize and manage the public keys that you've either created using Amazon EC2 or imported to Amazon EC2, you can tag them with custom metadata\. For more information about how tags work, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

You can view, add, and delete tags using one of the following methods\.

------
#### [ Console ]

**To view, add, or delete a tag for a public key**

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
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-tags.html) AWS CLI command\. In the following example, you describe the tags for all of your public keys\.

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
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-key-pairs.html) AWS CLI command\.

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

**To tag a public key**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\. In the following example, the public key is tagged with `Key=Cost-Center` and `Value=CC-123`\.

```
$ aws ec2 create-tags --resources key-0123456789EXAMPLE --tags Key=Cost-Center,Value=CC-123
```

**To delete a tag from a public key**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html) AWS CLI command\. For examples, see [Examples](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-tags.html#examples) in the *AWS CLI Command Reference*\.

------
#### [ PowerShell ]

**To view public key tags**  
Use the [https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Tag.html) command\.

**To describe the tags for a specific public key**  
Use the [https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2KeyPair.html) command\.

**To tag a public key**  
Use the [https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) command\.

**To delete a tag from a public key**  
Use the [https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Tag.html) command\.

------
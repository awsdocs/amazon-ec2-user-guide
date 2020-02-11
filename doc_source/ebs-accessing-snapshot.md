# Accessing the Contents of an EBS Snapshot<a name="ebs-accessing-snapshot"></a>

You can use the Amazon Elastic Block Store \(EBS\) direct APIs to directly read the data on your EBS snapshots, and identify the difference between two snapshots\. You can view the details of blocks in an EBS snapshot, compare the block difference between two snapshots, and directly access the data in a snapshot\. If you’re an independent software vendor \(ISV\) who offers backup services for EBS, the EBS direct APIs makes it easier and more cost\-effective to track incremental changes on your EBS volumes via EBS snapshots\. This can be done without having to create new volumes from EBS snapshots, and then use EC2 instances to compare the differences\.

This user guide provides an overview of the elements that make up the EBS direct APIs, and examples of how to use them effectively\. For more information about the actions, data types, parameters, and errors of the APIs, see the [EBS direct APIs reference](https://docs.aws.amazon.com/ebs/latest/APIReference/)\. For more information about the supported AWS Regions, endpoints, and service quotas for the EBS direct APIs, see [Amazon Elastic Block Store Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html) in the *AWS General Reference*\.

**Topics**
+ [Understanding the EBS direct APIs](#ebsapi-elements)
+ [Permissions for IAM Users](#ebsapi-permissions)
+ [Working with EBS direct APIs Using the Command Line](#ebsapi-examples)
+ [Working with the EBS direct APIs Using the API or AWS SDKs](#ebsapi-sdk)
+ [Frequently Asked Questions for the EBS direct APIs](#ebsapi-faq)

## Understanding the EBS direct APIs<a name="ebsapi-elements"></a>

The following are the key elements that you should understand before getting started with the EBS direct APIs\.

### Snapshots<a name="ebsapi-snapshots"></a>

Snapshots are the primary means to back up data from your EBS volumes\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. For more information, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

**Note**  
Public snapshots are not supported by the EBS direct APIs\.

### Blocks<a name="ebsapi-blocks"></a>

A block is a fragment of data within a snapshot\. Each snapshot can contain thousands of blocks\. All blocks in a snapshot are of a fixed size\.

### Block Indexes<a name="ebsapi-block-indexes"></a>

A block index is the offset position of a block within a snapshot, and it is used to identify the block\. Multiply the `BlockIndex` value with the `BlockSize` value \(`BlockIndex` \* `BlockSize`\) to identify the logical offset of the data in the logical block\.

### Block Tokens<a name="ebsapi-block-tokens"></a>

A block token is the identifying hash of a block within a snapshot, and it is used to locate the block data\.

**Note**  
Block tokens returned by EBS direct APIs are temporary\. Block tokens change if you run another `ListSnapshotBlocks` or `ListChangedBlocks` request for the same snapshot\.

### List Snapshot Blocks<a name="ebsapi-listsnapshotblocks"></a>

The `ListSnapshotBlocks` API operation returns the block indexes and block tokens for blocks in the specified snapshot\. For more information, see [ListSnapshotBlocks](https://docs.aws.amazon.com/ebs/latest/APIReference/API_ListSnapshotBlocks.html) in the *EBS direct APIs reference*\.

### List Changed Blocks<a name="ebsapi-listchangedblocks"></a>

The `ListChangedBlocks` API operation returns the block indexes and block tokens for blocks that are different between two specified snapshots of the same volume/snapshot lineage\. For more information, see [ListChangedBlocks](https://docs.aws.amazon.com/ebs/latest/APIReference/API_ListChangedBlocks.html) in the *EBS direct APIs reference*\.

### Get Snapshot Blocks<a name="ebsapi-getsnapshotblocks"></a>

The `GetSnapshotBlock` API operation returns the data in a block for the specified snapshot ID, block index, and block token\. For more information, see [GetSnapshotBlock](https://docs.aws.amazon.com/ebs/latest/APIReference/API_GetSnapshotBlock.html) in the *EBS direct APIs reference*\.

### Using the APIs<a name="ebsapi-using-operations"></a>

Use the `ListSnapshotBlocks` or `ListChangedBlocks` API operations to identify the block indexes and block tokens of blocks for which you want to get data\. Then, use the `GetSnapshotBlock` API operation to get the data from the blocks in a snapshot\. Examples for how to run these operations using the AWS CLI are provided later in this guide\.

## Permissions for IAM Users<a name="ebsapi-permissions"></a>

An IAM user must have the following policies to use the EBS direct APIs\.

**Important**  
Exercise caution when assigning the following policies to IAM users\. By assigning these policies, you might give access to a user who is denied access to the same resource via the EC2 APIs, such as the CopySnapshot or CreateVolume operations\.

The following policy grants full access to the EBS direct APIs\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:*"
            ],
            "Resource": "arn:aws:ec2:*::snapshot/*"
        }
    ]
}
```

The following policy grants access to a specific snapshot in a specific AWS Region\. In the policy, replace *<SnapshotID>* with the ID of the snapshot, and *<Region>* with the region of the snapshot\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:ListSnapshotBlocks"
            ],
            "Resource": "arn:aws:ec2:<Region>::snapshot/<SnapshotID>"
        }
    ]
}
```

The following policy grants access for the EBS direct APIs to snapshots with a specific key/value tag\. In the policy, replace *<Key>* with the key value of the tag, and *<Value>* with the value of the tag\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:ListChangedBlocks",
                "ebs:ListSnapshotBlocks",
                "ebs:GetSnapshotBlock"
            ],
            "Resource": "arn:aws:ec2:*::snapshot/*",
            "Condition": {
                "StringEqualsIgnoreCase": {
                    "aws:ResourceTag/<Key>": "<Value>"
                }
            }
        }
    ]
}
```

The following permission denies access for EBS direct APIs to a specific snapshot\. In the policy, replace *<SnapshotID>* with the ID of the snapshot\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:ListSnapshotBlocks",
                "ebs:ListChangedBlocks",
                "ebs:GetSnapshotBlock"
            ],
            "Resource": "arn:aws:ec2:*::snapshot/*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ebs:ListSnapshotBlocks",
                "ebs:ListChangedBlocks",
                "ebs:GetSnapshotBlock"
            ],
            "Resource": "arn:aws:ec2:us-east-2::<SnapshotID>"
        }
    ]
}
```

The following policy grants access to all snapshots within a specific time range\. In the policy, be sure to replace the date and time range shown with the date and time range for your policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:ListChangedBlocks",
                "ebs:ListSnapshotBlocks",
                "ebs:GetSnapshotBlock"
            ],
            "Resource": "arn:aws:ec2:*::snapshot/*",
            "Condition": {
                "DateGreaterThan": {
                    "aws:CurrentTime": "2018-05-29T00:00:00Z"
                },
                "DateLessThan": {
                    "aws:CurrentTime": "2020-05-29T23:59:59Z"
                }
            }
        }
    ]
}
```

The following policy grants access to decrypt an encrypted snapshot using a specific key ID from the AWS Key Management Service \(AWS KMS\)\. In the policy, replace *<AccountId>* with the ID of the AWS account for the AWS KMS key, and *<KeyId>* with the ID of the key used to encrypt the snapshot that you want to access with the EBS direct APIs\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt",
                "kms:DescribeKey"
            ],
            "Resource": "arn:aws:kms:us-west-2:<AccountId>:key/<KeyId>"
        }
    ]
}
```

For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.

## Working with EBS direct APIs Using the Command Line<a name="ebsapi-examples"></a>

The following examples show how to use the EBS direct APIs using the AWS Command Line Interface \(AWS CLI\)\. For more information about installing and configuring the AWS CLI, see [Installing the AWS CLI version 1](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv1.html) and [Quickly Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration)\.

**Example Example: Get the block indexes and block tokens for blocks that are in a snapshot**  <a name="listsnapshotblocks-cli"></a>
The following `list-snapshot-blocks` command example returns the block indexes and block tokens for blocks that are in snapshot **snap\-0987654321**, in the **us\-east\-1** AWS Region\. The `--starting-block-index` and `--max-results` parameters limit the results to the first **100** blocks with a block index greater than **1000**\.  

```
aws ebs list-snapshot-blocks --region us-east-1 --snapshot-id snap-0987654321 --starting-block-index 1000 --max-results 100
```
The following is an example response\. To get the data in a block, use the `get-snapshot-block` command and specify the block index and block token for the block\. The block tokens are valid until the expiry time listed\.  

```
{
    "Blocks": [
        {
            "BlockIndex": 1001,
            "BlockToken": "AAABAV3/PNhXOynVdMYHUpPsetaSvjLB1dtIGfbJv5OJ0sX855EzGTWos4a4"
        },
        {
            "BlockIndex": 1002,
            "BlockToken": "AAABATGQIgwr0WwIuqIMjCA/Sy7e/YoQFZsHejzGNvjKauzNgzeI13YHBfQB"
        },
        {
            "BlockIndex": 1007,
            "BlockToken": "AAABAZ9CTuQtUvp/dXqRWw4d07eOgTZ3jvn6hiW30W9duM8MiMw6yQayzF2c"
        },
        {
            "BlockIndex": 1012,
            "BlockToken": "AAABAQdzxhw0rVV6PNmsfo/YRIxo9JPR85XxPf1BLjg0Hec6pygYr6laE1p0"
        },
        {
            "BlockIndex": 1030,
            "BlockToken": "AAABAaYvPax6mv+iGWLdTUjQtFWouQ7Dqz6nSD9L+CbXnvpkswA6iDID523d"
        },
        {
            "BlockIndex": 1031,
            "BlockToken": "AAABATgWZC0XcFwUKvTJbUXMiSPg59KVxJGL+BWBClkw6spzCxJVqDVaTskJ"
        },
        ...
    ],
    "ExpiryTime": 1576287332.806,
    "VolumeSize": 32212254720,
    "BlockSize": 524288
}
```

**Example Example: Get the block indexes and block tokens for blocks that are different between two snapshots of the same volume/snapshot lineage**  <a name="listchangedblocks-cli"></a>
The following `list-changed-blocks` command example returns the block indexes and block tokens for blocks that are different between snapshots **snap\-1234567890** and **snap\-0987654321**, in the **us\-east\-1** AWS Region\. The `--starting-block-index` and `--max-results` parameters limit the results to the first **500** blocks with a block index greater than **0**\.\.  

```
aws ebs list-changed-blocks --region us-east-1 --first-snapshot-id snap-1234567890 --second-snapshot-id snap-0987654321 --starting-block-index 0 --max-results 500
```
The following is an example response\. It shows that block indexes 0, 6000, 6001, 6002, and 6003 are different between the two snapshots\. Additionally, block indexes 6001, 6002, and 6003 exist only in the first snapshot ID specified, and not in the second snapshot ID because there is no second block token listed in the response\.  
To get the data in a block, use the `get-snapshot-block` command and specify the block index and block token for the block\. The block tokens are valid until the expiry time listed\.  

```
{
    "ChangedBlocks": [
        {
            "BlockIndex": 0,
            "FirstBlockToken": "AAABAVahm9SO60Dyi0ORySzn2ZjGjW/KN3uygGlS0QOYWesbzBbDnX2dGpmC",
            "SecondBlockToken": "AAABAf8o0o6UFi1rDbSZGIRaCEdDyBu9TlvtCQxxoKV8qrUPQP7vcM6iWGSr"
        },
        {
            "BlockIndex": 6000,
            "FirstBlockToken": "AAABAbYSiZvJ0/R9tz8suI8dSzecLjN4kkazK8inFXVintPkdaVFLfCMQsKe",
            "SecondBlockToken": "AAABAZnqTdzFmKRpsaMAsDxviVqEI/3jJzI2crq2eFDCgHmyNf777elD9oVR"
        },
        {
            "BlockIndex": 6001,
            "FirstBlockToken": "AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR"
        },
        {
            "BlockIndex": 6002,
            "FirstBlockToken": "AAABASqX4/NWjvNceoyMUljcRd0DnwbSwNnes1UkoP62CrQXvn47BY5435aw"
        },
        {
            "BlockIndex": 6003,
            "FirstBlockToken": "AAABASmJ0O5JxAOce25rF4P1sdRtyIDsX12tFEDunnePYUKOf4PBROuICb2A"
        },
        ...
    ],
    "ExpiryTime": 1576308931.973,
    "VolumeSize": 32212254720,
    "BlockSize": 524288,
    "NextToken": "AAADARqElNng/sV98CYk/bJDCXeLJmLJHnNSkHvLzVaO0zsPH/QM3Bi3zF//O6Mdi/BbJarBnp8h"
}
```

**Example Example: Get the data in a block**  <a name="getsnapshotblock-cli"></a>
The following `get-snapshot-block` command example returns the data in the block index **6001** with block token **AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR**, in snapshot **snap\-1234567890**, in the **us\-east\-1** AWS Region\. The binary data will be output to the `output.txt` file in the **C:\\Temp** directory on a Windows computer\. If you run the command on a Linux or Unix computer, replace the output path with **/tmp/output\.txt** to output the data to the `output.txt` file in the **/tmp** directory\.  

```
aws ebs get-snapshot-block --region us-east-1 --snapshot-id snap-1234567890 --block-index 6001 --block-token AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR C:/Temp/output.txt
```
The following is an example response\. It shows the size of the data returned, the checksum to validate the data, and the checksum algorithm used to generate the checksum\. The binary data is automatically saved to the directory and file you specified in the request command\.  

```
{
    "DataLength": "524288",
    "Checksum": "cf0Y6/Fn0oFa4VyjQPOa/iD0zhTflPTKzxGv2OKowXc=",
    "ChecksumAlgorithm": "SHA256"
}
```

## Working with the EBS direct APIs Using the API or AWS SDKs<a name="ebsapi-sdk"></a>

The [EBS direct APIs Reference](https://docs.aws.amazon.com/ebs/latest/APIReference/) provides descriptions and syntax for each of the service’s actions and data types\. You can also use one of the AWS SDKs to access an API that's tailored to the programming language or platform that you're using\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.

The EBS direct APIs require an AWS Signature Version 4 signature\. For more information about creating these signatures, see [Signature Version 4 Signing Process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) in the *AWS General Reference*\.

You need to learn how to sign HTTP requests only if you intend to manually create them\. When you use the AWS Command Line Interface \(AWS CLI\) or one of the AWS SDKs to make requests to AWS, these tools automatically sign the requests for you with the access key that you specify when you configure the tools\. When you use these tools, you don't need to learn how to sign requests yourself\. 

## Frequently Asked Questions for the EBS direct APIs<a name="ebsapi-faq"></a>

**Can a snapshot be accessed using the EBS direct APIs if it has a pending status?**  
No\. The snapshot can only be accessed if it has a completed status\.

**Are the block indexes returned by the EBS direct APIs in numerical order?**  
Yes\. The block indexes returned are unique, and in numerical order\.

**Can I submit a request with a `MaxResults` parameter value of under 100?**  
No\. The minimum `MaxResult` parameter value you can use is 100\. If you submit a request with a `MaxResult` parameter value of under 100, and there are more than 100 blocks in the snapshot, then the API will return at least 100 results\.

**Can I run API requests concurrently?**  
You can run API requests concurrently\. Be sure to account for other workloads that may be running in the account to avoid bottlenecks\. You should also build retry mechanisms into your EBS direct APIs workflows to handle throttling, timeouts, and service unavailability\.

**When running the `ListChangedBlocks` operation, is it possible to get an empty response even though there are blocks in the snapshot?**  
Yes\. If the changed blocks are few and far between in the snapshot, the response may be empty but the API will return a next page token value\. Use the next page token value to continue to the next page of results\. You can confirm that you have reached the last page of results when the API returns a next page token value of null\.

**If the `NextToken` parameter is specified together with a `StartingBlockIndex` parameter, which of the two is used?**  
The `NextToken` is used, and the `StartingBlockIndex` is ignored\.

**How long are the block tokens and next tokens valid?**  
Block tokens are valid for seven days, and next tokens are valid for 60 minutes\.

**Are encrypted snapshots supported?**  
Yes\. Encrypted snapshots can be accessed using the EBS direct APIs\.  
To access an encrypted snapshot, the user must have access to the key used to encrypt the snapshot, and the AWS KMS decrypt operation\. See the [ Using the APIs  Use the `ListSnapshotBlocks` or `ListChangedBlocks` API operations to identify the block indexes and block tokens of blocks for which you want to get data\. Then, use the `GetSnapshotBlock` API operation to get the data from the blocks in a snapshot\. Examples for how to run these operations using the AWS CLI are provided later in this guide\. ](#ebsapi-using-operations.title) section earlier in this guide for the AWS KMS policy to assign to a user\.

**Are public snapshots supported?**  
No\. Public snapshots are not supported\.

**Does list snapshot block return all block indexes and block tokens in a snapshot, or only those that have data written to them?**  
It returns only block indexes and tokens that have data written to them\.
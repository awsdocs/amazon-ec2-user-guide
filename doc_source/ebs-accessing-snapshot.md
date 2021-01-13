# Use EBS direct APIs to access the contents of an EBS snapshot<a name="ebs-accessing-snapshot"></a>

You can use the Amazon Elastic Block Store \(Amazon EBS\) direct APIs to create EBS snapshots, write data directly to your snapshots, read data on your snapshots, and identify the differences or changes between two snapshots\. If you’re an independent software vendor \(ISV\) who offers backup services for Amazon EBS, the EBS direct APIs make it more efficient and cost\-effective to track incremental changes on your EBS volumes through snapshots\. This can be done without having to create new volumes from snapshots, and then use Amazon Elastic Compute Cloud \(Amazon EC2\) instances to compare the differences\.

You can create incremental snapshots directly from data on\-premises into EBS volumes and the cloud to use for quick disaster recovery\. With the ability to write and read snapshots, you can write your on\-premises data to an EBS snapshot during a disaster\. Then after recovery, you can restore it back to AWS or on\-premises from the snapshot\. You no longer need to build and maintain complex mechanisms to copy data to and from Amazon EBS\.

This user guide provides an overview of the elements that make up the EBS direct APIs, and examples of how to use them effectively\. For more information about the actions, data types, parameters, and errors of the APIs, see the [EBS direct APIs reference](https://docs.aws.amazon.com/ebs/latest/APIReference/)\. For more information about the supported AWS Regions, endpoints, and service quotas for the EBS direct APIs, see [Amazon EBS Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html) in the *AWS General Reference*\.

**Topics**
+ [Understand the EBS direct APIs](#ebsapi-elements)
+ [Permissions for IAM users](#ebsapi-permissions)
+ [Use encryption](#ebsapis-using-encryption)
+ [Use Signature Version 4 signing](#ebsapis-using-sigv4)
+ [Use checksums](#ebsapis-using-checksums)
+ [Work with the EBS direct APIs using the API or AWS SDKs](#ebsapi-sdk-examples)
+ [Work with the EBS direct APIs using the command line](#ebsapi-cli-examples)
+ [Optimize performance](#ebsapi-performance)
+ [Frequently asked questions](#ebsapi-faq)
+ [Log API Calls for the EBS direct APIs with AWS CloudTrail](logging-ebs-apis-using-cloudtrail.md)
+ [EBS direct APIs and interface VPC endpoints](ebs-apis-vpc-endpoints.md)
+ [Idempotency for StartSnapshot API](ebs-direct-api-idempotency.md)

## Understand the EBS direct APIs<a name="ebsapi-elements"></a>

The following are the key elements that you should understand before getting started with the EBS direct APIs\.

### Pricing<a name="ebsapi-pricing"></a>

The price that you pay to use the EBS direct APIs depends on the requests you make\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

### Snapshots<a name="ebsapi-snapshots"></a>

Snapshots are the primary means to back up data from your EBS volumes\. With the EBS direct APIs, you can also back up data from your on\-premises disks to snapshots\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

**Note**  
Public snapshots are not supported by the EBS direct APIs\.

### Blocks<a name="ebsapi-blocks"></a>

A block is a fragment of data within a snapshot\. Each snapshot can contain thousands of blocks\. All blocks in a snapshot are of a fixed size\.

### Block indexes<a name="ebsapi-block-indexes"></a>

A block index is the offset position of a block within a snapshot, and it is used to identify the block\. Multiply the BlockIndex value with the BlockSize value \(BlockIndex \* BlockSize\) to identify the logical offset of the data in the logical volume\.

### Block tokens<a name="ebsapi-block-tokens"></a>

A block token is the identifying hash of a block within a snapshot, and it is used to locate the block data\. Block tokens returned by EBS direct APIs are temporary\. They change on the expiry timestamp specified for them, or if you run another ListSnapshotBlocks or ListChangedBlocks request for the same snapshot\.

### Checksum<a name="ebsapi-checksum"></a>

A checksum is a small\-sized datum derived from a block of data for the purpose of detecting errors that were introduced during its transmission or storage\. The EBS direct APIs use checksums to validate data integrity\. When you read data from an EBS snapshot, the service provides Base64\-encoded SHA256 checksums for each block of data transmitted, which you can use for validation\. When you write data to an EBS snapshot, you must provide a Base64 encoded SHA256 checksum for each block of data transmitted\. The service validates the data received using the checksum provided\. For more information, see [Use checksums](#ebsapis-using-checksums) later in this guide\.

### Encryption<a name="ebsapi-encryption"></a>

Encryption protects your data by converting it into unreadable code that can be deciphered only by people who have access to the key used to encrypt it\. You can use the EBS direct APIs to read and write encrypted snapshots, but there are some limitations\. For more information, see [Use encryption](#ebsapis-using-encryption) later in this guide\.

### API actions<a name="ebsapi-actions"></a>

The EBS direct APIs consists of six actions\. There are three read actions and three write actions\. The read actions are ListSnapshotBlocks, ListChangedBlocks, and GetSnapshotBlock\. The write actions are StartSnapshot, PutSnapshotBlock, and CompleteSnapshot\. These actions are described in the following sections\.

### List snapshot blocks<a name="ebsapi-listsnapshotblocks"></a>

The ListSnapshotBlocks action returns the block indexes and block tokens of blocks in the specified snapshot\.

### List changed blocks<a name="ebsapi-listchangedblocks"></a>

The ListChangedBlocks action returns the block indexes and block tokens of blocks that are different between two specified snapshots of the same volume and snapshot lineage\.

### Get snapshot block<a name="ebsapi-getsnapshotblock"></a>

The GetSnapshotBlock action returns the data in a block for the specified snapshot ID, block index, and block token\.

### Start snapshot<a name="ebsapi-startsnapshot"></a>

The StartSnapshot action starts a snapshot, either as an incremental snapshot of an existing one or as a new snapshot\. The started snapshot remains in a pending state until it is completed using the CompleteSnapshot action\.

### Put snapshot block<a name="ebsapi-putsnapshotblock"></a>

The PutSnapshotBlock action adds data to a started snapshot in the form of individual blocks\. You must specify a Base64\-encoded SHA256 checksum for the block of data transmitted\. The service validates the checksum after the transmission is completed\. The request fails if the checksum computed by the service doesn’t match what you specified\.

### Complete snapshot<a name="ebsapi-completesnapshot"></a>

The CompleteSnapshot action completes a started snapshot that is in a pending state\. The snapshot is then changed to a completed state\.

### Use the EBS direct APIs to read snapshots<a name="ebsapi-using-read-actions"></a>

The following steps describe how to use the EBS direct APIs to read snapshots:

1. Use the ListSnapshotBlocks action to view all block indexes and block tokens of blocks in a snapshot\. Or use the ListChangedBlocks action to view only the block indexes and block tokens of blocks that are different between two snapshots of the same volume and snapshot lineage\. These actions help you identify the block tokens and block indexes of blocks for which you might want to get data\.

1. Use the GetSnapshotBlock action, and specify the block index and block token of the block for which you want to get data\.

For examples of how to run these actions, see the [Work with the EBS direct APIs using the API or AWS SDKs](#ebsapi-sdk-examples) and [Work with the EBS direct APIs using the command line](#ebsapi-cli-examples) sections later in this guide\.

### Use the EBS direct APIs to write incremental snapshots<a name="ebsapi-using-write-actions"></a>

The following steps describe how to use the EBS direct APIs to write incremental snapshots:

1. Use the StartSnapshot action and specify a parent snapshot ID to start a snapshot as an incremental snapshot of an existing one, or omit the parent snapshot ID to start a new snapshot\. This action returns the new snapshot ID, which is in a pending state\.

1. Use the PutSnapshotBlock action and specify the ID of the pending snapshot to add data to it in the form of individual blocks\. You must specify a Base64\-encoded SHA256 checksum for the block of data transmitted\. The service computes the checksum of the data received and validates it with the checksum that you specified\. The action fails if the checksums don't match\.

1. When you're done adding data to the pending snapshot, use the CompleteSnapshot action to start an asynchronous workflow that seals the snapshot and moves it to a completed state\.

Repeat these steps to create a new, incremental snapshot using the previously created snapshot as the parent\.

For example, in the following diagram, snapshot A is the first new snapshot started\. Snapshot A is used as the parent snapshot to start snapshot B\. Snapshot B is used as the parent snapshot to start and create snapshot C\. Snapshots A, B, and C are incremental snapshots\. Snapshot A is used to create EBS volume 1\. Snapshot D is created from EBS volume 1\. Snapshot D is an incremental snapshot of A; it is not an incremental snapshot of B or C\.

![\[EBS direct APIs used to create incremental snapshots.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ebs-apis-write.png)

For examples of how to run these actions, see the [Work with the EBS direct APIs using the API or AWS SDKs](#ebsapi-sdk-examples) and [Work with the EBS direct APIs using the command line](#ebsapi-cli-examples) sections later in this guide\.

## Permissions for IAM users<a name="ebsapi-permissions"></a>

An AWS Identity and Access Management \(IAM\) user must have the following policies to use the EBS direct APIs\. For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html)\.

Be cautious when assigning the following policies to IAM users\. By assigning these policies, you might give access to a user who is denied access to the same resource through the Amazon EC2 APIs, such as the CopySnapshot or CreateVolume actions\.

### Permissions to read snapshots<a name="ebsapi-read-permissions"></a>

The following policy allows the *read* EBS direct APIs to be used on all snapshots in a specific AWS Region\. In the policy, replace *<Region>* with the Region of the snapshot\.

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
            "Resource": "arn:aws:ec2:<Region>::snapshot/*"
        }
    ]
}
```

The following policy allows the *read* EBS direct APIs to be used on snapshots with a specific key\-value tag\. In the policy, replace *<Key>* with the key value of the tag, and *<Value>* with the value of the tag\.

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

The following policy allows all of the *read* EBS direct APIs to be used on all snapshots in the account only within a specific time range\. This policy authorizes use of the EBS direct APIs based on the `aws:CurrentTime` global condition key\. In the policy, be sure to replace the date and time range shown with the date and time range for your policy\.

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

The following policy grants access to decrypt an encrypted snapshot using a specific key ID from the AWS Key Management Service \(AWS KMS\)\. It grants access to encrypt new snapshots using the default AWS KMS key ID for EBS snapshots\. It also provides the ability to determine if encrypt by default is enabled on the account\. In the policy, replace *<Region>* with the Region of the AWS KMS key, *<AccountId>* with the ID of the AWS account of the key, and *<KeyId>* with the ID of the key used to encrypt the snapshot that you want to read with the EBS direct APIs\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:GenerateDataKey",
                "kms:GenerateDataKeyWithoutPlaintext",
                "kms:ReEncrypt*",
                "kms:CreateGrant",
                "ec2:CreateTags",
                "kms:DescribeKey",
                "ec2:GetEbsDefaultKmsKeyId",
                "ec2:GetEbsEncryptionByDefault"
            ],
            "Resource": "arn:aws:kms:<Region>:<AccountId>:key/<KeyId>"
        }
    ]
}
```

For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.

### Permissions to write snapshots<a name="ebsapi-write-permissions"></a>

The following policy allows the *write* EBS direct APIs to be used on all snapshots in a specific AWS Region\. In the policy, replace *<Region>* with the Region of the snapshot\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:StartSnapshot",
                "ebs:PutSnapshotBlock",
                "ebs:CompleteSnapshot"
            ],
            "Resource": "arn:aws:ec2:<Region>::snapshot/*"
        }
    ]
}
```

The following policy allows the *write* EBS direct APIs to be used on snapshots with a specific key\-value tag\. In the policy, replace *<Key>* with the key value of the tag, and *<Value>* with the value of the tag\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:StartSnapshot",
                "ebs:PutSnapshotBlock",
                "ebs:CompleteSnapshot"
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

The following policy allows all of the EBS direct APIs to be used\. It also allows the `StartSnapshot` action only if a parent snapshot ID is specified\. Therefore, this policy blocks the ability to start new snapshots without using a parent snapshot\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ebs:*", 
            "Resource": "*",
            "Condition": {
                "StringEquals": {
                    "ebs:ParentSnapshot": "arn:aws:ec2:*::snapshot/*"
                }
            }
        }
    ]
}
```

The following policy allows all of the EBS direct APIs to be used\. It also allows only the `user` tag key to be created for a new snapshot\. This policy also ensures that the user has access to create tags\. The `StartSnapshot` action is the only action that can specify tags\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ebs:*", 
            "Resource": "*",
            "Condition": {
                "ForAllValues:StringEquals": {
                    "aws:TagKeys": "user"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": "ec2:CreateTags",
            "Resource": "*"
        }
    ]
}
```

The following policy allows all of the *write* EBS direct APIs to be used on all snapshots in the account only within a specific time range\. This policy authorizes use of the EBS direct APIs based on the `aws:CurrentTime` global condition key\. In the policy, be sure to replace the date and time range shown with the date and time range for your policy\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ebs:StartSnapshot",
                "ebs:PutSnapshotBlock",
                "ebs:CompleteSnapshot"
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

The following policy grants access to decrypt an encrypted snapshot using a specific key ID from the AWS Key Management Service \(AWS KMS\)\. It grants access to encrypt new snapshots using the default AWS KMS key ID for EBS snapshots\. It also provides the ability to determine if encrypt by default is enabled on the account\. In the policy, replace *<Region>* with the Region of the AWS KMS key, *<AccountId>* with the ID of the AWS account of the key, and *<KeyId>* with the ID of the key used to encrypt the snapshot that you want to read with the EBS direct APIs\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:GenerateDataKey",
                "kms:GenerateDataKeyWithoutPlaintext",
                "kms:ReEncrypt*",
                "kms:CreateGrant",
                "ec2:CreateTags",
                "kms:DescribeKey",
                "ec2:GetEbsDefaultKmsKeyId",
                "ec2:GetEbsEncryptionByDefault"
            ],
            "Resource": "arn:aws:kms:<Region>:<AccountId>:key/<KeyId>"
        }
    ]
}
```

For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.

## Use encryption<a name="ebsapis-using-encryption"></a>

If Amazon EBS encryption by default is enabled on your AWS account, you cannot start a new snapshot using an un\-encrypted parent snapshot\. You must first encrypt the parent snapshot by copying it\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md) and [Encryption by default](EBSEncryption.md#encryption-by-default)\.

To start an encrypted snapshot, specify the Amazon Resource Name \(ARN\) of an AWS KMS key, or specify an encrypted parent snapshot in your StartSnapshot request\. If neither are specified, and Amazon EBS encryption by default is enabled on the account, then the default CMK for the account is used\. If no default CMK has been specified for the account, then the AWS managed CMK is used\.

**Important**  
By default, all principals in the account have access to the default AWS managed CMK, and they can use it for EBS encryption and decryption operations\. For more information, see [Default key for EBS encryption](EBSEncryption.md#EBSEncryption_key_mgmt)\.

You might need additional IAM permissions to use the EBS direct APIs with encryption\. For more information, see the [Permissions for IAM users](#ebsapi-permissions) section earlier in this guide\.

## Use Signature Version 4 signing<a name="ebsapis-using-sigv4"></a>

Signature Version 4 is the process to add authentication information to AWS requests sent by HTTP\. For security, most requests to AWS must be signed with an access key, which consists of an access key ID and secret access key\. These two keys are commonly referred to as your security credentials\. For information about how to obtain credentials for your account, see [Understanding and getting your credentials](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html)\.

If you intend to manually create HTTP requests, you must learn how to sign them\. When you use the AWS Command Line Interface \(AWS CLI\) or one of the AWS SDKs to make requests to AWS, these tools automatically sign the requests for you with the access key that you specify when you configure the tools\. When you use these tools, you don't need to learn how to sign requests yourself\.

For more information, see [Signing AWS requests with Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html) in the *AWS General Reference*\.

## Use checksums<a name="ebsapis-using-checksums"></a>

The GetSnapshotBlock action returns data that is in a block of a snapshot, and the PutSnapshotBlock action adds data to a block in a snapshot\. The block data that is transmitted is not signed as part of the Signature Version 4 signing process\. As a result, checksums are used to validate the integrity of the data as follows:
+ When you use the GetSnapshotBlock action, the response provides a Base64\-encoded SHA256 checksum for the block data using the **x\-amz\-Checksum** header, and the checksum algorithm using the **x\-amz\-Checksum\-Algorithm** header\. Use the returned checksum to validate the integrity of the data\. If the checksum that you generate doesn't match what Amazon EBS provided, you should consider the data not valid and retry your request\.
+ When you use the PutSnapshotBlock action, your request must provide a Base64\-encoded SHA256 checksum for the block data using the **x\-amz\-Checksum** header, and the checksum algorithm using the **x\-amz\-Checksum\-Algorithm** header\. The checksum that you provide is validated against a checksum generated by Amazon EBS to validate the integrity of the data\. If the checksums do not correspond, the request fails\.
+ When you use the CompleteSnapshot action, your request can optionally provide an aggregate Base64\-encoded SHA256 checksum for the complete set of data added to the snapshot\. Provide the checksum using the **x\-amz\-Checksum** header, the checksum algorithm using the **x\-amz\-Checksum\-Algorithm** header, and the checksum aggregation method using the **x\-amz\-Checksum\-Aggregation\-Method** header\. To generate the aggregated checksum using the linear aggregation method, arrange the checksums for each written block in ascending order of their block index, concatenate them to form a single string, and then generate the checksum on the entire string using the SHA256 algorithm\. 

The checksums in these actions are part of the Signature Version 4 signing process\.

## Work with the EBS direct APIs using the API or AWS SDKs<a name="ebsapi-sdk-examples"></a>

The [EBS direct APIs Reference](https://docs.aws.amazon.com/ebs/latest/APIReference/) provides descriptions and syntax for each of the service’s actions and data types\. You can also use one of the AWS SDKs to access an API that's tailored to the programming language or platform that you're using\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.

The EBS direct APIs require an AWS Signature Version 4 signature\. For more information, see [Use Signature Version 4 signing](#ebsapis-using-sigv4)\. 

### Use the API to read snapshots<a name="ebsapi-sdk-examples-read"></a>

#### List blocks in a snapshot<a name="listsnapshotblocks-api"></a>

The following [ListChangedBlocks](https://docs.aws.amazon.com/ebs/latest/APIReference/API_ListSnapshotBlocks.html) example request returns the block indexes and block tokens of blocks that are in snapshot `snap-0acEXAMPLEcf41648`\. The `startingBlockIndex` parameter limits the results to block indexes greater than `1000`, and the `maxResults` parameter limits the results to the first `100` blocks\.

```
GET /snapshots/snap-0acEXAMPLEcf41648/blocks?maxResults=100&startingBlockIndex=1000 HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
User-Agent: <User agent parameter>
X-Amz-Date: 20200617T231953Z
Authorization: <Authentication parameter>
```

The following example response for the previous request lists the block indexes and block tokens in the snapshot\. Use the GetSnapshotBlock action and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\. 

```
HTTP/1.1 200 OK
x-amzn-RequestId: d6e5017c-70a8-4539-8830-57f5557f3f27
Content-Type: application/json
Content-Length: 2472
Date: Wed, 17 Jun 2020 23:19:56 GMT
Connection: keep-alive

{
    "BlockSize": 524288,
    "Blocks": [
        {
            "BlockIndex": 0,
            "BlockToken": "AAUBAcuWqOCnDNuKle11s7IIX6jp6FYcC/q8oT93913HhvLvA+3JRrSybp/0"
        },
        {
            "BlockIndex": 1536,
            "BlockToken": "AAUBAWudwfmofcrQhGVlLwuRKm2b8ZXPiyrgoykTRC6IU1NbxKWDY1pPjvnV"
        },
        {
            "BlockIndex": 3072,
            "BlockToken": "AAUBAV7p6pC5fKAC7TokoNCtAnZhqq27u6YEXZ3MwRevBkDjmMx6iuA6tsBt"
        },
        {
            "BlockIndex": 3073,
            "BlockToken": "AAUBAbqt9zpqBUEvtO2HINAfFaWToOwlPjbIsQOlx6JUN/0+iMQl0NtNbnX4"
        },
        ...
    ],
    "ExpiryTime": 1.59298379649E9,
    "VolumeSize": 3
}
```

#### List blocks that are different between two snapshots<a name="listchangedblocks-api"></a>

The following [ListChangedBlocks](https://docs.aws.amazon.com/ebs/latest/APIReference/API_ListChangedBlocks.html) example request returns the block indexes and block tokens of blocks that are different between snapshots `snap-0acEXAMPLEcf41648` and `snap-0c9EXAMPLE1b30e2f`\. The `startingBlockIndex` parameter limits the results to block indexes greater than `0`, and the `maxResults` parameter limits the results to the first `500` blocks\.

```
GET /snapshots/snap-0c9EXAMPLE1b30e2f/changedblocks?firstSnapshotId=snap-0acEXAMPLEcf41648&maxResults=500&startingBlockIndex=0 HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
User-Agent: <User agent parameter>
X-Amz-Date: 20200617T232546Z
Authorization: <Authentication parameter>
```

The following example response for the previous request shows that block indexes `0`, `3072`, `6002`, and `6003` are different between the two snapshots\. Additionally, block indexes `6002`, and `6003` exist only in the first snapshot ID specified, and not in the second snapshot ID because there is no second block token listed in the response\.

Use the `GetSnapshotBlock` action and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\. 

```
HTTP/1.1 200 OK
x-amzn-RequestId: fb0f6743-6d81-4be8-afbe-db11a5bb8a1f
Content-Type: application/json
Content-Length: 1456
Date: Wed, 17 Jun 2020 23:25:47 GMT
Connection: keep-alive

{
    "BlockSize": 524288,
    "ChangedBlocks": [
        {
            "BlockIndex": 0,
            "FirstBlockToken": "AAUBAVaWqOCnDNuKle11s7IIX6jp6FYcC/tJuVT1GgP23AuLntwiMdJ+OJkL",
            "SecondBlockToken": "AAUBASxzy0Y0b33JVRLoYm3NOresCxn5RO+HVFzXW3Y/RwfFaPX2Edx8QHCh"
        },
        {
            "BlockIndex": 3072,
            "FirstBlockToken": "AAUBAcHp6pC5fKAC7TokoNCtAnZhqq27u6fxRfZOLEmeXLmHBf2R/Yb24MaS",
            "SecondBlockToken": "AAUBARGCaufCqBRZC8tEkPYGGkSv3vqvOjJ2xKDi3ljDFiytUxBLXYgTmkid"
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
    "ExpiryTime": 1.592976647009E9,
    "VolumeSize": 3
}
```

#### Get block data from a snapshot<a name="getsnapshotblock-api"></a>

The following [GetSnapshotBlock](https://docs.aws.amazon.com/ebs/latest/APIReference/API_GetSnapshotBlock.html) example request returns the data in the block index `3072` with block token `AAUBARGCaufCqBRZC8tEkPYGGkSv3vqvOjJ2xKDi3ljDFiytUxBLXYgTmkid`, in snapshot `snap-0c9EXAMPLE1b30e2f`\.

```
GET /snapshots/snap-0c9EXAMPLE1b30e2f/blocks/3072?blockToken=AAUBARGCaufCqBRZC8tEkPYGGkSv3vqvOjJ2xKDi3ljDFiytUxBLXYgTmkid HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
User-Agent: <User agent parameter>
X-Amz-Date: 20200617T232838Z
Authorization: <Authentication parameter>
```

The following example response for the previous request shows the size of the data returned, the checksum to validate the data, and the algorithm used to generate the checksum\. The binary data is transmitted in the body of the response and is represented as *BlockData* in the following example\.

```
HTTP/1.1 200 OK
x-amzn-RequestId: 2d0db2fb-bd88-474d-a137-81c4e57d7b9f
x-amz-Data-Length: 524288
x-amz-Checksum: Vc0yY2j3qg8bUL9I6GQuI2orTudrQRBDMIhcy7bdEsw=
x-amz-Checksum-Algorithm: SHA256
Content-Type: application/octet-stream
Content-Length: 524288
Date: Wed, 17 Jun 2020 23:28:38 GMT
Connection: keep-alive

BlockData
```

### Use the API to write incremental snapshots<a name="ebsapi-sdk-examples-write"></a>

#### Start a snapshot<a name="startsnapshot-api"></a>

The following [StartSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_StartSnapshot.html) example request starts an `8` GiB snapshot, using snapshot `snap-123EXAMPLE1234567` as the parent snapshot\. The new snapshot will be an incremental snapshot of the parent snapshot\. The snapshot moves to an error state if there are no put or complete requests made for the snapshot within the specified `60` minute timeout period\. The `550e8400-e29b-41d4-a716-446655440000` client token ensures idempotency for the request\. If the client token is omitted, the AWS SDK automatically generates one for you\. For more information about idempotency, see [Idempotency for StartSnapshot API](ebs-direct-api-idempotency.md)\.

```
POST /snapshots HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
User-Agent: <User agent parameter>
X-Amz-Date: 20200618T040724Z
Authorization: <Authentication parameter>

{
    "VolumeSize": 8,
    "ParentSnapshot": snap-123EXAMPLE1234567,
    "ClientToken": "550e8400-e29b-41d4-a716-446655440000",
    "Timeout": 60
}
```

The following example response for the previous request shows the snapshot ID, AWS account ID, status, volume size in GiB, and size of the blocks in the snapshot\. The snapshot is started in a pending state\. Specify the snapshot ID in a subsequent `PutSnapshotBlocks` request to write data to the snapshot\.

```
HTTP/1.1 201 Created
x-amzn-RequestId: 929e6eb9-7183-405a-9502-5b7da37c1b18
Content-Type: application/json
Content-Length: 181
Date: Thu, 18 Jun 2020 04:07:29 GMT
Connection: keep-alive

{
    "BlockSize": 524288,
    "Description": null,
    "OwnerId": "138695307491",
    "Progress": null,
    "SnapshotId": "snap-052EXAMPLEc85d8dd",
    "StartTime": null,
    "Status": "pending",
    "Tags": null,
    "VolumeSize": 8
}
```

#### Put data into a snapshot<a name="putsnapshotblock-api"></a>

The following [PutSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_PutSnapshotBlock.html) example request writes `524288` Bytes of data to block index `1000` on snapshot `snap-052EXAMPLEc85d8dd`\. The Base64 encoded `QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=` checksum was generated using the `SHA256` algorithm\. The data is transmitted in the body of the request and is represented as *BlockData* in the following example\.

```
PUT /snapshots/snap-052EXAMPLEc85d8dd/blocks/1000 HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
x-amz-Data-Length: 524288
x-amz-Checksum: QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=
x-amz-Checksum-Algorithm: SHA256
User-Agent: <User agent parameter>
X-Amz-Date: 20200618T042215Z
X-Amz-Content-SHA256: UNSIGNED-PAYLOAD
Authorization: <Authentication parameter>
          
          BlockData
```

The following is example response for the previous request confirms the data length, checksum, and checksum algorithm for the data received by the service\. 

```
HTTP/1.1 201 Created
x-amzn-RequestId: 643ac797-7e0c-4ad0-8417-97b77b43c57b
x-amz-Checksum: QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=
x-amz-Checksum-Algorithm: SHA256
Content-Type: application/json
Content-Length: 2
Date: Thu, 18 Jun 2020 04:22:12 GMT
Connection: keep-alive

{}
```

#### Complete a snapshot<a name="completesnapshot-api"></a>

The following [CompleteSnapshot](https://docs.aws.amazon.com/ebs/latest/APIReference/API_CompleteSnapshot.html) example request completes snapshot `snap-052EXAMPLEc85d8dd`\. The command specifies that `5` blocks were written to the snapshot\. The `6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c=` checksum represents the checksum for the complete set of data written to a snapshot\.

```
POST /snapshots/completion/snap-052EXAMPLEc85d8dd HTTP/1.1
Host: ebs.us-east-2.amazonaws.com
Accept-Encoding: identity
x-amz-ChangedBlocksCount: 5
x-amz-Checksum: 6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c=
x-amz-Checksum-Algorithm: SHA256
x-amz-Checksum-Aggregation-Method: LINEAR
User-Agent: <User agent parameter>
X-Amz-Date: 20200618T043158Z
Authorization: <Authentication parameter>
```

The following is an example response for the previous request\.

```
HTTP/1.1 202 Accepted
x-amzn-RequestId: 06cba5b5-b731-49de-af40-80333ac3a117
Content-Type: application/json
Content-Length: 20
Date: Thu, 18 Jun 2020 04:31:50 GMT
Connection: keep-alive

{"Status":"pending"}
```

## Work with the EBS direct APIs using the command line<a name="ebsapi-cli-examples"></a>

The following examples show how to use the EBS direct APIs using the AWS Command Line Interface \(AWS CLI\)\. For more information about installing and configuring the AWS CLI, see [Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [Quickly Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration)\.

### Use the AWS CLI to read snapshots<a name="ebsapi-cli-examples-read"></a>

#### List blocks in a snapshot<a name="listsnapshotblocks-cli"></a>

The following [list\-snapshot\-blocks](https://docs.aws.amazon.com/cli/latest/reference/ebs/list-snapshot-blocks.html) example command returns the block indexes and block tokens of blocks that are in snapshot `snap-0987654321`\. The `--starting-block-index` parameter limits the results to block indexes greater than `1000`, and the `--max-results` parameter limits the results to the first `100` blocks\.

```
aws ebs list-snapshot-blocks --snapshot-id snap-0987654321 --starting-block-index 1000 --max-results 100
```

The following example response for the previous command lists the block indexes and block tokens in the snapshot\. Use the `get-snapshot-block` command and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\.

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

#### List blocks that are different between two snapshots<a name="listchangedblocks-cli"></a>

The following [list\-changed\-blocks](https://docs.aws.amazon.com/cli/latest/reference/ebs/list-changed-blocks.html) example command returns the block indexes and block tokens of blocks that are different between snapshots `snap-1234567890` and `snap-0987654321`\. The `--starting-block-index` parameter limits the results to block indexes greater than `0`, and the `--max-results` parameter limits the results to the first `500` blocks\.\.

```
aws ebs list-changed-blocks --first-snapshot-id snap-1234567890 --second-snapshot-id snap-0987654321 --starting-block-index 0 --max-results 500
```

The following example response for the previous command shows that block indexes 0, 6000, 6001, 6002, and 6003 are different between the two snapshots\. Additionally, block indexes 6001, 6002, and 6003 exist only in the first snapshot ID specified, and not in the second snapshot ID because there is no second block token listed in the response\.

Use the `get-snapshot-block` command and specify the block index and block token of the block for which you want to get data\. The block tokens are valid until the expiry time listed\.

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

#### Get block data from a snapshot<a name="getsnapshotblock-cli"></a>

The following [get\-snapshot\-block](https://docs.aws.amazon.com/cli/latest/reference/ebs/get-snapshot-block.html) example command returns the data in the block index `6001` with block token `AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR`, in snapshot `snap-1234567890`\. The binary data is output to the `data` file in the `C:\Temp` directory on a Windows computer\. If you run the command on a Linux or Unix computer, replace the output path with `/tmp/data` to output the data to the `data` file in the `/tmp` directory\.

```
aws ebs get-snapshot-block --snapshot-id snap-1234567890 --block-index 6001 --block-token AAABASBpSJ2UAD3PLxJnCt6zun4/T4sU25Bnb8jB5Q6FRXHFqAIAqE04hJoR C:/Temp/data
```

The following example response for the previous command shows the size of the data returned, the checksum to validate the data, and the algorithm of the checksum\. The binary data is automatically saved to the directory and file you specified in the request command\.

```
{
    "DataLength": "524288",
    "Checksum": "cf0Y6/Fn0oFa4VyjQPOa/iD0zhTflPTKzxGv2OKowXc=",
    "ChecksumAlgorithm": "SHA256"
}
```

### Use the AWS CLI to write incremental snapshots<a name="ebsapi-cli-examples-write"></a>

#### Start a snapshot<a name="startsnapshot-cli"></a>

The following [start\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ebs/start-snapshot.html) example command starts an `8` GiB snapshot, using snapshot `snap-123EXAMPLE1234567` as the parent snapshot\. The new snapshot will be an incremental snapshot of the parent snapshot\. The snapshot moves to an error state if there are no put or complete requests made for the snapshot within the specified `60` minute timeout period\. The `550e8400-e29b-41d4-a716-446655440000` client token ensures idempotency for the request\. If the client token is omitted, the AWS SDK automatically generates one for you\. For more information about idempotency, see [Idempotency for StartSnapshot API](ebs-direct-api-idempotency.md)\.

```
aws ebs start-snapshot --volume-size 8 --parent-snapshot snap-123EXAMPLE1234567 --timeout 60 --client-token 550e8400-e29b-41d4-a716-446655440000
```

The following example response for the previous command shows the snapshot ID, AWS account ID, status, volume size in GiB, and size of the blocks in the snapshot\. The snapshot is started in a `pending` state\. Specify the snapshot ID in subsequent `put-snapshot-block` commands to write data to the snapshot, then use the `complete-snapshot` command to complete the snapshot and change its status to `completed`\.

```
{
    "SnapshotId": "snap-0aaEXAMPLEe306d62",
    "OwnerId": "111122223333",
    "Status": "pending",
    "VolumeSize": 8,
    "BlockSize": 524288
}
```

#### Put data into a snapshot<a name="putsnapshotblock-cli"></a>

The following [put\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ebs/put-snapshot.html) example command writes `524288` Bytes of data to block index `1000` on snapshot `snap-0aaEXAMPLEe306d62`\. The Base64 encoded `QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=` checksum was generated using the `SHA256` algorithm\. The data that is transmitted is in the `/tmp/data` file\.

```
aws ebs put-snapshot-block --snapshot-id snap-0aaEXAMPLEe306d62 --block-index 1000 --data-length 524288 --block-data /tmp/data --checksum QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM= --checksum-algorithm SHA256
```

The following example response for the previous command confirms the data length, checksum, and checksum algorithm for the data received by the service\.

```
{
    "DataLength": "524288",
    "Checksum": "QOD3gmEQOXATfJx2Aa34W4FU2nZGyXfqtsUuktOw8DM=",
    "ChecksumAlgorithm": "SHA256"
}
```

#### Complete a snapshot<a name="completesnapshot-cli"></a>

The following [complete\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ebs/complete-snapshot.html) example command completes snapshot `snap-0aaEXAMPLEe306d62`\. The command specifies that `5` blocks were written to the snapshot\. The `6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c=` checksum represents the checksum for the complete set of data written to a snapshot\. For more information about checksums, see [Use checksums](#ebsapis-using-checksums) earlier in this guide\.

```
aws ebs complete-snapshot --snapshot-id snap-0aaEXAMPLEe306d62 --changed-blocks-count 5 --checksum 6D3nmwi5f2F0wlh7xX8QprrJBFzDX8aacdOcA3KCM3c= --checksum-algorithm SHA256 --checksum-aggregation-method LINEAR
```

The following is an example response for the previous command\.

```
{
    "Status": "pending"
}
```

## Optimize performance<a name="ebsapi-performance"></a>

You can run API requests concurrently\. Assuming PutSnapshotBlock latency is 100ms, then a thread can process 10 requests in one second\. Furthermore, assuming your client application creates multiple threads and connections \(for example, 100 connections\), it can make 1000 \(10 \* 100\) requests per second in total\. This will correspond to a throughput of around 500 MB per second\.

The following list contains few things to look for in your application:
+ Is each thread using a separate connection? If the connections are limited on the application then multiple threads will wait for the connection to be available and you will notice lower throughput\.
+ Is there any wait time in the application between two put requests? This will reduce the effective throughput of a thread\.
+ The bandwidth limit on the instance – If bandwidth on the instance is shared by other applications, it could limit the available throughput for PutSnapshotBlock requests\.

Be sure to take note of other workloads that might be running in the account to avoid bottlenecks\. You should also build retry mechanisms into your EBS direct APIs workflows to handle throttling, timeouts, and service unavailability\.

Review the EBS direct APIs service quotas to determine the maximum API requests that you can run per second\. For more information, see [Amazon Elastic Block Store Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#w542aab9d130b7c15) in the *AWS General Reference*\.

## Frequently asked questions<a name="ebsapi-faq"></a>

**Can a snapshot be accessed using the EBS direct APIs if it has a pending status?**  
No\. The snapshot can be accessed only if it has a completed status\.

**Are the block indexes returned by the EBS direct APIs in numerical order?**  
Yes\. The block indexes returned are unique, and in numerical order\.

**Can I submit a request with a MaxResults parameter value of under 100?**  
No\. The minimum MaxResult parameter value you can use is 100\. If you submit a request with a MaxResult parameter value of under 100, and there are more than 100 blocks in the snapshot, then the API will return at least 100 results\.

**Can I run API requests concurrently?**  
You can run API requests concurrently\. Be sure to take note of other workloads that might be running in the account to avoid bottlenecks\. You should also build retry mechanisms into your EBS direct APIs workflows to handle throttling, timeouts, and service unavailability\. For more information, see [Optimize performance](#ebsapi-performance)\.  
Review the EBS direct APIs service quotas to determine the API requests that you can run per second\. For more information, see [Amazon Elastic Block Store Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#w542aab9d130b7c15) in the *AWS General Reference*\.

**When running the ListChangedBlocks action, is it possible to get an empty response even though there are blocks in the snapshot?**  
Yes\. If the changed blocks are scarce in the snapshot, the response may be empty but the API will return a next page token value\. Use the next page token value to continue to the next page of results\. You can confirm that you have reached the last page of results when the API returns a next page token value of null\.

**If the NextToken parameter is specified together with a StartingBlockIndex parameter, which of the two is used?**  
The NextToken is used, and the StartingBlockIndex is ignored\.

**How long are the block tokens and next tokens valid?**  
Block tokens are valid for seven days, and next tokens are valid for 60 minutes\.

**Are encrypted snapshots supported?**  
Yes\. Encrypted snapshots can be accessed using the EBS direct APIs\.  
To access an encrypted snapshot, the user must have access to the key used to encrypt the snapshot, and the AWS KMS decrypt action\. See the [Permissions for IAM users](#ebsapi-permissions) section earlier in this guide for the AWS KMS policy to assign to a user\.

**Are public snapshots supported?**  
Public snapshots are not supported\.

**Does list snapshot block return all block indexes and block tokens in a snapshot, or only those that have data written to them?**  
It returns only block indexes and tokens that have data written to them\.

**Can I get a history of the API calls made by the EBS direct APIs on my account for security analysis and operational troubleshooting purposes?**  
Yes\. To receive a history of EBS direct APIs API calls made on your account, turn on AWS CloudTrail in the AWS Management Console\. For more information, see [Log API Calls for the EBS direct APIs with AWS CloudTrail](logging-ebs-apis-using-cloudtrail.md)\.
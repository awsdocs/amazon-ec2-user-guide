# Use EBS direct APIs to access the contents of an EBS snapshot<a name="ebs-accessing-snapshot"></a>

You can use the Amazon Elastic Block Store \(Amazon EBS\) direct APIs to create EBS snapshots, write data directly to your snapshots, read data on your snapshots, and identify the differences or changes between two snapshots\. If you’re an independent software vendor \(ISV\) who offers backup services for Amazon EBS, the EBS direct APIs make it more efficient and cost\-effective to track incremental changes on your EBS volumes through snapshots\. This can be done without having to create new volumes from snapshots, and then use Amazon Elastic Compute Cloud \(Amazon EC2\) instances to compare the differences\.

You can create incremental snapshots directly from data on\-premises into EBS volumes and the cloud to use for quick disaster recovery\. With the ability to write and read snapshots, you can write your on\-premises data to an EBS snapshot during a disaster\. Then after recovery, you can restore it back to AWS or on\-premises from the snapshot\. You no longer need to build and maintain complex mechanisms to copy data to and from Amazon EBS\.

This user guide provides an overview of the elements that make up the EBS direct APIs, and examples of how to use them effectively\. For more information about the actions, data types, parameters, and errors of the APIs, see the [EBS direct APIs reference](https://docs.aws.amazon.com/ebs/latest/APIReference/)\. For more information about the supported AWS Regions, endpoints, and service quotas for the EBS direct APIs, see [Amazon EBS Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html) in the *AWS General Reference*\.

**Topics**
+ [Understand the EBS direct APIs](#ebsapi-elements)
+ [IAM permissions for EBS direct APIs](ebsapi-permissions.md)
+ [Use EBS direct APIs](work-with.md)
+ [Pricing for EBS direct APIs](#ebsapi-pricing)
+ [Using interface VPC endpoints with EBS direct APIs](ebs-apis-vpc-endpoints.md)
+ [Log API Calls for EBS direct APIs with AWS CloudTrail](logging-ebs-apis-using-cloudtrail.md)
+ [Frequently asked questions](ebsapi-faq.md)

## Understand the EBS direct APIs<a name="ebsapi-elements"></a>

The following are the key elements that you should understand before getting started with the EBS direct APIs\.

### Snapshots<a name="ebsapi-snapshots"></a>

Snapshots are the primary means to back up data from your EBS volumes\. With the EBS direct APIs, you can also back up data from your on\-premises disks to snapshots\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

**Note**  
Public snapshots are not supported by the EBS direct APIs\.

### Blocks<a name="ebsapi-blocks"></a>

A block is a fragment of data within a snapshot\. Each snapshot can contain thousands of blocks\. All blocks in a snapshot are of a fixed size\.

### Block indexes<a name="ebsapi-block-indexes"></a>

A block index is a logical index in units of `512` KiB blocks\. To identify the block index, divide the logical offset of the data in the logical volume by the block size \(logical offset of data/`524288`\)\. The logical offset of the data must be `512` KiB aligned\.

### Block tokens<a name="ebsapi-block-tokens"></a>

A block token is the identifying hash of a block within a snapshot, and it is used to locate the block data\. Block tokens returned by EBS direct APIs are temporary\. They change on the expiry timestamp specified for them, or if you run another ListSnapshotBlocks or ListChangedBlocks request for the same snapshot\.

### Checksum<a name="ebsapi-checksum"></a>

A checksum is a small\-sized datum derived from a block of data for the purpose of detecting errors that were introduced during its transmission or storage\. The EBS direct APIs use checksums to validate data integrity\. When you read data from an EBS snapshot, the service provides Base64\-encoded SHA256 checksums for each block of data transmitted, which you can use for validation\. When you write data to an EBS snapshot, you must provide a Base64 encoded SHA256 checksum for each block of data transmitted\. The service validates the data received using the checksum provided\. For more information, see [Use checksums](ebsapis-using-checksums.md) later in this guide\.

### Encryption<a name="ebsapi-encryption"></a>

Encryption protects your data by converting it into unreadable code that can be deciphered only by people who have access to the KMS key used to encrypt it\. You can use the EBS direct APIs to read and write encrypted snapshots, but there are some limitations\. For more information, see [Use encryption](ebsapis-using-encryption.md) later in this guide\.

### API actions<a name="ebsapi-actions"></a>

The EBS direct APIs consists of six actions\. There are three read actions and three write actions\. The read actions are: 
+ **ListSnapshotBlocks** — returns the block indexes and block tokens of blocks in the specified snapshot
+ **ListChangedBlocks** — returns the block indexes and block tokens of blocks that are different between two specified snapshots of the same volume and snapshot lineage\.
+ **GetSnapshotBlock** — returns the data in a block for the specified snapshot ID, block index, and block token\.

The write actions are:
+ **StartSnapshot** — starts a snapshot, either as an incremental snapshot of an existing one or as a new snapshot\. The started snapshot remains in a pending state until it is completed using the CompleteSnapshot action\.
+ **PutSnapshotBlock** — adds data to a started snapshot in the form of individual blocks\. You must specify a Base64\-encoded SHA256 checksum for the block of data transmitted\. The service validates the checksum after the transmission is completed\. The request fails if the checksum computed by the service doesn’t match what you specified\.
+ **CompleteSnapshot** — completes a started snapshot that is in a pending state\. The snapshot is then changed to a completed state\.

## Pricing for EBS direct APIs<a name="ebsapi-pricing"></a>

**Topics**
+ [Pricing for APIs](#api-pricing)
+ [Networking costs](#networking-costs)

### Pricing for APIs<a name="api-pricing"></a>

The price that you pay to use the EBS direct APIs depends on the requests you make\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.
+ **ListChangedBlocks** and ListSnapshotBlocks APIs are charged per request\. For example, if you make 100,000 ListSnapshotBlocks API requests in a Region that charges $0\.0006 per 1,000 requests, you will be charged $0\.06 \($0\.0006 per 1,000 requests x 100\)\.
+ **GetSnapshotBlock** is charged per block returned\. For example, if you make 100,000 GetSnapshotBlock API requests in a Region that charges $0\.003 per 1,000 blocks returned, you will be charged $0\.30 \($0\.003 per 1,000 blocks retruned x 100\)\.
+ **PutSnapshotBlock** is charged per block written\. For example, if you make 100,000 PutSnapshotBlock API requests in a Region that charges $0\.006 per 1,000 blocks written, you will be charged $0\.60 \($0\.006 per 1,000 blocks written x 100\)\.

### Networking costs<a name="networking-costs"></a>

**Data transfer costs**  
Data transferred directly between EBS direct APIs and Amazon EC2 instances in the same AWS Region is free when using [ non\-FIPS endpoints](https://docs.aws.amazon.com/general/latest/gr/ebs-service.html#ebs_direct_apis)\. For more information, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\. If other AWS services are in the path of your data transfer, you will be charged their associated data processing costs\. These services include, but are not limited to, PrivateLink endpoints, NAT Gateway and Transit Gateway\.

**VPC interface endpoints**  
If you are using EBS direct APIs from Amazon EC2 instances or AWS Lambda functions in private subnets, you can use VPC interface endpoints, instead of using NAT gateways, to reduce network data transfer costs\. For more information, see [Using interface VPC endpoints with EBS direct APIs](ebs-apis-vpc-endpoints.md)\.
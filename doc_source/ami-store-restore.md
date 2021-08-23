# Store and restore an AMI using S3<a name="ami-store-restore"></a>

You can store an Amazon Machine Image \(AMI\) in an Amazon S3 bucket, copy the AMI to another S3 bucket, and then restore it from the S3 bucket\. By storing and restoring an AMI using S3 buckets, you can copy AMIs from one AWS partition to another, for example, from the main commercial partition to the AWS GovCloud \(US\) partition\. You can also make archival copies of AMIs by storing them in an S3 bucket\.

The supported APIs for storing and restoring an AMI using S3 are `CreateStoreImageTask`, `DescribeStoreImageTasks`, and `CreateRestoreImageTask`\.

`CopyImage` is the recommended API to use for copying AMIs *within* an AWS [partition](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. However, `CopyImage` can’t copy an AMI to *another* partition\.

**Warning**  
Ensure that you comply with all applicable laws and business requirements when moving data between AWS partitions or AWS Regions, including, but not limited to, any applicable government regulations and data residency requirements\.

**Topics**
+ [Use cases](#use-cases)
+ [How the AMI store and restore APIs work](#how-it-works)
+ [Limitations](#limitations)
+ [Costs](#store-restore-costs)
+ [Securing your AMIs](#securing-amis)
+ [Permissions for storing and restoring AMIs using S3](#ami-s3-permissions)
+ [Work with the AMI store and restore APIs](#work-with-ami-store-restore)

## Use cases<a name="use-cases"></a>

**Topics**
+ [Copy an AMI from one AWS partition to another AWS partition](#copy-to-partition)
+ [Make archival copies of AMIs](#archival-copies)

### Copy an AMI from one AWS partition to another AWS partition<a name="copy-to-partition"></a>

By storing and restoring an AMI using S3 buckets, you can copy an AMI from one AWS partition to another, or from one AWS Region to another\. In the following example, you copy an AMI from the main commercial partition to the AWS GovCloud \(US\) partition, specifically from the `us-east-2` Region to the `us-gov-east-1` Region\.

To copy an AMI from one partition to another, follow these steps:
+ Store the AMI in an S3 bucket in the current Region by using `CreateStoreImageTask`\. In this example, the S3 bucket is located in `us-east-2`\. For an example command, see [Store an AMI in an S3 bucket](#store-ami)\.
+ Monitor the progress of the store task by using `DescribeStoreImageTasks`\. The object becomes visible in the S3 bucket when the task is completed\. For an example command, see [Describe the progress of an AMI store task](#describe-store-ami)\.
+ Copy the stored AMI object to an S3 bucket in the target partition using a procedure of your choice\. In this example, the S3 bucket is located in `us-gov-east-1`\.
**Note**  
Because you need different AWS credentials for each partition, you can’t copy an S3 object directly from one partition to another\. The process for copying an S3 object across partitions is outside the scope of this documentation\. We provide the following copy processes as examples, but you must use the copy process that meets your security requirements\.  
To copy one AMI across partitions, the copy process could be as straightforward as the following: [Download the object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/download-objects.html) from the source bucket to an intermediate host \(for example, an EC2 instance or a laptop\), and then [upload the object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html) from the intermediate host to the source bucket\. For each stage of the process, use the AWS credentials for the partition\.
For more sustained usage, consider developing an application that manages the copies, potentially using S3 [multipart downloads and uploads](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html)\.
+ Restore the AMI from the S3 bucket in the target partition by using `CreateRestoreImageTask`\. In this example, the S3 bucket is located in `us-gov-east-1`\. For an example command, see [Restore an AMI from an S3 bucket](#restore-ami)\.
+ Monitor the progress of the restore task by describing the AMI to check when its state becomes available\. You can also monitor the progress percentages of the snapshots that make up the restored AMI by describing the snapshots\.

### Make archival copies of AMIs<a name="archival-copies"></a>

You can make archival copies of AMIs by storing them in an S3 bucket\. For an example command, see [Store an AMI in an S3 bucket](#store-ami)\.

The AMI is packed into a single object in S3, and all of the AMI metadata \(excluding sharing information\) is preserved as part of the stored AMI\. The AMI data is compressed as part of the storage process\. AMIs that contain data that can easily be compressed will result in smaller objects in S3\. To reduce costs, you can use less expensive S3 storage tiers\. For more information, see [Amazon S3 Storage Classes](http://aws.amazon.com/s3/storage-classes/) and [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)

## How the AMI store and restore APIs work<a name="how-it-works"></a>

To store and restore an AMI using S3, you use the following APIs:
+ `CreateStoreImageTask` – Stores the AMI in an S3 bucket
+ `DescribeStoreImageTasks` – Provides the progress of the AMI store task
+ `CreateRestoreImageTask` – Restores the AMI from an S3 bucket

**Topics**
+ [CreateStoreImageTask](#CreateStoreImageTask)
+ [DescribeStoreImageTasks](#DescribeStoreImageTasks)
+ [CreateRestoreImageTask](#CreateRestoreImageTask)

### CreateStoreImageTask<a name="CreateStoreImageTask"></a>

The [CreateStoreImageTask](#store-ami) API stores an AMI as a single object in an S3 bucket\.

The API creates a task that reads all of the data from the AMI and its snapshots, and then uses an [S3 multipart upload](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html) to store the data in an S3 object\. The API takes all of the components of the AMI, including most of the non\-Region\-specific AMI metadata, and all the EBS snapshots contained in the AMI, and packs them into a single object in S3\. The data is compressed as part of the upload process to reduce the amount of space used in S3, so the object in S3 might be smaller than the sum of the sizes of the snapshots in the AMI\.

If there are AMI and snapshot tags visible to the account calling this API, they are preserved\.

The object in S3 has the same ID as the AMI, but with a `.bin` extension\. The following data is also stored as S3 metadata tags on the S3 object: AMI name, AMI description, AMI registration date, AMI owner account, and a timestamp for the store operation\.

The time it takes to complete the task depends on the size of the AMI\. It also depends on how many other tasks are in progress because tasks are queued\. You can track the progress of the task by calling the [DescribeStoreImageTasks](#describe-store-ami) API\.

The sum of the sizes of all the AMIs in progress is limited to 600 GB of EBS snapshot data per account\. Further task creation will be rejected until the tasks in progress are less than the limit\. For example, if an AMI with 100 GB of snapshot data and another AMI with 200 GB of snapshot data are currently being stored, another request will be accepted, because the total in progress is 300 GB, which is less than the limit\. But if a single AMI with 800 GB of snapshot data is currently being stored, further tasks are rejected until the task is completed\.

### DescribeStoreImageTasks<a name="DescribeStoreImageTasks"></a>

The [DescribeStoreImageTasks](#describe-store-ami) API describes the progress of the AMI store tasks\. You can describe tasks for specified AMIs\. If you don't specify AMIs, you get a paginated list of all of the store image tasks that have been processed in the last 31 days\.

For each AMI task, the response indicates if the task is `InProgress`, `Completed`, or `Failed`\. For tasks `InProgress`, the response shows an estimated progress as a percentage\.

Tasks are listed in reverse chronological order\.

Currently, only tasks from the previous month can be viewed\.

### CreateRestoreImageTask<a name="CreateRestoreImageTask"></a>

The [CreateRestoreImageTask](#restore-ami) API starts a task that restores an AMI from an S3 object that was previously created by using a [CreateStoreImageTask](#store-ami) request\.

The restore task can be performed in the same or a different Region in which the store task was performed\.

The S3 bucket from which the AMI object will be restored must be in the same Region in which the restore task is requested\. The AMI will be restored in this Region\.

The AMI is restored with its metadata, such as the name, description, and block device mappings corresponding to the values of the stored AMI\. The name must be unique for AMIs in the Region for this account\. If you do not provide a name, the new AMI gets the same name as the original AMI\. The AMI gets a new AMI ID that is generated at the time of the restore process\.

The time it takes to complete the AMI restoration task depends on the size of the AMI\. It also depends on how many other tasks are in progress because tasks are queued\. You can view the progress of the task by describing the AMI \([describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html)\) or its EBS snapshots \([describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html)\)\. If the task fails, the AMI and snapshots are moved to a failed state\.

The sum of the sizes of all of the AMIs in progress is limited to 300 GB \(based on the size after restoration\) of EBS snapshot data per account\. Further task creation will be rejected until the tasks in progress are less than the limit\.

## Limitations<a name="limitations"></a>
+ Only EBS\-backed AMIs can be stored using these APIs\.
+ Paravirtual \(PV\) AMIs are not supported\.
+ The size of an AMI \(before compression\) that can be stored is limited to the size limit of a single S3 object, which is 1 TB\.
+ Quota on [store image](#store-ami) requests: 600 GB of storage work \(snapshot data\) in progress\.
+ Quota on [restore image](#restore-ami) requests: 300 GB of restore work \(snapshot data\) in progress\.
+ For the duration of the store task, the snapshots must not be deleted and the IAM principal doing the store must have access to the snapshots, otherwise the store process will fail\.
+ You can’t create multiple copies of an AMI in the same S3 bucket\.
+ An AMI that is stored in an S3 bucket can’t be restored with its original AMI ID\. You can mitigate this by using [AMI aliasing](https://docs.aws.amazon.com/systems-manager/latest/userguide/parameter-store-ec2-aliases.html)\.
+ Currently the store and restore APIs are only supported by using the AWS Command Line Interface, AWS SDKs, and Amazon EC2 API\. You can’t store and restore an AMI using the Amazon EC2 console\.

## Costs<a name="store-restore-costs"></a>

When you store and restore AMIs using S3, you are charged for the services that are used by the store and restore APIs, and for data transfer\. The APIs use S3 and the EBS Direct API \(used internally by these APIs to access the snapshot data\)\. For more information, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/) and [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.

## Securing your AMIs<a name="securing-amis"></a>

To use the store and restore APIs, the S3 bucket and the AMI must be in the same Region\. It is important to ensure that the S3 bucket is configured with sufficient security to secure the content of the AMI and that the security is maintained for as long as the AMI objects remain in the bucket\. If this can’t be done, use of these APIs is not recommended\. Ensure that public access to the S3 bucket is not allowed\. We recommend enabling [Server Side Encryption](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html) for the S3 buckets in which you store the AMIs, although it’s not required\.

For information about how to set the appropriate security settings for your S3 buckets, review the following security topics:
+ [Blocking public access to your Amazon S3 storage](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)
+ [Setting default server\-side encryption behavior for Amazon S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-encryption.html)
+ [What S3 bucket policy should I use to comply with the AWS Config rule s3\-bucket\-ssl\-requests\-only?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-bucket-policy-for-config-rule/)
+ [Enabling Amazon S3 server access logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/enable-server-access-logging.html)

When the AMI snapshots are copied to the S3 object, the data is then copied over TLS connections\. You can store AMIs with encrypted snapshots, but the snapshots are decrypted as part of the store process\.

## Permissions for storing and restoring AMIs using S3<a name="ami-s3-permissions"></a>

If your IAM principals will store or restore AMIs using S3, you need to grant them the required permissions\.

The following example policy includes all of the actions that are required to allow an IAM principal to carry out the store and restore tasks\.

You can also craft policies so that IAM principals can only access named resources\. For more example policies, see [Access management for AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:ListBucket",
                "s3:PutObject",
                "s3:AbortMultipartUpload",
                "ebs:CompleteSnapshot",
                "ebs:GetSnapshotBlock",
                "ebs:ListChangedBlocks",
                "ebs:ListSnapshotBlocks",
                "ebs:PutSnapshotBlock",
                "ebs:StartSnapshot",
                "ec2:CreateStoreImageTask",
                "ec2:DescribeStoreImageTasks",
                "ec2:CreateRestoreImageTask",
                "ec2:GetEbsEncryptionByDefault",
                "ec2:DescribeTags"
            ],
            "Resource": "*"
        }
    ]
}
```

## Work with the AMI store and restore APIs<a name="work-with-ami-store-restore"></a>

**Topics**
+ [Store an AMI in an S3 bucket](#store-ami)
+ [Describe the progress of an AMI store task](#describe-store-ami)
+ [Restore an AMI from an S3 bucket](#restore-ami)

### Store an AMI in an S3 bucket<a name="store-ami"></a>

**To store an AMI \(AWS CLI\)**  
Use the [create\-store\-image\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-store-image-task.html) command\. Specify the ID of the AMI and the name of the S3 bucket in which to store the AMI\.

```
aws ec2 create-store-image-task \
    --image-id ami-1234567890abcdef0 \
    --bucket myamibucket
```

Expected output

```
{
  "ObjectKey": "ami-1234567890abcdef0.bin"
}
```

### Describe the progress of an AMI store task<a name="describe-store-ami"></a>

**To describe the progress of an AMI store task \(AWS CLI\)**  
Use the [describe\-store\-image\-tasks](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-store-image-tasks.html) command\.

```
aws ec2 describe-store-image-tasks
```

Expected output

```
{
  "AmiId": "ami-1234567890abcdef0",
  "Bucket": "myamibucket",
  "ProgressPercentage": 17,
  "S3ObjectKey": "ami-1234567890abcdef0.bin",
  "StoreTaskState": "InProgress",
  "StoreTaskFailureReason": null,
  "TaskStartTime": "2021-01-01T01:01:01.001Z"
}
```

### Restore an AMI from an S3 bucket<a name="restore-ami"></a>

**To restore an AMI \(AWS CLI\)**  
Use the [create\-restore\-image\-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-restore-image-task.html) command\. Using the values for `S3ObjectKey` and `Bucket` from the `describe-store-image-tasks` output, specify the object key of the AMI and the name of the S3 bucket to which the AMI was copied\. Also specify a name for the restored AMI\. The name must be unique for AMIs in the Region for this account\.

**Note**  
The restored AMI gets a new AMI ID\.

```
aws ec2 create-restore-image-task \
    --object-key ami-1234567890abcdef0.bin \
    --bucket myamibucket \
    --name "New AMI Name"
```

Expected output

```
{
   "ImageId": "ami-0eab20fe36f83e1a8"
}
```
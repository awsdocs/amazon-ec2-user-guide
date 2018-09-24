# Copying an AMI<a name="CopyingAMIs"></a>

You can copy an Amazon Machine Image \(AMI\) within or across an AWS region using the AWS Management Console, the AWS AWS Command Line Interface or SDKs, or the Amazon EC2 API, all of which support the `CopyImage` action\. You can copy both Amazon EBS\-backed AMIs and instance store\-backed AMIs\. You can copy encrypted AMIs and AMIs with encrypted snapshots\.

Copying a source AMI results in an identical but distinct target AMI with its own unique identifier\. In the case of an Amazon EBS\-backed AMI, each of its backing snapshots is, by default, copied to an identical but distinct target snapshot\. \(The one exception is when you choose to encrypt the snapshot\.\) You can change or deregister the source AMI with no effect on the target AMI\. The reverse is also true\.

There are no charges for copying an AMI\. However, standard storage and data transfer rates apply\.

AWS does not copy launch permissions, user\-defined tags, or Amazon S3 bucket permissions from the source AMI to the new AMI\. After the copy operation is complete, you can apply launch permissions, user\-defined tags, and Amazon S3 bucket permissions to the new AMI\.

## Permissions for Copying an Instance Store\-Backed AMI<a name="copy-ami-permissions"></a>

If you use an IAM user to copy an instance store\-backed AMI, the user must have the following Amazon S3 permissions: `s3:CreateBucket`, `s3:GetBucketAcl`, `s3:ListAllMyBuckets`, `s3:GetObject`, `s3:PutObject`, and `s3:PutObjectAcl`\.

The following example policy allows the user to copy the AMI source in the specified bucket to the specified region\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": [
                "arn:aws:s3:::*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::ami-source-bucket/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
              "s3:CreateBucket",
              "s3:GetBucketAcl",
              "s3:PutObjectAcl",
              "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::amis-for-123456789012-in-us-east-1*"
            ]
        }
    ]
}
```

To find the Amazon Resource Name \(ARN\) of the AMI source bucket, open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/), in the navigation pane choose **AMIs**, and locate the bucket name in the **Source** column\.

## Cross\-Region AMI Copy<a name="copy-amis-across-regions"></a>

Copying an AMI across geographically diverse regions provides the following benefits:
+ Consistent global deployment: Copying an AMI from one region to another enables you to launch consistent instances in different regions based on the same AMI\.
+ Scalability: You can more easily design and build global applications that meet the needs of your users, regardless of their location\.
+ Performance: You can increase performance by distributing your application, as well as locating critical components of your application in closer proximity to your users\. You can also take advantage of region\-specific features, such as instance types or other AWS services\.
+ High availability: You can design and deploy applications across AWS regions, to increase availability\.

The following diagram shows the relations among a source AMI and two copied AMIs in different regions, as well as the EC2 instances launched from each\. When you launch an instance from an AMI, it resides in the same region where the AMI resides\. If you make changes to the source AMI and want those changes to be reflected in the AMIs in the target regions, you must recopy the source AMI to the target regions\. 

![\[AMIs copied in different regions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_copy.png)

When you first copy an instance store\-backed AMI to a region, we create an Amazon S3 bucket for the AMIs copied to that region\. All instance store\-backed AMIs that you copy to that region are stored in this bucket\. The bucket names have the following format: amis\-for\-*account*\-in\-*region*\-*hash*\. For example: `amis-for-123456789012-in-us-east-2-yhjmxvp6`\.

**Prerequisite**  
Prior to copying an AMI, you must ensure that the contents of the source AMI are updated to support running in a different region\. For example, you should update any database connection strings or similar application configuration data to point to the appropriate resources\. Otherwise, instances launched from the new AMI in the destination region may still use the resources from the source region, which can impact performance and cost\.

**Limits**
+ Destination regions are limited to 50 concurrent AMI copies at a time, with no more than 25 of those coming from a single source region\.
+ Not all regions support paravirtual \(PV\) AMIs; therefore, you cannot copy a PV AMI to those regions\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.

## Cross\-Account AMI Copy<a name="copy-ami-across-accounts"></a>

You can share an AMI with another AWS account\. Sharing an AMI does not affect the ownership of the AMI\. The owning account is charged for the storage in the region\. For more information, see [Sharing an AMI with Specific AWS Accounts](sharingamis-explicit.md)\.

If you copy an AMI that has been shared with your account, you are the owner of the target AMI in your account\. The owner of the source AMI is charged standard Amazon EBS or Amazon S3 transfer fees, and you are charged for the storage of the target AMI in the destination region\.

**Resource Permissions**  
To copy an AMI that was shared with you from another account, the owner of the source AMI must grant you read permissions for the storage that backs the AMI, either the associated EBS snapshot \(for an Amazon EBS\-backed AMI\) or an associated S3 bucket \(for an instance store\-backed AMI\)\.

**Limits**
+ You can't copy an encrypted AMI that was shared with you from another account\. Instead, if the underlying snapshot and encryption key were shared with you, you can copy the snapshot while re\-encrypting it with a key of your own\. You own the copied snapshot, and can register it as a new AMI\.
+ You can't copy an AMI with an associated `billingProduct` code that was shared with you from another account\. This includes Windows AMIs and AMIs from the AWS Marketplace\. To copy a shared AMI with a `billingProduct` code, launch an EC2 instance in your account using the shared AMI and then create an AMI from the instance\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\.

## Encryption and AMI Copy<a name="ami-copy-encryption"></a>

Encrypting during AMI copy applies only to Amazon EBS\-backed AMIs\. Because an instance store\-backed AMI does not rely on snapshots, you cannot use AMI copy to change its encryption status\.

You can use AMI copy to create a new AMI backed by encrypted Amazon EBS snapshots\. If you invoke encryption while copying an AMI, each snapshot taken of its associated Amazon EBS volumes—including the root volume—is encrypted using a key that you specify\. For more information about using AMIs with encrypted snapshots, see [AMIs with Encrypted Snapshots](AMIEncryption.md)\.

By default, the backing snapshot of an AMI is copied with its original encryption status\. Copying an AMI backed by an unencrypted snapshot results in an identical target snapshot that is also unencrypted\. If the source AMI is backed by an encrypted snapshot, copying it results in a target snapshot encrypted to the specified key\. Copying an AMI backed by multiple snapshots preserves the source encryption status in each target snapshot\. For more information about copying AMIs with multiple snapshots, see [AMIs with Encrypted Snapshots](AMIEncryption.md)\.

The following table shows encryption support for various scenarios\. Note that while it is possible to copy an unencrypted snapshot to yield an encrypted snapshot, you cannot copy an encrypted snapshot to yield an unencrypted one\.


| Scenario | Description | Supported | 
| --- | --- | --- | 
| 1 | Unencrypted\-to\-unencrypted | Yes | 
| 2 | Encrypted\-to\-encrypted | Yes | 
| 3 | Unencrypted\-to\-encrypted | Yes | 
| 4 | Encrypted\-to\-unencrypted | No | 

**Copy an unencrypted source AMI to an unencrypted target AMI**  
In this scenario, a copy of an AMI with an unencrypted single backing snapshot is created in the specified geographical region \(not shown\)\. Although this diagram shows an AMI with a single backing snapshot, you can also copy an AMI with multiple snapshots\. The encryption status of each snapshot is preserved\. Therefore, an unencrypted snapshot in the source AMI results in an unencrypted snapshot in the target AMI, and an encrypted shapshot in the source AMI results in an encrypted snapshot in the target AMI\.

![\[Copy an unencrypted source AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-unencrypted.png)

**Copy an encrypted source AMI to an encrypted target AMI**  
Although this scenario involves encrypted snapshots, it is functionally equivalent to the previous scenario\. If you apply encryption while copying a multi\-snapshot AMI, all of the target snapshots are encrypted using the specified key or the default key if none is specified\.

![\[Copy an encrypted source AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-encrypted.png)

**Copy an unencrypted source AMI to an encrypted target AMI**  
In this scenario, copying an AMI changes the encryption status of the destination image, for instance, by encrypting an unencrypted snapshot, or re\-encrypting an encrypted snapshot with a different key\. To apply encryption during the copy, you must provide an encryption flag and key\. Volumes created from the target snapshot are accessible only using this key\.

![\[Copy an unencrypted source AMI to encrypted target AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-convert.png)

## Copying an AMI<a name="ami-copy-steps"></a>

You can copy an AMI as follows\.

**Prerequisite**  
Create or obtain an AMI backed by an Amazon EBS snapshot\. Note that you can use the Amazon EC2 console to search a wide variety of AMIs provided by AWS\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) and [Finding a Linux AMI](finding-an-ami.md)\.

**To copy an AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console navigation bar, select the region that contains the AMI\. In the navigation pane, choose **Images**, **AMIs** to display the list of AMIs available to you in the region\.

1. Select the AMI to copy and choose **Actions**, **Copy AMI**\.

1. In the **Copy AMI** dialog box, specify the following information and then choose **Copy AMI**:
   + **Destination region**: The region in which to copy the AMI\.
   + **Name**: A name for the new AMI\. You can include operating system information in the name, as we do not provide this information when displaying details about the AMI\.
   + **Description**: By default, the description includes information about the source AMI so that you can distinguish a copy from its original\. You can change this description as needed\.
   + **Encryption**: Select this field to encrypt the target snapshots, or to re\-encrypt them using a different key\.
   + **Master Key**: The KMS key to used to encrypt the target snapshots\.

1. We display a confirmation page to let you know that the copy operation has been initiated and to provide you with the ID of the new AMI\.

   To check on the progress of the copy operation immediately, follow the provided link\. To check on the progress later, choose **Done**, and then when you are ready, use the navigation bar to switch to the target region \(if applicable\) and locate your AMI in the list of AMIs\.

   The initial status of the target AMI is `pending` and the operation is complete when the status is `available`\.

**To copy an AMI using the AWS CLI**  
You can copy an AMI using the [copy\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-image.html) command\. You must specify both the source and destination regions\. You specify the source region using the `--source-region` parameter\. You can specify the destination region using either the `--region` parameter or an environment variable\. For more information, see [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

When you encrypt a target snapshot during copying, you must specify these additional parameters: `--encrypted` and `--kms-key-id`\.

**To copy an AMI using the Tools for Windows PowerShell**  
You can copy an AMI using the [Copy\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Copy-EC2Image.html) command\. You must specify both the source and destination regions\. You specify the source region using the `-SourceRegion` parameter\. You can specify the destination region using either the `-Region` parameter or the `Set-AWSDefaultRegion` command\. For more information, see [Specifying AWS Regions](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)\.

When you encrypt a target snapshot during copying, you must specify these additional parameters: `-Encrypted` and `-KmsKeyId`\.

## Stopping a Pending AMI Copy Operation<a name="ami-copy-stop"></a>

You can stop a pending AMI copy as follows\.

**To stop an AMI copy operation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the destination region from the region selector\.

1. In the navigation pane, choose **AMIs**\.

1. Select the AMI to stop copying and choose **Actions**, **Deregister**\.

1. When asked for confirmation, choose **Continue**\.

**To stop an AMI copy operation using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [deregister\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/deregister-image.html) \(AWS CLI\)
+ [Unregister\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Image.html) \(AWS Tools for Windows PowerShell\)
# Copying an AMI<a name="CopyingAMIs"></a>

You can copy an Amazon Machine Image \(AMI\) within or across AWS Regions using the AWS Management Console, the AWS Command Line Interface or SDKs, or the Amazon EC2 API, all of which support the `CopyImage` action\. You can copy both Amazon EBS\-backed AMIs and instance\-store\-backed AMIs\. You can copy AMIs with encrypted snapshots and also change encryption status during the copy process\.

Copying a source AMI results in an identical but distinct target AMI with its own unique identifier\. In the case of an Amazon EBS\-backed AMI, each of its backing snapshots is, by default, copied to an identical but distinct target snapshot\. \(The sole exceptions are when you choose to encrypt or re\-encrypt the snapshot\.\) You can change or deregister the source AMI with no effect on the target AMI\. The reverse is also true\.

There are no charges for copying an AMI\. However, standard storage and data transfer rates apply\. If you copy an EBS\-backed AMI, you will incur charges for the storage of any additional EBS snapshots\. 

AWS does not copy launch permissions, user\-defined tags, or Amazon S3 bucket permissions from the source AMI to the new AMI\. After the copy operation is complete, you can apply launch permissions, user\-defined tags, and Amazon S3 bucket permissions to the new AMI\.

You can't copy an AMI that was obtained from the AWS Marketplace, regardless of whether you obtained it directly or it was shared with you\. Instead, launch an EC2 instance using the AWS Marketplace AMI and then create an AMI from the instance\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.

## Permissions for copying an instance store\-backed AMI<a name="copy-ami-permissions"></a>

If you use an IAM user to copy an instance store\-backed AMI, the user must have the following Amazon S3 permissions: `s3:CreateBucket`, `s3:GetBucketAcl`, `s3:ListAllMyBuckets`, `s3:GetObject`, `s3:PutObject`, and `s3:PutObjectAcl`\.

The following example policy allows the user to copy the AMI source in the specified bucket to the specified Region\.

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

## Cross\-Region copying<a name="copy-amis-across-regions"></a>

Copying an AMI across geographically diverse Regions provides the following benefits:
+ Consistent global deployment: Copying an AMI from one Region to another enables you to launch consistent instances in different Regions based on the same AMI\.
+ Scalability: You can more easily design and build global applications that meet the needs of your users, regardless of their location\.
+ Performance: You can increase performance by distributing your application, as well as locating critical components of your application in closer proximity to your users\. You can also take advantage of Region\-specific features, such as instance types or other AWS services\.
+ High availability: You can design and deploy applications across AWS regions, to increase availability\.

The following diagram shows the relations among a source AMI and two copied AMIs in different Regions, as well as the EC2 instances launched from each\. When you launch an instance from an AMI, it resides in the same Region where the AMI resides\. If you make changes to the source AMI and want those changes to be reflected in the AMIs in the target Regions, you must recopy the source AMI to the target Regions\.

![\[AMIs copied in different Regions\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_copy.png)

When you first copy an instance store\-backed AMI to a Region, we create an Amazon S3 bucket for the AMIs copied to that Region\. All instance store\-backed AMIs that you copy to that Region are stored in this bucket\. The bucket names have the following format: amis\-for\-*account*\-in\-*region*\-*hash*\. For example: `amis-for-123456789012-in-us-east-2-yhjmxvp6`\.

**Prerequisite**  
Prior to copying an AMI, you must ensure that the contents of the source AMI are updated to support running in a different Region\. For example, you should update any database connection strings or similar application configuration data to point to the appropriate resources\. Otherwise, instances launched from the new AMI in the destination Region may still use the resources from the source Region, which can impact performance and cost\.

**Limits**
+ Destination Regions are limited to 50 concurrent AMI copies\.
+ You cannot copy a paravirtual \(PV\) AMI to a Region that does not support PV AMIs\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.

## Cross\-account copying<a name="copy-ami-across-accounts"></a>

You can share an AMI with another AWS account\. Sharing an AMI does not affect the ownership of the AMI\. The owning account is charged for the storage in the Region\. For more information, see [Sharing an AMI with specific AWS accounts](sharingamis-explicit.md)\.

If you copy an AMI that has been shared with your account, you are the owner of the target AMI in your account\. The owner of the source AMI is charged standard Amazon EBS or Amazon S3 transfer fees, and you are charged for the storage of the target AMI in the destination Region\.

**Resource Permissions**  
To copy an AMI that was shared with you from another account, the owner of the source AMI must grant you read permissions for the storage that backs the AMI, either the associated EBS snapshot \(for an Amazon EBS\-backed AMI\) or an associated S3 bucket \(for an instance store\-backed AMI\)\. If the shared AMI has encrypted snapshots, the owner must share the key or keys with you as well\.

## Encryption and copying<a name="ami-copy-encryption"></a>

The following table shows encryption support for various AMI\-copying scenarios\. While it is possible to copy an unencrypted snapshot to yield an encrypted snapshot, you cannot copy an encrypted snapshot to yield an unencrypted one\.


| Scenario | Description | Supported | 
| --- | --- | --- | 
| 1 | Unencrypted\-to\-unencrypted | Yes | 
| 2 | Encrypted\-to\-encrypted | Yes | 
| 3 | Unencrypted\-to\-encrypted | Yes | 
| 4 | Encrypted\-to\-unencrypted | No | 

**Note**  
Encrypting during the `CopyImage` action applies only to Amazon EBS\-backed AMIs\. Because an instance store\-backed AMI does not rely on snapshots, you cannot use copying to change its encryption status\.

By default \(i\.e\., without specifying encryption parameters\), the backing snapshot of an AMI is copied with its original encryption status\. Copying an AMI backed by an unencrypted snapshot results in an identical target snapshot that is also unencrypted\. If the source AMI is backed by an encrypted snapshot, copying it results in an identical target snapshot that is encrypted by the same customer master key \(CMK\)\. Copying an AMI backed by multiple snapshots preserves, by default, the source encryption status in each target snapshot\.

If you specify encryption parameters while copying an AMI, you can encrypt or re\-encrypt its backing snapshots\. The following example shows a non\-default case that supplies encryption parameters to the `CopyImage` action in order to change the target AMI's encryption state\.

**Copy an unencrypted source AMI to an encrypted target AMI**

In this scenario, an AMI backed by an unencrypted root snapshot is copied to an AMI with an encrypted root snapshot\. The `CopyImage` action is invoked with two encryption parameters, including a CMK\. As a result, the encryption status of the root snapshot changes, so that the target AMI is backed by a root snapshot containing the same data as the source snapshot, but encrypted using the specified key\. You incur storage costs for the snapshots in both AMIs, as well as charges for any instances you launch from either AMI\.

**Note**  
Enabling [encryption by default](EBSEncryption.md#encryption-by-default) has the same effect as setting the `Encrypted` parameter to `true` for all snapshots in the AMI\.

![\[Copy AMI and encrypt snapshot on the fly\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-convert.png)

Setting the `Encrypted` parameter encrypts the single snapshot for this instance\. If you do not specify the `KmsKeyId` parameter, the default CMK is used to encrypt the snapshot copy\.

For more information about copying AMIs with encrypted snapshots, see [Using encryption with EBS\-backed AMIs](AMIEncryption.md)\.

## Copying an AMI<a name="ami-copy-steps"></a>

You can copy an AMI as follows\.

**Prerequisite**  
Create or obtain an AMI backed by an Amazon EBS snapshot\. Note that you can use the Amazon EC2 console to search a wide variety of AMIs provided by AWS\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md) and [Finding a Linux AMI](finding-an-ami.md)\.

**To copy an AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the console navigation bar, select the Region that contains the AMI\. In the navigation pane, choose **Images**, **AMIs** to display the list of AMIs available to you in the Region\.

1. Select the AMI to copy and choose **Actions**, **Copy AMI**\.

1. In the **Copy AMI** dialog box, specify the following information and then choose **Copy AMI**:
   + **Destination region**: The Region in which to copy the AMI\.
   + **Name**: A name for the new AMI\. You can include operating system information in the name, as we do not provide this information when displaying details about the AMI\.
   + **Description**: By default, the description includes information about the source AMI so that you can distinguish a copy from its original\. You can change this description as needed\.
   + **Encryption**: Select this field to encrypt the target snapshots, or to re\-encrypt them using a different key\. If you have enabled [encryption by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/encryption-by-default.html), the **Encryption** option is set and cannot be unset from the AMI console\. 
   + **Master Key**: The KMS key to used to encrypt the target snapshots\.

1. We display a confirmation page to let you know that the copy operation has been initiated and to provide you with the ID of the new AMI\.

   To check on the progress of the copy operation immediately, follow the provided link\. To check on the progress later, choose **Done**, and then when you are ready, use the navigation bar to switch to the target region \(if applicable\) and locate your AMI in the list of AMIs\.

   The initial status of the target AMI is `pending` and the operation is complete when the status is `available`\.

**To copy an AMI using the AWS CLI**  
You can copy an AMI using the [copy\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-image.html) command\. You must specify both the source and destination Regions\. You specify the source Region using the `--source-region` parameter\. You can specify the destination Region using either the `--region` parameter or an environment variable\. For more information, see [Configuring the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html)\.

When you encrypt a target snapshot during copying, you must specify these additional parameters: `--encrypted` and `--kms-key-id`\.

**To copy an AMI using the Tools for Windows PowerShell**  
You can copy an AMI using the [Copy\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Copy-EC2Image.html) command\. You must specify both the source and destination Regions\. You specify the source Region using the `-SourceRegion` parameter\. You can specify the destination Region using either the `-Region` parameter or the `Set-AWSDefaultRegion` command\. For more information, see [Specifying AWS Regions](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html)\.

When you encrypt a target snapshot during copying, you must specify these additional parameters: `-Encrypted` and `-KmsKeyId`\.

## Stopping a pending AMI copy operation<a name="ami-copy-stop"></a>

You can stop a pending AMI copy as follows\.

**To stop an AMI copy operation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the destination Region from the Region selector\.

1. In the navigation pane, choose **AMIs**\.

1. Select the AMI to stop copying and choose **Actions**, **Deregister**\.

1. When asked for confirmation, choose **Continue**\.

**To stop an AMI copy operation using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [deregister\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/deregister-image.html) \(AWS CLI\)
+ [Unregister\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Unregister-EC2Image.html) \(AWS Tools for Windows PowerShell\)
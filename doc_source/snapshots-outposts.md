# Amazon EBS local snapshots on Outposts<a name="snapshots-outposts"></a>

Amazon EBS snapshots are a point\-in\-time copy of your EBS volumes\.

By default, snapshots of EBS volumes on an Outpost are stored in Amazon S3 in the Region of the Outpost\. You can also use Amazon EBS local snapshots on Outposts to store snapshots of volumes on an Outpost locally in Amazon S3 on the Outpost itself\. This ensures that the snapshot data resides on the Outpost, and on your premises\. In addition, you can use AWS Identity and Access Management \(IAM\) policies and permissions to set up data residency enforcement policies to ensue that snapshot data does not leave the Outpost\. This is especially useful if you reside in a country or region that is not yet served by an AWS Region and that has data residency requirements\.

This topic provides information about working with Amazon EBS local snapshots on Outposts\. For more information about Amazon EBS snapshots and about working with snapshots in an AWS Region, see [Amazon EBS snapshots](EBSSnapshots.md)\.

For more information about AWS Outposts, see [ AWS Outposts Features](http://aws.amazon.com/outposts/features/) and the [AWS Outposts User Guide](https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.html)\. For pricing information, see [AWS Outposts pricing](http://aws.amazon.com/outposts/pricing/)\.

**Topics**
+ [Frequently asked questions](#faq)
+ [Prerequisites](#prereqs)
+ [Considerations](#considerations)
+ [Controlling access with IAM](#iam)
+ [Working with local snapshots](#using)

## Frequently asked questions<a name="faq"></a>

**1\. What are local snapshots?**  
By default, Amazon EBS snapshots of volumes on an Outpost are stored in Amazon S3 in the Region of the Outpost\. If the Outpost is provisioned with Amazon S3 on Outposts, you can choose to store the snapshots locally on the Outpost itself\. Local snapshots are incremental, which means that only the blocks of the volume that have changed after your most recent snapshot are saved\. You can use these snapshots to restore a volume on the same Outpost as the snapshot at any time\. For more information about Amazon EBS snapshots, see [Amazon EBS snapshots](EBSSnapshots.md)\.

**2\. Why should I use local snapshots?**  
Snapshots are a convenient way of backing up your data\. With local snapshots, all of your snapshot data is stored locally on the Outpost\. This means that it does not leave your premises\. This is especially useful if you reside in a country or region that is not yet served by an AWS Region and that has residency requirements\.  
Additionally, using local snapshots can help to reduce the bandwidth used for communication between the Region and the Outpost in bandwidth constrained environments\.

**3\. How do I enforce snapshot data residency on Outposts?**  
You can use AWS Identity and Access Management \(IAM\) policies to control the permissions that principals \(AWS accounts, IAM users, and IAM roles\) have when working with local snapshots and to enforce data residency\. You can create a policy that prevents principals from creating snapshots from Outpost volumes and instances and storing the snapshots in an AWS Region\. Currently, copying snapshots and images from an Outpost to a Region is not supported\. For more information, see [Controlling access with IAM](#iam)\.

**4\. Are multi\-volume, crash\-consistent local snapshots supported?**  
Yes, you can create multi\-volume, crash\-consistent local snapshots from instances on an Outpost\.

**5\. How do I create local snapshots?**  
You can create snapshots manually using the AWS Command Line Interface \(AWS CLI\) or the Amazon EC2 console\. For more information see, [Working with local snapshots](#using)\. You can also automate the lifecycle of local snapshots using Amazon Data Lifecycle Manager\. For more information see, [Automate snapshots on an Outpost](#dlm)\.

**6\. Can I create, use, or delete local snapshots if my Outpost loses connectivity to its Region?**  
No\. The Outpost must have connectivity with its Region as the Region provides the access, authorization, logging, and monitoring services that are critical for your snapshots' health\. If there is no connectivity, you can't create new local snapshots, create volumes or launch instances from existing local snapshots, or delete local snapshots\.

**7\. How quickly is Amazon S3 storage capacity made available after deleting local snapshots?**  
Amazon S3 storage capacity becomes available within 72 hours after deleting local snapshots and the volumes that reference them\.

**8\. How can I ensure that I do not run out of Amazon S3 capacity on my Outpost?**  
We recommend that you use Amazon CloudWatch alarms to monitor your Amazon S3 storage capacity, and delete snapshots and volumes that you no longer need to avoid running out of storage capacity\. If you are using Amazon Data Lifecycle Manager to automate the lifecycle of local snapshots, ensure that your snapshot retention policies do not retain snapshots for longer than is needed\.

**9\. What happens if I run out of local Amazon S3 capacity on my Outposts?**  
If you run out of local Amazon S3 capacity on your Outposts, Amazon Data Lifecycle Manager will not be able to successfully create local snapshots on the Outposts\. Amazon Data Lifecycle Manager will attempt to create the local snapshots on the Outposts, but the snapshots immediately transition to the `error` state and they are eventually deleted by Amazon Data Lifecycle Manager\. We recommend that you use the `SnapshotsCreateFailed` Amazon CloudWatch metric to monitor your snapshot lifecycle policies for snapshot creation failures\. For more information, see [Monitor your policies using Amazon CloudWatch](monitor-dlm-cw-metrics.md)\.

**10\. Can I use local snapshots and AMIs backed by local snapshots with Spot Instances and Spot Fleet?**  
No, you can't use local snapshots or AMIs backed by local snapshots to launch Spot Instances or a Spot Fleet\.

**11\. Can I use local snapshots and AMIs backed by local snapshots with Amazon EC2 Auto Scaling?**  
Yes, you can use local snapshots and AMIs backed by local snapshots to launch Auto Scaling groups in a subnet that is on the same Outpost as the snapshots\. The Amazon EC2 Auto Scaling group service\-linked role must have permission to use the KMS key used to encrypt the snapshots\.  
You can't use local snapshots or AMIs backed by local snapshots to launch Auto Scaling groups in an AWS Region\.

## Prerequisites<a name="prereqs"></a>

To store snapshots on an Outpost, you must have an Outpost that is provisioned with Amazon S3 on Outposts\. For more information about Amazon S3 on Outposts, see [Using Amazon S3 on Outposts](https://docs.aws.amazon.com/AmazonS3/latest/dev/S3onOutposts.html) in the *Amazon Simple Storage Service User Guide*\.

## Considerations<a name="considerations"></a>

Keep the following in mind when working with local snapshots\.
+ Outposts must have connectivity to their AWS Region to use local snapshots\.
+ Snapshot metadata is stored in the AWS Region associated with the Outpost\. This does not include any snapshot data\.
+ Snapshots stored on Outposts are encrypted by default\. Unencrypted snapshots are not supported\. Snapshots that are created on an Outpost and snapshots that are copied to an Outpost are encrypted using the default KMS key for the Region or a different KMS key that you specify at the time of the request\.
+ When you create a volume on an Outpost from a local snapshot, you cannot re\-encrypt the volume using a different KMS key\. Volumes created from local snapshots must be encrypted using the same KMS key as the source snapshot\.
+ After you delete local snapshots from an Outpost, the Amazon S3 storage capacity used by the deleted snapshots becomes available within 72 hours\. For more information, see [Delete local snapshots](#delete-snapshots)\.
+ You can't export local snapshots from an Outpost\.
+ You can't enable fast snapshot restore for local snapshots\.
+ EBS direct APIs are not supported with local snapshots\.
+ You can't copy local snapshots or AMIs from an Outpost to an AWS Region, from one Outpost to another, or within an Outpost\. However, you can copy snapshots from an AWS Region to an Outpost\. For more information, see [Copy snapshots from an AWS Region to an Outpost](#copy-snapshots)\.
+ When copying a snapshot from an AWS region to an Outpost, the data is transferred over the service link\. Copying multiple snapshots simultaneously could impact other services running on the Outpost\.
+ You can't share local snapshots\.
+ You must use IAM policies to ensure that your data residency requirements are met\. For more information, see [Controlling access with IAM](#iam)\.
+ Local snapshots are incremental backups\. Only the blocks in the volume that have changed after your most recent snapshot are saved\. Each local snapshot contains all of the information that is needed to restore your data \(from the moment when the snapshot was taken\) to a new EBS volume\. For more information, see [How incremental snapshots work](EBSSnapshots.md#how_snapshots_work)\.
+ You can’t use IAM policies to enforce data residency for **CopySnapshot** and **CopyImage** actions\.

## Controlling access with IAM<a name="iam"></a>

You can use AWS Identity and Access Management \(IAM\) policies to control the permissions that principals \(AWS accounts, IAM users, and IAM roles\) have when working with local snapshots\. The following are example policies that you can use to grant or deny permission to perform specific actions with local snapshots\.

**Important**  
Copying snapshots and images from an Outpost to a Region is currently not supported\. As result, you currently can’t use IAM policies to enforce data residency for **CopySnapshot** and **CopyImage** actions\.

**Topics**
+ [Enforce data residency for snapshots](#enforce-residency-snapshot)
+ [Prevent principals from deleting local snapshots](#deny-delete)

### Enforce data residency for snapshots<a name="enforce-residency-snapshot"></a>

The following example policy prevents all principals from creating snapshots from volumes and instances on Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef` and storing the snapshot data in an AWS Region\. Principals can still create local snapshots\. This policy ensures that all snapshots remain on the Outpost\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [
                "ec2:CreateSnapshot",
                "ec2:CreateSnapshots"
            ],
            "Resource": "arn:aws:ec2:us-east-1::snapshot/*",
            "Condition": {
                "StringEquals": {
                    "ec2:SourceOutpostArn": "arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0"
                },
                "Null": {
                    "ec2:OutpostArn": "true"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSnapshot",
                "ec2:CreateSnapshots"
            ],
            "Resource": "*"
        }
    ]
}
```

### Prevent principals from deleting local snapshots<a name="deny-delete"></a>

The following example policy prevents all principals from deleting local snapshots that are stored on Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [
                "ec2:DeleteSnapshot"
            ],
            "Resource": "arn:aws:ec2:us-east-1::snapshot/*",
            "Condition": {
                "StringEquals": {
                    "ec2:OutpostArn": "arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DeleteSnapshot"
            ],
            "Resource": "*"
        }
    ]
}
```

## Working with local snapshots<a name="using"></a>

The following sections explain how to use local snapshots\.

**Topics**
+ [Rules for storing snapshots](#lineage)
+ [Create local snapshots from volumes on an Outpost](#create-snapshot)
+ [Create multi\-volume local snapshots from instances on an Outpost](#create-multivol-snapshot)
+ [Create AMIs from local snapshots](#ami)
+ [Copy snapshots from an AWS Region to an Outpost](#copy-snapshots)
+ [Copy AMIs from an AWS Region to an Outpost](#copy-amis)
+ [Create volumes from local snapshots](#volumes)
+ [Launch instances from AMIs backed by local snapshots](#instances)
+ [Delete local snapshots](#delete-snapshots)
+ [Automate snapshots on an Outpost](#dlm)

### Rules for storing snapshots<a name="lineage"></a>

The following rules apply to snapshot storage:
+ If the most recent snapshot of a volume is stored on an Outpost, then all successive snapshots must be stored on the same Outpost\. 
+ If the most recent snapshot of a volume is stored in an AWS Region, then all successive snapshots must be stored in the same Region\. To start creating local snapshots from that volume, do the following:

  1. Create a snapshot of the volume in the AWS Region\.

  1. Copy the snapshot to the Outpost from the AWS Region\.

  1. Create a new volume from the local snapshot\.

  1. Attach the volume to an instance on the Outpost\.

  For the new volume on the Outpost, the next snapshot can be stored on the Outpost or in the AWS Region\. All successive snapshots must then be stored in that same location\.
+ Local snapshots, including snapshots created on an Outpost and snapshots copied to an Outpost from an AWS Region, can be used only to create volumes on the same Outpost\. 
+ If you create a volume on an Outpost from a snapshot in a Region, then all successive snapshots of that new volume must be in the same Region\.
+ If you create a volume on an Outpost from a local snapshot, then all successive snapshots of that new volume must be on the same Outpost\.

### Create local snapshots from volumes on an Outpost<a name="create-snapshot"></a>

You can create local snapshots from volumes on your Outpost\. You can choose to store the snapshots on the same Outpost as the source volume, or in the Region for the Outpost\.

Local snapshots can be used to create volumes on the same Outpost only\.

You can create local snapshots from volumes on an Outpost using one of the following methods\.

------
#### [ Console ]

**To create local snapshots from volumes on an Outpost**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Select the volume on the Outpost, and choose **Actions**, **Create Snapshot**\.

1. \(Optional\) For **Description**, enter a brief description for the snapshot\.

1. For **Snapshot destination**, choose **AWS Outpost**\. The snapshot will be created on the same Outpost as the source volume\. The **Outpost ARN** field shows the Amazon Resource Name \(ARN\) of the destination Outpost\.

1. \(Optional\) Choose **Add Tag** to add tags to your snapshot\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Snapshot**\.

------
#### [ Command line ]

**To create local snapshots from volumes on an Outpost**  
Use the [ create\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) command\. Specify the ID of the volume from which to create the snapshot, and the ARN of the destination Outpost on which to store the snapshot\. If you omit the Outpost ARN, the snapshot is stored in the AWS Region for the Outpost\.

For example, the following command creates a local snapshot of volume `vol-1234567890abcdef0`, and stores the snapshot on Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0`\.

```
$ aws ec2 create-snapshot --volume-id vol-1234567890abcdef0 --outpost-arn arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0 --description "single volume local snapshot"
```

------

### Create multi\-volume local snapshots from instances on an Outpost<a name="create-multivol-snapshot"></a>

You can create crash\-consistent multi\-volume local snapshots from instances on your Outpost\. You can choose to store the snapshots on the same Outpost as the source instance, or in the Region for the Outpost\.

Multi\-volume local snapshots can be used to create volumes on the same Outpost only\.

You can create multi\-volume local snapshots from instances on an Outpost using one of the following methods\.

------
#### [ Console ]

**To create multi\-volume local snapshots from instances on an Outpost**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose **Create Snapshot**\.

1. For **Select resource type**, choose **Instance**\.

1. For **Instance ID**, select the instance on the Outpost from which to create the snapshots\.

1. \(Optional\) For **Description**, enter a brief description for the snapshots\.

1. For **Snapshot destination**, choose **AWS Outpost**\. The snapshots will be created on the same Outpost as the source instance\. The **Outpost ARN** shows the ARN of the destination Outpost\.

1. \(Optional\) To exclude the root volume from being snapshotted, select **Exclude root volume**\.

1. \(Optional\) To automatically copy tags from the source volume to the snapshots, select **Copy tags from volume**\. This sets snapshot metadata—such as access policies, attachment information, and cost allocation—to match the source volume\.

1. \(Optional\) Choose **Add Tag** to add tags to your snapshot\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Snapshot**\.

   During snapshot creation, the snapshots are managed together\. If one of the snapshots in the volume set fails, the other snapshots in the volume set are moved to error status\.

------
#### [ Command line ]

**To create multi\-volume local snapshots from instances on an Outpost**  
Use the [ create\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshots.html) command\. Specify the ID of the instance from which to create the snapshots, and the ARN of the destination Outpost on which to store the snapshots\. If you omit the Outpost ARN, the snapshots are stored in the AWS Region for the Outpost\.

For example, the following command creates snapshots of the volumes attached to instance `i-1234567890abcdef0` and stores the snapshots on Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0`\.

```
$ aws ec2 create-snapshots --instance-specification InstanceId=i-1234567890abcdef0 --outpost-arn arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0 --description "multi-volume local snapshots"
```

------

### Create AMIs from local snapshots<a name="ami"></a>

You can create Amazon Machine Images \(AMIs\) using a combination of local snapshots and snapshots that are stored in the Region of the Outpost\. For example, if you have an Outpost in `us-east-1`, you can create an AMI with data volumes that are backed by local snapshots on that Outpost, and a root volume that is backed by a snapshot in the `us-east-1` Region\.

**Note**  
You can't create AMIs that include backing snapshots stored across multiple Outposts\.
You can’t currently create AMIs directly from instances on an Outposts using **CreateImage** API or the Amazon EC2 console for Outposts that are enabled with Amazon S3 on Outposts\.
AMIs that are backed by local snapshots can be used to launch instances on the same Outpost only\.

**To create an AMI on an Outpost from snapshots in a Region**

1. Copy the snapshots from the Region to the Outpost\. For more information, see [Copy snapshots from an AWS Region to an Outpost](#copy-snapshots)\.

1. Use the Amazon EC2 console or the [ register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command to create the AMI using the snapshot copies on the Outpost\. For more information, see [ Creating an AMI from a snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html#creating-launching-ami-from-snapshot)\.

**To create an AMI on an Outpost from an instance on an Outpost**

1. Create snapshots from the instance on the Outpost and store the snapshots on the Outpost\. For more information, see [Create multi\-volume local snapshots from instances on an Outpost](#create-multivol-snapshot)\.

1. Use the Amazon EC2 console or the [ register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command to create the AMI using the local snapshots\. For more information, see [ Creating an AMI from a snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html#creating-launching-ami-from-snapshot)\.

**To create an AMI in a Region from an instance on an Outpost**

1. Create snapshots from the instance on the Outpost and store the snapshots in the Region\. For more information, see [Create local snapshots from volumes on an Outpost](#create-snapshot) or [Create multi\-volume local snapshots from instances on an Outpost](#create-multivol-snapshot)\.

1. Use the Amazon EC2 console or the [ register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command to create the AMI using the snapshot copies in the Region\. For more information, see [ Creating an AMI from a snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/creating-an-ami-ebs.html#creating-launching-ami-from-snapshot)\.

### Copy snapshots from an AWS Region to an Outpost<a name="copy-snapshots"></a>

You can copy snapshots from an AWS Region to an Outpost\. You can do this only if the snapshots are in the Region for the Outpost\. If the snapshots are in a different Region, you must first copy the snapshot to the Region for the Outpost, and then copy it from that Region to the Outpost\.

**Note**  
You can't copy local snapshots from an Outpost to a Region, from one Outpost to another, or within the same Outpost\.

You can copy snapshots from a Region to an Outpost using one of the following methods\.

------
#### [ Console ]

**To copy a snapshot from an AWS Region to an Outpost**

Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the snapshot in the Region, and choose **Actions**, **Copy**\.

1. For **Destination Region**, choose the Region for the destination Outpost\.

1. For **Snapshot Destination**, choose **AWS Outpost**\.

   The **Snapshot Destination** field only appears if you have Outposts in the selected destination Region\. If the field does not appear, you do not have any Outposts in the selected destination Region\.

1. For **Destination Outpost ARN**, enter the ARN of the Outpost to which to copy the snapshot\.

1. \(Optional\) For **Description**, enter a brief description of the copied snapshot\.

1. Encryption is enabled by default for the snapshot copy\. Encryption cannot be disabled\. For **KMS key**, choose the KMS key to use\.

1. Choose **Copy**\.

------
#### [ Command line ]

**To copy a snapshot from a Region to an Outpost**  
Use the [ copy\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-snapshot.html) command\. Specify the ID of the snapshot to copy, the Region from which to copy the snapshot, and the ARN of the destination Outpost\.

For example, the following command copies snapshot `snap-1234567890abcdef0` from the `us-east-1` Region to Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0`\.

```
$ aws ec2 copy-snapshot --source-region us-east-1 --source-snapshot-id snap-1234567890abcdef0 --destination-outpost-arn arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0 --description "Local snapshot copy"
```

------

### Copy AMIs from an AWS Region to an Outpost<a name="copy-amis"></a>

You can copy AMIs from an AWS Region to an Outpost\. When you copy an AMI from a Region to an Outpost, all of the snapshots associated with the AMI are copied from the Region to the Outpost\.

You can copy an AMI from a Region to an Outpost only if the snapshots associated with the AMI are in the Region for the Outpost\. If the snapshots are in a different Region, you must first copy the AMI to the Region for the Outpost, and then copy it from that Region to the Outpost\.

**Note**  
You can't copy an AMI from an Outpost to a Region, from one Outpost to another, or within an Outpost\.

You can copy AMIs from a Region to an Outpost using the AWS CLI only\.

------
#### [ Command line ]

**To copy an AMI from a Region to an Outpost**  
Use the [ copy\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-image.html) command\. Specify the ID of the AMI to copy, the source Region, and the ARN of the destination Outpost\.

For example, the following command copies AMI `ami-1234567890abcdef0` from the `us-east-1` Region to Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0`\.

```
$ aws ec2 copy-image --source-region us-east-1 --source-image-id ami-1234567890abcdef0 --name "Local AMI copy"  --destination-outpost-arn arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0
```

------

### Create volumes from local snapshots<a name="volumes"></a>

You can create volumes on Outposts from local snapshots\. Volumes must be created on the same Outpost as the source snapshots\. You cannot use local snapshots to create volumes in the Region for the Outpost\.

When you create a volume from a local snapshot, you cannot re\-encrypt the volume using different KMS key\. Volumes created from local snapshots must be encrypted using the same KMS key as the source snapshot\.

For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

### Launch instances from AMIs backed by local snapshots<a name="instances"></a>

You can launch instances from AMIs that are backed by local snapshots\. You must launch Instances on the same Outpost as the source AMI\. For more information, see [ Launch an instance on your Outpost](https://docs.aws.amazon.com/outposts/latest/userguide/launch-instance.html) in the *AWS Outposts User Guide*\.

### Delete local snapshots<a name="delete-snapshots"></a>

You can delete local snapshots from an Outpost\. After you delete a snapshot from an Outpost, the Amazon S3 storage capacity used by the deleted snapshot becomes available within 72 hours after deleting the snapshot and the volumes that reference that snapshot\. 

Because Amazon S3 storage capacity does not become available immediately, we recommend that you use Amazon CloudWatch alarms to monitor your Amazon S3 storage capacity\. Delete snapshots and volumes that you no longer need to avoid running out of storage capacity\.

For more information about deleting snapshots, see [Delete a snapshot](ebs-deleting-snapshot.md#ebs-delete-snapshot)\.

### Automate snapshots on an Outpost<a name="dlm"></a>

You can create Amazon Data Lifecycle Manager snapshot lifecycle policies that automatically create, copy, retain, and delete snapshots of your volumes and instances on an Outpost\. You can choose whether to store the snapshots in a Region or whether to store them locally on an Outpost\. Additionally, you can automatically copy snapshots that are created and stored in an AWS Region to an Outpost\.

The following table shows provides and Overview of the supported features\.


| 
| 
| Resource location | Snapshot destination | Cross\-region copy | Fast snapshot restore | Cross\-account sharing | 
| --- |--- |--- |--- |--- |
| To Region | To Outpost | 
| --- |--- |
| Region | Region | ✓ | ✓ | ✓ | ✓ | 
| Outpost | Region | ✓ | ✓ | ✓ | ✓ | 
| Outpost | Outpost | ✗ | ✗ | ✗ | ✗ | 

**Considerations**
+ Only Amazon EBS snapshot lifecycle policies are currently supported\. EBS\-backed AMI policies and Cross\-account sharing event policies are not supported\.
+ If a policy manages snapshots for volumes or instances in a Region, then snapshots are created in the same Region as the source resource\.
+ If a policy manages snapshots for volumes or instances on an Outpost, then snapshots can be created on the source Outpost, or in the Region for that Outpost\.
+ A single policy can't manage both snapshots in a Region and snapshots on an Outpost\. If you need to automate snapshots in a Region and on an Outpost, you must create separate policies\.
+ Fast snapshot restore is not supported for snapshots created on an Outpost, or for snapshots copied to an Outpost\.
+ Cross\-account sharing is not supported for snapshots created on an Outpost\.

For more information about creating a snapshot lifecycle that manages local snapshots, see [Automating snapshot lifecycles](snapshot-ami-policy.md)\.
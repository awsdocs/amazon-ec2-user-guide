# AMIs with Encrypted Snapshots<a name="AMIEncryption"></a>

AMIs that are backed by Amazon EBS snapshots can take advantage of Amazon EBS encryption\. Snapshots of both data and root volumes can be encrypted and attached to an AMI\.

EC2 instances with encrypted volumes are launched from AMIs in the same way as other instances\. 

The `CopyImage` action can be used to create an AMI with encrypted snapshots from an AMI with unencrypted snapshots\. By default, `CopyImage` preserves the encryption status of source snapshots when creating destination copies\. However, you can configure the parameters of the copy process to also encrypt the destination snapshots\. 

Snapshots can be encrypted with either your default AWS Key Management Service customer master key \(CMK\), or with a custom key that you specify\. You must in all cases have permission to use the selected key\. If you have an AMI with encrypted snapshots, you can choose to re\-encrypt them with a different encryption key as part of the `CopyImage` action\. `CopyImage` accepts only one key at a time and encrypts all of an image's snapshots \(whether root or data\) to that key\. However, it is possible to manually build an AMI with snapshots encrypted to multiple keys\.

Support for creating AMIs with encrypted snapshots is accessible through the Amazon EC2 console, Amazon EC2 API, or the AWS CLI\. 

The encryption parameters of `CopyImage` are available in all regions where AWS KMS is available\.

## AMI Scenarios Involving Encrypted EBS Snapshots<a name="AMIEncryption_scenarios"></a>

You can copy an AMI and simultaneously encrypt its associated EBS snapshots using the AWS Management Console or the command line\.

### Copying an AMI with an Encrypted Data Snapshot<a name="w3ab1c15c37c15b4"></a>

In this scenario, an EBS\-backed AMI has an unencrypted root snapshot and an encrypted data snapshot, shown in step 1\. The `CopyImage` action is invoked in step 2 without encryption parameters\. As a result, the encryption status of each snapshot is preserved, so that the destination AMI, in step 3, is also backed by an unencrypted root snapshot and an encrypted data snapshot\. Though the snapshots contain the same data, they are distinct from each other and you will incur storage costs for the snapshots in both AMIs, as well as charges for any instances you launch from either AMI\.

![\[Copy an AMI with encrypted data snapshot\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-mixed.png)

You can perform a simple copy such as this using either the Amazon EC2 console or the command line\. For more information, see [Copying an AMI](CopyingAMIs.md)\.

### Copying an AMI Backed by An Encrypted Root Snapshot<a name="w3ab1c15c37c15b6"></a>

In this scenario, an Amazon EBS\-backed AMI has an encrypted root snapshot, shown in step 1\. The `CopyImage` action is invoked in step 2 without encryption parameters\. As a result, the encryption status of the snapshot is preserved, so that the destination AMI, in step 3, is also backed by an encrypted root snapshot\. Though the root snapshots contain identical system data, they are distinct from each other and you will incur storage costs for the snapshots in both AMIs, as well as charges for any instances you launch from either AMI\.

![\[Copy an AMI backed by encrypted root snapshot\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-encrypted.png)

You can perform a simple copy such as this using either the Amazon EC2 console or the command line\. For more information, see [Copying an AMI](CopyingAMIs.md)\.

### Creating an AMI with Encrypted Root Snapshot from an Unencrypted AMI<a name="w3ab1c15c37c15b8"></a>

In this scenario, an Amazon EBS\-backed AMI has an unencrypted root snapshot, shown in step 1, and an AMI is created with an encrypted root snapshot, shown in step 3\. The `CopyImage` action in step 2 is invoked with two encryption parameters, including the choice of a CMK\. As a result, the encryption status of the root snapshot changes, so that the target AMI is backed by a root snapshot containing the same data as the source snapshot, but encrypted using the specified key\. You will incur storage costs for the snapshots in both AMIs, as well as charges for any instances you launch from either AMI\.

![\[Create an AMI from unencrypted AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-convert.png)

You can perform a copy and encrypt operation such as this using either the Amazon EC2 console or the command line\. For more information, see [Copying an AMI](CopyingAMIs.md)\.

### Creating an AMI with an Encrypted Root Snapshot from a Running Instance<a name="w3ab1c15c37c15c10"></a>

In this scenario, an AMI is created from a running EC2 instance\. The running instance in step 1 has an encrypted root volume, and the created AMI in step 3 has a root snapshot encrypted to the same key as the source volume\. The `CreateImage` action has exactly the same behavior whether or not encryption is present\.

![\[Create an AMI from instance with encrypted root snapshot\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/running-instance-encrypted.png)

You can create an AMI from a running Amazon EC2 instance \(with or without encrypted volumes\) using either the Amazon EC2 console or the command line\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\. 

### Creating an AMI with Unique CMKs for Each Encrypted Snapshot<a name="w3ab1c15c37c15c12"></a>

This scenario starts with an AMI backed by a root\-volume snapshot \(encrypted to key \#1\), and finishes with an AMI that has two additional data\-volume snapshots attached \(encrypted to key \#2 and key \#3\)\. The `CopyImage` action cannot apply more than one encryption key in a single operation\. However, you can create an AMI from an instance that has multiple attached volumes encrypted to different keys\. The resulting AMI has snapshots encrypted to those keys and any instance launched from this new AMI also has volumes encrypted to those keys\.

The steps of this example procedure correspond to the following diagram\.

1. Start with the source AMI backed by vol\. \#1 \(root\) snapshot, which is encrypted with key \#1\.

1. Launch an EC2 instance from the source AMI\.

1. Create EBS volumes vol\. \#2 \(data\) and vol\. \#3 \(data\), encrypted to key \#2 and key \#3 respectively\.

1. Attach the encrypted data volumes to the EC2 instance\.

1. The EC2 instance now has an encrypted root volume as well as two encrypted data volumes, all using different keys\.

1. Use the `CreateImage` action on the EC2 instance\.

1. The resulting target AMI contains encrypted snapshots of the three EBS volumes, all using different keys\.

![\[Create AMIs with unique CMKs\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/multi-key.png)

You can carry out this procedure using either the Amazon EC2 console or the command line\. For more information, see the following topics:

+ [Launch Your Instance](LaunchingAndUsingInstances.md)

+ [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\.

+ [Amazon EBS Volumes](EBSVolumes.md)

+ [AWS Key Management](http://docs.aws.amazon.com/kms/latest/developerguide/getting-started.html) in the *AWS Key Management Service Developer Guide*
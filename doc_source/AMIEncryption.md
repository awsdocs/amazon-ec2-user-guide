# Using encryption with EBS\-backed AMIs<a name="AMIEncryption"></a>

AMIs that are backed by Amazon EBS snapshots can take advantage of Amazon EBS encryption\. Snapshots of both data and root volumes can be encrypted and attached to an AMI\. You can launch instances and copy images with full EBS encryption support included\. Encryption parameters for these operations are supported in all Regions where AWS KMS is available\.

EC2 instances with encrypted EBS volumes are launched from AMIs in the same way as other instances\. In addition, when you launch an instance from an AMI backed by unencrypted EBS snapshots, you can encrypt some or all of the volumes during launch\. 

Like EBS volumes, snapshots in AMIs can be encrypted by either your default AWS Key Management Service customer master key \(CMK\), or to a customer managed key that you specify\. You must in all cases have permission to use the selected key\.

AMIs with encrypted snapshots can be shared across AWS accounts\. For more information, see [Shared AMIs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/sharing-amis.html)\.

## Instance\-launching scenarios<a name="AMI-encryption-launch"></a>

Amazon EC2 instances are launched from AMIs using the `RunInstances` action with parameters supplied through block device mapping, either by means of the AWS Management Console or directly using the Amazon EC2 API or CLI\. For more information about block device mapping, see [Block device mapping](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/block-device-mapping-concepts.html)\. For examples of controlling block device mapping from the AWS CLI, see [Launch, List, and Terminate EC2 Instances](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-instances.html)\.

By default, without explicit encryption parameters, a `RunInstances` action maintains the existing encryption state of an AMI's source snapshots while restoring EBS volumes from them\. If [Encryption by default](EBSEncryption.md#encryption-by-default) is enabled, all volumes created from the AMI \(whether from encrypted or unencrypted snapshots\) will be encrypted\. If encryption by default is not enabled, then the instance maintains the encryption state of the AMI\.

You can also launch an instance and simultaneously apply a new encryption state to the resulting volumes by supplying encryption parameters\. Consequently, the following behaviors are observed:

**Launch with no encryption parameters**
+ An unencrypted snapshot is restored to an unencrypted volume, unless encryption by default is enabled, in which case all the newly created volumes will be encrypted\.
+ An encrypted snapshot that you own is restored to a volume that is encrypted to the same CMK\.
+ An encrypted snapshot that you do not own \(for example, the AMI is shared with you\) is restored to a volume that is encrypted by your AWS account's default CMK\.

The default behaviors can be overridden by supplying encryption parameters\. The available parameters are `Encrypted` and `KmsKeyId`\. Setting only the `Encrypted` parameter results in the following:

**Instance launch behaviors with `Encrypted` set, but no `KmsKeyId` specified**
+ An unencrypted snapshot is restored to an EBS volume that is encrypted by your AWS account's default CMK\.
+ An encrypted snapshot that you own is restored to an EBS volume encrypted by the same CMK\. \(In other words, the `Encrypted` parameter has no effect\.\)
+ An encrypted snapshot that you do not own \(i\.e\., the AMI is shared with you\) is restored to a volume that is encrypted by your AWS account's default CMK\. \(In other words, the `Encrypted` parameter has no effect\.\)

Setting both the `Encrypted` and `KmsKeyId` parameters allows you to specify a non\-default CMK for an encryption operation\. The following behaviors result:

**Instance with both `Encrypted` and `KmsKeyId` set**
+ An unencrypted snapshot is restored to an EBS volume encrypted by the specified CMK\.
+ An encrypted snapshot is restored to an EBS volume encrypted not to the original CMK, but instead to the specified CMK\.

Submitting a `KmsKeyId` without also setting the `Encrypted` parameter results in an error\.

The following sections provide examples of launching instances from AMIs using non\-default encryption parameters\. In each of these scenarios, parameters supplied to the `RunInstances` action result in a change of encryption state during restoration of a volume from a snapshot\.

**Note**  
For detailed console procedures to launch an instance from an AMI, see [Launch Your Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/LaunchingAndUsingInstances.html)\.  
For documentation of the `RunInstances` API, see [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html)\.  
For documentation of the `run-instances` command in the AWS Command Line Interface, see [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html)\.

### Encrypt a volume during launch<a name="launch1"></a>

In this example, an AMI backed by an unencrypted snapshot is used to launch an EC2 instance with an encrypted EBS volume\.

![\[Launch instance and encrypt volume on the fly\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-launch-convert.png)

The `Encrypted` parameter alone results in the volume for this instance being encrypted\. Providing a `KmsKeyId` parameter is optional\. If no key ID is specified, the AWS account's default CMK is used to encrypt the volume\. To encrypt the volume to a different CMK that you own, supply the `KmsKeyId` parameter\. 

### Re\-encrypt a volume during launch<a name="launch2"></a>

In this example, an AMI backed by an encrypted snapshot is used to launch an EC2 instance with an EBS volume encrypted by a new CMK\. 

![\[Launch instance and re-encrypt volume on the fly\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-launch-encrypted.png)

If you own the AMI and supply no encryption parameters, the resulting instance has a volume encrypted by the same key as the snapshot\. If the AMI is shared rather than owned by you, and you supply no encryption parameters, the volume is encrypted by your default CMK\. With encryption parameters supplied as shown, the volume is encrypted by the specified CMK\.

### Change encryption state of multiple volumes during launch<a name="launch3"></a>

In this more complex example, an AMI backed by multiple snapshots \(each with its own encryption state\) is used to launch an EC2 instance with a newly encrypted volume and a re\-encrypted volume\.

![\[Encrypt and re-encrypt multiple volumes during launch\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-launch-mixed.png)

In this scenario, the `RunInstances` action is supplied with encryption parameters for each of the source snapshots\. When all possible encryption parameters are specified, the resulting instance is the same regardless of whether you own the AMI\.

## Image\-copying scenarios<a name="AMI-encryption-copy"></a>

Amazon EC2 AMIs are copied using the `CopyImage` action, either through the AWS Management Console or directly using the Amazon EC2 API or CLI\.

By default, without explicit encryption parameters, a `CopyImage` action maintains the existing encryption state of an AMI's source snapshots during copy\. You can also copy an AMI and simultaneously apply a new encryption state to its associated EBS snapshots by supplying encryption parameters\. Consequently, the following behaviors are observed:

**Copy with no encryption parameters**
+ An unencrypted snapshot is copied to another unencrypted snapshot, unless encryption by default is enabled, in which case all the newly created snapshots will be encrypted\.
+ An encrypted snapshot that you own is copied to a snapshot encrypted with the same key\.
+ An encrypted snapshot that you do not own \(that is, the AMI is shared with you\) is copied to a snapshot that is encrypted by your AWS account's default CMK\.

All of these default behaviors can be overridden by supplying encryption parameters\. The available parameters are `Encrypted` and `KmsKeyId`\. Setting only the `Encrypted` parameter results in the following:

**Copy\-image behaviors with `Encrypted` set, but no `KmsKeyId` specified**
+ An unencrypted snapshot is copied to a snapshot encrypted by the AWS account's default CMK\.
+ An encrypted snapshot is copied to a snapshot encrypted by the same CMK\. \(In other words, the `Encrypted` parameter has no effect\.\)
+ An encrypted snapshot that you do not own \(i\.e\., the AMI is shared with you\) is copied to a volume that is encrypted by your AWS account's default CMK\. \(In other words, the `Encrypted` parameter has no effect\.\)

Setting both the `Encrypted` and `KmsKeyId` parameters allows you to specify a customer managed CMK for an encryption operation\. The following behaviors result:

**Copy\-image behaviors with both `Encrypted` and `KmsKeyId` set**
+ An unencrypted snapshot is copied to a snapshot encrypted by the specified CMK\.
+ An encrypted snapshot is copied to a snapshot encrypted not to the original CMK, but instead to the specified CMK\.

Submitting a `KmsKeyId` without also setting the `Encrypted` parameter results in an error\.

The following section provides an example of copying an AMI using non\-default encryption parameters, resulting in a change of encryption state\.

**Note**  
For detailed console procedures to copy an AMI, see [Copying an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/CopyingAMIs.html)\.  
For documentation of the `CopyImage` API, see [CopyImage](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CopyImage.html)\.  
For documentation of the command `copy-image` in the AWS Command Line Interface, see [copy\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/copy-image.html)\.

### Encrypt an unencrypted image during copy<a name="copy-unencrypted-to-encrypted"></a>

In this scenario, an AMI backed by an unencrypted root snapshot is copied to an AMI with an encrypted root snapshot\. The `CopyImage` action is invoked with two encryption parameters, including a CMK\. As a result, the encryption status of the root snapshot changes, so that the target AMI is backed by a root snapshot containing the same data as the source snapshot, but encrypted using the specified key\. You incur storage costs for the snapshots in both AMIs, as well as charges for any instances you launch from either AMI\.

**Note**  
Enabling [encryption by default](EBSEncryption.md#encryption-by-default) has the same effect as setting the `Encrypted` parameter to `true` for all snapshots in the AMI\.

![\[Copy AMI and encrypt snapshot on the fly\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami-to-ami-convert.png)

Setting the `Encrypted` parameter encrypts the single snapshot for this instance\. If you do not specify the `KmsKeyId` parameter, the default CMK is used to encrypt the snapshot copy\.

**Note**  
You can also copy an image with multiple snapshots and configure the encryption state of each individually\.
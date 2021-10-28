# Use encryption<a name="ebsapis-using-encryption"></a>

If Amazon EBS encryption by default is enabled on your AWS account, you cannot start a new snapshot using an un\-encrypted parent snapshot\. You must first encrypt the parent snapshot by copying it\. For more information, see [Copy an Amazon EBS snapshot](ebs-copy-snapshot.md) and [Encryption by default](EBSEncryption.md#encryption-by-default)\.

To start an encrypted snapshot, specify the Amazon Resource Name \(ARN\) of an KMS key, or specify an encrypted parent snapshot in your StartSnapshot request\. If neither are specified, and Amazon EBS encryption by default is enabled on the account, then the default KMS key for the account is used\. If no default KMS key has been specified for the account, then the AWS managed key is used\.

**Important**  
By default, all principals in the account have access to the default AWS managed key, and they can use it for EBS encryption and decryption operations\. For more information, see [Default KMS key for EBS encryption](EBSEncryption.md#EBSEncryption_key_mgmt)\.

You might need additional IAM permissions to use the EBS direct APIs with encryption\. For more information, see the [IAM permissions for EBS direct APIs](ebsapi-permissions.md) section earlier in this guide\.
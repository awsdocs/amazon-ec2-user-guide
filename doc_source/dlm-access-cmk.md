# Permissions for encryption<a name="dlm-access-cmk"></a>

Consider the following when working with Amazon Data Lifecycle Manager and encrypted resources\.
+ If the source volume is encrypted, ensure that the Amazon Data Lifecycle Manager default roles \(**AWSDataLifecycleManagerDefaultRole** and **AWSDataLifecycleManagerDefaultRoleForAMIManagement**\) have permission to use the KMS keys used to encrypt the volume\.
+ If you enable **Cross Region copy** for unencrypted snapshots or AMIs backed by unencrypted snapshots, and choose to enable encryption in the destination Region, ensure that the default roles have permission to use the KMS key needed to perform the encryption in the destination Region\.
+ If you enable **Cross Region copy** for encrypted snapshots or AMIs backed by encrypted snapshots, ensure that the default roles have permission to use both the source and destination KMS keys\. 
+ If you enable snapshot archiving for encrypted snapshots, ensure that the Amazon Data Lifecycle Manager default role \(**AWSDataLifecycleManagerDefaultRole** has permission to use the KMS key used to encrypt the snapshot\.

For more information, see [Allowing users in other accounts to use a KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html) in the *AWS Key Management Service Developer Guide*\.
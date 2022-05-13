# UEFI Secure Boot<a name="uefi-secure-boot"></a>

UEFI Secure Boot builds on the long\-standing secure boot process of Amazon EC2, and provides additional defense\-in\-depth that helps customers secure software from threats that persist across reboots\. It ensures that the instance only boots software that is signed with cryptographic keys\. The keys are stored in the key database of the [UEFI non\-volatile variable store](uefi-variables.md)\. UEFI Secure Boot prevents unauthorized modification of the instance boot flow\.

**Topics**
+ [How UEFI Secure Boot works](how-uefi-secure-boot-works.md)
+ [Launch a Linux instance with UEFI Secure Boot support](launch-instance-with-uefi-sb.md)
+ [Verify whether a Linux instance is enabled for UEFI Secure Boot](verify-uefi-secure-boot.md)
+ [Create a Linux AMI to support UEFI Secure Boot](create-ami-with-uefi-secure-boot.md)
+ [How the AWS binary blob is created](aws-binary-blob-creation.md)
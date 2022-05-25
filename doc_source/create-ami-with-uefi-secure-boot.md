# Create a Linux AMI to support UEFI Secure Boot<a name="create-ami-with-uefi-secure-boot"></a>

The following procedures describe how to create your own UEFI variable store for secure boot with custom\-made private keys\. Currently, we do not provide Linux AMIs that are preconfigured to enable UEFI Secure Boot\.

**Important**  
The following procedures for creating an AMI to support UEFI Secure Boot are intended for advanced users only\. You must have sufficient knowledge of SSL and Linux distribution boot flow to use these procedures\.

**Prerequisites**
+ The following tools will be used:
  + OpenSSL – [https://www\.openssl\.org/](https://www.openssl.org/ )
  + efivar – [https://github\.com/rhboot/efivar](https://github.com/rhboot/efivar)
  + efitools – [https://git\.kernel\.org/pub/scm/linux/kernel/git/jejb/efitools\.git/](https://git.kernel.org/pub/scm/linux/kernel/git/jejb/efitools.git/)
  + [get\-instance\-uefi\-data](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-instance-uefi-data.html) AWS CLI command
+ Your Linux instance must have been launched with a Linux AMI that supports UEFI boot mode, and have non\-volatile data present\.

Newly created instances without UEFI Secure Boot keys are created in `SetupMode`, which allows you to enroll your own keys\. Some AMIs come preconfigured with UEFI Secure Boot and you cannot change the existing keys\. If you want to change the keys, you must create a new AMI based on the original AMI\.

You have two ways to propagate the keys in the variable store, which are described in Option A and Option B that follow\. Option A describes how to do this from within the instance, mimicking the flow of real hardware\. Option B describes how to create a binary blob, which is then passed as a base64\-encoded file when you create the AMI\. For both options, you must first create the three key pairs, which are used for the chain of trust\.

**Topics**
+ [Create three key pairs](uefi-secure-boot-create-three-key-pairs.md)
+ [Option A: Add keys to the variable store from within the instance](uefi-secure-boot-optionA.md)
+ [Option B: Create a binary blob containing a pre\-filled variable store](uefi-secure-boot-optionB.md)

**Note**  
These instructions can only be used to create a Linux AMI\. If you need a Windows AMI, use one of the supported Windows AMIs\. For more information, see [Launch an instance with UEFI Secure Boot support ](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/launch-instance-with-uefi-sb) in the *Amazon EC2 User Guide for Windows Instances*\.
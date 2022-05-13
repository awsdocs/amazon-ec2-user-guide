# NitroTPM<a name="nitrotpm"></a>

Nitro Trusted Platform Module \(NitroTPM\) is a virtual device that is provided by the [AWS Nitro System](http://aws.amazon.com/ec2/nitro/) and conforms to the [TPM 2\.0 specification](https://trustedcomputinggroup.org/resource/trusted-platform-module-2-0-a-brief-introduction/)\. It securely stores artifacts \(such as passwords, certificates, or encryption keys\) that are used to authenticate the instance\. NitroTPM can generate keys and use them for cryptographic functions \(such as hashing, signing, encryption, and decryption\)\.

NitroTPM provides *measured boot*, a process where the bootloader and operating system create cryptographic hashes of every boot binary and combine them with the previous values in NitroTPM internal Platform Configuration Registers \(PCRs\)\. With measured boot, you can obtain signed PCR values from NitroTPM and use them to prove to remote entities the integrity of the instance's boot software\. This is known as remote *attestation*\.

With NitroTPM, keys and secrets can be tagged with a specific PCR value so that they can never be accessed if the value of the PCR, and thus the instance integrity, changes\. This special form of conditional access is referred to as *sealing and unsealing*\. Operating system technologies, like [BitLocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview), can use NitroTPM to seal a drive decryption key so that the drive can only be decrypted when the operating system has booted correctly and is in a known good state\.

To use NitroTPM, you must select an [Amazon Machine Image](AMIs.md) \(AMI\) that has been configured for NitroTPM support, and then use the AMI to launch a [Nitro\-based instance](instance-types.md#ec2-nitro-instances)\. You can select one of Amazon’s prebuilt AMIs or create one yourself\.

**Costs**  
There is no additional cost for using NitroTPM\. You pay only for the underlying resources that you use\.

**Topics**
+ [Considerations](nitrotpm-considerations.md)
+ [Prerequisites](enable-nitrotpm-prerequisites.md)
+ [Create an AMI for NitroTPM support](enable-nitrotpm-support-on-ami.md)
+ [Verify whether an AMI is enabled for NitroTPM](verify-nitrotpm-support-on-ami.md)
+ [Enable or stop using NitroTPM on an instance](nitrotpm-instance.md)
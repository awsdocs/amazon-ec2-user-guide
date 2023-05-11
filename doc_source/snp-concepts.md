# Concepts and terminology<a name="snp-concepts"></a>

Before you begin using AMD SEV\-SNP, ensure that you are familiar with the following concepts and terminology\.

**AMD SEV\-SNP attestation report**  
The AMD SEV\-SNP attestation report is a document that an instance can request from the CPU\. The AMD SEV\-SNP attestation report can be used to validate the state and identity of an instance, and to verify that it is running in a sanctioned AMD environment\. The report includes a launch measurement, which is a cryptographic hash of the initial boot state of an instance, including its initial instance memory contents and initial state of the vCPUs\. The AMD SEV\-SNP attestation report is signed with a VLEK signature that chains back to an AMD root of trust\.

**VLEK**  
The Versioned Loaded Endorsement Key \(VLEK\) is a versioned signing key that is certified by AMD and used by the AMD CPU to sign the AMD SEV\-SNP attestation reports\. VLEK signatures can be validated using certificates provided by AMD\.

**OVMF binary**  
The Open Virtual Machine Firmware \(OVMF\) is the early\-boot code that is used to provide a UEFI environment for the instance\. The early\-boot code is run before the code in the AMI is booted\. The OVMF also finds and runs the boot loader provided in the AMI\. For more information, see the [OVMF repository](https://github.com/tianocore/tianocore.github.io/wiki/OVMF)\.
# Prerequisites for launching Linux instances<a name="enable-nitrotpm-prerequisites"></a>

To launch a Linux instance with NitroTPM enabled, the following prerequisites must be in place\. For the prerequisites for launching a Windows instance, see [Prerequisites for launching Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/enable-nitrotpm-prerequisites.html) in the *Amazon EC2 User Guide for Windows Instances***\.

**AMI**  
Requires an AMI with NitroTPM enabled\.  
Currently, there are no NitroTPM\-enabled Amazon Linux AMIs\. To use a supported AMI, you must perform a number of configuration steps on your own Linux AMI\. For more information, see [Create a Linux AMI for NitroTPM support](enable-nitrotpm-support-on-ami.md)\.

**Operating system**  
The AMI must include an operating system with a TPM 2\.0 Command Response Buffer \(CRB\) driver\. Most current operating systems, such as Amazon Linux 2, contain a TPM 2\.0 CRB driver\.

**Instance type**  
Supported virtualized instance types: `C5`, `C5a`, `C5ad`, `C5d`, `C5n`, `C6i`, `D3`, `D3en`, `G4dn`, `G5`, `Hpc6a`, `I3en`, `I4i`, `Inf1`, `M5`, `M5a`, `M5ad`, `M5d`, `M5dn`, `M5n`, `M5zn`, `M6a`, `M6i`, `R5`, `R5a`, `R5ad`, `R5b`, `R5d`, `R5dn`, `R5n`, `R6i`, `T3`, `T3a`, `U-3tb1`, `U-6tb1`, `U-9tb1`, `U-12tb1`, `X2idn`, `X2iedn`, `X2iezn`, and `z1d`\.  
Support is coming soon on: `C6a`, `G4ad`, and `P3dn`  
Not supported: Graviton \(all generations\), Xen, Mac, and bare metal instances

**UEFI boot mode**  
NitroTPM requires that an instance runs in UEFI boot mode, which requires that the AMI must be configured for UEFI boot mode\. For more information, see [UEFI Secure Boot](uefi-secure-boot.md)\.
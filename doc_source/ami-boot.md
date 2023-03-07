# Boot modes<a name="ami-boot"></a>

When a computer boots, the first software that it runs is responsible for initializing the platform and providing an interface for the operating system to perform platform\-specific operations\.

In Amazon EC2, two variants of the boot mode software are supported: Unified Extensible Firmware Interface \(UEFI\) and Legacy BIOS\.

**Possible boot mode parameters on an AMI**  
An AMI can have one of the following boot mode parameter values: `uefi`, `legacy-bios`, or `uefi-preferred`\. The AMI boot mode parameter is optional\. For AMIs with no boot mode parameter, the instances launched from these AMIs use the default boot mode value of the instance type\.

**Purpose of the AMI boot mode parameter**  
The AMI boot mode parameter signals to Amazon EC2 which boot mode to use when launching an instance\. When the boot mode parameter is set to `uefi`, EC2 attempts to launch the instance on UEFI\. If the operating system is not configured to support UEFI, the instance launch will be unsuccessful\.

**UEFI Preferred boot mode parameter**  
You can create AMIs that support both UEFI and Legacy BIOS by using the `uefi-preferred` boot mode parameter\. When the boot mode parameter is set to `uefi-preferred`, and if the instance type supports UEFI, the instance is launched on UEFI\. If the instance type does not support UEFI, the instance is launched on Legacy BIOS\.

**Warning**  
Some features, like UEFI Secure Boot, are only available on instances that boot on UEFI\. When you use the `uefi-preferred` AMI boot mode parameter with an instance type that does not support UEFI, the instance will launch as Legacy BIOS and the UEFI\-dependent feature will be disabled\. If you rely on the availability of a UEFI\-dependent feature, set your AMI boot mode parameter to `uefi`\.

**Default boot modes for instance types**
+ Graviton instance types: UEFI
+ Intel and AMD instance types: Legacy BIOS

**Running Intel and AMD instances types on UEFI**  
[Most Intel and AMD instance types](launch-instance-boot-mode.md#UEFI-supported-types) can run on both UEFI and Legacy BIOS\. To use UEFI, you must select an AMI with the boot mode parameter set either to `uefi` or `uefi-preferred`, and the operating system contained in the AMI must be configured to support UEFI\.

**Topics**
+ [Launch an instance](launch-instance-boot-mode.md)
+ [Determine the boot mode parameter of an AMI](ami-boot-mode.md)
+ [Determine the supported boot modes of an instance type](instance-type-boot-mode.md)
+ [Determine the boot mode of an instance](instance-boot-mode.md)
+ [Determine the boot mode of the operating system](os-boot-mode.md)
+ [Set the boot mode of an AMI](set-ami-boot-mode.md)
+ [UEFI variables](uefi-variables.md)
+ [UEFI Secure Boot](uefi-secure-boot.md)
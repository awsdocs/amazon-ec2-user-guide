# Boot modes<a name="ami-boot"></a>

When a computer boots, the first software that it runs is responsible for initializing the platform and providing an interface for the operating system to perform platform\-specific operations\.

**Default boot modes**  
In EC2, two variants of the boot mode software are supported: Unified Extensible Firmware Interface \(UEFI\) and Legacy BIOS\. By default, Graviton instance types run on UEFI, and Intel and AMD instance types run on Legacy BIOS\.

**Running Intel and AMD instances types on UEFI**  
[Most Intel and AMD instance types](launch-instance-boot-mode.md#UEFI-supported-types) can run on both UEFI and Legacy BIOS\. To use UEFI, you must select an AMI with the boot mode parameter set to **uefi**, and the operating system contained in the AMI must be configured to support UEFI\.

**Purpose of the AMI boot mode parameter**  
The AMI boot mode parameter signals to EC2 which boot mode to use when launching an instance\. When the boot mode parameter is set to **uefi**, EC2 attempts to launch the instance on UEFI\. If the operating system is not configured to support UEFI, the instance launch might be unsuccessful\.

**Warning**  
Setting the boot mode parameter does not automatically configure the operating system for the specified boot mode\. The configuration is specific to the operating system\. For the configuration instructions, see the manual for your operating system\.

**Possible boot mode parameters on an AMI**  
The AMI boot mode parameter is optional\. An AMI can have one of the following boot mode parameter values: **uefi** or **legacy\-bios**\. Some AMIs do not have a boot mode parameter\. For AMIs with no boot mode parameter, the instances launched from these AMIs use the default value of the instance type—**uefi** on Graviton, and **legacy\-bios** on all Intel and AMD instance types\.

**Topics**
+ [Launch an instance](launch-instance-boot-mode.md)
+ [Determine the boot mode parameter of an AMI](ami-boot-mode.md)
+ [Determine the supported boot modes of an instance type](instance-type-boot-mode.md)
+ [Determine the boot mode of an instance](instance-boot-mode.md)
+ [Determine the boot mode of the operating system](os-boot-mode.md)
+ [Set the boot mode of an AMI](set-ami-boot-mode.md)
+ [UEFI variables](uefi-variables.md)
+ [UEFI Secure Boot](uefi-secure-boot.md)
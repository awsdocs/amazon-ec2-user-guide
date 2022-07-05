# Launch an instance<a name="launch-instance-boot-mode"></a>

You can launch an instance in UEFI or Legacy BIOS boot mode\.

**Limitations**  
UEFI boot isn't supported in Local Zones, Wavelength Zones, or with AWS Outposts\. 

## Considerations<a name="boot-considerations"></a>

Consider the following when launching an instance:
+ Default boot modes:
  + Graviton instance types: UEFI
  + Intel and AMD instance types: Legacy BIOS
+ Intel and AMD instance types that support UEFI, in addition to Legacy BIOS:
  + All instances built on the AWS Nitro System, except: bare metal instances, DL1, G4ad, P4, u\-3tb1, u\-6tb1, u\-9tb1, u\-12tb1 and VT1

   

**To see the available instance types that support UEFI in a specific Region**  
The available instance types vary by Region\. To see the available instance types that support UEFI in a Region, use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command with the `--region` parameter\. Include the `--filters` parameter to scope the results to the instance types that support UEFI and the `--query` parameter to scope the output to the value of `InstanceType`\.

  ```
  aws ec2 describe-instance-types --filters Name=supported-boot-mode,Values=uefi --query "InstanceTypes[*].[InstanceType]" --output text | sort
  ```

  Example output

  ```
  a1.2xlarge
  a1.4xlarge
  a1.large
  a1.medium
  ...
  ```

   

**To see the available instance types that support UEFI Secure Boot and persist non\-volatile variables in a specific Region**  
Currently, bare metal instances do not support UEFI Secure Boot and non\-volatile variables\. Use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command as described in the preceding example, but filter out the bare metal instances by including the `Name=hypervisor,Values=nitro` filter\. For information about UEFI Secure Boot, see [UEFI Secure Boot](uefi-secure-boot.md)\.

  ```
  aws ec2 describe-instance-types --filters Name=supported-boot-mode,Values=uefi Name=hypervisor,Values=nitro --query "InstanceTypes[*].[InstanceType]" --output text | sort
  ```

## Requirements for launching an instance with UEFI<a name="uefi-requirements"></a>

To launch an instance in UEFI mode, you must select an instance type that supports UEFI, and configure the AMI and the operating system for UEFI, as follows:

**Instance type**  
When launching an instance, you must select an instance type that supports UEFI\. For more information, see [Determine the supported boot modes of an instance type](instance-type-boot-mode.md)\.

**AMI**  
When launching an instance, you must select an AMI that is configured for UEFI\. The AMI must be configured as follows:  
+ **Operating system** – The operating system contained in the AMI must be configured to use UEFI; otherwise, the instance launch will fail\. For more information, see [Determine the boot mode of the operating system](os-boot-mode.md)\.
+ **AMI boot mode parameter** – The boot mode parameter of the AMI must be set to `uefi`\. For more information, see [Determine the boot mode parameter of an AMI](ami-boot-mode.md)\.
AWS only provides Linux AMIs configured to support UEFI for Graviton\-based instance types\. To use Linux on other UEFi instance types, you must [configure the AMI](set-ami-boot-mode.md), import the AMI through [VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/), or import the AMI through [CloudEndure](https://docs.cloudendure.com/)\.
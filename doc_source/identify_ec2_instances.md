# Identify EC2 Linux instances<a name="identify_ec2_instances"></a>

You might need to determine whether your application is running on an EC2 instance\.

For information about identifying Windows instances, see [Identify EC2 Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/identify_ec2_instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Inspect the instance identity document<a name="inspect-document"></a>

For a definitive and cryptographically verified method of identifying an EC2 instance, check the instance identity document, including its signature\. These documents are available on every EC2 instance at the local, non\-routable address `http://169.254.169.254/latest/dynamic/instance-identity/`\. For more information, see [Instance identity documents](instance-identity-documents.md)\.

## Inspect the system UUID<a name="inspect-uuid"></a>

You can get the system UUID and look for the presence of the characters "ec2" or "EC2" in the beginning octet of the UUID\. This method to determine whether a system is an EC2 instance is quick but potentially inaccurate because there is a small chance that a system that is not an EC2 instance could have a UUID that starts with these characters\. Furthermore, for EC2 instances that are not using Amazon Linux 2, the distribution's implementation of SMBIOS might represent the UUID in little\-endian format, therefore the "EC2" characters do not appear at the beginning of the UUID\.

**Example : Get the UUID from DMI \(HVM AMIs only\)**  
Use the following command to get the UUID using the Desktop Management Interface \(DMI\):  

```
[ec2-user ~]$ sudo dmidecode --string system-uuid
```
In the following example output, the UUID starts with "EC2", which indicates that the system is probably an EC2 instance\.  

```
EC2E1916-9099-7CAF-FD21-012345ABCDEF
```
In the following example output, the UUID is represented in little\-endian format\.  

```
45E12AEC-DCD1-B213-94ED-012345ABCDEF
```
Alternatively, for instances built on the Nitro system, you can use the following command:  

```
[ec2-user ~]$ cat /sys/devices/virtual/dmi/id/board_asset_tag
```
If the output is an instance ID, as the following example output, the system is an EC2 instance:  

```
i-0af01c0123456789a
```

**Example : Get the UUID from the hypervisor \(PV AMIs only\)**  
Use the following command to get the UUID from the hypervisor:  

```
[ec2-user ~]$ cat /sys/hypervisor/uuid
```
In the following example output, the UUID starts with "ec2", which indicates that the system is probably an EC2 instance\.  

```
ec2e1916-9099-7caf-fd21-012345abcdef
```

## Inspect the system virtual machine generation identifier<a name="vm-generation-id"></a>

A virtual machine generation identifier consists of a unique buffer of 128\-bit interpreted as cryptographic random integer identifier\. You can retrieve the virtual machine generation identifier to identify your Amazon Elastic Compute Cloud instance\. The generation identifier is exposed within the guest operating system of the instance through an ACPI table entry\. The value will change if your machine is cloned, copied, or imported into AWS, such as with [VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/what-is-vmimport.html)\.

**Example : Retrieve the virtual machine generation identifier from Linux**  
You can use the following commands to retrieve the virtual machine generation identifier from your instances running Linux\.  

1. Update your existing software packages, as necessary, using the following command:

   ```
   sudo yum update
   ```

1. If necessary, source the busybox package with the following command:

   ```
   sudo curl https://www.rpmfind.net/linux/epel/next/8/Everything/x86_64/Packages/b/busybox-1.35.0-2.el8.next.x86_64.rpm --output busybox.rpm
   ```

1. If necessary, install the prerequisite packages using the following command:

   ```
   sudo yum install busybox.rpm iasl -y
   ```

1. Run the following `iasl` command to produce output from the ACPI table:

   ```
   sudo iasl -p ./SSDT2 -d /sys/firmware/acpi/tables/SSDT2
   ```

1. Run the following command to review the output of the `iasl` command:

   ```
   cat SSDT2.dsl
   ```

   The output should yield the address space required to retrieve the virtual machine generation identifier:

   ```
   Intel ACPI Component Architecture
   ASL+ Optimizing Compiler/Disassembler version 20190509
   Copyright (c) 2000 - 2019 Intel Corporation
   
   File appears to be binary: found 32 non-ASCII characters, disassembling
   Binary file appears to be a valid ACPI table, disassembling
   Input file /sys/firmware/acpi/tables/SSDT2, Length 0x7B (123) bytes
   ACPI: SSDT 0x0000000000000000 00007B (v01 AMAZON AMZNSSDT 00000001 AMZN 00000001)
   Pass 1 parse of [SSDT]
   Pass 2 parse of [SSDT]
   Parsing Deferred Opcodes (Methods/Buffers/Packages/Regions)
   
   Parsing completed
   Disassembly completed
   ASL Output:    ./SSDT2.dsl - 1065 bytes
   $
   /*
    * Intel ACPI Component Architecture
    * AML/ASL+ Disassembler version 20190509 (64-bit version)
    * Copyright (c) 2000 - 2019 Intel Corporation
    *
    * Disassembling to symbolic ASL+ operators
    *
    * Disassembly of /sys/firmware/acpi/tables/SSDT2, Tue Mar 29 16:15:14 2022
    *
    * Original Table Header:
    *     Signature        "SSDT"
    *     Length           0x0000007B (123)
    *     Revision         0x01
    *     Checksum         0xB8
    *     OEM ID           "AMAZON"
    *     OEM Table ID     "AMZNSSDT"
    *     OEM Revision     0x00000001 (1)
    *     Compiler ID      "AMZN"
    *     Compiler Version 0x00000001 (1)
    */
   DefinitionBlock ("", "SSDT", 1, "AMAZON", "AMZNSSDT", 0x00000001)
   {
       Scope (\_SB)
       {
           Device (VMGN)
           {
               Name (_CID, "VM_Gen_Counter")  // _CID: Compatible ID
               Name (_DDN, "VM_Gen_Counter")  // _DDN: DOS Device Name
               Name (_HID, "AMZN0000")  // _HID: Hardware ID
               Name (ADDR, Package (0x02)
               {
                   0xFED01000,
                   Zero
               })
           }
       }
   }
   ```

1. \(Optional\) Elevate your terminal permissions for the remaining steps with the following command:

   ```
   sudo -s
   ```

1. Use the following command to store the previously gathered address space:

   ```
   VMGN_ADDR=0xFED01000
   ```

1. Use the following command to iterate through the address space and build the virtual machine generation identifier:

   ```
   for offset in 0x0 0x4 0x8 0xc; do busybox devmem $(($VMGN_ADDR + $offset)) | sed 's/0x//' | sed -z '$ s/\n$//' >> vmgenid; done
   ```

1. Retrieve the virtual machine generation identifier from the output file with the following command:

   ```
   cat vmgenid ; echo
   ```

   Your output should be similar to the following:

   ```
   EC2F335D979132C4165896753E72BD1C
   ```

1. Update your existing software packages, as necessary, using the following command:

   ```
   sudo apt update
   ```

1. If necessary, install the prerequisite packages using the following command:

   ```
   sudo apt install busybox iasl -y
   ```

1. Run the following `iasl` command to produce output from the ACPI table:

   ```
   sudo iasl -p ./SSDT2 -d /sys/firmware/acpi/tables/SSDT2
   ```

1. Run the following command to review the output of the `iasl` command:

   ```
   cat SSDT2.dsl
   ```

   The output should yield the address space required to retrieve the virtual machine generation identifier:

   ```
   Intel ACPI Component Architecture
   ASL+ Optimizing Compiler/Disassembler version 20190509
   Copyright (c) 2000 - 2019 Intel Corporation
   
   File appears to be binary: found 32 non-ASCII characters, disassembling
   Binary file appears to be a valid ACPI table, disassembling
   Input file /sys/firmware/acpi/tables/SSDT2, Length 0x7B (123) bytes
   ACPI: SSDT 0x0000000000000000 00007B (v01 AMAZON AMZNSSDT 00000001 AMZN 00000001)
   Pass 1 parse of [SSDT]
   Pass 2 parse of [SSDT]
   Parsing Deferred Opcodes (Methods/Buffers/Packages/Regions)
   
   Parsing completed
   Disassembly completed
   ASL Output:    ./SSDT2.dsl - 1065 bytes
   $
   /*
    * Intel ACPI Component Architecture
    * AML/ASL+ Disassembler version 20190509 (64-bit version)
    * Copyright (c) 2000 - 2019 Intel Corporation
    *
    * Disassembling to symbolic ASL+ operators
    *
    * Disassembly of /sys/firmware/acpi/tables/SSDT2, Tue Mar 29 16:15:14 2022
    *
    * Original Table Header:
    *     Signature        "SSDT"
    *     Length           0x0000007B (123)
    *     Revision         0x01
    *     Checksum         0xB8
    *     OEM ID           "AMAZON"
    *     OEM Table ID     "AMZNSSDT"
    *     OEM Revision     0x00000001 (1)
    *     Compiler ID      "AMZN"
    *     Compiler Version 0x00000001 (1)
    */
   DefinitionBlock ("", "SSDT", 1, "AMAZON", "AMZNSSDT", 0x00000001)
   {
       Scope (\_SB)
       {
           Device (VMGN)
           {
               Name (_CID, "VM_Gen_Counter")  // _CID: Compatible ID
               Name (_DDN, "VM_Gen_Counter")  // _DDN: DOS Device Name
               Name (_HID, "AMZN0000")  // _HID: Hardware ID
               Name (ADDR, Package (0x02)
               {
                   0xFED01000,
                   Zero
               })
           }
       }
   }
   ```

1. \(Optional\) Elevate your terminal permissions for the remaining steps with the following command:

   ```
   sudo -s
   ```

1. Use the following commands to store the previously gathered address space:

   ```
   VMGN_ADDR=0xFED01000
   ```

1. Use the following command to iterate through the address space and build the virtual machine generation identifier:

   ```
   for offset in 0x0 0x4 0x8 0xc; do busybox devmem $(($VMGN_ADDR + $offset)) | sed 's/0x//' | sed -z '$ s/\n$//' >> vmgenid; done
   ```

1. Retrieve the virtual machine generation identifier from the output file with the following command:

   ```
   cat vmgenid ; echo
   ```

   Your output should be similar to the following:

   ```
   EC2F335D979132C4165896753E72BD1C
   ```
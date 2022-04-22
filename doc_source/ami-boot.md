# Boot modes<a name="ami-boot"></a>

When a computer boots, the first software that it runs is responsible for initializing the platform and providing an interface for the operating system to perform platform\-specific operations\.

**Default boot modes**  
In EC2, two variants of the boot mode software are supported: Legacy BIOS and Unified Extensible Firmware Interface \(UEFI\)\. By default, Intel and AMD instance types run on Legacy BIOS, and Graviton instance types run on UEFI\.

**Running Intel and AMD instances types on UEFI**  
[Most Intel and AMD instance types](#UEFI-supported-types) can run on both UEFI and Legacy BIOS\. To use UEFI, you must select an AMI with the boot mode parameter set to **uefi**, and the operating system contained in the AMI must be configured to support UEFI\.

**Purpose of the AMI boot mode parameter**  
The AMI boot mode parameter signals to EC2 which boot mode to use when launching an instance\. When the boot mode parameter is set to **uefi**, EC2 attempts to launch the instance on UEFI\. If the operating system is not configured to support UEFI, the instance launch might be unsuccessful\.

**Warning**  
Setting the boot mode parameter does not automatically configure the operating system for the specified boot mode\. The configuration is specific to the operating system\. For the configuration instructions, see the manual for your operating system\.

**Possible boot mode parameter on an AMI**  
The AMI boot mode parameter is optional\. An AMI can have one of the following boot mode parameter values: **uefi** or **legacy\-bios**\. Some AMIs do not have a boot mode parameter\. For AMIs with no boot mode parameter, the instances launched from these AMIs use the default value of the instance type—**uefi** on Graviton, and **legacy\-bios** on all Intel and AMD instance types\.

**Topics**
+ [Considerations](#boot-considerations)
+ [Requirements for launching an instance with UEFI](#uefi-requirements)
+ [Determine the boot mode parameter of an AMI](#ami-boot-mode)
+ [Determine the supported boot modes of an instance type](#instance-type-boot-mode)
+ [Determine the boot mode of an instance](#instance-boot-mode)
+ [Determine the boot mode of the OS](#os-boot-mode)
+ [Set the boot mode of an AMI](#set-ami-boot-mode)
+ [UEFI variables](#uefi-variables)

## Considerations<a name="boot-considerations"></a>
+ Default boot modes:
  + Intel and AMD instance types: Legacy BIOS
  + Graviton instance types: UEFI
+ Intel and AMD instance types that support UEFI, in addition to Legacy BIOS:
  + Virtualized: C5, C5a, C5ad, C5d, C5n, D3, D3en, G4, I3en, M5, M5a, M5ad, M5d, M5dn, M5n, M5zn, M6i, R5, R5a, R5ad, R5b, R5d, R5dn, R5n, T3, T3a, and z1d
+ UEFI Secure Boot is currently not supported\.

## Requirements for launching an instance with UEFI<a name="uefi-requirements"></a>

To launch an instance in UEFI mode, you must select an instance type that supports UEFI, and configure the AMI and the OS for UEFI, as follows:
+ **Instance type** – When launching an instance, you must select an instance type that supports UEFI\. For more information, see [Determine the supported boot modes of an instance type](#instance-type-boot-mode)\.
+ **AMI** – When launching an instance, you must select an AMI that is configured for UEFI\. The AMI must be configured as follows:
  + **OS** – The operating system contained in the AMI must be configured to use UEFI; otherwise, the instance launch will fail\. For more information, see [Determine the boot mode of the OS](#os-boot-mode)\.
  + **AMI boot mode parameter** – The boot mode parameter of the AMI must be set to `uefi`\. For more information, see [Determine the boot mode parameter of an AMI](#ami-boot-mode)\.

AWS does not provide AMIs that are already configured to support UEFI\. You must [configure the AMI](#set-ami-boot-mode), import the AMI through [VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/), or import the AMI through [CloudEndure](https://docs.cloudendure.com/)\.

## Determine the boot mode parameter of an AMI<a name="ami-boot-mode"></a>

The AMI boot mode parameter is optional\. An AMI can have one of the following boot mode parameter values: **uefi** and **legacy\-bios**\.

Some AMIs do not have a boot mode parameter\. When an AMI has no boot mode parameter, the instances launched from the AMI use the default value of the instance type, which is **uefi** on Graviton, and **legacy\-bios** on Intel and AMD instance types\.

**To determine the boot mode parameter of an AMI \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, and then select the AMI\.

1. On the **Details** tab, inspect the **Boot mode** field\.

**To determine the boot mode parameter of an AMI when launching an instance \(console\)**  
When launching an instance using the launch instance wizard, at the step to select an AMI, inspect the **Boot mode** field\. For more information, see [Step 1: Choose an Amazon Machine Image \(AMI\)](launching-instance.md#step-1-AMI)\.

**To determine the boot mode parameter of an AMI \(AWS CLI version 1\.19\.34 and later and version 2\.1\.32 and later\)**  
Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command to determine the boot mode of an AMI\.

```
aws ec2 --region us-east-1 describe-images --image-id ami-0abcdef1234567890
```

Expected output

```
{
    "Images": [
        {
         ...
            ],
            "EnaSupport": true,
            "Hypervisor": "xen",
            "ImageOwnerAlias": "amazon",
            "Name": "UEFI_Boot_Mode_Enabled-Windows_Server-2016-English-Full-Base-2020.09.30",
            "RootDeviceName": "/dev/sda1",
            "RootDeviceType": "ebs",
            "SriovNetSupport": "simple",
            "VirtualizationType": "hvm",
            "BootMode": "uefi"
        }
    ]
}
```

## Determine the supported boot modes of an instance type<a name="instance-type-boot-mode"></a>

**To determine the supported boot modes of an instance type \(AWS CLI version 1\.19\.34 and later and version 2\.1\.32 and later\)**  
Use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command to determine the supported boot modes of an instance type\. By including the `--query` parameter, you can filter the output\. In this example, the output is filtered to return only the supported boot modes\.

The following example shows that `m5.2xlarge` supports both UEFI and Legacy BIOS boot modes\.

```
aws ec2 --region us-east-1 describe-instance-types --instance-types m5.2xlarge --query "InstanceTypes[*].SupportedBootModes"
```

Expected output

```
[
    [
        "legacy-bios",
        "uefi"
    ]
]
```

The following example shows that `t2.xlarge` supports only Legacy BIOS\.

```
aws ec2 --region us-east-1 describe-instance-types --instance-types t2.xlarge --query "InstanceTypes[*].SupportedBootModes"
```

Expected output

```
[
    [
        "legacy-bios"
    ]
]
```

## Determine the boot mode of an instance<a name="instance-boot-mode"></a>

When an instance is launched, the value for its boot mode parameter is determined by the value of the boot mode parameter of the AMI used to launch it, as follows:
+ An AMI with a boot mode parameter of **uefi** creates an instance with a boot mode parameter of **uefi**\.
+ An AMI with a boot mode parameter of **legacy\-bios** creates an instance with no boot mode parameter\. An instance with no boot mode parameter uses its default value, which is **legacy\-bios** in this case\.
+ An AMI with no boot mode parameter value creates an instance with no boot mode parameter value\.

The value of the instance's boot mode parameter determines the mode in which it boots\. If there is no value, the default boot mode is used, which is **uefi** on Graviton, and **legacy\-bios** on Intel and AMD instance types\.

**To determine the boot mode of an instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Details** tab, inspect the **Boot mode** field\.

**To determine the boot mode of an instance \(AWS CLI version 1\.19\.34 and later and version 2\.1\.32 and later\)**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command to determine the boot mode of an instance\.

```
aws ec2 --region us-east-1 describe-instances --instance-ids i-1234567890abcdef0
```

Expected output

```
{
    "Reservations": [
        {
            "Groups": [],
            "Instances": [
                {
                    "AmiLaunchIndex": 0,
                    "ImageId": "ami-0e2063e7f6dc3bee8",
                    "InstanceId": "i-1234567890abcdef0",
                    "InstanceType": "m5.2xlarge",
                    ... 
                    },
                    "BootMode": "uefi"
                }
            ],
            "OwnerId": "1234567890",
            "ReservationId": "r-1234567890abcdef0"
        }
    ]
}
```

## Determine the boot mode of the OS<a name="os-boot-mode"></a>

The boot mode of the OS guides EC2 on which boot mode to use to boot an instance\. To view whether the operating system of your instance is configured for UEFI, you need to connect to your instance via SSH\.

**To determine the boot mode of the instance’s OS**

1. [Connect to your Linux instance using SSH](AccessingInstancesLinux.md)\.

1. To view the boot mode of the OS, try one of the following:
   + Run the following command\.

     ```
     [ec2-user ~]$ sudo /usr/sbin/efibootmgr
     ```

     Expected output from an instance booted in UEFI boot mode

     ```
     BootCurrent: 0001
     Timeout: 0 seconds
     BootOrder: 0000,0001,0002
     Boot0000* UiApp
     Boot0001* UEFI Amazon Elastic Block Store vol-xyz
     Boot0002* EFI Internal Shell
     ```
   + Run the following command to verify the existence of the `/sys/firmware/efi` directory\. This directory exists only if the instance boots using UEFI\. If this directory doesn't exist, the command returns `Legacy BIOS Boot Detected`\.

     ```
     [ec2-user ~]$ [ -d /sys/firmware/efi ] && echo "UEFI Boot Detected" || echo "Legacy BIOS Boot Detected"
     ```

     Expected output from an instance booted in UEFI boot mode

     ```
     UEFI Boot Detected
     ```

     Expected output from an instance booted in Legacy BIOS boot mode

     ```
     Legacy BIOS Boot Detected
     ```
   + Run the following command to verify that EFI appears in the `dmesg` output\.

     ```
     [ec2-user ~]$ dmesg | grep -i "EFI"
     ```

     Expected output from an instance booted in UEFI boot mode

     ```
     [    0.000000] efi: Getting EFI parameters from FDT:
     [    0.000000] efi: EFI v2.70 by EDK II
     ```

## Set the boot mode of an AMI<a name="set-ami-boot-mode"></a>

When you create an AMI using the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command, you can set the boot mode of the AMI to either `uefi` or `legacy-bios`\.

To convert an existing Legacy BIOS\-based instance to UEFI, or an existing UEFI\-based instance to Legacy BIOS, you need to perform a number of steps: First, modify the instance's volume and OS to support the selected boot mode\. Then, create a snapshot of the volume\. Finally, use [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to create the AMI using the snapshot\.

You can't set the boot mode of an AMI using the [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) command\. With [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html), the AMI inherits the boot mode of the EC2 instance used for creating the AMI\. For example, if you create an AMI from an EC2 instance running on Legacy BIOS, the AMI boot mode will be configured as `legacy-bios`\.

**Warning**  
Before proceeding with these steps, you must first make suitable modifications to the instance's volume and OS to support booting via the selected boot mode; otherwise, the resulting AMI will not be usable\. The modifications that are required are operating system\-specific\. For more information, see the manual for your operating system\.

**To set the boot mode of an AMI \(AWS CLI version 1\.19\.34 and later and version 2\.1\.32 and later\)**

1. Make suitable modifications to the instance's volume and OS to support booting via the selected boot mode\. The modifications that are required are operating system\-specific\. For more information, see the manual for your operating system\.
**Note**  
If you don't perform this step, the AMI will not be usable\.

1. To find the volume ID of the instance, use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\. You'll create a snapshot of this volume in the next step\.

   ```
   aws ec2 describe-instances --region us-east-1 --instance-ids i-1234567890abcdef0 
   ```

   Expected output

   ```
   ...
               "BlockDeviceMappings": [
                           {
                               "DeviceName": "/dev/sda1",
                               "Ebs": {
                                   "AttachTime": "",
                                   "DeleteOnTermination": true,
                                   "Status": "attached",
                                   "VolumeId": "vol-1234567890abcdef0"
                               }
                           }
                           ...
   ```

1. To create a snapshot of the volume, use the [create\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) command\. Use the volume ID from the previous step\.

   ```
   aws ec2 create-snapshot --region us-east-1 --volume-id vol-1234567890abcdef0 
   --description "add text"
   ```

   Expected output

   ```
   {
    "Description": "add text",
    "Encrypted": false,
    "OwnerId": "123",
    "Progress": "",
    "SnapshotId": "snap-01234567890abcdef",
    "StartTime": "",
    "State": "pending",
    "VolumeId": "vol-1234567890abcdef0",
    "VolumeSize": 30,
    "Tags": []
   }
   ```

1. Note the snapshot ID in the output from the previous step\.

1. Wait until the snapshot creation is `completed` before going to the next step\. To query the state of the snapshot, use the [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html) command\.

   ```
   aws ec2 describe-snapshots --region us-east-1 --snapshot-ids snap-01234567890abcdef
   ```

   Example output

   ```
   {
       "Snapshots": [
           {
               "Description": "This is my snapshot",
               "Encrypted": false,
               "VolumeId": "vol-049df61146c4d7901",
               "State": "completed",
               "VolumeSize": 8,
               "StartTime": "2019-02-28T21:28:32.000Z",
               "Progress": "100%",
               "OwnerId": "012345678910",
               "SnapshotId": "snap-01234567890abcdef",
   ...
   ```

1. To create a new AMI, use the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command\. Use the snapshot ID that you noted in the earlier step\. To set the boot mode to UEFI, add the `--boot-mode uefi` parameter to the command\.

   ```
   aws ec2 register-image \
      --region us-east-1 \
      --description "add description" \
      --name "add name" \
      --block-device-mappings "DeviceName=/dev/sda1,Ebs={SnapshotId=snap-01234567890abcdef,DeleteOnTermination=true}" \
      --architecture x86_64 \
      --root-device-name /dev/sda1 \
      --virtualization-type hvm \
      --ena-support \
      --boot-mode uefi
   ```

   Expected output

   ```
   {
   "ImageId": "ami-new_ami_123"
   }
   ```

1. To verify that the newly\-created AMI has the boot mode that you specified in the previous step, use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

   ```
   aws ec2 describe-images --region us-east-1 --image-id ami-new_ami_123
   ```

   Expected output

   ```
   {
     "Images": [
      {
      "Architecture": "x86_64",
      "CreationDate": "2021-01-06T14:31:04.000Z",
      "ImageId": "ami-new_ami_123",
      "ImageLocation": "",
      ...
      "BootMode": "uefi"
      }
      ]
   }
   ```

1. Launch a new instance using the newly\-created AMI\. All new instances created from this AMI will inherit the same boot mode\.

1. To verify that the new instance has the expected boot mode, use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

## UEFI variables<a name="uefi-variables"></a>

When you launch an instance where the boot mode is set to UEFI, a key\-value store for variables is created\. The store can be used by UEFI and the instance operating system for storing UEFI variables\.

UEFI variables are used by the boot loader and the OS to configure early system startup\. They allow the OS to manage certain settings of the boot process, like the boot order\.

**Warning**  
Operating systems often provide read access to local processes for any UEFI variable\. You should never store sensitive data, such as passwords or personally identifiable information, in the UEFI variable store\.
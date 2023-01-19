# Option A: Add keys to the variable store from within the instance<a name="uefi-secure-boot-optionA"></a>

After you have created the [three key pairs](uefi-secure-boot-create-three-key-pairs.md), you can connect to your instance and add the keys to the variable store from within the instance by completing the following steps\.

**Topics**
+ [Step 1: Launch an instance that will support UEFI Secure Boot](#step1-launch-uefi-sb)
+ [Step 2: Configure an instance to support UEFI Secure Boot](#step2-launch-uefi-sb)
+ [Step 3: Create an AMI from the instance](#step3-launch-uefi-sb)

## Step 1: Launch an instance that will support UEFI Secure Boot<a name="step1-launch-uefi-sb"></a>

When you [launch an instance](LaunchingAndUsingInstances.md) with the following prerequisites, the instance will then be ready to be configured to support UEFI Secure Boot\. You can only enable support for UEFI Secure Boot on an instance at launch; you can't enable it later\.

**Prerequisites**
+ **AMI** \- The Linux AMI must support UEFI boot mode\. To verify that the AMI supports UEFI boot mode, the AMI boot mode parameter must be **uefi**\. For more information, see [Determine the boot mode parameter of an AMI](ami-boot-mode.md)\.

  Note that AWS only provides Linux AMIs configured to support UEFI for Graviton\-based instance types\. AWS currently does not provide x86\_64 Linux AMIs that support UEFI boot mode\. You can configure your own AMI to support UEFI boot mode for all architectures\. To configure your own AMI to support UEFI boot mode, you must perform a number of configuration steps on your own AMI\. For more information, see [Set the boot mode of an AMI](set-ami-boot-mode.md)\.
+ **Instance type** – All virtualized instance types that support UEFI also support UEFI Secure Boot\. Bare metal instance types do not support UEFI Secure Boot\. For the instance types that support UEFI Secure Boot, see [Considerations](launch-instance-boot-mode.md#boot-considerations)\.
+ Launch your instance after the release of UEFI Secure Boot\. Only instances launched after May 10, 2022 \(when UEFI Secure Boot was released\) can support UEFI Secure Boot\.

After you’ve launched your instance, you can verify that it is ready to be configured to support UEFI Secure Boot \(in other words, you can proceed to [Step 2](#step2-launch-uefi-sb)\) by checking whether UEFI data is present\. The presence of UEFI data indicates that non\-volatile data is persisted\.

**To verify whether your instance is ready for Step 2**  
Use the [get\-instance\-uefi\-data](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-instance-uefi-data.html) command and specify the instance ID\.

```
aws ec2 get-instance-uefi-data --instance-id i-0123456789example
```

The instance is ready for Step 2 if UEFI data is present in the output\. If the output is empty, the instance cannot be configured to support UEFI Secure Boot\. This can happen if your instance was launched before UEFI Secure Boot support became available\. Launch a new instance and try again\.

## Step 2: Configure an instance to support UEFI Secure Boot<a name="step2-launch-uefi-sb"></a>

### Enroll the key pairs in your UEFI variable store on the instance<a name="step2a-launch-uefi-sb"></a>

**Warning**  
You must sign your boot images *after* you enroll the keys, otherwise you won’t be able to boot your instance\.

After you create the signed UEFI signature lists \(`PK`, `KEK`, and `db`\), they must be enrolled into the UEFI firmware\.

Writing to the `PK` variable is possible only if:
+ No PK is enrolled yet, which is indicated if the `SetupMode` variable is `1`\. Check this by using the following command\. The output is either `1` or `0`\.

  ```
  efivar -d -n 8be4df61-93ca-11d2-aa0d-00e098032b8c-SetupMode 
  ```
+ The new PK is signed by the private key of the existing PK\.

**To enroll the keys in your UEFI variable store**  
The following commands must be run on the instance\.

If SetupMode is enabled \(the value is `1`\), the keys can be enrolled by running the following commands on the instance:

```
[ec2-user ~]$ efi-updatevar -f db.auth db
```

```
[ec2-user ~]$ efi-updatevar -f KEK.auth KEK
```

```
[ec2-user ~]$ efi-updatevar -f PK.auth PK
```

**To verify that UEFI Secure Boot is enabled**  
To verify that UEFI Secure Boot is enabled, follow the steps in [Verify whether a Linux instance is enabled for UEFI Secure Boot](verify-uefi-secure-boot.md)\.

You can now export your UEFI variable store with the `GetInstanceUefiData` API, or you continue to the next step and sign your boot images to reboot into a UEFI Secure Boot\-enabled instance\.

## Step 3: Create an AMI from the instance<a name="step3-launch-uefi-sb"></a>

To create an AMI from the instance, you can use the console or the `CreateImage` API, CLI, or SDKs\. For the console instructions, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\. For the API instructions, see [CreateImage](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_CreateImage.html)\.

**Note**  
The CreateImage API automatically copies the UEFI variable store of the instance to the AMI\. The console uses the CreateImage API\. After you launch instances using this AMI, the instances will have the same UEFI variable store\.
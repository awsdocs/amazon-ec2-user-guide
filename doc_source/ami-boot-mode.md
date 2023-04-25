# Determine the boot mode parameter of an AMI<a name="ami-boot-mode"></a>

The AMI boot mode parameter is optional\. An AMI can have one of the following boot mode parameter values: `uefi`, `legacy-bios`, or `uefi-preferred`\.

Some AMIs don't have a boot mode parameter\. When an AMI has no boot mode parameter, the instances launched from the AMI use the default value of the instance type, which is `uefi` on Graviton, and `legacy-bios` on Intel and AMD instance types\.

**To determine the boot mode parameter of an AMI \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, and then select the AMI\.

1. On the **Details** tab, inspect the **Boot mode** field\.

**To determine the boot mode parameter of an AMI when launching an instance \(console\)**  
When launching an instance using the launch instance wizard, at the step to select an AMI, inspect the **Boot mode** field\. For more information, see [Application and OS Images \(Amazon Machine Image\)](ec2-launch-instance-wizard.md#liw-ami)\.

**To determine the boot mode parameter of an AMI \(AWS CLI\)**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command to determine the boot mode of an AMI\.

```
aws ec2 describe-images --region us-east-1 --image-id ami-0abcdef1234567890
```

In the output, the `BootMode` field indicates the boot mode of the AMI\. A value of `uefi` indicates that the AMI supports UEFI\. A value of `uefi-preferred` indicates that the AMI supports both UEFI and Legacy BIOS\.

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
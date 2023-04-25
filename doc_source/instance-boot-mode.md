# Determine the boot mode of an instance<a name="instance-boot-mode"></a>

The boot mode of an instance is displayed in the **Boot mode** field in the Amazon EC2 console, and by the `currentInstanceBootMode` parameter in the AWS CLI\.

When an instance is launched, the value for its boot mode parameter is determined by the value of the boot mode parameter of the AMI used to launch it, as follows:
+ An AMI with a boot mode parameter of `uefi` creates an instance with a `currentInstanceBootMode` parameter of `uefi`\.
+ An AMI with a boot mode parameter of `legacy-bios` creates an instance with a `currentInstanceBootMode` parameter of` legacy-bios`\.
+ An AMI with a boot mode parameter of `uefi-preferred` creates an instance with a `currentInstanceBootMode` parameter of `uefi` if the instance type supports UEFI; otherwise, it creates an instance with a `currentInstanceBootMode` parameter of `legacy-bios`\.
+ An AMI with no boot mode parameter value creates an instance with a `currentInstanceBootMode` parameter value that is dependent on whether the AMI architecture is ARM or x86 and the supported boot mode of the instance type\. The default boot mode is `uefi` on Graviton instance types, and `legacy-bios` on Intel and AMD instance types\.

**To determine the boot mode of an instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select your instance\.

1. On the **Details** tab, inspect the **Boot mode** field\.

**To determine the boot mode of an instance \(AWS CLI\)**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command to determine the boot mode of an instance\. You can also determine the boot mode of the AMI that was used to the create the instance\. 

```
aws ec2 describe-instances --region us-east-1 --instance-ids i-1234567890abcdef0
```

In the output, the following parameters describe the boot mode:
+ `BootMode` – The boot mode of the AMI that was used to create the instance\.
+ `CurrentInstanceBootMode` – The boot mode that is used to boot the instance at launch or start\.

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
                   "BootMode": "uefi",
                   "CurrentInstanceBootMode": "uefi"
                }
            ],
            "OwnerId": "1234567890",
            "ReservationId": "r-1234567890abcdef0"
        }
    ]
}
```
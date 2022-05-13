# Determine the boot mode of an instance<a name="instance-boot-mode"></a>

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
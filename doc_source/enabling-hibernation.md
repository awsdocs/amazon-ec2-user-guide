# Enable hibernation for an instance<a name="enabling-hibernation"></a>

To hibernate an instance, you must first enable it for hibernation while launching the instance\.

**Important**  
You can't enable or disable hibernation for an instance after you launch it\.

------
#### [ New console ]

**To enable hibernation using the console**

1. Follow the procedure to [launch an instance](ec2-launch-instance-wizard.md#liw-quickly-launch-instance), but don't launch the instance until you've completed the following steps to enable hibernation\.

1. To enable hibernation, configure the following fields in the launch instance wizard:

   1. Under **Application and OS Images \(Amazon Machine Image\)**, select an AMI that supports hibernation\. For more information, see [Supported Linux AMIs](hibernating-prerequisites.md#hibernation-prereqs-supported-amis)\.

   1. Under **Instance type**, select a supported instance type\. For more information, see [Supported instance families](hibernating-prerequisites.md#hibernation-prereqs-supported-instance-families)\.

   1. Under **Configure storage**, choose **Advanced** \(at the right\), and specify the following information for the root volume:
      + For **Size \(GiB\)**, enter the EBS root volume size\. The volume must be large enough to store the RAM contents and accommodate your expected usage\.
      + For **Volume type**, select a supported EBS volume type: General Purpose SSD \(`gp2` and `gp3`\) or Provisioned IOPS SSD \(`io1` and `io2`\)\.
      + For **Encrypted**, choose **Yes**\. If you enabled encryption by default in this AWS Region, **Yes** is selected\.
      + For **KMS key**, select the encryption key for the volume\. If you enabled encryption by default in this AWS Region, the default encryption key is selected\.

      For more information about the prerequisites for the root volume, see [Hibernation prerequisites](hibernating-prerequisites.md)\.

   1. Expand **Advanced details**, and for **Stop \- Hibernate behavior**, choose **Enable**\.

1. In the **Summary** panel, review your instance configuration, and then choose **Launch instance**\. For more information, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\.

------
#### [ Old console ]

**To enable hibernation using the console**

1. Follow the [Launch an instance using the old launch instance wizard](launching-instance.md) procedure\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI that supports hibernation\. For more information about supported AMIs, see [Hibernation prerequisites](hibernating-prerequisites.md)\.

1. On the **Choose an Instance Type** page, select a supported instance type, and choose **Next: Configure Instance Details**\. For information about supported instance types, see [Hibernation prerequisites](hibernating-prerequisites.md)\.

1. On the **Configure Instance Details** page, for **Stop \- Hibernate Behavior**, select the **Enable hibernation as an additional stop behavior** check box\.

1. On the **Add Storage** page, for the root volume, specify the following information: 
   + For **Size \(GiB\)**, enter the EBS root volume size\. The volume must be large enough to store the RAM contents and accommodate your expected usage\.
   + For **Volume Type**, select a supported EBS volume type, General Purpose SSD \(`gp2` and `gp3`\) or Provisioned IOPS SSD \(`io1` and `io2`\)\.
   + For **Encryption**, select the encryption key for the volume\. If you enabled encryption by default in this AWS Region, the default encryption key is selected\.

   For more information about the prerequisites for the root volume, see [Hibernation prerequisites](hibernating-prerequisites.md)\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launch an instance using the old launch instance wizard](launching-instance.md)\.

------
#### [ AWS CLI ]

**To enable hibernation using the AWS CLI**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch an instance\. Specify the EBS root volume parameters using the `--block-device-mappings file://mapping.json` parameter, and enable hibernation using the `--hibernation-options Configured=true` parameter\.

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type m5.large \
    --block-device-mappings file://mapping.json \
    --hibernation-options Configured=true \
    --count 1 \
    --key-name MyKeyPair
```

Specify the following in `mapping.json`\.

```
[
    {
        "DeviceName": "/dev/xvda",
        "Ebs": {
            "VolumeSize": 30,
            "VolumeType": "gp2",
            "Encrypted": true
        }
    }
]
```

**Note**  
The value for `DeviceName` must match the root device name that's associated with the AMI\. To find the root device name, use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.  

```
aws ec2 describe-images --image-id ami-0abcdef1234567890
```
If you enabled encryption by default in this AWS Region, you can omit `"Encrypted": true`\.

------
#### [ PowerShell ]

**To enable hibernation using the AWS Tools for Windows PowerShell**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) command to launch an instance\. Specify the EBS root volume by first defining the block device mapping, and then adding it to the command using the `-BlockDeviceMappings` parameter\. Enable hibernation using the `-HibernationOptions_Configured $true` parameter\.

```
PS C:\> $ebs_encrypt = New-Object Amazon.EC2.Model.BlockDeviceMapping
PS C:\> $ebs_encrypt.DeviceName = "/dev/xvda"
PS C:\> $ebs_encrypt.Ebs = New-Object Amazon.EC2.Model.EbsBlockDevice
PS C:\> $ebs_encrypt.Ebs.VolumeSize = 30
PS C:\> $ebs_encrypt.Ebs.VolumeType = "gp2"
PS C:\> $ebs_encrypt.Ebs.Encrypted = $true

PS C:\> New-EC2Instance `
             -ImageId ami-0abcdef1234567890 `
             -InstanceType m5.large `
             -BlockDeviceMappings $ebs_encrypt `
             -HibernationOptions_Configured $true `
             -MinCount 1 `
             -MaxCount 1 `
             -KeyName MyKeyPair
```

**Note**  
The value for `DeviceName` must match the root device name associated with the AMI\. To find the root device name, use the [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) command\.  

```
Get-EC2Image -ImageId ami-0abcdef1234567890
```
If you enabled encryption by default in this AWS Region, you can omit `Encrypted = $true` from the block device mapping\.

------

 

------
#### [ New console ]

**To view if an instance is enabled for hibernation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, on the **Details** tab, in the **Instance details** section, inspect **Stop\-hibernate behavior**\. **Enabled** indicates that the instance is enabled for hibernation\.

------
#### [ Old console ]

**To view if an instance is enabled for hibernation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and, in the details pane, inspect **Stop \- Hibernation behavior**\. **Enabled** indicates that the instance is enabled for hibernation\.

------
#### [ AWS CLI ]

**To view if an instance is enabled for hibernation using the AWS CLI**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the `--filters "Name=hibernation-options.configured,Values=true"` parameter to filter instances that are enabled for hibernation\.

```
aws ec2 describe-instances \
    --filters "Name=hibernation-options.configured,Values=true"
```

The following field in the output indicates that the instance is enabled for hibernation\.

```
"HibernationOptions": {
    "Configured": true
}
```

------
#### [ PowerShell ]

**To view if an instance is enabled for hibernation using the AWS Tools for Windows PowerShell**  
Use the [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) command and specify the `-Filter @{ Name="hibernation-options.configured"; Value="true"}` parameter to filter instances that are enabled for hibernation\.

```
Get-EC2Instance `
    -Filter @{ Name="hibernation-options.configured"; Value="true"}
```

The output lists the EC2 instances that are enabled for hibernation\. 

------
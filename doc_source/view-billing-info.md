# Finding AMI billing and usage details<a name="view-billing-info"></a>

In the Amazon EC2 console, you can view the AMI billing information from the **AMIs** page or from the **Instances** page\. You can also find billing information using the AWS CLI\.

The following fields can help you verify AMI charges on your bill:
+ **Platform details**
+ **Usage operation**
+ **AMI ID**

## Find AMI billing information \(console\)<a name="view-ami-billing-info-console"></a>

Follow these steps to view AMI billing information in the Amazon EC2 console:

**Look up AMI billing information from the **AMIs** page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, and then select an AMI\.

1. On the **Details** tab, check the values for **Platform details** and **Usage operation**\.

**Look up AMI billing information from the **Instances** page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select an instance\.

1. On the **Details** tab \(or the **Description** tab if you are using the prior version of the console\), check the values for **Platform details** and **Usage operation**\.

To look up the `billingProducts` code for a running instance, you can use the Instance Metadata Service to retrieve the instance identity document in plaintext JSON format\. For more information, see [Instance identity documents](instance-identity-documents.md)\.

## Find AMI billing information \(AWS CLI\)<a name="view-ami-billing-info-cli"></a>

If you know the instance ID, and want to find out what the AMI ID is for that instance, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command to retrieve it\.

```
aws ec2 describe-instances --instance-ids i-123456789abcde123
..."Instances": [
{
    "AmiLaunchIndex": 0,
    "ImageId": "ami-0123456789EXAMPLE",
    "InstanceId": "i-123456789abcde123",
    ...
}]
```

If you know the AMI ID, you can use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command to show AMI platform and usage operation details:

```
$ aws ec2 describe-images --image-ids ami-0123456789EXAMPLE
```

The following example output shows the `PlatformDetails` and `UsageOperation` fields\. In this example, the ami\-0123456789EXAMPLE platform is `Red Hat Enterprise Linux` and the usage operation and billing code is `RunInstances:0010`\.

```
{
    "Images": [
        {
            "VirtualizationType": "hvm", 
            "Description": "Provided by Red Hat, Inc.",
            "Hypervisor": "xen", 
            "EnaSupport": true, 
            "SriovNetSupport": "simple", 
            "ImageId": "ami-0123456789EXAMPLE",
            "State": "available", 
            "BlockDeviceMappings": [
                {
                    "DeviceName": "/dev/sda1", 
                    "Ebs": {
                        "SnapshotId": "snap-111222333444aaabb", 
                        "DeleteOnTermination": true, 
                        "VolumeType": "gp2", 
                        "VolumeSize": 10, 
                        "Encrypted": false
                    }
                }
            ], 
            "Architecture": "x86_64", 
            "ImageLocation": "123456789012/RHEL-8.0.0_HVM-20190618-x86_64-1-Hourly2-GP2", 
            "RootDeviceType": "ebs", 
            "OwnerId": "123456789012",
            "PlatformDetails": "Red Hat Enterprise Linux",
            "UsageOperation": "RunInstances:0010", 
            "RootDeviceName": "/dev/sda1", 
            "CreationDate": "2019-05-10T13:17:12.000Z", 
            "Public": true, 
            "ImageType": "machine", 
            "Name": "RHEL-8.0.0_HVM-20190618-x86_64-1-Hourly2-GP2"
        }
    ]
}
```
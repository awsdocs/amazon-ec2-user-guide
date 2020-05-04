# Obtaining billing information<a name="ami-billing-info"></a>

You can determine the platform details and billing information associated with an Amazon Machine Image \(AMI\) before you launch an On\-Demand Instance or Spot Instance, or purchase a Reserved Instance\. For Spot Instances, you can use the platform details to confirm that the AMI is supported for Spot Instances\. When purchasing a Reserved Instance, you can make sure that, for **Platform**, you select the correct value that maps to **Platform details** on the AMI\. By knowing the billing information before launching an instance or purchasing a Reserved Instance, you reduce the chance of erroneously launching instances from incorrect AMIs and incurring unplanned costs\.

For more information about instance pricing, see [Amazon EC2 pricing](http://aws.amazon.com/ec2/pricing/)\.

**Topics**
+ [AMI billing information fields](#billing-info-fields)
+ [Platform details and usage operation values](#billing-info)
+ [Viewing platform details and usage operation values](#view-billing-info)
+ [Confirm billing information on your bill](#compare-billing-info-on-bill)

## AMI billing information fields<a name="billing-info-fields"></a>

The following fields provide billing information associated with an AMI:

Platform details  
 The platform details associated with the billing code of the AMI\. For example, `Red Hat Enterprise Linux`\.

Usage operation  
The operation of the Amazon EC2 instance and the billing code that is associated with the AMI\. For example, `RunInstances:0010`\. **Usage operation** corresponds to the [lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation) column on your AWS Cost and Usage Report \(CUR\) and in the [AWS Price List API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html)\. For the list of **Usage operation** codes, see [Platform details and usage operation values](#billing-info) in the following section\.

You can view these fields on the **Instances** or **AMIs** page in the Amazon EC2 console, or in the response that is returned by the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

## Platform details and usage operation values<a name="billing-info"></a>

The following table lists the platform details and usage operation values that can be displayed on the **Instances** or **AMIs** page in the Amazon EC2 console, or in the response that is returned by the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.


****  

|  Platform details  |  Usage operation \*\*  | 
| --- | --- | 
|  Linux/UNIX  |  RunInstances  | 
|  Red Hat BYOL Linux  |  RunInstances:00g0  | 
|  Red Hat Enterprise Linux  |  RunInstances:0010  | 
|  SQL Server Enterprise  |  RunInstances:0100  | 
|  SQL Server Standard  |  RunInstances:0004  | 
|  SQL Server Web  |  RunInstances:0200  | 
|  SUSE Linux  |  RunInstances:000g  | 
|  Windows  |  RunInstances:0002  | 
|  Windows BYOL  |  RunInstances:0800  | 
|  Windows with SQL Server Enterprise \*  |  RunInstances:0102  | 
|  Windows with SQL Server Standard \*  |  RunInstances:0006  | 
|  Windows with SQL Server Web \*  |  RunInstances:0202  | 

\* If two software licenses are associated with an AMI, the **Platform details** field shows both\.

\*\* If you are running Spot Instances, the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` on your AWS Cost and Usage Report might be different from the **Usage operation** value that is listed here\. For example, if `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` displays `RunInstances:0010:SV006`, it means that Amazon EC2 is running Red Hat Enterprise Linux Spot Instance\-hour in US East \(Virginia\) in VPC Zone \#6\.

## Viewing platform details and usage operation values<a name="view-billing-info"></a>

You can view the platform details and usage operation values associated with an AMI from the AMI or from the instance\. You can view these values in the Amazon EC2 console or by using the AWS CLI\.

### From the AMI<a name="view-billing-info-AMI"></a>

**To view the platform details and usage operation associated with an AMI \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, and then select an AMI\.

1. On the **Details** tab, check the values for **Platform details** and **Usage operation**\.

**To view the platform details and usage operation associated with an AMI \(AWS CLI\)**  
Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

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

### From the instance<a name="view-billing-info-instance"></a>

**To view the platform details and usage operation associated with an AMI \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and then select an instance\.

1. On the **Details** tab, check the values for **Platform details** and **Usage operation**\.

**To view the platform details and usage operation associated with an AMI \(console\)**

After you have launched an instance, you can find the billing information by inspecting the `billingProducts` field in the instance metadata\. For more information, see [Instance Identity Documents](instance-identity-documents.md)\. Alternatively, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command to obtain the AMI ID for the instance, and then use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command, as described in the preceding procedure, to obtain the billing information from the `PlatformDetails` and `UsageOperation` fields in the response\.

## Confirm billing information on your bill<a name="compare-billing-info-on-bill"></a>

To ensure that you're not incurring unplanned costs, you can confirm that the billing information for an instance in your AWS Cost and Usage Report \(CUR\) matches the billing information associated with the AMI that you used to launch the instance\. To confirm the billing information, find the instance ID in your CUR and check the corresponding value in the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` column\. The value should match the value for **Usage operation** associated with the AMI\.

For example, the AMI, `ami-0123456789EXAMPLE`, has the following billing information: **Platform details** = `Red Hat Enterprise Linux` and **Usage operation** = `RunInstances:0010`\. If you launched an instance using this AMI, you can find the instance ID in your CUR and check the corresponding value in the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` column\. In this example\. the value should be `RunInstances:0010`\.
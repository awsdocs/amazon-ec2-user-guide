# Obtaining Billing Information<a name="ami-billing-info"></a>

You can determine the platform details and billing information associated with an Amazon Machine Image \(AMI\) before you launch an On\-Demand Instance or Spot Instance, or purchase a Reserved Instance\. For Spot Instances, you can use the platform details to confirm that the AMI is supported for Spot Instances\. When purchasing a Reserved Instance, you can make sure that, for **Platform**, you select the correct value that maps to `PlatformDetails` on the AMI\. By knowing the billing information before launching an instance or purchasing a Reserved Instance, you reduce the chance of erroneously launching instances from incorrect AMIs and incurring unplanned costs\.

For more information about instance pricing, see [Amazon EC2 pricing](http://aws.amazon.com/ec2/pricing/)\.

**Topics**
+ [AMI Billing Information Fields](#billing-info-fields)
+ [Platform Details and Usage Operation Billing Codes](#billing-info)
+ [Viewing Platform Details and Billing Information](#view-billing-info)

## AMI Billing Information Fields<a name="billing-info-fields"></a>

The [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command returns the following information about an AMI:

PlatformDetails  
 The platform details associated with the billing code of the AMI\. For example, `Red Hat Enterprise Linux`\.

UsageOperation  
The operation of the Amazon EC2 instance and the billing code that is associated with the AMI\. `UsageOperation` corresponds to the [lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation) column on your AWS Cost and Usage Report \(CUR\) and in the [AWS Price List API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html)\. For the list of `UsageOperation` codes, see [Platform Details and Usage Operation Billing Codes](#billing-info) in the following section\.

## Platform Details and Usage Operation Billing Codes<a name="billing-info"></a>

The following table lists the `PlatformDetails` and `UsageOperation` values that can be returned when you run the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command on an AMI\.


****  

|  PlatformDetails  |  UsageOperation \*\*  | 
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

\* If two software licenses are associated with an AMI, the `PlatformDetails` field shows both\.

\*\* If you are running Spot Instances, the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` on your AWS Cost and Usage Report might be different from the `UsageOperation` value that is listed here\. For example, if `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` displays `RunInstances:0010:SV006`, it means that Amazon EC2 is running Red Hat Enterprise Linux Spot Instance\-hour in US East \(Virginia\) in VPC Zone \#6\.

## Viewing Platform Details and Billing Information<a name="view-billing-info"></a>

**To view the platform details and billing information associated with an AMI**  
Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

```
$ aws ec2 describe-images --image-ids ami-0123456789EXAMPLE
```

The following example output shows the `PlatformDetails` and `UsageOperation` fields\. In this example, the ami\-0123456789EXAMPLE platform is `Red Hat Enterprise Linux` and the usage operation is `RunInstances:0010`\.

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

To ensure that you're not incurring unplanned costs, you can confirm that the billing information in your AWS Cost and Usage Report matches the billing information that was returned from [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html)\. If you launched an instance using ami\-0123456789EXAMPLE, you can check the billing information for the instance in your AWS Cost and Usage Report\. Find the instance ID and check the corresponding value in the `[lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation)` column\. In this example\. the value should should be `RunInstances:0010`\.

**To view the platform details and billing information associated with an instance**

After you have launched an instance, you can find the billing information by inspecting the `billingProducts` field in the instance metadata\. For more information, see [Obtaining the Instance Identity Document and Signatures](instance-identity-documents.md#instance-identity-signatures)\. Alternatively, you can use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command to obtain the AMI ID for the instance, and then use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command, as described in the preceding procedure, to obtain the billing information from the `PlatformDetails` and `UsageOperation` fields in the response\.
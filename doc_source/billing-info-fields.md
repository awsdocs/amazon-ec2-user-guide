# AMI billing information fields<a name="billing-info-fields"></a>

The following fields provide billing information associated with an AMI:

Platform details  
 The platform details associated with the billing code of the AMI\. For example, `Red Hat Enterprise Linux`\.

Usage operation  
The operation of the Amazon EC2 instance and the billing code that is associated with the AMI\. For example, `RunInstances:0010`\. **Usage operation** corresponds to the [lineitem/Operation](https://docs.aws.amazon.com/cur/latest/userguide/Lineitem-columns.html#Lineitem-details-O-Operation) column on your AWS Cost and Usage Report \(CUR\) and in the [AWS Price List API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html)\.

You can view these fields on the **Instances** or **AMIs** page in the Amazon EC2 console, or in the response that is returned by the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

## Sample data: usage operation by platform<a name="billing-info"></a>

The following table lists some of the platform details and usage operation values that can be displayed on the **Instances** or **AMIs** pages in the Amazon EC2 console, or in the response that is returned by the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.


|  Platform details  |  Usage operation \*\*  | 
| --- | --- | 
|  Linux/UNIX  |  RunInstances  | 
|  Red Hat BYOL Linux  |  RunInstances:00g0  | 
|  Red Hat Enterprise Linux  |  RunInstances:0010  | 
|  Red Hat Enterprise Linux with HA  |  RunInstances:1010  | 
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
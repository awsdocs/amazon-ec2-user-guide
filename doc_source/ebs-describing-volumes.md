# Viewing Volume Information<a name="ebs-describing-volumes"></a>

You can view descriptive information for your Amazon EBS volumes in a selected region at a time in the AWS Management Console\. You can also view detailed information about a single volume, including the size, volume type, whether the volume is encrypted, which master key was used to encrypt the volume, and the specific instance to which the volume is attached\.

**View information about an EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. To view more information about a volume, select it\. In the details pane, you can inspect the information provided about the volume\.

**To view what EBS \(or other\) volumes are attached to an Amazon EC2 instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\. 

1. To view more information about an instance, select it\.

1. In the details pane, you can inspect the information provided about root and block devices\.

**To view information about an EBS volume using the command line**

You can use one of the following commands to view volume attributes\. For more information, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-volumes](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) \(AWS CLI\)
+ [Get\-EC2Volume](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Volume.html) \(AWS Tools for Windows PowerShell\)
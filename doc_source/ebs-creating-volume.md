# Creating an Amazon EBS Volume<a name="ebs-creating-volume"></a>

You can create an Amazon EBS volume that you can then attach to any EC2 instance within the same Availability Zone\. You can choose to create an encrypted EBS volume, but encrypted volumes can only be attached to selected instance types\. For more information, see [Supported Instance Types](EBSEncryption.md#EBSEncryption_supported_instances)\. You can use IAM policies to enforce encryption on new volumes\. For more information, see the example IAM policies in [Working with Volumes](ExamplePolicies_EC2.md#iam-example-manage-volumes) and [Launching Instances \(RunInstances\)](ExamplePolicies_EC2.md#iam-example-runinstances)\.

You can also create and attach EBS volumes when you launch instances by specifying a block device mapping\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) and [Block Device Mapping](block-device-mapping-concepts.md)\. You can restore volumes from previously created snapshots\. For more information, see [Restoring an Amazon EBS Volume from a Snapshot](ebs-restoring-volume.md)\.

You can apply tags to EBS volumes at the time of creation\. With tagging, you can simplify tracking of your Amazon EC2 resource inventory\. Tagging on creation can be combined with an IAM policy to enforce tagging on new volumes\. For more information, see [Tagging Your Resources](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_Tags.html)\.

If you are creating a volume for a high\-performance storage scenario, you should make sure to use a Provisioned IOPS SSD \(`io1`\) volume and attach it to an instance with enough bandwidth to support your application, such as an EBS\-optimized instance or an instance with 10\-Gigabit network connectivity\. The same advice holds for Throughput Optimized HDD \(`st1`\) and Cold HDD \(`sc1`\) volumes\. For more information, see [Amazon EC2 Instance Configuration](ebs-ec2-config.md)\.

New EBS volumes receive their maximum performance the moment that they are available and do not require initialization \(formerly known as pre\-warming\)\. However, storage blocks on volumes that were restored from snapshots must be initialized \(pulled down from Amazon S3 and written to the volume\) before you can access the block\. This preliminary action takes time and can cause a significant increase in the latency of an I/O operation the first time each block is accessed\. For most applications, amortizing this cost over the lifetime of the volume is acceptable\. Performance is restored after the data is accessed once\. For more information, see [Initializing Amazon EBS Volumes](ebs-initialize.md)\.

**To create a new \(empty\) EBS volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select the region in which you would like to create your volume\. This choice is important because some Amazon EC2 resources can be shared between regions, while others can't\. For more information, see [Resource Locations](resources.md)\.

1. In the navigation pane, choose **ELASTIC BLOCK STORE**, **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose a volume type\. For more information, see [Amazon EBS Volume Types](EBSVolumeTypes.md)\.
**Note**  
Some AWS accounts created before 2012 might have access to Availability Zones in us\-west\-1 or ap\-northeast\-1 that do not support Provisioned IOPS SSD \(`io1`\) volumes\. If you are unable to create an `io1` volume \(or launch an instance with an `io1` volume in its block device mapping\) in one of these regions, try a different Availability Zone in the region\. You can verify that an Availability Zone supports `io1` volumes by creating a 4 GiB `io1` volume in that zone\.

1. For **Size \(GiB\)**, type the size of the volume\.

1. With a Provisioned IOPS SSD volume, for **IOPS**, type the maximum number of input/output operations per second \(IOPS\) that the volume should support\.

1. For **Availability Zone**, choose the Availability Zone in which to create the volume\. EBS volumes can only be attached to EC2 instances within the same Availability Zone\.

1. \(Optional\) To create an encrypted volume, select the **Encrypted** box and choose the Customer Master Key \(CMK\) you want to use when encrypting the volume\. You can accept the default, which is the default CMK for your account, or you can choose any CMK that you have previously created using the AWS Key Management Service\. Choose an available key from the **Master Key** menu or paste the full ARN of any key that you have access to\. For more information, see the [AWS Key Management Service Developer Guide](https://docs.aws.amazon.com/kms/latest/developerguide/)\.
**Note**  
Encrypted volumes can only be attached to selected instance types\. For more information, see [Supported Instance Types](EBSEncryption.md#EBSEncryption_supported_instances)\.

1. \(Optional\) Choose **Create additional tags** to add tags to the volume\. For each tag, provide a tag key and a tag value\.

1. Choose **Create Volume**\. 

**To create a new \(empty\) EBS volume using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) \(AWS CLI\)
+ [New\-EC2Volume](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Volume.html) \(AWS Tools for Windows PowerShell\)
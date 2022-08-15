# AMI types<a name="ComponentsAMIs"></a>

You can select an AMI to use based on the following characteristics:
+ Region \(see [Regions and Zones](using-regions-availability-zones.md)\)
+ Operating system
+ Architecture \(32\-bit or 64\-bit\)
+ [Launch permissions](#launch-permissions)
+ [Storage for the root device](#storage-for-the-root-device)

## Launch permissions<a name="launch-permissions"></a>

The owner of an AMI determines its availability by specifying launch permissions\. Launch permissions fall into the following categories\.


| Launch permission | Description | 
| --- | --- | 
| public | The owner grants launch permissions to all AWS accounts\. | 
| explicit | The owner grants launch permissions to specific AWS accounts, organizations, or organizational units \(OUs\)\. | 
| implicit | The owner has implicit launch permissions for an AMI\. | 

Amazon and the Amazon EC2 community provide a large selection of public AMIs\. For more information, see [Shared AMIs](sharing-amis.md)\. Developers can charge for their AMIs\. For more information, see [Paid AMIs](paid-amis.md)\.

## Storage for the root device<a name="storage-for-the-root-device"></a>

All AMIs are categorized as either *backed by Amazon EBS* or *backed by instance store*\.
+ Amazon EBS\-backed AMI – The root device for an instance launched from the AMI is an Amazon Elastic Block Store \(Amazon EBS\) volume created from an Amazon EBS snapshot\. 
+ Amazon instance store\-backed AMI – The root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3\.

For more information, see [Amazon EC2 instance root device volume](RootDeviceStorage.md)\.

The following table summarizes the important differences when using the two types of AMIs\.


| Characteristic | Amazon EBS\-backed AMI | Amazon instance store\-backed AMI | 
| --- | --- | --- | 
|  Boot time for an instance  |  Usually less than 1 minute  |  Usually less than 5 minutes  | 
|  Size limit for a root device  |  64 TiB\*\*  |  10 GiB  | 
|  Root device volume  |  EBS volume  |  Instance store volume  | 
|  Data persistence  |  By default, the root volume is deleted when the instance terminates\.\* Data on any other EBS volumes persists after instance termination by default\.   |  Data on any instance store volumes persists only during the life of the instance\.  | 
|  Modifications  |  The instance type, kernel, RAM disk, and user data can be changed while the instance is stopped\.  |  Instance attributes are fixed for the life of an instance\.  | 
|  Charges  |  You're charged for instance usage, EBS volume usage, and storing your AMI as an EBS snapshot\.  |  You're charged for instance usage and storing your AMI in Amazon S3\.  | 
|  AMI creation/bundling  |  Uses a single command/call  |  Requires installation and use of AMI tools  | 
|  Stopped state  |  Can be in a stopped state\. Even when the instance is stopped and not running, the root volume is persisted in Amazon EBS  |  Cannot be in a stopped state; instances are running or terminated  | 

\* By default, EBS root volumes have the `DeleteOnTermination` flag set to `true`\. For information about how to change this flag so that the volume persists after termination, see [Change the root volume to persist](RootDeviceStorage.md#Using_RootDeviceStorage)\.

\*\* Supported with `io2` EBS Block Express only\. For more information, see [`io2` Block Express volumes](provisioned-iops.md#io2-block-express)\.

### Determine the root device type of your AMI<a name="display-ami-root-device-type"></a>

------
#### [ New console ]

**To determine the root device type of an AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, and select the AMI\.

1. On the **Details** tab, check the value of **Root device type** as follows:
   + `ebs` – This is an EBS\-backed AMI\.
   + `instance store` – This is an an instance store\-backed AMI\.

------
#### [ Old console ]

**To determine the root device type of an AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **AMIs**, and select the AMI\.

1. On the **Details** tab, check the value of **Root Device Type** as follows:
   + `ebs` – This is an EBS\-backed AMI\.
   + `instance store` – This is an an instance store\-backed AMI\.

------

**To determine the root device type of an AMI using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) \(AWS CLI\)
+ [Get\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Image.html) \(AWS Tools for Windows PowerShell\)

### Stopped state<a name="ec2-bootfromebs-stopped-state"></a>

You can stop an instance that has an EBS volume for its root device, but you can't stop an instance that has an instance store volume for its root device\.

Stopping causes the instance to stop running \(its status goes from `running` to `stopping` to `stopped`\)\. A stopped instance persists in Amazon EBS, which allows it to be restarted\. Stopping is different from terminating; you can't restart a terminated instance\. Because instances with an instance store volume for the root device can't be stopped, they're either running or terminated\. For more information about what happens and what you can do while an instance is stopped, see [Stop and start your instance](Stop_Start.md)\.

### Default data storage and persistence<a name="ec2-bootfromebs-default-data-storage-and-persistence"></a>

Instances that have an instance store volume for the root device automatically have instance store available \(the root volume contains the root partition and you can store additional data\)\. You can add persistent storage to your instance by attaching one or more EBS volumes\. Any data on an instance store volume is deleted when the instance fails or terminates\. For more information, see [Instance store lifetime](InstanceStorage.md#instance-store-lifetime)\.

Instances that have Amazon EBS for the root device automatically have an EBS volume attached\. The volume appears in your list of volumes like any other\. With most instance types, instances that have an EBS volume for the root device don't have instance store volumes by default\. You can add instance store volumes or additional EBS volumes using a block device mapping\. For more information, see [Block device mappings](block-device-mapping-concepts.md)\.

### Boot times<a name="ec2-bootfromebs-boot-times"></a>

Instances launched from an Amazon EBS\-backed AMI launch faster than instances launched from an instance store\-backed AMI\. When you launch an instance from an instance store\-backed AMI, all the parts have to be retrieved from Amazon S3 before the instance is available\. With an Amazon EBS\-backed AMI, only the parts required to boot the instance need to be retrieved from the snapshot before the instance is available\. However, the performance of an instance that uses an EBS volume for its root device is slower for a short time while the remaining parts are retrieved from the snapshot and loaded into the volume\. When you stop and restart the instance, it launches quickly, because the state is stored in an EBS volume\.

### AMI creation<a name="ec2-bootfromebs-ami-creation"></a>

To create Linux AMIs backed by instance store, you must create an AMI from your instance on the instance itself using the Amazon EC2 AMI tools\. 

AMI creation is much easier for AMIs backed by Amazon EBS\. The `CreateImage` API action creates your Amazon EBS\-backed AMI and registers it\. There's also a button in the AWS Management Console that lets you create an AMI from a running instance\. For more information, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.

### How you're charged<a name="ec2-bootfromebs-how-you-are-charged"></a>

With AMIs backed by instance store, you're charged for instance usage and storing your AMI in Amazon S3\. With AMIs backed by Amazon EBS, you're charged for instance usage, EBS volume storage and usage, and storing your AMI as an EBS snapshot\.

With Amazon EC2 instance store\-backed AMIs, each time you customize an AMI and create a new one, all of the parts are stored in Amazon S3 for each AMI\. So, the storage footprint for each customized AMI is the full size of the AMI\. For Amazon EBS\-backed AMIs, each time you customize an AMI and create a new one, only the changes are stored\. So, the storage footprint for subsequent AMIs that you customize after the first is much smaller, resulting in lower AMI storage charges\. 

When an instance with an EBS volume for its root device is stopped, you're not charged for instance usage; however, you're still charged for volume storage\. As soon as you start your instance, we charge a minimum of one minute for usage\. After one minute, we charge only for the seconds used\. For example, if you run an instance for 20 seconds and then stop it, we charge for a full one minute\. If you run an instance for 3 minutes and 40 seconds, we charge for exactly 3 minutes and 40 seconds of usage\. We charge you for each second, with a one\-minute minimum, that you keep the instance running, even if the instance remains idle and you don't connect to it\.
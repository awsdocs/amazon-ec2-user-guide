# Add Instance Store Volumes to Your EC2 Instance<a name="add-instance-store-volumes"></a>

You specify the EBS volumes and instance store volumes for your instance using a block device mapping\. Each entry in a block device mapping includes a device name and the volume that it maps to\. The default block device mapping is specified by the AMI you use\. Alternatively, you can specify a block device mapping for the instance when you launch it\. All the NVMe instance store volumes supported by an instance type are automatically enumerated and assigned a device name on instance launch; including them in the block device mapping for the AMI or the instance has no effect\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

A block device mapping always specifies the root volume for the instance\. The root volume is either an Amazon EBS volume or an instance store volume\. For more information, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\. The root volume is mounted automatically\. For instances with an instance store volume for the root volume, the size of this volume varies by AMI, but the maximum size is 10 GB\.

You can use a block device mapping to specify additional EBS volumes when you launch your instance, or you can attach additional EBS volumes after your instance is running\. For more information, see [Amazon EBS Volumes](EBSVolumes.md)\.

You can specify the instance store volumes for your instance only when you launch an instance\. You can't attach instance store volumes to an instance after you've launched it\.

The number and size of available instance store volumes for your instance varies by instance type\. Some instance types do not support instance store volumes\. For more information about the instance store volumes support by each instance type, see [Instance Store Volumes](InstanceStorage.md#instance-store-volumes)\. If the instance type you choose for your instance supports instance store volumes, you must add them to the block device mapping for the instance when you launch it\. After you launch the instance, you must ensure that the instance store volumes for your instance are formatted and mounted before you can use them\. The root volume of an instance store\-backed instance is mounted automatically\.

**Topics**
+ [Adding Instance Store Volumes to an AMI](#adding-instance-storage-ami)
+ [Adding Instance Store Volumes to an Instance](#adding-instance-storage-instance)
+ [Making Instance Store Volumes Available on Your Instance](#making-instance-stores-available-on-your-instances)

## Adding Instance Store Volumes to an AMI<a name="adding-instance-storage-ami"></a>

You can create an AMI with a block device mapping that includes instance store volumes\. After you add instance store volumes to an AMI, any instance that you launch from the AMI includes these instance store volumes\. When you launch an instance, you can omit volumes specified in the AMI block device mapping and add new volumes\.

**Important**  
For M3 instances, specify instance store volumes in the block device mapping of the instance, not the AMI\. Amazon EC2 might ignore instance store volumes that are specified only in the block device mapping of the AMI\.

**To add instance store volumes to an Amazon EBS\-backed AMI using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the instance\.

1. Choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** dialog box, type a meaningful name and description for your image\.

1. For each instance store volume to add, choose **Add New Volume**, from **Volume Type** select an instance store volume, and from **Device** select a device name\. \(For more information, see [Device Naming on Linux Instances](device_naming.md)\.\) The number of available instance store volumes depends on the instance type\. For instances with NVMe instance store volumes, the device mapping of these volumes depends on the order in which the operating system enumerates the volumes\.

1. Choose **Create Image**\.

**To add instance store volumes to an AMI using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) or [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)
+ [New\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html) and [Register\-EC2Image](http://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)

## Adding Instance Store Volumes to an Instance<a name="adding-instance-storage-instance"></a>

When you launch an instance, the default block device mapping is provided by the specified AMI\. If you need additional instance store volumes, you must add them to the instance as you launch it\. You can also omit devices specified in the AMI block device mapping\.

**Important**  
For M3 instances, you might receive instance store volumes even if you do not specify them in the block device mapping for the instance\.

**Important**  
For HS1 instances, no matter how many instance store volumes you specify in the block device mapping of an AMI, the block device mapping for an instance launched from the AMI automatically includes the maximum number of supported instance store volumes\. You must explicitly remove the instance store volumes that you don't want from the block device mapping for the instance before you launch it\.

**To update the block device mapping for an instance using the console**

1. Open the Amazon EC2 console\.

1. From the dashboard, choose **Launch Instance**\.

1. In **Step 1: Choose an Amazon Machine Image \(AMI\)**, select the AMI to use and choose **Select**\.

1. Follow the wizard to complete **Step 1: Choose an Amazon Machine Image \(AMI\)**, **Step 2: Choose an Instance Type**, and **Step 3: Configure Instance Details**\.

1. In **Step 4: Add Storage**, modify the existing entries as needed\. For each instance store volume to add, choose **Add New Volume**, from **Volume Type** select an instance store volume, and from **Device** select a device name\. The number of available instance store volumes depends on the instance type\.

1. Complete the wizard and launch the instance\.

**To update the block device mapping for an instance using the command line**

You can use one of the following options commands with the corresponding command\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ `--block-device-mappings` with [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
+ `-BlockDeviceMapping` with [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

## Making Instance Store Volumes Available on Your Instance<a name="making-instance-stores-available-on-your-instances"></a>

After you launch an instance, the instance store volumes are available to the instance, but you can't access them until they are mounted\. For Linux instances, the instance type determines which instance store volumes are mounted for you and which are available for you to mount yourself\. For Windows instances, the EC2Config service mounts the instance store volumes for an instance\. The block device driver for the instance assigns the actual volume name when mounting the volume, and the name assigned can be different than the name that Amazon EC2 recommends\.

Many instance store volumes are pre\-formatted with the ext3 file system\. SSD\-based instance store volumes that support TRIM instruction are not pre\-formatted with any file system\. However, you can format volumes with the file system of your choice after you launch your instance\. For more information, see [Instance Store Volume TRIM Support](ssd-instance-store.md#InstanceStoreTrimSupport)\. For Windows instances, the EC2Config service reformats the instance store volumes with the NTFS file system\.

You can confirm that the instance store devices are available from within the instance itself using instance metadata\. For more information, see [Viewing the Instance Block Device Mapping for Instance Store Volumes](block-device-mapping-concepts.md#bdm-instance-metadata)\.

For Windows instances, you can also view the instance store volumes using Windows Disk Management\. For more information, see [Listing the Disks Using Windows Disk Management](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-windows-volumes.html#windows-disks)\.

For Linux instances, you can view and mount the instance store volumes as described in the following procedure\.

**To make an instance store volume available on Linux**

1. Connect to the instance using an SSH client\.

1. Use the `df -h` command to view the volumes that are formatted and mounted\. Use the `lsblk` to view any volumes that were mapped at launch but not formatted and mounted\.

1. To format and mount an instance store volume that was mapped only, do the following:

   1. Create a file system on the device using the `mkfs` command\.

   1. Create a directory on which to mount the device using the `mkdir` command\.

   1. Mount the device on the newly created directory using the `mount` command\.
# Creating an Amazon EBS\-Backed Linux AMI<a name="creating-an-ami-ebs"></a>

To create an Amazon EBS\-backed Linux AMI, start from an instance that you've launched from an existing Amazon EBS\-backed Linux AMI\. This can be an AMI you have obtained from the AWS Marketplace, an AMI you have created using the [AWS Server Migration Service](https://aws.amazon.com/server-migration-service/) or [VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/what-is-vmimport.html), or any other AMI you can access\. After you customize the instance to suit your needs, create and register a new AMI, which you can use to launch new instances with these customizations\.

The procedures described below work for Amazon EC2 instances backed by encrypted Amazon EBS volumes \(including the root volume\) as well as for unencrypted volumes\.

The AMI creation process is different for instance store\-backed AMIs\. For more information about the differences between Amazon EBS\-backed and instance store\-backed instances, and how to determine the root device type for your instance, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\. For more information about creating an instance store\-backed Linux AMI, see [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\.

For more information about creating an Amazon EBS\-backed Windows AMI, see [Creating an Amazon EBS\-Backed Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Creating_EBSbacked_WinAMI.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Overview of Creating Amazon EBS\-Backed AMIs<a name="process-creating-an-ami-ebs"></a>

First, launch an instance from an AMI that's similar to the AMI that you'd like to create\. You can connect to your instance and customize it\. When the instance is configured correctly, ensure data integrity by stopping the instance before you create an AMI, then create the image\. When you create an Amazon EBS\-backed AMI, we automatically register it for you\.

Amazon EC2 powers down the instance before creating the AMI to ensure that everything on the instance is stopped and in a consistent state during the creation process\. If you're confident that your instance is in a consistent state appropriate for AMI creation, you can tell Amazon EC2 not to power down and reboot the instance\. Some file systems, such as XFS, can freeze and unfreeze activity, making it safe to create the image without rebooting the instance\.

During the AMI\-creation process, Amazon EC2 creates snapshots of your instance's root volume and any other EBS volumes attached to your instance\. You're charged for the snapshots until you deregister the AMI and delete the snapshots\. For more information, see [Deregistering Your Linux AMI](deregister-ami.md)\. If any volumes attached to the instance are encrypted, the new AMI only launches successfully on instances that support Amazon EBS encryption\. For more information, see [Amazon EBS Encryption](EBSEncryption.md)\.

Depending on the size of the volumes, it can take several minutes for the AMI\-creation process to complete \(sometimes up to 24 hours\)\. You may find it more efficient to create snapshots of your volumes before creating your AMI\. This way, only small, incremental snapshots need to be created when the AMI is created, and the process completes more quickly \(the total time for snapshot creation remains the same\)\. For more information, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

After the process completes, you have a new AMI and snapshot created from the root volume of the instance\. When you launch an instance using the new AMI, we create a new EBS volume for its root volume using the snapshot\.

If you add instance\-store volumes or EBS volumes to your instance in addition to the root device volume, the block device mapping for the new AMI contains information for these volumes, and the block device mappings for instances that you launch from the new AMI automatically contain information for these volumes\. The instance\-store volumes specified in the block device mapping for the new instance are new and don't contain any data from the instance store volumes of the instance you used to create the AMI\. The data on EBS volumes persists\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

**Note**  
When you create a new instance from an EBS\-backed AMI, you should initialize both its root volume and any additional EBS storage before putting it into production\. For more information, see [Initializing Amazon EBS Volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-initialize.html)\.

## Creating a Linux AMI from an Instance<a name="how-to-create-ebs-ami"></a>

You can create an AMI using the AWS Management Console or the command line\. The following diagram summarizes the process for creating an Amazon EBS\-backed AMI from a running EC2 instance\. Start with an existing AMI, launch an instance, customize it, create a new AMI from it, and finally launch an instance of your new AMI\. The steps in the following diagram match the steps in the procedure below\.

![\[Workflow for creating an AMI from an instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/running-instance.png)

**To create an AMI from an instance using the console**

1. Select an appropriate EBS\-backed AMI to serve as a starting point for your new AMI, and configure it as needed before launch\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

1. Choose **Launch** to launch an instance of the EBS\-backed AMI that you've selected\. Accept the default values as you step through the wizard\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

1. While the instance is running, connect to it\. You can perform any of the following actions on your instance to customize it for your needs:
   + Install software and applications
   + Copy data
   + Reduce start time by deleting temporary files, defragmenting your hard drive, and zeroing out free space
   + Attach additional Amazon EBS volumes

1. \(Optional\) Create snapshots of all the volumes attached to your instance\. For more information about creating snapshots, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.

1. In the navigation pane, choose **Instances**, select your instance, and then choose **Actions**, **Image**, **Create Image**\.
**Tip**  
If this option is disabled, your instance isn't an Amazon EBS\-backed instance\.

1. In the **Create Image** dialog box, specify the following information, and then choose **Create Image**\.
   + **Image name** – A unique name for the image\.
   + **Image description** – An optional description of the image, up to 255 characters\.
   + **No reboot** – This option is not selected by default\. Amazon EC2 shuts down the instance, takes snapshots of any attached volumes, creates and registers the AMI, and then reboots the instance\. Select **No reboot** to avoid having your instance shut down\.
**Warning**  
If you select **No reboot**, we can't guarantee the file system integrity of the created image\.
   + **Instance Volumes** – The fields in this section enable you to modify the root volume, and add additional Amazon EBS and instance store volumes\. For information about each field, pause on the **i** icon next to each field to display field tooltips\. Some important points are listed below\.
     + To change the size of the root volume, locate **Root** in the **Volume Type** column, and for **Size \(GiB\)**, type the required value\.
     + If you select **Delete on Termination**, when you terminate the instance created from this AMI, the EBS volume is deleted\. If you clear **Delete on Termination**, when you terminate the instance, the EBS volume is not deleted\.
**Note**  
**Delete on Termination** determines if the EBS volume is deleted or not; it does not affect the instance or the AMI\. 
     + To add an Amazon EBS volume, choose **Add New Volume** \(which adds a new row\)\. For **Volume Type**, choose **EBS**, and fill in the fields in the row\. When you launch an instance from your new AMI, additional volumes are automatically attached to the instance\. Empty volumes must be formatted and mounted\. Volumes based on a snapshot must be mounted\.
     + To add an instance store volume, see [Adding Instance Store Volumes to an AMI](add-instance-store-volumes.md#adding-instance-storage-ami)\. When you launch an instance from your new AMI, additional volumes are automatically initialized and mounted\. These volumes do not contain data from the instance store volumes of the running instance on which you based your AMI\.

1. To view the status of your AMI while it is being created, in the navigation pane, choose **AMIs**\. Initially, the status is `pending` but should change to `available` after a few minutes\.

   \(Optional\) To view the snapshot that was created for the new AMI, choose **Snapshots**\. When you launch an instance from this AMI, we use this snapshot to create its root device volume\.

1. Launch an instance from your new AMI\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

1. The new running instance contains all of the customizations that you applied in previous steps\.

### To Create an AMI from an Instance Using the Command Line<a name="create-ami-cli"></a>

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\)
+ [New\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Image.html) \(AWS Tools for Windows PowerShell\)

## Creating a Linux AMI from a Snapshot<a name="creating-launching-ami-from-snapshot"></a>

If you have a snapshot of the root device volume of an instance, you can create an AMI from this snapshot using the AWS Management Console or the command line\.

**Important**  
Some Linux distributions, such as Red Hat Enterprise Linux \(RHEL\) and SUSE Linux Enterprise Server \(SLES\), use the Amazon EC2 `billingProduct` code associated with an AMI to verify subscription status for package updates\. Creating an AMI from an EBS snapshot does not maintain this billing code, and instances launched from such an AMI are not able to connect to package update infrastructure\. If you purchase a Reserved Instance offering for one of these Linux distributions and launch instances using an AMI that does not contain the required billing code, your Reserved Instance is not applied to these instances\.  
Similarly, although you can create a Windows AMI from a snapshot, you can't successfully launch an instance from the AMI\.  
In general, AWS advises against manually creating AMIs from snapshots\.  
For more information about creating Windows AMIs or AMIs for Linux operating systems that must retain AMI billing codes to work properly, see [Creating a Linux AMI from an Instance](#how-to-create-ebs-ami)\.

**To create an AMI from a snapshot using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, under **Elastic Block Store**, choose **Snapshots**\.

1. Choose the snapshot and choose **Actions**, **Create Image**\.

1. In the **Create Image from EBS Snapshot** dialog box, complete the fields to create your AMI, then choose **Create**\. If you're re\-creating a parent instance, then choose the same options as the parent instance\.
   + **Architecture**: Choose **i386** for 32\-bit or **x86\_64** for 64\-bit\.
   + **Root device name**: Enter the appropriate name for the root volume\. For more information, see [Device Naming on Linux Instances](device_naming.md)\.
   + **Virtualization type**: Choose whether instances launched from this AMI use paravirtual \(PV\) or hardware virtual machine \(HVM\) virtualization\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.
   + \(PV virtualization type only\) **Kernel ID** and **RAM disk ID**: Choose the AKI and ARI from the lists\. If you choose the default AKI or don't choose an AKI, you must specify an AKI every time you launch an instance using this AMI\. In addition, your instance may fail the health checks if the default AKI is incompatible with the instance\.
   + \(Optional\) **Block Device Mappings**: Add volumes or expand the default size of the root volume for the AMI\. For more information about resizing the file system on your instance for a larger volume, see [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.

**To create an AMI from a snapshot using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\)
+ [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)
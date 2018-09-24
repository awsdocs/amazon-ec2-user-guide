# Launching a Linux Instance from a Backup<a name="instance-launch-snapshot"></a>

With an Amazon EBS\-backed Linux instance, you can back up the root device volume of the instance by creating a snapshot\. When you have a snapshot of the root device volume of an instance, you can terminate that instance and then later launch a new instance from the snapshot\. This can be useful if you don't have the original AMI that you launched an instance from, but you need to be able to launch an instance using the same image\.

Some Linux distributions, such as Red Hat Enterprise Linux \(RHEL\) and SUSE Linux Enterprise Server \(SLES\), use the billing product code associated with an AMI to verify subscription status for package updates\. Creating an AMI from an EBS snapshot does not maintain this billing code, and subsequent instances launched from such an AMI are not able to connect to the package update infrastructure\. To retain the billing product codes, create the AMI from the instance not from a snapshot\. For more information, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md) or [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\.

Use the following procedure to create an AMI from the root volume of your instance using the console\. If you prefer, you can use one of the following commands instead: [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) \(AWS CLI\) or [Register\-EC2Image](https://docs.aws.amazon.com/powershell/latest/reference/items/Register-EC2Image.html) \(AWS Tools for Windows PowerShell\)\. You specify the snapshot using the block device mapping\.

**To create an AMI from your root volume using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Snapshots**\.

1. Choose **Create Snapshot**\.

1. For **Volumes**, start typing the name or ID of the root volume, and then select it from the list of options\.

1. Choose the snapshot that you just created, and then choose **Actions**, **Create Image**\.

1. In the **Create Image from EBS Snapshot** dialog box, provide the following information and then choose **Create**\. If you're re\-creating a parent instance, then choose the same options as the parent instance\.
   + **Architecture**: Choose **i386** for 32\-bit or **x86\_64** for 64\-bit\.
   + **Root device name**: Enter the appropriate name for the root volume\. For more information, see [Device Naming on Linux Instances](device_naming.md)\.
   + **Virtualization type**: Choose whether instances launched from this AMI use paravirtual \(PV\) or hardware virtual machine \(HVM\) virtualization\. For more information, see [Linux AMI Virtualization Types](virtualization_types.md)\.
   + \(PV virtualization type only\) **Kernel ID** and **RAM disk ID**: Choose the AKI and ARI from the lists\. If you choose the default AKI or don't choose an AKI, you are required to specify an AKI every time you launch an instance using this AMI\. In addition, your instance may fail the health checks if the default AKI is incompatible with the instance\.
   + \(Optional\) **Block Device Mappings**: Add volumes or expand the default size of the root volume for the AMI\. For more information about resizing the file system on your instance for a larger volume, see [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)\.

1. In the navigation pane, choose **AMIs**\.

1. Choose the AMI that you just created, and then choose **Launch**\. Follow the wizard to launch your instance\. For more information about how to configure each step in the wizard, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.
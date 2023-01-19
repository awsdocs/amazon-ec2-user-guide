# Hibernation prerequisites<a name="hibernating-prerequisites"></a>

**Topics**
+ [Supported Linux AMIs](#hibernation-prereqs-supported-amis)
+ [Supported instance families](#hibernation-prereqs-supported-instance-families)
+ [Instance size](#hibernation-prereqs-instance-size)
+ [Instance RAM size](#instance-ram-size)
+ [Root volume type](#hibernation-prereqs-root-volume-type)
+ [EBS root volume size](#hibernation-prereqs-ebs-root-volume-size)
+ [Supported EBS volume types](#hibernation-prereqs-ebs-volume-types)
+ [EBS root volume encryption](#hibernation-prereqs-ebs-root-volume-encryption)
+ [Enable hibernation at launch](#hibernation-prereqs-enable-at-launch)
+ [Purchasing options](#hibernation-prereqs-purchasing-options)

## Supported Linux AMIs<a name="hibernation-prereqs-supported-amis"></a>

Must be an HVM AMI that supports hibernation:


****  

| AMI | Xen \- [supported instance families only](#hibsupported-instance-families) | Nitro \- [supported instance families only](#hibsupported-instance-families) | 
| --- | --- | --- | 
| Amazon Linux 2 AMI released 2019\.08\.29 or later | Supported | Supported | 
| Amazon Linux AMI 2018\.03 released 2018\.11\.16 or later | Supported | Supported | 
| CentOS version 8 AMI ¹ \([Additional configuration](hibernation-enabled-AMI.md#configure-centos-for-hibernation) is required\) | Not supported | Supported | 
| Fedora version 34 or later AMI ¹ \([Additional configuration](hibernation-enabled-AMI.md#configure-fedora-for-hibernation) is required\) | Not supported | Supported | 
| Red Hat Enterprise Linux \(RHEL\) 8 AMI ¹ \([Additional configuration](hibernation-enabled-AMI.md#configure-RHEL-for-hibernation) is required\) | Not supported | Supported | 
|  Ubuntu 20\.04 LTS \- Focal AMI released with serial number 20210820 or later ²  | Supported | Supported | 
| Ubuntu 18\.04 LTS \- Bionic AMI released with serial number 20190722\.1 or later ² | Supported | Supported | 
| Ubuntu 16\.04 LTS \- Xenial AMI ² ³ \([Additional configuration](hibernation-enabled-AMI.md#configure-ubuntu1604-for-hibernation) is required\) | Supported | Supported | 

¹ For CentOS, Fedora, and Red Hat Enterprise Linux, hibernation is supported on Nitro\-based instances only\.

² We recommend disabling KASLR on instances with Ubuntu 20\.04 LTS – Focal, Ubuntu 18\.04 LTS \- Bionic, and Ubuntu 16\.04 LTS \- Xenial\. For more information, see [Disable KASLR on an instance \(Ubuntu only\)](hibernation-disable-kaslr.md)\.

³ For the Ubuntu 16\.04 LTS \- Xenial AMI, hibernation is not supported on `t3.nano` instance types\. No patch will be made available because Ubuntu Xenial ended support in April 2021\. If you want to use `t3.nano` instance types, we recommend that you upgrade to the Ubuntu 20\.04 LTS \- Focal AMI or the Ubuntu 18\.04 LTS \- Bionic AMI\.

To configure your own AMI to support hibernation, see [Configure an existing AMI to support hibernation](hibernation-enabled-AMI.md)\.

Support for other versions of Ubuntu and other operating systems is coming soon\.

For information about the supported Windows AMIs, see [Supported Windows AMIs](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/hibernating-prerequisites.html#hibernation-prereqs-supported-amis) in the *Amazon EC2 User Guide for Windows Instances*\.

## Supported instance families<a name="hibernation-prereqs-supported-instance-families"></a>
+ General purpose: M3, M4, M5, M5a, M5ad, M5d, M6i, T2, T3, and T3a
+ Compute optimized: C3, C4, C5, C5d, and C6i
+ Memory optimized: R3, R4, R5, R5a, R5ad, and R5d
+ Storage optimized: I3, and I3en

**To see the available instance types that support hibernation in a specific Region**  
The available instance types vary by Region\. To see the available instance types that support hibernation in a Region, use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command with the `--region` parameter\. Include the `--filters` parameter to scope the results to the instance types that support hibernation and the `--query` parameter to scope the output to the value of `InstanceType`\.

```
aws ec2 describe-instance-types --filters Name=hibernation-supported,Values=true --query "InstanceTypes[*].[InstanceType]" --output text | sort
```

Example output

```
c3.2xlarge
c3.4xlarge
c3.8xlarge
c3.large
c3.xlarge
c4.2xlarge
c4.4xlarge
c4.8xlarge
...
```

## Instance size<a name="hibernation-prereqs-instance-size"></a>

Not supported for bare metal instances\.

## Instance RAM size<a name="instance-ram-size"></a>

Must be less than 150 GB\.

## Root volume type<a name="hibernation-prereqs-root-volume-type"></a>

Must be an EBS volume, not an instance store volume\.

## EBS root volume size<a name="hibernation-prereqs-ebs-root-volume-size"></a>

Must be large enough to store the RAM contents and accommodate your expected usage, for example, OS or applications\. If you enable hibernation, space is allocated on the root volume at launch to store the RAM\.

## Supported EBS volume types<a name="hibernation-prereqs-ebs-volume-types"></a>
+ General Purpose SSD \(`gp2` and `gp3`\)
+ Provisioned IOPS SSD \(`io1` and `io2`\)

If you choose a Provisioned IOPS SSD volume type, you must provision the EBS volume with the appropriate IOPS to achieve optimum performance for hibernation\. For more information, see [Amazon EBS volume types](ebs-volume-types.md)\.

## EBS root volume encryption<a name="hibernation-prereqs-ebs-root-volume-encryption"></a>

To use hibernation, the root volume must be encrypted to ensure the protection of sensitive content that is in memory at the time of hibernation\. When RAM data is moved to the EBS root volume, it is always encrypted\. Encryption of the root volume is enforced at instance launch\.

Use one of the following three options to ensure that the root volume is an encrypted EBS volume:
+ **EBS encryption by default** – You can enable EBS encryption by default to ensure that all new EBS volumes created in your AWS account are encrypted\. This way, you can enable hibernation for your instances without specifying encryption intent at instance launch\. For more information, see [Encryption by default](EBSEncryption.md#encryption-by-default)\.
+ **EBS "single\-step" encryption** – You can launch encrypted EBS\-backed EC2 instances from an unencrypted AMI and also enable hibernation at the same time\. For more information, see [Use encryption with EBS\-backed AMIs](AMIEncryption.md)\.
+ **Encrypted AMI** – You can enable EBS encryption by using an encrypted AMI to launch your instance\. If your AMI does not have an encrypted root snapshot, you can copy it to a new AMI and request encryption\. For more information, see [Encrypt an unencrypted image during copy](AMIEncryption.md#copy-unencrypted-to-encrypted) and [Copy an AMI](CopyingAMIs.md#ami-copy-steps)\.

## Enable hibernation at launch<a name="hibernation-prereqs-enable-at-launch"></a>

You cannot enable hibernation on an existing instance \(running or stopped\)\. For more information, see [Enable hibernation for an instance](enabling-hibernation.md)\.

## Purchasing options<a name="hibernation-prereqs-purchasing-options"></a>

This feature is available for On\-Demand Instances, including those that have a Reserved Instance billing discount applied to them\. It is not available for Spot Instances\. For information about hibernating a Spot Instance, see [Hibernate interrupted Spot Instances](hibernate-spot-instances.md)\.
# Hibernate interrupted Spot Instances<a name="hibernate-spot-instances"></a>

You can specify that Amazon EC2 hibernates your Spot Instances when they are interrupted\. For more information, see [Specify the interruption behavior](interruption-behavior.md#specifying-spot-interruption-behavior)\.

When Amazon EC2 hibernates a Spot Instance, the following occurs:
+ When the instance receives a signal from Amazon EC2, the agent prompts the operating system to hibernate\. If the agent is not installed, or the underlying operating system doesn't support hibernation, or there isn't enough volume space to save the instance memory, hibernation fails and Amazon EC2 stops the instance instead\.
+ The instance memory \(RAM\) is preserved on the root volume\.
+ The EBS volumes and private IP addresses of the instance are preserved\.
+ Instance store volumes and public IP addresses, other than Elastic IP addresses, are not preserved\. 

For information about hibernating On\-Demand Instances, see [Hibernate your On\-Demand Linux instance](Hibernate.md)\.

## Considerations<a name="hibernate-interrupted-spot-instances-considerations"></a>
+ Only Amazon EC2 can hibernate a Spot Instance\. You can't manually hibernate a Spot Instance\.
+ Only Amazon EC2 can resume a hibernated Spot Instance\. You can't manually resume a hibernated Spot Instance\.
+ Amazon EC2 resumes the instance when capacity becomes available\.
+ When Amazon EC2 hibernates a Spot Instance, hibernation begins immediately\. You receive an interruption notice, but you do not have two minutes before the Spot Instance is interrupted\.
+ While the instance is in the process of hibernating, instance health checks might fail\.
+ When the hibernation process completes, the state of the instance is `stopped`\.
+ While the instance is hibernated, you are charged only for the EBS volumes\. With EC2 Fleet and Spot Fleet, if you have many hibernated instances, you can exceed the limit on the number of EBS volumes for your account\.
+ Make sure that you are familiar with the implications of hibernating an instance\. For information about what happens when an instance is hibernated, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

## Prerequisites<a name="spot-instance-hibernation-prerequisites"></a>

To hibernate a Spot Instance, the following prerequisites must be in place:

**Spot request type**  
Spot Instance request type – Must be `persistent`\. You can't specify a launch group in the Spot Instance request\.  
EC2 Fleet or Spot Fleet request type – Must be `maintain`\.

**Supported Linux AMIs**  
The following supported AMIs include the hibernation agent\. To use an earlier version of the following AMIs, you must [install the hibernation agent](#install-spot-instance-hibernation-agent)\.  
+ Amazon Linux 2 2019\.08\.29 or later
+ Amazon Linux AMI 2017\.09\.1 or later
+ Ubuntu Xenial 16\.04 20171121 or later ¹
¹ To use an earlier version of the Ubuntu Xenial AMI, it must have a Ubuntu kernel tuned by AWS\(`linux-aws`\) greater than 4\.4\.0\-1041\.  
For information about the supported Windows AMIs, see the [prerequisites](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/hibernate-spot-instances.html#spot-instance-hibernation-prerequisites) in the *Amazon EC2 User Guide for Windows Instances*\.

**Start the hibernation agent**  
We recommend that you use user data to start the hibernation agent at instance launch\. Alternatively, you could start the agent manually\. For more information, see [Start the hibernation agent at launch](#start-spot-instance-hibernation-agent)\.

**Supported instance families**  
C3, C4, C5, M4, M5, R3, R4

**Instance RAM size**  
Must be less than 100 GB\.

**Root volume type**  
Must be an EBS volume, not an instance store volume\.

**EBS root volume size**  
Must be large enough to store the instance memory \(RAM\) during hibernation\.

**EBS root volume encryption** – recommended, but not a prerequisite for Spot Instance hibernation  
We strongly recommend that you use an encrypted EBS volume as the root volume, because instance memory is stored on the root volume during hibernation\. This ensures that the contents of memory \(RAM\) are encrypted when the data is at rest on the volume and when data is moving between the instance and volume\.  
Use one of the following three options to ensure that the root volume is an encrypted EBS volume:  
+ **EBS encryption by default** – You can enable EBS encryption by default to ensure that all new EBS volumes created in your AWS account are encrypted\. This way, you can enable hibernation for your instances without specifying encryption intent at instance launch\. For more information, see [Encryption by default](EBSEncryption.md#encryption-by-default)\.
+ **EBS "single\-step" encryption** – You can launch encrypted EBS\-backed EC2 instances from an unencrypted AMI and also enable hibernation at the same time\. For more information, see [Use encryption with EBS\-backed AMIs](AMIEncryption.md)\.
+ **Encrypted AMI** – You can enable EBS encryption by using an encrypted AMI to launch your instance\. If your AMI does not have an encrypted root snapshot, you can copy it to a new AMI and request encryption\. For more information, see [Encrypt an unencrypted image during copy](AMIEncryption.md#copy-unencrypted-to-encrypted) and [Copy an AMI](CopyingAMIs.md#ami-copy-steps)\. 

## Install the hibernation agent on your Linux AMI<a name="install-spot-instance-hibernation-agent"></a>

You must install the hibernation agent on your AMI, unless you plan to use an AMI that already includes the agent\. 

The following instructions describe how to install the hibernation agent on a Linux AMI\. For the instructions to install the hibernation agent on a Windows AMI, see [Install the hibernation agent on your on Windows AMI](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/hibernate-spot-instances.html#install-spot-instance-hibernation-agent) in the *Amazon EC2 User Guide for Windows Instances*\.

**To install the hibernation agent on an Amazon Linux AMI**

1. Verify that your kernel supports hibernation and update the kernel if necessary\.

   ```
   sudo yum update kernel
   ```

1. If your AMI doesn't include the agent, install the agent\.

   ```
   sudo yum update; sudo yum install hibagent
   ```

**To install the hibernation agent on an Ubuntu AMI**  
If your AMI doesn't include the agent, install the agent\. The hibernation agent is only available on Ubuntu 16\.04 or later\.

```
sudo apt-get install hibagent
```

## Start the hibernation agent at launch<a name="start-spot-instance-hibernation-agent"></a>

The hibernation agent must run at instance startup, whether the agent was included in your AMI or you installed it yourself\.

The following instructions describe how to start the hibernation agent on a Linux instance\. For the instructions to start the hibernation agent on a Windows instance, see [Start the hibernation agent at launch](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/hibernate-spot-instances.html#start-spot-instance-hibernation-agent) in the *Amazon EC2 User Guide for Windows Instances*\.

**To start the hibernation agent on a Spot Instance**  
Follow the steps to request a Spot Instance using your preferred [launch method](LaunchingAndUsingInstances.md), and add the following to the user data\.

```
#!/bin/bash
/usr/bin/enable-ec2-spot-hibernation
```
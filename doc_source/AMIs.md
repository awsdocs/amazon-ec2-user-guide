# Amazon Machine Images \(AMI\)<a name="AMIs"></a>

An Amazon Machine Image \(AMI\) provides the information required to launch an instance\. You must specify an AMI when you launch an instance\. You can launch multiple instances from a single AMI when you need multiple instances with the same configuration\. You can use different AMIs to launch instances when you need instances with different configurations\.

An AMI includes the following:
+ One or more EBS snapshots, or, for instance\-store\-backed AMIs, a template for the root volume of the instance \(for example, an operating system, an application server, and applications\)\.
+ Launch permissions that control which AWS accounts can use the AMI to launch instances\.
+ A block device mapping that specifies the volumes to attach to the instance when it's launched\.

**Topics**
+ [Using an AMI](#ami-using)
+ [Creating your own AMI](#creating-an-ami)
+ [Buying, sharing, and selling AMIs](#buy-share-sell)
+ [Deregistering your AMI](#deregistering)
+ [Amazon Linux 2 and Amazon Linux AMI](#amazon-linux)
+ [AMI types](ComponentsAMIs.md)
+ [Linux AMI virtualization types](virtualization_types.md)
+ [Finding a Linux AMI](finding-an-ami.md)
+ [Shared AMIs](sharing-amis.md)
+ [Paid AMIs](paid-amis.md)
+ [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)
+ [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)
+ [Using encryption with EBS\-backed AMIs](AMIEncryption.md)
+ [Copying an AMI](CopyingAMIs.md)
+ [Obtaining billing information](ami-billing-info.md)
+ [Deregistering your Linux AMI](deregister-ami.md)
+ [Amazon Linux](amazon-linux-ami-basics.md)
+ [Enabling Your Own Linux Kernels](UserProvidedKernels.md)
+ [Using the MATE desktop environment provided with Amazon Linux 2](amazon-linux-ami-mate.md)

## Using an AMI<a name="ami-using"></a>

The following diagram summarizes the AMI lifecycle\. After you create and register an AMI, you can use it to launch new instances\. \(You can also launch instances from an AMI if the AMI owner grants you launch permissions\.\) You can copy an AMI within the same Region or to different Regions\. When you no longer require an AMI, you can deregister it\.

![\[The AMI lifecycle (create, register, launch, copy, deregister).\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_lifecycle.png)

You can search for an AMI that meets the criteria for your instance\. You can search for AMIs provided by AWS or AMIs provided by the community\. For more information, see [AMI types](ComponentsAMIs.md) and [Finding an AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html)\.

After you launch an instance from an AMI, you can connect to it\. When you are connected to an instance, you can use it just like you use any other server\. For information about launching, connecting, and using your instance, see [Tutorial: Getting started with Amazon EC2 Linux instances](EC2_GetStarted.md)\.

## Creating your own AMI<a name="creating-an-ami"></a>

You can launch an instance from an existing AMI, customize the instance \(for example, [install software](install-software.md) on the instance\), and then save this updated configuration as a custom AMI\. Instances launched from this new custom AMI include the customizations that you made when you created the AMI\.

The root storage device of the instance determines the process you follow to create an AMI\. The root volume of an instance is either an Amazon EBS volume or an instance store volume\. For more information about the root device volume, see [Amazon EC2 root device volume](RootDeviceStorage.md)\.
+ To create an Amazon EBS\-backed AMI, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.
+ To create an instance store\-backed AMI, see [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\.

To help categorize and manage your AMIs, you can assign custom *tags* to them\. For more information, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

## Buying, sharing, and selling AMIs<a name="buy-share-sell"></a>

After you create an AMI, you can keep it private so that only you can use it, or you can share it with a specified list of AWS accounts\. You can also make your custom AMI public so that the community can use it\. Building a safe, secure, usable AMI for public consumption is a fairly straightforward process, if you follow a few simple guidelines\. For information about how to create and use shared AMIs, see [Shared AMIs](sharing-amis.md)\.

You can purchase AMIs from a third party, including AMIs that come with service contracts from organizations such as Red Hat\. You can also create an AMI and sell it to other Amazon EC2 users\. For more information about buying or selling AMIs, see [Paid AMIs](paid-amis.md)\.

## Deregistering your AMI<a name="deregistering"></a>

You can deregister an AMI when you have finished with it\. After you deregister an AMI, it can't be used to launch new instances\. Existing instances launched from the AMI are not affected\. For more information, see [Deregistering your Linux AMI](deregister-ami.md)\.

## Amazon Linux 2 and Amazon Linux AMI<a name="amazon-linux"></a>

Amazon Linux 2 and the Amazon Linux AMI are supported and maintained Linux images provided by AWS\. The following are some of the features of Amazon Linux 2 and Amazon Linux AMI:
+ A stable, secure, and high\-performance execution environment for applications running on Amazon EC2\.
+ Provided at no additional charge to Amazon EC2 users\.
+ Repository access to multiple versions of MySQL, PostgreSQL, Python, Ruby, Tomcat, and many more common packages\.
+ Updated on a regular basis to include the latest components, and these updates are also made available in the yum repositories for installation on running instances\.
+ Includes packages that enable easy integration with AWS services, such as the AWS CLI, Amazon EC2 API and AMI tools, the Boto library for Python, and the Elastic Load Balancing tools\.

For more information, see [Amazon Linux](amazon-linux-ami-basics.md)\.
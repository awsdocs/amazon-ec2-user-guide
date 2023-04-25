# Create an instance store\-backed Linux AMI<a name="creating-an-ami-instance-store"></a>

The AMI that you specify when you launch your instance determines the type of root device volume\.

To create an instance store\-backed Linux AMI, start from an instance that you've launched from an existing instance store\-backed Linux AMI\. After you've customized the instance to suit your needs, bundle the volume and register a new AMI, which you can use to launch new instances with these customizations\.

**Important**  
Only the following instance types support an instance store volume as the root device: C3, D2, G2, I2, M3, and R3\.

The AMI creation process is different for Amazon EBS\-backed AMIs\. For more information about the differences between Amazon EBS\-backed and instance store\-backed instances, and how to determine the root device type for your instance, see [Storage for the root device](ComponentsAMIs.md#storage-for-the-root-device)\. If you need to create an Amazon EBS\-backed Linux AMI, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.

## Overview of the creation process for instance store\-backed AMIs<a name="process-creating-an-ami-instance-store"></a>

The following diagram summarizes the process of creating an AMI from an instance store\-backed instance\.

![\[Creating an instance store-backed AMI.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_create_instance_store.png)

First, launch an instance from an AMI that's similar to the AMI that you'd like to create\. You can connect to your instance and customize it\. When the instance is set up the way you want it, you can bundle it\. It takes several minutes for the bundling process to complete\. After the process completes, you have a bundle, which consists of an image manifest \(`image.manifest.xml`\) and files \(`image.part.`*xx*\) that contain a template for the root volume\. Next you upload the bundle to your Amazon S3 bucket and then register your AMI\.

**Note**  
To upload objects to an S3 bucket for your instance store\-backed Linux AMI, ACLs must be enabled for the bucket\. Otherwise, Amazon EC2 will not be able to set ACLs on the objects to upload\. If your destination bucket uses the bucket owner enforced setting for S3 Object Ownership, this won’t work because ACLs are disabled\. For more information, see [Controlling ownership of uploaded objects using S3 Object Ownership](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html)\.

When you launch an instance using the new AMI, we create the root volume for the instance using the bundle that you uploaded to Amazon S3\. The storage space used by the bundle in Amazon S3 incurs charges to your account until you delete it\. For more information, see [Deregister your AMI](deregister-ami.md)\.

If you add instance store volumes to your instance in addition to the root device volume, the block device mapping for the new AMI contains information for these volumes, and the block device mappings for instances that you launch from the new AMI automatically contain information for these volumes\. For more information, see [Block device mappings](block-device-mapping-concepts.md)\.

## Prerequisites<a name="bundle-ami-prerequisites"></a>

Before you can create an AMI, you must complete the following tasks:
+ Install the AMI tools\. For more information, see [Set up the AMI tools](set-up-ami-tools.md)\.
+ Install the AWS CLI\. For more information, see [Getting Set Up with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)\.
+ Ensure that you have an S3 bucket for the bundle, and that your bucket has ACLs enabled\. 

  To create an S3 bucket, open the Amazon S3 console and click **Create Bucket**\. Alternatively, you can use the AWS CLI [mb](https://docs.aws.amazon.com/cli/latest/reference/s3/mb.html) command\.
+ Ensure that you have your AWS account ID\. For more information, see [AWS Account Identifiers](https://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html) in the *AWS General Reference*\.
+ Ensure that you have credentials to use the AWS CLI\. For more information, see [Best Practices for AWS accounts](https://docs.aws.amazon.com/accounts/latest/reference/best-practices.html) in the *AWS Account Management Reference Guide*\.
+ Ensure that you have an X\.509 certificate and corresponding private key\.
  + If you need to create an X\.509 certificate, see [Manage signing certificates](set-up-ami-tools.md#ami-tools-managing-certs)\. The X\.509 certificate and private key are used to encrypt and decrypt your AMI\.
  + \[China \(Beijing\)\] Use the `$EC2_AMITOOL_HOME/etc/ec2/amitools/cert-ec2-cn-north-1.pem` certificate\.
  + \[AWS GovCloud \(US\-West\)\] Use the `$EC2_AMITOOL_HOME/etc/ec2/amitools/cert-ec2-gov.pem` certificate\.
+ Connect to your instance and customize it\. For example, you can install software and applications, copy data, delete temporary files, and modify the Linux configuration\.

**Tasks**
+ [Set up the AMI tools](set-up-ami-tools.md)
+ [Create an AMI from an instance store\-backed Amazon Linux instance](create-instance-store-ami.md#amazon_linux_instructions)
+ [Create an AMI from an instance store\-backed Ubuntu instance](create-instance-store-ami.md#ubuntu_instructions)
+ [Convert your instance store\-backed AMI to an Amazon EBS\-backed AMI](Using_ConvertingS3toEBS.md)
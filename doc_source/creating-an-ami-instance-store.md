# Creating an Instance Store\-Backed Linux AMI<a name="creating-an-ami-instance-store"></a>

To create an instance store\-backed Linux AMI, start from an instance that you've launched from an existing instance store\-backed Linux AMI\. After you've customized the instance to suit your needs, bundle the volume and register a new AMI, which you can use to launch new instances with these customizations\.

The AMI creation process is different for instance store\-backed AMIs\. For more information about the differences between Amazon EBS\-backed and instance store\-backed instances, and how to determine the root device type for your instance, see [Storage for the Root Device](ComponentsAMIs.md#storage-for-the-root-device)\. If you need to create an Amazon EBS\-backed Linux AMI, see [Creating an Amazon EBS\-Backed Linux AMI](creating-an-ami-ebs.md)\.

## Overview of the Creation Process for Instance Store\-Backed AMIs<a name="process-creating-an-ami-instance-store"></a>

The following diagram summarizes the process of creating an AMI from an instance store\-backed instance\.

![\[Creating an instance store-backed AMI\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ami_create_instance_store.png)

First, launch an instance from an AMI that's similar to the AMI that you'd like to create\. You can connect to your instance and customize it\. When the instance is set up the way you want it, you can bundle it\. It takes several minutes for the bundling process to complete\. After the process completes, you have a bundle, which consists of an image manifest \(`image.manifest.xml`\) and files \(`image.part.`*xx*\) that contain a template for the root volume\. Next you upload the bundle to your Amazon S3 bucket and then register your AMI\.

When you launch an instance using the new AMI, we create the root volume for the instance using the bundle that you uploaded to Amazon S3\. The storage space used by the bundle in Amazon S3 incurs charges to your account until you delete it\. For more information, see [Deregistering Your Linux AMI](deregister-ami.md)\.

If you add instance store volumes to your instance in addition to the root device volume, the block device mapping for the new AMI contains information for these volumes, and the block device mappings for instances that you launch from the new AMI automatically contain information for these volumes\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

## Prerequisites<a name="bundle-ami-prerequisites"></a>

Before you can create an AMI, you must complete the following tasks:

+ Install the AMI tools\. For more information, see [Setting Up the AMI Tools](set-up-ami-tools.md)\.

+ Install the AWS CLI\. For more information, see [Getting Set Up with the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html)\.

+ Ensure that you have an Amazon S3 bucket for the bundle\. To create an Amazon S3 bucket, open the Amazon S3 console and click **Create Bucket**\. Alternatively, you can use the AWS CLI [mb](http://docs.aws.amazon.com/cli/latest/reference/s3/mb.html) command\.

+ Ensure that you have your AWS account ID\. For more information, see [AWS Account Identifiers](http://docs.aws.amazon.com/general/latest/gr/acct-identifiers.html) in the *AWS General Reference*\.

+ Ensure that you have your access key ID and secret access key\. For more information, see [Access Keys](http://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys) in the *AWS General Reference*\.

+ Ensure that you have an X\.509 certificate and corresponding private key\.

  + If you need to create an X\.509 certificate, see [Managing Signing Certificates](set-up-ami-tools.md#ami-tools-managing-certs)\. The X\.509 certificate and private key are used to encrypt and decrypt your AMI\.

  + \[China \(Beijing\)\] Use the `$EC2_AMITOOL_HOME/etc/ec2/amitools/cert-ec2-cn-north-1.pem` certificate\.

  + \[AWS GovCloud \(US\)\] Use the `$EC2_AMITOOL_HOME/etc/ec2/amitools/cert-ec2-gov.pem` certificate\.

+ Connect to your instance and customize it\. For example, you can install software and applications, copy data, delete temporary files, and modify the Linux configuration\.

**Tasks**

+ [Setting Up the AMI Tools](set-up-ami-tools.md)

+ [Creating an AMI from an Instance Store\-Backed Amazon Linux Instance](create-instance-store-ami.md#amazon_linux_instructions)

+ [Creating an AMI from an Instance Store\-Backed Ubuntu Instance](create-instance-store-ami.md#ubuntu_instructions)

+ [Converting your Instance Store\-Backed AMI to an Amazon EBS\-Backed AMI](Using_ConvertingS3toEBS.md)
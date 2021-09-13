# Data protection in Amazon EC2<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in Amazon Elastic Compute Cloud\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form fields such as a **Name** field\. This includes when you work with Amazon EC2 or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.

## Encryption at rest<a name="encryption-rest"></a>

**EBS volumes**  
Amazon EBS encryption is an encryption solution for your EBS volumes and snapshots\. It uses AWS KMS keys\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

**Instance store volumes**  
The data on NVMe instance store volumes is encrypted using an XTS\-AES\-256 cipher implemented on a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\.

The data on HDD instance store volumes on H1, D3, and D3en instances is encrypted using XTS\-AES\-256 and one\-time keys\.

**Memory**

Memory encryption is enabled on the following instances:
+ Instances with AWS Graviton 2 processors, such as M6g instances\. These processors support always\-on memory encryption\. The encryption keys are securely generated within the host system, do not leave the host system, and are destroyed when the host is rebooted or powered down\.
+ Instances with Intel Xeon Scalable processors \(Ice Lake\), such as M6i instances\. These processors support always\-on memory encryption using Intel Total Memory Encryption \(TME\)\. 

## Encryption in transit<a name="encryption-transit"></a>

**Encryption at the physical layer**  
All data flowing across AWS Regions over the AWS global network is automatically encrypted at the physical layer before it leaves AWS secured facilities\. All traffic between AZs is encrypted\. Additional layers of encryption, including those listed in this section, may provide additional protections\. 

**Encryption provided by Amazon VPC and Transit Gateway cross\-Region peering**  
All cross\-Region traffic that uses Amazon VPC and Transit Gateway peering is automatically bulk\-encrypted when it exits a Region\. An additional layer of encryption is automatically provided at the physical layer for all cross\-Region traffic, as previously noted in this section\.

**Encryption between instances**  
AWS provides secure and private connectivity between EC2 instances of all types\. In addition, some instance types use the offload capabilities of the underlying Nitro System hardware to automatically encrypt in\-transit traffic between instances, using AEAD algorithms with 256\-bit encryption\. There is no impact on network performance\. To support this additional in\-transit traffic encryption between instances, the following requirements must be met:
+ The instances use the following instance types:
  + General purpose: M5dn \| M5n \| M5zn \| M6i
  + Compute optimized: C5a \| C5ad \| C5n \| C6gn
  + Memory optimized: R5dn \| R5n \| high memory \(u\-\*\), virtualized only
  + Storage optimized: D3 \| D3en \| I3en
  + Accelerated computing: G4ad \| G4dn \| Inf1 \| P3dn \| P4d \| VT1
+ The instances are in the same Region\.
+ The instances are in the same VPC or peered VPCs, and the traffic does not pass through a virtual network device or service, such as a load balancer or a transit gateway\.

An additional layer of encryption is automatically provided at the physical layer for all traffic before it leaves AWS secured facilities, as previously noted in this section\.

**To view the instance types that encrypt in\-transit traffic between instances using the AWS CLI**  
Use the following [ describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command\.

```
aws ec2 describe-instance-types \
--filters Name=network-info.encryption-in-transit-supported,Values=true \ 
--query "InstanceTypes[*].[InstanceType]" --output text
```

**Encryption to and from AWS Outposts**  
An Outpost creates special network connections called *service links* to its AWS home Region and, optionally, private connectivity to a VPC subnet that you specify\. All traffic over those connection is fully encrypted\. For more information, see [Connectivity through service links](https://docs.aws.amazon.com/outposts/latest/userguide/region-connectivity.html#service-links) and [Encryption in transit](https://docs.aws.amazon.com/outposts/latest/userguide/data-protection.html#encryption-transit) in the *AWS Outposts User Guide*\.

**Remote access encryption**  
SSH provides a secure communications channel for remote access to your Linux instances, whether directly or through EC2 Instance Connect\. Remote access to your instances using AWS Systems Manager Session Manager or the Run Command is encrypted using TLS 1\.2, and requests to create a connection are signed using [SigV4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) and authenticated and authorized by [AWS Identity and Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\.

It is your responsibility to use an encryption protocol, such as Transport Layer Security \(TLS\), to encrypt sensitive data in transit between clients and your Amazon EC2 instances\.
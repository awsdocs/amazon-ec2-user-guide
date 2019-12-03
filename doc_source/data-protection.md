# Data Protection in Amazon EC2<a name="data-protection"></a>

Amazon Elastic Compute Cloud \(Amazon EC2\) conforms to the AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/), which includes regulations and guidelines for data protection\. AWS is responsible for protecting the global infrastructure that runs all AWS services\. AWS maintains control over data hosted on this infrastructure, including the security configuration controls for handling customer content and personal data\. AWS customers and APN Partners, acting either as data controllers or data processors, are responsible for any personal data that they put in the AWS Cloud\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\), so that each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use TLS to communicate with AWS resources\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields or metadata, such as function names and tags\. Any data that you enter into metadata might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credential information in the URL to validate your request to that server\.

For more information about data protection, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

## Encryption at Rest<a name="encryption-rest"></a>

Amazon EBS encryption is an encryption solution for your EBS volumes and snapshots\. It uses AWS Key Management Service \(AWS KMS\) customer master keys \(CMK\)\. For more information, see [Amazon EBS Encryption](EBSEncryption.md)\.

The data on NVMe instance store volumes is encrypted using an XTS\-AES\-256 cipher implemented on a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\.

## Encryption in Transit<a name="encryption-transit"></a>

AWS provides secure and private connectivity between EC2 instances\. In addition, we automatically encrypt in\-transit traffic between supported instances in the same VPC or in peered VPCs, using AEAD algorithms with 256\-bit encryption\. This encryption feature uses the offload capabilities of the underlying hardware, and there is no impact on network performance\. The supported instances are: C5n, G4, I3en, M5dn, M5n, P3dn, R5dn, and R5n\.

SSH provides a secure communications channel for remote access to your Linux instances\. Remote access to your instances using AWS Systems Manager Session Manager and Run Command is encrypted using TLS 1\.2, and requests to create a connection are signed using SigV4\.

Use an encryption protocol such as Transport Layer Security \(TLS\) to encrypt sensitive data in transit between clients and your instances\.
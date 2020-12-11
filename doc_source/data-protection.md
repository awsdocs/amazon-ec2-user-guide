# Data protection in Amazon EC2<a name="data-protection"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in Amazon Elastic Compute Cloud\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields such as a **Name** field\. This includes when you work with Amazon EC2 or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into Amazon EC2 or other services might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credentials information in the URL to validate your request to that server\.

## Encryption at rest<a name="encryption-rest"></a>

Amazon EBS encryption is an encryption solution for your EBS volumes and snapshots\. It uses AWS Key Management Service \(AWS KMS\) customer master keys \(CMK\)\. For more information, see [Amazon EBS encryption](EBSEncryption.md)\.

The data on NVMe instance store volumes is encrypted using an XTS\-AES\-256 cipher implemented on a hardware module on the instance\. The encryption keys are generated using the hardware module and are unique to each NVMe instance storage device\. All encryption keys are destroyed when the instance is stopped or terminated and cannot be recovered\. You cannot disable this encryption and you cannot provide your own encryption key\.

## Encryption in transit<a name="encryption-transit"></a>

SSH provides a secure communications channel for remote access to your Linux instances\. Remote access to your instances using AWS Systems Manager Session Manager and Run Command is encrypted using TLS 1\.2, and requests to create a connection are signed using SigV4\.

Use an encryption protocol such as Transport Layer Security \(TLS\) to encrypt sensitive data in transit between clients and your instances\.

AWS provides secure and private connectivity between EC2 instances of all types\. In addition, some instance types use the offload capabilities of the underlying hardware to automatically encrypt in\-transit traffic between instances, using AEAD algorithms with 256\-bit encryption\. There is no impact on network performance\. The following requirements must be met to ensure the additional in\-transit traffic encryption:
+ The instances use the following instance types: C5a, C5ad, C5n, D3, D3en, G4ad, G4dn, I3en, M5dn, M5n, M5zn, P3dn, P4d, R5dn, and R5n\.
+ The instances are in the same Region\.
+ The instances are in the same VPC or peered VPCs, and the traffic does not pass through a virtual network device, such as a load balancer or a transit gateway\.
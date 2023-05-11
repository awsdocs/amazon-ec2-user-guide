# AMD SEV\-SNP<a name="sev-snp"></a>

AMD Secure Encrypted Virtualization\-Secure Nested Paging \(AMD SEV\-SNP\) is a CPU feature that provides the following properties:
+ **Attestation** – AMD SEV\-SNP enables you to retrieve a signed attestation report that contains a cryptographic measure that can be used to validate the instance’s state and identity, and that it is running on genuine AMD hardware\. For more information, see [Attestation with AMD SEV\-SNP](snp-attestation.md)\.
+ **Memory encryption** – Starting with AMD EPYC \(Milan\), AWS Graviton2, and Intel Xeon Scalable \(Ice Lake\) processors, instance memory is always encrypted\. Instances that are enabled for AMD SEV\-SNP use an instance\-specific key for their memory encryption\.

## Pricing<a name="snp-pricing"></a>

When you launch an Amazon EC2 instance with AMD SEV\-SNP turned on, you are charged an additional hourly usage fee that is equivalent to 10 percent of the [On\-Demand hourly rate](http://aws.amazon.com/ec2/pricing/on-demand/) of the selected instance type\.

This AMD SEV\-SNP usage fee is a separate charge to your Amazon EC2 instance usage\. Reserved Instances, Savings Plans, and operating system usage don't impact this fee\. 
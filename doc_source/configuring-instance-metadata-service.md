# Use IMDSv2<a name="configuring-instance-metadata-service"></a>

You can access instance metadata from a running instance using one of the following methods:
+ Instance Metadata Service Version 1 \(IMDSv1\) – a request/response method
+ Instance Metadata Service Version 2 \(IMDSv2\) – a session\-oriented method

By default, you can use either IMDSv1 or IMDSv2, or both\. The instance metadata service distinguishes between IMDSv1 and IMDSv2 requests based on whether, for any given request, either the `PUT` or `GET` headers, which are unique to IMDSv2, are present in that request\.

You can configure the Instance Metadata Service \(IMDS\) on each instance so that local code or users must use IMDSv2\. When you specify that IMDSv2 must be used, IMDSv1 no longer works\. For information about how to configure your instance to use IMDSv2, see [Configure the instance metadata options](configuring-instance-metadata-options.md)\.

For an extensive review of IMDSv2, see [Add defense in depth against open firewalls, reverse proxies, and SSRF vulnerabilities with enhancements to the EC2 Instance Metadata Service](http://aws.amazon.com/blogs/security/defense-in-depth-open-firewalls-reverse-proxies-ssrf-vulnerabilities-ec2-instance-metadata-service/)\.

To retrieve instance metadata, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

**Topics**
+ [How Instance Metadata Service Version 2 works](instance-metadata-v2-how-it-works.md)
+ [Transition to using Instance Metadata Service Version 2](instance-metadata-transition-to-version-2.md)
+ [Use a supported AWS SDK](use-a-supported-sdk-version-for-imdsv2.md)
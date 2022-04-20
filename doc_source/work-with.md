# Use EBS direct APIs<a name="work-with"></a>

The following topics show how to read and write snapshots using the EBS direct APIs\. You can read and write snapshots using the AWS CLI, AWS APIs, and AWS SDKs only\. For more information, see:
+ [ Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and [ Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html#cli-quick-configuration)
+ [EBS direct APIs Reference](https://docs.aws.amazon.com/ebs/latest/APIReference/)
+ [AWS SDKs](http://aws.amazon.com/tools/#SDKs)

**Important**  
The EBS direct APIs require an AWS Signature Version 4 signature\. For more information, see [Use Signature Version 4 signing](ebsapis-using-sigv4.md)\.

**Topics**
+ [Read snapshots](readsnapshots.md)
+ [Write snapshots](writesnapshots.md)
+ [Use encryption](ebsapis-using-encryption.md)
+ [Use Signature Version 4 signing](ebsapis-using-sigv4.md)
+ [Use checksums](ebsapis-using-checksums.md)
+ [Idempotency for StartSnapshot API](ebs-direct-api-idempotency.md)
+ [Error retries](error-retries.md)
+ [Optimize performance](ebsapi-performance.md)
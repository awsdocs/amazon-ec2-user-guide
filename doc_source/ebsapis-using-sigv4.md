# Use Signature Version 4 signing<a name="ebsapis-using-sigv4"></a>

Signature Version 4 is the process to add authentication information to AWS requests sent by HTTP\. For security, most requests to AWS must be signed with an access key, which consists of an access key ID and secret access key\. These two keys are commonly referred to as your security credentials\. For information about how to obtain credentials for your account, see [Understanding and getting your credentials](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html)\.

If you intend to manually create HTTP requests, you must learn how to sign them\. When you use the AWS Command Line Interface \(AWS CLI\) or one of the AWS SDKs to make requests to AWS, these tools automatically sign the requests for you with the access key that you specify when you configure the tools\. When you use these tools, you don't need to learn how to sign requests yourself\.

For more information, see [Signing AWS requests with Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html) in the *AWS General Reference*\.
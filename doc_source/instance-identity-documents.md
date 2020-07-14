# Instance Identity Documents<a name="instance-identity-documents"></a>

Each instance that you launch has an instance identity document that provides information about the instance itself\. You can use the instance identity document to validate the attributes of the instance\.

The instance identity document is generated when the instance is launched and it is exposed \(in plaintext JSON format\) through the Instance Metadata Service\. The IP address `169.254.169.254` is a link\-local address and is valid only from the instance\. For more information, see [Link\-local address](https://en.wikipedia.org/wiki/Link-local_address) on Wikipedia\.

You can retrieve the instance identity document from a running instance at any time\. The instance identity document includes the following information:


| Data | Description | 
| --- | --- | 
| devpayProductCodes | Deprecated\. | 
| marketplaceProductCodes | The AWS Marketplace product code of the AMI used to launch the instance\. | 
| availabilityZone | The Availability Zone in which the instance is running\. | 
| privateIp | The private IPv4 address of the instance\. | 
| version | The version of the instance identity document format\. | 
| instanceId | The ID of the instance\. | 
| billingProducts | The billing product code of the AMI used to launch the instance\. | 
| instanceType | The instance type of the instance\. | 
| accountId | The ID of the AWS account that launched the instance\. | 
| imageId | The ID of the AMI used to launch the instance\. | 
| pendingTime | The date and time that the instance was launched\. | 
| architecture | The architecture of the AMI used to launch the instance \(i386 \| x86\_64 \| arm64\)\. | 
| kernelId | The ID of the kernel associated with the instance, if applicable\. | 
| ramdiskId | The ID of the RAM disk associated with the instance, if applicable\. | 
| region | The Region in which the instance is running\. | 

## Retrieve the Plaintext instance identity document<a name="retrieve-iid"></a>

**To retrieve the plaintext instance identity document**  
Connect to the instance and run one of the following commands depending on the Instance Metadata Service \(IMDS\) version used by the instance\.

------
#### [ IMDSv2 ]

```
$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/dynamic/instance-identity/document
```

------
#### [ IMDSv1 ]

```
$ curl http://169.254.169.254/latest/dynamic/instance-identity/document
```

------

The following is example output\.

```
{
    "devpayProductCodes" : null,
    "marketplaceProductCodes" : [ "1abc2defghijklm3nopqrs4tu" ], 
    "availabilityZone" : "us-west-2b",
    "privateIp" : "10.158.112.84",
    "version" : "2017-09-30",
    "instanceId" : "i-1234567890abcdef0",
    "billingProducts" : null,
    "instanceType" : "t2.micro",
    "accountId" : "123456789012",
    "imageId" : "ami-5fb8c835",
    "pendingTime" : "2016-11-19T16:32:11Z",
    "architecture" : "x86_64",
    "kernelId" : null,
    "ramdiskId" : null,
    "region" : "us-west-2"
}
```

## Verifying the Instance Identity Document<a name="verify-iid"></a>

If you intend to use the contents of the instance identity document for an important purpose, you should verify its contents and authenticity before using it\.

The plaintext instance identity document is accompanied by three hashed and encrypted signatures\. You can use these signatures to verify the origin and authenticity of the instance identity document and the information that it includes\. The following signatures are provided:
+ Base64\-encoded signature—This is a base64\-encoded SHA256 hash of the instance identity document that is encrypted using an RSA key pair\.
+ PKCS7 signature—This is a SHA1 hash of the instance identity document that is encrypted using a DSA key pair\.
+ RSA\-2048 signature—This is a SHA256 hash of the instance identity document that is encrypted using an RSA\-2048 key pair\.

Each signature is available at a different endpoint in the instance metadata\. You can use any one of these signatures depending on your hashing and encryption requirements\. To verify the signatures, you must use the corresponding AWS public certificate\.

**Important**  
To validate the instance identity document using the base64\-encoded signature or RSA2048 signature, you must request the corresponding AWS public certificate from [ AWS Support](https://console.aws.amazon.com/support/home#/)\. 

The following topics provide detailed steps for validating the instance identity document using each signature\.
+ [Using the PKCS7 Signature to Verify the Instance Identity Document](verify-pkcs7.md)
+ [Using the Base64\-Encoded Signature to Verify the Instance Identity Document](verify-signature.md)
+ [Using the RSA\-2048 Signature to Verify the Instance Identity Document](verify-rsa2048.md)
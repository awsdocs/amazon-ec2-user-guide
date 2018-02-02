# Instance Identity Documents<a name="instance-identity-documents"></a>

An instance identity document is a JSON file that describes an instance\. The instance identity document is accompanied by a signature and a PKCS7 signature which can be used to verify the accuracy, origin, and authenticity of the information provided in the document\. For example, you may have downloaded free software with paid updates\.

The instance identity document is generated when the instance is launched, and exposed to the instance through instance metadata\. It validates the attributes of the instances, such as the subscribed software, instance size, instance type, operating system, and AMI\. 

**Important**  
Due to the dynamic nature of instance identity documents and signatures, we recommend retrieving the instance identity document and signature regularly\.

## Obtaining the Instance Identity Document and Signatures<a name="instance-identity-signatures"></a>

To retrieve the instance identity document, use the following command from your running instance:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/dynamic/instance-identity/document
```

The following is example output:

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

To retrieve the instance identity signature, use the following command from your running instance:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/dynamic/instance-identity/signature
```

The following is example output:

```
dExamplesjNQhhJan7pORLpLSr7lJEF4V2DhKGlyoYVBoUYrY9njyBCmhEayaGrhtS/AWY+LPx
lVSQURF5n0gwPNCuO6ICT0fNrm5IH7w9ydyaexamplejJw8XvWPxbuRkcN0TAA1p4RtCAqm4ms
x2oALjWSCBExample=
```

To retrieve the PKCS7 signature, use the following command from your running instance:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/dynamic/instance-identity/pkcs7
```

The following is example output:

```
MIICiTCCAfICCQD6m7oRw0uXOjANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMC
VVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6
b24xFDASBgNVBAsTC0lBTSBDb25zb2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAd
BgkqhkiG9w0BCQEWEG5vb25lQGFtYXpvbi5jb20wHhcNMTEwNDI1MjA0NTIxWhcN
MTIwNDI0MjA0NTIxWjCBiDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYD
VQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6b24xFDASBgNVBAsTC0lBTSBDb25z
b2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAdBgkqhkiG9w0BCQEWEG5vb25lQGFt
YXpvbi5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMaK0dn+a4GmWIWJ
21uUSfwfEvySWtC2XADZ4nB+BLYgVIk60CpiwsZ3G93vUEIO3IyNoH/f0wYK8m9T
rDHudUZg3qX4waLG5M43q7Wgc/MbQITxOUSQv7c7ugFFDzQGBzZswY6786m86gpE
Ibb3OhjZnzcvQAaRHhdlQWIMm2nrAgMBAAEwDQYJKoZIhvcNAQEFBQADgYEAtCu4
nUhVVxYUntneD9+h8Mg9q6q+auNKyExzyLwaxlAoo7TJHidbtS4J5iNmZgXL0Fkb
FFBjvSfpJIlJ00zbhNYS5f6GuoEDmFJl0ZxBHjJnyp378OD8uTs7fLvjx79LjSTb
NYiytVbZPQUQ5Yaxu2jXnimvw3rrszlaEXAMPLE
```

## Verifying the PKCS7 Signature<a name="instance-identity-signature-verification-example"></a>

You can use the PKCS7 signature to verify your instance by validating it against the appropriate AWS public certificate\.

The AWS public certificate for the regions provided by an AWS account is as follows:

```
-----BEGIN CERTIFICATE-----
MIIC7TCCAq0CCQCWukjZ5V4aZzAJBgcqhkjOOAQDMFwxCzAJBgNVBAYTAlVTMRkw
FwYDVQQIExBXYXNoaW5ndG9uIFN0YXRlMRAwDgYDVQQHEwdTZWF0dGxlMSAwHgYD
VQQKExdBbWF6b24gV2ViIFNlcnZpY2VzIExMQzAeFw0xMjAxMDUxMjU2MTJaFw0z
ODAxMDUxMjU2MTJaMFwxCzAJBgNVBAYTAlVTMRkwFwYDVQQIExBXYXNoaW5ndG9u
IFN0YXRlMRAwDgYDVQQHEwdTZWF0dGxlMSAwHgYDVQQKExdBbWF6b24gV2ViIFNl
cnZpY2VzIExMQzCCAbcwggEsBgcqhkjOOAQBMIIBHwKBgQCjkvcS2bb1VQ4yt/5e
ih5OO6kK/n1Lzllr7D8ZwtQP8fOEpp5E2ng+D6Ud1Z1gYipr58Kj3nssSNpI6bX3
VyIQzK7wLclnd/YozqNNmgIyZecN7EglK9ITHJLP+x8FtUpt3QbyYXJdmVMegN6P
hviYt5JH/nYl4hh3Pa1HJdskgQIVALVJ3ER11+Ko4tP6nwvHwh6+ERYRAoGBAI1j
k+tkqMVHuAFcvAGKocTgsjJem6/5qomzJuKDmbJNu9Qxw3rAotXau8Qe+MBcJl/U
hhy1KHVpCGl9fueQ2s6IL0CaO/buycU1CiYQk40KNHCcHfNiZbdlx1E9rpUp7bnF
lRa2v1ntMX3caRVDdbtPEWmdxSCYsYFDk4mZrOLBA4GEAAKBgEbmeve5f8LIE/Gf
MNmP9CM5eovQOGx5ho8WqD+aTebs+k2tn92BBPqeZqpWRa5P/+jrdKml1qx4llHW
MXrs3IgIb6+hUIB+S8dz8/mmO0bpr76RoZVCXYab2CZedFut7qc3WUH9+EUAH5mw
vSeDCOUMYQR7R9LINYwouHIziqQYMAkGByqGSM44BAMDLwAwLAIUWXBlk40xTwSw
7HX32MxXYruse9ACFBNGmdX2ZBrVNGrN9N2f6ROk0k9K
-----END CERTIFICATE-----
```

The AWS public certificate for the AWS GovCloud \(US\) region is as follows:

```
-----BEGIN CERTIFICATE-----
MIICuzCCAiQCCQDrSGnlRgvSazANBgkqhkiG9w0BAQUFADCBoTELMAkGA1UEBhMC
VVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZWF0dGxlMRMwEQYDVQQKEwpBbWF6
b24uY29tMRYwFAYDVQQLEw1FQzIgQXV0aG9yaXR5MRowGAYDVQQDExFFQzIgQU1J
IEF1dGhvcml0eTEqMCgGCSqGSIb3DQEJARYbZWMyLWluc3RhbmNlLWlpZEBhbWF6
b24uY29tMB4XDTExMDgxMjE3MTgwNVoXDTIxMDgwOTE3MTgwNVowgaExCzAJBgNV
BAYTAlVTMQswCQYDVQQIEwJXQTEQMA4GA1UEBxMHU2VhdHRsZTETMBEGA1UEChMK
QW1hem9uLmNvbTEWMBQGA1UECxMNRUMyIEF1dGhvcml0eTEaMBgGA1UEAxMRRUMy
IEFNSSBBdXRob3JpdHkxKjAoBgkqhkiG9w0BCQEWG2VjMi1pbnN0YW5jZS1paWRA
YW1hem9uLmNvbTCBnzANBgkqhkiG9w0BAQEFAAOBjQAwgYkCgYEAqaIcGFFTx/SO
1W5G91jHvyQdGP25n1Y91aXCuOOWAUTvSvNGpXrI4AXNrQF+CmIOC4beBASnHCx0
82jYudWBBl9Wiza0psYc9flrczSzVLMmN8w/c78F/95NfiQdnUQPpvgqcMeJo82c
gHkLR7XoFWgMrZJqrcUK0gnsQcb6kakCAwEAATANBgkqhkiG9w0BAQUFAAOBgQDF
VH0+UGZr1LCQ78PbBH0GreiDqMFfa+W8xASDYUZrMvY3kcIelkoIazvi4VtPO7Qc
yAiLr6nkk69Tr/MITnmmsZJZPetshqBndRyL+DaTRnF0/xvBQXj5tEh+AmRjvGtp
6iS1rQoNanN8oEcT2j4b48rmCmnDhRoBcFHwCYs/3w==
-----END CERTIFICATE-----
```

For other regions, contact [AWS Support](https://aws.amazon.com/premiumsupport/) to get the AWS public certificate\.

**To verify the PKCS7 signature**

1. From your instance, create a temporary file for the PKCS7 signature:

   ```
   [ec2-user ~]$ PKCS7=$(mktemp)
   ```

1. Add the `-----BEGIN PKCS7-----` header to the temporary PKCS7 file:

   ```
   [ec2-user ~]$ echo "-----BEGIN PKCS7-----" > $PKCS7
   ```

1. Append the contents of the PKCS7 signature from the instance metadata, plus a new line:

   ```
   [ec2-user ~]$ curl -s http://169.254.169.254/latest/dynamic/instance-identity/pkcs7 >> $PKCS7
   [ec2-user ~]$ echo "" >> $PKCS7
   ```

1. Append the `-----END PKCS7-----` footer:

   ```
   [ec2-user ~]$ echo "-----END PKCS7-----" >> $PKCS7
   ```

1. Create a temporary file for the instance identity document:

   ```
   [ec2-user ~]$ DOCUMENT=$(mktemp)
   ```

1. Add the contents of the document from your instance metadata to the temporary document file:

   ```
   [ec2-user ~]$ curl -s http://169.254.169.254/latest/dynamic/instance-identity/document > $DOCUMENT
   ```

1. Open a text editor and create a file named `AWSpubkey`\. Copy and paste the contents of the AWS public certificate above to the file and save it\.

1. Use the OpenSSL tools to verify the signature as follows:

   ```
   [ec2-user ~]$ openssl smime -verify -in $PKCS7 -inform PEM -content $DOCUMENT -certfile AWSpubkey -noverify > /dev/null
   Verification successful
   ```
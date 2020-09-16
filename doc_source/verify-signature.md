# Using the base64\-encoded signature to verify the instance identity document<a name="verify-signature"></a>

This topic explains how to verify the instance identity document using the base64\-encoded signature and the AWS RSA public certificate\.

**Important**  
To validate the instance identity document using the base64\-encoded signature, you must request the AWS RSA public certificate from [AWS Support](https://console.aws.amazon.com/support/home#/)\. 

**To validate the instance identity document using the base64\-encoded signature and the AWS RSA public certificate**

1. Connect to the instance\.

1. Retrieve the base64\-encoded signature from the instance metadata, convert it to binary, and add it to a file named `signature`\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

   ```
   $ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \ && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/dynamic/instance-identity/signature | base64 -d > signature
   ```

------
#### [ IMDSv1 ]

   ```
   $ curl -s http://169.254.169.254/latest/dynamic/instance-identity/signature | base64 -d > signature
   ```

------

1. Retrieve the plaintext instance identity document from the instance metadata and add it to a file named `document`\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

   ```
   $ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \ && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/dynamic/instance-identity/document > document
   ```

------
#### [ IMDSv1 ]

   ```
   $ curl -s http://169.254.169.254/latest/dynamic/instance-identity/document > document
   ```

------

1. Add the AWS RSA public certificate that you received from AWS Support to a file named `certificate`\.

1. Extract the public key from the certificate that you received from AWS Support and save it to a file named `key`\.

   ```
   $ openssl x509 -pubkey -noout -in certificate > key
   ```

1. Use **OpenSSL dgst** command to verify the instance identity document\.

   ```
   $ openssl dgst -sha256 -verify key -signature signature document
   ```

   If the signature is valid, the `Verified OK` message appears\. If the signature cannot be verified, contact AWS Support\.
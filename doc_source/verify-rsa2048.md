# Using the RSA\-2048 Signature to Verify the Instance Identity Document<a name="verify-rsa2048"></a>

This topic explains how to verify the instance identity document using the RSA\-2048 signature and the AWS RSA\-2048 public certificate\.

**Important**  
To validate the instance identity document using the RSA\-2048 signature, you must request the AWS RSA\-2048 public certificate from [AWS Support](https://console.aws.amazon.com/support/home#/)\. 

**To verify the instance identity document using the RSA\-2048 signature and the AWS RSA\-2048 public certificate**

1. Connect to the instance\.

1. Retrieve the RSA\-2048 signature from the instance metadata and add it to a file named `rsa2048`\.

   1. Add the `-----BEGIN PKCS7-----` header to the `rsa2048` file\.

      ```
      $ echo "-----BEGIN PKCS7-----" > rsa2048
      ```

   1. Retrieve the RSA\-2048 signature from the instance metadata and append it to the `rsa2048` file\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

      ```
      $ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
      && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/dynamic/instance-identity/rsa2048 >> rsa2048
      ```

------
#### [ IMDSv1 ]

      ```
      $ curl -s http://169.254.169.254/latest/dynamic/instance-identity/rsa2048 >> rsa2048
      ```

------

   1. Append the `-----END PKCS7-----` footer to a new line in the `rsa2048` file\.

      ```
      $ echo "" >> rsa2048
      $ echo "-----END PKCS7-----" >> rsa2048
      ```

1. Add the contents of the instance identity document from the instance metadata to a file named `document`\. Use one of the following commands depending on the IMDS version used by the instance\.

------
#### [ IMDSv2 ]

   ```
   $ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \ 
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/dynamic/instance-identity/document > document
   ```

------
#### [ IMDSv1 ]

   ```
   $ curl -s http://169.254.169.254/latest/dynamic/instance-identity/document > document
   ```

------

1. Add the AWS RSA\-2048 public certificate to a file named `certificate`\.

   1. Create the `certificate` file\.

      ```
      $ touch certificate
      ```

   1. Open the `certificate` file using your preferred text editor and add the contents of the AWS RSA\-2048 public certificate that you received from AWS Support\.

   1. Save and close the file\.

1. Use the **OpenSSL smime** command to verify the signature\. Include the `-verify` option to indicate that the signature needs to be verified, and the `-noverify` option to indicate that the certificate does not need to be verified\.

   ```
   $ openssl smime -verify -in rsa2048 -inform PEM -content document -certfile certificate -noverify
   ```

   If the signature is valid, the `Verification successful` message appears\. If the signature cannot be verified, contact AWS Support\.
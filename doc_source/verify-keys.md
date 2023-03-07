# Verify keys<a name="verify-keys"></a>

## Verify your key pair's fingerprint<a name="verify-key-pair-fingerprints"></a>

On the **Key Pairs** page in the Amazon EC2 console, the **Fingerprint** column displays the fingerprints generated from your key pairs\.

You can use the fingerprint that's displayed on the **Key Pairs** page to verify that the private key you have on your local machine matches the public key stored in Amazon EC2\. From the computer where you downloaded the private key file, generate a fingerprint from the private key file\. The output should match the fingerprint that's displayed in the console\. When Amazon EC2 calculates a fingerprint, Amazon EC2 might append padding to the fingerprint with `=` characters\. Other tools, such as ssh\-keygen, might omit this padding\.

### How the fingerprints are calculated<a name="how-ec2-key-fingerprints-are-calculated"></a>

Amazon EC2 uses different hash functions to calculate the fingerprints for RSA and ED25519 key pairs\. Furthermore, for RSA key pairs, Amazon EC2 calculates the fingerprints differently using different hash functions depending on whether the key pair was created by Amazon EC2 or imported to Amazon EC2\.

The following table lists the hash functions that are used to calculate the fingerprints for RSA and ED25519 key pairs that are created by Amazon EC2 and imported to Amazon EC2\.


**Hash functions used to calculate fingerprints**  

| Key pair source | RSA key pairs | ED25519 key pairs | 
| --- | --- | --- | 
| Created by Amazon EC2 | SHA\-1 | SHA\-256 | 
| Imported to Amazon EC2 | MD5¹ | SHA\-256 | 

¹ If you import a public RSA key to Amazon EC2, the fingerprint is calculated using an MD5 hash function\. This is true regardless of how you created the key pair, for example, by using a third\-party tool or by generating a new public key from an existing private key created using Amazon EC2\.

### When using the same key pair in different Regions<a name="when-using-same-key-pair-in-different-regions"></a>

If you plan to use the same key pair to connect to instances in different AWS Regions, you must import the public key to all of the Regions in which you'll use it\. If you use Amazon EC2 to create the key pair, you can [Retrieve the public key material](describe-keys.md#retrieving-the-public-key) so that you can import the public key to the other Regions\.

**Note**  
If you create an RSA key pair using Amazon EC2, and then you generate a public key from the Amazon EC2 private key, the imported public keys will have a different fingerprint than the original public key\. This is because the fingerprint of the original RSA key created using Amazon EC2 is calculated using a SHA\-1 hash function, while the fingerprint of the imported RSA keys is calculated using an MD5 hash function\.

For ED25519 key pairs, the fingerprints will be same regardless of whether they’re created by Amazon EC2 or imported to Amazon EC2, because the same SHA\-256 hash function is used to calculate the fingerprint\.

### Generate a fingerprint from the private key<a name="generate-fingerprint-from-private-key"></a>

Use one of the following commands to generate a fingerprint from the private key on your local machine\.

If you're using a Windows local machine, you can run the following commands using the Windows Subsystem for Linux \(WSL\)\. Install the WSL and a Linux distribution using the instructions in the [Windows 10 Installation Guide](https://docs.microsoft.com/en-us/windows/wsl/install-win10)\. The example in the instructions installs the Ubuntu distribution of Linux, but you can install any distribution\. You are prompted to restart your computer for the changes to take effect\.
+ **If you created the key pair using Amazon EC2**

  Use the OpenSSL tools to generate a fingerprint as shown in the following examples\.

  For RSA key pairs:

  ```
  openssl pkcs8 -in path_to_private_key -inform PEM -outform DER -topk8 -nocrypt | openssl sha1 -c
  ```

  For ED25519 key pairs:

  ```
  ssh-keygen -l -f path_to_private_key
  ```
+ **\(RSA key pairs only\) If you imported the public key to Amazon EC2**

   You can follow this procedure regardless of how you created the key pair, for example, by using a third\-party tool or by generating a new public key from an existing private key created using Amazon EC2

  Use the OpenSSL tools to generate the fingerprint as shown in the following example\.

  ```
  openssl rsa -in path_to_private_key -pubout -outform DER | openssl md5 -c
  ```
+ **If you created an OpenSSH key pair using OpenSSH 7\.8 or later and imported the public key to Amazon EC2**

  Use ssh\-keygen to generate the fingerprint as shown in the following examples\.

  For RSA key pairs:

  ```
  ssh-keygen -ef path_to_private_key -m PEM | openssl rsa -RSAPublicKey_in -outform DER | openssl md5 -c
  ```

  For ED25519 key pairs:

  ```
  ssh-keygen -l -f path_to_private_key
  ```
# Tutorial: Configure SSL/TLS on Amazon Linux<a name="SSL-on-amazon-linux-ami"></a>

Secure Sockets Layer/Transport Layer Security \(SSL/TLS\) creates an encrypted channel between a web server and web client that protects data in transit from being eavesdropped on\. This tutorial explains how to add support manually for SSL/TLS on an EC2 instance with the Amazon Linux AMI and Apache web server\. If you plan to offer commercial\-grade services, the [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/), which is not discussed here, is a good option\.

For historical reasons, web encryption is often referred to simply as SSL\. While web browsers still support SSL, its successor protocol TLS is less vulnerable to attack\. The Amazon Linux AMI disables server\-side support all versions of SSL by default\. [Security standards bodies](https://www.ssl.com/article/deprecating-early-tls/) consider TLS 1\.0 to be unsafe, and both TLS 1\.0 and TLS 1\.1 are on track to be formally [deprecated](https://tools.ietf.org/html/draft-ietf-tls-oldversions-deprecate-03) by the IETF\. This tutorial contains guidance based exclusively on enabling TLS 1\.2\. \(A newer TLS 1\.3 protocol exists in draft form, but is not supported on Amazon Linux\.\) For more information about the updated encryption standards, see [RFC 7568](https://tools.ietf.org/html/rfc7568) and [RFC 8446](https://tools.ietf.org/html/rfc8446)\.

This tutorial refers to modern web encryption simply as TLS\.

**Important**  
These procedures are intended for use with the Amazon Linux AMI\. If you are trying to set up a LAMP web server on an instance with a different distribution, some procedures in this tutorial might not work for you\. For information about LAMP web servers on Ubuntu, see the Ubuntu community documentation [ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)\. For information about Red Hat Enterprise Linux, see the Customer Portal documentation [Web Servers](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/ch-Web_Servers.html)\.

**Topics**
+ [Prerequisites](#ssl-prereq-alami)
+ [Step 1: Enable TLS on the server](#ssl-enable-alami)
+ [Step 2: Obtain a CA\-signed certificate](#ssl-certificate-alami)
+ [Step 3: Test and harden the security configuration](#ssl-test-alami)
+ [Troubleshooting](#troubleshooting-alami)
+ [Certificate automation: Let's Encrypt with Certbot on Amazon Linux](#lets-encrypt-alami)

## Prerequisites<a name="ssl-prereq-alami"></a>

Before you begin this tutorial, complete the following steps:
+ Launch an EBS\-backed instance using the Amazon Linux AMI\. For more information, see [Step 1: Launch an instance](EC2_GetStarted.md#ec2-launch-instance)\. 
+ Configure your security group to allow your instance to accept connections on the following TCP ports: 
  + SSH \(port 22\)
  + HTTP \(port 80\)
  + HTTPS \(port 443\)

  For more information, see [Authorizing inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.
+ Install Apache web server\. For step\-by\-step instructions, see [Tutorial: Installing a LAMP Web Server on Amazon Linux](install-LAMP.md)\. Only the http24 package and its dependencies are needed; you can ignore the instructions involving PHP and MySQL\.
+ To identify and authenticate web sites, the TLS public key infrastructure \(PKI\) relies on the Domain Name System \(DNS\)\. To use your EC2 instance to host a public web site, you need to register a domain name for your web server or transfer an existing domain name to your Amazon EC2 host\. Numerous third\-party domain registration and DNS hosting services are available for this, or you can use [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)\. 

## Step 1: Enable TLS on the server<a name="ssl-enable-alami"></a>

This procedure takes you through the process of setting up TLS on Amazon Linux with a self\-signed digital certificate\.

**Note**  
A self\-signed certificate is acceptable for testing but not production\. If you expose your self\-signed certificate to the internet, visitors to your site receive security warnings\.

**To enable TLS on a server**

1. [Connect to your instance](EC2_GetStarted.md#ec2-connect-to-instance-linux) and confirm that Apache is running\.

   ```
   [ec2-user ~]$ sudo service httpd status
   ```

   If necessary, start Apache\.

   ```
   [ec2-user ~]$ sudo service httpd start
   ```

1. To ensure that all of your software packages are up to date, perform a quick software update on your instance\. This process may take a few minutes, but it is important to make sure you have the latest security updates and bug fixes\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. If you would like to examine the updates before installing, you can omit this option\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Now that your instance is current, add TLS support by installing the Apache module `mod_ssl`:

   ```
   [ec2-user ~]$ sudo yum install -y mod24_ssl
   ```

   Your instance now has the following files that you use to configure your secure server and create a certificate for testing:  
`/etc/httpd/conf.d/ssl.conf`  
The configuration file for mod\_ssl\. It contains "directives" telling Apache where to find encryption keys and certificates, the TLS protocol versions to allow, and the encryption ciphers to accept\.  
`/etc/pki/tls/private/localhost.key`  
An automatically generated, 2048\-bit RSA private key for your Amazon EC2 host\. During installation, OpenSSL used this key to generate a self\-signed host certificate, and you can also use this key to generate a certificate signing request \(CSR\) to submit to a certificate authority \(CA\)\.  
`/etc/pki/tls/certs/localhost.crt`  
An automatically generated, self\-signed X\.509 certificate for your server host\. This certificate is useful for testing that Apache is properly set up to use TLS\.

   The `.key` and `.crt` files are both in PEM format, which consists of Base64\-encoded ASCII characters framed by "BEGIN" and "END" lines, as in this abbreviated example of a certificate:

   ```
   -----BEGIN CERTIFICATE-----
   MIIEazCCA1OgAwIBAgICWxQwDQYJKoZIhvcNAQELBQAwgbExCzAJBgNVBAYTAi0t
   MRIwEAYDVQQIDAlTb21lU3RhdGUxETAPBgNVBAcMCFNvbWVDaXR5MRkwFwYDVQQK
   DBBTb21lT3JnYW5pemF0aW9uMR8wHQYDVQQLDBZTb21lT3JnYW5pemF0aW9uYWxV
   bml0MRkwFwYDVQQDDBBpcC0xNzItMzEtMjAtMjM2MSQwIgYJKoZIhvcNAQkBFhVy
   ...
   z5rRUE/XzxRLBZOoWZpNWTXJkQ3uFYH6s/sBwtHpKKZMzOvDedREjNKAvk4ws6F0
   WanXWehT6FiSZvB4sTEXXJN2jdw8g+sHGnZ8zCOsclknYhHrCVD2vnBlZJKSZvak
   3ZazhBxtQSukFMOnWPP2a0DMMFGYUHOd0BQE8sBJxg==
   -----END CERTIFICATE-----
   ```

   The file names and extensions are a convenience and have no effect on function; you can call a certificate `cert.crt`, `cert.pem`, or any other file name, so long as the related directive in the `ssl.conf` file uses the same name\.
**Note**  
When you replace the default TLS files with your own customized files, be sure that they are in PEM format\. 

1. Restart Apache\.

   ```
   [ec2-user ~]$ sudo service httpd restart
   ```

1. Your Apache web server should now support HTTPS \(secure HTTP\) over port 443\. Test it by typing the IP address or fully qualified domain name of your EC2 instance into a browser URL bar with the prefix **https://**\. Because you are connecting to a site with a self\-signed, untrusted host certificate, your browser may display a series of security warnings\. 

   Override the warnings and proceed to the site\. If the default Apache test page opens, it means that you have successfully configured TLS on your server\. All data passing between the browser and server is now safely encrypted\.

   To prevent site visitors from encountering warning screens, you need to obtain a certificate that not only encrypts, but also publicly authenticates you as the owner of the site\.

## Step 2: Obtain a CA\-signed certificate<a name="ssl-certificate-alami"></a>

You can use the following process to obtain a CA\-signed certificate:
+ Generate a certificate signing request \(CSR\) from a private key
+ Submit the CSR to a certificate authority \(CA\)
+ Obtain a signed host certificate
+ Configure Apache to use the certificate

A self\-signed TLS X\.509 host certificate is cryptologically identical to a CA\-signed certificate\. The difference is social, not mathematical; a CA promises to validate, at a minimum, a domain's ownership before issuing a certificate to an applicant\. Each web browser contains a list of CAs trusted by the browser vendor to do this\. An X\.509 certificate consists primarily of a public key that corresponds to your private server key, and a signature by the CA that is cryptographically tied to the public key\. When a browser connects to a web server over HTTPS, the server presents a certificate for the browser to check against its list of trusted CAs\. If the signer is on the list, or accessible through a chain of trust consisting of other trusted signers, the browser negotiates a fast encrypted data channel with the server and loads the page\. 

Certificates generally cost money because of the labor involved in validating the requests, so it pays to shop around\. A list of well\-known CAs can be found at [dmoztools\.net](http://dmoztools.net/Computers/Security/Public_Key_Infrastructure/PKIX/Tools_and_Services/Third_Party_Certificate_Authorities/)\. A few CAs offer basic\-level certificates free of charge\. The most notable of these is the [Let's Encrypt](https://letsencrypt.org/) project, which also supports automation of the certificate creation and renewal process\. For more information about using Let's Encrypt as your CA, see [Certificate automation: Let's Encrypt with Certbot on Amazon Linux](#lets-encrypt-alami)\. 

Underlying the host certificate is the key\. As of 2017, [government](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r4.pdf) and [industry](https://cabforum.org/wp-content/uploads/CA-Browser-Forum-BR-1.4.2.pdf) groups recommend using a minimum key \(modulus\) size of 2048 bits for RSA keys intended to protect documents through 2030\. The default modulus size generated by OpenSSL in Amazon Linux is 2048 bits, which means that the existing auto\-generated key is suitable for use in a CA\-signed certificate\. An alternative procedure is described below for those who desire a customized key, for instance, one with a larger modulus or using a different encryption algorithm\. 

These instructions for acquiring a CA\-signed host certificate do not work unless you own a registered and hosted DNS domain\.

**To obtain a CA\-signed certificate**

1.  [Connect to your instance](EC2_GetStarted.md#ec2-connect-to-instance-linux) and navigate to /etc/pki/tls/private/\. This is the directory where the server's private key for TLS is stored\. If you prefer to use your existing host key to generate the CSR, skip to Step 3\.

1. \(Optional\) Generate a new private key\. Here are some examples of key configurations\. Any of the resulting keys work with your web server, but they vary in how \(and how much\) security they implement\.
   + **Example 1:** Create a default RSA host key\. The resulting file, **custom\.key**, is a 2048\-bit RSA private key\.

     ```
     [ec2-user ~]$ sudo openssl genrsa -out custom.key
     ```
   + **Example 2:** Create a stronger RSA key with a bigger modulus\. The resulting file, **custom\.key**, is a 4096\-bit RSA private key\.

     ```
     [ec2-user ~]$ sudo openssl genrsa -out custom.key 4096
     ```
   + **Example 3:** Create a 4096\-bit encrypted RSA key with password protection\. The resulting file, **custom\.key**, is a 4096\-bit RSA private key encrypted with the AES\-128 cipher\.
**Important**  
Encrypting the key provides greater security, but because an encrypted key requires a password, services depending on it cannot be auto\-started\. Each time you use this key, you must supply the password \(in the preceding example, "abcde12345"\) over an SSH connection\.

     ```
     [ec2-user ~]$ sudo openssl genrsa -aes128 -passout pass:abcde12345 -out custom.key 4096
     ```
   + **Example 4:** Create a key using a non\-RSA cipher\. RSA cryptography can be relatively slow because of the size of its public keys, which are based on the product of two large prime numbers\. However, it is possible to create keys for TLS that use non\-RSA ciphers\. Keys based on the mathematics of elliptic curves are smaller and computationally faster when delivering an equivalent level of security\.

     ```
     [ec2-user ~]$ sudo openssl ecparam -name prime256v1 -out custom.key -genkey
     ```

     The result is a 256\-bit elliptic curve private key using prime256v1, a "named curve" that OpenSSL supports\. Its cryptographic strength is slightly greater than a 2048\-bit RSA key, [according to NIST](http://csrc.nist.gov/publications/nistpubs/800-57/sp800-57_part1_rev3_general.pdf)\.
**Note**  
Not all CAs provide the same level of support for elliptic\-curve\-based keys as for RSA keys\.

   Make sure that the new private key has highly restrictive ownership and permissions \(owner=root, group=root, read/write for owner only\)\. The commands would be as follows:

   ```
   [ec2-user ~]$ sudo chown root.root custom.key
   [ec2-user ~]$ sudo chmod 600 custom.key
   [ec2-user ~]$ ls -al custom.key
   ```

   The commands above should yield the following result:

   ```
   -rw------- root root custom.key
   ```

    After you have created and configured a satisfactory key, you can create a CSR\. 

1. Create a CSR using your preferred key; the example below uses **custom\.key**:

   ```
   [ec2-user ~]$ sudo openssl req -new -key custom.key -out csr.pem
   ```

   OpenSSL opens a dialog and prompts you for the information shown in the following table\. All of the fields except **Common Name** are optional for a basic, domain\-validated host certificate\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-ami.html)

   Finally, OpenSSL prompts you for an optional challenge password\. This password applies only to the CSR and to transactions between you and your CA, so follow the CA's recommendations about this and the other optional field, optional company name\. The CSR challenge password has no effect on server operation\.

   The resulting file **csr\.pem** contains your public key, your digital signature of your public key, and the metadata that you entered\.

1. Submit the CSR to a CA\. This usually consists of opening your CSR file in a text editor and copying the contents into a web form\. At this time, you may be asked to supply one or more subject alternate names \(SANs\) to be placed on the certificate\. If **www\.example\.com** is the common name, then **example\.com** would be a good SAN, and vice versa\. A visitor to your site typing in either of these names would see an error\-free connection\. If your CA web form allows it, include the common name in the list of SANs\. Some CAs include it automatically\.

   After your request has been approved, you receive a new host certificate signed by the CA\. You might also be instructed to download an *intermediate certificate* file that contains additional certificates needed to complete the CA's chain of trust\.
**Note**  
Your CA may send you files in multiple formats intended for various purposes\. For this tutorial, you should only use a certificate file in PEM format, which is usually \(but not always\) marked with a `.pem` or `.crt` extension\. If you are uncertain which file to use, open the files with a text editor and find the one containing one or more blocks beginning with the following:  

   ```
   - - - - -BEGIN CERTIFICATE - - - - - 
   ```
The file should also end with the following:  

   ```
   - - - -END CERTIFICATE - - - - -
   ```
You can also test a file at the command line as follows:  

   ```
   [ec2-user certs]$ openssl x509 -in certificate.crt -text
   ```
Verify that these lines appear in the file\. Do not use files ending with `.p7b`, `.p7c`, or similar file extensions\.

1. Place the new CA\-signed certificate and any intermediate certificates in the `/etc/pki/tls/certs` directory\.
**Note**  
There are several ways to upload your custom key to your EC2 instance, but the most straightforward and informative way is to open a text editor \(for example, vi, nano, or notepad\) on both your local computer and your instance, and then copy and paste the file contents between them\. You need root \[sudo\] permissions when performing these operations on the EC2 instance\. This way, you can see immediately if there are any permission or path problems\. Be careful, however, not to add any additional lines while copying the contents, or to change them in any way\.

   From inside the `/etc/pki/tls/certs` directory, use the following commands to verify that the file ownership, group, and permission settings match the highly restrictive Amazon Linux defaults \(owner=root, group=root, read/write for owner only\)\.

   ```
   [ec2-user certs]$ sudo chown root.root custom.crt
   [ec2-user certs]$ sudo chmod 600 custom.crt
   [ec2-user certs]$ ls -al custom.crt
   ```

   The commands above should yield the following result: 

   ```
   -rw------- root root custom.crt
   ```

   The permissions for the intermediate certificate file are less stringent \(owner=root, group=root, owner can write, group can read, world can read\)\. The commands would be: 

   ```
   [ec2-user certs]$ sudo chown root.root intermediate.crt
   [ec2-user certs]$ sudo chmod 644 intermediate.crt
   [ec2-user certs]$ ls -al intermediate.crt
   ```

   The commands above should yield the following result:

   ```
   -rw-r--r-- root root intermediate.crt
   ```

1. If you used a custom key to create your CSR and the resulting host certificate, remove or rename the old key from the `/etc/pki/tls/private/` directory, and then install the new key there\. 
**Note**  
There are several ways to upload your custom key to your EC2 instance, but the most straightforward and informative way is to open a text editor \(vi, nano, notepad, etc\.\) on both your local computer and your instance, and then copy and paste the file contents between them\. You need root \[sudo\] privileges when performing these operations on the EC2 instance\. This way, you can see immediately if there are any permission or path problems\. Be careful, however, not to add any additional lines while copying the contents, or to change them in any way\.

   From inside the `/etc/pki/tls/private` directory, check that the file ownership, group, and permission settings match the highly restrictive Amazon Linux defaults \(owner=root, group=root, read/write for owner only\)\. The commands would be as follows: 

   ```
   [ec2-user private]$ sudo chown root.root custom.key
   [ec2-user private]$ sudo chmod 600 custom.key
   [ec2-user private]$ ls -al custom.key
   ```

   The commands above should yield the following result: 

   ```
   -rw------- root root custom.key
   ```

1. Edit `/etc/httpd/conf.d/ssl.conf` to reflect your new certificate and key files\.

   1. Provide the path and file name of the CA\-signed host certificate in Apache's `SSLCertificateFile` directive:

      ```
      SSLCertificateFile /etc/pki/tls/certs/custom.crt
      ```

   1. If you received an intermediate certificate file \(`intermediate.crt` in this example\), provide its path and file name using Apache's `SSLCACertificateFile` directive:

      ```
      SSLCACertificateFile /etc/pki/tls/certs/intermediate.crt
      ```
**Note**  
Some CAs combine the host certificate and the intermediate certificates in a single file, making this directive unnecessary\. Consult the instructions provided by your CA\.

   1. Provide the path and file name of the private key in Apache's `SSLCertificateKeyFile` directive:

      ```
      SSLCertificateKeyFile /etc/pki/tls/private/custom.key
      ```

1. Save `/etc/httpd/conf.d/ssl.conf` and restart Apache\.

   ```
   [ec2-user ~]$ sudo service httpd restart
   ```

1. Test your server by entering your domain name into a browser URL bar with the prefix `https://`\. Your browser should load the test page over HTTPS without generating errors\.

## Step 3: Test and harden the security configuration<a name="ssl-test-alami"></a>

After your TLS is operational and exposed to the public, you should test how secure it really is\. This is easy to do using online services such as [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html), which performs a free and thorough analysis of your security setup\. Based on the results, you may decide to harden the default security configuration by controlling which protocols you accept, which ciphers you prefer, and which you exclude\. For more information, see [how Qualys formulates its scores](https://github.com/ssllabs/research/wiki/SSL-Server-Rating-Guide)\.

**Important**  
Real\-world testing is crucial to the security of your server\. Small configuration errors may lead to serious security breaches and loss of data\. Because recommended security practices change constantly in response to research and emerging threats, periodic security audits are essential to good server administration\. 

On the [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html) site, type the fully qualified domain name of your server, in the form **www\.example\.com**\. After about two minutes, you receive a grade \(from A to F\) for your site and a detailed breakdown of the findings\. Though the overview shows that the configuration is mostly sound, the detailed report flags several potential problems\. For example:

✗ **The RC4 cipher is supported for use by certain older browsers\.** A cipher is the mathematical core of an encryption algorithm\. RC4, a fast cipher used to encrypt TLS data\-streams, is known to have several [serious weaknesses](http://www.imperva.com/docs/hii_attacking_ssl_when_using_rc4.pdf)\. Unless you have very good reasons to support legacy browsers, you should disable this\.

✗ **Old TLS versions are supported\.** The configuration supports TLS 1\.0 \(already deprecated\) and TLS 1\.1 \(on a path to deprecation\)\. Only TLS 1\.2 has been recommended since 2018\.

**To correct the TLS configuration**

1. Open the configuration file `/etc/httpd/conf.d/ssl.conf` in a text editor and comment out the following lines by typing "\#" at the beginning of each:

   ```
   #SSLProtocol all -SSLv3
   #SSLProxyProtocol all -SSLv3
   ```

1. Add the following directives:

   ```
   SSLProtocol -SSLv2 -SSLv3 -TLSv1 -TLSv1.1 +TLSv1.2
   SSLProxyProtocol -SSLv2 -SSLv3 -TLSv1 -TLSv1.1 +TLSv1.2
   ```

   These directives explicitly disable SSL versions 2 and 3, as well as TLS versions 1\.0 and 1\.1\. The server now refuses to accept encrypted connections with clients using anything except TLS 1\.2\. The verbose wording in the directive communicates more clearly, to a human reader, what the server is configured to do\.
**Note**  
Disabling TLS versions 1\.0 and 1\.1 in this manner blocks a small percentage of outdated web browsers from accessing your site\.

**To modify the list of allowed ciphers**

1. Open the configuration file `/etc/httpd/conf.d/ssl.conf` and find the section with commented\-out examples for configuring **SSLCipherSuite** and **SSLProxyCipherSuite**\.

   ```
   #SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5
   #SSLProxyCipherSuite HIGH:MEDIUM:!aNULL:!MD5
   ```

   Leave these as they are, and below them add the following directives:
**Note**  
Though shown here on several lines for readability, each of these two directives must be on a single line without spaces between the cipher names\.

   ```
   SSLCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:
   ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:
   ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES:!aNULL:!eNULL:!EXPORT:!DES:
   !RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
   
   SSLProxyCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:
   ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:
   ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES:!aNULL:!eNULL:!EXPORT:!DES:
   !RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA
   ```

   These ciphers are a subset of the much longer list of supported ciphers in OpenSSL\. They were selected and ordered according to the following criteria:
   + Support for forward secrecy
   + Strength
   + Speed
   + Specific ciphers before cipher families
   + Allowed ciphers before denied ciphers

   Note that the high\-ranking ciphers have *ECDHE* in their names, for *Elliptic Curve Diffie\-Hellman Ephemeral *; the *ephemeral* indicates forward secrecy\. Also, RC4 is now among the forbidden ciphers near the end\.

   We recommend that you use an explicit list of ciphers instead relying on defaults or terse directives whose content isn't visible\.
**Important**  
The cipher list shown here is just one of many possible lists; for instance, you might want to optimize a list for speed rather than forward secrecy\.   
If you anticipate a need to support older clients, you can allow the DES\-CBC3\-SHA cipher suite\.  
Finally, each update to OpenSSL introduces new ciphers and deprecates old ones\. Keep your EC2 Amazon Linux instance up to date, watch for security announcements from [OpenSSL](https://www.openssl.org/), and be alert to reports of new security exploits in the technical press\. For more information, see [Predefined SSL Security Policies for Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-security-policy-table.html) in the *User Guide for Classic Load Balancers*\.

1. Uncomment the following line by removing the "\#":

   ```
   #SSLHonorCipherOrder on
   ```

   This command forces the server to prefer high\-ranking ciphers, including \(in this case\) those that support forward secrecy\. With this directive turned on, the server tries to establish a strongly secure connection before falling back to allowed ciphers with lesser security\.

1. Restart Apache\. If you test the domain again on [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html), you should see that the RC4 vulnerability is gone\.

## Troubleshooting<a name="troubleshooting-alami"></a>
+ **My Apache webserver won't start unless I supply a password**

  This is expected behavior if you installed an encrypted, password\-protected, private server key\.

  You can remove the encryption and password requirement from the key\. Assuming that you have a private encrypted RSA key called `custom.key` in the default directory, and that the password on it is **abcde12345**, run the following commands on your EC2 instance to generate an unencrypted version of the key\.

  ```
  [ec2-user ~]$ cd /etc/pki/tls/private/
  [ec2-user private]$ sudo cp custom.key custom.key.bak
  [ec2-user private]$ sudo openssl rsa -in custom.key -passin pass:abcde12345 -out custom.key.nocrypt 
  [ec2-user private]$ sudo mv custom.key.nocrypt custom.key
  [ec2-user private]$ sudo chown root.root custom.key
  [ec2-user private]$ sudo chmod 600 custom.key
  [ec2-user private]$ sudo service httpd restart
  ```

  Apache should now start without prompting you for a password\.

## Certificate automation: Let's Encrypt with Certbot on Amazon Linux<a name="lets-encrypt-alami"></a>

The [Let's Encrypt](https://letsencrypt.org/) certificate authority is the centerpiece of the Electronic Frontier Foundation \(EFF\) effort to encrypt the entire internet\. In line with that goal, Let's Encrypt host certificates are designed to be created, validated, installed, and maintained with minimal human intervention\. The automated aspects of certificate management are carried out by an agent running on the web server\. After you install and configure the agent, it communicates securely with Let's Encrypt and performs administrative tasks on Apache and the key management system\. This tutorial uses the free [Certbot](https://certbot.eff.org) agent because it allows you either to supply a customized encryption key as the basis for your certificates, or to allow the agent itself to create a key based on its defaults\. You can also configure Certbot to renew your certificates on a regular basis without human interaction, as described in [To automate Certbot](SSL-on-amazon-linux-2.md#automate_certbot)\. For more information, consult the Certbot [User Guide](https://certbot.eff.org/docs/using.html) or [man page](http://manpages.ubuntu.com/manpages/bionic/en/man1/certbot.1.html)\.

Certbot is not officially supported on Amazon Linux AMI, but is available for download and functions correctly when installed\. We recommend that you make the following backups to protect your data and avoid inconvenience:
+ Before you begin, take a snapshot of your Amazon EBS root volume\. This allows you to restore the original state of your EC2 instance\. For information about creating EBS snapshots, see [Creating Amazon EBS snapshots](ebs-creating-snapshot.md)\.
+ The procedure below requires you to edit your `httpd.conf` file, which controls Apache's operation\. Certbot makes its own automated changes to this and other configuration files\. Make a backup copy of your entire `/etc/httpd` directory in case you need to restore it\.

**To install and run Certbot**

1. Enable the Extra Packages for Enterprise Linux \(EPEL\) repository from the Fedora project on your instance\. Packages from EPEL are required as dependencies when you run the Certbot installation script\. 

   ```
   [ec2-user ~]$ sudo yum-config-manager --enable epel
   ```

1. Download the latest release of Certbot from EFF onto your EC2 instance using the following command\.

   ```
   [ec2-user ~]$ wget https://dl.eff.org/certbot-auto
   ```

1. Make the downloaded file executable\.

   ```
   [ec2-user ~]$ chmod a+x certbot-auto
   ```

1. Run the file with root permissions and the `--debug` flag\.

   ```
   [ec2-user ~]$ sudo ./certbot-auto --debug
   ```

1. At the prompt "Is this ok \[y/d/N\]," type "y" and press Enter\.

1. At the prompt "Enter email address \(used for urgent renewal and security notices\)," type a contact address and press Enter\.

1. Agree to the Let's Encrypt Terms of Service at the prompt\. Type "A" and press Enter to proceed:

   ```
   -------------------------------------------------------------------------------
   Please read the Terms of Service at
   https://letsencrypt.org/documents/LE-SA-v1.1.1-August-1-2016.pdf. You must agree
   in order to register with the ACME server at
   https://acme-v01.api.letsencrypt.org/directory
   -------------------------------------------------------------------------------
   (A)gree/(C)ancel: A
   ```

1. Click through the authorization for EFF put you on their mailing list by typing "Y" or "N" and press Enter\.

1. At the prompt shown below, type your Common Name \(the name of your domain as described above\) and your Subject Alternative Name \(SAN\), separating the two names with a space or a comma\. Then press Enter\. In this example, the names have been provided:

   ```
   No names were found in your configuration files. Please enter in your domain
   name(s) (comma and/or space separated)  (Enter 'c' to cancel):example.com www.example.com
   ```

1. On an Amazon Linux system with a default Apache configuration, you see output similar to the example below, asking about the first name you provided\. Type "1" and press Enter\.

   ```
   Obtaining a new certificate
   Performing the following challenges:
   tls-sni-01 challenge for example.com
   tls-sni-01 challenge for www.example.com
   
   We were unable to find a vhost with a ServerName or Address of example.com.
   Which virtual host would you like to choose?
   (note: conf files with multiple vhosts are not yet supported)
   -------------------------------------------------------------------------------
   1: ssl.conf                       |                       | HTTPS | Enabled
   -------------------------------------------------------------------------------
   Press 1 [enter] to confirm the selection (press 'c' to cancel): 1
   ```

1. Next, Certbot asks about the second name\. Type "1" and press Enter\.

   ```
   We were unable to find a vhost with a ServerName or Address of www.example.com.
   Which virtual host would you like to choose?
   (note: conf files with multiple vhosts are not yet supported)
   -------------------------------------------------------------------------------
   1: ssl.conf                       |                       | HTTPS | Enabled
   -------------------------------------------------------------------------------
   Press 1 [enter] to confirm the selection (press 'c' to cancel): 1
   ```

   At this point, Certbot creates your key and a CSR:

   ```
   Waiting for verification...
   Cleaning up challenges
   Generating key (2048 bits): /etc/letsencrypt/keys/0000_key-certbot.pem
   Creating CSR: /etc/letsencrypt/csr/0000_csr-certbot.pem
   ```

1. Authorize Certbot to create and all needed host certificates\. When prompted for each name, type "1" and press Enter as shown in the example:

   ```
   We were unable to find a vhost with a ServerName or Address of example.com.
   Which virtual host would you like to choose?
   (note: conf files with multiple vhosts are not yet supported)
   -------------------------------------------------------------------------------
   1: ssl.conf                       |                       | HTTPS | Enabled
   -------------------------------------------------------------------------------
   Press 1 [enter] to confirm the selection (press 'c' to cancel): 1
   Deploying Certificate for example.com to VirtualHost /etc/httpd/conf.d/ssl.conf
   
   We were unable to find a vhost with a ServerName or Address of www.example.com.
   Which virtual host would you like to choose?
   (note: conf files with multiple vhosts are not yet supported)
   -------------------------------------------------------------------------------
   1: ssl.conf                       | example.com        | HTTPS | Enabled
   -------------------------------------------------------------------------------
   Press 1 [enter] to confirm the selection (press 'c' to cancel): 1
   Deploying Certificate for www.example.com to VirtualHost /etc/httpd/conf.d/ssl.conf
   ```

1. Choose whether to allow insecure connections to your web server\. If you choose option 2 \(as shown in the example\), all connections to your server will either be encrypted or rejected\.

   ```
   Please choose whether HTTPS access is required or optional. 
   -------------------------------------------------------------------------------
   1: Easy - Allow both HTTP and HTTPS access to these sites
   2: Secure - Make all requests redirect to secure HTTPS access
   -------------------------------------------------------------------------------
   Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
   ```

   Certbot completes the configuration of Apache and reports success and other information:

   ```
   Congratulations! You have successfully enabled https://example.com and
   https://www.example.com
   
   You should test your configuration at:
   https://www.ssllabs.com/ssltest/analyze.html?d=example.com
   https://www.ssllabs.com/ssltest/analyze.html?d=www.example.com
   -------------------------------------------------------------------------------
   
   IMPORTANT NOTES:
    - Congratulations! Your certificate and chain have been saved at
      /etc/letsencrypt/live/example.com/fullchain.pem. Your cert will
      expire on 2017-07-19. To obtain a new or tweaked version of this
      certificate in the future, simply run certbot-auto again with the
      "certonly" option. To non-interactively renew *all* of your
      certificates, run "certbot-auto renew"
   ....
   ```

1. After you complete the installation, test and optimize the security of your server as described in [Step 3: Test and harden the security configuration](SSL-on-amazon-linux-2.md#ssl_test)\. 

Certbot is designed to become an invisible, error\-resistant part of your server system\. By default, it generates host certificates with a short, 90\-day expiration time\. If you have not previously configured your system to call the command automatically, you must re\-run the certbot command manually\. This procedure shows how to automate Certbot by setting up a cron job\.<a name="automate-certbot-alami"></a>

**To configure automated certificate renewal**

1. Open the `/etc/crontab` file in a text editor, such as vim or nano, using sudo\. Alternatively, use sudo crontab \-e\.

1. Add a line similar to the following and save the file\.

   ```
   39      1,13    *       *       *       root    certbot renew --no-self-upgrade
   ```

   Here is an explanation of each component:  
`39 1,13 * * *`  
Schedules a command to be run at 01:39 and 13:39 every day\. The selected values are arbitrary, but the Certbot developers suggest running the command at least twice daily\. This guarantees that any certificate found to be compromised is promptly revoked and replaced\.  
`root`  
The command runs with root privileges\.  
`certbot renew --no-self-upgrade`   
The command to be run\. The renew subcommand causes Certbot to check any previously obtained certificates and to renew those that are approaching expiration\. The `--no-self-upgrade` flag prevents Certbot from upgrading itself without your intervention\.

1. Restart the cron daemon:

   ```
   [ec2-user ~]$ sudo service crond restart
   ```
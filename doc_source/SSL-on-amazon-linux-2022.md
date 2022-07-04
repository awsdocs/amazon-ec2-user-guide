# Tutorial: Configure SSL/TLS on Amazon Linux 2022<a name="SSL-on-amazon-linux-2022"></a>

Secure Sockets Layer/Transport Layer Security \(SSL/TLS\) creates an encrypted channel between a web server and web client that protects data in transit from being eavesdropped on\. This tutorial explains how to add support manually for SSL/TLS on an EC2 instance with Amazon Linux 2022 and Apache web server\. This tutorial assumes that you are not using a load balancer\. If you are using Elastic Load Balancing, you can choose to configure SSL offload on the load balancer, using a certificate from [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/) instead\.

For historical reasons, web encryption is often referred to simply as SSL\. While web browsers still support SSL, its successor protocol TLS is less vulnerable to attack\. Amazon Linux 2022 disables server\-side support for all versions of SSL by default\. [Security standards bodies](https://www.ssl.com/article/deprecating-early-tls/) consider TLS 1\.0 to be unsafe, and both TLS 1\.0 and TLS 1\.1 are on track to be formally [deprecated](https://tools.ietf.org/html/draft-ietf-tls-oldversions-deprecate-03) by the IETF\. This tutorial contains guidance based exclusively on enabling TLS 1\.2\. \(A newer TLS 1\.3 protocol exists, but it is not installed by default on Amazon Linux 2022\.\) For more information about the updated encryption standards, see [RFC 7568](https://tools.ietf.org/html/rfc7568) and [RFC 8446](https://tools.ietf.org/html/rfc8446)\.

This tutorial refers to modern web encryption simply as TLS\.

**Important**  
These procedures are intended for use with Amazon Linux 2022, which is still in Preview phase\. You may access the official AMIs in the AWS Management Console by using the search filters 'Amazon Linux 2022' and 'Owner: Amazon images' on the Community AMI page, or click directly from the [Amazon Linux 2022](https://aws.amazon.com/linux/amazon-linux-2022/) news post\. If you are trying to set up an EC2 instance running a different distribution, or an instance running an old version of Amazon Linux, some procedures in this tutorial might not work\. For the Amazon Linux AMI, see [Tutorial: Configure SSL/TLS with the Amazon Linux AMI](SSL-on-amazon-linux-ami.md)\. For Ubuntu, see the following Ubuntu community documentation: [ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)\. For Red Hat Enterprise Linux, see the following: [Setting up the Apache HTTP Web Server](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_different_types_of_servers/setting-apache-http-server_deploying-different-types-of-servers)\. For other distributions, see their specific documentation\.

**Note**  
Alternatively, you can use AWS Certificate Manager \(ACM\) for AWS Nitro enclaves, which is an enclave application that allows you to use public and private SSL/TLS certificates with your web applications and servers running on Amazon EC2 instances with AWS Nitro Enclaves\. Nitro Enclaves is an Amazon EC2 capability that enables creation of isolated compute environments to protect and securely process highly sensitive data, such as SSL/TLS certificates and private keys\.  
ACM for Nitro Enclaves works with **nginx** running on your Amazon EC2 Linux instance to create private keys, to distribute certificates and private keys, and to manage certificate renewals\.  
To use ACM for Nitro Enclaves, you must use an enclave\-enabled Linux instance\.  
For more information, see [ What is AWS Nitro Enclaves?](https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave.html) and [AWS Certificate Manager for Nitro Enclaves](https://docs.aws.amazon.com/enclaves/latest/user/nitro-enclave-refapp.html) in the *AWS Nitro Enclaves User Guide*\.

**Topics**
+ [Prerequisites](#ssl_prereq_2022)
+ [Step 1: Enable TLS on the server](#ssl_enable)
+ [Step 2: Obtain a CA\-signed certificate](#ssl_cert)
+ [Step 3: Test and harden the security configuration](#ssl_test_2022)
+ [Troubleshoot](#troubleshooting-2022)
+ [Certificate automation: Let's Encrypt with Certbot on Amazon Linux 2022](#letsencrypt-2022)

## Prerequisites<a name="ssl_prereq_2022"></a>

Before you begin this tutorial, complete the following steps:
+ Launch an EBS\-backed Amazon Linux 2022 instance\. For more information, see [Step 1: Launch an instance](EC2_GetStarted.md#ec2-launch-instance)\. 
+ Configure your security groups to allow your instance to accept connections on the following TCP ports: 
  + SSH \(port 22\)
  + HTTP \(port 80\)
  + HTTPS \(port 443\)

  For more information, see [Authorize inbound traffic for your Linux instances](authorizing-access-to-an-instance.md)\.
+ Install the Apache web server\. For step\-by\-step instructions, see [Tutorial: Install a LAMP Web Server on Amazon Linux 2022](ec2-lamp-amazon-linux-2.md)\. Only the httpd package and its dependencies are needed, so you can ignore the instructions involving PHP and MariaDB\.
+ To identify and authenticate websites, the TLS public key infrastructure \(PKI\) relies on the Domain Name System \(DNS\)\. To use your EC2 instance to host a public website, you need to register a domain name for your web server or transfer an existing domain name to your Amazon EC2 host\. Numerous third\-party domain registration and DNS hosting services are available for this, or you can use [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)\. 

## Step 1: Enable TLS on the server<a name="ssl_enable"></a>

This procedure takes you through the process of setting up TLS on Amazon Linux 2022 with a self\-signed digital certificate\.

**Note**  
A self\-signed certificate is acceptable for testing but not production\. If you expose your self\-signed certificate to the internet, visitors to your site are greeted by security warnings\. 

**To enable TLS on a server**

1. [Connect to your instance](EC2_GetStarted.md#ec2-connect-to-instance-linux) and confirm that Apache is running\.

   ```
   [ec2-user ~]$ sudo systemctl is-enabled httpd
   ```

   If the returned value is not "enabled," start Apache and set it to start each time the system boots\.

   ```
   [ec2-user ~]$ sudo systemctl start httpd && sudo systemctl enable httpd
   ```

1. To ensure that all of your software packages are up to date, perform a quick software update on your instance\. This process may take a few minutes, but it is important to make sure that you have the latest security updates and bug fixes\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. If you would like to examine the updates before installing, you can omit this option\.

   ```
   [ec2-user ~]$ sudo yum install openssl mod_ssl
   ```

1. After you enter the following command, you will be taken to a prompt where you can enter information about your site\. 

   ```
   [ec2-user ~]$ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/pki/tls/private/localhost.key -out /etc/pki/tls/certs/localhost.crt
   ```

   This generates a new file `localhost.crt` in the `/etc/pki/tls/certs/` directory\. The specified file name matches the default that is assigned in the SSLCertificateFile directive in `/etc/httpd/conf.d/ssl.conf`\. 

   Your instance now has the following files that you use to configure your secure server and create a certificate for testing:
   +  `/etc/httpd/conf.d/ssl.conf` 

     The configuration file for mod\_ssl\. It contains *directives* telling Apache where to find encryption keys and certificates, the TLS protocol versions to allow, and the encryption ciphers to accept\. This will be your local certificate file: 
   + `/etc/pki/tls/certs/localhost.crt`

   This file contains both a self\-signed certificate and the certificate's private key\. Apache requires the certificate and key to be in PEM format, which consists of Base64\-encoded ASCII characters framed by "BEGIN" and "END" lines, as in the following abbreviated example\.

   ```
   -----BEGIN PRIVATE KEY-----
   MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQD2KKx/8Zk94m1q
   3gQMZF9ZN66Ls19+3tHAgQ5Fpo9KJDhzLjOOCI8u1PTcGmAah5kEitCEc0wzmNeo
   BCl0wYR6G0rGaKtK9Dn7CuIjvubtUysVyQoMVPQ97ldeakHWeRMiEJFXg6kZZ0vr
   GvwnKoMh3DlK44D9dX7IDua2PlYx5+eroA+1Lqf32ZSaAO0bBIMIYTHigwbHMZoT
   ...
   56tE7THvH7vOEf4/iUOsIrEzaMaJ0mqkmY1A70qQGQKBgBF3H1qNRNHuyMcPODFs
   27hDzPDinrquSEvoZIggkDMlh2irTiipJ/GhkvTpoQlv0fK/VXw8vSgeaBuhwJvS
   LXU9HvYq0U6O4FgD3nAyB9hI0BE13r1HjUvbjT7moH+RhnNz6eqqdscCS09VtRAo
   4QQvAqOa8UheYeoXLdWcHaLP
   -----END PRIVATE KEY-----                    
   
   -----BEGIN CERTIFICATE-----
   MIIEazCCA1OgAwIBAgICWxQwDQYJKoZIhvcNAQELBQAwgbExCzAJBgNVBAYTAi0t
   MRIwEAYDVQQIDAlTb21lU3RhdGUxETAPBgNVBAcMCFNvbWVDaXR5MRkwFwYDVQQK
   DBBTb21lT3JnYW5pemF0aW9uMR8wHQYDVQQLDBZTb21lT3JnYW5pemF0aW9uYWxV
   bml0MRkwFwYDVQQDDBBpcC0xNzItMzEtMjAtMjM2MSQwIgYJKoZIhvcNAQkBFhVy
   ...
   z5rRUE/XzxRLBZOoWZpNWTXJkQ3uFYH6s/sBwtHpKKZMzOvDedREjNKAvk4ws6F0
   CuIjvubtUysVyQoMVPQ97ldeakHWeRMiEJFXg6kZZ0vrGvwnKoMh3DlK44D9dlU3
   WanXWehT6FiSZvB4sTEXXJN2jdw8g+sHGnZ8zCOsclknYhHrCVD2vnBlZJKSZvak
   3ZazhBxtQSukFMOnWPP2a0DMMFGYUHOd0BQE8sBJxg==
   -----END CERTIFICATE-----
   ```

   The file names and extensions are a convenience and have no effect on function\. For example, you can call a certificate `cert.crt`, `cert.pem`, or any other file name, so long as the related directive in the `ssl.conf` file uses the same name\.
**Note**  
When you replace the default TLS files with your own customized files, be sure that they are in PEM format\.

1. Restart Apache\.

   ```
   [ec2-user ~]$ sudo systemctl restart httpd
   ```
**Note**  
Make sure that TCP port 443 is accessible on your EC2 instance, as previously described\.

1. Your Apache web server should now support HTTPS \(secure HTTP\) over port 443\. Test it by entering the IP address or fully qualified domain name of your EC2 instance into a browser URL bar with the prefix **https://**\.

   Because you are connecting to a site with a self\-signed, untrusted host certificate, your browser may display a series of security warnings\. Override the warnings and proceed to the site\. 

   If the default Apache test page opens, it means that you have successfully configured TLS on your server\. All data passing between the browser and server is now encrypted\.
**Note**  
To prevent site visitors from encountering warning screens, you must obtain a trusted, CA\-signed certificate that not only encrypts, but also publicly authenticates you as the owner of the site\. 

## Step 2: Obtain a CA\-signed certificate<a name="ssl_cert"></a>

You can use the following process to obtain a CA\-signed certificate:
+ Generate a certificate signing request \(CSR\) from a private key
+ Submit the CSR to a certificate authority \(CA\)
+ Obtain a signed host certificate
+ Configure Apache to use the certificate

A self\-signed TLS X\.509 host certificate is cryptologically identical to a CA\-signed certificate\. The difference is social, not mathematical\. A CA promises, at a minimum, to validate a domain's ownership before issuing a certificate to an applicant\. Each web browser contains a list of CAs trusted by the browser vendor to do this\. An X\.509 certificate consists primarily of a public key that corresponds to your private server key, and a signature by the CA that is cryptographically tied to the public key\. When a browser connects to a web server over HTTPS, the server presents a certificate for the browser to check against its list of trusted CAs\. If the signer is on the list, or accessible through a *chain of trust* consisting of other trusted signers, the browser negotiates a fast encrypted data channel with the server and loads the page\. 

Certificates generally cost money because of the labor involved in validating the requests, so it pays to shop around\. A few CAs offer basic\-level certificates free of charge\. The most notable of these CAs is the [Let's Encrypt](https://letsencrypt.org/) project, which also supports the automation of the certificate creation and renewal process\. For more information about using Let's Encrypt as your CA, see [Certificate automation: Let's Encrypt with Certbot on Amazon Linux 2](SSL-on-amazon-linux-2.md#letsencrypt)\.

If you plan to offer commercial\-grade services, [AWS Certificate Manager](https://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html) is a good option\.

Underlying the host certificate is the key\. As of 2019, [government](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r4.pdf) and [industry](https://cabforum.org/wp-content/uploads/CA-Browser-Forum-BR-1.6.5.pdf) groups recommend using a minimum key \(modulus\) size of 2048 bits for RSA keys intended to protect documents, through 2030\. The default modulus size generated by OpenSSL in Amazon Linux 2022 is 2048 bits, which is suitable for use in a CA\-signed certificate\. In the following procedure, an optional step provided for those who want a customized key, for example, one with a larger modulus or using a different encryption algorithm\.

**Important**  
These instructions for acquiring a CA\-signed host certificate do not work unless you own a registered and hosted DNS domain\.

**To obtain a CA\-signed certificate**

1.  [Connect to your instance](EC2_GetStarted.md#ec2-connect-to-instance-linux) and navigate to /etc/pki/tls/private/\. This is the directory where you store the server's private key for TLS\. If you prefer to use an existing host key to generate the CSR, skip to Step 3\.

1. \(Optional\) Generate a new private key\. Here are some examples of key configurations\. Any of the resulting keys works with your web server, but they vary in the degree and type of security that they implement\.
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

     The result is a 256\-bit elliptic curve private key using prime256v1, a "named curve" that OpenSSL supports\. Its cryptographic strength is slightly greater than a 2048\-bit RSA key, [according to NIST](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r4.pdf)\.
**Note**  
Not all CAs provide the same level of support for elliptic\-curve\-based keys as for RSA keys\.

   Make sure that the new private key has highly restrictive ownership and permissions \(owner=root, group=root, read/write for owner only\)\. The commands would be as shown in the following example\.

   ```
   [ec2-user ~]$ sudo chown root:root custom.key
   [ec2-user ~]$ sudo chmod 600 custom.key
   [ec2-user ~]$ ls -al custom.key
   ```

   The preceding commands yield the following result\.

   ```
   -rw------- root root custom.key
   ```

    After you have created and configured a satisfactory key, you can create a CSR\. 

1. Create a CSR using your preferred key\. The following example uses **custom\.key**\.

   ```
   [ec2-user ~]$ sudo openssl req -new -key custom.key -out csr.pem
   ```

   OpenSSL opens a dialog and prompts you for the information shown in the following table\. All of the fields except **Common Name** are optional for a basic, domain\-validated host certificate\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2022.html)

   Finally, OpenSSL prompts you for an optional challenge password\. This password applies only to the CSR and to transactions between you and your CA, so follow the CA's recommendations about this and the other optional field, optional company name\. The CSR challenge password has no effect on server operation\.

   The resulting file **csr\.pem** contains your public key, your digital signature of your public key, and the metadata that you entered\.

1. Submit the CSR to a CA\. This usually consists of opening your CSR file in a text editor and copying the contents into a web form\. At this time, you may be asked to supply one or more subject alternate names \(SANs\) to be placed on the certificate\. If **www\.example\.com** is the common name, then **example\.com** would be a good SAN, and vice versa\. A visitor to your site entering either of these names would see an error\-free connection\. If your CA web form allows it, include the common name in the list of SANs\. Some CAs include it automatically\.

   After your request has been approved, you receive a new host certificate signed by the CA\. You might also be instructed to download an *intermediate certificate* file that contains additional certificates needed to complete the CA's chain of trust\. 
**Note**  
Your CA might send you files in multiple formats intended for various purposes\. For this tutorial, you should only use a certificate file in PEM format, which is usually \(but not always\) marked with a `.pem` or `.crt` file extension\. If you are uncertain which file to use, open the files with a text editor and find the one containing one or more blocks beginning with the following line\.  

   ```
   - - - - -BEGIN CERTIFICATE - - - - - 
   ```
The file should also end with the following line\.  

   ```
   - - - -END CERTIFICATE - - - - -
   ```
You can also test the file at the command line as shown in the following\.  

   ```
   [ec2-user certs]$ openssl x509 -in certificate.crt -text
   ```
Verify that these lines appear in the file\. Do not use files ending with `.p7b`, `.p7c`, or similar file extensions\.

1. Place the new CA\-signed certificate and any intermediate certificates in the `/etc/pki/tls/certs` directory\.
**Note**  
There are several ways to upload your new certificate to your EC2 instance, but the most straightforward and informative way is to open a text editor \(for example, vi, nano, or notepad\) on both your local computer and your instance, and then copy and paste the file contents between them\. You need root \[sudo\] permissions when performing these operations on the EC2 instance\. This way, you can see immediately if there are any permission or path problems\. Be careful, however, not to add any additional lines while copying the contents, or to change them in any way\. 

   From inside the `/etc/pki/tls/certs` directory, check that the file ownership, group, and permission settings match the highly restrictive Amazon Linux 2022 defaults \(owner=root, group=root, read/write for owner only\)\. The following example shows the commands to use\. 

   ```
   [ec2-user certs]$ sudo chown root:root custom.crt
   [ec2-user certs]$ sudo chmod 600 custom.crt
   [ec2-user certs]$ ls -al custom.crt
   ```

   These commands should yield the following result\. 

   ```
   -rw------- root root custom.crt
   ```

   The permissions for the intermediate certificate file are less stringent \(owner=root, group=root, owner can write, group can read, world can read\)\. The following example shows the commands to use\. 

   ```
   [ec2-user certs]$ sudo chown root:root intermediate.crt
   [ec2-user certs]$ sudo chmod 644 intermediate.crt
   [ec2-user certs]$ ls -al intermediate.crt
   ```

   These commands should yield the following result\.

   ```
   -rw-r--r-- root root intermediate.crt
   ```

1. Place the private key that you used to create the CSR in the `/etc/pki/tls/private/` directory\. 
**Note**  
There are several ways to upload your custom key to your EC2 instance, but the most straightforward and informative way is to open a text editor \(for example, vi, nano, or notepad\) on both your local computer and your instance, and then copy and paste the file contents between them\. You need root \[sudo\] permissions when performing these operations on the EC2 instance\. This way, you can see immediately if there are any permission or path problems\. Be careful, however, not to add any additional lines while copying the contents, or to change them in any way\.

   From inside the `/etc/pki/tls/private` directory, use the following commands to verify that the file ownership, group, and permission settings match the highly restrictive Amazon Linux 2022 defaults \(owner=root, group=root, read/write for owner only\)\.

   ```
   [ec2-user private]$ sudo chown root:root custom.key
   [ec2-user private]$ sudo chmod 600 custom.key
   [ec2-user private]$ ls -al custom.key
   ```

   These commands should yield the following result\.

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
Some CAs combine the host certificate and the intermediate certificates in a single file, making the `SSLCACertificateFile` directive unnecessary\. Consult the instructions provided by your CA\.

   1. Provide the path and file name of the private key \(`custom.key` in this example\) in Apache's `SSLCertificateKeyFile` directive:

      ```
      SSLCertificateKeyFile /etc/pki/tls/private/custom.key
      ```

1. Save `/etc/httpd/conf.d/ssl.conf` and restart Apache\.

   ```
   [ec2-user ~]$ sudo systemctl restart httpd
   ```

1. Test your server by entering your domain name into a browser URL bar with the prefix `https://`\. Your browser should load the test page over HTTPS without generating errors\.

## Step 3: Test and harden the security configuration<a name="ssl_test_2022"></a>

After your TLS is operational and exposed to the public, you should test how secure it really is\. This is easy to do using online services such as [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html), which performs a free and thorough analysis of your security setup\. Based on the results, you may decide to harden the default security configuration by controlling which protocols you accept, which ciphers you prefer, and which you exclude\. For more information, see [how Qualys formulates its scores](https://github.com/ssllabs/research/wiki/SSL-Server-Rating-Guide)\.

**Important**  
Real\-world testing is crucial to the security of your server\. Small configuration errors may lead to serious security breaches and loss of data\. Because recommended security practices change constantly in response to research and emerging threats, periodic security audits are essential to good server administration\. 

On the [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html) site, enter the fully qualified domain name of your server, in the form **www\.example\.com**\. After about two minutes, you receive a grade \(from A to F\) for your site and a detailed breakdown of the findings\. The following table summarizes the report for a domain with settings identical to the default Apache configuration on Amazon Linux 2022, and with a default Certbot certificate\. 


|  |  | 
| --- |--- |
| Overall rating | B | 
| Certificate | 100% | 
| Protocol support | 95% | 
| Key exchange | 70% | 
| Cipher strength | 90% | 

Though the overview shows that the configuration is mostly sound, the detailed report flags several potential problems, listed here in order of severity:

✗ **The RC4 cipher is supported for use by certain older browsers\.** A cipher is the mathematical core of an encryption algorithm\. RC4, a fast cipher used to encrypt TLS data\-streams, is known to have several [serious weaknesses](http://www.imperva.com/docs/hii_attacking_ssl_when_using_rc4.pdf)\. Unless you have very good reasons to support legacy browsers, you should disable this\.

✗ **Old TLS versions are supported\.** The configuration supports TLS 1\.0 \(already deprecated\) and TLS 1\.1 \(on a path to deprecation\)\. Only TLS 1\.2 has been recommended since 2018\.

✗ **Forward secrecy is not fully supported\.** [Forward secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) is a feature of algorithms that encrypt using temporary \(ephemeral\) session keys derived from the private key\. This means in practice that attackers cannot decrypt HTTPS data even if they possess a web server's long\-term private key\.

**To correct and future\-proof the TLS configuration**

1. Open the configuration file `/etc/httpd/conf.d/ssl.conf` in a text editor and comment out the following line by entering "\#" at the beginning of the line\.

   ```
   #SSLProtocol all -SSLv3
   ```

1. Add the following directive:

   ```
   #SSLProtocol all -SSLv3
   SSLProtocol -SSLv2 -SSLv3 -TLSv1 -TLSv1.1 +TLSv1.2
   ```

   This directive explicitly disables SSL versions 2 and 3, as well as TLS versions 1\.0 and 1\.1\. The server now refuses to accept encrypted connections with clients using anything except TLS 1\.2\. The verbose wording in the directive conveys more clearly, to a human reader, what the server is configured to do\.
**Note**  
Disabling TLS versions 1\.0 and 1\.1 in this manner blocks a small percentage of outdated web browsers from accessing your site\.

**To modify the list of allowed ciphers**

1. In the configuration file `/etc/httpd/conf.d/ssl.conf`, find the section with the **SSLCipherSuite** directive and comment out the existing line by entering "\#" at the beginning of the line\.

   ```
   #SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5
   ```

1. Specify explicit cipher suites and a cipher order that prioritizes forward secrecy and avoids insecure ciphers\. The `SSLCipherSuite` directive used here is based on output from the [Mozilla SSL Configuration Generator](https://mozilla.github.io/server-side-tls/ssl-config-generator/), which tailors a TLS configuration to the specific software running on your server\. \(For more information, see Mozilla's useful resource [Security/Server Side TLS](https://wiki.mozilla.org/Security/Server_Side_TLS)\.\) First determine your Apache and OpenSSL versions by using the output from the following commands\.

   ```
   [ec2-user ~]$ yum list installed | grep httpd
   
   [ec2-user ~]$ yum list installed | grep openssl
   ```

   For example, if the returned information is Apache 2\.4\.34 and OpenSSL 1\.0\.2, we enter this into the generator\. If you choose the "modern" compatibility model, this creates an `SSLCipherSuite` directive that aggressively enforces security but still works for most browsers\. If your software doesn't support the modern configuration, you can update your software or choose the "intermediate" configuration instead\.

   ```
   SSLCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:
   ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:
   ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256
   ```

   The selected ciphers have *ECDHE* in their names, an abbreviation for *Elliptic Curve Diffie\-Hellman Ephemeral *\. The term *ephemeral* indicates forward secrecy\. As a by\-product, these ciphers do not support RC4\.

   We recommend that you use an explicit list of ciphers instead of relying on defaults or terse directives whose content isn't visible\.

   Copy the generated directive into `/etc/httpd/conf.d/ssl.conf`\.
**Note**  
Though shown here on several lines for readability, the directive must be on a single line when copied to `/etc/httpd/conf.d/ssl.conf`, with only a colon \(no spaces\) between cipher names\.

1. Finally, uncomment the following line by removing the "\#" at the beginning of the line\.

   ```
   #SSLHonorCipherOrder on
   ```

   This directive forces the server to prefer high\-ranking ciphers, including \(in this case\) those that support forward secrecy\. With this directive turned on, the server tries to establish a strong secure connection before falling back to allowed ciphers with lesser security\.

After completing both of these procedures, save the changes to `/etc/httpd/conf.d/ssl.conf` and restart Apache\.

If you test the domain again on [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html), you should see that the RC4 vulnerability and other warnings are gone and the summary looks something like the following\.


|  |  | 
| --- |--- |
| Overall rating | A | 
| Certificate | 100% | 
| Protocol support | 100% | 
| Key exchange | 90% | 
| Cipher strength | 90% | 

Each update to OpenSSL introduces new ciphers and removes support for old ones\. Keep your EC2 Amazon Linux 2022 instance up\-to\-date, watch for security announcements from [OpenSSL](https://www.openssl.org/), and be alert to reports of new security exploits in the technical press\.

## Troubleshoot<a name="troubleshooting-2022"></a>
+ **My Apache webserver doesn't start unless I enter a password**

  This is expected behavior if you installed an encrypted, password\-protected, private server key\.

  You can remove the encryption and password requirement from the key\. Assuming that you have a private encrypted RSA key called `custom.key` in the default directory, and that the password on it is **abcde12345**, run the following commands on your EC2 instance to generate an unencrypted version of the key\.

  ```
  [ec2-user ~]$ cd /etc/pki/tls/private/
  [ec2-user private]$ sudo cp custom.key custom.key.bak
  [ec2-user private]$ sudo openssl rsa -in custom.key -passin pass:abcde12345 -out custom.key.nocrypt 
  [ec2-user private]$ sudo mv custom.key.nocrypt custom.key
  [ec2-user private]$ sudo chown root:root custom.key
  [ec2-user private]$ sudo chmod 600 custom.key
  [ec2-user private]$ sudo systemctl restart httpd
  ```

  Apache should now start without prompting you for a password\.
+  **I get errors when I run sudo dnf install \-y mod\_ssl\.**

  When you are installing the required packages for SSL, you may see errors similar to the following\.

  ```
  Error: httpd24-tools conflicts with httpd-tools-2.2.34-1.16.amzn1.x86_64
  Error: httpd24 conflicts with httpd-2.2.34-1.16.amzn1.x86_64
  ```

  This typically means that your EC2 instance is not running Amazon Linux 2022\. This tutorial only supports instances freshly created from an official Amazon Linux 2022 AMI\.

## Certificate automation: Let's Encrypt with Certbot on Amazon Linux 2022<a name="letsencrypt-2022"></a>

**Warning**  
 Let's Encrypt cross\-signed DST Root CA X3 certificate expired on **Sept 30th, 2021**\. This can cause Let's Encrypt connections to fail with OpenSSL 1\.0\.x on CentOS/RHEL7 and Amazon Linux\. Remediation steps can be found [https://aws.amazon.com/premiumsupport/knowledge-center/ec2-expired-certificate/](https://aws.amazon.com/premiumsupport/knowledge-center/ec2-expired-certificate/), or you can follow one of the manual workarounds found on the [https://www.openssl.org/blog/blog/2021/09/13/LetsEncryptRootCertExpire](https://www.openssl.org/blog/blog/2021/09/13/LetsEncryptRootCertExpire)\. 

**Important**  
These instructions for acquiring a Let's Encrypt host certificate do not work unless you own a registered and hosted DNS domain\. These instructions do not work with the public DNS hostnames assigned by AWS\.

The [Let's Encrypt](https://letsencrypt.org/) certificate authority is the centerpiece of an effort by the Electronic Frontier Foundation \(EFF\) to encrypt the entire internet\. In line with that goal, Let's Encrypt host certificates are designed to be created, validated, installed, and maintained with minimal human intervention\. The automated aspects of certificate management are carried out by a software agent running on your web server\. After you install and configure the agent, it communicates securely with Let's Encrypt and performs administrative tasks on Apache and the key management system\. This tutorial uses the free [Certbot](https://certbot.eff.org) agent because it allows you either to supply a customized encryption key as the basis for your certificates, or to allow the agent itself to create a key based on its defaults\. You can also configure Certbot to renew your certificates on a regular basis without human interaction, as described in [To automate Certbot](#automate_certbot_2022)\. For more information, consult the Certbot [User Guide](https://certbot.eff.org/docs/using.html) and [man page](http://manpages.ubuntu.com/manpages/bionic/en/man1/certbot.1.html)\. 

Certbot is not officially supported on Amazon Linux 2022, but is available for download and functions correctly when installed\. We recommend that you make the following backups to protect your data and avoid inconvenience:
+ Before you begin, take a snapshot of your Amazon EBS root volume\. This allows you to restore the original state of your EC2 instance\. For information about creating EBS snapshots, see [Create Amazon EBS snapshots](ebs-creating-snapshot.md)\.
+ The procedure below requires you to edit your `httpd.conf` file, which controls Apache's operation\. Certbot makes its own automated changes to this and other configuration files\. Make a backup copy of your entire `/etc/httpd` directory in case you need to restore it\.

### Prepare to install<a name="prepare-2022"></a>

Complete the following procedures before you install Certbot\.

1. Download the Extra Packages for Enterprise Linux \(EPEL\) 7 repository packages\. These are required to supply dependencies needed by Certbot\. 

   1. Navigate to your home directory \(`/home/ec2-user`\)\. Download and install these packages using the following command\.

      ```
      [ec2-user ~]$ sudo dnf install -y python3 augeas-libs pip
      ```

   1. Execute the following instructions on the command line on the machine to set up a Python virtual environment\.

      ```
      sudo python3 -m venv /opt/certbot/
      ```

   1. Install the latest version of pip\.

      ```
      [ec2-user ~]$ xudo /opt/certbot/bin/pip install --upgrade pip
      ```

      Install certbot\.

      ```
      [ec2-user ~]$ sudo /opt/certbot/bin/pip install certbot
      ```

      A a symbolic link\.

      ```
      [ec2-user ~]$ sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
      ```

1. You will need to stop http before the next step\.

   ```
   sudo systemctl stop httpd
   ```

   If you cannot stop your web server, alternatively you can use the following command\.

   ```
   [ec2-user ~]$ sudo certbot certonly --webroot
   ```

### Run Certbot<a name="install-certbot-2022"></a>

This procedure is based on the EFF documentation for installing Certbot on [ Fedora](https://certbot.eff.org/docs/install.html#alternate-installation-methods) and on [RHEL 7](https://certbot.eff.org/#centosrhel7-apache)\. It describes the default use of Certbot, resulting in a certificate based on a 2048\-bit RSA key\.

1. Run Certbot\.

   ```
   [ec2-user ~]$ sudo certbot certonly --standalone
   ```

1. At the prompt "Enter email address \(used for urgent renewal and security notices\)," enter a contact address and press Enter\.

1. Agree to the Let's Encrypt Terms of Service at the prompt\. Enter "A" and press Enter to proceed\.

   ```
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Please read the Terms of Service at
   https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
   agree in order to register with the ACME server at
   https://acme-v02.api.letsencrypt.org/directory
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   (A)gree/(C)ancel: A
   ```

1. At the authorization for EFF to put you on their mailing list, enter "Y" or "N" and press Enter\.

1. Certbot displays the Common Name and Subject Alternative Name \(SAN\) that you provided in the VirtualHost block\.

   ```
   Which names would you like to activate HTTPS for?
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   1: example.com
   2: www.example.com
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Select the appropriate numbers separated by commas and/or spaces, or leave input
   blank to select all options shown (Enter 'c' to cancel):
   ```

   Leave the input blank and press Enter\. 

1. Certbot displays the following output as it creates certificates and configures Apache\. It then prompts you about redirecting HTTP queries to HTTPS\.

   ```
   Obtaining a new certificate
   Performing the following challenges:
   http-01 challenge for example.com
   http-01 challenge for www.example.com
   Waiting for verification...
   Cleaning up challenges
   Created an SSL vhost at /etc/httpd/conf/httpd-le-ssl.conf
   Deploying Certificate for example.com to VirtualHost /etc/httpd/conf/httpd-le-ssl.conf
   Enabling site /etc/httpd/conf/httpd-le-ssl.conf by adding Include to root configuration
   Deploying Certificate for www.example.com to VirtualHost /etc/httpd/conf/httpd-le-ssl.conf
   
   Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   1: No redirect - Make no further changes to the webserver configuration.
   2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
   new sites, or if you're confident your site works on HTTPS. You can undo this
   change by editing your web server's configuration.
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Select the appropriate number [1-2] then [enter] (press 'c' to cancel):
   ```

   To allow visitors to connect to your server via unencrypted HTTP, enter "1"\. If you want to accept only encrypted connections via HTTPS, enter "2"\. Press Enter to submit your choice\.

1. Certbot completes the configuration of Apache and reports success and other information\.

   ```
   Congratulations! You have successfully enabled https://example.com and
   https://www.example.com
   
   You should test your configuration at:
   https://www.ssllabs.com/ssltest/analyze.html?d=example.com
   https://www.ssllabs.com/ssltest/analyze.html?d=www.example.com
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   
   IMPORTANT NOTES:
    - Congratulations! Your certificate and chain have been saved at:
      /etc/letsencrypt/live/certbot.oneeyedman.net/fullchain.pem
      Your key file has been saved at:
      /etc/letsencrypt/live/certbot.oneeyedman.net/privkey.pem
      Your cert will expire on 2019-08-01. To obtain a new or tweaked
      version of this certificate in the future, simply run certbot again
      with the "certonly" option. To non-interactively renew *all* of
      your certificates, run "certbot renew"
    - Your account credentials have been saved in your Certbot
      configuration directory at /etc/letsencrypt. You should make a
      secure backup of this folder now. This configuration directory will
      also contain certificates and private keys obtained by Certbot so
      making regular backups of this folder is ideal.
   ```

1. After you complete the installation, test and optimize the security of your server as described in [Step 3: Test and harden the security configuration](#ssl_test_2022)\. 

### Configure automated certificate renewal<a name="automate-2022"></a>

Certbot is designed to become an invisible, error\-resistant part of your server system\. By default, it generates host certificates with a short, 90\-day expiration time\. If you have not configured your system to call the command automatically, you must re\-run the certbot command manually before expiration\. This procedure shows how to automate Certbot by setting up a cron job\.<a name="automate_certbot_2022"></a>

**To automate Certbot**

1. Open the `/etc/crontab` file in a text editor, such as vim or nano, using sudo\. Alternatively, use sudo crontab \-e\.

1. Add a line similar to the following and save the file\.

   ```
   39      1,13    *       *       *       root    certbot renew --no-self-upgrade
   ```

   Here is an explanation of each component:  
`39 1,13 * * *`  
Schedules a command to be run at 01:39 and 13:39 every day\. The selected values are arbitrary, but the Certbot developers suggest running the command at least twice daily\. This guarantees that any certificate found to be compromised is promptly revoked and replaced\.  
`root`  
The command runs with root permissions\.  
`certbot renew --no-self-upgrade`   
The command to be run\. The renew subcommand causes Certbot to check any previously obtained certificates and to renew those that are approaching expiration\. The `--no-self-upgrade` flag prevents Certbot from upgrading itself without your intervention\.

1. Restart the cron daemon\.

   ```
   [ec2-user ~]$ sudo systemctl restart crond
   ```
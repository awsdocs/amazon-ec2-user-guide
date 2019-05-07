# Tutorial: Configure Apache Web Server on Amazon Linux 2 to Use SSL/TLS<a name="SSL-on-an-instance"></a>

Secure Sockets Layer/Transport Layer Security \(SSL/TLS\) creates an encrypted channel between a web server and web client that protects data in transit from being eavesdropped on\. This tutorial explains how to add support manually for SSL/TLS on a single instance of Amazon Linux 2 running Apache web server\. If you plan to offer commercial\-grade services, the [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/), not discussed here, is a good option\.

For historical reasons, web encryption is often referred to simply as SSL\. While web browsers still support SSL, its successor protocol TLS is less vulnerable to attack\. Amazon Linux 2 disables server\-side support for all versions of SSL by default\. [Security standards bodies](https://www.ssl.com/article/deprecating-early-tls/) consider TLS 1\.0 to be unsafe, and both TLS 1\.0 and TLS 1\.1 are on track to be formally [deprecated](https://tools.ietf.org/html/draft-ietf-tls-oldversions-deprecate-03) by the IETF\. This tutorial contains guidance based exclusively on enabling TLS 1\.2\. \(A newer TLS 1\.3 protocol exists in draft form, but is not yet supported on Amazon Linux 2 \.\) For more information about the updated encryption standards, see [RFC 7568](https://tools.ietf.org/html/rfc7568) and [RFC 8446](https://tools.ietf.org/html/rfc8446)\.

In the following procedures, this tutorial will refer to modern Web encryption simply as TLS\.

**Important**  
These procedures are intended for use with Amazon Linux 2\. We also assume that you are starting with a fresh EC2 instance\. If you are trying to set up a LAMP web server on a different distribution, or if you are re\-purposing an older, existing instance, some procedures in this tutorial might not work for you\. For information about LAMP web servers on Ubuntu, see the Ubuntu community documentation [ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP) topic\. For information about Red Hat Enterprise Linux, see the Customer Portal topic [Web Servers](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/ch-Web_Servers.html)\.  
The version of this tutorial for use with Amazon Linux AMI is no longer maintained, but you can find it on the [Internet Archive](https://web.archive.org/web/20171208004937/https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-an-instance.html)\.

**Topics**
+ [Prerequisites](#ssl_prereq)
+ [Step 1: Enable TLS on the Server](#ssl_enable)
+ [Step 2: Obtain a CA\-signed Certificate](#ssl_certificate)
+ [Step 3: Test and Harden the Security Configuration](#ssl_test)
+ [Troubleshooting](#troubleshooting)
+ [Appendix: Let's Encrypt with Certbot on Amazon Linux 2](#letsencrypt)

## Prerequisites<a name="ssl_prereq"></a>

Before you begin this tutorial, complete the following steps:
+ Launch an EBS\-backed Amazon Linux 2 instance\. For more information, see [Step 1: Launch an Instance](EC2_GetStarted.md#ec2-launch-instance)\. 
+ Configure your security groups to allow your instance to accept connections on the following TCP ports: 
  + SSH \(port 22\)
  + HTTP \(port 80\)
  + HTTPS \(port 443\)

  For more information, see [Authorizing Inbound Traffic for Your Linux Instances](authorizing-access-to-an-instance.md)\.
+ Install the Apache web server\. For step\-by\-step instructions, see [Tutorial: Install a LAMP Web Server on Amazon Linux 2](ec2-lamp-amazon-linux-2.md)\. Only the httpd package and its dependencies are needed, so you can ignore the instructions involving PHP and MariaDB\.
+ To identify and authenticate websites, the TLS public key infrastructure \(PKI\) relies on the Domain Name System \(DNS\)\. To use your EC2 instance to host a public website, you need to register a domain name for your web server or transfer an existing domain name to your Amazon EC2 host\. Numerous third\-party domain registration and DNS hosting services are available for this, or you may use [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)\. 
**Important**  
This tutorial's instructions for acquiring CA\-signed host certificates will not work unless you own a registered and hosted DNS domain\.

## Step 1: Enable TLS on the Server<a name="ssl_enable"></a>

This procedure takes you through the process of setting up TLS on Amazon Linux 2 with a self\-signed digital certificate\.

**Note**  
A self\-signed certificate is acceptable for testing but not production\. If you expose your self\-signed certificate to the internet, visitors to your site are greeted by security warnings\. 

**To enable TLS on a server**

1. [Connect to your instance](EC2_GetStarted.md#ec2-connect-to-instance-linux) and confirm that Apache is running\.

   ```
   [ec2-user ~]$ sudo systemctl is-enabled httpd
   ```

   If the returned value is not "enabled," start Apache and set it to start each time the system boots:

   ```
   [ec2-user ~]$ sudo systemctl start httpd && sudo systemctl enable httpd
   ```

1. To ensure that all of your software packages are up\-to\-date, perform a quick software update on your instance\. This process may take a few minutes, but it is important to make sure that you have the latest security updates and bug fixes\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. If you would like to examine the updates before installing, you can omit this option\.

   ```
   [ec2-user ~]$ sudo yum update -y
   ```

1. Now that your instance is current, add TLS support by installing the Apache module `mod_ssl`:

   ```
   [ec2-user ~]$ sudo yum install -y mod_ssl
   ```

   Your instance now has the following files that you will use to configure your secure server and create a certificate for testing:
   +  `/etc/httpd/conf.d/ssl.conf` 

     The configuration file for mod\_ssl\. It contains *directives* telling Apache where to find encryption keys and certificates, the TLS protocol versions to allow, and the encryption ciphers to accept\. 
   + `/etc/pki/tls/certs/make-dummy-cert`

     A script to generate a self\-signed X\.509 certificate and private key for your server host\. This certificate is useful for testing that Apache is properly set up to use TLS\. Because it offers no proof of identity, it should not be used in production, and will trigger warnings in Web browsers\.

1. Run the script to generate a self\-signed dummy certificate and key for testing:

   ```
   [ec2-user ~]$ sudo /etc/pki/tls/certs/make-dummy-cert localhost.crt
   ```

   This generates a new file `localhost.crt` in the `/etc/pki/tls/certs/` directory\. The specified file name matches the default assigned in the SSLCertificateFile directive in `/etc/httpd/conf.d/ssl.conf`\. 

   This file contains both a self\-signed certificate and the certificate's private key\. Apache requires the certificate and key to be in PEM format, which consists of Base64\-encoded ASCII characters framed by "BEGIN" and "END" lines, as in this abbreviated example:

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
Make sure the TCP port 443 is accessible on your EC2 instance, as described above\.

1. Your Apache web server should now support HTTPS \(secure HTTP\) over port 443\. Test it by typing the IP address or fully qualified domain name of your EC2 instance into a browser URL bar with the prefix **https://**\. 

   Because you are connecting to a site with a self\-signed, untrusted host certificate, your browser may display a series of security warnings\. Override the warnings and proceed to the site\. 

   If the default Apache test page opens, it means that you have successfully configured TLS on your server\. All data passing between the browser and server is now encrypted\.
**Note**  
To prevent site visitors from encountering warning screens, you need to obtain a trusted, CA\-signed certificate that not only encrypts, but also publicly authenticates you as the owner of the site\. 

## Step 2: Obtain a CA\-signed Certificate<a name="ssl_certificate"></a>

This section describes the process of generating a certificate signing request \(CSR\) from a private key, submitting the CSR to a certificate authority \(CA\), obtaining a signed host certificate, and configuring Apache to use it\.

A self\-signed TLS X\.509 host certificate is cryptologically identical to a CA\-signed certificate\. The difference is social, not mathematical\. A CA promises to validate, at a minimum, a domain's ownership before issuing a certificate to an applicant\. Each web browser contains a list of CAs trusted by the browser vendor to do this\. An X\.509 certificate consists primarily of a public key that corresponds to your private server key, and a signature by the CA that is cryptographically tied to the public key\. When a browser connects to a web server over HTTPS, the server presents a certificate for the browser to check against its list of trusted CAs\. If the signer is on the list, or accessible through a *chain of trust *consisting of other trusted signers, the browser negotiates a fast encrypted data channel with the server and loads the page\. 

Certificates generally cost money because of the labor involved in validating the requests, so it pays to shop around\. A list of well\-known CAs can be found at [dmoztools\.net](http://dmoztools.net/Computers/Security/Public_Key_Infrastructure/PKIX/Tools_and_Services/Third_Party_Certificate_Authorities/)\. A few CAs offer basic\-level certificates free of charge\. The most notable of these is the [Let's Encrypt](https://letsencrypt.org/) project, which also supports the automation of the certificate creation and renewal process\. For more information about using Let's Encrypt as your CA, see [Appendix: Let's Encrypt with Certbot on Amazon Linux 2](#letsencrypt) \. 

Underlying the host certificate is the key\. As of 2019, [government](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r4.pdf) and [industry](https://cabforum.org/wp-content/uploads/CA-Browser-Forum-BR-1.6.5.pdf) groups recommend using a minimum key \(modulus\) size of 2048 bits for RSA keys intended to protect documents through 2030\. The default modulus size generated by OpenSSL in Amazon Linux 2 is 2048 bits, which is suitable for use in a CA\-signed certificate\. An alternative procedure is described below for those who desire a customized key, for instance one with a larger modulus or using a different encryption algorithm\. 

**To obtain a CA\-signed certificate**

1.  [Connect to your instance](EC2_GetStarted.md#ec2-connect-to-instance-linux) and navigate to /etc/pki/tls/private/\. This is the directory where you will store the server's private key for TLS\. If you prefer to use an existing host key to generate the CSR, skip to Step 3\.

1. Generate a new private key\. Here are some sample key configurations\. Any of the resulting keys work with your web server, but they vary in the degree and type of security that they implement\.

   1. As a starting point, here is the command to create a default RSA host key:

      ```
      [ec2-user ~]$ sudo openssl genrsa -out custom.key
      ```

      The resulting file, **custom\.key**, is a default\-sized 2048\-bit RSA private key\. 

   1. To create a stronger RSA key with a bigger modulus, use the following command: 

      ```
      [ec2-user ~]$ sudo openssl genrsa -out custom.key 4096
      ```

      The resulting file, **custom\.key**, is a 4096\-\-bit RSA private key\. 

   1. To create a 4096\-bit encrypted RSA key with password protection, use the following command: 

      ```
      [ec2-user ~]$ sudo openssl genrsa -aes128 -passout pass:abcde12345 -out custom.key 4096
      ```

      This results in a 4096\-bit RSA private key that has been encrypted with the AES\-128 cipher\.
**Important**  
Encrypting the key provides greater security, but because an encrypted key requires a password, services depending on it cannot be auto\-started\. Each time you use this key, you need to supply the password "abcde12345" over an SSH connection\. 

   1. RSA cryptography can be relatively slow, because its security relies on the difficulty of factoring the product of two large two prime numbers\. However, it is possible to create keys for TLS that use non\-RSA ciphers\. Keys based on the mathematics of elliptic curves are smaller and computationally faster when delivering an equivalent level of security\. Here is an example: 

      ```
      [ec2-user ~]$ sudo openssl ecparam -name prime256v1 -out custom.key -genkey
      ```

       The output in this case is a 256\-bit elliptic curve private key using prime256v1, a "named curve" that OpenSSL supports\. Its cryptographic strength is slightly greater than a 2048\-bit RSA key, [according to NIST](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r4.pdf)\.
**Note**  
Not all CAs provide the same level of support for elliptic\-curve\-based keys as for RSA keys\.

   Make sure that the new private key has highly restrictive ownership and permissions \(owner=root, group=root, read/write for owner only\)\. The commands would be as follows:

   ```
   [ec2-user ~]$ sudo chown root:root custom.key
   [ec2-user ~]$ sudo chmod 600 custom.key
   [ec2-user ~]$ ls -al custom.key
   ```

   The commands above should yield the following result:

   ```
   -rw------- root root custom.key
   ```

    After you have created and configured a satisfactory key, you can create a CSR\. 

1. Create a CSR using your preferred key\. The example below uses **custom\.key**:

   ```
   [ec2-user ~]$ sudo openssl req -new -key custom.key -out csr.pem
   ```

   OpenSSL opens a dialog and prompts you for information shown in the table below\. All of the fields except **Common Name** are optional for a basic, domain\-validated host certificate\.   
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-an-instance.html)

   Finally, OpenSSL prompts you for an optional challenge password\. This password applies only to the CSR and to transactions between you and your CA, so follow the CA's recommendations about this and the other optional field, optional company name\. The CSR challenge password has no effect on server operation\.

   The resulting file **csr\.pem** contains your public key, your digital signature of your public key, and the metadata that you entered\.

1. Submit the CSR to a CA\. This usually consists of opening your CSR file in a text editor and copying the contents into a web form\. At this time, you may be asked to supply one or more subject alternate names \(SANs\) to be placed on the certificate\. If **www\.example\.com** is the common name, then **example\.com** would be a good SAN, and vice versa\. A visitor to your site typing in either of these names would see an error\-free connection\. If your CA web form allows it, include the common name in the list of SANs\. Some CAs include it automatically\.

   After your request has been approved, you will receive a new host certificate signed by the CA\. You may also be instructed to download an *intermediate certificate* file that contains additional certificates needed to complete the CA's chain of trust\. 
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
Examine the output for the tell\-tale lines described above\. Do not use files ending with `.p7b`, `.p7c`, or similar extensions\.

1. Place the new CA\-signed certificate and any intermediate certificates in the `/etc/pki/tls/certs` directory\.
**Note**  
There are several ways to upload your new certificate to your EC2 instance, but the most straightforward and informative way is to open a text editor \(vi, nano, notepad, etc\.\) on both your local computer and your instance, and then copy and paste the file contents between them\. You need root \[sudo\] privileges when performing these operations on the EC2 instance\. This way, you can see immediately if there are any permission or path problems\. Be careful, however, not to add any additional lines while copying the contents, or to change them in any way\. 

   From inside the `/etc/pki/tls/certs` directory, check that the file ownership, group, and permission settings match the highly restrictive Amazon Linux 2 defaults \(owner=root, group=root, read/write for owner only\)\. The commands would be as follows: 

   ```
   [ec2-user certs]$ sudo chown root:root custom.crt
   [ec2-user certs]$ sudo chmod 600 custom.crt
   [ec2-user certs]$ ls -al custom.crt
   ```

   These commands should yield the following result: 

   ```
   -rw------- root root custom.crt
   ```

   The permissions for the intermediate certificate file are less stringent \(owner=root, group=root, owner can write, group can read, world can read\)\. The commands would be: 

   ```
   [ec2-user certs]$ sudo chown root:root intermediate.crt
   [ec2-user certs]$ sudo chmod 644 intermediate.crt
   [ec2-user certs]$ ls -al intermediate.crt
   ```

   These commands should yield the following result:

   ```
   -rw-r--r-- root root intermediate.crt
   ```

1. Place the private key you used to create the CSR in the `/etc/pki/tls/private/` directory\. 
**Note**  
There are several ways to upload your custom key to your EC2 instance, but the most straightforward and informative way is to open a text editor \(vi, nano, notepad, etc\.\) on both your local computer and your instance, and then copy and paste the file contents between them\. You need root \[sudo\] privileges when performing these operations on the EC2 instance\. This way, you can see immediately if there are any permission or path problems\. Be careful, however, not to add any additional lines while copying the contents, or to change them in any way\.

   From inside the `/etc/pki/tls/private` directory, check that the file ownership, group, and permission settings match the highly restrictive Amazon Linux 2 defaults \(owner=root, group=root, read/write for owner only\)\. The commands would be as follows: 

   ```
   [ec2-user private]$ sudo chown root:root custom.key
   [ec2-user private]$ sudo chmod 600 custom.key
   [ec2-user private]$ ls -al custom.key
   ```

   These commands should yield the following result: 

   ```
   -rw------- root root custom.key
   ```

1. Edit `/etc/httpd/conf.d/ssl.conf` to align it with your new certificate and key files\.
   + Provide the path and file name of the CA\-signed host certificate in Apache's `SSLCertificateFile` directive:

     ```
     SSLCertificateFile /etc/pki/tls/certs/custom.crt
     ```
   + If you received an intermediate certificate file \(**intermediate\.crt** in this example\), provide its path and file name using Apache's `SSLCACertificateFile` directive:

     ```
     SSLCACertificateFile /etc/pki/tls/certs/intermediate.crt
     ```
**Note**  
Some CAs combine the host certificate and the intermediate certificates in a single file, making the `SSLCACertificateFile` directive unnecessary\. Consult the instructions provided by your CA\.
   + Provide the path and file name of the private key \(**custom\.key** in this example\) in Apache's SSLCertificateKeyFile directive:

     ```
     SSLCertificateKeyFile /etc/pki/tls/private/custom.key
     ```

1. Save `/etc/httpd/conf.d/ssl.conf` and restart Apache\.

   ```
   [ec2-user ~]$ sudo systemctl restart httpd
   ```

1. Test your server by typing your domain name into a browser URL bar with the prefix `https://`\. Your browser should load the test page over HTTPS without generating errors\. 
**Note**  
If you encounter a "page not found" error when providing a URL in the form `https://www.example.com`, check whether DNS is working\. You can do this by entering the server's public\-facing IP address in a browser in the form `https://111.111.111.111`\. \(You can find the IP address by opening the EC2 console's details page for your instance\.\) If the browser connects, even with certificate errors, it probably means that DNS is misconfigured\.

## Step 3: Test and Harden the Security Configuration<a name="ssl_test"></a>

After your SSL/TLS is operational and exposed to the public, you should test how secure it really is\. This is easy to do using online services such as [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html), which performs a free and thorough analysis of your security setup\. Based on the results, you may decide to harden the default security configuration by controlling which protocols you accept, which ciphers you prefer, and which you exclude\. For more information, see [how Qualys formulates its scores](https://github.com/ssllabs/research/wiki/SSL-Server-Rating-Guide)\.

**Important**  
Real\-world testing is crucial to the security of your server\. Small configuration errors may lead to serious security breaches and loss of data\. Because recommended security practices change constantly in response to research and emerging threats, periodic security audits are essential to good server administration\. 

On the [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html) site, type the fully qualified domain name of your server, in the form **www\.example\.com**\. After about two minutes, you receive a grade \(from A to F\) for your site and a detailed breakdown of the findings\. The table below summarizes the report for a domain with settings identical to the default Apache configuration on Amazon Linux 2 and a default Certbot certificate: 


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

The simplest of these issues to fix is TLS version support\. 

**To correct and future\-proof the TLS configuration**

1. Open the configuration file `/etc/httpd/conf.d/ssl.conf` in a text editor and comment out the following line by typing "\#" at the beginning:

   ```
   #SSLProtocol all -SSLv3
   ```

1. Add the following directive: 

   ```
   SSLProtocol -SSLv2 -SSLv3 -TLSv1 -TLSv1.1 +TLSv1.2
   ```

   This directive explicitly disables SSL versions 2 and 3, as well as TLS versions 1\.0 and 1\.1\. The server now refuses to accept encrypted connections with clients using anything except TLS 1\.2\. The verbose wording in the directive conveys more clearly, to a human reader, what the server is configured to do\.
**Note**  
Disabling TLS versions 1\.0 and 1\.1 in this manner blocks a small percentage of outdated web browsers from accessing your site\.

The other problems go away when we improve the list of allowed ciphers, prioritizing those that support forward secrecy, and removing the ones that support RC4\.

**To modify the list of allowed ciphers**

1. In the configuration file `/etc/httpd/conf.d/ssl.conf`, find the section with the **SSLCipherSuite** directive and comment out the existing line:

   ```
   #SSLCipherSuite HIGH:MEDIUM:!aNULL:!MD5
   ```

1. Specify explicit cipher suites and a cipher order that prioritizes forward secrecy and avoids insecure ciphers\. The `SSLCipherSuite` directive used here is based on output from the [Mozilla SSL Configuration Generator](mozilla.github.io/server-side-tls/ssl-config-generator/), which tailors a TLS configuration to the specific software running on your server\. \(For more information, see Mozilla's useful resource [Security/Server Side TLS](https://wiki.mozilla.org/Security/Server_Side_TLS)\.\) First determine your Apache and OpenSSL versions using output form the following commands:

   ```
   [ec2-user ~]$ yum list installed | grep httpd
   
   [ec2-user ~]$ yum list installed | grep openssl
   ```

   For example, if the returned information is be Apache 2\.4\.34 and OpenSSL 1\.0\.2, and we can enter this into the generator and choose the "modern" compatibility model, which creates an `SSLCipherSuite` directive that aggressively enforces security but still works for most browsers:

   ```
   SSLCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:
   ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:
   ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256
   ```

   The selected ciphers have *ECDHE* in their names, for *Elliptic Curve Diffie\-Hellman Ephemeral *\. The term *ephemeral* indicates forward secrecy\. As a by\-product, these ciphers do not support RC4\.

   We recommend that you use an explicit list of ciphers instead relying on defaults or terse directives whose content isn't visible\.

   Copy the generated directive into `/etc/httpd/conf.d/ssl.conf`\.
**Note**  
Though shown here on several lines for readability, the directive must be on a single line when copied to `/etc/httpd/conf.d/ssl.conf` with only a colon \(no spaces\) between cipher names\.

1. Finally, uncomment the following line by removing the "\#":

   ```
   #SSLHonorCipherOrder on
   ```

   This directive forces the server to prefer high\-ranking ciphers, including \(in this case\) those that support forward secrecy\. With this directive turned on, the server tries to establish a strong secure connection before falling back to allowed ciphers with lesser security\.

After completing both of these procedures, save the changes to `/etc/httpd/conf.d/ssl.conf` and restart Apache\.

If you test the domain again on [Qualys SSL Labs](https://www.ssllabs.com/ssltest/analyze.html), you should see that the RC4 vulnerability and other warnings are gone and the summary looks something like the following:


****  

|  |  | 
| --- |--- |
| Overall rating | A | 
| Certificate | 100% | 
| Protocol support | 100% | 
| Key exchange | 90% | 
| Cipher strength | 90% | 

**Important**  
Each update to OpenSSL introduces new ciphers and removes support for old ones\. Keep your EC2 Amazon Linux 2 instance up\-to\-date, watch for security announcements from [OpenSSL](https://www.openssl.org/), and be alert to reports of new security exploits in the technical press\. For more information, see [Predefined SSL Security Policies for Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-security-policy-table.html) in the *User Guide for Classic Load Balancers*\.

## Troubleshooting<a name="troubleshooting"></a>
+ **My Apache webserver doesn't start unless I supply a password\.**

  This is expected behavior if you installed an encrypted, password\-protected, private server key\. 

  You can strip the key of its encryption and password\. Assuming you have a private encrypted RSA key called `custom.key` in the default directory, and that the passphrase on it is **abcde12345**, run the following commands on your EC2 instance to generate an unencrypted version of this key:

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
+  **I get errors when I run **sudo yum install \-y mod\_ssl**\.**

  When you are installing the required packages for SSL, you may see errors like these:

  ```
  Error: httpd24-tools conflicts with httpd-tools-2.2.34-1.16.amzn1.x86_64
  Error: httpd24 conflicts with httpd-2.2.34-1.16.amzn1.x86_64
  ```

  This typically means that your EC2 instance is not running Amazon Linux 2\. This tutorial only supports instances freshly created from an official Amazon Linux 2 AMI\.

## Appendix: Let's Encrypt with Certbot on Amazon Linux 2<a name="letsencrypt"></a>

The [Let's Encrypt](https://letsencrypt.org/) certificate authority is the centerpiece of an effort by the Electronic Frontier Foundation \(EFF\) to encrypt the entire Internet\. In line with that goal, Let's Encrypt host certificates are designed to be created, validated, installed, and maintained with minimal human intervention\. The automated aspects of certificate management are carried out by a software agent running on your webserver\. After you install and configure the agent, it communicates securely with Let's Encrypt and performs administrative tasks on Apache and the key management system\. This tutorial uses the free [Certbot](https://certbot.eff.org) agent because it allows you either to supply a customized encryption key as the basis for your certificates, or to allow the agent itself to create a key based on its defaults\. You can also configure Certbot to renew your certificates on a regular basis without human interaction, as described below in [To automate Certbot](#automate_certbot)\. For more information, consult the Certbot [User Guide](https://certbot.eff.org/docs/using.html) and [man page](http://manpages.ubuntu.com/manpages/bionic/en/man1/certbot.1.html)\. 

![\[Certbot logo\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/certbot-logo-7.png)

 **Certbot is a client utility for EFF's Let's Encrypt certificate service\.** 

Certbot is not officially supported on Amazon Linux 2, but is available for download and functions correctly once installed\. We recommend that you make the following backups to protect your data and avoid inconvenience:
+ Before you begin, take a snapshot of your EBS root volume\. This allows you to restore the original state of your EC2 instance\. For information about creating EBS snapshots, see [Creating an Amazon EBS Snapshot](ebs-creating-snapshot.md)\.
+ The procedure below requires you to edit your `httpd.conf` file, which controls Apache's operation\. Certbot makes its own automated changes to this and other configuration files\. Make a backup copy of your entire `/etc/httpd` directory in case you need to restore it\.

### Prepare to Install<a name="prepare"></a>

Complete the following procedures before you install Certbot\.

1. Download the Extra Packages for Enterprise Linux \(EPEL\) 7 repository packages\. These are required to supply dependencies needed by Certbot\. 

   1. Navigate to your home directory \(`/home/ec2-user`\)\. Download EPEL with the following command:

      ```
      [ec2-user ~]$ sudo wget -r --no-parent -A 'epel-release-*.rpm' http://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/
      ```

   1. Install the repository packages as follows:

      ```
      [ec2-user ~]$ sudo rpm -Uvh dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-*.rpm
      ```

   1. Enable EPEL:

      ```
      [ec2-user ~]$ sudo yum-config-manager --enable epel*
      ```

      You can confirm that EPEL is enabled with the following command, should should return information such as the snippet shown:

      ```
      [ec2-user ~]$ sudo yum repolist all
      
      ...
      epel/x86_64                          Extra Packages for Enterprise Linux 7 - x86_64                               enabled: 12949+175
      epel-debuginfo/x86_64                Extra Packages for Enterprise Linux 7 - x86_64 - Debug                       enabled:      2890
      epel-source/x86_64                   Extra Packages for Enterprise Linux 7 - x86_64 - Source                      enabled:         0
      epel-testing/x86_64                  Extra Packages for Enterprise Linux 7 - Testing - x86_64                     enabled:    778+12
      epel-testing-debuginfo/x86_64        Extra Packages for Enterprise Linux 7 - Testing - x86_64 - Debug             enabled:       107
      epel-testing-source/x86_64           Extra Packages for Enterprise Linux 7 - Testing - x86_64 - Source            enabled:         0
      ...
      ```

1. Edit the main Apache configuration file, `/etc/httpd/conf/httpd.conf`\. Locate the "`listen 80`" directive and add the following lines after it, replacing the example domain names with the actual Common Name and Subject Alternative Name \(SAN\) to configure:

   ```
   <VirtualHost *:80>
       DocumentRoot "/var/www/html"
       ServerName "example.com"
       ServerAlias "www.example.com"
   </VirtualHost>
   ```

   Save the file and restart Apache:

   ```
   [ec2-user ~]$ sudo systemctl restart httpd
   ```

### Install and Run Certbot<a name="install"></a>

This procedure is based on the EFF's documentation for installing Certbot on [ Fedora](https://certbot.eff.org/docs/install.html#alternate-installation-methods) and on [RHEL 7](https://certbot.eff.org/#centosrhel7-apache)\. It describes the default use of Certbot, resulting in a certificate based on a 2048\-bit RSA key\. If you want to experiment with customized keys, you might start with [Using ECDSA certificates with Let's Encrypt](https://www.ericlight.com/using-ecdsa-certificates-with-lets-encrypt)\.

1. Install Certbot packages and dependencies using the following command:

   ```
   [ec2-user ~]$ sudo yum install -y certbot python2-certbot-apache
   ```

1. Run Certbot:

   ```
   [ec2-user ~]$ sudo certbot
   ```

1. At the prompt "Enter email address \(used for urgent renewal and security notices\)," type a contact address and press Enter\.

1. Agree to the Let's Encrypt Terms of Service at the prompt\. Type "A" and press Enter to proceed:

   ```
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   Please read the Terms of Service at
   https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
   agree in order to register with the ACME server at
   https://acme-v02.api.letsencrypt.org/directory
   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
   (A)gree/(C)ancel: A
   ```

1. At the authorization for EFF to put you on their mailing list, type "Y" or "N" and press Enter\.

1. Certbot displays the Common Name and Subject Alternative Name \(SAN\) that you provided in the VirtualHost block:

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

1. Certbot displays the following output as it creates certificates and configures Apache\. It then prompts you about redirecting HTTP queries to HTTPS:

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

   To allow visitors to connect to your server via unencrypted HTTP, type "1"\. If you want to accept only encrypted connections via HTTPS, type "2"\. Press Enter to submit your choice\.

1. Certbot completes the configuration of Apache and reports success and other information:

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

1. After you complete the installation, test and optimize the security of your server as described in [Step 3: Test and Harden the Security Configuration](#ssl_test)\. 

### Configure Automated Certificate Renewal<a name="automate"></a><a name="automate_certbot"></a>

**To automate Certbot**

Certbot is designed to become an invisible, error\-resistant part of your server system\. By default, it generates host certificates with a short, 90\-day expiration time\. If you have not configured your system to call the command automatically, you must re\-run the certbot command manually before expiration\. This procedure shows how to automate Certbot by setting up a cron job\.

1. Open `/etc/crontab` in a text editor and add a line similar to the following:

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

   Save the file when done\.

1. Restart the cron daemon:

   ```
   [ec2-user ~]$ sudo systemctl restart crond
   ```
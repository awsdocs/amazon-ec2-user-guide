# Setting up the AMI tools<a name="set-up-ami-tools"></a>

You can use the AMI tools to create and manage instance store\-backed Linux AMIs\. To use the tools, you must install them on your Linux instance\. The AMI tools are available as both an RPM and as a \.zip file for Linux distributions that don't support RPM\.

**To set up the AMI tools using the RPM**

1. Install Ruby using the package manager for your Linux distribution, such as yum\. For example:

   ```
   [ec2-user ~]$ sudo yum install -y ruby
   ```

1. Download the RPM file using a tool such as wget or curl\. For example:

   ```
   [ec2-user ~]$ wget https://s3.amazonaws.com/ec2-downloads/ec2-ami-tools.noarch.rpm
   ```

1. Verify the RPM file's signature using the following command:

   ```
   [ec2-user ~]$ rpm -K ec2-ami-tools.noarch.rpm
   ```

   The command above should indicate that the file's SHA1 and MD5 hashes are `OK.` If the command indicates that the hashes are `NOT OK`, use the following command to view the file's Header SHA1 and MD5 hashes:

   ```
   [ec2-user ~]$ rpm -Kv ec2-ami-tools.noarch.rpm
   ```

   Then, compare your file's Header SHA1 and MD5 hashes with the following verified AMI tools hashes to confirm the file's authenticity:
   + Header SHA1: a1f662d6f25f69871104e6a62187fa4df508f880
   + MD5: 9faff05258064e2f7909b66142de6782

   If your file's Header SHA1 and MD5 hashes match the verified AMI tools hashes, continue to the next step\.

1. Install the RPM using the following command:

   ```
   [ec2-user ~]$ sudo yum install ec2-ami-tools.noarch.rpm
   ```

1. Verify your AMI tools installation using the [ec2\-ami\-tools\-version](ami-tools-commands.md#ami-tools-version) command\.

   ```
   [ec2-user ~]$ ec2-ami-tools-version
   ```
**Note**  
If you receive a load error such as "cannot load such file \-\- ec2/amitools/version \(LoadError\)", complete the next step to add the location of your AMI tools installation to your `RUBYLIB` path\.

1. \(Optional\) If you received an error in the previous step, add the location of your AMI tools installation to your `RUBYLIB` path\.

   1. Run the following command to determine the paths to add\.

      ```
      [ec2-user ~]$ rpm -qil ec2-ami-tools | grep ec2/amitools/version
      /usr/lib/ruby/site_ruby/ec2/amitools/version.rb
      /usr/lib64/ruby/site_ruby/ec2/amitools/version.rb
      ```

      In the above example, the missing file from the previous load error is located at `/usr/lib/ruby/site_ruby` and `/usr/lib64/ruby/site_ruby`\.

   1. Add the locations from the previous step to your `RUBYLIB` path\.

      ```
      [ec2-user ~]$ export RUBYLIB=$RUBYLIB:/usr/lib/ruby/site_ruby:/usr/lib64/ruby/site_ruby
      ```

   1. Verify your AMI tools installation using the [ec2\-ami\-tools\-version](ami-tools-commands.md#ami-tools-version) command\.

      ```
      [ec2-user ~]$ ec2-ami-tools-version
      ```

**To set up the AMI tools using the \.zip file**

1. Install Ruby and unzip using the package manager for your Linux distribution, such as apt\-get\. For example:

   ```
   [ec2-user ~]$ sudo apt-get update -y && sudo apt-get install -y ruby unzip
   ```

1. Download the \.zip file using a tool such as wget or curl\. For example:

   ```
   [ec2-user ~]$ wget https://s3.amazonaws.com/ec2-downloads/ec2-ami-tools.zip
   ```

1. Unzip the files into a suitable installation directory, such as `/usr/local/ec2`\.

   ```
   [ec2-user ~]$ sudo mkdir -p /usr/local/ec2
   $ sudo unzip ec2-ami-tools.zip -d /usr/local/ec2
   ```

   Notice that the \.zip file contains a folder ec2\-ami\-tools\-*x*\.*x*\.*x*, where *x*\.*x*\.*x* is the version number of the tools \(for example, `ec2-ami-tools-1.5.7`\)\.

1. Set the `EC2_AMITOOL_HOME` environment variable to the installation directory for the tools\. For example:

   ```
   [ec2-user ~]$ export EC2_AMITOOL_HOME=/usr/local/ec2/ec2-ami-tools-x.x.x
   ```

1. Add the tools to your `PATH` environment variable\. For example:

   ```
   [ec2-user ~]$ export PATH=$EC2_AMITOOL_HOME/bin:$PATH
   ```

1. You can verify your AMI tools installation using the [ec2\-ami\-tools\-version](ami-tools-commands.md#ami-tools-version) command\.

   ```
   [ec2-user ~]$ ec2-ami-tools-version
   ```

## Managing signing certificates<a name="ami-tools-managing-certs"></a>

Certain commands in the AMI tools require a signing certificate \(also known as X\.509 certificate\)\. You must create the certificate and then upload it to AWS\. For example, you can use a third\-party tool such as OpenSSL to create the certificate\.

**To create a signing certificate**

1. Install and configure OpenSSL\.

1. Create a private key using the `openssl genrsa` command and save the output to a `.pem` file\. We recommend that you create a 2048\- or 4096\-bit RSA key\.

   ```
   openssl genrsa 2048 > private-key.pem
   ```

1. Generate a certificate using the `openssl req` command\.

   ```
   openssl req -new -x509 -nodes -sha256 -days 365 -key private-key.pem -outform PEM -out certificate.pem
   ```

To upload the certificate to AWS, use the [upload\-signing\-certificate](https://docs.aws.amazon.com/cli/latest/reference/iam/upload-signing-certificate.html) command\.

```
aws iam upload-signing-certificate --user-name user-name --certificate-body file://path/to/certificate.pem
```

To list the certificates for a user, use the [list\-signing\-certificates](https://docs.aws.amazon.com/cli/latest/reference/iam/list-signing-certificates.html) command:

```
aws iam list-signing-certificates --user-name user-name
```

To disable or re\-enable a signing certificate for a user, use the [update\-signing\-certificate](https://docs.aws.amazon.com/cli/latest/reference/iam/update-signing-certificate.html) command\. The following command disables the certificate:

```
aws iam update-signing-certificate --certificate-id OFHPLP4ZULTHYPMSYEX7O4BEXAMPLE --status Inactive --user-name user-name
```

To delete a certificate, use the [delete\-signing\-certificate](https://docs.aws.amazon.com/cli/latest/reference/iam/delete-signing-certificate.html) command:

```
aws iam delete-signing-certificate --user-name user-name --certificate-id OFHPLP4ZULTHYPMSYEX7O4BEXAMPLE
```
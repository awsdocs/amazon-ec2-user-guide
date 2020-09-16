# \(Optional\) Verify the signature of EC2Rescue for Linux<a name="ec2rl_verify"></a>

The following is the recommended process of verifying the validity of the EC2Rescue for Linux package for Linux\-based operating systems\.

When you download an application from the internet, we recommend that you authenticate the identity of the software publisher and check that the application has not been altered or corrupted after it was published\. This protects you from installing a version of the application that contains a virus or other malicious code\.

If, after running the steps in this topic, you determine that the software for EC2Rescue for Linux is altered or corrupted, do not run the installation file\. Instead, contact Amazon Web Services\.

EC2Rescue for Linux files for Linux\-based operating systems are signed using GnuPG, an open\-source implementation of the Pretty Good Privacy \(OpenPGP\) standard for secure digital signatures\. GnuPG \(also known as GPG\) provides authentication and integrity checking through a digital signature\. AWS publishes a public key and signatures that you can use to verify the downloaded EC2Rescue for Linux package\. For more information about PGP and GnuPG \(GPG\), see [http://www\.gnupg\.org](http://www.gnupg.org)\.

The first step is to establish trust with the software publisher\. Download the public key of the software publisher, check that the owner of the public key is who they claim to be, and then add the public key to your keyring\. Your keyring is a collection of known public keys\. After you establish the authenticity of the public key, you can use it to verify the signature of the application\.

**Topics**
+ [Install the GPG tools](#ec2rl_install_gpg)
+ [Authenticate and import the public key](#ec2rl_authenticate)
+ [Verify the signature of the package](#ec2rl_verify_signature)

## Install the GPG tools<a name="ec2rl_install_gpg"></a>

If your operating system is Linux or Unix, the GPG tools may already be installed\. To test whether the tools are installed on your system, enter gpg2 at a command prompt\. If the GPG tools are installed, you see a GPG command prompt\. If the GPG tools are not installed, you see an error stating that the command cannot be found\. You can install the GnuPG package from a repository\.

**To install GPG tools on Debian\-based Linux**
+ From a terminal, run the following command:

  ```
  apt-get install gnupg2
  ```

**To install GPG tools on Red Hat–based Linux**
+ From a terminal, run the following command:

  ```
  yum install gnupg2
  ```

## Authenticate and import the public key<a name="ec2rl_authenticate"></a>

The next step in the process is to authenticate the EC2Rescue for Linux public key and add it as a trusted key in your GPG keyring\.

**To authenticate and import the EC2Rescue for Linux public key**

1. At a command prompt, use the following command to obtain a copy of our public GPG build key:

   ```
   curl -O https://s3.amazonaws.com/ec2rescuelinux/ec2rl.key
   ```

1. At a command prompt in the directory where you saved `ec2rl.key`, use the following command to import the EC2Rescue for Linux public key into your keyring:

   ```
   gpg2 --import ec2rl.key
   ```

   The command returns results similar to the following:

   ```
   gpg: /home/ec2-user/.gnupg/trustdb.gpg: trustdb created
   gpg: key 2FAE2A1C: public key "ec2autodiag@amazon.com <EC2 Rescue for Linux>" imported
   gpg: Total number processed: 1
   gpg:               imported: 1  (RSA: 1)
   ```

## Verify the signature of the package<a name="ec2rl_verify_signature"></a>

After you've installed the GPG tools, authenticated and imported the EC2Rescue for Linux public key, and verified that the EC2Rescue for Linux public key is trusted, you are ready to verify the signature of the EC2Rescue for Linux installation script\.

**To verify the EC2Rescue for Linux installation script signature**

1. At a command prompt, run the following command to download the signature file for the installation script:

   ```
   curl -O https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz.sig
   ```

1. Verify the signature by running the following command at a command prompt in the directory where you saved `ec2rl.tgz.sig` and the EC2Rescue for Linux installation file\. Both files must be present\.

   ```
   gpg2 --verify ./ec2rl.tgz.sig
   ```

   The output should look something like the following:

   ```
   gpg: Signature made Thu 12 Jul 2018 01:57:51 AM UTC using RSA key ID 6991ED45
   gpg: Good signature from "ec2autodiag@amazon.com <EC2 Rescue for Linux>"
   gpg: WARNING: This key is not certified with a trusted signature!
   gpg:          There is no indication that the signature belongs to the owner.
   Primary key fingerprint: E528 BCC9 0DBF 5AFA 0F6C  C36A F780 4843 2FAE 2A1C
        Subkey fingerprint: 966B 0D27 85E9 AEEC 1146  7A9D 8851 1153 6991 ED45
   ```

   If the output contains the phrase `Good signature from "ec2autodiag@amazon.com <EC2 Rescue for Linux>"`, it means that the signature has successfully been verified, and you can proceed to run the EC2Rescue for Linux installation script\.

   If the output includes the phrase `BAD signature`, check whether you performed the procedure correctly\. If you continue to get this response, contact Amazon Web Services and do not run the installation file that you downloaded previously\.

The following are details about the warnings that you might see:
+ WARNING: This key is not certified with a trusted signature\! There is no indication that the signature belongs to the owner\. This refers to your personal level of trust in your belief that you possess an authentic public key for EC2Rescue for Linux\. In an ideal world, you would visit an Amazon Web Services office and receive the key in person\. However, more often you download it from a website\. In this case, the website is an Amazon Web Services website\.
+ gpg2: no ultimately trusted keys found\. This means that the specific key is not "ultimately trusted" by you \(or by other people whom you trust\)\.

For more information, see [http://www\.gnupg\.org](http://www.gnupg.org)\.
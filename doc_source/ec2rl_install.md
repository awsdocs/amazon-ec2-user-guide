# Installing EC2Rescue for Linux<a name="ec2rl_install"></a>

The EC2Rescue for Linux tool can be installed on an Amazon EC2 Linux instance that meets the following prerequisites\.

**Prerequisites**
+ Supported operating systems:
  + Amazon Linux 2
  + Amazon Linux 2016\.09\+
  + SLES 12\+
  + RHEL 7\+
  + Ubuntu 16\.04\+
+ Software requirements:
  + Python 2\.7\.9\+ or 3\.2\+

**To install EC2Rescue for Linux**

1. From a working Linux instance, download the [EC2Rescue for Linux](https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz) tool:

   ```
   [ec2-user ~]$ curl -O https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz
   ```

1. Download the sha256 hash file:

   ```
   [ec2-user ~]$ curl -O https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz.sha256
   ```

1. Verify the integrity of the tarball:

   ```
   [ec2-user ~]$  sha256sum -c ec2rl.tgz.sha256
   ```

1. Unpack the tarball:

   ```
   [ec2-user ~]$ tar -xvf ec2rl.tgz
   ```

1. Verify the installation by listing out the help file:

   ```
   [ec2-user ~]$ cd ec2rl-<version_number>
   [ec2-user ~]$ ./ec2rl help
   ```
# Install EC2Rescue for Linux<a name="ec2rl_install"></a>

The EC2Rescue for Linux tool can be installed on an Amazon EC2 Linux instance that meets the following prerequisites\.

**Prerequisites**
+ Supported operating systems:
  + Amazon Linux 2
  + Amazon Linux 2016\.09\+
  + SUSE Linux Enterprise Server 12\+
  + RHEL 7\+
  + Ubuntu 16\.04\+
+ Software requirements:
  + Python 2\.7\.9\+ or 3\.2\+

If your system has the required Python version, you can install the standard build\. Otherwise, you can install the bundled build, which includes a minimal copy of Python\.

**To install the standard build**

1. From a working Linux instance, download the [EC2Rescue for Linux](https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz) tool:

   ```
   curl -O https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz
   ```

1. \(Optional\) Before proceeding, you can optionally verify the signature of the EC2Rescue for Linux installation file\. For more information, see [\(Optional\) Verify the signature of EC2Rescue for Linux](ec2rl_verify.md)\.

1. Download the sha256 hash file:

   ```
   curl -O https://s3.amazonaws.com/ec2rescuelinux/ec2rl.tgz.sha256
   ```

1. Verify the integrity of the tarball:

   ```
   sha256sum -c ec2rl.tgz.sha256
   ```

1. Unpack the tarball:

   ```
   tar -xvf ec2rl.tgz
   ```

1. Verify the installation by listing out the help file:

   ```
   cd ec2rl-<version_number>
   ./ec2rl help
   ```

**To install the bundled build**  
For a link to the download and a list of limitations, see [EC2Rescue for Linux](https://github.com/awslabs/aws-ec2rescue-linux/blob/master/README.md) on github\.
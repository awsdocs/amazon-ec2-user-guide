# AMI Tools Reference<a name="ami-tools-commands"></a>

You can use the AMI tools commands to create and manage instance store\-backed Linux AMIs\. To set up the tools, see [Setting Up the AMI Tools](set-up-ami-tools.md)\.

For information about your access keys, see [Best Practices for Managing AWS Access Keys](http://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html)\.

**Topics**
+ [ec2\-ami\-tools\-version](#ami-tools-version)
+ [ec2\-bundle\-image](#ami-bundle-image)
+ [ec2\-bundle\-vol](#ami-bundle-vol)
+ [ec2\-delete\-bundle](#ami-delete-bundle)
+ [ec2\-download\-bundle](#ami-download-bundle)
+ [ec2\-migrate\-manifest](#ami-migrate-manifest)
+ [ec2\-unbundle](#ami-unbundle)
+ [ec2\-upload\-bundle](#ami-upload-bundle)
+ [Common Options for AMI Tools](#common-args-ami)

## ec2\-ami\-tools\-version<a name="ami-tools-version"></a>

### Description<a name="ami-tools-version-description"></a>

Describes the version of the AMI tools\.

### Syntax<a name="ami-tools-version-request"></a>

**ec2\-ami\-tools\-version**

### Output<a name="ami-tools-version-output"></a>

The version information\.

### Example<a name="ami-tools-version-response"></a>

This example command displays the version information for the AMI tools that you're using\.

```
[ec2-user ~]$ ec2-ami-tools-version
1.5.2 20071010
```

## ec2\-bundle\-image<a name="ami-bundle-image"></a>

### Description<a name="bundle-image-description"></a>

Creates an instance store\-backed Linux AMI from an operating system image created in a loopback file\.

### Syntax<a name="bundle-image-request"></a>

**ec2\-bundle\-image \-c *path* \-k *path* \-u *account* \-i *path* \[\-d *path*\] \[\-\-ec2cert *path*\] \[\-r *architecture*\] \[\-\-productcodes *code1*,*code2*,\.\.\.\] \[\-B *mapping*\] \[\-p *prefix*\]** 

### Options<a name="bundle-image-parameters"></a>

`-c, --cert` *path*  
The user's PEM encoded RSA public key certificate file\.  
Required: Yes

`-k, --privatekey` *path*  
The path to a PEM\-encoded RSA key file\. You'll need to specify this key to unbundle this bundle, so keep it in a safe place\. Note that the key doesn't have to be registered to your AWS account\.  
Required: Yes

`-u, --user ` *account*  
The user's AWS account ID, without dashes\.  
Required: Yes

`-i, --image` *path*  
The path to the image to bundle\.  
Required: Yes

`-d, --destination` *path*  
The directory in which to create the bundle\.  
Default: `/tmp`  
Required: No

`--ec2cert` *path*  
The path to the Amazon EC2 X\.509 public key certificate used to encrypt the image manifest\.  
The `us-gov-west-1` and `cn-north-1` regions use a non\-default public key certificate and the path to that certificate must be specified with this option\. The path to the certificate varies based on the installation method of the AMI tools\. For Amazon Linux, the certificates are located at `/opt/aws/amitools/ec2/etc/ec2/amitools/`\. If you installed the AMI tools from the RPM or ZIP file in [Setting Up the AMI Tools](set-up-ami-tools.md), the certificates are located at `$EC2_AMITOOL_HOME/etc/ec2/amitools/`\.  
Required: Only for the `us-gov-west-1` and `cn-north-1` regions\.

`-r, --arch` *architecture*  
Image architecture\. If you don't provide the architecture on the command line, you'll be prompted for it when bundling starts\.  
Valid values: `i386` \| `x86_64`  
Required: No

`--productcodes` *code1,code2,\.\.\.*  
Product codes to attach to the image at registration time, separated by commas\.  
Required: No

`-B, --block-device-mapping` *mapping*  
Defines how block devices are exposed to an instance of this AMI if its instance type supports the specified device\.  
Specify a comma\-separated list of key\-value pairs, where each key is a virtual name and each value is the corresponding device name\. Virtual names include the following:  
+ `ami`—The root file system device, as seen by the instance
+ `root`—The root file system device, as seen by the kernel
+ `swap`—The swap device, as seen by the instance
+ `ephemeralN`—The Nth instance store volume
Required: No

`-p, --prefix` *prefix*  
The filename prefix for bundled AMI files\.  
Default: The name of the image file\. For example, if the image path is `/var/spool/my-image/version-2/debian.img`, then the default prefix is `debian.img`\.  
Required: No

`--kernel` *kernel\_id*  
Deprecated\. Use [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to set the kernel\.  
Required: No

`--ramdisk` *ramdisk\_id*  
Deprecated\. Use [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to set the RAM disk if required\.  
Required: No

### Output<a name="bundle-image-output"></a>

Status messages describing the stages and status of the bundling process\.

### Example<a name="bundle-image-response"></a>

This example creates a bundled AMI from an operating system image that was created in a loopback file\.

```
[ec2-user ~]$ ec2-bundle-image -k pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -c cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -u 111122223333 -i image.img -d bundled/ -r x86_64
Please specify a value for arch [i386]: 
Bundling image file...
Splitting bundled/image.gz.crypt...
Created image.part.00
Created image.part.01
Created image.part.02
Created image.part.03
Created image.part.04
Created image.part.05
Created image.part.06
Created image.part.07
Created image.part.08
Created image.part.09
Created image.part.10
Created image.part.11
Created image.part.12
Created image.part.13
Created image.part.14
Generating digests for each part...
Digests generated.
Creating bundle manifest...
ec2-bundle-image complete.
```

## ec2\-bundle\-vol<a name="ami-bundle-vol"></a>

### Description<a name="bundle-vol-description"></a>

Creates an instance store\-backed Linux AMI by compressing, encrypting, and signing a copy of the root device volume for the instance\.

Amazon EC2 attempts to inherit product codes, kernel settings, RAM disk settings, and block device mappings from the instance\.

By default, the bundle process excludes files that might contain sensitive information\. These files include `*.sw`, `*.swo`, `*.swp`, `*.pem`, `*.priv`, `*id_rsa*`, `*id_dsa*` `*.gpg`, `*.jks`, `*/.ssh/authorized_keys`, and `*/.bash_history`\. To include all of these files, use the `--no-filter` option\. To include some of these files, use the `--include` option\.

For more information, see [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\.

### Syntax<a name="bundle-vol-request"></a>

**ec2\-bundle\-vol \-c *path* \-k *path* \-u *account* \[\-d *path*\] \[\-\-ec2cert *path*\] \[\-r *architecture*\] \[\-\-productcodes *code1*,*code2*,\.\.\.\] \[\-B *mapping*\] \[\-\-all\] \[\-e *directory1*,*directory2*,\.\.\.\] \[\-i *file1*,*file2*,\.\.\.\] \[\-\-no\-filter\] \[\-p *prefix*\] \[\-s *size*\] \[\-\-\[no\-\]inherit\] \[\-v *volume*\] \[\-P *type*\] \[\-S *script*\] \[\-\-fstab *path*\] \[\-\-generate\-fstab\] \[\-\-grub\-config *path*\]** 

### Options<a name="bundle-vol-parameters"></a>

`-c, --cert` *path*  
The user's PEM encoded RSA public key certificate file\.  
Required: Yes

`-k, --privatekey ` *path*   
The path to the user's PEM\-encoded RSA key file\.  
Required: Yes

`-u, --user` *account*  
The user's AWS account ID, without dashes\.  
Required: Yes

`-d, --destination` *destination*  
The directory in which to create the bundle\.  
Default: `/tmp`  
Required: No

`--ec2cert` *path*  
The path to the Amazon EC2 X\.509 public key certificate used to encrypt the image manifest\.  
The `us-gov-west-1` and `cn-north-1` regions use a non\-default public key certificate and the path to that certificate must be specified with this option\. The path to the certificate varies based on the installation method of the AMI tools\. For Amazon Linux, the certificates are located at `/opt/aws/amitools/ec2/etc/ec2/amitools/`\. If you installed the AMI tools from the RPM or ZIP file in [Setting Up the AMI Tools](set-up-ami-tools.md), the certificates are located at `$EC2_AMITOOL_HOME/etc/ec2/amitools/`\.  
Required: Only for the `us-gov-west-1` and `cn-north-1` regions\.

`-r, --arch ` *architecture*  
The image architecture\. If you don't provide this on the command line, you'll be prompted to provide it when the bundling starts\.  
Valid values: `i386` \| `x86_64`  
Required: No

`--productcodes` *code1,code2,\.\.\.*  
Product codes to attach to the image at registration time, separated by commas\.  
Required: No

`-B, --block-device-mapping` *mapping*  
Defines how block devices are exposed to an instance of this AMI if its instance type supports the specified device\.  
Specify a comma\-separated list of key\-value pairs, where each key is a virtual name and each value is the corresponding device name\. Virtual names include the following:  
+ `ami`—The root file system device, as seen by the instance
+ `root`—The root file system device, as seen by the kernel
+ `swap`—The swap device, as seen by the instance
+ `ephemeralN`—The Nth instance store volume
Required: No

`-a, --all`  
Bundle all directories, including those on remotely mounted file systems\.  
Required: No

`-e, --exclude ` *directory1,directory2,\.\.\.*  
A list of absolute directory paths and files to exclude from the bundle operation\. This parameter overrides the `--all` option\. When exclude is specified, the directories and subdirectories listed with the parameter will not be bundled with the volume\.  
Required: No

`-i, --include ` *file1,file2,\.\.\.*  
A list of files to include in the bundle operation\. The specified files would otherwise be excluded from the AMI because they might contain sensitive information\.  
Required: No

`--no-filter`  
If specified, we won't exclude files from the AMI because they might contain sensitive information\.  
Required: No

`-p, --prefix ` *prefix*  
The file name prefix for bundled AMI files\.  
Default: `image`  
Required: No

`-s, --size` *size*  
The size, in MB \(1024 \* 1024 bytes\), of the image file to create\. The maximum size is 10240 MB\.   
Default: 10240  
Required: No

`--[no-]inherit`  
Indicates whether the image should inherit the instance's metadata \(the default is to inherit\)\. Bundling fails if you enable `--inherit` but the instance metadata is not accessible\.  
Required: No

`-v, --volume ` *volume*  
The absolute path to the mounted volume from which to create the bundle\.  
Default: The root directory \(/\)  
Required: No

`-P, --partition` *type*  
Indicates whether the disk image should use a partition table\. If you don't specify a partition table type, the default is the type used on the parent block device of the volume, if applicable, otherwise the default is `gpt`\.  
Valid values: `mbr` \| `gpt` \| `none`  
Required: No

`-S, --script` *script*  
A customization script to be run right before bundling\. The script must expect a single argument, the mount point of the volume\.  
Required: No

`--fstab` *path*  
The path to the fstab to bundle into the image\. If this is not specified, Amazon EC2 bundles /etc/fstab\.  
Required: No

`--generate-fstab`  
Bundles the volume using an Amazon EC2\-provided fstab\.  
Required: No

`--grub-config`  
The path to an alternate grub configuration file to bundle into the image\. By default, `ec2-bundle-vol` expects either `/boot/grub/menu.lst` or `/boot/grub/grub.conf` to exist on the cloned image\. This option allows you to specify a path to an alternative grub configuration file, which will then be copied over the defaults \(if present\)\.  
Required: No

`--kernel` *kernel\_id*  
Deprecated\. Use [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to set the kernel\.  
Required: No

`--ramdisk`*ramdisk\_id*  
Deprecated\. Use [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to set the RAM disk if required\.  
Required: No

### Output<a name="bundle-vol-output"></a>

Status messages describing the stages and status of the bundling\.

### Example<a name="bundle-vol-response"></a>

This example creates a bundled AMI by compressing, encrypting and signing a snapshot of the local machine's root file system\. 

```
[ec2-user ~]$ ec2-bundle-vol -d /mnt -k pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -c cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -u 111122223333 -r x86_64
  Copying / into the image file /mnt/image...
  Excluding:
       sys
       dev/shm
       proc
       dev/pts
       proc/sys/fs/binfmt_misc
       dev
       media
       mnt
       proc
       sys
       tmp/image
       mnt/img-mnt
  1+0 records in
  1+0 records out
  mke2fs 1.38 (30-Jun-2005)
  warning: 256 blocks unused.

  Splitting /mnt/image.gz.crypt...
  Created image.part.00
  Created image.part.01
  Created image.part.02
  Created image.part.03
  ...
  Created image.part.22
  Created image.part.23
  Generating digests for each part...
  Digests generated.
  Creating bundle manifest...
  Bundle Volume complete.
```

## ec2\-delete\-bundle<a name="ami-delete-bundle"></a>

### Description<a name="delete-bundle-description"></a>

Deletes the specified bundle from Amazon S3 storage\. After you delete a bundle, you can't launch instances from the corresponding AMI\.

### Syntax<a name="delete-bundle-request"></a>

**ec2\-delete\-bundle \-b *bucket* \-a *access\_key\_id* \-s *secret\_access\_key* \[\-t *token*\] \[\-\-url *url*\] \[\-\-region *region*\] \[\-\-sigv *version*\] \[\-m *path*\] \[\-p *prefix*\] \[\-\-clear\] \[\-\-retry\] \[\-y\]** 

### Options<a name="delete-bundle-parameters"></a>

`-b, --bucket `*bucket*  
The name of the Amazon S3 bucket containing the bundled AMI, followed by an optional '/'\-delimited path prefix  
Required: Yes

`-a, --access-key` *access\_key\_id*  
The AWS access key ID\.  
Required: Yes

`-s, --secret-key` *secret\_access\_key*  
The AWS secret access key\.  
Required: Yes

`-t, --delegation-token` *token*  
The delegation token to pass along to the AWS request\. For more information, see the [Using Temporary Security Credentials](http://docs.aws.amazon.com/STS/latest/UsingSTS/)\.  
Required: Only when you are using temporary security credentials\.  
Default: The value of the `AWS_DELEGATION_TOKEN` environment variable \(if set\)\.

`--region`*region*  
The region to use in the request signature\.  
Default: `us-east-1`  
Required: Required if using signature version 4

`--sigv`*version*  
The signature version to use when signing the request\.  
Valid values: `2` \| `4`  
Default: `4`  
Required: No

`-m, --manifest`*path*  
The path to the manifest file\.  
Required: You must specify `--prefix` or `--manifest`\.

`-p, --prefix` *prefix*  
The bundled AMI filename prefix\. Provide the entire prefix\. For example, if the prefix is image\.img, use `-p image.img` and not `-p image`\.  
Required: You must specify `--prefix` or `--manifest`\.

`--clear`  
Deletes the Amazon S3 bucket if it's empty after deleting the specified bundle\.  
Required: No

`--retry`  
Automatically retries on all Amazon S3 errors, up to five times per operation\.  
Required: No

`-y, --yes`  
Automatically assumes the answer to all prompts is yes\.  
Required: No

### Output<a name="delete-bundle-output"></a>

Amazon EC2 displays status messages indicating the stages and status of the delete process\.

### Example<a name="delete-bundle-response"></a>

This example deletes a bundle from Amazon S3\.

```
[ec2-user ~]$ ec2-delete-bundle -b myawsbucket -a your_access_key_id -s your_secret_access_key
Deleting files:
myawsbucket/image.manifest.xml
myawsbucket/image.part.00
myawsbucket/image.part.01
myawsbucket/image.part.02
myawsbucket/image.part.03
myawsbucket/image.part.04
myawsbucket/image.part.05
myawsbucket/image.part.06
Continue? [y/n]
y
Deleted myawsbucket/image.manifest.xml
Deleted myawsbucket/image.part.00
Deleted myawsbucket/image.part.01
Deleted myawsbucket/image.part.02
Deleted myawsbucket/image.part.03
Deleted myawsbucket/image.part.04
Deleted myawsbucket/image.part.05
Deleted myawsbucket/image.part.06
ec2-delete-bundle complete.
```

## ec2\-download\-bundle<a name="ami-download-bundle"></a>

### Description<a name="download-bundle-description"></a>

Downloads the specified instance store\-backed Linux AMIs from Amazon S3 storage\.

### Syntax<a name="download-bundle-request"></a>

**ec2\-download\-bundle \-b *bucket* \-a *access\_key\_id* \-s *secret\_access\_key* \-k *path* \[\-\-url *url*\] \[\-\-region *region*\] \[\-\-sigv *version*\] \[\-m *file*\] \[\-p *prefix*\] \[\-d *directory*\] \[\-\-retry\]** 

### Options<a name="download-bundle-parameters"></a>

`-b, --bucket` *bucket*  
The name of the Amazon S3 bucket where the bundle is located, followed by an optional '/'\-delimited path prefix\.  
Required: Yes

`-a, --access-key` *access\_key\_id*  
The AWS access key ID\.  
Required: Yes

`-s, --secret-key` *secret\_access\_key*  
The AWS secret access key\.  
Required: Yes

`-k, --privatekey` *path*  
The private key used to decrypt the manifest\.  
Required: Yes

`--url` *url*  
The Amazon S3 service URL\.  
Default: `https://s3.amazonaws.com/`  
Required: No

`--region` *region*  
The region to use in the request signature\.  
Default: `us-east-1`  
Required: Required if using signature version 4

`--sigv` *version*  
The signature version to use when signing the request\.  
Valid values: `2` \| `4`  
Default: `4`  
Required: No

`-m, --manifest` *file*  
The name of the manifest file \(without the path\)\. We recommend that you specify either the manifest \(`-m`\) or a prefix \(`-p`\)\.  
Required: No

`-p, --prefix ` *prefix*  
The filename prefix for the bundled AMI files\.  
Default: `image`  
Required: No

`-d, --directory ` *directory*  
The directory where the downloaded bundle is saved\. The directory must exist\.  
Default: The current working directory\.  
Required: No

 `--retry`   
Automatically retries on all Amazon S3 errors, up to five times per operation\.  
Required: No

### Output<a name="download-bundle-output"></a>

Status messages indicating the various stages of the download process are displayed\.

### Example<a name="download-bundle-response"></a>

This example creates the `bundled` directory \(using the Linux mkdir command\) and downloads the bundle from the `myawsbucket` Amazon S3 bucket\.

```
[ec2-user ~]$ mkdir bundled
[ec2-user ~]$ ec2-download-bundle -b myawsbucket/bundles/bundle_name -m image.manifest.xml -a your_access_key_id -s your_secret_access_key -k pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -d mybundle
Downloading manifest image.manifest.xml from myawsbucket to mybundle/image.manifest.xml ...
Downloading part image.part.00 from myawsbucket/bundles/bundle_name to mybundle/image.part.00 ...
Downloaded image.part.00 from myawsbucket
Downloading part image.part.01 from myawsbucket/bundles/bundle_name to mybundle/image.part.01 ...
Downloaded image.part.01 from myawsbucket
Downloading part image.part.02 from myawsbucket/bundles/bundle_name to mybundle/image.part.02 ...
Downloaded image.part.02 from myawsbucket
Downloading part image.part.03 from myawsbucket/bundles/bundle_name to mybundle/image.part.03 ...
Downloaded image.part.03 from myawsbucket
Downloading part image.part.04 from myawsbucket/bundles/bundle_name to mybundle/image.part.04 ...
Downloaded image.part.04 from myawsbucket
Downloading part image.part.05 from myawsbucket/bundles/bundle_name to mybundle/image.part.05 ...
Downloaded image.part.05 from myawsbucket
Downloading part image.part.06 from myawsbucket/bundles/bundle_name to mybundle/image.part.06 ...
Downloaded image.part.06 from myawsbucket
```

## ec2\-migrate\-manifest<a name="ami-migrate-manifest"></a>

### Description<a name="migrate-manifest-description"></a>

Modifies an instance store\-backed Linux AMI \(for example, its certificate, kernel, and RAM disk\) so that it supports a different region\.

### Syntax<a name="migrate-manifest-request"></a>

**ec2\-migrate\-manifest \-c *path* \-k *path* \-m *path* \{\(\-a *access\_key\_id* \-s *secret\_access\_key* \-\-region *region*\) \| \(\-\-no\-mapping\)\} \[\-\-ec2cert *ec2\_cert\_path*\] \[\-\-kernel *kernel\-id*\] \[\-\-ramdisk *ramdisk\_id*\]** 

### Options<a name="migrate-manifest-parameters"></a>

`-c, --cert` *path*  
The user's PEM encoded RSA public key certificate file\.  
Required: Yes

`-k, --privatekey` *path*  
The path to the user's PEM\-encoded RSA key file\.  
Required: Yes

`--manifest` *path*  
The path to the manifest file\.  
Required: Yes

`-a, --access-key` *access\_key\_id*  
The AWS access key ID\.  
Required: Required if using automatic mapping\.

`-s, --secret-key ` *secret\_access\_key*  
The AWS secret access key\.  
Required: Required if using automatic mapping\.

`--region` *region*  
The region to look up in the mapping file\.  
Required: Required if using automatic mapping\.

`--no-mapping`  
Disables automatic mapping of kernels and RAM disks\.  
 During migration, Amazon EC2 replaces the kernel and RAM disk in the manifest file with a kernel and RAM disk designed for the destination region\. Unless the `--no-mapping` parameter is given, `ec2-migrate-bundle` might use the `DescribeRegions` and `DescribeImages` operations to perform automated mappings\.   
Required: Required if you're not providing the `-a`, `-s`, and `--region` options used for automatic mapping\.

`--ec2cert` *path*  
The path to the Amazon EC2 X\.509 public key certificate used to encrypt the image manifest\.  
The `us-gov-west-1` and `cn-north-1` regions use a non\-default public key certificate and the path to that certificate must be specified with this option\. The path to the certificate varies based on the installation method of the AMI tools\. For Amazon Linux, the certificates are located at `/opt/aws/amitools/ec2/etc/ec2/amitools/`\. If you installed the AMI tools from the ZIP file in [Setting Up the AMI Tools](set-up-ami-tools.md), the certificates are located at `$EC2_AMITOOL_HOME/etc/ec2/amitools/`\.  
Required: Only for the `us-gov-west-1` and `cn-north-1` regions\.

`--kernel` *kernel\_id*  
The ID of the kernel to select\.  
We recommend that you use PV\-GRUB instead of kernels and RAM disks\. For more information, see [User Provided Kernels](UserProvidedKernels.md)\.
Required: No

`--ramdisk` *ramdisk\_id*  
The ID of the RAM disk to select\.  
We recommend that you use PV\-GRUB instead of kernels and RAM disks\. For more information, see [User Provided Kernels](UserProvidedKernels.md)\.
Required: No

### Output<a name="migrate-manifest-output"></a>

Status messages describing the stages and status of the bundling process\.

### Example<a name="migrate-manifest-response"></a>

This example copies the AMI specified in the `my-ami.manifest.xml` manifest from the US to the EU\.

```
[ec2-user ~]$ ec2-migrate-manifest --manifest my-ami.manifest.xml --cert cert-HKZYKTAIG2ECMXYIBH3HXV4ZBZQ55CLO.pem --privatekey pk-HKZYKTAIG2ECMXYIBH3HXV4ZBZQ55CLO.pem --region eu-west-1 

Backing up manifest...
Successfully migrated my-ami.manifest.xml It is now suitable for use in eu-west-1.
```

## ec2\-unbundle<a name="ami-unbundle"></a>

### Description<a name="unbundle-description"></a>

Re\-creates the bundle from an instance store\-backed Linux AMI\.

### Syntax<a name="unbundle-request"></a>

**ec2\-unbundle \-k *path* \-m *path* \[\-s *source\_directory*\] \[\-d *destination\_directory*\]** 

### Options<a name="unbundle-parameters"></a>

`-k, --privatekey` *path*  
The path to your PEM\-encoded RSA key file\.  
Required: Yes

`-m, --manifest` *path*  
The path to the manifest file\.  
Required: Yes

`-s, --source` *source\_directory*  
The directory containing the bundle\.  
Default: The current directory\.  
Required: No

`-d, --destination` *destination\_directory*  
The directory in which to unbundle the AMI\. The destination directory must exist\.   
Default: The current directory\.  
Required: No

### Example<a name="unbundle-response"></a>

This Linux and UNIX example unbundles the AMI specified in the `image.manifest.xml` file\.

```
[ec2-user ~]$ mkdir unbundled
$ ec2-unbundle -m mybundle/image.manifest.xml -k pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -s mybundle -d unbundled
$ ls -l unbundled
total 1025008
-rw-r--r-- 1 root root 1048578048 Aug 25 23:46 image.img
```

### Output<a name="unbundle-output"></a>

Status messages indicating the various stages of the unbundling process are displayed\.

## ec2\-upload\-bundle<a name="ami-upload-bundle"></a>

### Description<a name="upload-bundle-description"></a>

Uploads the bundle for an instance store\-backed Linux AMI to Amazon S3 and sets the appropriate ACLs on the uploaded objects\. For more information, see [Creating an Instance Store\-Backed Linux AMI](creating-an-ami-instance-store.md)\.

### Syntax<a name="upload-bundle-request"></a>

**ec2\-upload\-bundle \-b *bucket* \-a *access\_key\_id* \-s *secret\_access\_key* \[\-t *token*\] \-m *path* \[\-\-url *url*\] \[\-\-region *region*\] \[\-\-sigv *version*\] \[\-\-acl *acl*\] \[\-d *directory*\] \[\-\-part *part*\] \[\-\-retry\] \[\-\-skipmanifest\]** 

### Options<a name="upload-bundle-parameters"></a>

`-b, --bucket` *bucket*  
The name of the Amazon S3 bucket in which to store the bundle, followed by an optional '/'\-delimited path prefix\. If the bucket doesn't exist, it's created if the bucket name is available\.  
Required: Yes

`-a, --access-key` *access\_key\_id*  
Your AWS access key ID\.  
Required: Yes

`-s, --secret-key` *secret\_access\_key*  
Your AWS secret access key\.  
Required: Yes

`-t, --delegation-token` *token*  
The delegation token to pass along to the AWS request\. For more information, see the [Using Temporary Security Credentials](http://docs.aws.amazon.com/STS/latest/UsingSTS/)\.  
Required: Only when you are using temporary security credentials\.  
Default: The value of the `AWS_DELEGATION_TOKEN` environment variable \(if set\)\.

`-m, --manifest` *path*  
The path to the manifest file\. The manifest file is created during the bundling process and can be found in the directory containing the bundle\.  
Required: Yes

`--url` *url*  
Deprecated\. Use the `--region` option instead unless your bucket is constrained to the `EU` location \(and not `eu-west-1`\)\. The `--location` flag is the only way to target that specific location restraint\.  
The Amazon S3 endpoint service URL\.  
Default: `https://s3.amazonaws.com/`  
Required: No

`--region` *region*  
The region to use in the request signature for the destination S3 bucket\.  
+ If the bucket doesn't exist and you don't specify a region, the tool creates the bucket without a location constraint \(in `us-east-1`\)\.
+ If the bucket doesn't exist and you specify a region, the tool creates the bucket in the specified region\.
+ If the bucket exists and you don't specify a region, the tool uses the bucket's location\.
+ If the bucket exists and you specify `us-east-1` as the region, the tool uses the bucket's actual location without any error message, any existing matching files are over\-written\.
+ If the bucket exists and you specify a region \(other than `us-east-1`\) that doesn't match the bucket's actual location, the tool exits with an error\.
If your bucket is constrained to the `EU` location \(and not `eu-west-1`\), use the `--location` flag instead\. The `--location` flag is the only way to target that specific location restraint\.  
Default: `us-east-1`  
Required: Required if using signature version 4

`--sigv` *version*  
The signature version to use when signing the request\.  
Valid values: `2` \| `4`  
Default: `4`  
Required: No

`--acl` *acl*  
The access control list policy of the bundled image\.  
Valid values: `public-read` \| `aws-exec-read`  
Default: `aws-exec-read`  
Required: No

`-d, --directory` *directory*  
The directory containing the bundled AMI parts\.  
Default: The directory containing the manifest file \(see the `-m` option\)\.  
Required: No

`--part` *part*  
Starts uploading the specified part and all subsequent parts\. For example, `--part 04`\.  
Required: No

`--retry`  
Automatically retries on all Amazon S3 errors, up to five times per operation\.  
Required: No

`--skipmanifest`  
Does not upload the manifest\.  
Required: No

`--location` *location*  
Deprecated\. Use the `--region` option instead, unless your bucket is constrained to the `EU` location \(and not `eu-west-1`\)\. The `--location` flag is the only way to target that specific location restraint\.  
The location constraint of the destination Amazon S3 bucket\. If the bucket exists and you specify a location that doesn't match the bucket's actual location, the tool exits with an error\. If the bucket exists and you don't specify a location, the tool uses the bucket's location\. If the bucket doesn't exist and you specify a location, the tool creates the bucket in the specified location\. If the bucket doesn't exist and you don't specify a location, the tool creates the bucket without a location constraint \(in `us-east-1`\)\.   
Default: If `--region` is specified, the location is set to that specified region\. If `--region` is not specified, the location defaults to `us-east-1`\.  
Required: No

### Output<a name="upload-bundle-output"></a>

Amazon EC2 displays status messages that indicate the stages and status of the upload process\.

### Example<a name="upload-bundle-response"></a>

This example uploads the bundle specified by the `image.manifest.xml` manifest\.

```
[ec2-user ~]$ ec2-upload-bundle -b myawsbucket/bundles/bundle_name -m image.manifest.xml -a your_access_key_id -s your_secret_access_key
Creating bucket...
Uploading bundled image parts to the S3 bucket myawsbucket ...
Uploaded image.part.00
Uploaded image.part.01
Uploaded image.part.02
Uploaded image.part.03
Uploaded image.part.04
Uploaded image.part.05
Uploaded image.part.06
Uploaded image.part.07
Uploaded image.part.08
Uploaded image.part.09
Uploaded image.part.10
Uploaded image.part.11
Uploaded image.part.12
Uploaded image.part.13
Uploaded image.part.14
Uploading manifest ...
Uploaded manifest.
Bundle upload completed.
```

## Common Options for AMI Tools<a name="common-args-ami"></a>

Most of the AMI tools accept the following optional parameters\.

`--help, -h`  
Displays the help message\.

`--version`  
Displays the version and copyright notice\.

`--manual`  
Displays the manual entry\.

`--batch`  
Runs in batch mode, suppressing interactive prompts\.

`--debug`  
Displays information that can be useful when troubleshooting problems\.
# Creating an AMI from an Instance Store\-Backed Instance<a name="create-instance-store-ami"></a>

The following procedures are for creating an instance store\-backed AMI from an instance store\-backed instance\. Before you begin, ensure that you've read the [Prerequisites](creating-an-ami-instance-store.md#bundle-ami-prerequisites)\.

**Topics**
+ [Creating an AMI from an Instance Store\-Backed Amazon Linux Instance](#amazon_linux_instructions)
+ [Creating an AMI from an Instance Store\-Backed Ubuntu Instance](#ubuntu_instructions)

## Creating an AMI from an Instance Store\-Backed Amazon Linux Instance<a name="amazon_linux_instructions"></a>

This section describes the creation of an AMI from an Amazon Linux instance\. The following procedures may not work for instances running other Linux distributions\. For Ubuntu\-specific procedures, see [Creating an AMI from an Instance Store\-Backed Ubuntu Instance](#ubuntu_instructions)\.

**To prepare to use the AMI tools \(HVM instances only\)**

1. The AMI tools require GRUB Legacy to boot properly\. Use the following command to install GRUB:

   ```
   [ec2-user ~]$ sudo yum install -y grub
   ```

1. Install the partition management packages with the following command:

   ```
   [ec2-user ~]$ sudo yum install -y gdisk kpartx parted
   ```

**To create an AMI from an instance store\-backed Amazon Linux instance**

This procedure assumes that you have satisfied the prerequisites in [Prerequisites](creating-an-ami-instance-store.md#bundle-ami-prerequisites)\.

1. Upload your credentials to your instance\. We use these credentials to ensure that only you and Amazon EC2 can access your AMI\.

   1. Create a temporary directory on your instance for your credentials as follows:

      ```
      [ec2-user ~]$ mkdir /tmp/cert
      ```

      This enables you to exclude your credentials from the created image\.

   1. Copy your X\.509 certificate and corresponding private key from your computer to the `/tmp/cert` directory on your instance using a secure copy tool such as [scp](AccessingInstancesLinux.md#AccessingInstancesLinuxSCP)\. The `-i my-private-key.pem` option in the following scp command is the private key you use to connect to your instance with SSH, not the X\.509 private key\. For example:

      ```
      you@your_computer:~ $ scp -i my-private-key.pem /path/to/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem /path/to/cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem ec2-user@ec2-203-0-113-25.compute-1.amazonaws.com:/tmp/cert/
      pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem  100%  717     0.7KB/s   00:00
      cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem  100%  685     0.7KB/s   00:00
      ```

   Alternatively, because these are plain text files, you can open the certificate and key in a text editor and copy their contents into new files in `/tmp/cert`\.

1. <a name="step_with_bundle_path_amazon_linux"></a>Prepare the bundle to upload to Amazon S3 by running the [ec2\-bundle\-vol](ami-tools-commands.md#ami-bundle-vol) command from inside your instance\. Be sure to specify the `-e` option to exclude the directory where your credentials are stored\. By default, the bundle process excludes files that might contain sensitive information\. These files include `*.sw`, `*.swo`, `*.swp`, `*.pem`, `*.priv`, `*id_rsa*`, `*id_dsa*` `*.gpg`, `*.jks`, `*/.ssh/authorized_keys`, and `*/.bash_history`\. To include all of these files, use the `--no-filter` option\. To include some of these files, use the `--include` option\.
**Important**  
By default, the AMI bundling process creates a compressed, encrypted collection of files in the `/tmp` directory that represents your root volume\. If you do not have enough free disk space in `/tmp` to store the bundle, you need to specify a different location for the bundle to be stored with the `-d /path/to/bundle/storage` option\. Some instances have ephemeral storage mounted at `/mnt` or `/media/ephemeral0` that you can use, or you can also [create](ebs-creating-volume.md), [attach](ebs-attaching-volume.md), and [mount](ebs-using-volumes.md) a new Amazon EBS volume to store the bundle\.

   1. You must run the ec2\-bundle\-vol command as root\. For most commands, you can use sudo to gain elevated permissions, but in this case, you should run sudo \-E su to keep your environment variables\.

      ```
      [ec2-user ~]$ sudo -E su
      ```

      Note that bash prompt now identifies you as the root user, and that the dollar sign has been replaced by a hash tag, signalling that you are in a root shell:

      ```
      [root ec2-user]#
      ```

   1. To create the AMI bundle, run the [ec2\-bundle\-vol](ami-tools-commands.md#ami-bundle-vol) command as follows:

      ```
      [root ec2-user]# ec2-bundle-vol -k /tmp/cert/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -c /tmp/cert/cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -u 123456789012 -r x86_64 -e /tmp/cert --partition gpt
      ```
**Note**  
For the China \(Beijing\) and AWS GovCloud \(US\) regions, use the `--ec2cert` parameter and specify the certificates as per the [prerequisites](creating-an-ami-instance-store.md#bundle-ami-prerequisites)\.

      It can take a few minutes to create the image\. When this command completes, your `/tmp` \(or non\-default\) directory contains the bundle \(`image.manifest.xml`, plus multiple `image.part.`*xx* files\)\.

   1. Exit from the root shell\.

      ```
      [root ec2-user]# exit
      ```

1. \(Optional\) To add more instance store volumes, edit the block device mappings in the `image.manifest.xml` file for your AMI\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

   1. Create a backup of your `image.manifest.xml` file\.

      ```
      [ec2-user ~]$ sudo cp /tmp/image.manifest.xml /tmp/image.manifest.xml.bak
      ```

   1. Reformat the `image.manifest.xml` file so that it is easier to read and edit\.

      ```
      [ec2-user ~]$ sudo xmllint --format /tmp/image.manifest.xml.bak > sudo /tmp/image.manifest.xml
      ```

   1. Edit the block device mappings in `image.manifest.xml` with a text editor\. The example below shows a new entry for the `ephemeral1` instance store volume\.

      ```
          <block_device_mapping>
            <mapping>
              <virtual>ami</virtual>
              <device>sda</device>
            </mapping>
            <mapping>
              <virtual>ephemeral0</virtual>
              <device>sdb</device>
            </mapping>
            <mapping>
              <virtual>ephemeral1</virtual>
              <device>sdc</device>
            </mapping>
            <mapping>
              <virtual>root</virtual>
              <device>/dev/sda1</device>
            </mapping>
          </block_device_mapping>
      ```

   1. Save the `image.manifest.xml` file and exit your text editor\.

1. To upload your bundle to Amazon S3, run the [ec2\-upload\-bundle](ami-tools-commands.md#ami-upload-bundle) command as follows\.

   ```
   [ec2-user ~]$ ec2-upload-bundle -b my-s3-bucket/bundle_folder/bundle_name -m /tmp/image.manifest.xml -a your_access_key_id -s your_secret_access_key
   ```
**Important**  
To register your AMI in a region other than US East \(N\. Virginia\), you must specify both the target region with the `--region` option and a bucket path that already exists in the target region or a unique bucket path that can be created in the target region\.

1. \(Optional\) After the bundle is uploaded to Amazon S3, you can remove the bundle from the `/tmp` directory on the instance using the following rm command:

   ```
   [ec2-user ~]$ sudo rm /tmp/image.manifest.xml /tmp/image.part.* /tmp/image
   ```
**Important**  
If you specified a path with the `-d /path/to/bundle/storage` option in [Step 2](#step_with_bundle_path_amazon_linux), use that path instead of `/tmp`\.

1. To register your AMI, run the [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command as follows\.

   ```
   [ec2-user ~]$ aws ec2 register-image --image-location my-s3-bucket/bundle_folder/bundle_name/image.manifest.xml --name AMI_name --virtualization-type hvm
   ```
**Important**  
If you previously specified a region for the [ec2\-upload\-bundle](ami-tools-commands.md#ami-upload-bundle) command, specify that region again for this command\.

## Creating an AMI from an Instance Store\-Backed Ubuntu Instance<a name="ubuntu_instructions"></a>

This section describes the creation of an AMI from an Ubuntu Linux instance\. The following procedures may not work for instances running other Linux distributions\. For procedures specific to Amazon Linux, see [Creating an AMI from an Instance Store\-Backed Amazon Linux Instance](#amazon_linux_instructions)\.

**To prepare to use the AMI Tools \(HVM instances only\)**

The AMI tools require GRUB Legacy to boot properly\. However, Ubuntu is configured to use GRUB 2\. You must check to see that your instance uses GRUB Legacy, and if not, you need to install and configure it\.

HVM instances also require partitioning tools to be installed for the AMI tools to work properly\.

1. GRUB Legacy \(version 0\.9*x* or less\) must be installed on your instance\. Check to see if GRUB Legacy is present and install it if necessary\.

   1. Check the version of your GRUB installation\.

      ```
      ubuntu:~$ grub-install --version
      grub-install (GRUB) 1.99-21ubuntu3.10
      ```

      In this example, the GRUB version is greater than 0\.9*x*, so GRUB Legacy must be installed\. Proceed to [Step 2](#grub-install-step)\. If GRUB Legacy is already present, you can skip to [Step 2](#partition)\.

   1. <a name="grub-install-step"></a>Install the `grub` package using the following command\.

      ```
      ubuntu:~$ sudo apt-get install -y grub
      ```

      Verify that your instance is using GRUB Legacy\.

      ```
      ubuntu:~$ grub --version
      grub (GNU GRUB 0.97)
      ```

1. <a name="partition"></a>Install the following partition management packages using the package manager for your distribution\. 
   + `gdisk` \(some distributions may call this package `gptfdisk` instead\) 
   + `kpartx`
   + `parted`

   Use the following command\.

   ```
   ubuntu:~$ sudo apt-get install -y gdisk kpartx parted
   ```

1. Check the kernel parameters for your instance\.

   ```
   ubuntu:~$ cat /proc/cmdline
   BOOT_IMAGE=/boot/vmlinuz-3.2.0-54-virtual root=UUID=4f392932-ed93-4f8f-aee7-72bc5bb6ca9d ro console=ttyS0 xen_emul_unplug=unnecessary
   ```

   Note the options following the kernel and root device parameters: `ro`, `console=ttyS0`, and `xen_emul_unplug=unnecessary`\. Your options may differ\.

1. Check the kernel entries in `/boot/grub/menu.lst`\.

   ```
   ubuntu:~$ grep ^kernel /boot/grub/menu.lst
   kernel		/boot/vmlinuz-3.2.0-54-virtual root=LABEL=cloudimg-rootfs ro console=hvc0
   kernel		/boot/vmlinuz-3.2.0-54-virtual root=LABEL=cloudimg-rootfs ro single
   kernel		/boot/memtest86+.bin
   ```

   Note that the `console` parameter is pointing to `hvc0` instead of `ttyS0` and that the `xen_emul_unplug=unnecessary` parameter is missing\. Again, your options may differ\.

1. Edit the `/boot/grub/menu.lst` file with your favorite text editor \(such as vim or nano\) to change the console and add the parameters you identified earlier to the boot entries\.

   ```
   title           Ubuntu 12.04.3 LTS, kernel 3.2.0-54-virtual
   root            (hd0)
   kernel          /boot/vmlinuz-3.2.0-54-virtual root=LABEL=cloudimg-rootfs ro console=ttyS0 xen_emul_unplug=unnecessary
   initrd          /boot/initrd.img-3.2.0-54-virtual
   
   title           Ubuntu 12.04.3 LTS, kernel 3.2.0-54-virtual (recovery mode)
   root            (hd0)
   kernel          /boot/vmlinuz-3.2.0-54-virtual root=LABEL=cloudimg-rootfs ro single console=ttyS0 xen_emul_unplug=unnecessary
   initrd          /boot/initrd.img-3.2.0-54-virtual
   
   title           Ubuntu 12.04.3 LTS, memtest86+
   root            (hd0)
   kernel          /boot/memtest86+.bin
   ```

1. Verify that your kernel entries now contain the correct parameters\.

   ```
   ubuntu:~$ grep ^kernel /boot/grub/menu.lst
   kernel		/boot/vmlinuz-3.2.0-54-virtual root=LABEL=cloudimg-rootfs ro console=ttyS0 xen_emul_unplug=unnecessary
   kernel		/boot/vmlinuz-3.2.0-54-virtual root=LABEL=cloudimg-rootfs ro single console=ttyS0 xen_emul_unplug=unnecessary
   kernel		/boot/memtest86+.bin
   ```

1. \[For Ubuntu 14\.04 and later only\] Starting with Ubuntu 14\.04, instance store backed Ubuntu AMIs use a GPT partition table and a separate EFI partition mounted at `/boot/efi`\. The ec2\-bundle\-vol command will not bundle this boot partition, so you need to comment out the `/etc/fstab` entry for the EFI partition as shown in the following example\.

   ```
   LABEL=cloudimg-rootfs   /        ext4   defaults        0 0
   #LABEL=UEFI     /boot/efi       vfat    defaults        0 0
   /dev/xvdb       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2
   ```

**To create an AMI from an instance store\-backed Ubuntu instance**

This procedure assumes that you have satisfied the prerequisites in [Prerequisites](creating-an-ami-instance-store.md#bundle-ami-prerequisites)\.

1. Upload your credentials to your instance\. We use these credentials to ensure that only you and Amazon EC2 can access your AMI\.

   1. Create a temporary directory on your instance for your credentials as follows:

      ```
      ubuntu:~$ mkdir /tmp/cert
      ```

      This enables you to exclude your credentials from the created image\.

   1. Copy your X\.509 certificate and private key from your computer to the `/tmp/cert` directory on your instance, using a secure copy tool such as [scp](AccessingInstancesLinux.md#AccessingInstancesLinuxSCP)\. The `-i my-private-key.pem` option in the following scp command is the private key you use to connect to your instance with SSH, not the X\.509 private key\. For example:

      ```
      you@your_computer:~ $ scp -i my-private-key.pem /path/to/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem /path/to/cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem ec2-user@ec2-203-0-113-25.compute-1.amazonaws.com:/tmp/cert/
      pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem  100%  717     0.7KB/s   00:00
      cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem  100%  685     0.7KB/s   00:00
      ```

   Alternatively, because these are plain text files, you can open the certificate and key in a text editor and copy their contents into new files in `/tmp/cert`\.

1. <a name="step_with_bundle_path_ubuntu"></a>Prepare the bundle to upload to Amazon S3 by running the [ec2\-bundle\-vol](ami-tools-commands.md#ami-bundle-vol) command from your instance\. Be sure to specify the `-e` option to exclude the directory where your credentials are stored\. By default, the bundle process excludes files that might contain sensitive information\. These files include `*.sw`, `*.swo`, `*.swp`, `*.pem`, `*.priv`, `*id_rsa*`, `*id_dsa*` `*.gpg`, `*.jks`, `*/.ssh/authorized_keys`, and `*/.bash_history`\. To include all of these files, use the `--no-filter` option\. To include some of these files, use the `--include` option\.
**Important**  
By default, the AMI bundling process creates a compressed, encrypted collection of files in the `/tmp` directory that represents your root volume\. If you do not have enough free disk space in `/tmp` to store the bundle, you need to specify a different location for the bundle to be stored with the `-d /path/to/bundle/storage` option\. Some instances have ephemeral storage mounted at `/mnt` or `/media/ephemeral0` that you can use, or you can also [create](ebs-creating-volume.md), [attach](ebs-attaching-volume.md), and [mount](ebs-using-volumes.md) a new Amazon EBS volume to store the bundle\.

   1. You must run the ec2\-bundle\-vol command needs as root\. For most commands, you can use sudo to gain elevated permissions, but in this case, you should run sudo \-E su to keep your environment variables\.

      ```
      ubuntu:~$ sudo -E su
      ```

      Note that bash prompt now identifies you as the root user, and that the dollar sign has been replaced by a hash tag, signalling that you are in a root shell:

      ```
      root@ubuntu:#
      ```

   1. <a name="create_bundle_Ubuntu_Linux_step"></a>To create the AMI bundle, run the [ec2\-bundle\-vol](ami-tools-commands.md#ami-bundle-vol) command as follows\.

      ```
      root@ubuntu:# ec2-bundle-vol -k /tmp/cert/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -c /tmp/cert/cert-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -u your_aws_account_id -r x86_64 -e /tmp/cert --partition gpt
      ```
**Important**  
For Ubuntu 14\.04 and later HVM instances, add the `--partition mbr` flag to bundle the boot instructions properly; otherwise, your newly\-created AMI will not boot\.

      It can take a few minutes to create the image\. When this command completes, your `tmp` directory contains the bundle \(`image.manifest.xml`, plus multiple `image.part.`*xx* files\)\.

   1. Exit from the root shell\.

      ```
      root@ubuntu:# exit
      ```

1. \(Optional\) To add more instance store volumes, edit the block device mappings in the `image.manifest.xml` file for your AMI\. For more information, see [Block Device Mapping](block-device-mapping-concepts.md)\.

   1. Create a backup of your `image.manifest.xml` file\.

      ```
      ubuntu:~$ sudo cp /tmp/image.manifest.xml /tmp/image.manifest.xml.bak
      ```

   1. Reformat the `image.manifest.xml` file so that it is easier to read and edit\.

      ```
      ubuntu:~$ sudo xmllint --format /tmp/image.manifest.xml.bak > /tmp/image.manifest.xml
      ```

   1. Edit the block device mappings in `image.manifest.xml` with a text editor\. The example below shows a new entry for the *ephemeral1* instance store volume\.

      ```
          <block_device_mapping>
            <mapping>
              <virtual>ami</virtual>
              <device>sda</device>
            </mapping>
            <mapping>
              <virtual>ephemeral0</virtual>
              <device>sdb</device>
            </mapping>
            <mapping>
              <virtual>ephemeral1</virtual>
              <device>sdc</device>
            </mapping>
            <mapping>
              <virtual>root</virtual>
              <device>/dev/sda1</device>
            </mapping>
          </block_device_mapping>
      ```

   1. Save the `image.manifest.xml` file and exit your text editor\.

1. To upload your bundle to Amazon S3, run the [ec2\-upload\-bundle](ami-tools-commands.md#ami-upload-bundle) command as follows\.

   ```
   ubuntu:~$ ec2-upload-bundle -b my-s3-bucket/bundle_folder/bundle_name -m /tmp/image.manifest.xml -a your_access_key_id -s your_secret_access_key
   ```
**Important**  
If you intend to register your AMI in a region other than US East \(N\. Virginia\), you must specify both the target region with the `--region` option and a bucket path that already exists in the target region or a unique bucket path that can be created in the target region\.

1. \(Optional\) After the bundle is uploaded to Amazon S3, you can remove the bundle from the `/tmp` directory on the instance using the following rm command:

   ```
   ubuntu:~$ sudo rm /tmp/image.manifest.xml /tmp/image.part.* /tmp/image
   ```
**Important**  
If you specified a path with the `-d /path/to/bundle/storage` option in [Step 2](#step_with_bundle_path_ubuntu), use that same path below, instead of `/tmp`\.

1. To register your AMI, run the [register\-image](http://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) AWS CLI command as follows\.

   ```
   ubuntu:~$ aws ec2 register-image --image-location my-s3-bucket/bundle_folder/bundle_name/image.manifest.xml --name AMI_name --virtualization-type hvm
   ```
**Important**  
If you previously specified a region for the [ec2\-upload\-bundle](ami-tools-commands.md#ami-upload-bundle) command, specify that region again for this command\.

1. \[Ubuntu 14\.04 and later\] Uncomment the EFI entry in `/etc/fstab`; otherwise, your running instance will not be able to reboot\.
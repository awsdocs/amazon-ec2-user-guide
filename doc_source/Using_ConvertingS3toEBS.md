# Convert your instance store\-backed AMI to an Amazon EBS\-backed AMI<a name="Using_ConvertingS3toEBS"></a>

You can convert an instance store\-backed Linux AMI that you own to an Amazon EBS\-backed Linux AMI\. 

**Important**  
You can't convert an instance store\-backed Windows AMI to an Amazon EBS\-backed Windows AMI and you cannot convert an AMI that you do not own\.

**To convert an instance store\-backed AMI to an Amazon EBS\-backed AMI**

1. Launch an Amazon Linux instance from an Amazon EBS\-backed AMI\. For more information, see [Launch an instance using the old launch instance wizard](launching-instance.md)\. Amazon Linux instances have the AWS CLI and AMI tools pre\-installed\.

1. Upload the X\.509 private key that you used to bundle your instance store\-backed AMI to your instance\. We use this key to ensure that only you and Amazon EC2 can access your AMI\.

   1. Create a temporary directory on your instance for your X\.509 private key as follows:

      ```
      [ec2-user ~]$ mkdir /tmp/cert
      ```

   1. Copy your X\.509 private key from your computer to the `/tmp/cert` directory on your instance, using a secure copy tool such as [scp](AccessingInstancesLinux.md#AccessingInstancesLinuxSCP)\. The *my\-private\-key* parameter in the following command is the private key you use to connect to your instance with SSH\. For example:

      ```
      you@your_computer:~ $ scp -i my-private-key.pem /path/to/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem ec2-user@ec2-203-0-113-25.compute-1.amazonaws.com:/tmp/cert/
      pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem  100%  717     0.7KB/s   00:00
      ```

1. Set environment variables for your AWS access key and secret key\.

   ```
   [ec2-user ~]$ export AWS_ACCESS_KEY_ID=your_access_key_id
   [ec2-user ~]$ export AWS_SECRET_ACCESS_KEY=your_secret_access_key
   ```

1. Prepare an Amazon Elastic Block Store \(Amazon EBS\) volume for your new AMI\.

   1. Create an empty EBS volume in the same Availability Zone as your instance using the [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command\. Note the volume ID in the command output\.
**Important**  
 This EBS volume must be the same size or larger than the original instance store root volume\.

      ```
      [ec2-user ~]$ aws ec2 create-volume --size 10 --region us-west-2 --availability-zone us-west-2b						
      ```

   1. Attach the volume to your Amazon EBS\-backed instance using the [attach\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/attach-volume.html) command\.

      ```
      [ec2-user ~]$ aws ec2 attach-volume --volume-id volume_id --instance-id instance_id --device /dev/sdb --region us-west-2
      ```

1. Create a folder for your bundle\.

   ```
   [ec2-user ~]$ mkdir /tmp/bundle
   ```

1. Download the bundle for your instance store\-based AMI to `/tmp/bundle` using the [ec2\-download\-bundle](ami-tools-commands.md#ami-download-bundle) command\.

   ```
   [ec2-user ~]$ ec2-download-bundle -b my-s3-bucket/bundle_folder/bundle_name -m image.manifest.xml -a $AWS_ACCESS_KEY_ID -s $AWS_SECRET_ACCESS_KEY --privatekey /path/to/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem -d /tmp/bundle
   ```

1. Reconstitute the image file from the bundle using the [ec2\-unbundle](ami-tools-commands.md#ami-unbundle) command\.

   1. Change directories to the bundle folder\.

      ```
      [ec2-user ~]$ cd /tmp/bundle/
      ```

   1. Run the [ec2\-unbundle](ami-tools-commands.md#ami-unbundle) command\.

      ```
      [ec2-user bundle]$ ec2-unbundle -m image.manifest.xml --privatekey /path/to/pk-HKZYKTAIG2ECMXYIBH3HXV4ZBEXAMPLE.pem
      ```

1. Copy the files from the unbundled image to the new EBS volume\.

   ```
   [ec2-user bundle]$ sudo dd if=/tmp/bundle/image of=/dev/sdb bs=1M
   ```

1. Probe the volume for any new partitions that were unbundled\.

   ```
   [ec2-user bundle]$ sudo partprobe /dev/sdb1
   ```

1. List the block devices to find the device name to mount\.

   ```
   [ec2-user bundle]$ lsblk
   NAME         MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   /dev/sda    202:0    0   8G  0 disk
   └─/dev/sda1 202:1    0   8G  0 part /
   /dev/sdb    202:80   0  10G  0 disk
   └─/dev/sdb1 202:81   0  10G  0 part
   ```

   In this example, the partition to mount is `/dev/sdb1`, but your device name will likely be different\. If your volume is not partitioned, then the device to mount will be similar to `/dev/sdb` \(without a device partition trailing digit\)\.

1. Create a mount point for the new EBS volume and mount the volume\.

   ```
   [ec2-user bundle]$ sudo mkdir /mnt/ebs
   [ec2-user bundle]$ sudo mount /dev/sdb1 /mnt/ebs
   ```

1. Open the `/etc/fstab` file on the EBS volume with your favorite text editor \(such as vim or nano\) and remove any entries for instance store \(ephemeral\) volumes\. Because the EBS volume is mounted on `/mnt/ebs`, the `fstab` file is located at `/mnt/ebs/etc/fstab`\.

   ```
   [ec2-user bundle]$ sudo nano /mnt/ebs/etc/fstab
   #
   LABEL=/     /           ext4    defaults,noatime  1   1
   tmpfs       /dev/shm    tmpfs   defaults        0   0
   devpts      /dev/pts    devpts  gid=5,mode=620  0   0
   sysfs       /sys        sysfs   defaults        0   0
   proc        /proc       proc    defaults        0   0
   /dev/sdb        /media/ephemeral0       auto    defaults,comment=cloudconfig    0       2
   ```

   In this example, the last line should be removed\.

1. Unmount the volume and detach it from the instance\.

   ```
   [ec2-user bundle]$ sudo umount /mnt/ebs
   [ec2-user bundle]$ aws ec2 detach-volume --volume-id volume_id --region us-west-2
   ```

1. Create an AMI from the new EBS volume as follows\.

   1. Create a snapshot of the new EBS volume\.

      ```
      [ec2-user bundle]$ aws ec2 create-snapshot --region us-west-2 --description "your_snapshot_description" --volume-id volume_id
      ```

   1. Check to see that your snapshot is complete\.

      ```
      [ec2-user bundle]$ aws ec2 describe-snapshots --region us-west-2 --snapshot-id snapshot_id
      ```

   1. Identify the processor architecture, virtualization type, and the kernel image \(`aki`\) used on the original AMI with the describe\-images command\. You need the AMI ID of the original instance store\-backed AMI for this step\.

      ```
      [ec2-user bundle]$ aws ec2 describe-images --region us-west-2 --image-id ami-id --output text
      IMAGES	x86_64	amazon/amzn-ami-pv-2013.09.2.x86_64-s3	ami-8ef297be	amazon	available	public	machine	aki-fc8f11cc	instance-store	paravirtual	xen
      ```

      In this example, the architecture is `x86_64` and the kernel image ID is `aki-fc8f11cc`\. Use these values in the following step\. If the output of the above command also lists an `ari` ID, take note of that as well\.

   1. Register your new AMI with the snapshot ID of your new EBS volume and the values from the previous step\. If the previous command output listed an `ari` ID, include that in the following command with `--ramdisk-id ari_id`\.

      ```
      [ec2-user bundle]$ aws ec2 register-image --region us-west-2 --name your_new_ami_name --block-device-mappings DeviceName=device-name,Ebs={SnapshotId=snapshot_id} --virtualization-type paravirtual --architecture x86_64 --kernel-id aki-fc8f11cc --root-device-name device-name
      ```

1. \(Optional\) After you have tested that you can launch an instance from your new AMI, you can delete the EBS volume that you created for this procedure\.

   ```
   aws ec2 delete-volume --volume-id volume_id
   ```
# Set the boot mode of an AMI<a name="set-ami-boot-mode"></a>

When you create an AMI using the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command, you can set the boot mode of the AMI to either `uefi` or `legacy-bios`\.

To convert an existing Legacy BIOS\-based instance to UEFI, or an existing UEFI\-based instance to Legacy BIOS, you need to perform a number of steps: First, modify the instance's volume and operating system to support the selected boot mode\. Then, create a snapshot of the volume\. Finally, use [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to create the AMI using the snapshot\.

You can't set the boot mode of an AMI using the [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) command\. With [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html), the AMI inherits the boot mode of the EC2 instance used for creating the AMI\. For example, if you create an AMI from an EC2 instance running on Legacy BIOS, the AMI boot mode will be configured as `legacy-bios`\.

**Warning**  
Before proceeding with these steps, you must first make suitable modifications to the instance's volume and operating system to support booting via the selected boot mode; otherwise, the resulting AMI will not be usable\. The modifications that are required are operating system\-specific\. For more information, see the manual for your operating system\.

**To set the boot mode of an AMI \(AWS CLI version 1\.19\.34 and later and version 2\.1\.32 and later\)**

1. Make suitable modifications to the instance's volume and operating system to support booting via the selected boot mode\. The modifications that are required are operating system\-specific\. For more information, see the manual for your operating system\.
**Note**  
If you don't perform this step, the AMI will not be usable\.

1. To find the volume ID of the instance, use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\. You'll create a snapshot of this volume in the next step\.

   ```
   aws ec2 describe-instances --region us-east-1 --instance-ids i-1234567890abcdef0 
   ```

   Expected output

   ```
   ...
               "BlockDeviceMappings": [
                           {
                               "DeviceName": "/dev/sda1",
                               "Ebs": {
                                   "AttachTime": "",
                                   "DeleteOnTermination": true,
                                   "Status": "attached",
                                   "VolumeId": "vol-1234567890abcdef0"
                               }
                           }
                           ...
   ```

1. To create a snapshot of the volume, use the [create\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) command\. Use the volume ID from the previous step\.

   ```
   aws ec2 create-snapshot --region us-east-1 --volume-id vol-1234567890abcdef0 
   --description "add text"
   ```

   Expected output

   ```
   {
    "Description": "add text",
    "Encrypted": false,
    "OwnerId": "123",
    "Progress": "",
    "SnapshotId": "snap-01234567890abcdef",
    "StartTime": "",
    "State": "pending",
    "VolumeId": "vol-1234567890abcdef0",
    "VolumeSize": 30,
    "Tags": []
   }
   ```

1. Note the snapshot ID in the output from the previous step\.

1. Wait until the snapshot creation is `completed` before going to the next step\. To query the state of the snapshot, use the [describe\-snapshots](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-snapshots.html) command\.

   ```
   aws ec2 describe-snapshots --region us-east-1 --snapshot-ids snap-01234567890abcdef
   ```

   Example output

   ```
   {
       "Snapshots": [
           {
               "Description": "This is my snapshot",
               "Encrypted": false,
               "VolumeId": "vol-049df61146c4d7901",
               "State": "completed",
               "VolumeSize": 8,
               "StartTime": "2019-02-28T21:28:32.000Z",
               "Progress": "100%",
               "OwnerId": "012345678910",
               "SnapshotId": "snap-01234567890abcdef",
   ...
   ```

1. To create a new AMI, use the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command\. Use the snapshot ID that you noted in the earlier step\. To set the boot mode to UEFI, add the `--boot-mode uefi` parameter to the command\.

   ```
   aws ec2 register-image \
      --region us-east-1 \
      --description "add description" \
      --name "add name" \
      --block-device-mappings "DeviceName=/dev/sda1,Ebs={SnapshotId=snap-01234567890abcdef,DeleteOnTermination=true}" \
      --architecture x86_64 \
      --root-device-name /dev/sda1 \
      --virtualization-type hvm \
      --ena-support \
      --boot-mode uefi
   ```

   Expected output

   ```
   {
   "ImageId": "ami-new_ami_123"
   }
   ```

1. To verify that the newly\-created AMI has the boot mode that you specified in the previous step, use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

   ```
   aws ec2 describe-images --region us-east-1 --image-id ami-new_ami_123
   ```

   Expected output

   ```
   {
     "Images": [
      {
      "Architecture": "x86_64",
      "CreationDate": "2021-01-06T14:31:04.000Z",
      "ImageId": "ami-new_ami_123",
      "ImageLocation": "",
      ...
      "BootMode": "uefi"
      }
      ]
   }
   ```

1. Launch a new instance using the newly\-created AMI\. All new instances created from this AMI will inherit the same boot mode\.

1. To verify that the new instance has the expected boot mode, use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.
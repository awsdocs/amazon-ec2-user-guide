# Option B: Create a binary blob containing a pre\-filled variable store<a name="uefi-secure-boot-optionB"></a>

After you have created the [three key pairs](uefi-secure-boot-create-three-key-pairs.md), you can create a binary blob containing a pre\-filled variable store containing the UEFI Secure Boot keys\.

**Warning**  
You must sign your boot images *before* you enroll the keys, otherwise you won’t be able to boot your instance\.

**Topics**
+ [Step 1: Create a new variable store or update an existing one](#uefi-secure-boot-create-or-update-variable)
+ [Step 2: Upload the binary blob on AMI creation](#uefi-secure-boot-upload-binary-blob-on-ami-creation)

## Step 1: Create a new variable store or update an existing one<a name="uefi-secure-boot-create-or-update-variable"></a>

You can create the variable store *offline* without a running instance by using the python\-uefivars tool\. The tool can create a new variable store from your keys\. The script currently supports the EDK2 format, the AWS format, and a JSON representation that is easier to edit with higher\-level tooling\.

**To create the variable store offline without a running instance**

1. Download the tool at the following link\.

   ```
   https://github.com/awslabs/python-uefivars
   ```

1. Create a new variable store from your keys by running the following command\. This will create a base64\-encoded binary blob in *your\_binary\_blob*\.bin\. The tool also supports updating a binary blob via the `-I` parameter\.

   ```
   ./uefivars.py -i none -o aws -O your_binary_blob.bin -P PK.esl -K KEK.esl --db db.esl --dbx dbx.esl
   ```

## Step 2: Upload the binary blob on AMI creation<a name="uefi-secure-boot-upload-binary-blob-on-ami-creation"></a>

Use [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) to pass your UEFI variable store data\. For the `--uefi-data` parameter, specify your binary blob, and for the `--boot-mode` parameter, specify `uefi`\.

```
aws ec2 register-image \
    --name uefi_sb_tpm_register_image_test \
    --uefi-data $(cat your_binary_blob.bin) \
    --block-device-mappings "DeviceName=/dev/sda1,Ebs= {SnapshotId=snap-0123456789example,DeleteOnTermination=true}" \
    --architecture x86_64 --root-device-name /dev/sda1 --virtualization-type hvm —ena-support
    --boot-mode uefi
```

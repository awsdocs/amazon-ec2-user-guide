# Create an AMI for NitroTPM support<a name="enable-nitrotpm-support-on-ami"></a>

You configure your AMI for NitroTPM support when you register the AMI\. You can’t configure NitroTPM support later\.

**To configure an AMI for NitroTPM support**  
Use the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) command and set the `tpm-support` parameter to `v2.0` and the `boot-mode` parameter to `uefi`\.

```
aws ec2 register-image \
    --region us-east-1 \
    --name my-image \
    --boot-mode uefi \
    --architecture x86_64 \
    --root-device-name /dev/xvda \
    --block-device-mappings DeviceName=/dev/xvda,Ebs={SnapshotId=snap-0123456789example} DeviceName=/dev/xvdf,Ebs={VolumeSize=10} \
    --tpm-support v2.0
```

Expected output

```
{
"ImageId": "ami-0123456789example"
}
```
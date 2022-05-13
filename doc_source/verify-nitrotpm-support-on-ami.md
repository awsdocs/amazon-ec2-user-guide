# Verify whether an AMI is enabled for NitroTPM<a name="verify-nitrotpm-support-on-ami"></a>

You can use either `describe-images` or `describe-image-attributes` to verify whether an AMI is enabled for NitroTPM\.

**To verify whether an AMI is enabled for NitroTPM using `describe-images`**  
Use the [describe\-images](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command and specify the AMI ID\.

```
aws ec2 describe-images --image-ids ami-0123456789example
```

If NitroTPM is enabled for the AMI, `"TpmSupport": "v2.0"` appears in the output\.

```
{
    "Images": [
        {
        ...
        "BootMode": "uefi",
        ...
        "TpmSupport": "v2.0"
        }
    ]
}
```

**To verify whether an AMI is enabled for NitroTPM using `describe-image-attribute`**  
Use the [describe\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-image-attribute.html) command and specify the `attribute` parameter with the `tpmSupport` value\.

**Note**  
You must be the AMI owner to call `describe-image-attribute`\.

```
aws ec2 describe-image-attribute \
    --region us-east-1 \
    --image-id ami-0123456789example \
    --attribute tpmSupport
```

If NitroTPM is enabled for the AMI, the value for `TpmSupport` is `"v2.0"`\. Note that `describe-image-attribute` only returns the attributes that are specified in the request\.

```
{
    "ImageId": "ami-0123456789example",
    "TpmSupport": {
        "Value": "v2.0"
    }
}
```
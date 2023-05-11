# Determine the supported boot modes of an instance type<a name="instance-type-boot-mode"></a>

You can use the AWS CLI to determine the supported boot modes of an instance type\.

**To determine the supported boot modes of an instance type**  
Use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command to determine the supported boot modes of an instance type\. By including the `--query` parameter, you can filter the output\. In this example, the output is filtered to return only the supported boot modes\.

The following example shows that `m5.2xlarge` supports both UEFI and Legacy BIOS boot modes\.

```
aws ec2 describe-instance-types --region us-east-1 --instance-types m5.2xlarge --query "InstanceTypes[*].SupportedBootModes"
```

Expected output

```
[
    [
        "legacy-bios",
        "uefi"
    ]
]
```

The following example shows that `t2.xlarge` supports only Legacy BIOS\.

```
aws ec2 describe-instance-types --region us-east-1 --instance-types t2.xlarge --query "InstanceTypes[*].SupportedBootModes"
```

Expected output

```
[
    [
        "legacy-bios"
    ]
]
```
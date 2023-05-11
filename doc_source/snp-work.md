# Working with AMD SEV\-SNP<a name="snp-work"></a>

**Topics**
+ [Find supported instance types](#snp-find-instance-types)
+ [Turn on AMD SEV\-SNP at launch](#snp-work-launch)
+ [Check AMD SEV\-SNP status](#snp-work-check)

## Find supported instance types<a name="snp-find-instance-types"></a>

You can use the AWS CLI to find instance types that support AMD SEV\-SNP\.

------
#### [ AWS CLI ]

To find the instance types that support AMD SEV\-SNP using the AWS CLI, use the following [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command\.

```
$ aws ec2 describe-instance-types \
--filters Name=processor-info.supported-features,Values=amd-sev-snp \
--query 'InstanceTypes[*].InstanceType'
```

Example output\.

```
[
    "r6a.2xlarge", 
    "m6a.large", 
    "m6a.2xlarge", 
    "r6a.xlarge", 
    "c6a.16xlarge", 
    "c6a.8xlarge", 
    "m6a.4xlarge", 
    "c6a.12xlarge", 
    "r6a.4xlarge", 
    "c6a.xlarge", 
    "c6a.4xlarge", 
    "c6a.2xlarge", 
    "m6a.xlarge", 
    "c6a.large", 
    "r6a.large", 
    "m6a.8xlarge"
]
```

------

## Turn on AMD SEV\-SNP at launch<a name="snp-work-launch"></a>

You can use the AWS CLI to launch an instance with AMD SEV\-SNP turned on\.

------
#### [ AWS CLI ]

To launch an instance with AMD SEV\-SNP turned on using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command and include the `--cpu-options AmdSevSnp=enabled` option\. For `--image-id`, specify an AMI with the `uefi` or `uefi-prefered` boot mode and an operating system that supports AMD SEV\-SNP\. For `--instance-type`, specify a supported instance type\.

```
$ aws ec2 run-instances \
--image-id supported_ami_id \
--instance-type supported_instance_type \
--key-name key_pair_name \
--subnet-id subnet_id \
--cpu-options AmdSevSnp=enabled
```

------

## Check AMD SEV\-SNP status<a name="snp-work-check"></a>

You can use one of the following methods to find instance types that support AMD SEV\-SNP\.

------
#### [ AWS CLI ]

To check whether AMD SEV\-SNP is turned on for an instance using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\. For `--instance-ids`, specify the ID of the instance to check\.

```
$ aws ec2 describe-instances --instance-ids instance_id
```

In the command output, the value for `AmdSevSnp` in `CpuOptions` indicates whether AMD SEV\-SNP is turned on or off\.

------
#### [ AWS CloudTrail ]

In the AWS CloudTrail event for the instance launch request, a value of `"cpuOptions": {"AmdSevSnp": enabled}` indicates that AMD SEV\-SNP is turned on for the instance\.

------
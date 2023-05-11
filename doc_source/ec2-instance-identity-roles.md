# Instance identity roles<a name="ec2-instance-identity-roles"></a>

Each instance that you launch has an *instance identity role* that represents its identity\. An instance identity role is a type of IAM role\. AWS services and features that are integrated to use the instance identity role can use it to identify the instance to the service\.

The instance identity role credentials are accessible from the Instance Metadata Service \(IMDS\) at `/identity-credentials/ec2/security-credentials/ec2-instance`\. The credentials consist of an AWS temporary access key pair and a session token\. They are used to sign AWS Sigv4 requests to the AWS services that use the instance identity role\. The credentials are present in the instance metadata regardless of whether a service or feature that makes use of instance identity roles is enabled on the instance\.

Instance identity roles are automatically created when an instance is launched, have no role\-trust policy document, and are not subject to any identity or resource policy\.

## Supported services<a name="iir-supported-services"></a>

The following AWS services use the instance identity role:
+ **Amazon EC2** – [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md) uses the instance identity role to update an EC2 instance’s host keys\.
+ **Amazon GuardDuty** – [EKS Runtime Monitoring](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty-eks-runtime-monitoring.html) uses the instance identity role to allow the runtime agent to send security telemetry to the GuardDuty VPC endpoint\.
+ **AWS Security Token Service \(AWS STS\)** – Instance identity role credentials can be used with the AWS STS [https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html) action\.
+ **AWS Systems Manager** – When using [Default Host Management Configuration](https://docs.aws.amazon.com/systems-manager/latest/userguide/managed-instances-default-host-management.html), AWS Systems Manager uses the identity provided by the instance identity role to register EC2 instances\. After identifying your instance, Systems Manager can pass your `AWSSystemsManagerDefaultEC2InstanceManagementRole` IAM role to your instance\.

Instance identity roles can’t be used with other AWS services or features because they do not have an integration with instance identity roles\.

## Instance identity role ARN<a name="iir-arn"></a>

The instance identity role ARN takes the following format:

```
arn:aws-partition:iam::account-number:assumed-role/aws:ec2-instance/instance-id
```

For example:

```
arn:aws:iam::0123456789012:assumed-role/aws:ec2-instance/i-0123456789example
```

For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference-arns.html) in the *IAM User Guide*\.
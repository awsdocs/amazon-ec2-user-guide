# Configure instance metadata options for new instances<a name="configuring-IMDS-new-instances"></a>

**Topics**
+ [Require the use of IMDSv2](#configure-IMDS-new-instances)
+ [Configure IPv4 and IPv6 endpoints](#configure-IMDS-new-instances-ipv4-ipv6-endpoints)
+ [Turn off access to instance metadata](#configure-IMDS-new-instances--turn-off-instance-metadata)

## Require the use of IMDSv2<a name="configure-IMDS-new-instances"></a>

**Topics**
+ [Configure the instance at launch](#configure-IMDS-new-instances-instance-settings)
+ [Configure the AMI](#configure-IMDS-new-instances-ami-configuration)
+ [Use an IAM policy](#configure-IMDS-new-instances-iam-policy)

### Configure the instance at launch<a name="configure-IMDS-new-instances-instance-settings"></a>

When you [launch an instance](ec2-launch-instance-wizard.md#liw-quickly-launch-instance), you can configure the instance to require the use of IMDSv2 by configuring the following fields:
+ Amazon EC2 console: Set **Metadata version** to **V2 only \(token required\)**\.
+ AWS CLI: Set `HttpTokens` to `required`\.

When you specify that IMDSv2 is required, you must also enable the Instance Metadata Service \(IMDS\) endpoint by setting **Metadata accessible** to **Enabled** \(console\) or `HttpEndpoint` to `enabled` \(AWS CLI\)\.

------
#### [ New console ]

**To require the use of IMDSv2 on a new instance**
+ When launching a new instance in the Amazon EC2 console, expand **Advanced details**, and do the following:
  + For **Metadata accessible**, choose **Enabled**\.
  + For **Metadata version**, choose **V2 only \(token required\)**\.

  For more information, see [Advanced details](ec2-launch-instance-wizard.md#liw-advanced-details)\.

------
#### [ Old console ]

**To require the use of IMDSv2 on a new instance**
+ When launching a new instance in the Amazon EC2 console, select the following options on the **Configure Instance Details** page:
  + Under **Advanced Details**, for **Metadata accessible**, select **Enabled**\.
  + For **Metadata version**, select **V2 \(token required\)**\.

For more information, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.

------
#### [ AWS CLI ]

**To require the use of IMDSv2 on a new instance**  
The following [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) example launches a `c3.large` instance with `--metadata-options` set to `HttpTokens=required`\. When you specify a value for `HttpTokens`, you must also set `HttpEndpoint` to `enabled`\. Because the secure token header is set to `required` for metadata retrieval requests, this requires the instance to use IMDSv2 when requesting instance metadata\.

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type c3.large \
	...
    --metadata-options "HttpEndpoint=enabled,HttpTokens=required"
```

------
#### [ AWS CloudFormation ]

To specify the metadata options for an instance using AWS CloudFormation, see the [AWS::EC2::LaunchTemplate MetadataOptions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-launchtemplate-launchtemplatedata-metadataoptions.html) property in the *AWS CloudFormation User Guide*\. 

------

### Configure the AMI<a name="configure-IMDS-new-instances-ami-configuration"></a>

When you register a new AMI or modify an existing AMI, you can set the `imds-support` parameter to `v2.0`\. Instances launched from this AMI will have **Metadata version** set to **V2 only \(token required\)** \(console\) or `HttpTokens` set to `required` \(AWS CLI\) \. With these settings, the instance requires that IMDSv2 is used when requesting instance metadata\.

Note that when you set `imds-support` to `v2.0`, instances launched from this AMI will also have **Metadata response hop limit** \(console\) or `http-put-response-hop-limit` \(AWS CLI\) set to **2**\.

**Important**  
Do not use this parameter unless your AMI software supports IMDSv2\. After you set the value to `v2.0`, you can't undo it\. The only way to "reset" your AMI is to create a new AMI from the underlying snapshot\.

**To configure a new AMI for IMDSv2**  
The following [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) example registers an AMI using the specified snapshot of an EBS root volume as device `/dev/xvda`\. Specify `v2.0` for the `imds-support` parameter so that instances launched from this AMI will require that IMDSv2 is used when requesting instance metadata\.

```
aws ec2 register-image \
    --name my-image \
    --root-device-name /dev/xvda \
    --block-device-mappings DeviceName=/dev/xvda,Ebs={SnapshotId=snap-0123456789example} \
    --imds-support v2.0
```

**To configure an existing AMI for IMDSv2**  
The following [modify\-image\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-image-attribute.html) example modifies an existing AMI for IMDSv2 only\. Specify `v2.0` for the `imds-support` parameter so that instances launched from this AMI will require that IMDSv2 is used when requesting instance metadata\.

```
aws ec2 modify-image-attribute \
    --image-id ami-0123456789example \
    --imds-support v2.0
```

### Use an IAM policy<a name="configure-IMDS-new-instances-iam-policy"></a>

You can create an IAM policy that prevents users from launching new instances unless they require IMDSv2 on the new instance\.

**To enforce the use of IMDSv2 on all new instances by using an IAM policy**  
To ensure that users can only launch instances that require the use of IMDSv2 when requesting instance metadata, you can specify that the condition to require IMDSv2 must be met before an instance can be launched\. For the example IAM policy, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

## Configure IPv4 and IPv6 endpoints<a name="configure-IMDS-new-instances-ipv4-ipv6-endpoints"></a>

By default, the IPv6 endpoint is disabled\. This is true even if you are launching an instance into an IPv6\-only subnet\. You can choose to enable the IPv6 endpoint at instance launch when using the AWS CLI\. This option is not available in the Amazon EC2 console\.

The IPv6 endpoint for the IMDS is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

**Configure IPv4 and IPv6 endpoints**  
The following [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) example launches a `t3.large` instance with the IPv6 endpoint enabled for the IMDS\. To enable the IPv6 endpoint, for the `--metadata-options` parameter, specify `HttpProtocolIpv6=enabled`\. When you specify a value for `HttpProtocolIpv6`, you must also set `HttpEndpoint` to `enabled`\.

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type t3.large \
    ...
    --metadata-options "HttpEndpoint=enabled,HttpProtocolIpv6=enabled"
```

## Turn off access to instance metadata<a name="configure-IMDS-new-instances--turn-off-instance-metadata"></a>

You can ensure that access to your instance metadata is turned off, regardless of which version of the IMDS you are using\. You can turn on access later\. For more information, see [Turn on access to instance metadata](configuring-IMDS-existing-instances.md#enable-instance-metadata-on-existing-instances)\.

------
#### [ New console ]

**To turn off access to instance metadata**
+ [Launch the instance](ec2-launch-instance-wizard.md#liw-quickly-launch-instance) in the Amazon EC2 console with the following specified under **Advanced details**:
  + For **Metadata accessible**, choose **Disabled**\.

For more information, see [Advanced details](ec2-launch-instance-wizard.md#liw-advanced-details)\.

------
#### [ Old console ]

**To turn off access to instance metadata**
+ Launch the instance in the Amazon EC2 console with the following option selected on the **Configure Instance Details** page:
  + Under **Advanced Details**, for **Metadata accessible**, select **Disabled**\.

For more information, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.

------
#### [ AWS CLI ]

**To turn off access to instance metadata**  
Launch the instance with `--metadata-options` set to `HttpEndpoint=disabled`\.

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --instance-type c3.large \
    ... 
    --metadata-options "HttpEndpoint=disabled"
```

------
#### [ AWS CloudFormation ]

To specify the metadata options for an instance using AWS CloudFormation, see the [AWS::EC2::LaunchTemplate MetadataOptions](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-launchtemplate-launchtemplatedata-metadataoptions.html) property in the *AWS CloudFormation User Guide*\. 

------
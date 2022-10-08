# Modify instance metadata options for existing instances<a name="configuring-IMDS-existing-instances"></a>

You can modify metadata options for existing instances\. You can also create an IAM policy that prevents users from modifying the metadata options on existing instances\.

You can modify the following metadata options for existing instances\.
+ Require the use IMDSv2 on an existing instance\.
+ Change the `PUT` response hop limit\.
+ Turn off access to instance metadata on an existing instance\.

**Note**  
You can't use the Amazon EC2 console for modifying instance metadata options\. Currently only the AWS CLI and AWS SDK support modifying the instance metadata options on existing instances\.

**To require the use of IMDSv2**  
You can require that IMDSv2 is used when requesting instance metadata\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-tokens` parameter to `required`\. When you specify a value for `http-tokens`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-tokens required \
    --http-endpoint enabled
```

**To change the PUT response hop limit**  
For existing instances, you can change the settings of the `PUT` response hop limit\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-put-response-hop-limit` parameter to the required number of hops\. In the following example, the hop limit is set to `3`\. Note that when specifying a value for `http-put-response-hop-limit`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-put-response-hop-limit 3 \
    --http-endpoint enabled
```

**To restore the use of IMDSv1 on an instance using IMDSv2**  
You can use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command with `http-tokens` set to `optional` to restore the use of IMDSv1 when requesting instance metadata\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-tokens optional \
    --http-endpoint enabled
```

**To turn on the IPv6 endpoint for your instance**  
By default, the IPv6 endpoint is disabled\. This is true even if you have launched an instance into an IPv6\-only subnet\. The IPv6 endpoint for the instance metadata service is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `enabled`\.

```
aws ec2 modify-instance-metadata-options \
	--instance-id i-1234567898abcdef0 \
	--http-protocol-ipv6 enabled \
	--http-endpoint enabled
```

**To turn off access to instance metadata**  
You can turn off access to your instance metadata by disabling the HTTP endpoint of the instance metadata service, regardless of which version of the instance metadata service you are using\. You can reverse this change at any time by enabling the HTTP endpoint\.\.

Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `disabled`

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-endpoint disabled
```

**To control the use of modify\-instance\-metadata\-options**  
To control which IAM users can modify the instance metadata options, specify a policy that prevents all users other than users with a specified role to use the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\. For the example IAM policy, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.
# Configure the instance metadata options<a name="configuring-instance-metadata-options"></a>

Instance metadata options allow you to configure new or existing instances to do the following:
+ Require the use of IMDSv2 when requesting instance metadata
+ Specify the `PUT` response hop limit
+ Turn off access to instance metadata

You can also use IAM condition keys in an IAM policy or SCP to do the following:
+ Allow an instance to launch only if it's configured to require the use of IMDSv2
+ Restrict the number of allowed hops
+ Turn off access to instance metadata

**Note**  
You should proceed cautiously and conduct careful testing before making any changes\. Take note of the following:  
If you enforce the use of IMDSv2, applications or agents that use IMDSv1 for instance metadata access will break\.
If you turn off all access to instance metadata, applications or agents that rely on instance metadata access to function will break\.
For IMDSv2, you must use `/latest/api/` token when retrieving the token\.

**Topics**
+ [Configure instance metadata options for new instances](#configuring-IMDS-new-instances)
+ [Modify instance metadata options for existing instances](#configuring-IMDS-existing-instances)

## Configure instance metadata options for new instances<a name="configuring-IMDS-new-instances"></a>

You can require the use of IMDSv2 on an instance when you launch it\. You can also create an IAM policy that prevents users from launching new instances unless they require IMDSv2 on the new instance\.

------
#### [ Console ]

**To require the use of IMDSv2 on a new instance**
+ When launching a new instance in the Amazon EC2 console, select the following options on the **Configure Instance Details** page:
  + Under **Advanced Details**, for **Metadata accessible**, select **Enabled**\.
  + For **Metadata version**, select **V2 \(token required\)**\.

For more information, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.

------
#### [ AWS CLI ]

**To require the use of IMDSv2 on a new instance**  
The following [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) example launches a `c3.large` instance with `--metadata-options` set to `HttpTokens=required`\. When you specify a value for `HttpTokens`, you must also set `HttpEndpoint` to `enabled`\. Because the secure token header is set to `required` for metadata retrieval requests, this opts in the instance to require using IMDSv2 when requesting instance metadata\.

```
aws ec2 run-instances 
    --image-id ami-0abcdef1234567890 
    --instance-type c3.large 
	...
    --metadata-options "HttpEndpoint=enabled,HttpTokens=required"
```

------

**To enforce the use of IMDSv2 on all new instances**  
To ensure that IAM users can only launch instances that require the use of IMDSv2 when requesting instance metadata, you can specify that the condition to require IMDSv2 must be met before an instance can be launched\. For the example IAM policy, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

------
#### [ Console ]

**To turn off access to instance metadata**
+ To ensure that access to your instance metadata is turned off, regardless of which version of the instance metadata service you are using, launch the instance in the Amazon EC2 console with the following option selected on the **Configure Instance Details** page:
  + Under **Advanced Details**, for **Metadata accessible**, select **Disabled**\.

For more information, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.

------
#### [ AWS CLI ]

**To turn off access to instance metadata**  
To ensure that access to your instance metadata is turned off, regardless of which version of the instance metadata service you are using, launch the instance with `--metadata-options` set to `HttpEndpoint=disabled`\. You can turn access on later by using the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) command\.

```
aws ec2 run-instances 
    --image-id ami-0abcdef1234567890 
    --instance-type c3.large 
    ... 
    --metadata-options "HttpEndpoint=disabled"
```

------

## Modify instance metadata options for existing instances<a name="configuring-IMDS-existing-instances"></a>

You can require the use IMDSv2 on an existing instance\. You can also change the PUT response hop limit and turn off access to instance metadata on an existing instance\. You can also create an IAM policy that prevents users from modifying the instance metadata options on an existing instance\.

Currently only the AWS SDK or AWS CLI support modifying the instance metadata options on existing instances\. You can't use the Amazon EC2 console for modifying instance metadata options\.

**To require the use of IMDSv2**  
You can opt in to require that IMDSv2 is used when requesting instance metadata\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-tokens` parameter to `required`\. When you specify a value for `http-tokens`, you must also set `http-endpoint` to `enabled`\.

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

**To turn off access to instance metadata**  
You can turn off access to your instance metadata by disabling the HTTP endpoint of the instance metadata service, regardless of which version of the instance metadata service you are using\. You can reverse this change at any time by enabling the HTTP endpoint\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `disabled`\.

```
aws ec2 modify-instance-metadata-options \
    --instance-id i-1234567898abcdef0 \
    --http-endpoint disabled
```

**To control the use of modify\-instance\-metadata\-options**  
To control which IAM users can modify the instance metadata options, specify a policy that prevents all users other than users with a specified role to use the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\. For the example IAM policy, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.
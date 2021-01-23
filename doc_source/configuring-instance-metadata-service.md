# Configure the instance metadata service<a name="configuring-instance-metadata-service"></a>

You can access instance metadata from a running instance using one of the following methods:
+ Instance Metadata Service Version 1 \(IMDSv1\) – a request/response method
+ Instance Metadata Service Version 2 \(IMDSv2\) – a session\-oriented method

By default, you can use either IMDSv1 or IMDSv2, or both\. The instance metadata service distinguishes between IMDSv1 and IMDSv2 requests based on whether, for any given request, either the `PUT` or `GET` headers, which are unique to IMDSv2, are present in that request\.

You can configure the instance metadata service on each instance such that local code or users must use IMDSv2\. When you specify that IMDSv2 must be used, IMDSv1 no longer works\. For more information, see [Configure the instance metadata options](#configuring-instance-metadata-options)\.

To retrieve instance metadata, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

## How Instance Metadata Service Version 2 works<a name="instance-metadata-v2-how-it-works"></a>

IMDSv2 uses session\-oriented requests\. With session\-oriented requests, you create a session token that defines the session duration, which can be a minimum of one second and a maximum of six hours\. During the specified duration, you can use the same session token for subsequent requests\. After the specified duration expires, you must create a new session token to use for future requests\.

The following example uses a Linux shell script and IMDSv2 to retrieve the top\-level instance metadata items\. The example command: 
+ Creates a session token lasting six hours \(21,600 seconds\) using the `PUT` request
+ Stores the session token header in a variable named `TOKEN`
+ Requests the top\-level metadata items using the token

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/
```

After you've created a token, you can reuse it until it expires\. In the following example command, which gets the ID of the AMI used to launch the instance, the token that is stored in `$TOKEN` in the previous example is reused\.

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/ami-id
```

When you use IMDSv2 to request instance metadata, the request must include the following:

1. Use a `PUT` request to initiate a session to the instance metadata service\. The `PUT` request returns a token that must be included in subsequent `GET` requests to the instance metadata service\. The token is required to access metadata using IMDSv2\.

1. Include the token in all `GET` requests to the instance metadata service\. When token usage is set to `required`, requests without a valid token or with an expired token receive a `401 - Unauthorized` HTTP error code\. For information about changing the token usage requirement, see [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.
   + The token is an instance\-specific key\. The token is not valid on other EC2 instances and will be rejected if you attempt to use it outside of the instance on which it was generated\.
   + The `PUT` request must include a header that specifies the time to live \(TTL\) for the token, in seconds, up to a maximum of six hours \(21,600 seconds\)\. The token represents a logical session\. The TTL specifies the length of time that the token is valid and, therefore, the duration of the session\.
   + After a token expires, to continue accessing instance metadata, you must create a new session using another `PUT`\.
   + You can choose to reuse a token or create a new token with every request\. For a small number of requests, it might be easier to generate and immediately use a token each time you need to access the instance metadata service\. But for efficiency, you can specify a longer duration for the token and reuse it rather than having to write a `PUT` request every time you need to request instance metadata\. There is no practical limit on the number of concurrent tokens, each representing its own session\. IMDSv2 is, however, still constrained by normal instance metadata service connection and throttling limits\. For more information, see [Query throttling](instancedata-data-retrieval.md#instancedata-throttling)\.

HTTP `GET` and `HEAD` methods are allowed in IMDSv2 instance metadata requests\. `PUT` requests are rejected if they contain an X\-Forwarded\-For header\.

By default, the response to `PUT` requests has a response hop limit \(time to live\) of `1` at the IP protocol level\. You can adjust the hop limit using the `modify-instance-metadata-options` command if you need to make it larger\. For example, you might need a larger hop limit for backward compatibility with container services running on the instance\. For more information, see [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.

## Transition to using Instance Metadata Service Version 2<a name="instance-metadata-transition-to-version-2"></a>

Use of Instance Metadata Service Version 2 \(IMDSv2\) is optional\. Instance Metadata Service Version 1 \(IMDSv1\) will continue to be supported indefinitely\. If you choose to migrate to using IMDSv2, we recommend that you use the following tools and transition path\. 

**Tools for helping with the transition to IMDSv2**

If your software uses IMDSv1, use the following tools to help reconfigure your software to use IMDSv2\.
+ **AWS software:** The latest versions of the AWS SDKs and CLIs support IMDSv2\. To use IMDSv2, make sure that your EC2 instances have the latest versions of the AWS SDKs and CLIs\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.
+ **CloudWatch**: IMDSv2 uses token\-backed sessions, while IMDSv1 does not\. The `MetadataNoToken` CloudWatch metric tracks the number of calls to the instance metadata service that are using IMDSv1\. By tracking this metric to zero, you can determine if and when all of your software has been upgraded to use IMDSv2\. For more information, see [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\.
+ **Updates to EC2 APIs and CLIs**: For existing instances, you can use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command \(or the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\) to require the use of IMDSv2\. For new instances, you can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) CLI command \(or the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API\) and the `metadata-options` parameter to launch new instances that require the use of IMDSv2\.

  To require the use of IMDSv2 on all new instances launched by Auto Scaling groups, your Auto Scaling groups can use either a launch template or a launch configuration\. When you [create a launch template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) or [create a launch configuration](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-launch-configuration.html), you must configure the `MetadataOptions` parameters to require the use IMDSv2\. After you configure the launch template or launch configuration, the Auto Scaling group launches new instances using the new launch template or launch configuration, but existing instances are not affected\.

  Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command \(or the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\) to require the use of IMDSv2 on the existing instances, or terminate the instances and the Auto Scaling group will launch new replacement instances with the instance metadata options settings that are defined in the launch template or launch configuration\.

+ **IAM policies and SCPs**: You can use an IAM condition to enforce that IAM users can't launch an instance unless it uses IMDSv2\. You can also use IAM conditions to enforce that IAM users can't modify running instances to re\-enable IMDSv1, and to enforce that the instance metadata service is available on the instance\.

  The `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit`, and `ec2:MetadataHttpEndpoint` IAM condition keys can be used to control the use of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) and the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API and corresponding CLI\. If a policy is created, and a parameter in the API call does not match the state specified in the policy using the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\. These condition keys can be used either in IAM policies or AWS Organizations service control policies \(SCPs\)\.

  Furthermore, you can choose an additional layer of protection to enforce the change from IMDSv1 to IMDSv2\. At the access management layer with respect to the APIs called via EC2 Role credentials, you can use a new condition key in either IAM policies or AWS Organizations service control policies \(SCPs\)\. Specifically, by using the policy condition key `ec2:RoleDelivery` with a value of `2.0` in your IAM policies, API calls made with EC2 Role credentials obtained from IMDSv1 will receive an `UnauthorizedOperation` response\. The same thing can be achieved more broadly with that condition required by an SCP\. This ensures that credentials delivered via IMDSv1 cannot actually be used to call APIs because any API calls not matching the specified condition will receive an `UnauthorizedOperation` error\. For example IAM policies, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\. For more information, see [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) in the *AWS Organizations User Guide*\.

**Recommended path to requiring IMDSv2 access**

Using the above tools, we recommend that you follow this path for transitioning to IMDSv2:

### Step 1: At the start<a name="path-step-1"></a>

Update the SDKs, CLIs, and your software that use Role credentials on their EC2 instances to IMDSv2\-compatible versions\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.

Then, change your software that directly accesses instance metadata \(in other words, that does not use an SDK\) using the IMDSv2 requests\. 

### Step 2: During the transition<a name="path-step-2"></a>

Track your transition progress by using the CloudWatch metric `MetadataNoToken`\. This metric shows the number of calls to the instance metadata service that are using IMDSv1 on your instances\. For more information, see [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\. 

### Step 3: When everything is ready on all instances<a name="path-step-3"></a>

Everything is ready on all instances when the CloudWatch metric `MetadataNoToken` records zero IMDSv1 usage\. At this stage, you can do the following:
+ For existing instances: You can require IMDSv2 use through the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) command\. You can make these changes on running instances; you do not need to restart your instances\. 
+ For new instances: When launching a new instance, you can do one of the following:
  + In the Amazon EC2 console launch instance wizard, set **Metadata accessible** to **Enabled** and **Metadata version** to **V2**\. For more information, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.
  + Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to specify that only IMDSv2 is to be used\.

Updating instance metadata options for existing instances is available only through the API or AWS CLI\. It is currently not available in the Amazon EC2 console\. For more information, see [Configure the instance metadata options](#configuring-instance-metadata-options)\.

### Step 4: When all of your instances are transitioned to IMDSv2<a name="path-step-4"></a>

The `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit`, and `ec2:MetadataHttpEndpoint` IAM condition keys can be used to control the use of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) and the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API and corresponding CLI\. If a policy is created, and a parameter in the API call does not match the state specified in the policy using the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\. For example IAM policies, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

## Configure the instance metadata options<a name="configuring-instance-metadata-options"></a>

Instance metadata options allow you to configure new or existing instances to do the following:
+ Require the use of IMDSv2 when requesting instance metadata
+ Specify the `PUT` response hop limit
+ Turn off access to instance metadata

You can also use IAM condition keys in an IAM policy or SCP to do the following:
+ Allow an instance to launch only if it's configured to require the use of IMDSv2
+ Restrict the number of allowed hops
+ Turn off access to instance metadata

You can configure instance metadata options when launching new instances from the Amazon EC2 console\. For more information, see [Step 3: Configure Instance Details](launching-instance.md#configure_instance_details_step)\.

To configure the instance metadata options on new or existing instances, you can use the AWS SDK or AWS CLI\. For more information, see [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) and [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.

**Note**  
You should proceed cautiously and conduct careful testing before making any changes\. Take note of the following:  
If you enforce the use of IMDSv2, applications or agents that use IMDSv1 for instance metadata access will break\.
If you turn off all access to instance metadata, applications or agents that rely on instance metadata access to function will break\.

**Topics**
+ [Configure instance metadata options for new instances](#configuring-IMDS-new-instances)
+ [Configure instance metadata options for existing instances](#configuring-IMDS-existing-instances)

### Configure instance metadata options for new instances<a name="configuring-IMDS-new-instances"></a>

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

### Configure instance metadata options for existing instances<a name="configuring-IMDS-existing-instances"></a>

You can require the use IMDSv2 on an existing instance\. You can also change the PUT response hop limit and turn off access to instance metadata on an existing instance\. You can also create an IAM policy that prevents users from modifying the instance metadata options on an existing instance\.

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

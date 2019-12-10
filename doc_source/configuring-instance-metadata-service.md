# Configuring the Instance Metadata Service<a name="configuring-instance-metadata-service"></a>

You can access instance metadata from a running instance using one of the following methods:
+ Instance Metadata Service Version 1 \(IMDSv1\) – a request/response method
+ Instance Metadata Service Version 2 \(IMDSv2\) – a session\-oriented method

By default, you can use either IMDSv1 or IMDSv2, or both\. The instance metadata service distinguishes between IMDSv1 and IMDSv2 requests based on whether, for any given request, either the `PUT` or `GET` headers, which are unique to IMDSv2, are present in that request\.

You can configure the instance metadata service on each instance such that local code or users must use IMDSv2\. When you specify that IMDSv2 must be used, IMDSv1 no longer works\. For more information, see [Configuring the Instance Metadata Options](#configuring-instance-metadata-options)\.

## How Instance Metadata Service Version 2 Works<a name="instance-metadata-v2-how-it-works"></a>

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
   + You can choose to reuse a token or create a new token with every request\. For a small number of requests, it might be easier to generate and immediately use a token each time you need to access the instance metadata service\. But for efficiency, you can specify a longer duration for the token and reuse it rather than having to write a `PUT` request every time you need to request instance metadata\. There is no practical limit on the number of concurrent tokens, each representing its own session\. IMDSv2 is, however, still constrained by normal instance metadata service connection and throttling limits\. For more information, see [Throttling](instancedata-data-retrieval.md#instancedata-throttling)\.

HTTP `GET` and `HEAD` methods are allowed in IMDSv2 instance metadata requests\. `PUT` requests are rejected if they contain an X\-Forwarded\-For header\.

By default, the response to `PUT` requests has a response hop limit \(time to live\) of `1` at the IP protocol level\. You can adjust the hop limit using the `modify-instance-metadata-options` command if you need to make it larger\. For example, you might need a larger hop limit for backward compatibility with container services running on the instance\. For more information, see [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.

## Transitioning to Using Instance Metadata Service Version 2<a name="instance-metadata-transition-to-version-2"></a>

Use of Instance Metadata Service Version 2 \(IMDSv2\) is optional\. Instance Metadata Service Version 1 \(IMDSv1\) will continue to be supported indefinitely\. If you choose to migrate to using IMDSv2, we recommend that you use the following tools and transition path\. 

**Tools for helping with the transition to IMDSv2**

If your software uses IMDSv1, use the following tools to help reconfigure your software to use IMDSv2\.
+ **AWS software:** The latest versions of the AWS SDKs and CLIs support IMDSv2\. To use IMDSv2, make sure that your EC2 instances have the latest versions of the AWS SDKs and CLIs\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.
+ **CloudWatch**: IMDSv1 does not use token\-backed sessions\. You can track the number of calls to the instance metadata service that are not using token\-backed sessions through the CloudWatch metric `MetadataNoToken`\. This metric tracks the number of calls that are using IMDSv1\. By tracking this metric to zero, you can determine if and when all of your software has been upgraded to use IMDSv2\. For more information, see [Instance Metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\.
+ **Updates to EC2 APIs and CLIs**: For existing instances, you can use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command \(or the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\) to require the use of IMDSv2\. For new instances, you can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) CLI command \(or the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API\) and the `metadata-options` parameter to launch new instances that require the use of IMDSv2\.
+ **IAM policies and SCPs**: You can use condition keys to block users from launching new instances or from modifying running instances, unless they specify the use of IMDSv2 only\. You can also use another condition key to block subsequent API calls made with EC2 Role credentials obtained via IMDSv1\. Those condition keys, which are described in the following section, can be used either in IAM policies or AWS Organizations service control policies \(SCPs\)\. For more information, see **When all of your instances are transitioned to IMDSv2** in the following section\. For example IAM policies, see [Example: Working with Instance Metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

**Recommended path to requiring IMDSv2 access**

Using the above tools, we recommend that you follow this path for transitioning to IMDSv2:

1. **At the start**

   Update the SDKs, CLIs, and your software that use Role credentials on their EC2 instances to IMDSv2\-compatible versions\. Then, change your software that directly accesses instance metadata \(in other words, that does not use an SDK\) using the IMDSv2 requests\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.

1. **During the transition**

   Track your transition progress by looking at the CloudWatch metric `MetadataNoToken`, which shows the number of calls to IMDSv1 on your instances\. For more information, see [Instance Metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\. 

1. **When everything is ready on all instances**

   Everything is ready on all instances when the CloudWatch metric `MetadataNoToken` records zero usage\. At this stage, you can require IMDSv2 use through the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) command\. You can make these changes on running instances; you do not need to restart your instances\. For newly\-launched instances, you can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to specify that only IMDSv2 is to be used\. Specifying your instance metadata options is available only through the API or AWS CLI; it is currently not available via the AWS Management Console\. For more information, see [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) and [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.

1. **When all of your instances are transitioned to IMDSv2**

   You can use an IAM condition to enforce that IAM users can't launch an instance unless it uses IMDSv2\. For more information, see **To enforce the use of IMDSv2 on all new instances** in the following section\. You can also use IAM conditions to enforce that IAM users can't modify running instances to re\-enable IMDSv1, and to enforce that the instance metadata service is available on the instance\. 

   Furthermore, you can choose an additional layer of protection to enforce the change from IMDSv1 to IMDSv2\. At the access management layer with respect to the APIs called via EC2 Role credentials, you can use a new condition key in either IAM policies or AWS Organizations service control policies \(SCPs\)\. Specifically, by using the policy condition key `ec2:RoleDelivery` with a value of `2.0` in your IAM policies, API calls made with EC2 Role credentials obtained from IMDSv1 will receive an `UnauthorizedOperation` response\. The same thing can be achieved more broadly with that condition required by an SCP\. This ensures that credentials delivered via IMDSv1 cannot actually be used to call APIs because any API calls not matching the specified condition will receive an `UnauthorizedOperation` error\. For example IAM policies, see [Example: Working with Instance Metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\. For more information, see [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) in the *AWS Organizations User Guide*\.

## Configuring the Instance Metadata Options<a name="configuring-instance-metadata-options"></a>

Instance metadata options allow you to configure new or existing instances to require the use of IMDSv2 when requesting instance metadata, specify the `PUT` response hop limit, and turn off access to instance metadata\. You can also use condition keys in an IAM policy or SCP that allows an instance to launch only if it's configured to require the use of IMDSv2, or to restrict the number of allowed hops, or to enforce that the IMDS is disabled altogether\. To configure the instance metadata options on new or existing instances, you use the AWS SDK or CLI\. For more information, see [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) and [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.

**Note**  
You should proceed cautiously and conduct careful testing before making any changes\. Take note of the following:  
If you enforce the use of IMDSv2, applications or agents that use IMDSv1 for instance metadata access will break\.
If you turn off all access to instance metadata, applications or agents that rely on instance metadata access to function will break\.

**To enforce the use of IMDSv2 on all new instances**

To ensure that IAM users can only launch instances that require the use of IMDSv2 when requesting instance metadata, you can specify that the condition to require IMDSv2 must be met before an instance can be launched\.

The following instructions explain how to create the policy and attach it using the AWS CLI\. For instructions that use the AWS Management Console, see [Creating IAM Policies \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-start) and [Adding Permissions by Attaching Policies Directly to the User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-directly-console) in the *IAM User Guide*\.

1. Create a JSON policy document that includes the following:
   + The `ec2:RunInstances` action\. This grants an IAM user permission to launch new instances\.
   + The `ec2:MetadataHttpTokens` condition set to `required`\. This specifies that, for the instance to launch, it must be configured to require IMDSv2, which uses a secure token header for instance metadata retrieval requests\.

   The following is an example policy document\.

   ```
   {
   	"Version": "2012-10-17",
   	"Statement": [{
   		"Sid": "RunInstanceWithImdsV2Only",
   		"Effect": "Allow",
   		"Action": "ec2:RunInstances",
   		"Resource": "*",
   		"Condition": {
   			"StringEquals": {
   				"ec2:MetadataHttpTokens": "required"
   			}
   		}
   	}]
   }
   ```

1. Use the [create\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/create-policy.html) command to create a new managed policy, and specify the JSON document that you created to use as the content for the new policy\.

   ```
   $ aws iam create-policy --policy-name my-policy --policy-document file://JSON-file-name
   ```

1. Use the [attach\-user\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/create-policy.html) command to attach the managed policy to the specified IAM user\. For the `--user-name` parameter, specify the friendly name \(not the ARN\) of the IAM user\.

   ```
   $ aws iam attach-user-policy --policy-arn arn:aws:iam::account-id:policy/my-policy --user-name IAM-friendly-name
   ```

The `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit`, and `ec2:MetadataHttpEndpoint` IAM condition keys can also be used to control the use of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) and the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API and corresponding CLI\. For the valid conditions for these keys, see [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) and [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\. If a policy is created, and a parameter in the API call does not match the state specified in the policy using the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\. For more example IAM policies, see [Example: Working with Instance Metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

**To require the use of IMDSv2 on an existing instance**  
For existing instances, you can opt in to require that IMDSv2 is used when requesting instance metadata\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-tokens` parameter to `required`\. Note that when specifying a value for `http-tokens`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options --instance-id i-1234567898abcdef0 --http-tokens required --http-endpoint enabled
```

To control the use of the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API as well as the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API using IAM policies, see [Example: Working with Instance Metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

**To change the PUT response hop limit on an existing instance**  
For existing instances, you can change the settings of the `PUT` response hop limit\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-put-response-hop-limit` parameter to the required number of hops\. In the following example, the hop limit is set to `3`\. Note that when specifying a value for `http-put-response-hop-limit`, you must also set `http-endpoint` to `enabled`\.

```
aws ec2 modify-instance-metadata-options --instance-id i-1234567898abcdef0 --http-put-response-hop-limit 3 --http-endpoint enabled
```

**To turn off access to instance metadata on an existing instance**  
For existing instances, you can turn off access to your instance metadata by disabling the HTTP endpoint of the instance metadata service, regardless of which version of the instance metadata service you are using\. You can reverse this change at any time by enabling the HTTP endpoint\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `disabled`\.

```
aws ec2 modify-instance-metadata-options --instance-id i-1234567898abcdef0 --http-endpoint disabled
```
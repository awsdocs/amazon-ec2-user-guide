# Instance Metadata and User Data<a name="ec2-instance-metadata"></a>

*Instance metadata* is data about your instance that you can use to configure or manage the running instance\. Instance metadata is divided into categories, for example, host name, events, and security groups\.

You can also use instance metadata to access *user data* that you specified when launching your instance\. For example, you can specify parameters for configuring your instance, or include a simple script\. You can build generic AMIs and use user data to modify the configuration files supplied at launch time\. For example, if you run web servers for various small businesses, they can all use the same generic AMI and retrieve their content from the Amazon S3 bucket that you specify in the user data at launch\. To add a new customer at any time, create a bucket for the customer, add their content, and launch your AMI with the unique bucket name provided to your code in the user data\. If you launch more than one instance at the same time, the user data is available to all instances in that reservation\. Each instance that is part of the same reservation has a unique `ami-launch-index` number, allowing you to write code that controls what to do\. For example, the first host might elect itself as an initial master node in a cluster\. For a detailed AMI launch example, see [Example: AMI Launch Index Value](#AMI-launch-index-examples)\.

EC2 instances can also include *dynamic data*, such as an instance identity document that is generated when the instance is launched\. For more information, see [Dynamic Data Categories](#dynamic-data-categories)\.

**Important**  
Although you can only access instance metadata and user data from within the instance itself, the data is not protected by authentication or cryptographic methods\. Anyone who has direct access to the instance, and potentially any software running on the instance, can view its metadata\. Therefore, you should not store sensitive data, such as passwords or long\-lived encryption keys, as user data\.

**Topics**
+ [Configuring the Instance Metadata Service](#configuring-instance-metadata-service)
+ [Retrieving Instance Metadata](#instancedata-data-retrieval)
+ [Working with Instance User Data](#instancedata-add-user-data)
+ [Retrieving Dynamic Data](#instancedata-dynamic-data-retrieval)
+ [Example: AMI Launch Index Value](#AMI-launch-index-examples)
+ [Instance Metadata Categories](#instancedata-data-categories)
+ [Instance Identity Documents](instance-identity-documents.md)

## Configuring the Instance Metadata Service<a name="configuring-instance-metadata-service"></a>

You can access instance metadata from a running instance using one of the following methods:
+ Instance Metadata Service Version 1 \(IMDSv1\) – a request/response method
+ Instance Metadata Service Version 2 \(IMDSv2\) – a session\-oriented method

By default, you can use either IMDSv1 or IMDSv2, or both\. The instance metadata service distinguishes between IMDSv1 and IMDSv2 requests based on whether, for any given request, either the `PUT` or `GET` headers, which are unique to IMDSv2, are present in that request\.

You can configure the instance metadata service on each instance such that local code or users must use IMDSv2\. When you specify that IMDSv2 must be used, IMDSv1 no longer works\. For more information, see [Configuring the Instance Metadata Options](#configuring-instance-metadata-options)\.

### How Instance Metadata Service Version 2 Works<a name="instance-metadata-v2-how-it-works"></a>

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
   + You can choose to reuse a token or create a new token with every request\. For a small number of requests, it might be easier to generate and immediately use a token each time you need to access the instance metadata service\. But for efficiency, you can specify a longer duration for the token and reuse it rather than having to write a `PUT` request every time you need to request instance metadata\. There is no practical limit on the number of concurrent tokens, each representing its own session\. IMDSv2 is, however, still constrained by normal instance metadata service connection and throttling limits\. For more information, see [Throttling](#instancedata-throttling)\.

HTTP `GET` and `HEAD` methods are allowed in IMDSv2 instance metadata requests\. `PUT` requests are rejected if they contain an X\-Forwarded\-For header\.

By default, the response to `PUT` requests has a response hop limit \(time to live\) of `1` at the IP protocol level\. You can adjust the hop limit using the `modify-instance-metadata-options` command if you need to make it larger\. For example, you might need a larger hop limit for backward compatibility with container services running on the instance\. For more information, see [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\.

### Transitioning to Using Instance Metadata Service Version 2<a name="instance-metadata-transition-to-version-2"></a>

Use of Instance Metadata Service Version 2 \(IMDSv2\) is optional\. Instance Metadata Service Version 1 \(IMDSv1\) will continue to be supported indefinitely\. If you choose to migrate to using IMDSv2, we recommend that you use the following tools and transition path\. 

**Tools for helping with the transition to IMDSv2**

If your software uses IMDSv1, use the following tools to help reconfigure your software to use IMDSv2\.
+ **AWS software:** The latest versions of the AWS SDKs and CLIs support IMDSv2\. To use IMDSv2, make sure that your EC2 instances have the latest versions of the AWS SDKs and CLIs\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.
+ **CloudWatch**: IMDSv1 does not use token\-backed sessions\. You can track the number of calls to the instance metadata service that are not using token\-backed sessions through the CloudWatch metric `MetadataNoToken`\. This metric tracks the number of calls that are using IMDSv1\. By tracking this metric to zero, you can determine if and when all of your software has been upgraded to use IMDSv2\. For more information, see [Instance Metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\.
+ **Updates to EC2 APIs and CLIs**: For existing instances, you can use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command \(or the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\) to require the use of IMDSv2\. For new instances, you can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) CLI command \(or the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API\) and the `metadata-options` parameter to launch new instances that require the use of IMDSv2\.
+ **IAM policies and SCPs**: You can use condition keys to block users from launching new instances or from modifying running instances, unless they specify the use of IMDSv2 only\. You can also use another condition key to block subsequent API calls made with EC2 Role credentials obtained via IMDSv1\. Those condition keys, which are described in the following section, can be used either in IAM policies or AWS Organizations service control policies \(SCPs\)\. For more information, see **When all of your instances are transitioned to IMDSv2** in the following section\. 

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

   Furthermore, you can choose an additional layer of protection to enforce the change from IMDSv1 to IMDSv2\. At the access management layer with respect to the APIs called via EC2 Role credentials, you can use a new condition key in either IAM policies or AWS Organizations service control policies \(SCPs\)\. Specifically, by using the policy condition key `ec2:RoleDelivery` with a value of `2.0` in your IAM policies, API calls made with EC2 Role credentials obtained from IMDSv1 will receive an `Access Denied` response\. The same thing can be achieved more broadly with that condition required by an SCP\. This ensures that credentials delivered via IMDSv1 cannot actually be used to call APIs because any API calls not matching the specified condition will receive an `Access Denied` error\. For more information, see [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) in the *AWS Organizations User Guide*\.

### Configuring the Instance Metadata Options<a name="configuring-instance-metadata-options"></a>

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

The `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit`, and `ec2:MetadataHttpEndpoint` IAM condition keys can also be used to control the use of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) and the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API and corresponding CLI\. For the valid conditions for these keys, see [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) and [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) in the *AWS CLI Command Reference*\. If a policy is created, and a parameter in the API call does not match the state specified in the policy using the condition key, the API or CLI call fails with an `Access Denied` response\. You can use the preceding example policy as an example of how to create policies with these condition keys\.

**To require the use of IMDSv2 on an existing instance**  
For existing instances, you can opt in to require that IMDSv2 is used when requesting instance metadata\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-tokens` parameter to `required`\.

```
aws ec2 modify-instance-metadata-options --instance-id i-1234567898abcdef0 --http-tokens required
```

Although we do not provide an example policy, the `ec2:MetadataHttpTokens` condition key can also be used to control the use of the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API as well as the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API\.

**To change the PUT response hop limit on an existing instance**  
For existing instances, you can change the settings of the `PUT` response hop limit\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-put-response-hop-limit` parameter to the required number of hops\. In the following example, the hop limit is set to `3`\.

```
aws ec2 modify-instance-metadata-options --instance-id i-1234567898abcdef0 --http-put-response-hop-limit 3
```

**To turn off access to instance metadata on an existing instance**  
For existing instances, you can turn off access to your instance metadata by disabling the HTTP endpoint of the instance metadata service, regardless of which version of the instance metadata service you are using\. You can reverse this change at any time by enabling the HTTP endpoint\. Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command and set the `http-endpoint` parameter to `disabled`\.

```
aws ec2 modify-instance-metadata-options --instance-id i-1234567898abcdef0 --http-endpoint disabled
```

## Retrieving Instance Metadata<a name="instancedata-data-retrieval"></a>

Because your instance metadata is available from your running instance, you do not need to use the Amazon EC2 console or the AWS CLI\. This can be helpful when you're writing scripts to run from your instance\. For example, you can access the local IP address of your instance from instance metadata to manage a connection to an external application\.

Instance metadata is divided into categories\. For a description of each instance metadata category, see [Instance Metadata Categories](#instancedata-data-categories)\.

To view all categories of instance metadata from within a running instance, use the following URI\.

```
http://169.254.169.254/latest/meta-data/
```

The IP address `169.254.169.254` is a link\-local address and is valid only from the instance\. For more information, see [Link\-local address](https://en.wikipedia.org/wiki/Link-local_address) on Wikipedia\.

Note that you are not billed for HTTP requests used to retrieve instance metadata and user data\.

The command format is different, depending on whether you use IMDSv1 or IMDSv2\. By default, you can use both instance metadata services\. To require the use of IMDSv2, see [Configuring the Instance Metadata Service](#configuring-instance-metadata-service)\.

You can use a tool such as cURL, as shown in the following example\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/
```

------

You can also download the [Instance Metadata Query tool](https://aws.amazon.com/code/ec2-instance-metadata-query-tool/), which allows you to query the instance metadata using instance metadata service version 1\.0 without having to enter the full URI or category names\.

### Responses and Error Messages<a name="instance-metadata-returns"></a>

All instance metadata is returned as text \(HTTP content type `text/plain`\)\.

A request for a specific metadata resource returns the appropriate value, or a `404 - Not Found` HTTP error code if the resource is not available\. 

A request for a general metadata resource \(the URI ends with a /\) returns a list of available resources, or a `404 - Not Found` HTTP error code if there is no such resource\. The list items are on separate lines, terminated by line feeds \(ASCII 10\)\.

For requests made using Instance Metadata Service Version 2, the following HTTP error codes can be returned:
+ `400 - Missing or Invalid Parameters` – The `PUT` request is not valid\.
+ `401 - Unauthorized` – The `GET` request uses an invalid token\. The recommended action is to generate a new token\.
+ `403 - Forbidden` – The request is not allowed or the instance metadata service is turned off\.

### Examples of Retrieving Instance Metadata<a name="instancedata-meta-data-retrieval-examples"></a>

**Topics**
+ [Get the Available Versions of the Instance Metadata](#instance-metadata-ex-1)
+ [Get the Top\-Level Metadata Items](#instance-metadata-ex-2)
+ [Get the List of Available Public Keys](#instance-metadata-ex-3)
+ [Show the Formats in Which Public Key 0 Is Available](#instance-metadata-ex-4)
+ [Get Public Key 0 \(in the OpenSSH Key Format\)](#instance-metadata-ex-5)
+ [Get the Subnet ID for an Instance](#instance-metadata-ex-6)

#### Get the Available Versions of the Instance Metadata<a name="instance-metadata-ex-1"></a>

This example gets the available versions of the instance metadata\. These versions do not necessarily correlate with an Amazon EC2 API version\. The earlier versions are available to you in case you have scripts that rely on the structure and information present in a previous version\. 

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/
1.0
2007-01-19
2007-03-01
2007-08-29
2007-10-10
2007-12-15
2008-02-01
2008-09-01
2009-04-04
2011-01-01
2011-05-01
2012-01-12
2014-02-25
2014-11-05
2015-10-20
2016-04-19
2016-06-30
2016-09-02
latest
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/
1.0
2007-01-19
2007-03-01
2007-08-29
2007-10-10
2007-12-15
2008-02-01
2008-09-01
2009-04-04
2011-01-01
2011-05-01
2012-01-12
2014-02-25
2014-11-05
2015-10-20
2016-04-19
2016-06-30
2016-09-02
latest
```

------

#### Get the Top\-Level Metadata Items<a name="instance-metadata-ex-2"></a>

This example gets the top\-level metadata items\. For more information, see [Instance Metadata Categories](#instancedata-data-categories)\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \ 
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/latest/meta-data/    
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
iam/
instance-action
instance-id
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups
services/
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/    
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
iam/
instance-action
instance-id
instance-type
local-hostname
local-ipv4
mac
metrics/
network/
placement/
profile
public-hostname
public-ipv4
public-keys/
reservation-id
security-groups
services/
```

------

The following examples get the values of some of the top\-level metadata items that were obtained in the preceding example\. The IMDSv2 requests use the stored token that was created in the preceding example command, assuming it has not expired\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/ami-id
ami-0abcdef1234567890
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/ami-id
ami-0abcdef1234567890
```

------

 

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/reservation-id
r-0efghijk987654321
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/reservation-id
r-0efghijk987654321
```

------

 

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/local-hostname
ip-10-251-50-12.ec2.internal
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/local-hostname
ip-10-251-50-12.ec2.internal
```

------

 

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/public-hostname
ec2-203-0-113-25.compute-1.amazonaws.com
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-hostname
ec2-203-0-113-25.compute-1.amazonaws.com
```

------

#### Get the List of Available Public Keys<a name="instance-metadata-ex-3"></a>

This example gets the list of available public keys\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ `curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/public-keys/
0=my-public-key
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/
0=my-public-key
```

------

#### Show the Formats in Which Public Key 0 Is Available<a name="instance-metadata-ex-4"></a>

This example shows the formats in which public key 0 is available\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/public-keys/0/
openssh-key
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/0/
openssh-key
```

------

#### Get Public Key 0 \(in the OpenSSH Key Format\)<a name="instance-metadata-ex-5"></a>

This example gets public key 0 \(in the OpenSSH key format\)\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
ssh-rsa MIICiTCCAfICCQD6m7oRw0uXOjANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMC
VVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6
b24xFDASBgNVBAsTC0lBTSBDb25zb2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAd
BgkqhkiG9w0BCQEWEG5vb25lQGFtYXpvbi5jb20wHhcNMTEwNDI1MjA0NTIxWhcN
MTIwNDI0MjA0NTIxWjCBiDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYD
VQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6b24xFDASBgNVBAsTC0lBTSBDb25z
b2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAdBgkqhkiG9w0BCQEWEG5vb25lQGFt
YXpvbi5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMaK0dn+a4GmWIWJ
21uUSfwfEvySWtC2XADZ4nB+BLYgVIk60CpiwsZ3G93vUEIO3IyNoH/f0wYK8m9T
rDHudUZg3qX4waLG5M43q7Wgc/MbQITxOUSQv7c7ugFFDzQGBzZswY6786m86gpE
Ibb3OhjZnzcvQAaRHhdlQWIMm2nrAgMBAAEwDQYJKoZIhvcNAQEFBQADgYEAtCu4
nUhVVxYUntneD9+h8Mg9q6q+auNKyExzyLwaxlAoo7TJHidbtS4J5iNmZgXL0Fkb
FFBjvSfpJIlJ00zbhNYS5f6GuoEDmFJl0ZxBHjJnyp378OD8uTs7fLvjx79LjSTb
NYiytVbZPQUQ5Yaxu2jXnimvw3rrszlaEXAMPLE my-public-key
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/0/openssh-key
ssh-rsa MIICiTCCAfICCQD6m7oRw0uXOjANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMC
VVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6
b24xFDASBgNVBAsTC0lBTSBDb25zb2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAd
BgkqhkiG9w0BCQEWEG5vb25lQGFtYXpvbi5jb20wHhcNMTEwNDI1MjA0NTIxWhcN
MTIwNDI0MjA0NTIxWjCBiDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYD
VQQHEwdTZWF0dGxlMQ8wDQYDVQQKEwZBbWF6b24xFDASBgNVBAsTC0lBTSBDb25z
b2xlMRIwEAYDVQQDEwlUZXN0Q2lsYWMxHzAdBgkqhkiG9w0BCQEWEG5vb25lQGFt
YXpvbi5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAMaK0dn+a4GmWIWJ
21uUSfwfEvySWtC2XADZ4nB+BLYgVIk60CpiwsZ3G93vUEIO3IyNoH/f0wYK8m9T
rDHudUZg3qX4waLG5M43q7Wgc/MbQITxOUSQv7c7ugFFDzQGBzZswY6786m86gpE
Ibb3OhjZnzcvQAaRHhdlQWIMm2nrAgMBAAEwDQYJKoZIhvcNAQEFBQADgYEAtCu4
nUhVVxYUntneD9+h8Mg9q6q+auNKyExzyLwaxlAoo7TJHidbtS4J5iNmZgXL0Fkb
FFBjvSfpJIlJ00zbhNYS5f6GuoEDmFJl0ZxBHjJnyp378OD8uTs7fLvjx79LjSTb
NYiytVbZPQUQ5Yaxu2jXnimvw3rrszlaEXAMPLE my-public-key
```

------

#### Get the Subnet ID for an Instance<a name="instance-metadata-ex-6"></a>

This example gets the subnet ID for an instance\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/network/interfaces/macs/02:29:96:8f:6a:2d/subnet-id
subnet-be9b61d7
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/network/interfaces/macs/02:29:96:8f:6a:2d/subnet-id
subnet-be9b61d7
```

------

### Throttling<a name="instancedata-throttling"></a>

We throttle queries to the instance metadata service on a per\-instance basis, and we place limits on the number of simultaneous connections from an instance to the instance metadata service\. 

If you're using the instance metadata service to retrieve AWS security credentials, avoid querying for credentials during every transaction or concurrently from a high number of threads or processes, as this might lead to throttling\. Instead, we recommend that you cache the credentials until they start approaching their expiry time\. 

If you are throttled while accessing the instance metadata service, retry your query with an exponential backoff strategy\.

### Limiting Instance Metadata Service Access<a name="instance-metadata-limiting-access"></a>

You can consider using local firewall rules to disable access from some or all processes to the instance metadata service\.

**Using iptables to limit access**

The following example uses Linux iptables and its `owner` module to prevent the Apache webserver \(based on its default installation user ID of `apache`\) from accessing 169\.254\.169\.254\. It uses a *deny rule* to reject all instance metadata requests \(whether IMDSv1 or IMDSv2\) from any process running as that user\.

```
$ sudo iptables --append OUTPUT --proto tcp --destination 169.254.169.254 --match owner --uid-owner apache --jump REJECT
```

Or, you can consider only allowing access to particular users or groups, by using *allow rules*\. Allow rules might be easier to manage from a security perspective, because they require you to make a decision about what software needs access to instance metadata\. If you use *allow rules*, it's less likely you will accidentally allow software to access the metadata service \(that you did not intend to have access\) if you later change the software or configuration on an instance\. You can also combine group usage with allow rules, so that you can add and remove users from a permitted group without needing to change the firewall rule\.

The following example prevents access to the instance metadata service by all processes, except for processes running in the user account `trustworthy-user`\.

```
$ sudo iptables --append OUTPUT --proto tcp --destination 169.254.169.254 --match owner ! --uid-owner trustworthy-user --jump REJECT
```

**Note**  
To use local firewall rules, you need to adapt the preceding example commands to suit your needs\. 
By default, iptables rules are not persistent across system reboots\. They can be made to be persistent by using OS features, not described here\.
The iptables `owner` module only matches group membership if the group is the primary group of a given local user\. Other groups are not matched\.

**Using PF or IPFW to limit access**

If you are using FreeBSD or OpenBSD, you can also consider using PF or IPFW\. The following examples limit access to the instance metadata service to just the root user\.

**PF**

```
$ block out inet proto tcp from any to 169.254.169.254
```

```
$ pass out inet proto tcp from any to 169.254.169.254 user root
```

**IPFW**

```
$ allow tcp from any to 169.254.169.254 uid root
```

```
$ deny tcp from any to 169.254.169.254
```

**Note**  
The order of the PF and IPFW commands matter\. PF defaults to last matching rule and IPFW defaults to first matching rule\.

## Working with Instance User Data<a name="instancedata-add-user-data"></a>

When working with instance user data, keep the following in mind:
+ User data must be base64\-encoded\. The Amazon EC2 console can perform the base64\-encoding for you or accept base64\-encoded input\.
+ User data is limited to 16 KB, in raw form, before it is base64\-encoded\. The size of a string of length *n* after base64\-encoding is ceil\(*n*/3\)\*4\.
+ User data must be base64\-decoded when you retrieve it\. If you retrieve the data using instance metadata or the console, it's decoded for you automatically\.
+ User data is treated as opaque data: what you give is what you get back\. It is up to the instance to be able to interpret it\.
+ If you stop an instance, modify its user data, and start the instance, the updated user data is not executed when you start the instance\.

### Specify Instance User Data at Launch<a name="specify-user-data-launch"></a>

You can specify user data when you launch an instance\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) and [Running Commands on Your Linux Instance at Launch](user-data.md)\.

### Modify Instance User Data<a name="modify-user-data-run"></a>

You can modify user data for an instance in the stopped state if the root volume is an EBS volume\. For more information, see [View and Update the Instance User Data](user-data.md#user-data-view-change)\.

### Retrieve Instance User Data<a name="instancedata-user-data-retrieval"></a>

To retrieve user data from within a running instance, use the following URI\.

```
http://169.254.169.254/latest/user-data
```

A request for user data returns the data as it is \(content type `application/octet-stream`\)\. 

This example returns user data that was provided as comma\-separated text\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/user-data
1234,john,reboot,true | 4512,richard, | 173,,,
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/user-data
1234,john,reboot,true | 4512,richard, | 173,,,
```

------

This example returns user data that was provided as a script\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/user-data
#!/bin/bash
yum update -y
service httpd start
chkconfig httpd on
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/user-data
#!/bin/bash
yum update -y
service httpd start
chkconfig httpd on
```

------

To retrieve user data for an instance from your own computer, see [User Data and the AWS CLI](user-data.md#user-data-api-cli)

## Retrieving Dynamic Data<a name="instancedata-dynamic-data-retrieval"></a>

To retrieve dynamic data from within a running instance, use the following URI\. 

```
http://169.254.169.254/latest/dynamic/
```

This example shows how to retrieve the high\-level instance identity categories\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/dynamic/instance-identity/
rsa2048
pkcs7
document
signature
dsa2048
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/dynamic/instance-identity/
rsa2048
pkcs7
document
signature
dsa2048
```

------

For more information about dynamic data and examples of how to retrieve it, see [Instance Identity Documents](instance-identity-documents.md)\.

## Example: AMI Launch Index Value<a name="AMI-launch-index-examples"></a>

This example demonstrates how you can use both user data and instance metadata to configure your instances\. 

Alice wants to launch four instances of her favorite database AMI, with the first acting as master and the remaining three acting as replicas\. When she launches them, she wants to add user data about the replication strategy for each replicant\. She is aware that this data will be available to all four instances, so she needs to structure the user data in a way that allows each instance to recognize which parts are applicable to it\. She can do this using the `ami-launch-index` instance metadata value, which will be unique for each instance\.

Here is the user data that Alice has constructed\.

```
replicate-every=1min | replicate-every=5min | replicate-every=10min
```

The `replicate-every=1min` data defines the first replicant's configuration, `replicate-every=5min` defines the second replicant's configuration, and so on\. Alice decides to provide this data as an ASCII string with a pipe symbol \(`|`\) delimiting the data for the separate instances\.

Alice launches four instances using the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command, specifying the user data\. 

```
aws ec2 run-instances --image-id ami-0abcdef1234567890 --count 4 --instance-type t2.micro --user-data "replicate-every=1min | replicate-every=5min | replicate-every=10min"
```

After they're launched, all instances have a copy of the user data and the common metadata shown here:
+ AMI id: ami\-0abcdef1234567890
+ Reservation ID: r\-1234567890abcabc0
+ Public keys: none 
+ Security group name: default
+ Instance type: t2\.micro

However, each instance has certain unique metadata\.


**Instance 1**  

| Metadata | Value | 
| --- | --- | 
| instance\-id | i\-1234567890abcdef0 | 
| ami\-launch\-index | 0 | 
| public\-hostname | ec2\-203\-0\-113\-25\.compute\-1\.amazonaws\.com | 
| public\-ipv4 | 67\.202\.51\.223 | 
| local\-hostname | ip\-10\-251\-50\-12\.ec2\.internal | 
| local\-ipv4 | 10\.251\.50\.35 | 


**Instance 2**  

| Metadata | Value | 
| --- | --- | 
| instance\-id | i\-0598c7d356eba48d7 | 
| ami\-launch\-index | 1 | 
| public\-hostname | ec2\-67\-202\-51\-224\.compute\-1\.amazonaws\.com | 
| public\-ipv4 | 67\.202\.51\.224 | 
| local\-hostname | ip\-10\-251\-50\-36\.ec2\.internal | 
| local\-ipv4 | 10\.251\.50\.36 | 


**Instance 3**  

| Metadata | Value | 
| --- | --- | 
| instance\-id | i\-0ee992212549ce0e7 | 
| ami\-launch\-index | 2 | 
| public\-hostname | ec2\-67\-202\-51\-225\.compute\-1\.amazonaws\.com | 
| public\-ipv4 | 67\.202\.51\.225 | 
| local\-hostname | ip\-10\-251\-50\-37\.ec2\.internal | 
| local\-ipv4 | 10\.251\.50\.37 | 


**Instance 4**  

| Metadata | Value | 
| --- | --- | 
| instance\-id | i\-1234567890abcdef0 | 
| ami\-launch\-index | 3 | 
| public\-hostname | ec2\-67\-202\-51\-226\.compute\-1\.amazonaws\.com | 
| public\-ipv4 | 67\.202\.51\.226 | 
| local\-hostname | ip\-10\-251\-50\-38\.ec2\.internal | 
| local\-ipv4 | 10\.251\.50\.38 | 

Alice can use the `ami-launch-index` value to determine which portion of the user data is applicable to a particular instance\.

1. She connects to one of the instances, and retrieves the `ami-launch-index` for that instance to ensure it is one of the replicants:

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/meta-data/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
   && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/ami-launch-index
   2
   ```

   For the following steps, the IMDSv2 requests use the stored token from the preceding IMDSv2 command, assuming the token has not expired\.

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/ami-launch-index
   2
   ```

------

1. She saves the `ami-launch-index` as a variable\.

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ ami_launch_index=`curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/ami-launch-index`
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ ami_launch_index=`curl http://169.254.169.254/latest/meta-data/ami-launch-index`
   ```

------

1. She saves the user data as a variable\.

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ user_data=`curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/user-data`
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ user_data=`curl http://169.254.169.254/latest/user-data`
   ```

------

1. Finally, Alice uses the cut command to extract the portion of the user data that is applicable to that instance\.

------
#### [ IMDSv2 ]

   ```
   [ec2-user ~]$ echo $user_data | cut -d"|" -f"$ami_launch_index"replicate-every=5min
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ echo $user_data | cut -d"|" -f"$ami_launch_index"
   replicate-every=5min
   ```

------

## Instance Metadata Categories<a name="instancedata-data-categories"></a>

The following table lists the categories of instance metadata\.

**Important**  
Category names that are formatted in red text are placeholders for data that is unique to your instance; for example, *mac* represents the MAC address for the network interface\. You must replace the placeholders with the actual values\.


| Data | Description | Version Introduced | 
| --- | --- | --- | 
|  ami\-id  | The AMI ID used to launch the instance\. | 1\.0 | 
|  ami\-launch\-index  | If you started more than one instance at the same time, this value indicates the order in which the instance was launched\. The value of the first instance launched is 0\. | 1\.0 | 
|  ami\-manifest\-path  | The path to the AMI manifest file in Amazon S3\. If you used an Amazon EBS\-backed AMI to launch the instance, the returned result is unknown\. | 1\.0 | 
|  ancestor\-ami\-ids  | The AMI IDs of any instances that were rebundled to create this AMI\. This value will only exist if the AMI manifest file contained an ancestor\-amis key\. | 2007\-10\-10 | 
| block\-device\-mapping/ami  | The virtual device that contains the root/boot file system\. | 2007\-12\-15 | 
|  block\-device\-mapping/ebsN  | The virtual devices associated with any Amazon EBS volumes\. Amazon EBS volumes are only available in metadata if they were present at launch time or when the instance was last started\. The N indicates the index of the Amazon EBS volume \(such as ebs1 or ebs2\)\. | 2007\-12\-15 | 
|  block\-device\-mapping/ephemeralN  | The virtual devices for any non\-NVMe instance store volumes\. The N indicates the index of each volume\. The number of instance store volumes in the block device mapping might not match the actual number of instance store volumes for the instance\. The instance type determines the number of instance store volumes that are available to an instance\. If the number of instance store volumes in a block device mapping exceeds the number available to an instance, the additional instance store volumes are ignored\. | 2007\-12\-15 | 
|  block\-device\-mapping/root  | The virtual devices or partitions associated with the root devices or partitions on the virtual device, where the root \(/ or C:\) file system is associated with the given instance\. | 2007\-12\-15 | 
|  block\-device\-mapping/swap  | The virtual devices associated with swap\. Not always present\. | 2007\-12\-15 | 
| elastic\-gpus/associations/elastic\-gpu\-id | If there is an Elastic GPU attached to the instance, contains a JSON string with information about the Elastic GPU, including its ID and connection information\. | 2016\-11\-30 | 
| elastic\-inference/associations/eia\-id | If there is an Elastic Inference accelerator attached to the instance, contains a JSON string with information about the Elastic Inference accelerator, including its ID and type\. | 2018\-11\-29 | 
| events/maintenance/history | If there are completed or canceled maintenance events for the instance, contains a JSON string with information about the events\. For more information, see [To view event history about completed or canceled events](monitoring-instances-status-check_sched.md#viewing-event-history)\. | 2018\-08\-17 | 
| events/maintenance/scheduled | If there are active maintenance events for the instance, contains a JSON string with information about the events\. For more information, see [Viewing Scheduled Events](monitoring-instances-status-check_sched.md#viewing_scheduled_events)\. | 2018\-08\-17 | 
| hostname | The private IPv4 DNS hostname of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 1\.0 | 
|  iam/info  | If there is an IAM role associated with the instance, contains information about the last time the instance profile was updated, including the instance's LastUpdated date, InstanceProfileArn, and InstanceProfileId\. Otherwise, not present\. | 2012\-01\-12 | 
|  iam/security\-credentials/role\-name  | If there is an IAM role associated with the instance, role\-name is the name of the role, and role\-name contains the temporary security credentials associated with the role \(for more information, see [Retrieving Security Credentials from Instance Metadata](iam-roles-for-amazon-ec2.md#instance-metadata-security-credentials)\)\. Otherwise, not present\. | 2012\-01\-12 | 
| identity\-credentials/ec2/info | \[Reserved for internal use only\] Information about the credentials that AWS uses to identify an instance to the rest of the Amazon EC2 infrastructure\. | 2018\-05\-23 | 
| identity\-credentials/ec2/security\-credentials/ec2\-instance | \[Reserved for internal use only\] The credentials that AWS uses to identify an instance to the rest of the Amazon EC2 infrastructure\. | 2018\-05\-23 | 
|  instance\-action  | Notifies the instance that it should reboot in preparation for bundling\. Valid values: none \| shutdown \| bundle\-pending\. | 2008\-09\-01 | 
|  instance\-id  | The ID of this instance\. | 1\.0 | 
|  instance\-type  | The type of instance\. For more information, see [Instance Types](instance-types.md)\. | 2007\-08\-29 | 
|  kernel\-id  | The ID of the kernel launched with this instance, if applicable\. | 2008\-02\-01 | 
|  local\-hostname  | The private IPv4 DNS hostname of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 2007\-01\-19 | 
|  local\-ipv4  | The private IPv4 address of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 1\.0 | 
|  mac  | The instance's media access control \(MAC\) address\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 2011\-01\-01 | 
| metrics/vhostmd | No longer available\. | 2011\-05\-01 | 
|  network/interfaces/macs/mac/device\-number  | The unique device number associated with that interface\. The device number corresponds to the device name; for example, a device\-number of 2 is for the eth2 device\. This category corresponds to the DeviceIndex and device\-index fields that are used by the Amazon EC2 API and the EC2 commands for the AWS CLI\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/interface\-id  | The ID of the network interface\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/ipv4\-associations/public\-ip  | The private IPv4 addresses that are associated with each public IP address and assigned to that interface\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/ipv6s | The IPv6 addresses associated with the interface\. Returned only for instances launched into a VPC\.  | 2016\-06\-30  | 
|  network/interfaces/macs/mac/local\-hostname  | The interface's local hostname\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/local\-ipv4s  | The private IPv4 addresses associated with the interface\.  | 2011\-01\-01 | 
|  network/interfaces/macs/mac/mac  | The instance's MAC address\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/owner\-id  | The ID of the owner of the network interface\. In multiple\-interface environments, an interface can be attached by a third party, such as Elastic Load Balancing\. Traffic on an interface is always billed to the interface owner\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/public\-hostname  | The interface's public DNS \(IPv4\)\. This category is only returned if the enableDnsHostnames attribute is set to true\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/public\-ipv4s  | The public IP address or Elastic IP addresses associated with the interface\. There may be multiple IPv4 addresses on an instance\.  | 2011\-01\-01 | 
| network/interfaces/macs/mac/security\-groups  | Security groups to which the network interface belongs\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/security\-group\-ids  | The IDs of the security groups to which the network interface belongs\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/subnet\-id  | The ID of the subnet in which the interface resides\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/subnet\-ipv4\-cidr\-block  | The IPv4 CIDR block of the subnet in which the interface resides\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/subnet\-ipv6\-cidr\-blocks  | The IPv6 CIDR block of the subnet in which the interface resides\. | 2016\-06\-30  | 
|  network/interfaces/macs/mac/vpc\-id  | The ID of the VPC in which the interface resides\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/vpc\-ipv4\-cidr\-block  | The primary IPv4 CIDR block of the VPC\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/vpc\-ipv4\-cidr\-blocks  | The IPv4 CIDR blocks for the VPC\. | 2016\-06\-30  | 
| network/interfaces/macs/mac/vpc\-ipv6\-cidr\-blocks  | The IPv6 CIDR block of the VPC in which the interface resides\. | 2016\-06\-30  | 
|  placement/availability\-zone  | The Availability Zone in which the instance launched\. | 2008\-02\-01 | 
|  product\-codes  | AWS Marketplace product codes associated with the instance, if any\.  | 2007\-03\-01 | 
|  public\-hostname  | The instance's public DNS\. This category is only returned if the enableDnsHostnames attribute is set to true\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the Amazon VPC User Guide\. | 2007\-01\-19 | 
|  public\-ipv4  | The public IPv4 address\. If an Elastic IP address is associated with the instance, the value returned is the Elastic IP address\. | 2007\-01\-19 | 
|  public\-keys/0/openssh\-key  | Public key\. Only available if supplied at instance launch time\. | 1\.0 | 
|  ramdisk\-id  | The ID of the RAM disk specified at launch time, if applicable\. | 2007\-10\-10 | 
|  reservation\-id  | The ID of the reservation\. | 1\.0 | 
|  security\-groups  |  The names of the security groups applied to the instance\. After launch, you can change the security groups of the instances\. Such changes are reflected here and in network/interfaces/macs/**mac**/security\-groups\.  | 1\.0 | 
|  services/domain  |  The domain for AWS resources for the Region\.  | 2014\-02\-25 | 
|  services/partition  |  The partition that the resource is in\. For standard AWS Regions, the partition is `aws`\. If you have resources in other partitions, the partition is `aws-partitionname`\. For example, the partition for resources in the China \(Beijing\) Region is `aws-cn`\.  | 2015\-10\-20 | 
|  spot/instance\-action  |  The action \(hibernate, stop, or terminate\) and the approximate time, in UTC, when the action will occur\. This item is present only if the Spot Instance has been marked for hibernate, stop, or terminate\. For more information, see [instance\-action](spot-interruptions.md#instance-action-metadata)\.  | 2016\-11\-15 | 
|  spot/termination\-time  |  The approximate time, in UTC, that the operating system for your Spot Instance will receive the shutdown signal\. This item is present and contains a time value \(for example, 2015\-01\-05T18:02:00Z\) only if the Spot Instance has been marked for termination by Amazon EC2\. The termination\-time item is not set to a time if you terminated the Spot Instance yourself\. For more information, see [termination\-time](spot-interruptions.md#termination-time-metadata)\.  | 2014\-11\-05 | 

### Dynamic Data Categories<a name="dynamic-data-categories"></a>

The following table lists the categories of dynamic data\.


| Data | Description | Version introduced | 
| --- | --- | --- | 
|  fws/instance\-monitoring  | Value showing whether the customer has enabled detailed one\-minute monitoring in CloudWatch\. Valid values: enabled \| disabled | 2009\-04\-04 | 
| instance\-identity/document  | JSON containing instance attributes, such as instance\-id, private IP address, etc\. See [Instance Identity Documents](instance-identity-documents.md)\.  | 2009\-04\-04 | 
| instance\-identity/pkcs7  | Used to verify the document's authenticity and content against the signature\. See [Instance Identity Documents](instance-identity-documents.md)\.  | 2009\-04\-04 | 
| instance\-identity/signature  | Data that can be used by other parties to verify its origin and authenticity\. See [Instance Identity Documents](instance-identity-documents.md)\.  | 2009\-04\-04 | 
# Use IMDSv2<a name="configuring-instance-metadata-service"></a>

You can access instance metadata from a running instance using one of the following methods:
+ Instance Metadata Service Version 1 \(IMDSv1\) – a request/response method
+ Instance Metadata Service Version 2 \(IMDSv2\) – a session\-oriented method

By default, you can use either IMDSv1 or IMDSv2, or both\. The instance metadata service distinguishes between IMDSv1 and IMDSv2 requests based on whether, for any given request, either the `PUT` or `GET` headers, which are unique to IMDSv2, are present in that request\. For more information, see [Add defense in depth against open firewalls, reverse proxies, and SSRF vulnerabilities with enhancements to the EC2 Instance Metadata Service](http://aws.amazon.com/blogs/security/defense-in-depth-open-firewalls-reverse-proxies-ssrf-vulnerabilities-ec2-instance-metadata-service/)\.

You can configure the instance metadata service on each instance so that local code or users must use IMDSv2\. When you specify that IMDSv2 must be used, IMDSv1 no longer works\. For more information, see [Configure the instance metadata options](configuring-instance-metadata-options.md)\.

To retrieve instance metadata, see [Retrieve instance metadata](instancedata-data-retrieval.md)\.

**Note**  
The examples in this section use the IPv4 address of the instance metadata service: `169.254.169.254`\. If you are retrieving instance metadata for EC2 instances over the IPv6 address, ensure that you enable and use the IPv6 address instead: `fd00:ec2::254`\. The IPv6 address of the instance metadata service is compatible with IMDSv2 commands\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

## How Instance Metadata Service Version 2 works<a name="instance-metadata-v2-how-it-works"></a>

IMDSv2 uses session\-oriented requests\. With session\-oriented requests, you create a session token that defines the session duration, which can be a minimum of one second and a maximum of six hours\. During the specified duration, you can use the same session token for subsequent requests\. After the specified duration expires, you must create a new session token to use for future requests\.

The following example uses a Linux shell script and IMDSv2 to retrieve the top\-level instance metadata items\. The example: 
+ Creates a session token lasting six hours \(21,600 seconds\) using the `PUT` request
+ Stores the session token header in a variable named `TOKEN`
+ Requests the top\-level metadata items using the token

You can run two separate commands, or combine them\.

**Separate commands**

First, generate a token using the following command\.

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"`
```

Then, use the token to generate top\-level metadata items using the following command\.

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/
```

**Combined commands**

You can store the token and combine the commands\. The following example combines the above two commands and stores the session token header in a variable named TOKEN\.

**Note**  
If there is an error in creating the token, instead of a valid token, an error message is stored in the variable, and the command will not work\.

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

**AWS software**  
The latest versions of the AWS CLI and AWS SDKs support IMDSv2\. To use IMDSv2, make sure that your EC2 instances have the latest versions of the CLI and SDKs\. For information about updating the CLI, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.  
All Amazon Linux 2 software packages support IMDSv2\.  
For the minimum AWS SDK versions that support IMDSv2, see [Use a supported AWS SDK](#use-a-supported-sdk-version-for-imdsv2)\.

**CloudWatch**  
IMDSv2 uses token\-backed sessions, while IMDSv1 does not\. The `MetadataNoToken` CloudWatch metric tracks the number of calls to the instance metadata service that are using IMDSv1\. By tracking this metric to zero, you can determine if and when all of your software has been upgraded to use IMDSv2\. For more information, see [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\.

**Updates to EC2 APIs and CLIs**  
For existing instances, you can use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command \(or the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\) to require the use of IMDSv2\. For new instances, you can use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) CLI command \(or the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API\) and the `metadata-options` parameter to launch new instances that require the use of IMDSv2\.  
To require the use of IMDSv2 on all new instances launched by Auto Scaling groups, your Auto Scaling groups can use either a launch template or a launch configuration\. When you [create a launch template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) or [create a launch configuration](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-launch-configuration.html), you must configure the `MetadataOptions` parameters to require the use of IMDSv2\. After you configure the launch template or launch configuration, the Auto Scaling group launches new instances using the new launch template or launch configuration, but existing instances are not affected\.  
Use the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command \(or the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API\) to require the use of IMDSv2 on the existing instances, or terminate the instances and the Auto Scaling group will launch new replacement instances with the instance metadata options settings that are defined in the launch template or launch configuration\.

**Use an AMI that configures IMDSv2 by default**  
When you launch an instance, you can automatically configure it to use IMDSv2 by default \(the `HttpTokens` parameter is set to `required`\) by launching it with an AMI that is configured with the `ImdsSupport` parameter set to `v2.0`\. You set the `ImdsSupport` parameter to `v2.0` when you register the AMI using the [register\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/register-image.html) CLI command\. For more information, see [Configure the AMI](configuring-IMDS-new-instances.md#configure-IMDS-new-instances-ami-configuration)\.

**IAM policies and SCPs**  
You can use an IAM policy or AWS Organizations service control policy \(SCP\) to control IAM users as follows:  
+ Can't launch an instance using the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) API unless the instance is configured to use IMDSv2\.
+ Can't modify a running instance using the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API to re\-enable IMDSv1\.
The IAM policy or SCP must contain the following IAM condition keys:  
+ `ec2:MetadataHttpEndpoint`
+ `ec2:MetadataHttpPutResponseHopLimit`
+ `ec2:MetadataHttpTokens`
If a parameter in the API or CLI call does not match the state specified in the policy that contains the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\.  
Furthermore, you can choose an additional layer of protection to enforce the change from IMDSv1 to IMDSv2\. At the access management layer with respect to the APIs called via EC2 Role credentials, you can use a new condition key in either IAM policies or AWS Organizations service control policies \(SCPs\)\. Specifically, by using the condition key `ec2:RoleDelivery` with a value of `2.0` in your IAM policies, API calls made with EC2 Role credentials obtained from IMDSv1 will receive an `UnauthorizedOperation` response\. The same thing can be achieved more broadly with that condition required by an SCP\. This ensures that credentials delivered via IMDSv1 cannot actually be used to call APIs because any API calls not matching the specified condition will receive an `UnauthorizedOperation` error\.  
For example IAM policies, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\. For more information on SCPs, see [Service Control Policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scp.html) in the *AWS Organizations User Guide*\.

**Recommended path to requiring IMDSv2 access**

Using the above tools, we recommend that you follow this path for transitioning to IMDSv2:

### Step 1: At the start<a name="path-step-1"></a>

Update the SDKs, CLIs, and your software that use Role credentials on their EC2 instances to versions compatible with IMDSv2\. For information about updating the CLI, see [Upgrading to the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-linux.html#install-linux-awscli-upgrade) in the *AWS Command Line Interface User Guide*\.

Then, change your software that directly accesses instance metadata \(in other words, that does not use an SDK\) using the IMDSv2 requests\. 

### Step 2: Track your transition progress<a name="path-step-2"></a>

Track your transition progress by using the CloudWatch metric `MetadataNoToken`\. This metric shows the number of calls to the instance metadata service that are using IMDSv1 on your instances\. For more information, see [Instance metrics](viewing_metrics_with_cloudwatch.md#ec2-cloudwatch-metrics)\. 

### Step 3: When there is zero IMDSv1 usage<a name="path-step-3"></a>

When the CloudWatch metric `MetadataNoToken` records zero IMDSv1 usage, your instances are ready to be fully transitioned to using IMDSv2\. At this stage, you can do the following:
+ **New instances**

  When launching a new instance, you can do the following:
  + Amazon EC2 console: In the launch instance wizard, set **Metadata accessible** to **Enabled** and **Metadata version** to **V2 only \(token required\)**\. For more information, see [Configure the instance at launch](configuring-IMDS-new-instances.md#configure-IMDS-new-instances-instance-settings)\.
  + AWS CLI: Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) CLI command to specify that only IMDSv2 is to be used\.
+ **Existing instances**

  You can require IMDSv2 use through the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) CLI command\. You can make these changes on running instances; you don't need to restart your instances\. 

**Note**  
Updating instance metadata options for existing instances is available only through the API or AWS CLI\. It is currently not available in the Amazon EC2 console\. For more information, see [Configure the instance metadata options](configuring-instance-metadata-options.md)\.

### Step 4: When all of your instances are transitioned to IMDSv2<a name="path-step-4"></a>

The `ec2:MetadataHttpTokens`, `ec2:MetadataHttpPutResponseHopLimit`, and `ec2:MetadataHttpEndpoint` IAM condition keys can be used to control the use of the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RunInstances.html) and the [ModifyInstanceMetadataOptions](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceMetadataOptions.html) API and corresponding CLI\. If a policy is created, and a parameter in the API call does not match the state specified in the policy using the condition key, the API or CLI call fails with an `UnauthorizedOperation` response\. For example IAM policies, see [Work with instance metadata](ExamplePolicies_EC2.md#iam-example-instance-metadata)\.

## Use a supported AWS SDK<a name="use-a-supported-sdk-version-for-imdsv2"></a>

To use IMDSv2, your EC2 instances must use an AWS SDK version that supports using IMDSv2\. The latest versions of the all AWS SDKs support using IMDSv2\.

**Important**  
We recommend that you to stay up to date with SDK releases to keep up with the latest features, security updates, and underlying dependencies\. Continued use of an unsupported SDK version is not recommended and is done at your discretion\. For more information, see the [AWS SDKs and Tools maintenance policy](https://docs.aws.amazon.com/sdkref/latest/guide/maint-policy.html) in the *AWS SDKs and Tools Reference Guide*\.

The following are the minimum versions that support using IMDSv2:
+ [AWS CLI](https://github.com/aws/aws-cli) – 1\.13\.23
+ [AWS SDK for \.NET](https://github.com/aws/aws-sdk-net) – 3\.3\.634\.1
+ [AWS SDK for C\+\+](https://github.com/aws/aws-sdk-cpp) – 1\.7\.229
+ [AWS SDK for Go](https://github.com/aws/aws-sdk-go) – 1\.25\.38
+ [AWS SDK for Go v2](https://github.com/aws/aws-sdk-go-v2) – 0\.19\.0
+ [AWS SDK for Java](https://github.com/aws/aws-sdk-java) – 1\.11\.678
+ [AWS SDK for Java 2\.x](https://github.com/aws/aws-sdk-java-v2) – 2\.10\.21
+ [AWS SDK for JavaScript in Node\.js](https://github.com/aws/aws-sdk-js) – 2\.722\.0
+ [AWS SDK for PHP](https://github.com/aws/aws-sdk-php) – 3\.147\.7
+ [AWS SDK for Python \(Boto\)](https://github.com/boto/botocore) – 1\.13\.23
+ [AWS SDK for Python \(Boto3\)](https://github.com/boto/boto3) – 1\.12\.6
+ [AWS SDK for Ruby](https://github.com/aws/aws-sdk-ruby) – 3\.79\.0
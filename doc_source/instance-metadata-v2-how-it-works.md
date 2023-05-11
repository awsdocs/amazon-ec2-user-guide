# How Instance Metadata Service Version 2 works<a name="instance-metadata-v2-how-it-works"></a>

IMDSv2 uses session\-oriented requests\. With session\-oriented requests, you create a session token that defines the session duration, which can be a minimum of one second and a maximum of six hours\. During the specified duration, you can use the same session token for subsequent requests\. After the specified duration expires, you must create a new session token to use for future requests\.

**Note**  
The examples in this section use the IPv4 address of the Instance Metadata Service \(IMDS\): `169.254.169.254`\. If you are retrieving instance metadata for EC2 instances over the IPv6 address, ensure that you enable and use the IPv6 address instead: `fd00:ec2::254`\. The IPv6 address of the IMDS is compatible with IMDSv2 commands\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

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

1. Include the token in all `GET` requests to the IMDS\. When token usage is set to `required`, requests without a valid token or with an expired token receive a `401 - Unauthorized` HTTP error code\.
   + The token is an instance\-specific key\. The token is not valid on other EC2 instances and will be rejected if you attempt to use it outside of the instance on which it was generated\.
   + The `PUT` request must include a header that specifies the time to live \(TTL\) for the token, in seconds, up to a maximum of six hours \(21,600 seconds\)\. The token represents a logical session\. The TTL specifies the length of time that the token is valid and, therefore, the duration of the session\.
   + After a token expires, to continue accessing instance metadata, you must create a new session using another `PUT`\.
   + You can choose to reuse a token or create a new token with every request\. For a small number of requests, it might be easier to generate and immediately use a token each time you need to access the IMDS\. But for efficiency, you can specify a longer duration for the token and reuse it rather than having to write a `PUT` request every time you need to request instance metadata\. There is no practical limit on the number of concurrent tokens, each representing its own session\. IMDSv2 is, however, still constrained by normal IMDS connection and throttling limits\. For more information, see [Query throttling](instancedata-data-retrieval.md#instancedata-throttling)\.

HTTP `GET` and `HEAD` methods are allowed in IMDSv2 instance metadata requests\. `PUT` requests are rejected if they contain an X\-Forwarded\-For header\.

By default, the response to `PUT` requests has a response hop limit \(time to live\) of `1` at the IP protocol level\. If you need a bigger hop limit, you can adjust it by using the [modify\-instance\-metadata\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-metadata-options.html) AWS CLI command\. For example, you might need a bigger hop limit for backward compatibility with container services running on the instance\. For more information, see [Modify instance metadata options for existing instances](configuring-IMDS-existing-instances.md)\.
# Retrieve instance metadata<a name="instancedata-data-retrieval"></a>

Because your instance metadata is available from your running instance, you do not need to use the Amazon EC2 console or the AWS CLI\. This can be helpful when you're writing scripts to run from your instance\. For example, you can access the local IP address of your instance from instance metadata to manage a connection to an external application\.

Instance metadata is divided into categories\. For a description of each instance metadata category, see [Instance metadata categories](instancedata-data-categories.md)\.

To view all categories of instance metadata from within a running instance, use the following IPv4 or IPv6 URIs\.

**IPv4**

```
http://169.254.169.254/latest/meta-data/
```

**IPv6**

```
http://[fd00:ec2::254]/latest/meta-data/
```

The IP addresses are link\-local addresses and are valid only from the instance\. For more information, see [Link\-local address](https://en.wikipedia.org/wiki/Link-local_address) on Wikipedia\.

**Note**  
The examples in this section use the IPv4 address of the instance metadata service: `169.254.169.254`\. If you are retrieving instance metadata for EC2 instances over the IPv6 address, ensure that you enable and use the IPv6 address instead: `fd00:ec2::254`\. The IPv6 address of the instance metadata service is compatible with IMDSv2 commands\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

The command format is different, depending on whether you use IMDSv1 or IMDSv2\. By default, you can use both instance metadata services\. To require the use of IMDSv2, see [Use IMDSv2](configuring-instance-metadata-service.md)\.

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

For the command to retrieve instance metadata from a Windows instance, see [Retrieve instance metadata](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/instancedata-data-retrieval.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Costs<a name="imds-costs"></a>

You are not billed for HTTP requests used to retrieve instance metadata and user data\.

## Considerations<a name="imds-considerations"></a>

To avoid problems with instance metadata retrieval, consider the following:
+ The AWS SDKs use IMDSv2 calls by default\. If the IMDSv2 call receives no response, the SDK retries the call and, if still unsuccessful, uses IMDSv1\. This can result in a delay\. In a container environment, if the hop limit is 1, the IMDSv2 response does not return because going to the container is considered an additional network hop\. To avoid the process of falling back to IMDSv1 and the resultant delay, in a container environment we recommend that you set the hop limit to 2\. For more information, see [Configure the instance metadata options](configuring-instance-metadata-options.md)\.
+ For IMDSv2, you must use `/latest/api/token` when retrieving the token\. Issuing `PUT` requests to any version\-specific path, for example `/2021-03-23/api/token`, will result in the metadata service returning 403 Forbidden errors\. This behavior is intended\. 

  

## Responses and error messages<a name="instance-metadata-returns"></a>

All instance metadata is returned as text \(HTTP content type `text/plain`\)\.

A request for a specific metadata resource returns the appropriate value, or a `404 - Not Found` HTTP error code if the resource is not available\. 

A request for a general metadata resource \(the URI ends with a /\) returns a list of available resources, or a `404 - Not Found` HTTP error code if there is no such resource\. The list items are on separate lines, terminated by line feeds \(ASCII 10\)\.

For requests made using Instance Metadata Service Version 2, the following HTTP error codes can be returned:
+ `400 - Missing or Invalid Parameters` – The `PUT` request is not valid\.
+ `401 - Unauthorized` – The `GET` request uses an invalid token\. The recommended action is to generate a new token\.
+ `403 - Forbidden` – The request is not allowed or the instance metadata service is turned off\.

## Examples of retrieving instance metadata<a name="instancedata-meta-data-retrieval-examples"></a>

The following examples provide commands that you can use on a Linux instance\. For the commands to retrieve instance metadata from a Windows instance, see [Retrieve instance metadata](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/instancedata-data-retrieval.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Get the available versions of the instance metadata](#instance-metadata-ex-1)
+ [Get the top\-level metadata items](#instance-metadata-ex-2)
+ [Get the list of available public keys](#instance-metadata-ex-3)
+ [Show the formats in which public key 0 is available](#instance-metadata-ex-4)
+ [Get public key 0 \(in the OpenSSH key format\)](#instance-metadata-ex-5)
+ [Get the subnet ID for an instance](#instance-metadata-ex-6)
+ [Get the instance tags for an instance](#instance-metadata-ex-7)

### Get the available versions of the instance metadata<a name="instance-metadata-ex-1"></a>

This example gets the available versions of the instance metadata\. Each version refers to an instance metadata build when new instance metadata categories were released\. The instance metadata build versions do not correlate with the Amazon EC2 API versions\. The earlier versions are available to you in case you have scripts that rely on the structure and information present in a previous version\.

**Note**  
To avoid having to update your code every time Amazon EC2 releases a new instance metadata build, we recommend that you use `latest` in the path, and not the version number\. For example, use `latest` as follows:  
`curl http://169.254.169.254/latest/meta-data/ami-id`

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
2018-03-28
2018-08-17
2018-09-24
2019-10-01
2020-10-27
2021-01-03
2021-03-23
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
2018-03-28
2018-08-17
2018-09-24
2019-10-01
2020-10-27
2021-01-03
2021-03-23
latest
```

------

### Get the top\-level metadata items<a name="instance-metadata-ex-2"></a>

This example gets the top\-level metadata items\. For more information, see [Instance metadata categories](instancedata-data-categories.md)\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/    
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
events/
hostname
iam/
instance-action
instance-id
instance-life-cycle
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

### Get the list of available public keys<a name="instance-metadata-ex-3"></a>

This example gets the list of available public keys\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ `curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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

### Show the formats in which public key 0 is available<a name="instance-metadata-ex-4"></a>

This example shows the formats in which public key 0 is available\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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

### Get public key 0 \(in the OpenSSH key format\)<a name="instance-metadata-ex-5"></a>

This example gets public key 0 \(in the OpenSSH key format\)\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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

### Get the subnet ID for an instance<a name="instance-metadata-ex-6"></a>

This example gets the subnet ID for an instance\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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

### Get the instance tags for an instance<a name="instance-metadata-ex-7"></a>

In the following examples, the sample instance has [tags on instance metadata enabled](Using_Tags.md#allow-access-to-tags-in-IMDS) and the instance tags `Name=MyInstance` and `Environment=Dev`\.

This example gets all the instance tag keys for an instance\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
&& curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/tags/instance
Name
Environment
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/tags/instance
Name
Environment
```

------

The following example gets the value of the `Name` key that was obtained in the preceding example\. The IMDSv2 request uses the stored token that was created in the preceding example command, assuming it has not expired\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/tags/instance/Name
MyInstance
```

------
#### [ IMDSv1 ]

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/tags/instance/Name
MyInstance
```

------

## Query throttling<a name="instancedata-throttling"></a>

We throttle queries to the instance metadata service on a per\-instance basis, and we place limits on the number of simultaneous connections from an instance to the instance metadata service\. 

If you're using the instance metadata service to retrieve AWS security credentials, avoid querying for credentials during every transaction or concurrently from a high number of threads or processes, as this might lead to throttling\. Instead, we recommend that you cache the credentials until they start approaching their expiry time\. 

If you are throttled while accessing the instance metadata service, retry your query with an exponential backoff strategy\.

## Limit instance metadata service access<a name="instance-metadata-limiting-access"></a>

You can consider using local firewall rules to disable access from some or all processes to the instance metadata service\.

**Note**  
For [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances), IMDS can be reached from your own network when a network appliance within your VPC, such as a virtual router, forwards packets to the IMDS address, and the default [source/destination check](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html#EIP_Disable_SrcDestCheck) on the instance is disabled\. To prevent a source from outside your VPC reaching IMDS, we recommend that you modify the configuration of the network appliance to drop packets with the destination IPv4 address of IMDS 169\.254\.169\.254 and, if you enabled the IPv6 endpoint, the IPv6 address of IMDS fd00:ec2::254\.

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

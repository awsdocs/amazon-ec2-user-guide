# Work with instance user data<a name="instancedata-add-user-data"></a>

When working with instance user data, keep the following in mind:
+ User data must be base64\-encoded\. The Amazon EC2 console can perform the base64\-encoding for you or accept base64\-encoded input\.
+ User data is limited to 16 KB, in raw form, before it is base64\-encoded\. The size of a string of length *n* after base64\-encoding is ceil\(*n*/3\)\*4\.
+ User data must be base64\-decoded when you retrieve it\. If you retrieve the data using instance metadata or the console, it's decoded for you automatically\.
+ User data is treated as opaque data: what you give is what you get back\. It is up to the instance to be able to interpret it\.
+ If you stop an instance, modify its user data, and start the instance, the updated user data is not run when you start the instance\.

## Specify instance user data at launch<a name="specify-user-data-launch"></a>

You can specify user data when you launch an instance\. For more information, see [Launch an instance using the old launch instance wizard](launching-instance.md) and [Run commands on your Linux instance at launch](user-data.md)\.

## Modify instance user data<a name="modify-user-data-run"></a>

You can modify user data for an instance in the stopped state if the root volume is an EBS volume\. For more information, see [View and update the instance user data](user-data.md#user-data-view-change)\.

## Retrieve instance user data<a name="instancedata-user-data-retrieval"></a>

**Note**  
The examples in this section use the IPv4 address of the instance metadata service: `169.254.169.254`\. If you are retrieving instance metadata for EC2 instances over the IPv6 address, ensure that you enable and use the IPv6 address instead: `fd00:ec2::254`\. The IPv6 address of the instance metadata service is compatible with IMDSv2 commands\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

To retrieve user data from within a running instance, use the following URI\.

```
http://169.254.169.254/latest/user-data
```

A request for user data returns the data as it is \(content type `application/octet-stream`\)\. 

This example returns user data that was provided as comma\-separated text\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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

To retrieve user data for an instance from your own computer, see [User data and the AWS CLI](user-data.md#user-data-api-cli)\.
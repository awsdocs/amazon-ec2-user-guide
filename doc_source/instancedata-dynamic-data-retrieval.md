# Retrieving dynamic data<a name="instancedata-dynamic-data-retrieval"></a>

To retrieve dynamic data from within a running instance, use the following URI\. 

```
http://169.254.169.254/latest/dynamic/
```

This example shows how to retrieve the high\-level instance identity categories\.

------
#### [ IMDSv2 ]

```
[ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
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
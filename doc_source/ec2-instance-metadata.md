# Instance Metadata and User Data<a name="ec2-instance-metadata"></a>

*Instance metadata* is data about your instance that you can use to configure or manage the running instance\. Instance metadata is divided into categories\. For more information, see [Instance Metadata Categories](#instancedata-data-categories)\.

**Important**  
Although you can only access instance metadata and user data from within the instance itself, the data is not protected by cryptographic methods\. Anyone who can access the instance can view its metadata\. Therefore, you should take suitable precautions to protect sensitive data \(such as long\-lived encryption keys\)\. You should not store sensitive data, such as passwords, as user data\.

You can also use instance metadata to access *user data* that you specified when launching your instance\. For example, you can specify parameters for configuring your instance, or attach a simple script\. You can also use this data to build more generic AMIs that can be modified by configuration files supplied at launch time\. For example, if you run web servers for various small businesses, they can all use the same AMI and retrieve their content from the Amazon S3 bucket you specify in the user data at launch\. To add a new customer at any time, simply create a bucket for the customer, add their content, and launch your AMI\. If you launch more than one instance at the same time, the user data is available to all instances in that reservation\.

EC2 instances can also include *dynamic data*, such as an instance identity document that is generated when the instance is launched\. For more information, see [Dynamic Data Categories](#dynamic-data-categories)\.

**Topics**
+ [Retrieving Instance Metadata](#instancedata-data-retrieval)
+ [Working with Instance User Data](#instancedata-add-user-data)
+ [Retrieving Dynamic Data](#instancedata-dynamic-data-retrieval)
+ [Example: AMI Launch Index Value](#AMI-launch-index-examples)
+ [Instance Metadata Categories](#instancedata-data-categories)
+ [Instance Identity Documents](instance-identity-documents.md)

## Retrieving Instance Metadata<a name="instancedata-data-retrieval"></a>

Because your instance metadata is available from your running instance, you do not need to use the Amazon EC2 console or the AWS CLI\. This can be helpful when you're writing scripts to run from your instance\. For example, you can access the local IP address of your instance from instance metadata to manage a connection to an external application\.

To view all categories of instance metadata from within a running instance, use the following URI:

```
http://169.254.169.254/latest/meta-data/
```

Note that you are not billed for HTTP requests used to retrieve instance metadata and user data\.

You can use a tool such as cURL, or if your instance supports it, the GET command; for example:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/
```

```
[ec2-user ~]$ GET http://169.254.169.254/latest/meta-data/
```

You can also download the [Instance Metadata Query tool](https://aws.amazon.com/code/ec2-instance-metadata-query-tool/), which allows you to query the instance metadata without having to type out the full URI or category names\.

All instance metadata is returned as text \(content type `text/plain`\)\. A request for a specific metadata resource returns the appropriate value, or a `404 - Not Found` HTTP error code if the resource is not available\. 

A request for a general metadata resource \(the URI ends with a /\) returns a list of available resources, or a `404 - Not Found` HTTP error code if there is no such resource\. The list items are on separate lines, terminated by line feeds \(ASCII 10\)\. 

### Examples of Retrieving Instance Metadata<a name="instancedata-meta-data-retrieval-examples"></a>

This example gets the available versions of the instance metadata\. These versions do not necessarily correlate with an Amazon EC2 API version\. The earlier versions are available to you in case you have scripts that rely on the structure and information present in a previous version\. 

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

This example gets the top\-level metadata items\. For more information, see [Instance Metadata Categories](#instancedata-data-categories)\.

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/    
ami-id
ami-launch-index
ami-manifest-path
block-device-mapping/
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

These examples get the value of some of the metadata items from the preceding example\.

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/ami-id
ami-12345678
```

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/reservation-id
r-fea54097
```

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/local-hostname
ip-10-251-50-12.ec2.internal
```

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-hostname
ec2-203-0-113-25.compute-1.amazonaws.com
```

This example gets the list of available public keys\.

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/
0=my-public-key
```

This example shows the formats in which public key 0 is available\. 

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/public-keys/0/
openssh-key
```

This example gets public key 0 \(in the OpenSSH key format\)\.

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

This example gets the subnet ID for an instance\.

```
[ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/network/interfaces/macs/02:29:96:8f:6a:2d/subnet-id
subnet-be9b61d7
```

### Throttling<a name="instancedata-throttling"></a>

We throttle queries to the instance metadata service on a per\-instance basis, and we place limits on the number of simultaneous connections from an instance to the instance metadata service\. 

If you're using the instance metadata service to retrieve AWS security credentials, avoid querying for credentials during every transaction or concurrently from a high number of threads or processes, as this may lead to throttling\. Instead, we recommend that you cache the credentials until they start approaching their expiry time\. 

If you're throttled while accessing the instance metadata service, retry your query with an exponential backoff strategy\.

## Working with Instance User Data<a name="instancedata-add-user-data"></a>

When working with instance user data, keep the following in mind:
+ User data is treated as opaque data: what you give is what you get back\. It is up to the instance to be able to interpret it\.
+ User data is limited to 16 KB\. This limit applies to the data in raw form, not base64\-encoded form\.
+ User data must be base64\-encoded\. The Amazon EC2 console can perform the base64 encoding for you or accept base64\-encoded input\.
+ User data must be decoded when you retrieve it\. The data is decoded when you retrieve it using instance metadata and the console\.
+ If you stop an instance, modify its user data, and start the instance, the updated user data is not executed when you start the instance\.

### Specify Instance User Data at Launch<a name="specify-user-data-launch"></a>

You can specify user data when you launch an instance\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) and [Running Commands on Your Linux Instance at Launch](user-data.md)\.

### Modify Instance User Data<a name="modify-user-data-run"></a>

You can modify user data for an instance in the stopped state if the root volume is an EBS volume\. For more information, see [View and Update the Instance User Data](user-data.md#user-data-view-change)\.

### Retrieve Instance User Data<a name="instancedata-user-data-retrieval"></a>

To retrieve user data from within a running instance, use the following URI:

```
http://169.254.169.254/latest/user-data
```

A request for user data returns the data as it is \(content type `application/octet-stream`\)\. 

This example returns user data that was provided as comma\-separated text:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/user-data
1234,john,reboot,true | 4512,richard, | 173,,,
```

This example returns user data that was provided as a script:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/user-data
#!/bin/bash
yum update -y
service httpd start
chkconfig httpd on
```

To retrieve user data for an instance from your own computer, see [User Data and the AWS CLI](user-data.md#user-data-api-cli)

## Retrieving Dynamic Data<a name="instancedata-dynamic-data-retrieval"></a>

To retrieve dynamic data from within a running instance, use the following URI: 

```
http://169.254.169.254/latest/dynamic/
```

This example shows how to retrieve the high\-level instance identity categories:

```
[ec2-user ~]$ curl http://169.254.169.254/latest/dynamic/instance-identity/
rsa2048
pkcs7
document
signature
dsa2048
```

For more information about dynamic data and examples of how to retrieve it, see [Instance Identity Documents](instance-identity-documents.md)\.

## Example: AMI Launch Index Value<a name="AMI-launch-index-examples"></a>

This example demonstrates how you can use both user data and instance metadata to configure your instances\. 

Alice wants to launch four instances of her favorite database AMI, with the first acting as master and the remaining three acting as replicas\. When she launches them, she wants to add user data about the replication strategy for each replicant\. She is aware that this data will be available to all four instances, so she needs to structure the user data in a way that allows each instance to recognize which parts are applicable to it\. She can do this using the `ami-launch-index` instance metadata value, which will be unique for each instance\.

Here is the user data that Alice has constructed:

```
replicate-every=1min | replicate-every=5min | replicate-every=10min
```

The `replicate-every=1min` data defines the first replicant's configuration, `replicate-every=5min` defines the second replicant's configuration, and so on\. Alice decides to provide this data as an ASCII string with a pipe symbol \(`|`\) delimiting the data for the separate instances\.

Alice launches four instances using the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command, specifying the user data: 

```
aws ec2 run-instances --image-id ami-12345678 --count 4 --instance-type t2.micro --user-data "replicate-every=1min | replicate-every=5min | replicate-every=10min"
```

After they're launched, all instances have a copy of the user data and the common metadata shown here:
+ AMI id: ami\-12345678
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

   ```
   [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/ami-launch-index
   2
   ```

1. She saves the `ami-launch-index` as a variable:

   ```
   [ec2-user ~]$ ami_launch_index=`curl http://169.254.169.254/latest/meta-data/ami-launch-index`
   ```

1. She saves the user data as a variable:

   ```
   [ec2-user ~]$ user_data=`curl http://169.254.169.254/latest/user-data/`
   ```

1. Finally, Alice uses the cut command to extract the portion of the user data that is applicable to that instance:

   ```
   [ec2-user ~]$ echo $user_data | cut -d"|" -f"$ami_launch_index"
   replicate-every=5min
   ```

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
|  block\-device\-mapping/ebsN  | The virtual devices associated with Amazon EBS volumes, if any are present\. Amazon EBS volumes are only available in metadata if they were present at launch time or when the instance was last started\. The N indicates the index of the Amazon EBS volume \(such as ebs1 or ebs2\)\. | 2007\-12\-15 | 
|  block\-device\-mapping/ephemeralN  | The virtual devices associated with ephemeral devices, if any are present\. The N indicates the index of the ephemeral volume\. | 2007\-12\-15 | 
|  block\-device\-mapping/root  | The virtual devices or partitions associated with the root devices, or partitions on the virtual device, where the root \(/ or C:\) file system is associated with the given instance\. | 2007\-12\-15 | 
|  block\-device\-mapping/swap  | The virtual devices associated with swap\. Not always present\. | 2007\-12\-15 | 
| elastic\-gpus/associations/elastic\-gpu\-id | If there is an Elastic GPU attached to the instance, contains a JSON string with information about the Elastic GPU, including its ID and connection information\. | 2016\-11\-30 | 
| hostname | The private IPv4 DNS hostname of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 1\.0 | 
|  iam/info  | If there is an IAM role associated with the instance, contains information about the last time the instance profile was updated, including the instance's LastUpdated date, InstanceProfileArn, and InstanceProfileId\. Otherwise, not present\. | 2012\-01\-12 | 
|  iam/security\-credentials/role\-name  | If there is an IAM role associated with the instance, role\-name is the name of the role, and role\-name contains the temporary security credentials associated with the role \(for more information, see [Retrieving Security Credentials from Instance Metadata](iam-roles-for-amazon-ec2.md#instance-metadata-security-credentials)\)\. Otherwise, not present\. | 2012\-01\-12 | 
|  instance\-action  | Notifies the instance that it should reboot in preparation for bundling\. Valid values: none \| shutdown \| bundle\-pending\. | 2008\-09\-01 | 
|  instance\-id  | The ID of this instance\. | 1\.0 | 
|  instance\-type  | The type of instance\. For more information, see [Instance Types](instance-types.md)\. | 2007\-08\-29 | 
|  kernel\-id  | The ID of the kernel launched with this instance, if applicable\. | 2008\-02\-01 | 
|  local\-hostname  | The private IPv4 DNS hostname of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 2007\-01\-19 | 
|  local\-ipv4  | The private IPv4 address of the instance\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 1\.0 | 
|  mac  | The instance's media access control \(MAC\) address\. In cases where multiple network interfaces are present, this refers to the eth0 device \(the device for which the device number is 0\)\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/device\-number  | The unique device number associated with that interface\. The device number corresponds to the device name; for example, a device\-number of 2 is for the eth2 device\. This category corresponds to the DeviceIndex and device\-index fields that are used by the Amazon EC2 API and the EC2 commands for the AWS CLI\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/interface\-id  | The ID of the network interface\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/ipv4\-associations/public\-ip  | The private IPv4 addresses that are associated with each public IP address and assigned to that interface\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/ipv6s | The IPv6 addresses associated with the interface\. Returned only for instances launched into a VPC\.  | 2016\-06\-30  | 
|  network/interfaces/macs/mac/local\-hostname  | The interface's local hostname\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/local\-ipv4s  | The private IPv4 addresses associated with the interface\.  | 2011\-01\-01 | 
|  network/interfaces/macs/mac/mac  | The instance's MAC address\. | 2011\-01\-01 | 
| network/interfaces/macs/mac/owner\-id  | The ID of the owner of the network interface\. In multiple\-interface environments, an interface can be attached by a third party, such as Elastic Load Balancing\. Traffic on an interface is always billed to the interface owner\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/public\-hostname  | The interface's public DNS \(IPv4\)\. This category is only returned if the enableDnsHostnames attribute is set to true\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html)\. | 2011\-01\-01 | 
|  network/interfaces/macs/mac/public\-ipv4s  | The Elastic IP addresses associated with the interface\. There may be multiple IPv4 addresses on an instance\.  | 2011\-01\-01 | 
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
|  product\-codes  | Marketplace product codes associated with the instance, if any\.  | 2007\-03\-01 | 
|  public\-hostname  | The instance's public DNS\. This category is only returned if the enableDnsHostnames attribute is set to true\. For more information, see [Using DNS with Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-dns.html) in the Amazon VPC User Guide\. | 2007\-01\-19 | 
|  public\-ipv4  | The public IPv4 address\. If an Elastic IP address is associated with the instance, the value returned is the Elastic IP address\. | 2007\-01\-19 | 
|  public\-keys/0/openssh\-key  | Public key\. Only available if supplied at instance launch time\. | 1\.0 | 
|  ramdisk\-id  | The ID of the RAM disk specified at launch time, if applicable\. | 2007\-10\-10 | 
|  reservation\-id  | The ID of the reservation\. | 1\.0 | 
|  security\-groups  |  The names of the security groups applied to the instance\. After launch, you can change the security groups of the instances\. Such changes are reflected here and in network/interfaces/macs/**mac**/security\-groups\.  | 1\.0 | 
|  services/domain  |  The domain for AWS resources for the region\.  | 2014\-02\-25 | 
|  services/partition  |  The partition that the resource is in\. For standard AWS regions, the partition is `aws`\. If you have resources in other partitions, the partition is `aws-partitionname`\. For example, the partition for resources in the China \(Beijing\) region is `aws-cn`\.  | 2015\-10\-20 | 
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
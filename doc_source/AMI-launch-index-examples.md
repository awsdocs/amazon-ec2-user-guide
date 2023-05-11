# Example: AMI launch index value<a name="AMI-launch-index-examples"></a>

This example demonstrates how you can use both user data and instance metadata to configure your instances\. 

**Note**  
The examples in this section use the IPv4 address of the IMDS: `169.254.169.254`\. If you are retrieving instance metadata for EC2 instances over the IPv6 address, ensure that you enable and use the IPv6 address instead: `fd00:ec2::254`\. The IPv6 address of the IMDS is compatible with IMDSv2 commands\. The IPv6 address is only accessible on [Instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.

Alice wants to launch four instances of her favorite database AMI, with the first acting as the original instance and the remaining three acting as replicas\. When she launches them, she wants to add user data about the replication strategy for each replica\. She is aware that this data will be available to all four instances, so she needs to structure the user data in a way that allows each instance to recognize which parts are applicable to it\. She can do this using the `ami-launch-index` instance metadata value, which will be unique for each instance\. If she starts more than one instance at the same time, the `ami-launch-index` indicates the order in which the instances were launched\. The value of the first instance launched is `0`\.

Here is the user data that Alice has constructed\.

```
replicate-every=1min | replicate-every=5min | replicate-every=10min
```

The `replicate-every=1min` data defines the first replica's configuration, `replicate-every=5min` defines the second replica's configuration, and so on\. Alice decides to provide this data as an ASCII string with a pipe symbol \(`|`\) delimiting the data for the separate instances\.

Alice launches four instances using the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command, specifying the user data\. 

```
aws ec2 run-instances \
    --image-id ami-0abcdef1234567890 \
    --count 4 \
    --instance-type t2.micro \
    --user-data "replicate-every=1min | replicate-every=5min | replicate-every=10min"
```

After they're launched, all instances have a copy of the user data and the common metadata shown here:
+ AMI ID: ami\-0abcdef1234567890
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

1. She connects to one of the instances, and retrieves the `ami-launch-index` for that instance to ensure it is one of the replicas:

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
   [ec2-user ~]$ echo $user_data | cut -d"|" -f"$ami_launch_index"
   replicate-every=5min
   ```

------
#### [ IMDSv1 ]

   ```
   [ec2-user ~]$ echo $user_data | cut -d"|" -f"$ami_launch_index"
   replicate-every=5min
   ```

------
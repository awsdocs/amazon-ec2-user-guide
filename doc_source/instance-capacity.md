# Troubleshooting Instance Capacity<a name="instance-capacity"></a>

The following errors are related to instance capacity\.

## Error: InsufficientInstanceCapacity<a name="instance-capacity-insufficient"></a>

If you get an `InsufficientInstanceCapacity` error when you try to launch an instance or start a stopped instance, AWS does not currently have enough available capacity to service your request\. Try the following:
+ Wait a few minutes and then submit your request again; capacity can shift frequently\.
+ Submit a new request with a reduced number of instances\. For example, if you're making a single request to launch 15 instances, try making 3 requests for 5 instances, or 15 requests for 1 instance instead\.
+ If you're launching an instance, submit a new request without specifying an Availability Zone\.
+ If you're launching an instance, submit a new request using a different instance type \(which you can resize at a later stage\)\. For more information, see [Changing the Instance Type](ec2-instance-resize.md)\.
+ If you are launching instances into a cluster placement group, you can get an insufficient capacity error\. For more information, see [Placement Group Rules and Limitations](placement-groups.md#concepts-placement-groups)\.
+ Try purchasing Reserved Instances\. Reserved Instances are a long\-term capacity reservation\. For more information, see [Amazon EC2 Reserved Instances](https://aws.amazon.com/ec2/purchasing-options/reserved-instances/)\.

The `InsufficientInstanceCapacity` error is returned by Amazon EC2\. For more information about the `InsufficientDBInstanceCapacity` error that's returned by Amazon RDS for DB instances, see [Amazon RDS Insufficient DB Instance Capacity](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Capacity) in the *Amazon Relational Database Service User Guide*\.

## Error: InstanceLimitExceeded<a name="instance-capacity-exceeded"></a>

If you get an `InstanceLimitExceeded` error when you try to launch an instance, you have reached your concurrent running instance limit\. For new AWS accounts, the default limit is 20\. If you need additional running instances, complete the form at [Request to Increase Amazon EC2 Instance Limit](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.
# Troubleshooting instance launch issues<a name="troubleshooting-launch"></a>

The following issues prevent you from launching an instance\.

**Topics**
+ [Instance limit exceeded](#troubleshooting-launch-limit)
+ [Insufficient instance capacity](#troubleshooting-launch-capacity)
+ [The requested configuration is currently not supported\. Please check the documentation for supported configurations\.](#troubleshooting-instance-configuration)
+ [Instance terminates immediately](#troubleshooting-launch-internal)

## Instance limit exceeded<a name="troubleshooting-launch-limit"></a>

### Description<a name="troubleshooting-launch-limit-description"></a>

You get the `InstanceLimitExceeded` error when you try to launch a new instance or restart a stopped instance\.

### Cause<a name="troubleshooting-launch-limit-cause"></a>

If you get an `InstanceLimitExceeded` error when you try to launch a new instance or restart a stopped instance, you have reached the limit on the number of instances that you can launch in a Region\. When you create your AWS account, we set default limits on the number of instances you can run on a per\-Region basis\.

### Solution<a name="troubleshooting-launch-limit-solution"></a>

You can request an instance limit increase on a per\-region basis\. For more information, see [Amazon EC2 service quotas](ec2-resource-limits.md)\.

## Insufficient instance capacity<a name="troubleshooting-launch-capacity"></a>

### Description<a name="troubleshooting-launch-capacity-description"></a>

You get the `InsufficientInstanceCapacity` error when you try to launch a new instance or restart a stopped instance\.

### Cause<a name="troubleshooting-launch-capacity-description"></a>

If you get this error when you try to launch an instance or restart a stopped instance, AWS does not currently have enough available On\-Demand capacity to fulfill your request\.

### Solution<a name="troubleshooting-launch-capacity-description"></a>

To resolve the issue, try the following:
+ Wait a few minutes and then submit your request again; capacity can shift frequently\.
+ Submit a new request with a reduced number of instances\. For example, if you're making a single request to launch 15 instances, try making 3 requests for 5 instances, or 15 requests for 1 instance instead\.
+ If you're launching an instance, submit a new request without specifying an Availability Zone\.
+ If you're launching an instance, submit a new request using a different instance type \(which you can resize at a later stage\)\. For more information, see [Changing the instance type](ec2-instance-resize.md)\.
+ If you are launching instances into a cluster placement group, you can get an insufficient capacity error\. For more information, see [Placement group rules and limitations](placement-groups.md#concepts-placement-groups)\.

## The requested configuration is currently not supported\. Please check the documentation for supported configurations\.<a name="troubleshooting-instance-configuration"></a>

### Description<a name="troubleshooting-instance-configuration-description"></a>

You get the `Unsupported` error when you try to launch a new instance because the instance configuration is not supported\.

### Cause<a name="troubleshooting-instance-configuration-cause"></a>

The error message provides additional details\. For example, an instance type or instance purchasing option might not be supported in the specified Region or Availability Zone\.

### Solution<a name="troubleshooting-instance-configuration-solution"></a>

Try a different instance configuration\. To search for an instance type that meets your requirements, see [Finding an Amazon EC2 instance type](instance-discovery.md)\.

## Instance terminates immediately<a name="troubleshooting-launch-internal"></a>

### Description<a name="troubleshooting-launch-internal-description"></a>

Your instance goes from the `pending` state to the `terminated` state\.

### Cause<a name="troubleshooting-launch-internal-cause"></a>

The following are a few reasons why an instance might immediately terminate:
+ You've exceeded your EBS volume limits\. For more information, see [Instance volume limits](volume_limits.md)\.
+ An EBS snapshot is corrupted\.
+ The root EBS volume is encrypted and you do not have permissions to access the CMK for decryption\.
+ A snapshot specified in the block device mapping for the AMI is encrypted and you do not have permissions to access the CMK for decryption or you do not have access to the CMK to encrypt the restored volumes\.
+ The instance store\-backed AMI that you used to launch the instance is missing a required part \(an image\.part\.*xx* file\)\.

For more information, get the termination reason using one of the following methods\.

**To get the termination reason using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. On the first tab, find the reason next to **State transition reason**\.

**To get the termination reason using the AWS Command Line Interface**

1. Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command and specify the instance ID\.

   ```
   aws ec2 describe-instances --instance-id instance_id
   ```

1. Review the JSON response returned by the command and note the values in the `StateReason` response element\.

   The following code block shows an example of a `StateReason` response element\.

   ```
   "StateReason": {
     "Message": "Client.VolumeLimitExceeded: Volume limit exceeded", 
     "Code": "Server.InternalError"
   },
   ```

**To get the termination reason using AWS CloudTrail**  
For more information, see [Viewing events with CloudTrail event history](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html) in the *AWS CloudTrail User Guide*\.

### Solution<a name="troubleshooting-launch-internal-solution"></a>

Depending on the termination reason, take one of the following actions:
+ **`Client.VolumeLimitExceeded: Volume limit exceeded`** — Delete unused volumes\. You can [submit a request](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ebs) to increase your volume limit\.
+ **`Client.InternalError: Client error on launch`** — Ensure that you have the permissions required to access the CMKs used to decrypt and encrypt volumes\. For more information, see [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.
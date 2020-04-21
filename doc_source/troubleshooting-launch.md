# Troubleshooting instance launch issues<a name="troubleshooting-launch"></a>

The following issues prevent you from launching an instance\.

**Topics**
+ [Instance limit exceeded](#troubleshooting-launch-limit)
+ [Insufficient instance capacity](#troubleshooting-launch-capacity)
+ [Instance terminates immediately](#troubleshooting-launch-internal)

## Instance limit exceeded<a name="troubleshooting-launch-limit"></a>

### Description<a name="troubleshooting-launch-limit-description"></a>

You get the `InstanceLimitExceeded` error when you try to launch a new instance or restart a stopped instance\.

### Cause<a name="troubleshooting-launch-limit-cause"></a>

If you get an `InstanceLimitExceeded` error when you try to launch a new instance or restart a stopped instance, you have reached the limit on the number of instances that you can launch in a Region\. When you create your AWS account, we set default limits on the number of instances you can run on a per\-Region basis\.

### Solution<a name="troubleshooting-launch-limit-solution"></a>

You can request an instance limit increase on a per\-region basis\. For more information, see [Amazon EC2 Service Quotas](ec2-resource-limits.md)\.

## Insufficient instance capacity<a name="troubleshooting-launch-capacity"></a>

### Description<a name="troubleshooting-launch-capacity-description"></a>

You get the `InsufficientInstanceCapacity` error when you try to launch a new instance or restart a stopped instance\.

### Cause<a name="troubleshooting-launch-capacity-description"></a>

If you get an `InsufficientInstanceCapacity` error when you try to launch an instance or restart a stopped instance, AWS does not currently have enough available On\-Demand capacity to service your request\.

### Solution<a name="troubleshooting-launch-capacity-description"></a>

To resolve the issue, try the following:
+ Wait a few minutes and then submit your request again; capacity can shift frequently\.
+ Submit a new request with a reduced number of instances\. For example, if you're making a single request to launch 15 instances, try making 3 requests for 5 instances, or 15 requests for 1 instance instead\.
+ If you're launching an instance, submit a new request without specifying an Availability Zone\.
+ If you're launching an instance, submit a new request using a different instance type \(which you can resize at a later stage\)\. For more information, see [Changing the instance type](ec2-instance-resize.md)\.
+ If you are launching instances into a cluster placement group, you can get an insufficient capacity error\. For more information, see [Placement Group Rules and Limitations](placement-groups.md#concepts-placement-groups)\.
+ Try creating an On\-Demand Capacity Reservation, which enables you to reserve Amazon EC2 capacity for any duration\. For more information, see [On\-Demand Capacity Reservations](ec2-capacity-reservations.md)\.
+ Try purchasing Reserved Instances, which are a long\-term capacity reservation\. For more information, see [Amazon EC2 Reserved Instances](https://aws.amazon.com/ec2/purchasing-options/reserved-instances/)\.

## Instance terminates immediately<a name="troubleshooting-launch-internal"></a>

### Description<a name="troubleshooting-launch-internal-description"></a>

Your instance goes from the `pending` state to the `terminated` state immediately after restarting it\.

### Cause<a name="troubleshooting-launch-internal-cause"></a>

The following are a few reasons why an instance might immediately terminate:
+ You've reached your EBS volume limit\.
+ An EBS snapshot is corrupt\.
+ The root EBS volume is encrypted and you do not have permissions to access the KMS key for decryption\.
+ The instance store\-backed AMI that you used to launch the instance is missing a required part \(an image\.part\.*xx* file\)\.

### Solution<a name="troubleshooting-launch-internal-solution"></a>

You can use the Amazon EC2 console or AWS Command Line Interface to get the termination reason\.

**To get the termination reason using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select the instance\.

1. In the **Description** tab, note the reason next to the **State transition reason** label\.

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

**To address the issue**

Take one of the following actions depending on the termination reason you noted:
+ If the reason is **`Client.VolumeLimitExceeded: Volume limit exceeded`**, you have reached your EBS volume limit\. For more information, see [Instance volume limits](volume_limits.md)\. To submit a request to increase your Amazon EBS volume limit, complete the AWS Support Center [Create Case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ebs) form\. For more information, see [Amazon EC2 Service Quotas](ec2-resource-limits.md)\.
+ If the reason is **`Client.InternalError: Client error on launch`**, that typically indicates that the root volume is encrypted and that you do not have permissions to access the KMS key for decryption\. To get permissions to access the required KMS key, add the appropriate KMS permissions to your IAM user\. For more information, see [Using Key Policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\.
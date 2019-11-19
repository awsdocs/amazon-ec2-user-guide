# Recover Your Instance<a name="ec2-instance-recover"></a>

You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance and automatically recovers the instance if it becomes impaired due to an underlying hardware failure or a problem that requires AWS involvement to repair\. Terminated instances cannot be recovered\. A recovered instance is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata\. If the impaired instance is in a placement group, the recovered instance runs in the placement group\. For more information about using Amazon CloudWatch alarms to recover an instance, see [Create Alarms That Stop, Terminate, Reboot, or Recover an Instance](UsingAlarmActions.md)\. To troubleshoot issues with instance recovery failures, see [Troubleshooting Instance Recovery Failures](#TroubleshootingInstanceRecovery)\.

When the `StatusCheckFailed_System` alarm is triggered, and the recover action is initiated, you will be notified by the Amazon SNS topic that you selected when you created the alarm and associated the recover action\. During instance recovery, the instance is migrated during an instance reboot, and any data that is in\-memory is lost\. When the process is complete, information is published to the SNS topic you've configured for the alarm\. Anyone who is subscribed to this SNS topic will receive an email notification that includes the status of the recovery attempt and any further instructions\. You will notice an instance reboot on the recovered instance\.

Examples of problems that cause system status checks to fail include:
+ Loss of network connectivity
+ Loss of system power
+ Software issues on the physical host
+ Hardware issues on the physical host that impact network reachability

If your instance has a public IPv4 address, it retains the public IPv4 address after recovery\.

## Requirements<a name="instance-recovery-requirements"></a>

The recover action is supported only on instances with the following characteristics:
+ Uses one of the following instance types: A1, C3, C4, C5, C5n, M3, M4, M5, M5a, M5n, P3, R3, R4, R5, R5a, R5n, T2, T3, T3a, X1, or X1e
+ Runs in a virtual private cloud \(VPC\)
+ Uses `default` or `dedicated` instance tenancy
+ Has only EBS volumes \(do not configure instance store volumes\)

## Troubleshooting Instance Recovery Failures<a name="TroubleshootingInstanceRecovery"></a>

The following issues can cause automatic recovery of your instance to fail:
+ Temporary, insufficient capacity of replacement hardware\.
+ The instance has an attached instance store storage, which is an unsupported configuration for automatic instance recovery\.
+ There is an ongoing Service Health Dashboard event that prevented the recovery process from successfully executing\. Refer to [http://status\.aws\.amazon\.com/](http://status.aws.amazon.com/) for the latest service availability information\.
+ The instance has reached the maximum daily allowance of three recovery attempts\.

The automatic recovery process attempts to recover your instance for up to three separate failures per day\. If the instance system status check failure persists, we recommend that you manually stop and start the instance\. For more information, see [Stop and Start Your Instance](Stop_Start.md)\.

Your instance may subsequently be retired if automatic recovery fails and a hardware degradation is determined to be the root cause for the original system status check failure\.
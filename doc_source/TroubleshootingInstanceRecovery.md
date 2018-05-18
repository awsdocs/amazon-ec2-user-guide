# Troubleshooting Instance Recovery Failures<a name="TroubleshootingInstanceRecovery"></a>

The following issues can cause automatic recovery of your instance to fail:
+ Temporary, insufficient capacity of replacement hardware\.
+ The instance has an attached instance store storage, which is an unsupported configuration for automatic instance recovery\.
+ There is an ongoing Service Health Dashboard event that prevented the recovery process from successfully executing\. Refer to [http://status\.aws\.amazon\.com/](http://status.aws.amazon.com/) for the latest service availability information\.
+ The instance has reached the maximum daily allowance of three recovery attempts\.

The automatic recovery process attempts to recover your instance for up to three separate failures per day\. If the instance system status check failure persists, we recommend that you manually start and stop the instance\. For more information, see [Stop and Start Your Instance](Stop_Start.md)\.

Your instance may subsequently be retired if automatic recovery fails and a hardware degradation is determined to be the root cause for the original system status check failure\.
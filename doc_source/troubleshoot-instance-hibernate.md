# Troubleshoot hibernation<a name="troubleshoot-instance-hibernate"></a>

Use this information to help diagnose and fix issues that you might encounter when hibernating an instance\.

## Can't hibernate immediately after launch<a name="hibernate-troubleshooting-1"></a>

If you try to hibernate an instance too quickly after you've launched it, you get an error\.

You must wait for about two minutes after launch before hibernating\.

## Takes too long to transition from `stopping` to `stopped`, and memory state not restored after start<a name="hibernate-troubleshooting-2"></a>

If it takes a long time for your hibernating instance to transition from the `stopping` state to `stopped`, and if the memory state is not restored after you start, this could indicate that hibernation was not properly configured\.

Check the instance system log and look for messages that are related to hibernation\. To access the system log, [connect](AccessingInstances.md) to the instance or use the [get\-console\-output](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-console-output.html) command\. Find the log lines from the `hibinit-agent`\. If the log lines indicate a failure or the log lines are missing, there was most likely a failure configuring hibernation at launch\.

For example, the following message indicates that the instance root volume is not large enough: `hibinit-agent: Insufficient disk space. Cannot create setup for hibernation. Please allocate a larger root device.`

If the last log line from the `hibinit-agent` is `hibinit-agent: Running: swapoff /swap`, hibernation was successfully configured\.











If you do not see any logs from these processes, your AMI might not support hibernation\. For information about supported AMIs, see [Hibernation prerequisites](hibernating-prerequisites.md)\. If you used your own AMI, make sure that you followed the instructions for [Configure an existing AMI to support hibernation](hibernation-enabled-AMI.md)\.

## Instance "stuck" in the stopping state<a name="hibernate-troubleshooting-3"></a>

If you hibernated your instance and it appears "stuck" in the `stopping` state, you can forcibly stop it\. For more information, see [Troubleshoot stopping your instance](TroubleshootingInstancesStopping.md)\.
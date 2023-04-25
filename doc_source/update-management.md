# Update management in Amazon EC2<a name="update-management"></a>

We recommend that you regularly patch, update, and secure the operating system and applications on your EC2 instances\. You can use [AWS Systems Manager Patch Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-patch.html) to automate the process of installing security\-related updates for both the operating system and applications\. 

For EC2 instances in an Auto Scaling group, you can use the [https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/automation-aws-patchasginstance.html](https://docs.aws.amazon.com/systems-manager-automation-runbooks/latest/userguide/automation-aws-patchasginstance.html) runbook to help avoid instances that are undergoing patching from being replaced\. Alternatively, you can use any automatic update services or recommended processes for installing updates that are provided by the application vendor\.

For a list of the latest Amazon EC2 AMIs running Windows Server, see [Details About AWS Windows AMI Versions](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/windows-ami-version-history.html#windows-ami-versions)\.
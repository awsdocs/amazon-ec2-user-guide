# Use EC2Rescue for Linux<a name="Linux-Server-EC2Rescue"></a>

EC2Rescue for Linux is an easy\-to\-use, open\-source tool that can be run on an Amazon EC2 Linux instance to diagnose and troubleshoot common issues using its library of over 100 modules\. A few generalized use cases for EC2Rescue for Linux include gathering syslog and package manager logs, collecting resource utilization data, and diagnosing/remediating known problematic kernel parameters and common OpenSSH issues\.

The `AWSSupport-TroubleshootSSH` runbook installs EC2Rescue for Linux and then uses the tool to check or attempt to fix common issues that prevent a remote connection to a Linux machine via SSH\. For more information, and to run this automation, see [AWS Support\-TroubleshootSSH](https://docs.aws.amazon.com/systems-manager/latest/userguide/automation-awssupport-troubleshootssh.html)\.

If you are using a Windows instance, see [EC2Rescue for Windows Server](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/Windows-Server-EC2Rescue.html)\.

**Contents**
+ [Install EC2Rescue for Linux](ec2rl_install.md)
+ [Work with EC2Rescue for Linux](ec2rl_working.md)
+ [Develop EC2Rescue modules](ec2rl_moduledev.md)
# Connect to Your Linux Instance<a name="AccessingInstances"></a>

Connect to the Linux instances that you launched and transfer files between your local computer and your instance\.

To connect to a Windows instance, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Connection Method<a name="connection-method"></a>

Your local computer's operating system determines the type of method you use to connect to your Linux instance\.


| Your local computer | Available connection methods | 
| --- | --- | 
|  Linux or macOS X  |  [SSH client](AccessingInstancesLinux.md) [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md) [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)  | 
|  Windows  |  [PuTTY](putty.md) [Windows Subsystem for Linux](WSL.md) [SSH client](AccessingInstancesLinux.md) [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)  | 

After you connect to your Linux instance, you can try one of our tutorials, such as [Tutorial: Install a LAMP Web Server with the Amazon Linux AMI](install-LAMP.md) or [Tutorial: Hosting a WordPress Blog with Amazon Linux](hosting-wordpress.md)\.
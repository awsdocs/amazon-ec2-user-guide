# Connect to your Linux instance<a name="AccessingInstances"></a>

Connect to the Linux instances that you launched and transfer files between your local computer and your instance\.

To connect to a Windows instance, see [Connect to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Connection options<a name="connection-method"></a>

The operating system of your local computer determines the options that you have to connect from your local computer to your Linux instance\.

**If your local computer operating system is Linux or macOS X**  
+ [SSH client](AccessingInstancesLinux.md)
+ [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

**If your local computer operating system is Windows**  
+ [OpenSSH](openssh.md)
+ [PuTTY](putty.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
+ [Windows Subsystem for Linux](WSL.md)

**Note**  
If you need to troubleshoot boot, network configuration, and other issues for instances built on the [AWS Nitro System](http://aws.amazon.com/ec2/nitro/), you can use the [EC2 Serial Console for Linux instances](ec2-serial-console.md)\.
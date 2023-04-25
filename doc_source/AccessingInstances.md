# Connect to your Linux instance<a name="AccessingInstances"></a>

This section of the *Amazon EC2 User Guide* provides information about how to connect to a Linux instance after you have launched it, and how to transfer files between your local computer and your instance\.

For information about how to connect to a Windows instance, see [Connect to your Windows instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

To troubleshoot connecting to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\.

## Set up to connect to your Linux instance<a name="setup-connect"></a>

For general steps to prepare to connect to your Linux instance, see [Set up to connect to your instance](connection-prereqs.md)\.

## Connection options<a name="connection-method"></a>

In addition to the general steps to prepare to connect to an instance, the operating system of your local computer determines the specific prerequisites for connecting from your local computer to your Linux instance\.

**Linux or macOS X**  
If your local computer operating system is Linux or macOS X, see the specific prerequisites for the following options to connect to your instance:
+ [SSH client](AccessingInstancesLinux.md)
+ [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

**Windows**  
If your local computer operating system is Windows, see the specific prerequisites for the following options to connect to your instance:
+ [OpenSSH](openssh.md)
+ [PuTTY](putty.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
+ [Windows Subsystem for Linux](WSL.md)

**Note**  
To troubleshoot boot, network configuration, and other issues for instances built on the [AWS Nitro System](http://aws.amazon.com/ec2/nitro/), you can use the [EC2 Serial Console for Linux instances](ec2-serial-console.md)\.
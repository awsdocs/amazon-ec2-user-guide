# Connect to Your Linux Instance<a name="AccessingInstances"></a>

Connect to the Linux instances that you launched and transfer files between your local computer and your instance\.

To connect to a Windows instance, see [Connecting to Your Windows Instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Connection Options<a name="connection-method"></a>

The operating system of your local computer determines the options that you have to connect from your local computer to your Linux instance\.

**Options for Linux and macOS X**  
+ [SSH client](AccessingInstancesLinux.md)
+ [EC2 Instance Connect](Connect-using-EC2-Instance-Connect.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)

**Options for Windows**  
+ [PuTTY](putty.md)
+ [SSH client](AccessingInstancesLinux.md)
+ [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)
+ [Windows Subsystem for Linux](WSL.md)

The Amazon EC2 console provides an option to connect to your instances directly from your browser using a Java SSH client\. However, it is no longer supported on many browsers\. For more information, see [Cannot connect using my browser](TroubleshootingInstancesConnecting.md#troubleshoot-instance-connect-mindterm)\.
# EC2 Serial Console for Linux instances<a name="ec2-serial-console"></a>

With the EC2 serial console, you have access to your Amazon EC2 instance's serial port, which you can use to troubleshoot boot, network configuration, and other issues\. The serial console does not require your instance to have any networking capabilities\. With the serial console, you can enter commands to an instance as if your keyboard and monitor are directly attached to the instance's serial port\.

Access to the serial console is not available by default\. Your organization must grant account access to the serial console and configure IAM policies to grant your users access to the serial console\. Serial console access can be controlled at a granular level by using instance IDs, resource tags, and other IAM levers\. For more information, see [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)\.

The serial console can be accessed by using the EC2 console or the AWS CLI\.

The serial console is available at no additional cost\.

If you are using a Windows instance, see [EC2 Serial Console for Windows instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ec2-serial-console.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Topics**
+ [Configure access to the EC2 Serial Console](configure-access-to-serial-console.md)
+ [Connect to the EC2 Serial Console](connect-to-serial-console.md)
+ [Terminate an EC2 Serial Console session](terminate-serial-console-session.title.md)
+ [Troubleshoot your Linux instance using the EC2 Serial Console](troubleshoot-using-serial-console.md)
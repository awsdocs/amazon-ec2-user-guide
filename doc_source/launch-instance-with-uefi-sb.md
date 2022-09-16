# Launch a Linux instance with UEFI Secure Boot support<a name="launch-instance-with-uefi-sb"></a>

When you [launch an instance](LaunchingAndUsingInstances.md) with the following prerequisites, the instance will automatically validate UEFI boot binaries against its UEFI Secure Boot database\. You can also configure UEFI Secure Boot on an instance after launch\.

**Note**  
UEFI Secure Boot protects your instance and its operating system against boot flow modifications\. Typically, UEFI Secure Boot is configured as part of the AMI\. If you create a new AMI with different parameters from the base AMI, such as changing the `UefiData` within the AMI, you can disable UEFI Secure Boot\.Prerequisites for Linux instances

**AMI**  
Requires an AMI with UEFI Secure Boot enabled\.  
Currently, there are no UEFI Secure Boot\-enabled Amazon Linux AMIs\. To use a supported AMI, you must perform a number of configuration steps on your own Linux AMI\. For more information, see [Create a Linux AMI to support UEFI Secure Boot](create-ami-with-uefi-secure-boot.md)\.

**Instance type**  
+ Supported: All virtualized instance types that support UEFI also support UEFI Secure Boot\. For the instance types that support UEFI Secure Boot, see [Considerations](launch-instance-boot-mode.md#boot-considerations)\.
+ Not supported: Bare metal instance types do not support UEFI Secure Boot\.

For the prerequisites for Windows instances, see [Launch an instance with UEFI Secure Boot support](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/launch-instance-with-uefi-sb) in the *Amazon EC2 User Guide for Windows Instances***\.
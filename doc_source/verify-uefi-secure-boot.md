# Verify whether a Linux instance is enabled for UEFI Secure Boot<a name="verify-uefi-secure-boot"></a>

You can use the `mokutil` utility to verify whether a Linux instance is enabled for UEFI Secure Boot\. If `mokutil` is not installed on your instance, you must install it\. For the installation instructions for Amazon Linux, see [Install software packages on an Amazon Linux instance](install-software.md)\. For other distributions, see their specific documentation\.

**To verify whether a Linux instance is enabled for UEFI Secure Boot**  
Run the following command as `root` on the instance\.

```
mokutil --sb-state 
```

Expected output:
+ If UEFI Secure Boot is enabled, the output contains `SecureBoot enabled`\.
+ If UEFI Secure Boot is not enabled, the output contains `SecureBoot disabled` or `Failed to read SecureBoot`\.

To verify whether a Windows instance is enabled, see [Verify whether a Windows instance is supported for UEFI Secure Boot](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/verify-uefi-secure-boot) in the *Amazon EC2 User Guide for Windows Instances***\.
# Verify whether a Linux instance is enabled for UEFI Secure Boot<a name="verify-uefi-secure-boot"></a>

**To verify whether a Linux instance is enabled for UEFI Secure Boot**  
Run the following command as `root` on the instance\.

```
mokutil --sb-state 
```

If UEFI Secure Boot is enabled, the output contains `SecureBoot enabled`\.

If UEFI Secure Boot is not enabled, the output contains `Failed to read SecureBoot`\.

To verify whether a Windows instance is enabled, see [Verify whether a Windows instance is supported for UEFI Secure Boot](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/verify-uefi-secure-boot) in the *Amazon EC2 User Guide for Windows Instances***\.
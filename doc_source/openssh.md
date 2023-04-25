# Connect to your Linux instance from Windows using OpenSSH<a name="openssh"></a>

The following procedures show you how to connect to your Linux instance from Windows using OpenSSH, an open source connectivity tool for remote login with the SSH protocol\. OpenSSH is supported on Windows Server 2019 and later operating systems\.

**Contents**
+ [Prerequisites](#openssh-prereqs)
+ [Install OpenSSH for Windows using PowerShell](#openssh-install)
+ [Connect to your Linux instance from Windows using OpenSSH](#openssh-connect)
+ [Uninstall OpenSSH from Windows using PowerShell](#openssh-uninstall)

## Prerequisites<a name="openssh-prereqs"></a>

Before you connect to your Linux instance from Windows using OpenSSH, complete the following prerequisites\.

**Verify that the instance is ready**  
After you launch an instance, it can take a few minutes for the instance to be ready so that you can connect to it\. Check that your instance has passed its status checks\. You can view this information in the **Status check** column on the **Instances** page\.

**Verify the general prerequisites for connecting to your instance**  
To find the public DNS name or IP address of your instance and the user name that you should use to connect to your instance, see [Set up to connect to your instance](connection-prereqs.md)\.

**Verify your Windows version**  
To connect to your Linux instance from Windows using OpenSSH, the Windows version must be Windows Server 2019 and later\.

**Verify PowerShell prerequisites**  
To install OpenSSH on your Windows OS using PowerShell, you must be running PowerShell version 5\.1 or later, and your account must be a member of the built\-in Administrators group\. Run `$PSVersionTable.PSVersion` from PowerShell to check your PowerShell version\.  
To check whether you are a member of the built\-in Administrators group, run the following PowerShell command:  

```
(New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
```
If you are a member of the built\-in Administrators group, the output is `True`\.

## Install OpenSSH for Windows using PowerShell<a name="openssh-install"></a>

To install OpenSSH for Windows using PowerShell, run the following PowerShell command:

```
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

Expected output:

```
Path          :
Online        : True
RestartNeeded : False
```

## Connect to your Linux instance from Windows using OpenSSH<a name="openssh-connect"></a>

After you install OpenSSH, use the following procedure to connect to your Linux instance from Windows using OpenSSH\. If you receive an error while attempting to connect to your instance, see [Troubleshoot connecting to your instance](TroubleshootingInstancesConnecting.md)\.

**To connect to your instance using OpenSSH**

1. In PowerShell or the Command Prompt, use the ssh command to connect to the instance\. You specify the path and file name of the private key \(`.pem`\), the user name for your instance, and the public DNS name or IPv6 address for your instance\. For more information about how to find the private key, the user name for your instance, and the DNS name or IPv6 address for an instance, see [Locate the private key and set the permissions](connection-prereqs.md#connection-prereqs-private-key) and [Get information about your instance](connection-prereqs.md#connection-prereqs-get-info-about-instance)\. To connect to your instance, use one of the following commands\.
   + \(Public DNS\) To connect using your instance's public DNS name, enter the following command\.

     ```
     ssh -i /path/key-pair-name.pem instance-user-name@instance-public-dns-name
     ```
   + \(IPv6\) Alternatively, if your instance has an IPv6 address, to connect using your instance's IPv6 address, enter the following command\.

     ```
     ssh -i /path/key-pair-name.pem instance-user-name@instance-IPv6-address
     ```

   You see a response like the following:

   ```
   The authenticity of host 'ec2-198-51-100-1.compute-1.amazonaws.com (198-51-100-1)' can't be established.
   ECDSA key fingerprint is l4UB/neBad9tvkgJf1QZWxheQmR59WgrgzEimCG6kZY.
   Are you sure you want to continue connecting (yes/no/[fingerprint])?
   ```

1. \(Optional\) Verify that the fingerprint in the security alert matches the fingerprint that you previously obtained in [\(Optional\) Get the instance fingerprint](connection-prereqs.md#connection-prereqs-fingerprint)\. If these fingerprints don't match, someone might be attempting a man\-in\-the\-middle attack\. If they match, continue to the next step\.

1. Enter **yes**\.

   You see a response like the following:

   ```
   Warning: Permanently added 'ec2-198-51-100-1.compute-1.amazonaws.com' (ECDSA) to the list of known hosts.
   ```

## Uninstall OpenSSH from Windows using PowerShell<a name="openssh-uninstall"></a>

To uninstall OpenSSH from Windows using PowerShell, run the following PowerShell command:

```
Remove-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
```

Expected output:

```
Path          :
Online        : True
RestartNeeded : True
```
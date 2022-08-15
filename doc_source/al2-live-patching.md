# Kernel Live Patching on Amazon Linux 2<a name="al2-live-patching"></a>

Kernel Live Patching for Amazon Linux 2 enables you to apply security vulnerability and critical bug patches to a running Linux kernel, without reboots or disruptions to running applications\. This allows you to benefit from improved service and application availability, while keeping your infrastructure secure and up to date\. 

AWS releases two types of kernel live patches for Amazon Linux 2:
+ **Security updates** – Include updates for Linux common vulnerabilities and exposures \(CVE\)\. These updates are typically rated as *important* or *critical* using the Amazon Linux Security Advisory ratings\. They generally map to a Common Vulnerability Scoring System \(CVSS\) score of 7 and higher\. In some cases, AWS might provide updates before a CVE is assigned\. In these cases, the patches might appear as bug fixes\.
+ **Bug fixes** – Include fixes for critical bugs and stability issues that are not associated with CVEs\.

AWS provides kernel live patches for an Amazon Linux 2 kernel version for up to 3 months after its release\. After the 3\-month period, you must update to a later kernel version to continue to receive kernel live patches\.

Amazon Linux 2 kernel live patches are made available as signed RPM packages in the existing Amazon Linux 2 repositories\. The patches can be installed on individual instances using existing **yum** workflows, or they can be installed on a group of managed instances using AWS Systems Manager\.

Kernel Live Patching on Amazon Linux 2 is provided at no additional cost\.

**Topics**
+ [Supported configurations and prerequisites](#al2-live-patching-prereq)
+ [Work with Kernel Live Patching](#working-with-live-patching)
+ [Limitations](#al2-live-patching-limitations)
+ [Frequently asked questions](#al2-live-patching-faq)

## Supported configurations and prerequisites<a name="al2-live-patching-prereq"></a>

Kernel Live Patching is supported on Amazon EC2 instances and [on\-premises virtual machines](amazon-linux-2-virtual-machine.md) running Amazon Linux 2\.

To use Kernel Live Patching on Amazon Linux 2, you must use:
+ Kernel version `4.14` or `5.10` on the `x86_64` architecture
+ Kernel version `5.10` on the `ARM64` architecture

### <a name="aml_live_patching"></a>

**Policy Requirements**

<a name="aml-live-patching"></a>To download packages from Amazon Linux repositories, Amazon Elastic Compute Cloud needs access to service\-owned Amazon S3 buckets\. If you are using a Amazon Virtual Private Cloud \(VPC\) endpoint for Amazon S3 in your environment, you need to ensure that your VPC endpoint policy allows access to those public buckets\. 

The table describes each of the Amazon S3 buckets that EC2 might need to access for Kernel Live Patching\.


| S3 bucket ARN | Description | 
| --- | --- | 
|  arn:aws:s3:::packages\.region\.amazonaws\.com/\*  |  Amazon S3 bucket containing Amazon Linux AMI packages   | 
|  arn:aws:s3:::repo\.region\.amazonaws\.com/\*  |  Amazon S3 bucket containing Amazon Linux AMI repositories  | 
|  arn:aws:s3:::amazonlinux\.region\.amazonaws\.com/\*  |  Amazon S3 bucket containing Amazon Linux 2 repositories  | 
|  arn:aws:s3:::amazonlinux\-2\-repos\-region/\*  |  Amazon S3 bucket containing Amazon Linux 2 repositories   | 

### <a name="s3_live_patch_permissions"></a>

The following policy illustrates how to restrict access to identities and resources that belong to your organization and provide access to the Amazon S3 buckets required for Kernel Live Patching\. Replace *region*, *principal\-org\-id* and *resource\-org\-id* with your organization’s values\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowRequestsByOrgsIdentitiesToOrgsResources",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalOrgID": "principal-org-id",
          "aws:ResourceOrgID": "resource-org-id"
        }
      }
    },
    {
      "Sid": "AllowAccessToAmazonLinuxAMIRepositories",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": [
        "s3:GetObject"
      ],
      "Resource": [
        "arn:aws:s3:::packages.region.amazonaws.com/*",
        "arn:aws:s3:::repo.region.amazonaws.com/*",
        "arn:aws:s3:::amazonlinux.region.amazonaws.com/*",
        "arn:aws:s3:::amazonlinux-2-repos-region/*"
      ]
    }
  ]
}
```

## Work with Kernel Live Patching<a name="working-with-live-patching"></a>

You can enable and use Kernel Live Patching on individual instances using the command line on the instance itself, or you can enable and use Kernel Live Patching on a group of managed instances using AWS Systems Manager\.

The following sections explain how to enable and use Kernel Live Patching on individual instances using the command line\.

For more information about enabling and using Kernel Live Patching on a group of managed instances, see [Use Kernel Live Patching on Amazon Linux 2 instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/kernel-live-patching.html) in the *AWS Systems Manager User Guide*\.

**Topics**
+ [Enable Kernel Live Patching](#al2-live-patching-enable)
+ [View the available kernel live patches](#al2-live-patching-view-available)
+ [Apply kernel live patches](#al2-live-patching-apply)
+ [View the applied kernel live patches](#al2-live-patching-view)
+ [Disable Kernel Live Patching](#al2-live-patching-disable)

### Enable Kernel Live Patching<a name="al2-live-patching-enable"></a>

Kernel Live Patching is disabled by default on Amazon Linux 2\. To use live patching, you must install the **yum** plugin for Kernel Live Patching and enable the live patching functionality\.

**Prerequisites**  
Kernel Live Patching requires `binutils`\. If you do not have `binutils` installed, install it using the following command:

```
$ sudo yum install binutils
```

**To enable Kernel Live Patching**

1. Kernel live patches are available for Amazon Linux 2 with kernel version `5.10` or later\. To check your kernel version, run the following command\.

   ```
   $ sudo yum list kernel
   ```

1. If you already have a supported kernel version, skip this step\. If you do not have a supported kernel version, run the following commands to update the kernel to the latest version and to reboot the instance\.

   ```
   $ sudo yum install -y kernel
   ```

   ```
   $ sudo reboot
   ```

1. Install the **yum** plugin for Kernel Live Patching\.

   ```
   $ sudo yum install -y yum-plugin-kernel-livepatch
   ```

1. Enable the **yum** plugin for Kernel Live Patching\.

   ```
   $ sudo yum kernel-livepatch enable -y
   ```

   This command also installs the latest version of the kernel live patch RPM from the configured repositories\.

1. To confirm that the **yum** plugin for kernel live patching has installed successfully, run the following command\.

   ```
   $ rpm -qa | grep kernel-livepatch
   ```

   When you enable Kernel Live Patching, an empty kernel live patch RPM is automatically applied\. If Kernel Live Patching was successfully enabled, this command returns a list that includes the initial empty kernel live patch RPM\. The following is example output\.

   ```
   yum-plugin-kernel-livepatch-1.0-0.11.amzn2.noarch
   kernel-livepatch-5.10.102-99.473-1.0-0.amzn2.x86_64
   ```

1. Install the **kpatch** package\.

   ```
   $ sudo yum install -y kpatch-runtime
   ```

1. Update the **kpatch** service if it was previously installed\. 

   ```
   $ sudo yum update kpatch-runtime
   ```

1. Start the **kpatch** service\. This service loads all of the kernel live patches upon initialization or at boot\. 

   ```
   $ sudo systemctl enable kpatch.service
   ```

1. Enable the Kernel Live Patching topic in the Amazon Linux 2 Extras Library\. This topic contains the kernel live patches\.

   ```
   $ sudo amazon-linux-extras enable livepatch
   ```

### View the available kernel live patches<a name="al2-live-patching-view-available"></a>

Amazon Linux security alerts are published to the Amazon Linux Security Center\. For more information about the Amazon Linux 2 security alerts, which include alerts for kernel live patches, see the [Amazon Linux Security Center](https://alas.aws.amazon.com/alas2.html)\. Kernel live patches are prefixed with `ALASLIVEPATCH`\. The Amazon Linux Security Center might not list kernel live patches that address bugs\.

You can also discover the available kernel live patches for advisories and CVEs using the command line\.

**To list all available kernel live patches for advisories**  
Use the following command\.

```
$ yum updateinfo list
```

The following shows example output\.

```
Loaded plugins: extras_suggestions, kernel-livepatch, langpacks, priorities, update-motd
ALAS2LIVEPATCH-2020-002 important/Sec. kernel-livepatch-5.10.102-99.473-1.0-3.amzn2.x86_64
ALAS2LIVEPATCH-2020-005 medium/Sec. kernel-livepatch-5.10.102-99.473-1.0-4.amzn2.x86_64
updateinfo list done
```

**To list all available kernel live patches for CVEs**  
Use the following command\.

```
$ yum updateinfo list cves
```

The following shows example output\.

```
Loaded plugins: extras_suggestions, kernel-livepatch, langpacks, priorities, update-motdamzn2-core/2/x86_64 | 2.4 kB 00:00:00 
CVE-2019-15918 important/Sec. kernel-livepatch-5.10.102-99.473-1.0-3.amzn2.x86_64
CVE-2019-20096 important/Sec. kernel-livepatch-5.10.102-99.473-1.0-3.amzn2.x86_64
CVE-2020-8648 medium/Sec. kernel-livepatch-5.10.102-99.473-1.0-4.amzn2.x86_64
updateinfo list done
```

### Apply kernel live patches<a name="al2-live-patching-apply"></a>

You apply kernel live patches using the **yum** package manager in the same way that you would apply regular updates\. The **yum** plugin for Kernel Live Patching manages the kernel live patches that are to be applied and eliminates the need to reboot\.

**Tip**  
We recommend that you update your kernel regularly using Kernel Live Patching to ensure that it remains secure and up to date\.

You can choose to apply a specific kernel live patch, or to apply any available kernel live patches along with your regular security updates\.

**To apply a specific kernel live patch**

1. Get the kernel live patch version using one of the commands described in [View the available kernel live patches](#al2-live-patching-view-available)\.

1. Apply the kernel live patch for your Amazon Linux 2 kernel\.

   ```
   $ sudo yum install kernel-livepatch-kernel_version.x86_64
   ```

   For example, the following command applies a kernel live patch for Amazon Linux 2 kernel version `5.10.102-99.473`\.

   ```
   $ sudo yum install kernel-livepatch-5.10.102-99.473-1.0-4.amzn2.x86_64
   ```

**To apply any available kernel live patches along with your regular security updates**  
Use the following command\.

```
$ sudo yum update --security
```

Omit the `--security` option to include bug fixes\.

**Important**  
The kernel version is not updated after applying kernel live patches\. The version is only updated to the new version after the instance is rebooted\.
An Amazon Linux 2 kernel receives kernel live patches for a period of three months\. After the three month period has lapsed, no new kernel live patches are released for that kernel version\. To continue to receive kernel live patches after the three\-month period, you must reboot the instance to move to the new kernel version, which will then continue receiving kernel live patches for the next three months\. To check the support window for your kernel version, run `yum kernel-livepatch supported`\.

### View the applied kernel live patches<a name="al2-live-patching-view"></a>

**To view the applied kernel live patches**  
Use the following command\.

```
$ kpatch list
```

The command returns a list of the loaded and installed security update kernel live patches\. The following is example output\.

```
Loaded patch modules:
livepatch_cifs_lease_buffer_len [enabled]
livepatch_CVE_2019_20096 [enabled]
livepatch_CVE_2020_8648 [enabled]
	
Installed patch modules:
livepatch_cifs_lease_buffer_len (5.10.102-99.473.amzn2.x86_64)
livepatch_CVE_2019_20096 (5.10.102-99.473.amzn2.x86_64)
livepatch_CVE_2020_8648 (5.10.102-99.473.amzn2.x86_64)
```

**Note**  
A single kernel live patch can include and install multiple live patches\.

### Disable Kernel Live Patching<a name="al2-live-patching-disable"></a>

If you no longer need to use Kernel Live Patching, you can disable it at any time\.

**To disable Kernel Live Patching**

1. Remove the RPM packages for the applied kernel live patches\.

   ```
   $ sudo yum kernel-livepatch disable
   ```

1. Uninstall the **yum** plugin for Kernel Live Patching\.

   ```
   $ sudo yum remove yum-plugin-kernel-livepatch
   ```

1. Reboot the instance\.

   ```
   $ sudo reboot
   ```

## Limitations<a name="al2-live-patching-limitations"></a>

Kernel Live Patching has the following limitations:
+ While applying a kernel live patch, you can't perform hibernation, use advanced debugging tools \(such as SystemTap, kprobes, and eBPF\-based tools\), or access ftrace output files used by the Kernel Live Patching infrastructure\.

## Frequently asked questions<a name="al2-live-patching-faq"></a>

For frequently asked questions about Kernel Live Patching for Amazon Linux 2, see the [Amazon Linux 2 Kernel Live Patching FAQ](http://aws.amazon.com/amazon-linux-2/faqs/)\.
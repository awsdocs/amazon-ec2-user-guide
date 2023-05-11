# Requirements<a name="snp-requirements"></a>

To use AMD SEV\-SNP, you must do the following:
+ Use one of the following supported instance types:
  + **M6a:** `m6a.large` \| `m6a.xlarge` \| `m6a.2xlarge` \| `m6a.4xlarge` \| `m6a.8xlarge`
  + **C6a:** `c6a.large` \| `c6a.xlarge` \| `c6a.2xlarge` \| `c6a.4xlarge` \| `c6a.8xlarge` \| `c6a.12xlarge` \| `c6a.16xlarge`
  + **R6a:** `r6a.large` \| `r6a.xlarge` \| `r6a.2xlarge` \| `r6a.4xlarge`
+ Launch your instance in a supported AWS Region\. Currently, only US East \(Ohio\) and Europe \(Ireland\) are supported\.
+ Use an AMI with `uefi` or `uefi-preferred` boot mode and an operating system that supports AMD SEV\-SNP\. For more information about AMD SEV\-SNP support on your operating system, refer to the respective operating system's documentation\. For AWS, AMD SEV\-SNP is supported on Amazon Linux 2023 and Ubuntu 23\.04\.
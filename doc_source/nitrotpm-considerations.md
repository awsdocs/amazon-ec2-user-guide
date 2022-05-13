# Considerations<a name="nitrotpm-considerations"></a>

The following considerations apply when using NitroTPM:
+ BitLocker volumes that are encrypted with NitroTPM\-based keys can only be used on the original instance\.
+ The NitroTPM state is not included in [Amazon EBS snapshots](EBSSnapshots.md)\.
+ The NitroTPM state is not included in [VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/) images\.
+ NitroTPM support is enabled by specifying a value of `v2.0` for the `tpm-support` parameter when creating an AMI\. After you launch an instance with the AMI, you can't modify the attributes on the instance\. Instances with NitroTPM do not support the [ModifyInstanceAttribute](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_ModifyInstanceAttribute.html) API\.
+ You can only create an AMI with NitroTPM configured by using the [RegisterImage](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_RegisterImage.html) API by using the AWS CLI and not with the Amazon EC2 console\.
+ NitroTPM is not supported on Outposts\.
+ NitroTPM is not supported in Local Zones or Wavelength Zones\.
# Attach a volume to multiple instances with Amazon EBS Multi\-Attach<a name="ebs-volumes-multi"></a>

Amazon EBS Multi\-Attach enables you to attach a single Provisioned IOPS SSD \(`io1` or `io2`\) volume to up to 16 Nitro\-based instances that are in the same Availability Zone\. You can attach multiple Multi\-Attach enabled volumes to an instance or set of instances\. Each instance to which the volume is attached has full read and write permission to the shared volume\. Multi\-Attach makes it easier for you to achieve higher application availability in clustered Linux applications that manage concurrent write operations\.

**Topics**
+ [Considerations and limitations](#considerations)
+ [Performance](#perf)
+ [Work with Multi\-Attach](#working)
+ [Monitor a Multi\-Attach enabled volume](#monitoring)
+ [Pricing and billing](#pricing)

## Considerations and limitations<a name="considerations"></a>
+ Multi\-Attach enabled volumes can be attached to up to 16 Linux instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) that are in the same Availability Zone\. You can attach a volume that is Multi\-Attach enabled to Windows instances, but the operating system does not recognize the data on the volume that is shared between the instances, which can result in data inconsistency\.
+ Multi\-Attach is supported exclusively on [Provisioned IOPS SSD volumes](ebs-volume-types.md#EBSVolumeTypes_piops)\.
+ Multi\-Attach is available in the following Regions only:
  + For `io2` volumes—`us-east-2`, `eu-central-1`, and `ap-south-1`\.
  + For `io1` volumes—`us-east-1`, `us-west-2`, `eu-west-1`, and `ap-northeast-2`\.
+ Multi\-Attach enabled volumes can't be attached to R5b instances\.
+ Multi\-Attach enabled volumes do not support I/O fencing\. I/O fencing protocols control write access in a shared storage environment to maintain data consistency\. Your applications must provide write ordering for the attached instances to maintain data consistency\.
+ Multi\-Attach enabled volumes can't be created as boot volumes\.
+ Multi\-Attach enabled volumes can be attached to one block device mapping per instance\.
+ Multi\-Attach can't be enabled during instance launch using either the Amazon EC2 console or RunInstances API\.
+ Multi\-Attach enabled volumes that have an issue at the Amazon EBS infrastructure layer are unavailable to all attached instances\. Issues at the Amazon EC2 or networking layer might impact only some attached instances\.
+ The following table shows volume modification support for Multi\-Attach enabled `io1` and `io2` volumes after creation\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volumes-multi.html)

  \* To change the volume type, first disable Multi\-Attach\.

  \*\* You can't disable Multi\-Attach if the volume is attached to more than one instance\.

## Performance<a name="perf"></a>

Each attached instance is able to drive its maximum IOPS performance up to the volume's maximum provisioned performance\. However, the aggregate performance of all of the attached instances can't exceed the volume's maximum provisioned performance\. If the attached instances' demand for IOPS is higher than the volume's Provisioned IOPS, the volume will not exceed its provisioned performance\.

For example, say you create an `io2` Multi\-Attach enabled volume with `50,000` Provisioned IOPS and you attach it to an `m5.8xlarge` instance and a `c5.12xlarge` instance\. The `m5.8xlarge` and `c5.12xlarge` instances support a maximum of `30,000` and `40,000` IOPS respectively\. Each instance can drive its maximum IOPS as it is less than the volume's Provisioned IOPS of `50,000`\. However, if both instances drive I/O to the volume simultaneously, their combined IOPS can't exceed the volume's provisioned performance of `50,000` IOPS\. The volume will not exceed `50,000` IOPS\.

To achieve consistent performance, it is best practice to balance I/O driven from attached instances across the sectors of a Multi\-Attach enabled volume\.

## Work with Multi\-Attach<a name="working"></a>

Multi\-Attach enabled volumes can be managed in much the same way that you would manage any other Amazon EBS volume\. However, in order to use the Multi\-Attach functionality, you must enable it for the volume\. When you create a new volume, Multi\-Attach is disabled by default\. 

**Contents**
+ [Enable Multi\-Attach](#enable)
+ [Disable Multi\-Attach](#disable)
+ [Attach a volume to instances](#attach)
+ [Delete on termination](#delete)

### Enable Multi\-Attach<a name="enable"></a>

You can enable Multi\-Attach for `io1` and `io2` volumes during creation\.

Use one of the following methods to enable Multi\-Attach for an `io1` or `io2` volume during creation\.

------
#### [ Console ]

**To enable Multi\-Attach during volume creation**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose **Provisioned IOPS SSD \(io1\)** or **Provisioned IOPS SSD \(io2\)**\.

1. For **Size** and **IOPS**, choose the required volume size and the number of IOPS to provision\.

1. For **Availability Zone**, choose the same Availability Zone that the instances are in\.

1. For **Multi\-Attach**, choose **Enable**\.

1. Choose **Create Volume**\.

------
#### [ Command line ]

**To enable Multi\-Attach during volume creation**  
Use the [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command and specify the `--multi-attach-enabled` parameter\.

```
$ aws ec2 create-volume --volume-type io2 --multi-attach-enabled --size 100 --iops 2000 --region us-west-2 --availability-zone us-west-2b
```

------

You can also enable Multi\-Attach for `io2` volumes after they have been created\. 

**Note**  
You can't enable Multi\-Attach for `io1` volumes after creation\.

Use one of the following methods to enable Multi\-Attach for an Amazon EBS volume after it has been created\.

------
#### [ Console ]

**To enable Multi\-Attach after creation**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Select the volume and choose **Actions**, **Modify Volume**\.

1. For **Multi\-Attach**, choose **Enable**\.

1. Choose **Modify**\.

------
#### [ Command line ]

**To enable Multi\-Attach after creation**  
Use the [modify\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-volume.html) command and specify the `--multi-attach-enabled` parameter\.

```
$ aws ec2 modify-volume --volume-id vol-1234567890abcdef0 --multi-attach-enabled
```

------

### Disable Multi\-Attach<a name="disable"></a>

You can disable Multi\-Attach for an `io2` volume only if it is attached to no more than one instance\.

**Note**  
You can't disable Multi\-Attach for `io1` volumes after creation\.

Use one of the following methods to disable Multi\-Attach for an `io2` volume\.

------
#### [ Console ]

**To disable Multi\-Attach**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Select the volume and choose **Actions**, **Modify Volume**\.

1. For **Multi\-Attach**, clear **Enable**\.

1. Choose **Modify**\.

------
#### [ Command line ]

**To disable Multi\-Attach after creation**  
Use the [modify\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-volume.html) command and specify the `–no-multi-attach-enabled` parameter\.

```
$ aws ec2 modify-volume --volume-id vol-1234567890abcdef0 -–no-multi-attach-enabled
```

------

### Attach a volume to instances<a name="attach"></a>

You attach a Multi\-Attach enabled volume to an instance in the same way that you attach any other EBS volume\. For more information, see [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

### Delete on termination<a name="delete"></a>

Multi\-Attach enabled volumes are deleted on instance termination if the last attached instance is terminated and if that instance is configured to delete the volume on termination\. If the volume is attached to multiple instances that have different delete on termination settings in their volume block device mappings, the last attached instance's block device mapping setting determines the delete on termination behavior\.

To ensure predictable delete on termination behavior, enable or disable delete on termination for all of the instances to which the volume is attached\.

By default, when a volume is attached to an instance,  the delete on termination setting for the block device mapping is set to false\. If you want to turn on delete on termination for a Multi\-Attach enabled volume, modify the block device mapping\.

If you want the volume to be deleted when the attached instances are terminated, enable delete on termination in the block device mapping for all of the attached instances\. If you want to retain the volume after the attached instances have been terminated, disable delete on termination in the block device mapping for all of the attached instances\. For more information, see [Preserve Amazon EBS volumes on instance termination](terminating-instances.md#preserving-volumes-on-termination)\.

You can modify an instance's delete on termination setting at launch or after it has launched\. If you enable or disable delete on termination during instance launch, the settings apply only to volumes that are attached at launch\. If you attach a volume to an instance after launch, you must explicitly set the delete on termination behavior for that volume\.

You can modify an instance's delete on termination setting using the command line tools only\.

**To modify the delete on termination setting for an existing instance**  
Use the [ modify\-instance\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-attribute.html) command and specify the `DeleteOnTermination` attribute in the `--block-device-mappings option`\.

```
aws ec2 modify-instance-attribute --instance-id i-1234567890abcdef0 --block-device-mappings file://mapping.json
```

Specify the following in `mapping.json`\.

```
[
  {
    "DeviceName": "/dev/sdf",
    "Ebs": {
      "DeleteOnTermination": true|false
    }
  }
]
```

## Monitor a Multi\-Attach enabled volume<a name="monitoring"></a>

You can monitor a Multi\-Attach enabled volume using the CloudWatch Metrics for Amazon EBS volumes\. For more information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

Data is aggregated across all of the attached instances\. You can't monitor metrics for individual attached instances\.

## Pricing and billing<a name="pricing"></a>

There are no additional charges for using Amazon EBS Multi\-Attach\. You are billed the standard charges that apply to Provisioned IOPS SSD \(`io1` and `io2`\) volumes\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.
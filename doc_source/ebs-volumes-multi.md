# Attaching a volume to multiple instances with Amazon EBS Multi\-Attach<a name="ebs-volumes-multi"></a>

Amazon EBS Multi\-Attach enables you to attach a single Provisioned IOPS SSD \(io1\) volume to up to 16 Nitro\-based instances that are in the same Availability Zone\. You can attach multiple Multi\-Attach enabled volumes to an instance or set of instances\. Each instance to which the volume is attached has full read and write permission to the shared volume\. Multi\-Attach makes it easier for you to achieve higher application availability in clustered Linux applications that manage concurrent write operations\.

Multi\-Attach enabled volumes support many of the features that are supported by regular Amazon EBS volumes, including:
+ [Tagging](Using_Tags.md)
+ [Amazon EBS Snapshots](EBSSnapshots.md)
+ [Automating the Amazon EBS Snapshot Lifecycle](snapshot-lifecycle.md)
+ [Amazon EBS encryption](EBSEncryption.md)
+ [Amazon CloudWatch Metrics for Amazon EBS](using_cloudwatch_ebs.md)
+ [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)

**Topics**
+ [Considerations and limitations](#considerations)
+ [Performance](#perf)
+ [Working with Multi\-Attach](#working)
+ [Monitoring](#monitoring)
+ [Pricing and billing](#pricing)

## Considerations and limitations<a name="considerations"></a>
+ Multi\-Attach enabled volumes do not support I/O fencing\. I/O fencing protocols control write access in a shared storage environment to maintain data consistency\. Your applications must provide write ordering for the attached instances to maintain data consistency\.
+ Multi\-Attach enabled volumes can be attached to up to 16 instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) that are in the same Availability Zone\.
+ Multi\-Attach is supported exclusively on [Provisioned IOPS SSD \(io1\) volumes](ebs-volume-types.md#EBSVolumeTypes_piops)\.
+ Multi\-Attach is available in the `us-east-1`, `us-west-2`, `eu-west-1`, and `ap-northeast-2` Regions\.
+ Multi\-Attach enabled volumes can't be created as boot volumes\.
+ Multi\-Attach enabled volumes can be attached to one block device mapping per instance\.
+ You can't enable or disable Multi\-Attach after volume creation\.
+ You can't change the volume type, size, or Provisioned IOPS of a Multi\-Attach enabled volume\.
+ Multi\-Attach can't be enabled during instance launch using either the Amazon EC2 console or RunInstances API\.
+ Multi\-Attach enabled volumes that have an issue at the Amazon EBS infrastructure layer are unavailable to all attached instances\. Issues at the Amazon EC2 or networking layer might only impact some attached instances\.
+ Multi\-Attach does not support standard file systems\. If you enable multiple servers to simultaneously access a standard file system on EBS Multi\-Attach volumes, data can be corrupted or lost\. File systems, such as XFS, EXT3, EXT4, and NTFS are not designed to be simultaneously accessed by multiple servers or EC2 instances\. Therefore, they do not have built\-in mechanisms to manage the coordination and control of writes, reads, locks, caches, mounts, fencing, and more\. 

## Performance<a name="perf"></a>

Each attached instance is able to drive its maximum IOPS performance up to the volume's maximum provisioned performance\. However, the aggregate performance of all of the attached instances can't exceed the volume's maximum provisioned performance\. If the attached instances' demand for IOPS is higher than the volume's Provisioned IOPS, the volume will not exceed its provisioned performance\.

For example, say you create an `io1` Multi\-Attach enabled volume with `50,000` Provisioned IOPS and you attach it to an `m5.8xlarge` instance and a `c5.12xlarge` instance\. The `m5.8xlarge` and `c5.12xlarge` instances support a maximum of `30,000` and `40,000` IOPS respectively\. Each instance can drive its maximum IOPS as it is less than the volume's Provisioned IOPS of `50,000`\. However, if both instances drive I/O to the volume simultaneously, their combined IOPS can't exceed the volume's provisioned performance of `50,000 IOPS`\. The volume will not exceed `50,000` IOPS\.

To achieve consistent performance, it is best practice to balance I/O driven from attached instances across the sectors of a Multi\-Attach enabled volume\.

## Working with Multi\-Attach<a name="working"></a>

Multi\-Attach enabled volumes can be managed in much the same way that you would manage any other Amazon EBS volume\. However, in order to use the Multi\-Attach functionality, you must enable it for the volume\. When you create a new volume, Multi\-Attach is disabled by default\.

**Contents**
+ [Enabling Multi\-Attach](#enable)
+ [Attaching a volume to instances](#attach)
+ [Deleting on termination](#delete)

### Enabling Multi\-Attach<a name="enable"></a>

You can enable Multi\-Attach for an Amazon EBS volume during creation only\.

Use one of the following methods to enable Multi\-Attach for an Amazon EBS volume during creation\.

------
#### [ Console ]

**To enable Multi\-Attach during volume creation**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Choose **Create Volume**\.

1. For **Volume Type**, choose **Provisioned IOPS SSD \(io1\)**\.

1. For **Size** and **IOPS**, choose the required volume size and the number of IOPS to provision\.

1. For **Availability Zone**, choose the same Availability Zone that the instances are in\.

1. For **Multi\-Attach**, choose **Enable**\.

1. Choose **Create Volume**\.

------
#### [ Command line ]

**To enable Multi\-Attach during volume creation**  
Use the [create\-volume](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-volume.html) command and specify the `--multi-attach-enabled` parameter\.

```
$ aws ec2 create-volume --volume-type io1 --multi-attach-enabled --size 100 --iops 2000 --region us-west-2 --availability-zone us-west-2b
```

------

### Attaching a volume to instances<a name="attach"></a>

You attach a Multi\-Attach enabled volume to an instance in the same way that you attach a regular volume\. For more information, see [Attaching an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

### Deleting on termination<a name="delete"></a>

Multi\-Attach enabled volumes are deleted on instance termination if the last attached instance is terminated and if that instance is configured to delete the volume on termination\. If the volume is attached to multiple instances that have different delete on termination settings in their volume block device mappings, the last attached instance's block device mapping setting determines the delete on termination behavior\.

To ensure predictable delete on termination behavior, enable or disable delete on termination for all of the instances to which the volume is attached\.

By default, when a volume is attached to an instance the delete on termination setting for the block device mapping is set to false\. If you want to turn on delete on termination for a Multi\-Attach enabled volume, modify the block device mapping\.

If you want the volume to be deleted when the attached instances are terminated, enable delete on termination in the block device mapping for all of the attached instances\. If you want to retain the volume after the attached instances have been terminated, disable delete on termination in the block device mapping for all of the attached instances\. For more information, see [Preserving Amazon EBS volumes on instance termination](terminating-instances.md#preserving-volumes-on-termination)\.

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

## Monitoring<a name="monitoring"></a>

You can monitor a Multi\-Attach enabled volume using the CloudWatch Metrics for Amazon EBS volumes\. For more information, see [Amazon CloudWatch Metrics for Amazon EBS](using_cloudwatch_ebs.md)\.

Data is aggregated across all of the attached instances\. You can't monitor metrics for individual attached instances\.

## Pricing and billing<a name="pricing"></a>

There are no additional charges for using Amazon EBS Multi\-Attach\. You are billed the standard charges that apply to Provisioned IOPS SSD \(io1\) volumes\. For more information, see [Amazon EBS pricing](http://aws.amazon.com/ebs/pricing/)\.
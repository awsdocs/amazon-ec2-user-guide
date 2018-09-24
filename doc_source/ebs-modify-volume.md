# Modifying the Size, IOPS, or Type of an EBS Volume on Linux<a name="ebs-modify-volume"></a>

If your current\-generation Amazon EBS volume is attached to a current\-generation EC2 instance type, you can increase its size, change its volume type, or \(for an `io1` volume\) adjust its IOPS performance, all without detaching it\. You can apply these changes to detached volumes as well\. For more information about the current generation instance types, see [Current Generation Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html#current-gen-instances)\.

The following previous\-generation instance types support modification of EBS volumes without detachment: C1, C3, CC2, CR1, G2, I2, M1, M3, R3\. You can safely ignore any warnings that occur with these instance types\. If you are using an unsupported previous\-generation instance type, or if you encounter an error while attempting a volume modification, follow the procedures in [Appendix: Starting and Stopping an Instance to Modify an EBS Volume](stop-start.md)\.

In general, use the following steps when modifying a volume:

1. **Issue the modification command\.** For more information, see [Modifying an EBS Volume from the Console](console-modify.md) and [Modifying an EBS Volume from the Command Line](cli-modify.md)\.

1. **Monitor the progress of the modification\.** For more information, see [Monitoring the Progress of Volume Modifications](monitoring_mods.md)\.

1. **If the size of the volume was modified, extend the volume's file system to take advantage of the increased storage capacity\.** For more information, see [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md) \.

Additionally, you can use [Amazon CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/) and [AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/) to automate the actions associated with volume modification\.

There is no charge to modify the configuration of a volume\. You are charged at the new volume configuration price after a modification starts\. For more information, see the *Amazon Elastic Block Store* section on the [Amazon EBS Pricing](https://aws.amazon.com/ebs/pricing) page\.

**Important**  
Before modifying a volume that contains valuable data, it is a best practice to create a snapshot of the volume in case you need to roll back your changes\. For information about EBS snapshots, see [Creating an Amazon EBS Snapshot](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html)\.

**Topics**
+ [Constraints on Modifying EBS Volume Size](volume_constraints.md)
+ [Modifying an EBS Volume from the Console](console-modify.md)
+ [Modifying an EBS Volume from the Command Line](cli-modify.md)
+ [Monitoring the Progress of Volume Modifications](monitoring_mods.md)
+ [Expanding a Linux Partition](expand-linux-partition.md)
+ [Extending a Linux File System after Resizing the Volume](recognize-expanded-volume-linux.md)
+ [Limitations When Modifying EBS Volumes](limitations.md)
+ [Appendix: Starting and Stopping an Instance to Modify an EBS Volume](stop-start.md)
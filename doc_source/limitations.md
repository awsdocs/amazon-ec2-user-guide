# Limitations When Modifying EBS Volumes<a name="limitations"></a>

Be aware of the following limits and requirements when you modify an EBS volume:
+ In some cases, you must detach the volume or stop the instance for modification to proceed\. If you encounter an error message while attempting to modify an EBS volume, or if you are modifying an EBS volume attached to a previous\-generation instance type, take one of the following steps:
  + For a non\-root volume, detach the volume from the instance, apply the modifications, and then re\-attach the volume\. For more information, see [Detaching an Amazon EBS Volume from an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-detaching-volume.html) and [Attaching an Amazon EBS Volume to an Instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html)\. 
  + For a root \(boot\) volume, stop the instance, apply the modifications, and then restart the instance\. For more information, see [Appendix: Starting and Stopping an Instance to Modify an EBS Volume](stop-start.md)\.
+ Decreasing the size of an EBS volume is not supported\. However, you can create a smaller volume and then migrate your data to it using an application\-level tool such as rsync\.
+ After modifying a volume, wait at least six hours before applying further modifications to the same volume\.
+ While `m3.medium` instances fully support volume modification, some `m3.large`, `m3.xlarge`, and `m3.2xlarge` instances might not support all volume modification features\. If you encounter an error, see [Appendix: Starting and Stopping an Instance to Modify an EBS Volume](stop-start.md)\.

## Volume Modification Support on Older Volumes<a name="initialize-modification-support"></a>

Before you can modify a volume that was attached to an instance before November 1, 2016, you must initialize volume modification support using one of the following actions:
+ Detach and attach the volume
+ Restart the instance

**To determine whether you must initialize volume modification support using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, choose **Instances**\.

1. Choose the **Show/Hide Columns** icon \(the gear\)\. Select the **Launch Time** and **Block Devices** attributes and then choose **Close**\.

1. Sort the list of instances by the **Launch Time** column\. For instances that were started before the cutoff date, check when the devices were attached\. In the following example, you must initialize volume modification for the first instance because it was started before the cutoff date and its root volume was attached before the cutoff date\. The other instances are ready because they were started after the cutoff, regardless of when the volumes were attached\.  
![\[Check the Launch Time and Block Devices columns.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/check-volume-modification-support.png)

**To determine whether you must initialize volume modification support using the CLI**

To find an instance that was last started before the cutoff date with a volume that was attached before the cutoff date, use the following [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

```
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId,LaunchTime<=`2016-11-01`,BlockDeviceMappings[*][Ebs.AttachTime<=`2016-11-01`]]" --output text
```

The output for each instance shows its ID, whether it was started before the cutoff date \(True or False\), and whether its volumes were attached before the cutoff date \(True or False\)\. In the following example output, you must initialize volume modification for the first instance because it was started before the cutoff date and its root volume was attached before the cutoff date\. The other instances are ready because they were started after the cutoff, regardless of when the volumes were attached\.

```
i-e905622e              True
True
i-719f99a8              False
True
i-006b02c1b78381e57     False
False
False
i-e3d172ed              False
True
```
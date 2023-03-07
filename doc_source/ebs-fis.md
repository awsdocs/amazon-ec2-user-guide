# Fault testing on Amazon EBS<a name="ebs-fis"></a>

Use AWS Fault Injection Simulator and the Pause I/O action to temporarily stop I/O between an Amazon EBS volume and the instances to which it is attached to test how your workloads handle I/O interruptions\. With AWS FIS, you can use controlled experiments to test your architecture and monitoring, such as Amazon CloudWatch alarms and OS timeout configurations, and improve resiliency to storage faults\.

For more information about AWS FIS, see the [https://docs.aws.amazon.com/fis/latest/userguide/what-is.html](https://docs.aws.amazon.com/fis/latest/userguide/what-is.html)\.

**Considerations**

Keep in mind the following considerations for pausing volume I/O:
+ You can pause I/O for all Amazon EBS volume types that are attached to [instances built on the Nitro System](instance-types.md#ec2-nitro-instances)\.
+ You can pause I/O for the root volume\.
+ You can pause I/O for Multi\-Attach enabled volumes\. If you pause I/O for a Multi\-Attach enabled volume, I/O is paused between the volume and all of the instances to which it is attached\.
+ To test your OS timeout configuration, set the experiment duration equal to or greater than the value specified for `nvme_core.io_timeout`\. For more information, see [I/O operation timeout](nvme-ebs-volumes.md#timeout-nvme-ebs-volumes)\.
+ If you drive I/O to a volume that has I/O paused, the following happens:
  + The volume's status transitions to `impaired` within 120 seconds\. For more information, see [Monitor the status of your volumes](monitoring-volume-status.md)\.
  + The CloudWatch metrics for queue length \(`VolumeQueueLength`\) will be non\-zero\. Any alarms or monitoring should monitor for a non\-zero queue depth\. For more information see [Volume metrics for volumes attached to all instance types](using_cloudwatch_ebs.md#ebs-volume-metrics)\.
  + The CloudWatch metrics for `VolumeReadOps` or `VolumeWriteOps` will be `0`, which indicates that the volume is no longer processing I/O\.

**Limitations**

Keep in mind the following limitations for pausing volume I/O:
+ Instance store volumes are not supported\.
+ Xen\-based instances types are not supported\.
+ You can't pause I/O for volumes created on an Outpost in AWS Outposts, in an AWS Wavelength Zone, or in a Local Zone\.
+ The Pause I/O action is not available in AWS GovCloud \(US\) Regions\.

You can perform a basic experiment from the Amazon EC2 console, or you can perform more advanced experiments using the AWS FIS console\. For more information about performing advanced experiments using the AWS FIS console, see [ Tutorials for AWS FIS](https://docs.aws.amazon.com/fis/latest/userguide/fis-tutorials.html) in the *AWS Fault Injection Simulator User Guide*\.

**To perform a basic experiment using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes\.**

1. Select the volume for which to pause I/O and choose **Actions**, **Fault injection**, **Pause volume I/O**\.

1. For **Duration**, enter the duration for which to pause I/O between the volume and the instances\. The field next to the Duration dropdown list shows the duration in ISO 8601 format\.

1. In the **Service access** section, select the [IAM service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html#iam-term-service-role) for AWS FIS to assume to perform the experiment\. You can use either the default role, or an existing role that you created\. For more information, see [Create an IAM role for AWS FIS experiments](https://docs.aws.amazon.com/fis/latest/userguide/getting-started-iam-service-role.html)\.

1. Choose **Pause volume I/O**\. When prompted, enter `start` in the confirmation field and choose **Start experiment**\.

1. Monitor the progress and impact of your experiment\. For more information, see [Monitoring AWS FIS](https://docs.aws.amazon.com/fis/latest/userguide/monitoring-experiments.html) in the *AWS FIS User Guide*\.
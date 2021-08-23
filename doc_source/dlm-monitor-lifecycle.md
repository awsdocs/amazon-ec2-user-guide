# Monitor the lifecycle of snapshots and AMIs<a name="dlm-monitor-lifecycle"></a>

You can use the following features to monitor the lifecycle of your snapshots and AMIs\.

**Topics**
+ [Console and AWS CLI](#monitor-console-cli)
+ [AWS CloudTrail](#monitor-lifecycle-cloudtrail)
+ [Monitor your policies using CloudWatch Events](monitor-cloudwatch-events.md)
+ [Monitor your policies using Amazon CloudWatch](monitor-dlm-cw-metrics.md)

## Console and AWS CLI<a name="monitor-console-cli"></a>

You can view your lifecycle policies using the Amazon EC2 console or the AWS CLI\. Each snapshot and AMI created by a policy has a timestamp and policy\-related tags\. You can filter snapshots and AMIs using these tags to verify that your backups are being created as you intend\. For information about viewing lifecycle policies using the console, see [View lifecycle policies](view-modify-delete.md#view)\.

## AWS CloudTrail<a name="monitor-lifecycle-cloudtrail"></a>

With AWS CloudTrail, you can track user activity and API usage to demonstrate compliance with internal policies and regulatory standards\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.
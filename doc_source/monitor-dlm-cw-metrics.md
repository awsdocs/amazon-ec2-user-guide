# Monitor your policies using Amazon CloudWatch<a name="monitor-dlm-cw-metrics"></a>

You can monitor your Amazon Data Lifecycle Manager lifecycle policies using CloudWatch, which collects raw data and processes it into readable, near real\-time metrics\. You can use these metrics to see exactly how many Amazon EBS snapshots and EBS\-backed AMIs are created, deleted, and copied by your policies over time\. You can also set alarms that watch for certain thresholds, and send notifications or take actions when those thresholds are met\.

Metrics are kept for a period of 15 months, so that you can access historical information and gain a better understanding of how your lifecycle policies perform over an extended period\.

For more information about Amazon CloudWatch, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

**Topics**
+ [Supported metrics](#metrics)
+ [View CloudWatch metrics for your policies](#view-metrics)
+ [Graph metrics](#graph-metrics)
+ [Create a CloudWatch alarm for a policy](#create-alarm)
+ [Example use cases](#use-cases)
+ [Managing policies that report failed actions](#manage)

## Supported metrics<a name="metrics"></a>

The `Data Lifecycle Manager` namespace includes the following metrics for Amazon Data Lifecycle Manager lifecycle policies\. The supported metrics differ by policy type\.

All metrics can be measured on the `DLMPolicyId` dimension\. The most useful statistics are `sum` and `average`, and the unit of measure is `count`\.

Choose a tab to view the metrics supported by that policy type\.

------
#### [ EBS snapshot policies ]


| Metric | Description | 
| --- | --- | 
|  `ResourcesTargeted`  |  The number of resources targeted by the tags specified in a snapshot or EBS\-backed AMI policy\.  | 
|  `SnapshotsCreateStarted`  |  The number of snapshot create actions initiated by a snapshot policy\. Each action is recorded only once, even if there are multiple subsequent retries\. If a snapshot create action fails, Amazon Data Lifecycle Manager sends a `SnapshotsCreateFailed` metric\.  | 
|  `SnapshotsCreateCompleted`  |  The number of snapshots created by a snapshot policy\. This includes successful retries within 60 minutes of the scheduled time\.  | 
|  `SnapshotsCreateFailed`  |  The number of snapshots that could not be created by a snapshot policy\. This includes unsuccessful retries within 60 minutes from the scheduled time\.  | 
|  `SnapshotsSharedCompleted`  |  The number of snapshots shared across accounts by a snapshot policy\.  | 
|  `SnapshotsDeleteCompleted`  |  The number of snapshots deleted by a snapshot or EBS\-backed AMI policy\. This metric applies only to snapshots created by the policy\. It does not apply to cross\-Region snapshot copies created by the policy\. This metric includes snapshots that are deleted when an EBS\-backed AMI policy deregisters AMIs\.  | 
|  `SnapshotsDeleteFailed`  |  The number of snapshots that could not be deleted by a snapshot or EBS\-backed AMI policy\. This metric applies only to snapshots created by the policy\. It does not apply to cross\-Region snapshot copies created by the policy\. This metric includes snapshots that are deleted when an EBS\-backed AMI policy deregisters AMIs\.  | 
|  `SnapshotsCopiedRegionStarted`  |  The number of cross\-Region snapshot copy actions initiated by a snapshot policy\.  | 
|  `SnapshotsCopiedRegionCompleted`  |  The number of cross\-Region snapshot copies created by a snapshot policy\. This includes successful retries within 24 hours of the scheduled time\.  | 
|  `SnapshotsCopiedRegionFailed`  |  The number of cross\-Region snapshot copies that could not be created by a snapshot policy\. This includes unsuccessful retries within 24 hours from the scheduled time\.  | 
|  `SnapshotsCopiedRegionDeleteCompleted`  |  The number of cross\-Region snapshot copies deleted, as designated by the retention rule, by a snapshot policy\.  | 
|  `SnapshotsCopiedRegionDeleteFailed`  |  The number of cross\-Region snapshot copies that could not be deleted, as designated by the retention rule, by a snapshot policy\.  | 

------
#### [ EBS\-backed AMI policies ]

The following metrics can be used with EBS\-backed AMI policies:


| Metric | Description | 
| --- | --- | 
|  `ResourcesTargeted`  |  The number of resources targeted by the tags specified in a snapshot or EBS\-backed AMI policy\.  | 
|  `SnapshotsDeleteCompleted`  |  The number of snapshots deleted by a snapshot or EBS\-backed AMI policy\. This metric applies only to snapshots created by the policy\. It does not apply to cross\-Region snapshot copies created by the policy\. This metric includes snapshots that are deleted when an EBS\-backed AMI policy deregisters AMIs\.  | 
|  `SnapshotsDeleteFailed`  |  The number of snapshots that could not be deleted by a snapshot or EBS\-backed AMI policy\. This metric applies only to snapshots created by the policy\. It does not apply to cross\-Region snapshot copies created by the policy\. This metric includes snapshots that are deleted when an EBS\-backed AMI policy deregisters AMIs\.  | 
|  `SnapshotsCopiedRegionDeleteCompleted`  |  The number of cross\-Region snapshot copies deleted, as designated by the retention rule, by a snapshot policy\.  | 
|  `SnapshotsCopiedRegionDeleteFailed`  |  The number of cross\-Region snapshot copies that could not be deleted, as designated by the retention rule, by a snapshot policy\.  | 
|  `ImagesCreateStarted`  |  The number of **CreateImage** actions initiated by an EBS\-backed AMI policy\.  | 
|  `ImagesCreateCompleted`  |  The number of AMIs created by an EBS\-backed AMI policy\.  | 
|  `ImagesCreateFailed`  |  The number of AMIs that could not be created by an EBS\-backed AMI policy\.  | 
|  `ImagesDeregisterCompleted`  |  The number of AMIs deregistered by an EBS\-backed AMI policy\.  | 
|  `ImagesDeregisterFailed`  |  The number of AMIs that could not be deregistered by an EBS\-backed AMI policy\.  | 
|  `ImagesCopiedRegionStarted`  |  The number of cross\-Region copy actions initiated by an EBS\-backed AMI policy\.  | 
|  `ImagesCopiedRegionCompleted`  |  The number of cross\-Region AMI copies created by an EBS\-backed AMI policy\.  | 
|  `ImagesCopiedRegionFailed`  |  The number of cross\-Region AMI copies that could not be created by an EBS\-backed AMI policy\.  | 
|  `ImagesCopiedRegionDeregisterCompleted`  |  The number of cross\-Region AMI copies deregistered, as designated by the retention rule, by an EBS\-backed AMI policy\.  | 
|  `ImagesCopiedRegionDeregisteredFailed`  |  The number of cross\-Region AMI copies that could not be deregistered, as designated by the retention rule, by an EBS\-backed AMI policy\.  | 
|  `EnableImageDeprecationCompleted`  |  The number of AMIs that were marked for deprecation by an EBS\-backed AMI policy\.  | 
|  `EnableImageDeprecationFailed`  |  The number of AMIs that could not be marked for deprecation by an EBS\-backed AMI policy\.  | 
|  `EnableCopiedImageDeprecationCompleted`  |  The number of cross\-Region AMI copies that were marked for deprecation by an EBS\-backed AMI policy\.  | 
|  `EnableCopiedImageDeprecationFailed`  |  The number of cross\-Region AMI copies that could not be marked for deprecation by an EBS\-backed AMI policy\.  | 

------
#### [ Cross\-account copy event policies ]

The following metrics can be used with cross\-account copy event policies:


| Metric | Description | 
| --- | --- | 
|  `SnapshotsCopiedAccountStarted`  |  The number of cross\-account snapshot copy actions initiated by a cross\-account copy event policy\.  | 
|  `SnapshotsCopiedAccountCompleted`  |  The number of snapshots copied from another account by a cross\-account copy event policy\. This includes successful retries within 24 hours of the scheduled time\.  | 
|  `SnapshotsCopiedAccountFailed`  |  The number of snapshots that could not be copied from another account by a cross\-account copy event policy\. This includes unsuccessful retries within 24 hours of the scheduled time\.  | 
|  `SnapshotsCopiedAccountDeleteCompleted`  |  The number of cross\-Region snapshot copies deleted, as designated by the retention rule, by a cross\-account copy event policy\.  | 
|  `SnapshotsCopiedAccountDeleteFailed`  |  The number of cross\-Region snapshot copies that could not be deleted, as designated by the retention rule, by a cross\-account copy event policy\.  | 

------

## View CloudWatch metrics for your policies<a name="view-metrics"></a>

You can use the AWS Management Console or the command line tools to list the metrics that Amazon Data Lifecycle Manager sends to Amazon CloudWatch\.

------
#### [ Amazon EC2 console ]

**To view metrics using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Lifecycle Manager**\.

1. Select a policy in the grid and then choose the **Monitoring** tab\.

------
#### [ CloudWatch console ]

**To view metrics using the Amazon CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Metrics**\.

1. Select the **EBS** namespace and then select **Data Lifecycle Manager metrics**\.

------
#### [ AWS CLI ]

**To list all the available metrics for Amazon Data Lifecycle Manager**  
Use the [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command\.

```
$ aws cloudwatch list-metrics --namespace AWS/EBS
```

**To list all the metrics for a specific policy**  
Use the [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command and specify the `DLMPolicyId` dimension\.

```
$ aws cloudwatch list-metrics --namespace AWS/EBS --dimensions Name=DLMPolicyId,Value=policy-abcdef01234567890
```

**To list a single metric across all policies**  
Use the [list\-metrics](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/list-metrics.html) command and specify the `--metric-name` option\.

```
$ aws cloudwatch list-metrics --namespace AWS/EBS --metric-name SnapshotsCreateCompleted
```

------

## Graph metrics for your policies<a name="graph-metrics"></a>

After you create a policy, you can open the Amazon EC2 console and view the monitoring graphs for the policy on the **Monitoring** tab\. Each graph is based on one of the available Amazon EC2 metrics\.

The following graphs metrics are available:
+ Resources targeted \(based on `ResourcesTargeted`\)
+ Snapshot creation started \(based on `SnapshotsCreateStarted`\)
+ Snapshot creation completed \(based on `SnapshotsCreateCompleted`\)
+ Snapshot creation failed \(based on `SnapshotsCreateFailed`\)
+ Snapshot sharing completed \(based on `SnapshotsSharedCompleted`\)
+ Snapshot deletion completed \(based on `SnapshotsDeleteCompleted`\)
+ Snapshot deletion failed \(based on `SnapshotsDeleteFailed`\)
+ Snapshot cross\-Region copy started \(based on `SnapshotsCopiedRegionStarted`\)
+ Snapshot cross\-Region copy completed \(based on `SnapshotsCopiedRegionCompleted`\)
+ Snapshot cross\-Region copy failed \(based on `SnapshotsCopiedRegionFailed`\)
+ Snapshot cross\-Region copy deletion completed \(based on `SnapshotsCopiedRegionDeleteCompleted`\)
+ Snapshot cross\-Region copy deletion failed \(based on `SnapshotsCopiedRegionDeleteFailed`\)
+ Snapshot cross\-account copy started \(based on `SnapshotsCopiedAccountStarted`\)
+ Snapshot cross\-account copy completed \(based on `SnapshotsCopiedAccountCompleted`\)
+ Snapshot cross\-account copy failed \(based on `SnapshotsCopiedAccountFailed`\)
+ Snapshot cross\-account copy deletion completed \(based on `SnapshotsCopiedAccountDeleteCompleted`\)
+ Snapshot cross\-account copy deletion failed \(based on `SnapshotsCopiedAccountDeleteFailed`\)
+ AMI creation started \(based on `ImagesCreateStarted`\)
+ AMI creation completed \(based on `ImagesCreateCompleted`\)
+ AMI creation failed \(based on `ImagesCreateFailed`\)
+ AMI deregistration completed \(based on `ImagesDeregisterCompleted`\)
+ AMI deregistration failed \(based on `ImagesDeregisterFailed`\)
+ AMI cross\-Region copy started \(based on `ImagesCopiedRegionStarted`\)
+ AMI cross\-Region copy completed \(based on `ImagesCopiedRegionCompleted`\)
+ AMI cross\-Region copy failed \(based on `ImagesCopiedRegionFailed`\)
+ AMI cross\-Region copy deregistration completed \(based on `ImagesCopiedRegionDeregisterCompleted`\)
+ AMI cross\-Region copy deregister failed \(based on `ImagesCopiedRegionDeregisteredFailed`\)
+ AMI enable deprecation completed \(based on `EnableImageDeprecationCompleted`\)
+ AMI enable deprecation failed \(based on `EnableImageDeprecationFailed`\)
+ AMI cross\-Region copy enable deprecation completed \(based on `EnableCopiedImageDeprecationCompleted`\)
+ AMI cross\-Region copy enable deprecation failed \(based on `EnableCopiedImageDeprecationFailed`\)

## Create a CloudWatch alarm for a policy<a name="create-alarm"></a>

You can create a CloudWatch alarm that monitors CloudWatch metrics for your policies\. CloudWatch will automatically send you a notification when the metric reaches a threshold that you specify\. You can create a CloudWatch alarm using the CloudWatch console\.

For more information about creating alarms using the CloudWatch console, see the following topic in the *Amazon CloudWatch User Guide*\.
+ [ Create a CloudWatch Alarm Based on a Static Threshold](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ConsoleAlarms.html)
+ [ Create a CloudWatch Alarm Based on Anomaly Detection](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Create_Anomaly_Detection_Alarm.html)

## Example use cases<a name="use-cases"></a>

The following are example use cases\.

**Topics**
+ [Example 1: ResourcesTargeted metric](#case1)
+ [Example 2: SnapshotDeleteFailed metric](#case2)
+ [Example 3: SnapshotsCopiedRegionFailed metric](#case3)

### Example 1: ResourcesTargeted metric<a name="case1"></a>

You can use the `ResourcesTargeted` metric to monitor the total number of resources that are targeted by a specific policy each time it is run\. This enables you to trigger an alarm when the number of targeted resources is below or above an expected threshold\.

For example, if you expect your daily policy to create backups of no more than `50` volumes, you can create an alarm that sends an email notification when the `sum` for `ResourcesTargeted` is greater than `50` over a `1` hour period\. In this way, you can ensure that no snapshots have been unexpectedly created from volumes that have been incorrectly tagged\.

You can use the following command to create this alarm:

```
$ aws cloudwatch put-metric-alarm \
--alarm-name resource-targeted-monitor \
--alarm-description "Alarm when policy targets more than 50 resources" \
--metric-name ResourcesTargeted \
--namespace AWS/EBS \
--statistic Sum \
--period 3600 \
--threshold 50 \
--comparison-operator GreaterThanThreshold \
--dimensions "Name=DLMPolicyId,Value=policy_id" \
--evaluation-periods 1 \
--alarm-actions sns_topic_arn
```

### Example 2: SnapshotDeleteFailed metric<a name="case2"></a>

You can use the `SnapshotDeleteFailed` metric to monitor for failures to delete snapshots as per the policy's snapshot retention rule\. 

For example, if you've created a policy that should automatically delete snapshots every twelve hours, you can create an alarm that notifies your engineering team when the `sum` of `SnapshotDeletionFailed` is greater than `0` over a `1` hour period\. This could help to investigate improper snapshot retention and to ensure that your storage costs are not increased by unnecessary snapshots\.

You can use the following command to create this alarm:

```
$ aws cloudwatch put-metric-alarm \
--alarm-name snapshot-deletion-failed-monitor \
--alarm-description "Alarm when snapshot deletions fail" \
--metric-name SnapshotsDeleteFailed \
--namespace AWS/EBS \
--statistic Sum \
--period 3600 \
--threshold 0 \
--comparison-operator GreaterThanThreshold \
--dimensions "Name=DLMPolicyId,Value=policy_id" \
--evaluation-periods 1 \
--alarm-actions sns_topic_arn
```

### Example 3: SnapshotsCopiedRegionFailed metric<a name="case3"></a>

Use the `SnapshotsCopiedRegionFailed` metric to identify when your policies fail to copy snapshots to other Regions\.

For example, if your policy copies snapshots across Regions daily, you can create an alarm that sends an SMS to your engineering team when the `sum` of `SnapshotCrossRegionCopyFailed` is greater than `0` over a `1` hour period\. This can be useful for verifying whether subsequent snapshots in the lineage were successfully copied by the policy\.

You can use the following command to create this alarm:

```
$ aws cloudwatch put-metric-alarm \
--alarm-name snapshot-copy-region-failed-monitor \
--alarm-description "Alarm when snapshot copy fails" \
--metric-name SnapshotsCopiedRegionFailed \
--namespace AWS/EBS \
--statistic Sum \
--period 3600 \
--threshold 0 \
--comparison-operator GreaterThanThreshold \
--dimensions "Name=DLMPolicyId,Value=policy_id" \
--evaluation-periods 1 \
--alarm-actions sns_topic_arn
```

## Managing policies that report failed actions<a name="manage"></a>

For more information about what to do when one of your policies reports an unexpected non\-zero value for a failed action metric, see the [What should I do if Amazon Data Lifecycle Manager reports failed actions in CloudWatch metrics?](http://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-metrics-dlm/) AWS Knowledge Center article\.
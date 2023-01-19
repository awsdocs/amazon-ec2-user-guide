# Amazon Data Lifecycle Manager<a name="snapshot-lifecycle"></a>

You can use Amazon Data Lifecycle Manager to automate the creation, retention, and deletion of EBS snapshots and EBS\-backed AMIs\. When you automate snapshot and AMI management, it helps you to:
+ Protect valuable data by enforcing a regular backup schedule\.
+ Create standardized AMIs that can be refreshed at regular intervals\.
+ Retain backups as required by auditors or internal compliance\.
+ Reduce storage costs by deleting outdated backups\.
+ Create disaster recovery backup policies that back up data to isolated accounts\.

When combined with the monitoring features of Amazon CloudWatch Events and AWS CloudTrail, Amazon Data Lifecycle Manager provides a complete backup solution for Amazon EC2 instances and individual EBS volumes at no additional cost\.

**Important**  
Amazon Data Lifecycle Manager cannot be used to manage snapshots or AMIs that are created by any other means\.  
Amazon Data Lifecycle Manager cannot be used to automate the creation, retention, and deletion of instance store\-backed AMIs\.

**Topics**
+ [How Amazon Data Lifecycle Manager works](#dlm-elements)
+ [Quotas](#dlm-quotas)
+ [Automate snapshot lifecycles](snapshot-ami-policy.md)
+ [Automate AMI lifecycles](ami-policy.md)
+ [Automate cross\-account snapshot copies](event-policy.md)
+ [View, modify, and delete lifecycle policies](view-modify-delete.md)
+ [AWS Identity and Access Management](dlm-prerequisites.md)
+ [Monitor the lifecycle of snapshots and AMIs](dlm-monitor-lifecycle.md)

## How Amazon Data Lifecycle Manager works<a name="dlm-elements"></a>

The following are the key elements of Amazon Data Lifecycle Manager\.

**Topics**
+ [Snapshots](#dlm-ebs-snapshots)
+ [EBS\-backed AMIs](#dlm-ebs-amis)
+ [Target resource tags](#dlm-tagging-volumes)
+ [Amazon Data Lifecycle Manager tags](#dlm-tagging-snapshots)
+ [Lifecycle policies](#dlm-lifecycle-policies)
+ [Policy schedules](#dlm-lifecycle-schedule)

### Snapshots<a name="dlm-ebs-snapshots"></a>

Snapshots are the primary means to back up data from your EBS volumes\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. When you delete one snapshot in a series of snapshots for a volume, only the data that's unique to that snapshot is removed\. The rest of the captured history of the volume is preserved\. For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

### EBS\-backed AMIs<a name="dlm-ebs-amis"></a>

An Amazon Machine Image \(AMI\) provides the information that's required to launch an instance\. You can launch multiple instances from a single AMI when you need multiple instances with the same configuration\. Amazon Data Lifecycle Manager supports EBS\-backed AMIs only\. EBS\-backed AMIs include a snapshot for each EBS volume that's attached to the source instance\. For more information, see [Amazon Machine Images \(AMI\)](AMIs.md)\.

### Target resource tags<a name="dlm-tagging-volumes"></a>

Amazon Data Lifecycle Manager uses resource tags to identify the resources to back up\. When you create a snapshot or EBS\-backed AMI policy, you can specify multiple target resource tags\. All resources of the specified type \(instance or volume\) that have at least one of the specified target resource tags will be targeted by the policy\. For example, if you create a snapshot policy that targets volumes and you specify `purpose=prod`, `costcenter=prod`, and `environment=live` as target resource tags, then the policy will target all volumes that have any of those tag\-key value pairs\.

If you want to run multiple policies on a resource, you can assign multiple tags to the target resource, and then create separate policies that each target a specific resource tag\.

You can't use the `\` or `=` characters in a tag key\. Target resource tags are case sensitive\. For more information, see [Tag your Amazon EC2 resources](Using_Tags.md)\.

### Amazon Data Lifecycle Manager tags<a name="dlm-tagging-snapshots"></a>

Amazon Data Lifecycle Manager applies the following system tags to all snapshots and AMIs created by a policy, to distinguish them from snapshots and AMIs created by any other means:
+ `aws:dlm:lifecycle-policy-id`
+ `aws:dlm:lifecycle-schedule-name`
+ `aws:dlm:expirationTime` — For snapshots created by an age\-based schedule\. Indicates when the snapshot is to be deleted from the standard tier\. 
+ `aws:dlm:managed`
+ `aws:dlm:archived` — For snapshots that were archived by a schedule\.

You can also specify custom tags to be applied to snapshots and AMIs on creation\. You can't use the `\` or `=` characters in a tag key\.

The target tags that Amazon Data Lifecycle Manager uses to associate volumes with a snapshot policy can optionally be applied to snapshots created by the policy\. Similarly, the target tags that are used to associate instances with an AMI policy can optionally be applied to AMIs created by the policy\.

### Lifecycle policies<a name="dlm-lifecycle-policies"></a>

A lifecycle policy consists of these core settings:
+ **Policy type**—Defines the type of resources that the policy can manage\. Amazon Data Lifecycle Manager supports the following types of lifecycle policies:
  + Snapshot lifecycle policy—Used to automate the lifecycle of EBS snapshots\. These policies can target individual EBS volumes or all EBS volumes attached to an instance\.
  + EBS\-backed AMI lifecycle policy—Used to automate the lifecycle of EBS\-backed AMIs and their backing snapshots\. These policies can target instances only\.
  + Cross\-account copy event policy—Used to automate snapshot copies across accounts\. Use this policy type in conjunction with an EBS snapshot policy that shares snapshots across accounts\.
+ **Resource type**—Defines the type of resources that are targeted by the policy\. Snapshot lifecycle policies can target instances or volumes\. Use `VOLUME` to create snapshots of individual volumes, or use `INSTANCE` to create multi\-volume snapshots of all of the volumes that are attached to an instance\. For more information, see [Multi\-volume snapshots](ebs-creating-snapshot.md#ebs-create-snapshot-multi-volume)\. AMI lifecycle policies can target instances only\. One AMI is created that includes snapshots of all of the volumes that are attached to the target instance\. 
+ **Target tags**—Specifies the tags that must be assigned to an EBS volume or an Amazon EC2 instance for it to be targeted by the policy\.
+ **Policy schedules**\(Snapshot and AMI policies only\)—Define when snapshots or AMIs are to be created and how long to retain them for\. For more information, see [Policy schedules](#dlm-lifecycle-schedule)\. 

For example, you could create a policy with settings similar to the following:
+ Manages all EBS volumes that have a tag with a key of `account` and a value of `finance`\.
+ Creates snapshots every `24` hours at `0900 UTC`\.
+ Retains only the five most recent snapshots\.
+ Starts snapshot creation no later than `0959` UTC each day\.

### Policy schedules<a name="dlm-lifecycle-schedule"></a>

Policy schedules define when snapshots or AMIs are created by the policy\. Policies can have up to four schedules—one mandatory schedule, and up to three optional schedules\.

Adding multiple schedules to a single policy lets you create snapshots or AMIs at different frequencies using the same policy\. For example, you can create a single policy that creates daily, weekly, monthly, and yearly snapshots\. This eliminates the need to manage multiple policies\.

For each schedule, you can define the frequency, fast snapshot restore settings \(snapshot lifecycle policies only\), cross\-Region copy rules, and tags\. The tags that are assigned to a schedule are automatically assigned to the snapshots or AMIs that are created when the schedule is initiated\. In addition, Amazon Data Lifecycle Manager automatically assigns a system\-generated tag based on the schedule's frequency to each snapshot or AMI\.

Each schedule is initiated individually based on its frequency\. If multiple schedules are initiated at the same time, Amazon Data Lifecycle Manager creates only one snapshot or AMI and applies the retention settings of the schedule that has the highest retention period\. The tags of all of the initiated schedules are applied to the snapshot or AMI\.
+ \(Snapshot lifecycle policies only\) If more than one of the initiated schedules is enabled for fast snapshot restore, then the snapshot is enabled for fast snapshot restore in all of the Availability Zones specified across all of the initiated schedules\. The highest retention settings of the initiated schedules is used for each Availability Zone\.
+ If more than one of the initiated schedules is enabled for cross\-Region copy, the snapshot or AMI is copied to all Regions specified across all of the initiated schedules\. The highest retention period of the initiated schedules is applied\.

## Quotas<a name="dlm-quotas"></a>

Your AWS account has the following quotas related to Amazon Data Lifecycle Manager:


| Description | Quota | 
| --- | --- | 
| Lifecycle policies per Region | 100 | 
| Tags per resource | 45 | 
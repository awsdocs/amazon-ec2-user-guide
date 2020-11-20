# Amazon Data Lifecycle Manager<a name="snapshot-lifecycle"></a>

You can use Amazon Data Lifecycle Manager to automate the creation, retention, and deletion of EBS snapshots and EBS\-backed AMIs\. When you automate snapshot and AMI management, it helps you to:
+ Protect valuable data by enforcing a regular backup schedule\.
+ Create standardized AMIs that can be refreshed at regular intervals\.
+ Retain backups as required by auditors or internal compliance\.
+ Reduce storage costs by deleting outdated backups\.

When combined with the monitoring features of Amazon CloudWatch Events and AWS CloudTrail, Amazon Data Lifecycle Manager provides a complete backup solution for Amazon EC2 instances and individual EBS volumes at no additional cost\.

**Important**  
Amazon Data Lifecycle Manager cannot be used to manage snapshots or AMIs that are created by any other means\.  
Amazon Data Lifecycle Manager cannot be used to automate the creation, retention, and deletion of instance store\-backed AMIs\.

**Topics**
+ [How Amazon Data Lifecycle Manager works](#dlm-elements)
+ [Considerations for Amazon Data Lifecycle Manager](#dlm-considerations)
+ [Prerequisites](#dlm-prerequisites)
+ [Manage backups using the console](#snapshot-lifecycle-console)
+ [Manage backups using the AWS CLI](#snaphot-lifecycle-cli)
+ [Manage backups using the API](#snaphot-lifecycle-api)
+ [Monitor the snapshot lifecycle](#dlm-monitor-lifecycle)

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

Snapshots are the primary means to back up data from your EBS volumes\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. When you delete one snapshot in a series of snapshots for a volume, only the data that's unique to that snapshot is removed\. The rest of the captured history of the volume is preserved\.

For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

### EBS\-backed AMIs<a name="dlm-ebs-amis"></a>

An Amazon Machine Image \(AMI\) provides the information that's required to launch an instance\. You can launch multiple instances from a single AMI when you need multiple instances with the same configuration\. Amazon Data Lifecycle Manager supports EBS\-backed AMIs only\. EBS\-backed AMIs include a snapshot for each EBS volume that's attached to the source instance\.

For more information, see [Amazon Machine Images \(AMI\)](AMIs.md)\.

### Target resource tags<a name="dlm-tagging-volumes"></a>

Amazon Data Lifecycle Manager uses resource tags to identify the resources to back up\. Tags are customizable metadata that you can assign to your AWS resources \(including Amazon EC2 instances, EBS volumes and snapshots\)\. An Amazon Data Lifecycle Manager policy \(described later\) targets an instance or volume for backup using a single tag\. Multiple tags can be assigned to an instance or volume if you want to run multiple policies on it\.

You can't use a '\\' or '=' character in a tag key\.

For more information, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

### Amazon Data Lifecycle Manager tags<a name="dlm-tagging-snapshots"></a>

Amazon Data Lifecycle Manager applies the following tags to all snapshots and AMIs created by a policy, to distinguish them from snapshots and AMIs created by any other means:
+ `aws:dlm:lifecycle-policy-id`
+ `aws:dlm:lifecycle-schedule-name`
+ `aws:dlm:expirationTime`
+ `dlm:managed`

You can also specify custom tags to be applied to snapshots and AMIs on creation\. You can't use a '\\' or '=' character in a tag key\.

The target tags that Amazon Data Lifecycle Manager uses to associate volumes with a snapshot policy can optionally be applied to snapshots created by the policy\. Similarly, the target tags that are used to associate instances with an AMI policy can optionally be applied to AMIs created by the policy\.

### Lifecycle policies<a name="dlm-lifecycle-policies"></a>

A lifecycle policy consists of these core settings:
+ **Policy type**—Defines the type of resources that the policy can manage\. Amazon Data Lifecycle Manager supports two types of lifecycle policies: 
  + Snapshot lifecycle policy—Used to automate the lifecycle of EBS snapshots\. These policies can target EBS volumes and instances\.
  + EBS\-backed AMI lifecycle policy—Used to automate the lifecycle of EBS\-backed AMIs\. These policies can target instances only\.
+ **Resource type**—Defines the type of resources that are targeted by the policy\. Snapshot lifecycle policies can target instances or volumes\. Use `VOLUME` to create snapshots of individual volumes, or use `INSTANCE` to create multi\-volume snapshots of all of the volumes that are attached to an instance\. For more information, see [Multi\-volume snapshots](ebs-creating-snapshot.md#ebs-create-snapshot-multi-volume)\. AMI lifecycle policies can target instances only\. One AMI is created that includes snapshots of all of the volumes that are attached to the target instance\. 
+ **Target tags**—Specifies the tags that must be assigned to an EBS volume or an Amazon EC2 instance for it to be targeted by the policy\.
+ **Schedules**—The start times and intervals for creating snapshots or AMIs\. The first snapshot or AMI is created by a policy within one hour after the specified start time\. Subsequent snapshots or AMIs are created within one hour of their scheduled time\. A policy can have up to four schedules: one mandatory schedule, and up to three optional schedules\. For more information, see [Policy schedules](#dlm-lifecycle-schedule)\. 
+ **Retention**—Specifies how snapshots or AMIs are to be retained\. You can retain snapshots or AMIs based either on their total count \(count\-based\), or their age \(age\-based\)\. For snapshot policies, when the retention threshold is reached, the oldest snapshot is deleted\. For AMI policies, when the retention threshold is reached, the oldest AMI is deregistered and its backing snapshots are deleted\. 

For example, you could create a policy with settings similar to the following:
+ Manages all EBS volumes that have a tag with a key of `account` and a value of `finance`\.
+ Creates snapshots every `24` hours at `0900 UTC`\.
+ Retains only the five most recent snapshots\.
+ Starts snapshot creation no later than `0959` UTC each day\.

### Policy schedules<a name="dlm-lifecycle-schedule"></a>

Policy schedules define when snapshots or AMIs are created by the policy\. Policies can have up to four schedules—one mandatory schedule, and up to three optional schedules\.

Adding multiple schedules to a single policy lets you create snapshots or AMIs at different frequencies using the same policy\. For example, you can create a single policy that creates daily, weekly, monthly, and yearly snapshots\. This eliminates the need to manage multiple policies\.

For each schedule, you can define the frequency, fast snapshot restore settings \(snapshot lifecycle policies only\), cross\-Region copy rules, and tags\. The tags that are assigned to a schedule are automatically assigned to the snapshots or AMIs that are created when the schedule is triggered\. In addition, Amazon Data Lifecycle Manager automatically assigns a system\-generated tag based on the schedule's frequency to each snapshot or AMI\.

Each schedule is triggered individually based on its frequency\. If multiple schedules are triggered at the same time, Amazon Data Lifecycle Manager creates only one snapshot or AMI and applies the retention settings of the schedule that has the highest retention period\. The tags of all of the triggered schedules are applied to the snapshot or AMI\.
+ \(Snapshot lifecycle policies only\) If more than one of the triggered schedules is enabled for fast snapshot restore, then the snapshot is enabled for fast snapshot restore in all of the Availability Zones specified across all of the triggered schedules\. The highest retention settings of the triggered schedules is used for each Availability Zone\.
+ If more than one of the triggered schedules is enabled for cross\-Region copy, the snapshot or AMI is copied to all Regions specified across all of the triggered schedules\. The highest retention period of the triggered schedules is applied\.

## Considerations for Amazon Data Lifecycle Manager<a name="dlm-considerations"></a>

Your AWS account has the following quotas related to Amazon Data Lifecycle Manager:
+ You can create up to 100 lifecycle policies per Region\.
+ You can add up to 45 tags per resource\.

The following considerations apply to lifecycle policies:
+ A policy does not begin creating snapshots or AMIs until you set its activation status to *enabled*\. You can configure a policy to be enabled upon creation\.
+ The first snapshot or AMI is created by a policy within one hour after the specified start time\. Subsequent snapshots or AMIs are created within one hour of their scheduled time\.
+ If you modify a policy by removing or changing its target tags, the EBS volumes or instances with those tags are no longer managed by the policy\.
+ If you modify a schedule name for a policy, the snapshots or AMIs created under the old schedule name are no longer affected by the policy\.
+ If you modify a time\-based retention schedule to use a new time interval, the new interval is used only for new snapshots or AMIs created after the change\. The new schedule does not affect the retention schedule of snapshots or AMIs created before the change\.
+ You cannot change the retention schedule of a policy from count\-based to time\-based after creation\. To make this change, you must create a new policy\.
+ If you disable a policy with an age\-based retention schedule, the snapshots or AMIs that are set to expire while the policy is disabled are retained indefinitely\. You must delete the snapshots or deregister the AMIs manually\. When you enable the policy again, Amazon Data Lifecycle Manager resumes deleting snapshots or deregistering AMIs as their retention periods expire\.
+ If you delete the resource to which a policy with count\-based retention applies, the policy no longer manages the previously created snapshots or AMIs\. You must manually delete the snapshots or deregister the AMIs if they are no longer needed\.
+ If you delete the resource to which a policy with age\-based retention applies, the policy continues to delete snapshots or deregister AMIs on the defined schedule, up to the last snapshot or AMI\. You must manually delete the last snapshot or deregister the last AMI if it is no longer needed\.
+ You can create multiple policies to back up an EBS volume or an Amazon EC2 instance\. For example, if an EBS volume has two tags, where tag *A* is the target for policy *A* to create a snapshot every 12 hours, and tag *B* is the target for policy *B* to create a snapshot every 24 hours, Amazon Data Lifecycle Manager creates snapshots according to the schedules for both policies\. Alternatively, you can achieve the same result by creating a single policy that has multiple schedules\. For example, you can create a single policy that targets only tag *A*, and specify two schedules—one for every 12 hours and one for every 24 hours\.
+ If you create a policy that targets instances, and new volumes are attached to the instance after the policy has been created, the newly\-added volumes are included in the backup at the next policy run\. All volumes attached to the instance at the time of the policy run are included\.
+ For AMI lifecycle policies, when the AMI retention threshold is reached, the oldest AMI is deregistered and its backing snapshots are deleted\.

The following considerations apply to snapshot lifecycle policies and [fast snapshot restore](ebs-fast-snapshot-restore.md):
+ A snapshot that is enabled for fast snapshot restore remains enabled even if you delete or disable the lifecycle policy, disable fast snapshot restore for the lifecycle policy, or disable fast snapshot restore for the Availability Zone\. You can disable fast snapshot restore for these snapshots manually\.
+ If you enable fast snapshot restore and you exceed the maximum number of snapshots that can be enabled for fast snapshot restore, Amazon Data Lifecycle Manager creates snapshots as scheduled but does not enable them for fast snapshot restore\. After a snapshot that is enabled for fast snapshot restore is deleted, the next snapshot that Amazon Data Lifecycle Manager creates is enabled for fast snapshot restore\.
+ When you enable fast snapshot restore for a snapshot, it takes 60 minutes per TiB to optimize the snapshot\. We recommend that you create a schedule that ensures that each snapshot is fully optimized before Amazon Data Lifecycle Manager creates the next snapshot\.
+ You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\. For more information, see [Pricing and Billing](ebs-fast-snapshot-restore.md#fsr-pricing)\.
**Note**  
Depending on the configuration of your lifecycle policies, you could have multiple snapshots enabled for fast snapshot restore simultaneously\.

The following considerations apply to snapshot lifecycle policies and [Multi\-Attach](ebs-volumes-multi.md) enabled volumes:
+ When creating a lifecycle policy based on instance tags for Multi\-Volume snapshots, Amazon Data Lifecycle Manager initiates a snapshot of the volume for each attached instance\. Use the *timestamp* tag to identify the set of time\-consistent snapshots that are created from the attached instances\.

## Prerequisites<a name="dlm-prerequisites"></a>

The following prerequisites are required by Amazon Data Lifecycle Manager\.

**Topics**
+ [Permissions for Amazon Data Lifecycle Manager](#dlm-permissions)
+ [Permissions for IAM users](#dlm-access-control)
+ [Permissions for encryption](#dlm-access-cmk)

### Permissions for Amazon Data Lifecycle Manager<a name="dlm-permissions"></a>

Amazon Data Lifecycle Manager uses IAM roles to get the permissions that are required to manage snapshots and AMIs on your behalf\. Amazon Data Lifecycle Manager creates the following default roles the first time you create a lifecycle policy using the AWS Management Console\.
+ **AWSDataLifecycleManagerDefaultRole**—default role for managing snapshots\. It is created the first time you create a snapshot lifecycle policy using the console\.
+ **AWSDataLifecycleManagerDefaultRoleForAMIManagement**—default role for managing AMIs\. It is created the first time you create an AMI lifecycle policy using the console\.

You can also create this role manually using the [create\-default\-role](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-default-role.html) command\. For `--resource-type`, specify one of the following, depending on the role to create:
+ `snapshot`—to create the default role for managing snapshot lifecycle policies
+ `image`—to create the default role for managing AMI lifecycle policies

```
aws dlm create-default-role --resource-type snapshot|image
```

Alternatively, you can create custom IAM roles with the required permissions and select them when you create a lifecycle policy\. 

**To create a custom IAM role**

1. Create roles with the following permissions\.
   + Permissions for managing snapshot lifecycle policies

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
         {
             "Effect": "Allow",
             "Action": [
                 "ec2:CreateSnapshot",
                 "ec2:CreateSnapshots",
                 "ec2:DeleteSnapshot",
                 "ec2:DescribeVolumes",
                 "ec2:DescribeInstances",
                 "ec2:DescribeSnapshots"
             ],
             "Resource": "*"
         },
         {
             "Effect": "Allow",
             "Action": [
                 "ec2:CreateTags"
             ],
             "Resource": "arn:aws:ec2:*::snapshot/*"
         }]
     }
     ```
   + Permissions for managing AMI lifecycle policies

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
         {
             "Effect": "Allow",
             "Action": "ec2:CreateTags",
             "Resource": [
                 "arn:aws:ec2:*::snapshot/*",
                 "arn:aws:ec2:*::image/*"
             ]
         },
         {
             "Effect": "Allow",
             "Action": [
                 "ec2:DescribeImages",
                 "ec2:DescribeInstances",
                 "ec2:DescribeImageAttribute",
                 "ec2:DescribeVolumes",
                 "ec2:DescribeSnapshots"
             ],
             "Resource": "*"
         },
         {
             "Effect": "Allow",
             "Action": "ec2:DeleteSnapshot",
             "Resource": "arn:aws:ec2:*::snapshot/*"
         },
         {
             "Effect": "Allow",
             "Action": [
             "ec2:ResetImageAttribute",
             "ec2:DeregisterImage",
             "ec2:CreateImage",
             "ec2:CopyImage",
             "ec2:ModifyImageAttribute"
         ],
         "Resource": "*"
         }]
     }
     ```

   For more information, see [ Creating a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

1. Add a trust relationship to the roles\.

   1. In the IAM console, choose **Roles**\.

   1. Select the roles that you created and then choose **Trust relationships**\.

   1. Choose **Edit Trust Relationship**, add the following policy, and then choose **Update Trust Policy**\.

      ```
      { 
          "Version": "2012-10-17",
          "Statement": [ 
          { 
              "Effect": "Allow", 
              "Principal": { 
              "Service": "dlm.amazonaws.com"
          }, 
              "Action": "sts:AssumeRole" 
          } ] 
      }
      ```

### Permissions for IAM users<a name="dlm-access-control"></a>

An IAM user must have the following permissions to use Amazon Data Lifecycle Manager\.

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Action": ["iam:PassRole", "iam:ListRoles"],
        "Resource": "arn:aws:iam::123456789012:role/AWSDataLifecycleManagerDefaultRole"
    },
    {
        "Effect": "Allow", 
        "Action": "dlm:*",
        "Resource": "*"
    }]
}
```

For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.

### Permissions for encryption<a name="dlm-access-cmk"></a>

If the source volume is encrypted, ensure that the Amazon Data Lifecycle Manager default roles \(**AWSDataLifecycleManagerDefaultRole** and **AWSDataLifecycleManagerDefaultRoleForAMIManagement**\) have permission to use the AWS KMS customer master keys \(CMKs\) used to encrypt the volume\.

If you enable **Cross Region copy** for unencrypted snapshots or AMIs backed by unencrypted snapshots, and choose to enable encryption in the destination Region, ensure that the default roles have permission to use the CMK needed to perform the encryption in the destination Region\.

If you enable **Cross Region copy** for encrypted snapshots or AMIs backed by encrypted snapshots, ensure that the default roles have permission to use both the source and destination CMKs\. 

For more information, see [ Managing access to AWS KMS CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#managing-access) in the *AWS Key Management Service Developer Guide*\.

## Manage backups using the console<a name="snapshot-lifecycle-console"></a>

The following examples show how to use Amazon Data Lifecycle Manager to manage the backups of your EBS volumes and Amazon EC2 instances using the AWS Management Console\.

**Topics**
+ [Create a lifecycle policy](#console-create-policy)
+ [View a lifecycle policy](#console-view-policy)
+ [Modify a lifecycle policy](#console-modify-policy)
+ [Delete a lifecycle policy](#console-delete-policy)

### Create a lifecycle policy<a name="console-create-policy"></a>

Use the following procedure to create a lifecycle policy\.

**To create a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, and then choose **Create lifecycle policy**\.

1. Provide the following information for your policy as needed:
   + **Description**—A description of the policy\.
   + **Policy type**—The type of resource to be managed by this policy\. To create a policy that automates the lifecycle of snapshots, choose **EBS snapshot policy**\. To create a policy that automates the lifecycle of EBS\-backed AMIs, choose **EBS\-backed AMI policy**\. 
   + **Resource type**—\(Snapshot lifecycle policies only\) The type of resource to back up\. Choose `Volume` to create snapshots of individual volumes or choose `Instance` to create multi\-volume snapshots from the volumes attached to an instance\. AMI lifecycle policies can back up instances only\.
   + **Target with these tags**—The resource tags that identify the volumes or instances to back up\.
   + **Lifecycle policy tags**—The tags to apply to the lifecycle policy\.

1. For **IAM role**, choose the IAM role that has permissions to create, delete, and describe snapshots or AMIs, depending on the selected policy type, and to describe volumes and instances\. AWS provides a default roles, or you can create a custom IAM role\.

1. Add the policy schedules\. Schedule 1 is mandatory\. Schedules 2, 3, and 4 are optional\. For each policy schedule, specify the following information:
   + **Schedule name**—A name for the schedule\.
   + **Frequency**—The interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to one year\. For more information, see [Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.
   + **Starting at ***hh*:*mm* **UTC**—The time at which the policy runs are scheduled to start\. The first policy run starts within an hour after the scheduled time\.
   + **Retention type**—You can retain snapshots or AMIs based on either their total count or their age\. For count\-based retention, the range is 1 to 1000\. After the maximum count is reached, the oldest snapshot or AMI is deleted when a new one is created\. For age\-based retention, the range is 1 day to 100 years\. After the retention period of each snapshot or AMI expires, it is deleted\. The retention period should be greater than or equal to the creation interval\.
**Note**  
All schedules must have the same retention type\. You can specify the retention type for Schedule 1 only\. Schedules 2, 3, and 4 inherit the retention type from Schedule 1\. Each schedule can have its own retention count or period\.
   + **Copy tags from source**—Choose whether to copy all of the user\-defined tags from the source resource to the snapshots or the AMIs created by this schedule\. For snapshot lifecycle policies, the tags assigned to the source volume are assigned to the snapshot\. For AMI lifecycle policies, the tags assigned to the source instance are assigned to the AMI\.
   + **Dynamic tags**—If the source resource is an instance, you can choose to automatically tag your snapshots or AMIs with the following variable tags:
     + `instance-id`—The ID of the source instance\.
     + `timestamp`—\(Snapshot lifecycle policies only\) The date and time of the policy run\.
   + **Additional tags**—Specify any additional tags to assign to the snapshots or AMIs created by this schedule\.
   + **Fast snapshot restore**—\(Snapshot lifecycle policies only\) Choose whether to enable fast snapshot restore for all snapshots that are created by this policy\. If you enable fast snapshot restore, you must choose the Availability Zones in which to enable it\. You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\. You can also specify the maximum number of snapshots that can be enabled for fast snapshot restore\.
   + **Enable cross Region copy**—You can copy each snapshot or AMI to up at three additional Regions\. You must ensure that you do not exceed the number of concurrent snapshot or AMI copies per Region\. For each Region, you can choose different retention policies and you can choose whether to copy all tags or no tags\. If the source snapshot or AMI is encrypted, or if encryption by default is enabled, the copied snapshots or AMIs are encrypted\. If the source snapshot or AMI is unencrypted, you can enable encryption\. If you do not specify a CMK, the snapshots or AMIs are encrypted using the default key for EBS encryption in each destination Region\. If you specify a CMK for the destination Region, you must have access to the CMK\.

1. \(AMI lifecycle policies only\) Indicate whether instances should be rebooted before AMI creation\. To prevent the targeted instances from being rebooted, for **Reboot Instance at policy run**, choose **No**\. Choosing this option could cause data consistency issues\. To reboot instances before AMI creation, for **Reboot Instance at policy run**, choose **Yes**\. Choosing this ensures data consistency but could result in multiple targeted instances rebooting simultaneously\. 

1. For **Policy status after creation**, choose **Enable policy** to start the policy runs at the next scheduled time, or **Disable policy** to prevent the policy from running\.

1. Choose **Create Policy**\.

### View a lifecycle policy<a name="console-view-policy"></a>

Use the following procedure to view a lifecycle policy\.

**To view a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\. The **Details** tab displays information about the policy\.

### Modify a lifecycle policy<a name="console-modify-policy"></a>

Use the following procedure to modify a lifecycle policy\.

**To modify a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\.

1. Choose **Actions**, **Modify Lifecycle Policy**\.

1. Modify the policy settings as needed\. For example, you can modify the schedule, add or remove tags, or enable or disable the policy\.

1. Choose **Update policy**\.

### Delete a lifecycle policy<a name="console-delete-policy"></a>

Use the following procedure to delete a lifecycle policy\.

**Note**  
You can delete snapshots created only by Amazon Data Lifecycle Manager\.

**To delete a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\.

1. Choose **Actions**, **Delete Lifecycle Policy**\.

1. When prompted for confirmation, choose **Delete Lifecycle Policy**\.

## Manage backups using the AWS CLI<a name="snaphot-lifecycle-cli"></a>

The following examples show how to use Amazon Data Lifecycle Manager to manage the backups of your EBS volumes and Amazon EC2 instances using the AWS CLI\.

**Topics**
+ [Create a lifecycle policy](#create-policy-cli)
+ [Display a lifecycle policy](#display-policy-cli)
+ [Modify a lifecycle policy](#modify-policy-cli)
+ [Delete a lifecycle policy](#delete-policy-cli)

### Create a lifecycle policy<a name="create-policy-cli"></a>

Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create a lifecycle policy\. To create a snapshot lifecycle policy, for `PolicyType`, specify `EBS_SNAPSHOT_MANAGEMENT`\. To create an AMI lifecycle policy, for `PolicyType`, specify `IMAGE_MANAGEMENT`\.

To simplify the syntax, the following examples use a JSON file, `policyDetails.json`, that includes the policy details\.

**Example 1—Snapshot lifecycle policy**  
This example creates a snapshot lifecycle policy that creates snapshots of all volumes that have a tag key of `costcenter` with a value of `115`\. The policy includes two schedules\. The first schedule creates a snapshot every day at 03:00 UTC\. The second schedule creates a weekly snapshot every Friday at 17:00 UTC\.

```
aws dlm create-lifecycle-policy --description "My volume policy" --state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole --policy-details file://policyDetails.json
```

The following is an example of the `policyDetails.json` file\.

```
{
    "PolicyType": "EBS_SNAPSHOT_MANAGEMENT",
    "ResourceTypes": [
        "VOLUME"
    ],
    "TargetTags": [{
        "Key": "costcenter",
        "Value": "115"
    }],
    "Schedules": [{
        "Name": "DailySnapshots",
        "TagsToAdd": [{
            "Key": "type",
            "Value": "myDailySnapshot"
        }],
        "CreateRule": {
            "Interval": 24,
            "IntervalUnit": "HOURS",
            "Times": [
                "03:00"
            ]
        },
        "RetainRule": {
            "Count": 5
        },
        "CopyTags": false
    },
    {
        "Name": "WeeklySnapshots",
        "TagsToAdd": [{
            "Key": "type",
            "Value": "myWeeklySnapshot"
        }],
        "CreateRule": {
            "CronExpression": "cron(0 0 17 ? * FRI *)"
        },
        "RetainRule": {
            "Count": 5
        },
        "CopyTags": false
    }
]}
```

Upon success, the command returns the ID of the newly created policy\. The following is example output\.

```
{
   "PolicyId": "policy-0123456789abcdef0"
}
```

**Example 2—AMI lifecycle policy**  
This example creates an AMI lifecycle policy that creates AMIs of all instances that have a tag key of `purpose` with a value of `production` without rebooting the targeted instances\. The policy includes one schedule that creates an AMI every day at 01:00 UTC\. The policy will retain the two most recent AMIs \(and their backing snapshots\), and it will copy the tags from the source instance to the AMIs that it creates\.

```
aws dlm create-lifecycle-policy --description "My AMI policy" --state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRoleForAMIManagement --policy-details file://policyDetails.json
```

The following is an example of the `policyDetails.json` file\.

```
{
    "PolicyType": "IMAGE_MANAGEMENT",
    "ResourceTypes": [
        "INSTANCE"
    ],
    "TargetTags": [{
        "Key": "purpose",
        "Value": "production"
    }],
    "Schedules": [{
            "Name": "DailyAMIs",
            "TagsToAdd": [{
                "Key": "type",
                "Value": "myDailyAMI"
            }],
            "CreateRule": {
                "Interval": 24,
                "IntervalUnit": "HOURS",
                "Times": [
                    "01:00"
                ]
            },
            "RetainRule": {
                "Count": 2
            },
            "CopyTags": true
        }
    ],
    "Parameters" : {
        "NoReboot":true
    }
}
```

Upon success, the command returns the ID of the newly created policy\. The following is example output\.

```
{
   "PolicyId": "policy-9876543210abcdef0"
}
```

### Display a lifecycle policy<a name="display-policy-cli"></a>

Use the [get\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/get-lifecycle-policy.html) command to display information about a lifecycle policy\.

```
aws dlm get-lifecycle-policy --policy-id policy-0123456789abcdef0
```

The following is example output\. It includes the information that you specified, plus metadata inserted by AWS\.

```
{
   "Policy":{
      "Description": "My first policy",
      "DateCreated": "2018-05-15T00:16:21+0000",
      "State": "ENABLED",
      "ExecutionRoleArn": "arn:aws:iam::210774411744:role/AWSDataLifecycleManagerDefaultRole",
      "PolicyId": "policy-0123456789abcdef0",
      "DateModified": "2018-05-15T00:16:22+0000",
      "PolicyDetails": {
        "PolicyType":"EBS_SNAPSHOT_MANAGEMENT",
         "ResourceTypes": [
            "VOLUME"
         ],
         "TargetTags": [
            {
               "Value": "115",
               "Key": "costcenter"
            }
         ],
         "Schedules": [
            {
               "TagsToAdd": [
                  {
                     "Value": "myDailySnapshot",
                     "Key": "type"
                  }
               ],
               "RetainRule": {
                  "Count": 5
               },
               "CopyTags": false, 
               "CreateRule": {
                  "Interval": 24,
                  "IntervalUnit": "HOURS",
                  "Times": [
                     "03:00"
                  ]
               },
               "Name": "DailySnapshots"
            }
         ]
      }
   }
}
```

### Modify a lifecycle policy<a name="modify-policy-cli"></a>

Use the [update\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/update-lifecycle-policy.html) command to modify the information in a lifecycle policy\. To simplify the syntax, this example references a JSON file, `policyDetailsUpdated.json`, that includes the policy details\.

```
aws dlm update-lifecycle-policy --state DISABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole" --policy-details file://policyDetailsUpdated.json
```

The following is an example of the `policyDetailsUpdated.json` file\.

```
{
   "ResourceTypes":[
      "VOLUME"
   ],
   "TargetTags":[
      {
         "Key": "costcenter",
         "Value": "120"
      }
   ],
   "Schedules":[
      {
         "Name": "DailySnapshots",
         "TagsToAdd": [
            {
               "Key": "type",
               "Value": "myDailySnapshot"
            }
         ],
         "CreateRule": {
            "Interval": 12,
            "IntervalUnit": "HOURS",
            "Times": [
               "15:00"
            ]
         },
         "RetainRule": {
            "Count" :5
         },
         "CopyTags": false 
      }
   ]
}
```

To view the updated policy, use the `get-lifecycle-policy` command\. You can see that the state, the value of the tag, the snapshot interval, and the snapshot start time were changed\.

### Delete a lifecycle policy<a name="delete-policy-cli"></a>

Use the [delete\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/delete-lifecycle-policy.html) command to delete a lifecycle policy and free up the target tags specified in the policy for reuse\. 

**Note**  
You can delete snapshots created only by Amazon Data Lifecycle Manager\.

```
aws dlm delete-lifecycle-policy --policy-id policy-0123456789abcdef0
```

## Manage backups using the API<a name="snaphot-lifecycle-api"></a>

The [Amazon Data Lifecycle Manager API Reference](https://docs.aws.amazon.com/dlm/latest/APIReference/) provides descriptions and syntax for each of the actions and data types for the Amazon Data Lifecycle Manager Query API\.

Alternatively, you can use one of the AWS SDKs to access the API in a way that's tailored to the programming language or platform that you're using\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.

## Monitor the snapshot lifecycle<a name="dlm-monitor-lifecycle"></a>

You can use the following features to monitor the lifecycle of your snapshots and AMIs\.

**Topics**
+ [Console and AWS CLI](#monitor-console-cli)
+ [CloudWatch Events](#monitor-cloudwatch-events)
+ [AWS CloudTrail](#monitor-lifecycle-cloudtrail)

### Console and AWS CLI<a name="monitor-console-cli"></a>

You can view your lifecycle policies using the Amazon EC2 console or the AWS CLI\. Each snapshot and AMI created by a policy has a timestamp and policy\-related tags\. You can filter snapshots and AMIs using these tags to verify that your backups are being created as you intend\. For information about viewing lifecycle policies using the console, see [View a lifecycle policy](#console-view-policy)\. For information about displaying information about lifecycle policies using the CLI, see [Display a lifecycle policy](#display-policy-cli)\.

### CloudWatch Events<a name="monitor-cloudwatch-events"></a>

Amazon EBS and Amazon Data Lifecycle Manager emit events related to lifecycle policy actions\. You can use AWS Lambda and Amazon CloudWatch Events to handle event notifications programmatically\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following events are available:

**Note**  
No events are emitted for AMI lifecycle policy actions\.
+ `createSnapshot`—An Amazon EBS event emitted when a `CreateSnapshot` action succeeds or fails\. For more information, see [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.
+ `DLM Policy State Change`—An Amazon Data Lifecycle Manager event emitted when a lifecycle policy enters an error state\. The event contains a description of what caused the error\. The following is an example of an event when the permissions granted by the IAM role are insufficient\.

  ```
  {
      "version": "0",
      "id": "01234567-0123-0123-0123-0123456789ab",
      "detail-type": "DLM Policy State Change",
      source": "aws.dlm",
      "account": "123456789012",
      "time": "2018-05-25T13:12:22Z",
      "region": "us-east-1",
      "resources": [
          "arn:aws:dlm:us-east-1:123456789012:policy/policy-0123456789abcdef"
      ],
      "detail": {
          "state": "ERROR",
          "cause": "Role provided does not have sufficient permissions",
          "policy_id": "arn:aws:dlm:us-east-1:123456789012:policy/policy-0123456789abcdef"
      }
  }
  ```

  The following is an example of an event when a limit is exceeded\.

  ```
  {
      "version": "0",
      "id": "01234567-0123-0123-0123-0123456789ab",
      "detail-type": "DLM Policy State Change",
      "source": "aws.dlm",
      "account": "123456789012",
      "time": "2018-05-25T13:12:22Z",
      "region": "us-east-1",
      "resources": [
          "arn:aws:dlm:us-east-1:123456789012:policy/policy-0123456789abcdef"
      ],
      "detail":{
          "state": "ERROR",
          "cause": "Maximum allowed active snapshot limit exceeded",
          "policy_id": "arn:aws:dlm:us-east-1:123456789012:policy/policy-0123456789abcdef"
      }
  }
  ```

### AWS CloudTrail<a name="monitor-lifecycle-cloudtrail"></a>

With AWS CloudTrail, you can track user activity and API usage to demonstrate compliance with internal policies and regulatory standards\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.
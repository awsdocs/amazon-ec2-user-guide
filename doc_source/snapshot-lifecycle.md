# Automating the Amazon EBS snapshot lifecycle<a name="snapshot-lifecycle"></a>

You can use Amazon Data Lifecycle Manager to automate the creation, retention, and deletion of snapshots taken to back up your Amazon EBS volumes\. Automating snapshot management helps you to:
+ Protect valuable data by enforcing a regular backup schedule\.
+ Retain backups as required by auditors or internal compliance\.
+ Reduce storage costs by deleting outdated backups\.

Combined with the monitoring features of Amazon CloudWatch Events and AWS CloudTrail, Amazon Data Lifecycle Manager provides a complete backup solution for EBS volumes at no additional cost\.

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
+ [Target resource tags](#dlm-tagging-volumes)
+ [Snapshot tags](#dlm-tagging-snapshots)
+ [Lifecycle policies](#dlm-lifecycle-policies)

### Snapshots<a name="dlm-ebs-snapshots"></a>

Snapshots are the primary means to back up data from your EBS volumes\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. When you delete one snapshot in a series of snapshots for a volume, only the data unique to that snapshot is removed\. The rest of the captured history of the volume is preserved\.

For more information, see [Amazon EBS snapshots](EBSSnapshots.md)\.

### Target resource tags<a name="dlm-tagging-volumes"></a>

Amazon Data Lifecycle Manager uses resource tags to identify the EBS volumes to back up\. Tags are customizable metadata that you can assign to your AWS resources \(including EBS volumes and snapshots\)\. An Amazon Data Lifecycle Manager policy \(described below\) targets a volume for backup using a single tag\. Multiple tags can be assigned to a volume if you want to run multiple policies on it\.

You can't use a '\\' or '=' character in a tag key\.

For more information, see [Tagging your Amazon EC2 resources](Using_Tags.md)\.

### Snapshot tags<a name="dlm-tagging-snapshots"></a>

Amazon Data Lifecycle Manager applies the following tags to all snapshots created by a policy, to distinguish them from snapshots created by any other means:
+ `aws:dlm:lifecycle-policy-id`
+ `aws:dlm:lifecycle-schedule-name`

You can also specify custom tags to be applied to snapshots on creation\.

You can't use a '\\' or '=' character in a tag key\.

The target tags that Amazon Data Lifecycle Manager uses to associate volumes with a policy can optionally be applied to snapshots created by the policy\.

### Lifecycle policies<a name="dlm-lifecycle-policies"></a>

A lifecycle policy consists of these core settings:
+ Resource type—The type of AWS resource managed by the policy\. Use `VOLUME` to create snapshots of individual volumes or use `INSTANCE` to create multi\-volume snapshots from the volumes for an instance\. For more information, see [Multi\-volume snapshots](ebs-creating-snapshot.md#ebs-create-snapshot-multi-volume)\.
+ Target tags—The tags that must be associated with an EBS volume or an EC2 instance for it to be managed by the policy\.
+ Schedule—The start time and interval for creating snapshots\. The first snapshot is created by a policy within one hour after the specified start time\. Subsequent snapshots are created within one hour of their scheduled time\.
+ Retention—You can retain snapshots based on either the total count of snapshots or the age of each snapshot\.

For example, you could create a policy that manages all EBS volumes with the tag `account=Finance`, creates snapshots every 24 hours at 0900, and retains the five most recent snapshots\. Snapshot creation would start by 0959 each day\.

## Considerations for Amazon Data Lifecycle Manager<a name="dlm-considerations"></a>

Your AWS account has the following quotas related to Amazon Data Lifecycle Manager:
+ You can create up to 100 lifecycle policies per Region\.
+ You can add up to 45 tags per resource\.
+ You can create one schedule per lifecycle policy\.

The following considerations apply to lifecycle policies:
+ A policy does not begin creating snapshots until you set its activation status to *enabled*\. You can configure a policy to be enabled upon creation\.
+ The first snapshot is created by a policy within one hour after the specified start time\. Subsequent snapshots are created within one hour of their scheduled time\.
+ If you modify a policy by removing or changing its target tag, the EBS volumes with that tag are no longer affected by the policy\.
+ If you modify the schedule name for a policy, the snapshots created under the old schedule name are no longer affected by the policy\.
+ If you modify a retention schedule based on time to use a new time interval, the new interval is used only for new snapshots\. The new schedule does not affect the retention schedule of existing snapshots created by this policy\.
+ You cannot change the retention schedule of a policy from the count of snapshots to the age of each snapshot\. To make this change, you must create a new policy\.
+ If you disable a policy with a retention schedule based on the age of each snapshot, the snapshots whose retention periods expire while the policy is disabled are retained indefinitely\. You must delete these snapshots manually\. When you enable the policy again, Amazon Data Lifecycle Manager resumes deleting snapshots as their retention periods expire\.
+ If you delete the resource to which a policy with count\-based retention applies, the policy no longer manages the previously created snapshots\. You must manually delete the snapshots if they are no longer needed\.
+ If you delete the resource to which a policy with age\-based retention applies, the policy continues to delete snapshots on the defined schedule, up to the last snapshot\. You must manually delete the last snapshot if it is no longer needed\.
+ You can create multiple policies to back up an EBS volume or an EC2 instance\. For example, if an EBS volume has two tags, where tag A is the target for policy A to create a snapshot every 12 hours, and tag B is the target for policy B to create a snapshot every 24 hours, Amazon Data Lifecycle Manager creates snapshots according to the schedules for both policies\.

The following considerations apply to lifecycle policies and [fast snapshot restore](ebs-fast-snapshot-restore.md):
+ A snapshot that is enabled for fast snapshot restore remains enabled even if you delete or disable the lifecycle policy, disable fast snapshot restore for the lifecycle policy, or disable fast snapshot restore for the Availability Zone\. You can disable fast snapshot restore for these snapshots manually\.
+ If you enable fast snapshot restore and you exceed the maximum number of snapshots that can be enabled for fast snapshot restore, Amazon Data Lifecycle Manager creates snapshots as scheduled but does not enable them for fast snapshot restore\. After a snapshot that is enabled for fast snapshot restore is deleted, the next snapshot Amazon Data Lifecycle Manager creates is enabled for fast snapshot restore\.
+ When you enable fast snapshot restore for a snapshot, it takes 60 minutes per TiB to optimize the snapshot\. We recommend that you create a schedule that ensures that each snapshot is fully optimized before Amazon Data Lifecycle Manager creates the next snapshot\.
+ You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\. For more information, see [Pricing and Billing](ebs-fast-snapshot-restore.md#fsr-pricing)\.
**Note**  
Depending on the configuration of your lifecycle policies, you could have multiple snapshots enabled for fast snapshot restore simultaneously\.

The following considerations apply to lifecycle policies and [Multi\-Attach](ebs-volumes-multi.md) enabled volumes:
+ When creating a lifecycle policy based on instance tags for Multi\-Volume snapshots, Amazon Data Lifecycle Manager initiates a snapshot of the volume for each attached instance\. Use the *timestamp* tag to identify the set of time\-consistent snapshots created from the attached instances\.

## Prerequisites<a name="dlm-prerequisites"></a>

The following prerequisites are required by Amazon Data Lifecycle Manager\.

**Topics**
+ [Permissions for Amazon Data Lifecycle Manager](#dlm-permissions)
+ [Permissions for IAM users](#dlm-access-control)
+ [Permissions for encrypted snapshots](#dlm-access-cmk)

### Permissions for Amazon Data Lifecycle Manager<a name="dlm-permissions"></a>

Amazon Data Lifecycle Manager uses an IAM role to get the permissions that are required to manage snapshots on your behalf\. Amazon Data Lifecycle Manager creates the **AWSDataLifecycleManagerDefaultRole** role the first time that you create a lifecycle policy using the AWS Management Console\. You can also create this role using the following [create\-default\-role](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-default-role.html) command\.

```
aws dlm create-default-role
```

Alternatively, you can create a custom IAM role with the required permissions and select it when you create a lifecycle policy\. 

**To create a custom IAM role**

1. Create a role with the following permissions\.

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
         }
      ]
   }
   ```

   For more information, see [Creating a Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.

1. Add a trust relationship to the role\.

   1. In the IAM console, choose **Roles**\.

   1. Select the role you created and then choose **Trust relationships**\.

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
            } 
         ] 
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
      }
   ]
}
```

For more information, see [Changing Permissions for an IAM User](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html) in the *IAM User Guide*\.

### Permissions for encrypted snapshots<a name="dlm-access-cmk"></a>

To copy an encrypted snapshot between Regions, you must have access to both the source and destination customer master key \(CMK\) from AWS Key Management Service \(AWS KMS\)\. 

If the source volume is encrypted, ensure that the **AWSDataLifecycleManagerDefaultRole** role has permission to use the CMK used to encrypt the volume\. If you enable **Cross Region copy** and choose to encrypt the copied snapshot, ensure that the **AWSDataLifecycleManagerDefaultRole** role has permission to use the CMK needed to encrypt the snapshot in the destination Region\. For more information, see [ Managing access to AWS KMS CMKs](https://docs.aws.amazon.com/kms/latest/developerguide/control-access-overview.html#managing-access) in the *AWS Key Management Service Developer Guide*\.

## Manage backups using the console<a name="snapshot-lifecycle-console"></a>

The following examples show how to use Amazon Data Lifecycle Manager to manage the backups of your EBS volumes using the AWS Management Console\.

**Topics**
+ [Create a lifecycle policy](#console-create-policy)
+ [View a lifecycle policy](#console-view-policy)
+ [Modify a lifecycle policy](#console-modify-policy)
+ [Delete a lifecycle policy](#console-delete-policy)

### Create a lifecycle policy<a name="console-create-policy"></a>

Use the following procedure to create a lifecycle policy\.

**To create a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, then choose **Create snapshot lifecycle policy**\.

1. Provide the following information for your policy as needed:
   + **Description**–A description of the policy\.
   + **Resource type**–The type of resource to back up\. Use `Volume` to create snapshots of individual volumes or use `Instance` to create multi\-volume snapshots from the volumes for an instance\.
   + **Target with these tags**–The resource tags that identify the volumes or instances to back up\.
   + **Lifecycle policy tags**–The tags for the lifecycle policy\.
   + **Schedule name**–A name for the schedule\.
   + **Frequency**–The interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to 1 year\. For more information, see [Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.
   + **Starting at ***hh*:*mm* **UTC**–The time at which the policy runs are scheduled to start\. The first policy run starts within an hour after the scheduled time\.
   + **Retention type**–You can retain snapshots based on either the total count of snapshots or the age of each snapshot\. For retention based on the count, the range is 1 to 1000\. After the maximum count is reached, the oldest snapshot is deleted when a new one is created\. For age\-based retention, the range is 1 day to 100 years\. After the retention period of each snapshot expires, it is deleted\. The retention period should be greater than or equal to the creation interval\.
   + **Tagging information**–Choose whether to copy all user\-defined tags on a source volume to the snapshots created by this policy\. You can also specify additional tags for the snapshots in addition to the tags applied by Amazon Data Lifecycle Manager\. If the resource type is `INSTANCE`, you can choose to automatically tag your snapshots with the following variable tags: `instance-id` and `timestamp`\. The values of the variable tags are determined when the tags are added\.
   + **Fast snapshot restore**–Choose whether to enable fast snapshot restore for all snapshots created by this policy\. If you enable fast snapshot restore, you must choose the Availability Zones in which to enable it\. You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\. You can also specify the maximum number of snapshots that can be enabled for fast snapshot restore\.
   + **Enable cross Region copy**–You can copy each snapshot to up at three additional Regions\. You must ensure that you do not exceed the number of concurrent snapshot copies per Region\. For each Region, you can choose different retention policies and whether to copy all tags or no tags\. If the source snapshot is encrypted or if encryption by default is enabled, the snapshots copies are encrypted\. If the source snapshot is unencrypted, you can enable encryption\. If you do not specify a CMK, the snapshots are encrypted using the default key for EBS encryption in each destination Region\. If you specify a CMK for the destination Region, you must have access to the CMK\.
   + **IAM role**–An IAM role that has permissions to create, delete, and describe snapshots, and to describe volumes\. AWS provides a default role, **AWSDataLifecycleManagerDefaultRole**, or you can create a custom IAM role\.
   + **Policy status after creation**–Choose **Enable policy** to start the policy runs at the next scheduled time or **Disable policy** to prevent the policy from running\.

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

1. Choose **Actions**, **Modify Snapshot Lifecycle Policy**\.

1. Modify the policy settings as needed\. For example, you can modify the schedule, add or remove tags, or enable or disable the policy\.

1. Choose **Update policy**\.

### Delete a lifecycle policy<a name="console-delete-policy"></a>

Use the following procedure to delete a lifecycle policy\.

**To delete a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\.

1. Choose **Actions**, **Delete Snapshot Lifecycle Policy**\.

1. When prompted for confirmation, choose **Delete Snapshot Lifecycle Policy**\.

## Manage backups using the AWS CLI<a name="snaphot-lifecycle-cli"></a>

The following examples show how to use Amazon Data Lifecycle Manager to manage the backups of your EBS volumes using the AWS CLI\.

**Topics**
+ [Create a lifecycle policy](#create-policy-cli)
+ [Display a lifecycle policy](#display-policy-cli)
+ [Modify a lifecycle policy](#modify-policy-cli)
+ [Delete a lifecycle policy](#delete-policy-cli)

### Create a lifecycle policy<a name="create-policy-cli"></a>

Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create a lifecycle policy\. To simplify the syntax, the example uses a JSON file, `policyDetails.json`, that includes the policy details\.

This example uses a resource type of `VOLUME` to create snapshots of all volumes with the specified target tags\. To create snapshots of all volumes for all instances with the specified target tags, use a resource type of `INSTANCE` instead\.

```
aws dlm create-lifecycle-policy --description "My volume policy" --state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole --policy-details file://policyDetails.json
```

The following is an example of the `policyDetails.json` file\.

```
{
   "ResourceTypes": [
      "VOLUME"
   ],
   "TargetTags": [
      {
         "Key": "costcenter",
         "Value": "115"
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
      }
   ]
}
```

Upon success, the command returns the ID of the newly created policy\. The following is example output\.

```
{
   "PolicyId": "policy-0123456789abcdef0"
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

```
aws dlm delete-lifecycle-policy --policy-id policy-0123456789abcdef0
```

## Manage backups using the API<a name="snaphot-lifecycle-api"></a>

The [Amazon Data Lifecycle Manager API Reference](https://docs.aws.amazon.com/dlm/latest/APIReference/) provides descriptions and syntax for each of the actions and data types for the Amazon Data Lifecycle Manager Query API\.

Alternatively, you can use one of the AWS SDKs to access the API in a way that's tailored to the programming language or platform that you're using\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.

## Monitor the snapshot lifecycle<a name="dlm-monitor-lifecycle"></a>

You can use the following features to monitor the lifecycle of your snapshots\.

**Topics**
+ [Console and AWS CLI](#monitor-console-cli)
+ [CloudWatch Events](#monitor-cloudwatch-events)
+ [AWS CloudTrail](#monitor-lifecycle-cloudtrail)

### Console and AWS CLI<a name="monitor-console-cli"></a>

You can view your lifecycle policies using the Amazon EC2 console or the AWS CLI\. Each snapshot created by a policy has a timestamp and policy\-related tags\. You can filter snapshots using tags to verify that your backups are being created as you intend\. For information about viewing lifecycle policies using the console, see [View a lifecycle policy](#console-view-policy)\. For information about displaying information about lifecycle policies using the CLI, see [Display a lifecycle policy](#display-policy-cli)\.

### CloudWatch Events<a name="monitor-cloudwatch-events"></a>

Amazon EBS and Amazon Data Lifecycle Manager emit events related to lifecycle policy actions\. You can use AWS Lambda and Amazon CloudWatch Events to handle event notifications programmatically\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following events are available:
+ `createSnapshot`—An Amazon EBS event emitted when a `CreateSnapshot` action succeeds or fails\. For more information, see [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.
+ `DLM Policy State Change`—A Amazon Data Lifecycle Manager event emitted when a lifecycle policy enters an error state\. The event contains a description of what caused the error\. The following is an example of an event when the permissions granted by the IAM role are insufficient:

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
     "detail": {
        "state": "ERROR",
        "cause": "Role provided does not have sufficient permissions",
        "policy_id": "arn:aws:dlm:us-east-1:123456789012:policy/policy-0123456789abcdef"
     }
  }
  ```

  The following is an example of an event when a limit is exceeded:

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
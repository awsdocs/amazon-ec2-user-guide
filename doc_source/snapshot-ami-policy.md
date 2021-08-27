# Automate snapshot lifecycles<a name="snapshot-ami-policy"></a>

The following procedure shows you how to use Amazon Data Lifecycle Manager to automate Amazon EBS snapshot lifecycles\.

Use one of the following procedures to create a snapshot lifecycle policy\.

------
#### [ New console ]

**To create a snapshot policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, and then choose **Create lifecycle policy**\.

1. On the **Select policy type** screen, choose **EBS snapshot policy** and then choose **Next**\.

1. In the **Target resources** section, do the following:

   1. For **Target resource types**, choose the type of resource to back up\. Choose `Volume` to create snapshots of individual volumes, or choose `Instance` to create multi\-volume snapshots from the volumes attached to an instance\.

   1. \(For AWS Outpost customers only\) For **Target resource location**, specify where the source resources are located\.
      + If the source resources are located in an AWS Region, choose **AWS Region**\. Amazon Data Lifecycle Manager backs up all resources of the specified type that have matching target tags in the current Region only\. If the resource is located in a Region, snapshots created by the policy will be stored in the same Region\. 
      + If the source resources are located on an Outpost in your account, choose **AWS Outpost**\. Amazon Data Lifecycle Manager backs up all resources of the specified type that have matching target tags across all of the Outposts in your account\. If the resource is located on an Outpost, snapshots created by the policy can be stored in the same Region or on the same Outpost as the resource\.
      + If you do not have any Outposts in your account, this option is hidden and AWS Region is selected for you\.

   1. For **Target resource tags**, choose the resource tags that identify the volumes or instances to back up\. Only resources that have the specified tag key and value pairs are backed up by the policy\.

1. For **Description**, enter a brief description for the policy\.

1. For **IAM role**, choose the IAM role that has permissions to manage snapshots and to describe volumes and instances\. To use the default role provided by Amazon Data Lifecycle Manager\. choose **Default role**\. Alternatively, to use a custom IAM role that you previously created, choose **Choose another role** and then select the role to use\.

1. For **Policy tags**, add the tags to apply to the lifecycle policy\. You can use these tags to identify and categorize your policies\.

1. For **Policy status after creation**, choose **Enable policy** to start the policy runs at the next scheduled time, or **Disable policy** to prevent the policy from running\. If you do not enable the policy now, it will not start creating snapshots until you manually enable it after creation\.

1. Choose **Next**\.

1. On the **Configure schedule** screen, configure the policy schedules\. A policy can have up to 4 schedules\. Schedule 1 is mandatory\. Schedules 2, 3, and 4 are optional\. For each policy schedule that you add, do the following:

   1. In the **Schedule details** section do the following:

      1. For **Schedule name**, specify a descriptive name for the schedule\.

      1. For **Frequency** and the related fields, configure the interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to one year\. For more information, see [Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.

      1. For **Starting at**, specify the time at which the policy runs are scheduled to start\. The first policy run starts within an hour after the scheduled time\. The time must be entered in the `hh:mm` UTC format\.

      1. For **Retention type**, specify the retention policy for snapshots created by the schedule\. You can retain snapshots based on either their total count or their age\.

         For count\-based retention, the range is `1` to `1000`\. After the maximum count is reached, the oldest snapshot is deleted when a new one is created\.

         For age\-based retention, the range is `1` day to `100` years\. After the retention period of each snapshot expires, it is deleted\.
**Note**  
All schedules must have the same retention type\. You can specify the retention type for Schedule 1 only\. Schedules 2, 3, and 4 inherit the retention type from Schedule 1\. Each schedule can have its own retention count or period\.

      1. \(For AWS Outposts customers only\) For **Snapshot destination**, specify the destination for snapshots created by the policy\.
         + If the policy targets resources in a Region, snapshots must be created in the same Region\. AWS Region is selected for you\.
         + If the policy targets resources on an Outpost, you can choose to create snapshots on the same Outpost as the source resource, or in the Region that is associated with the Outpost\.
         + If you do not have any Outposts in your account, this option is hidden and AWS Region is selected for you\.

          

   1. In the **Tagging** section, do the following:

      1. To copy all of the user\-defined tags from the source volume to the snapshots created by the schedule, select **Copy tags from source**\.

      1. To specify additional tags to assign to snapshots created by this schedule, choose **Add tags**\.

   1. To enable fast snapshot restore for snapshots created by the schedule, in the **Fast snapshot restore** section, select **Enable fast snapshot restore**\. If you enable fast snapshot restore, you must choose the Availability Zones in which to enable it\. If the schedule uses an age\-based retention schedule, you must specify the period for which to enable fast snapshot restore for each snapshot\. If the schedule uses count\-based retention, you must specify the maximum number of snapshots to enable for fast snapshot restore\.

      If the schedule creates snapshots on an Outpost, you can't enable fast snapshot restore\. Fast snapshot restore is not supported with local snapshots that are stored on an Outpost\.
**Note**  
You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\.

   1. To copy snapshots created by the schedule to an Outpost or to a different Region, in the **Cross\-Region copy** section, select **Enable cross\-Region copy**\.

      If the schedule creates snapshots in a Region, you can copy the snapshots to up to three additional Regions or Outposts in your account\. You must specify a separate cross\-Region copy rule for each destination Region or Outpost\.

      For each Region or Outpost, you can choose different retention policies and you can choose whether to copy all tags or no tags\. If the source snapshot is encrypted, or if encryption by default is enabled, the copied snapshots are encrypted\. If the source snapshot is unencrypted, you can enable encryption\. If you do not specify a KMS key, the snapshots are encrypted using the default KMS key for EBS encryption in each destination Region\. If you specify a KMS key for the destination Region, then the selected IAM role must have access to the KMS key\.
**Note**  
You must ensure that you do not exceed the number of concurrent snapshot copies per Region\.

       If the policy creates snapshots on an Outpost, then you can't copy the snapshots to a Region or to another Outpost and the cross\-Region copy settings are not available\.

   1. In the **Cross\-account sharing**, configure the policy to automatically share the snapshots created by the schedule with other AWS accounts\. Do the following:

      1. To enable sharing with other AWS accounts, select **Enable cross\-account sharing**\.

      1. To add the accounts with which to share the snapshots, choose **Add account**, enter the 12\-digit AWS account ID, and choose **Add**\.

      1. To automatically unshare shared snapshots after a specific period, select **Unshare automatically**\. If you choose to automatically unshare shared snapshots, the period after which to automatically unshare the snapshots cannot be longer than the period for which the policy retains its snapshots\. For example, if the policy's retention configuration retains snapshots for a period of 5 days, you can configure the policy to automatically unshare shared snapshots after periods up to 4 days\. This applies to policies with age\-based and count\-based snapshot retention configurations\.

         If you do not enable automatic unsharing, the snapshot is shared until it is deleted\.

   1. To add additional schedules, choose **Add another schedule**, which is located at the top of the screen\. For each additional schedule, complete the fields as described previously in this topic\.

   1. After you have added the required schedules, choose **Review policy**\.

1. Review the policy summary, and then choose **Create policy**\.

------
#### [ Old console ]

**To create a snapshot policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, and then choose **Create lifecycle policy**\.

1. Provide the following information for your policy as needed:
   + **Description**—A description of the policy\.
   + **Policy type**—The type of policy to create\. Choose **EBS snapshot policy**\.
   + **Resource type**—The type of resource to back up\. Choose `Volume` to create snapshots of individual volumes, or choose `Instance` to create multi\-volume snapshots from the volumes attached to an instance\.
   + **Resource location**— The location of the resources to backup\. If the source resources are located in an AWS Region, choose **AWS Region**\. If the source resources are located on an Outpost in your account, choose **AWS Outpost**\. If you choose AWS Outpost, Amazon Data Lifecycle Manager backs up all resources of the specified type that have matching target tags across all of the Outposts in your account\.

     If you do not have any Outposts in your account, then **AWS Region** is selected by default\.
**Note**  
If the resource is located in a Region, snapshots created by the policy will be stored in the same Region\. If the resource is located on an Outpost, snapshots created by the policy can be stored in the same Region or on the same Outpost as the resource\.
   + **Target with these tags**—The resource tags that identify the volumes or instances to back up\. Only resources that have the specified tag key and value pairs are backed up by the policy\.
   + **Policy tags**—The tags to apply to the lifecycle policy\.

1. For **IAM role**, choose the IAM role that has permissions to create, delete, and describe snapshots and to describe volumes and instances\. AWS provides a default role, or you can create a custom IAM role\.

1. Add the policy schedules\. Schedule 1 is mandatory\. Schedules 2, 3, and 4 are optional\. For each policy schedule that you add, specify the following information:
   + **Schedule name**—A name for the schedule\.
   + **Frequency**—The interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to one year\. For more information, see [ Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.
   + **Starting at ***hh*:*mm* **UTC**—The time at which the policy runs are scheduled to start\. The first policy run starts within an hour after the scheduled time\.
   + **Retention type**—You can retain snapshots based on either their total count or their age\. For count\-based retention, the range is 1 to 1000\. After the maximum count is reached, the oldest snapshot is deleted when a new one is created\. For age\-based retention, the range is 1 day to 100 years\. After the retention period of each snapshot expires, it is deleted\. The retention period should be greater than or equal to the interval\.
**Note**  
All schedules must have the same retention type\. You can specify the retention type for Schedule 1 only\. Schedules 2, 3, and 4 inherit the retention type from Schedule 1\. Each schedule can have its own retention count or period\.
   + **Snapshot destination**—Specifies the destination for snapshots created by the policy\. To create snapshots in the same AWS Region as the source resource, choose **AWS Region**\. To create snapshots on an Outpost, choose **AWS Outpost**\.

     If the policy targets resources in a Region, snapshots are created in the same Region, and cannot be created on an Outpost\.

     If the policy targets resources on an Outpost, snapshots can be created on the same Outpost as the source resource, or in the Region that is associated with the Outpost\.
   + **Copy tags from source**—Choose whether to copy all of the user\-defined tags from the source volume to the snapshots created by the schedule\.
   + **Variable tags**—If the source resource is an instance, you can choose to automatically tag your snapshots with the following variable tags:
     + `instance-id`—The ID of the source instance\.
     + `timestamp`—The date and time of the policy run\.
   + **Additional tags**—Specify any additional tags to assign to the snapshots created by this schedule\.
   + **Fast snapshot restore**—Choose whether to enable fast snapshot restore for all snapshots that are created by the schedule\. If you enable fast snapshot restore, you must choose the Availability Zones in which to enable it\. You are billed for each minute that fast snapshot restore is enabled for a snapshot in a particular Availability Zone\. Charges are pro\-rated with a minimum of one hour\. You can also specify the maximum number of snapshots that can be enabled for fast snapshot restore\.

     If the policy creates snapshots on an Outpost, you can't enable fast snapshot restore\. Fast snapshot restore is not supported with local snapshots that are stored on an Outpost\.
   + **Cross region copy**—If the policy creates snapshots in a Region, then you can copy the snapshots to up to three additional Regions or Outposts in your account\. You must specify a separate cross\-Region copy rule for each destination Region or Outpost\.

     For each Region or Outpost, you can choose different retention policies and you can choose whether to copy all tags or no tags\. If the source snapshot is encrypted, or if encryption by default is enabled, the copied snapshots are encrypted\. If the source snapshot is unencrypted, you can enable encryption\. If you do not specify a KMS key, the snapshots are encrypted using the default KMS key for EBS encryption in each destination Region\. If you specify a KMS key for the destination Region, then the selected IAM role must have access to the KMS key\.

     You must ensure that you do not exceed the number of concurrent snapshot copies per Region\.

      If the policy creates snapshots on an Outpost, then you can't copy the snapshots to a Region or to another Outpost and the cross\-Region copy settings are not available\.

1. For **Policy status after creation**, choose **Enable policy** to start the policy runs at the next scheduled time, or **Disable policy** to prevent the policy from running\.

1. Choose **Create Policy**\.

------
#### [ Command line ]

Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create a snapshot lifecycle policy\. For `PolicyType`, specify `EBS_SNAPSHOT_MANAGEMENT`\.

**Note**  
To simplify the syntax, the following examples use a JSON file, `policyDetails.json`, that includes the policy details\.

**Example 1—Snapshot lifecycle policy**  
This example creates a snapshot lifecycle policy that creates snapshots of all volumes that have a tag key of `costcenter` with a value of `115`\. The policy includes two schedules\. The first schedule creates a snapshot every day at 03:00 UTC\. The second schedule creates a weekly snapshot every Friday at 17:00 UTC\.

```
aws dlm create-lifecycle-policy \
--description "My volume policy" \
--state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole \
--policy-details file://policyDetails.json
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
            "CronExpression": "cron(0 17 ? * FRI *)"
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

**Example 2—Snapshot lifecycle policy that automates local snapshots of Outpost resources**  
This example creates a snapshot lifecycle policy that creates snapshots of volumes tagged with `team=dev` across all of your Outposts\. The policy creates the snapshots on the same Outposts as the source volumes\. The policy creates snapshots every `12` hours starting at `00:00` UTC\.

```
aws dlm create-lifecycle-policy \
--description "My local snapshot policy" \
--state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole \
--policy-details file://policyDetails.json
```

The following is an example of the `policyDetails.json` file\.

```
{
    "PolicyType": "EBS_SNAPSHOT_MANAGEMENT",
    "ResourceTypes": "VOLUME",
	"ResourceLocations": "OUTPOST",
    "TargetTags": [{
        "Key": "team",
        "Value": "dev"
    }],
    "Schedules": [{
        "Name": "on-site backup",
        "CreateRule": {
            "Interval": 12,
            "IntervalUnit": "HOURS",
            "Times": [
                "00:00"
            ],
	"Location": [
		"OUTPOST_LOCAL"
	]
        },
        "RetainRule": {
            "Count": 1
        },
        "CopyTags": false
    }
]}
```

**Example 3—Snapshot lifecycle policy that creates snapshots in a Region and copies them to an Outpost**  
The following example policy creates snapshots of volumes that are tagged with `team=dev`\. Snapshots are created in the same Region as the source volume\. Snapshots are created every `12` hours starting at `00:00` UTC, and retains a maximum of `1` snapshot\. The policy also copies the snapshots to Outpost `arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0`, encrypts the copied snapshots using the default encryption KMS key, and retains the copies for `1` month\.

```
aws dlm create-lifecycle-policy \
--description "Copy snapshots to Outpost" \
--state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole \
--policy-details file://policyDetails.json
```

The following is an example of the `policyDetails.json` file\.

```
{
    "PolicyType": "EBS_SNAPSHOT_MANAGEMENT",
    "ResourceTypes": "VOLUME",
    "ResourceLocations": "CLOUD",
    "TargetTags": [{
        "Key": "team",
        "Value": "dev"
    }],
    "Schedules": [{
        "Name": "on-site backup",
        "CopyTags": false,
        "CreateRule": {
            "Interval": 12,
            "IntervalUnit": "HOURS",
            "Times": [
                "00:00"
            ],
            "Location": "CLOUD"
        },
        "RetainRule": {
            "Count": 1
        },
        "CrossRegionCopyRules" : [
        {
            "Target": "arn:aws:outposts:us-east-1:123456789012:outpost/op-1234567890abcdef0",
            "Encrypted": true,
            "CopyTags": true,
            "RetainRule": {
                "Interval": 1,
                "IntervalUnit": "MONTHS"
            }
        }]
    }
]}
```

------
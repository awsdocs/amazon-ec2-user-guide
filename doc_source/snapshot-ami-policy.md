# Automate snapshot and EBS\-backed AMI lifecycles<a name="snapshot-ami-policy"></a>

The following procedures show how to use Amazon Data Lifecycle Manager to manage the backups of your EBS volumes and Amazon EC2 instances\.

Use one of the following procedure to create a lifecycle policy\.

------
#### [ Console ]

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

------
#### [ Command line ]

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

------
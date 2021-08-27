# Automate AMI lifecycles<a name="ami-policy"></a>

The following procedure shows you how to use Amazon Data Lifecycle Manager to automate EBS\-backed AMI lifecycles\.

Use one of the following procedures to create an AMI lifecycle policy\.

------
#### [ New console ]

**To create an AMI policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, and then choose **Create lifecycle policy**\.

1. On the **Select policy type** screen, choose **EBS\-backed AMI policy**, and then choose **Next**\.

1. In the **Target resources** section, for **Target resource tags**, choose the resource tags that identify the volumes or instances to back up\. The policy backs up only the resources that have the specified tag key and value pairs\.

1. For **Description**, enter a brief description for the policy\.

1. For **IAM role**, choose the IAM role that has permissions to manage AMIs and snapshot and to describe instances\. To use the default role provided by Amazon Data Lifecycle Manager, choose **Default role**\. Alternatively, to use a custom IAM role that you previously created, choose **Choose another role**, and then select the role to use\.

1. For **Policy tags**, add the tags to apply to the lifecycle policy\. You can use these tags to identify and categorize your policies\.

1. For **Policy status after creation**, choose **Enable policy** to start running the policy at the next scheduled time, or **Disable policy** to prevent the policy from running\. If you do not enable the policy now, it will not start creating AMIs until you manually enable it after creation\.

1. In the **Instance reboot** section, indicate whether instances should be rebooted before AMI creation\. To prevent the targeted instances from being rebooted, choose **No**\. Choosing **No** could cause data consistency issues\. To reboot instances before AMI creation, choose **Yes**\. Choosing this ensures data consistency, but could result in multiple targeted instances rebooting simultaneously\.

1. Choose **Next**\.

1. On the **Configure schedule** screen, configure the policy schedules\. A policy can have up to four schedules\. Schedule 1 is mandatory\. Schedules 2, 3, and 4 are optional\. For each policy schedule that you add, do the following:

   1. In the **Schedule details** section do the following:

      1. For **Schedule name**, specify a descriptive name for the schedule\.

      1. For **Frequency** and the related fields, configure the interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to one year\. For more information, see [Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.

      1. For **Starting at**, specify the time to start the policy runs\. The first policy run starts within an hour after the time that you schedule\. You must enter the time in the `hh:mm` UTC format\.

      1. For **Retention type**, specify the retention policy for AMIs created by the schedule\. You can retain AMIs based on either their total count or their age\.

         For count\-based retention, the range is `1` to `1000`\. After the maximum count is reached, the oldest AMI is deregistered when a new one is created\.

         For age\-based retention, the range is `1` day to `100` years\. After the retention period of each AMI expires, it is deregistered\.
**Note**  
All schedules must have the same retention type\. You can specify the retention type for Schedule 1 only\. Schedules 2, 3, and 4 inherit the retention type from Schedule 1\. Each schedule can have its own retention count or period\.

   1. In the **Tagging** section, do the following:

      1. To copy all of the user\-defined tags from the source instance to the AMIs created by the schedule, select **Copy tags from source**\.

      1. By default, AMIs created by the schedule are automatically tagged with the ID of the source instance\. To prevent this automatic tagging from happening, for **Variable tags**, remove the `instance-id:$(instance-id)` tile\.

      1. To specify additional tags to assign to AMIs created by this schedule, choose **Add tags**\.

   1. To deprecate AMIs when they should no longer be used, in the **AMI deprecation** section, select **Enable AMI deprecation for this schedule** and then specify the AMI deprecation rule\. The AMI deprecation rule specifies when AMIs are to be deprecated\.

      If the schedule uses count\-based AMI retention, you must specify the number of oldest AMIs to deprecate\. The deprecation count must be less than or equal to the schedule's AMI retention count, and it can't be greater than 1000\. For example, if the schedule is configured to retain a maximum of 5 AMIs, then you can configure the scheduled to deprecate up to old 5 oldest AMIs\.

      If the schedule uses age\-based AMI retention, you must specify the period after which AMIs are to be deprecated\. The deprecation count must be less than or equal to the schedule's AMI retention period, and it can't be greater than 10 years \(120 months, 520 weeks, or 3650 days\)\. For example, if the schedule is configured to retain AMIs for 10 days, then you can configure the scheduled to deprecate AMIs after periods up to 10 days after creation\.

   1. To copy AMIs created by the schedule to different Regions, in the **Cross\-Region copy** section, select **Enable cross\-Region copy**\. You can copy AMIs to up to three additional Regions in your account\. You must specify a separate cross\-Region copy rule for each destination Region\.

      For each destination Region, you can specify the following:
      + A retention policy for the AMI copy\. When the retention period expires, the copy in the destination Region is automatically deregistered\.
      + Encryption status for the AMI copy\. If the source AMI is encrypted, or if encryption by default is enabled, the copied AMIs are always encrypted\. If the source AMI is unencrypted and encryption by default is disabled, you can optionally enable encryption\. If you do not specify a KMS key, the AMIs are encrypted using the default KMS key for EBS encryption in each destination Region\. If you specify a KMS key for the destination Region, then the selected IAM role must have access to the KMS key\.
      + A deprecation rule for the AMI copy\. When the deprecation period expires, the AMI copy is automatically deprecated\. The deprecation period must be less than or equal to the copy retention period, and it can't be greater than 10 years\.
      + Whether to copy all tags or no tags from the source AMI\.
**Note**  
Do not exceed the number of concurrent AMI copies per Region\.

   1. To add additional schedules, choose **Add another schedule**, which is located at the top of the screen\. For each additional schedule, complete the fields as described previously in this topic\.

   1. After you have added the required schedules, choose **Review policy**\.

1. Review the policy summary, and then choose **Create policy**\.

------
#### [ Console ]

**To create an AMI lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, and then choose **Create lifecycle policy**\.

1. Provide the following information for your policy as needed:
   + **Description**—A description of the policy\.
   + **Policy type**—The type of policy to create\. Choose **EBS\-backed AMI policy**\.
   + **Target with these tags**—The resource tags that identify the instances to back up\. Only instances that have the specified tag key and value pairs are backed up by the policy\.
   + **Policy tags**—The tags to apply to the lifecycle policy\.

1. For **IAM role**, choose the IAM role that has permissions to manage images\. AWS provides a default roles, or you can create a custom IAM role\.

1. Add the policy schedules\. Schedule 1 is mandatory\. Schedules 2, 3, and 4 are optional\. For each policy schedule that you add, specify the following information:
   + **Schedule name**—A name for the schedule\.
   + **Frequency**—The interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to one year\. For more information, see [ Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\.
   + **Starting at ***hh*:*mm* **UTC**— The time at which the policy runs are scheduled to start\. The first policy run starts within an hour after the scheduled time\.
   + **Retention type**—You can retain AMIs based on either their total count or their age\. For count\-based retention, the range is 1 to 1000\. After the maximum count is reached, the oldest AMI is deleted when a new one is created\. For age\-based retention, the range is 1 day to 100 years\. After the retention period of each AMI expires, it is deleted\. The retention period should be greater than or equal to the interval\.
**Note**  
All schedules must have the same retention type\. You can specify the retention type for Schedule 1 only\. Schedules 2, 3, and 4 inherit the retention type from Schedule 1\. Each schedule can have its own retention count or period\.
   + **Copy tags from source**—Choose whether to copy all of the user\-defined tags from the source instance to the AMIs created by the schedule\. 
   + **Dynamic tags**—You can choose to automatically tag your AMIs with the ID of the source instance\.
   + **Additional tags**—Specify any additional tags to assign to the AMIs created by this schedule\.
   + **Enable cross Region copy**— You can copy AMIs to up to three additional Regions\.

     For each Region, you can choose different retention policies and you can choose whether to copy all tags or no tags\. If the source AMI is encrypted, or if encryption by default is enabled, the copied AMIs are encrypted\. If the AMI is unencrypted, you can enable encryption\. If you do not specify a KMS key, the AMIs are encrypted using the default KMS key for EBS encryption in each destination Region\. If you specify a KMS key for the destination Region, then the selected IAM role must have access to the KMS key\.

     Do not exceed the number of concurrent AMI copies per Region\.

1. Indicate whether instances should be rebooted before AMI creation\. To prevent the targeted instances from being rebooted, for **Reboot Instance at policy run**, choose **No**\. Choosing this option could cause data consistency issues\. To reboot instances before AMI creation, for **Reboot Instance at policy run**, choose **Yes**\. Choosing this ensures data consistency but could result in multiple targeted instances rebooting simultaneously\. 

1. For **Policy status after creation**, choose **Enable policy** to start the policy runs at the next scheduled time, or **Disable policy** to prevent the policy from running\.

1. Choose **Create Policy**\.

------
#### [ Command line ]

Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create an AMI lifecycle policy\. For `PolicyType`, specify `IMAGE_MANAGEMENT`\.

**Note**  
To simplify the syntax, the following examples use a JSON file, `policyDetails.json`, that includes the policy details\.

**Example 1: Age\-based retention and AMI deprecation**  
This example creates an AMI lifecycle policy that creates AMIs of all instances that have a tag key of `purpose` with a value of `production` without rebooting the targeted instances\. The policy includes one schedule that creates an AMI every day at `01:00` UTC\. The policy retains AMIs for `2` days and deprecates them after `1` day\. It also copies the tags from the source instance to the AMIs that it creates\.

```
aws dlm create-lifecycle-policy \
--description "My AMI policy" \
--state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRoleForAMIManagement \
--policy-details file://policyDetails.json
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
            RetainRule":{
                "Interval" : 2,
                "IntervalUnit" : "DAYS"
            },
            DeprecateRule": {
                "Interval" : 1,
                "IntervalUnit" : "DAYS"
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

**Example 2: Count\-based retention and AMI deprecation with cross\-Region copy**  
This example creates an AMI lifecycle policy that creates AMIs of all instances that have a tag key of `purpose` with a value of `production` and reboots the target instances\. The policy includes one schedule that creates an AMI every `6` hours starting at `17:30` UTC\. The policy retains `3` AMIs and automatically deprecates the `2` oldest AMIs\. It also has a cross\-Region copy rule that copies AMIs to `us-east-1`, retains `2` AMI copies, and automatically deprecates the oldest AMI\.

```
aws dlm create-lifecycle-policy \
--description "My AMI policy" \
--state ENABLED \
--execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRoleForAMIManagement \
--policy-details file://policyDetails.json
```

The following is an example of the `policyDetails.json` file\.

```
{
    "PolicyType": "IMAGE_MANAGEMENT",
    "ResourceTypes" : [
        "INSTANCE"
    ],
    "TargetTags": [{
        "Key":"purpose", 
        "Value":"production"
    }],
    "Parameters" : {
          "NoReboot": true
    },
    "Schedules" : [{
        "Name" : "Schedule1",
        "CopyTags": true,
        "CreateRule" : {
            "Interval": 6,
            "IntervalUnit": "HOURS",
            "Times" : ["17:30"]
        },
        "RetainRule":{
            "Count" : 3
        },
        "DeprecateRule":{
            "Count" : 2
        },
        "CrossRegionCopyRules": [{
            "TargetRegion": "us-east-1",
            "Encrypted": true,
            "RetainRule":{
                "IntervalUnit": "DAYS",
                "Interval": 2
            },
            "DeprecateRule":{
                "IntervalUnit": "DAYS",
                "Interval": 1
            },
            "CopyTags": true
        }]
    }]
}
```

------
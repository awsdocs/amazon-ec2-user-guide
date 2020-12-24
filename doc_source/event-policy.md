# Automating cross\-account snapshot copies<a name="event-policy"></a>

Automating cross\-account snapshot copies enables you to copy your Amazon EBS snapshots to specific Regions in an isolated account and encrypt those snapshots with an encryption key\. This enables you to protect yourself against data loss in the event of your account being compromised\.

Automating cross\-account snapshots copies involves two accounts:
+ **Source account**—The source account is the account that creates and shares the snapshots with the target account\. In this account, you must create an EBS snapshot policy that creates snapshots at set intervals and then shares them with other AWS accounts\.
+ **Target account**—The target account is the account with destination account with which the snapshots are shared, and it is the account that creates copies of the shared snapshots\. In this account, you must create a cross\-account copy event policy that automatically copies snapshots that are shared with it by one or more specified source accounts\.

**Topics**
+ [Creating cross\-account snapshot copy policies](#create-cac-policy)
+ [Specifying snapshot description filters](#snapshot-descr-filters)

## Creating cross\-account snapshot copy policies<a name="create-cac-policy"></a>

To prepare the source and target accounts for cross\-account snapshot copying, you need to perform the following steps:

**Topics**
+ [Step 1: Create the EBS snapshot policy \(*Source account*\)](#create-snapshot-policy)
+ [Step 2: Share the customer\-managed CMK \(*Source account*\)](#share-cmk)
+ [Step 3: Create cross\-account copy event policy \(*Target account*\)](#cac-policy)
+ [Step 4: Allow IAM role to use the required CMKs \(*Target account*\)](#target_iam-role)

### Step 1: Create the EBS snapshot policy \(*Source account*\)<a name="create-snapshot-policy"></a>

In the source account, create an EBS snapshot policy that will create the snapshots and share them with the required target accounts\.

Use one of the following methods to create the EBS snapshot policy\.

------
#### [ Console ]

****

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Lifecycle Manager** and then choose **Create Lifecycle Policy**\.

1. For **Policy Type**, choose **EBS snapshot policy**, and for **Resource Type**, choose **Volume**\.

1. For **Target with these tags**, specify the resource tags that identify the volumes from which to create the snapshots\.

1. For **IAM role**, choose the IAM role that has permissions to create, share, delete, and describe snapshots, and to describe volumes\. AWS provides a default role, or you can create a custom IAM role\.

   If you intend to share encrypted snapshots, then you must give this IAM role permission to use CMK used to encrypt the source volume\. For more information, see [Step 2: Share the customer\-managed CMK \(*Source account*\)](#share-cmk)\.

1. Add the policy schedules that define how snapshots are to be created and shared\. Schedule 1 is mandatory\. Schedules 2, 3, and 4 are optional\. For each policy schedule, specify the following information:
   + For **Schedule name**, enter a name for the schedule\.
   + For **Frequency**, specify the interval between policy runs\. You can configure policy runs on a daily, weekly, monthly, or yearly schedule\. Alternatively, choose **Custom cron expression** to specify an interval of up to one year\. For more information, see [Cron expressions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/ScheduledEvents.html#CronExpressions) in the *Amazon CloudWatch Events User Guide*\. 
   + For **Starting at ***hh*:*mm* **UTC**, specify the time at which the policy runs are scheduled to start\. The first policy run starts within an hour after the scheduled time\.
   + For **Retention type**, specify how the snapshots are to be retained\. You can retain snapshots or AMIs based on either their total count or their age\. For count\-based retention, the range is 1 to 1000\. After the maximum count is reached, the oldest snapshot or AMI is deleted when a new one is created\. For age\-based retention, the range is 1 day to 100 years\. After the retention period of each snapshot or AMI expires, it is deleted\. The retention period should be greater than or equal to the creation interval\.
**Note**  
All schedules must have the same retention type\. You can specify the retention type for Schedule 1 only\. Schedules 2, 3, and 4 inherit the retention type from Schedule 1\. Each schedule can have its own retention count or period\.
   + Select **Enable cross\-account sharing**, and then specify the target AWS accounts with which to share the snapshots\. These are the accounts with which the snapshots are to be shared\. If you are sharing encrypted snapshots, then you must give the selected target accounts permission to use the CMK used to encrypt the source volume\. For more information, see [Step 2: Share the customer\-managed CMK \(*Source account*\)](#share-cmk)\.
   + To automatically unshare shared snapshots, select **Unshare automatically**, and then specify when the snapshots are to be unshared\.

     If you chose to automatically unshare shared snapshots, the period after which to automatically unshare the snapshots cannot be longer than the period for which the policy retains its snapshots\. For example, if the policy's retention configuration retains snapshots for a period of 5 days, you can only configure the policy to automatically unshare shared snapshots after periods up to 4 days\. This applies to policies with age\-based and count\-based snapshot retention configurations\.

1. For **Policy status after creation**, choose **Enable policy** to start the policy runs at the next scheduled time\.

1. Choose **Create Policy**\.

------
#### [ Command line ]

Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create an EBS snapshot policy\. To create the snapshot policy, for `PolicyType`, specify `EBS_SNAPSHOT_MANAGEMENT`\.

For example, the following command creates a snapshot lifecycle policy in source account 111111111111\. The policy creates snapshots of all volumes that have a tag key of `inventory_db` with a value of `alpha`\. The policy includes one schedule that creates a snapshot every day at 15:30 UTC and retains the snapshot for two days\. The policy also shares the snapshots with target account 222222222222 and then automatically unshares them after one day\.

```
$ aws dlm create-lifecycle-policy --description "Sharing Policy" --state ENABLED --execution-role-arn arn:aws:iam::111111111111:role/AWSDataLifecycleManagerDefaultRole --policy-details file://policyDetails.json
```

The following shows the contents of the `policyDetails.json` file\.

```
{
    "PolicyType": "EBS_SNAPSHOT_MANAGEMENT",
    "ResourceTypes": [
        "VOLUME"
    ],
    "TargetTags": [{
        "Key": "inventory_db",
        "Value": "alpha"
    }],
    "Schedules": [{
        "Name": "Daily Snapshots",
        "CreateRule": {
            "Interval": 24,
            "IntervalUnit": "HOURS",
            "Times": [
                "15:30"
            ]
        },
        "RetainRule": {
            "Interval": 2,
            "IntervalUnit": "DAYS"
        },
        "ShareRules": [{
            "TargetAccounts": ["222222222222"],
            "UnshareInterval": 1,
            "UnshareIntervalUnit": "DAYS"
        }],
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

------

### Step 2: Share the customer\-managed CMK \(*Source account*\)<a name="share-cmk"></a>

If you are sharing encrypted snapshots, you must grant the IAM role and the target AWS accounts \(that you selected in the previous step\) permissions to use the customer\-managed customer master key \(CMK\) that was used to encrypt the source volume\.

**Note**  
Only perform this step if you are sharing encrypted snapshots\. If you are sharing unencrypted snapshots, skip this step\.

Use one of the following methods to update the key policy of the CMK\.

------
#### [ Console ]

****

1. Open the AWS KMS console at [https://console\.aws\.amazon\.com/kms](https://console.aws.amazon.com/kms)\.

1. To change the AWS Region, use the Region selector in the upper\-right corner of the page\.

1. In the navigation pane, choose **Customer managed keys** and then select the CMK that you need to share with the target accounts\.

   Make note of the CMK's ARN, you'll need this later\.

1. On the **Key policy** tab, scroll down to the **Key users** section\. Choose **Add**, enter the name of the IAM role that you selected in the previous step, and then choose **Add**\.

1. On the **Key policy** tab, scroll down to the **Other AWS accounts** section\. Choose **Add other AWS accounts**, and then add all of the target AWS accounts that you chose to share the snapshots with in the previous step\.

1. Choose **Save changes**\.

------
#### [ Command line ]

Use the [ get\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/get-key-policy.html) command to rerieve the key policy that is currently attached to the CMK\.

For example, the following command retrieves the key policy for a CMK with an ID of `9d5e2b3d-e410-4a27-a958-19e220d83a1e` and writes it to a file named `snapshotKey.json`\.

```
$ aws kms get-key-policy --policy-name default --key-id 9d5e2b3d-e410-4a27-a958-19e220d83a1e --query Policy --output text > snapshotKey.json
```

Open the key policy using your preferred text editor\. Add the ARN of the IAM role that you specified when you created the snapshot policy and the ARNs of the target accounts with which to share the CMK\.

For example, in the following policy, we added the ARN of the default IAM role, and the ARN of the root account for target account `222222222222.`

```
{
    "Sid" : "Allow use of the key",
    "Effect" : "Allow",
    "Principal" : {
        "AWS" : [
            "arn:aws:iam::111111111111:role/service-role/AWSDataLifecycleManagerDefaultRole",
            "arn:aws:iam::222222222222:root"
        ]
    },
    "Action" : [ 
        "kms:Encrypt", 
        "kms:Decrypt", 
        "kms:ReEncrypt*", 
        "kms:GenerateDataKey*", 
        "kms:DescribeKey" 
    ],
    "Resource" : "*"
}, 
{
    "Sid" : "Allow attachment of persistent resources",
    "Effect" : "Allow",
    "Principal" : {
        "AWS" : [
            "arn:aws:iam::111111111111:role/service-role/AWSDataLifecycleManagerDefaultRole",
            "arn:aws:iam::222222222222:root"
        ]
    },
    "Action" : [ 
        "kms:CreateGrant", 
        "kms:ListGrants", 
        "kms:RevokeGrant"
    ],
    "Resource" : "*",
    "Condition" : {
        "Bool" : {
          "kms:GrantIsForAWSResource" : "true"
        }
    }
}
```

Save and close the file\. Then use the [ put\-key\-policy](https://docs.aws.amazon.com/cli/latest/reference/kms/put-key-policy.html) command to attach the updated key policy to the CMK\. 



```
$ aws kms put-key-policy --policy-name default --key-id 9d5e2b3d-e410-4a27-a958-19e220d83a1e —policy file://snapshotKey.json
```

------

### Step 3: Create cross\-account copy event policy \(*Target account*\)<a name="cac-policy"></a>

In the target account, you must create a cross\-account copy event policy that will automatically copy snapshots that are shared by the required source accounts\.

This policy will only run in the target account when one of the specified source accounts shares snapshot with the account\.

Use one of the following methods to create the cross\-account copy event policy\.

------
#### [ Console ]

****

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Lifecycle Manager** and then choose **Create Lifecycle Policy**\.

1. For **Policy Type**, choose **Cross\-account copy event policy**\. For **Description**, enter a brief description for the policy\.

1. In the **Cross\-account copy event settings** section, for **Copy snapshots shared by**, enter the source AWS accounts from which you want to copy the shared snapshots\.

1. For **Snapshot description filter**, enter the required snapshot description using a regular expression\. Only snapshots that are shared by the specified sources accounts and that have descriptions that match the specified filter are copied by the policy\. For more information, see [Specifying snapshot description filters](#snapshot-descr-filters)\. 

1. For **IAM role**, choose the IAM role that has permissions to perform the snapshot copy action\. AWS provides a default role, or you can create a custom IAM role\.

   If you are copying encrypted snapshots, you must grant the selected IAM role permissions to use the encryption CMK used to encrypt the source volume\. Similarly, if you are encrypting the snapshot in the destination Region using a different CMK, you must grant the IAM role permission to use the destination CMK\. For more information, see [Step 4: Allow IAM role to use the required CMKs \(*Target account*\)](#target_iam-role)\.

1. In the **Copy settings** section, you can configure the policy to copy snapshots to up to three Regions in the target account\. Do the following:

   1. For **Name**, enter a descriptive name for the copy action\.

   1. For **Target Region**, select the Region to which to copy the snapshots\.

   1. For **Retain copy for**, specify how long to retain the snapshot copies in the target Region after creation\.

   1. For **Encryption**, select **Enable** to encrypt the snapshot copy in the target Region\. If the source snapshot is encrypted, or if encryption by default is enabled for your account, the snapshot copy is always encrypted, even if you do enable encryption here\. If the source snapshot is unencrypted and encryption by default is not enabled for your account, you can choose to enable or disable encryption\. If you enable encryption, but do not specify a CMK, the snapshots are encrypted using the default encryption CMK in each destination Region\. If you specify a CMK for the destination Region, you must have access to the CMK\.

   1. \(Optional\) To copy the snapshot to additional Regions, choose **Add additional region**, and then complete the required fields\.

1. For **Policy status after creation**, choose **Enable** policy to start the policy runs at the next scheduled time\.

1. Choose **Create Policy**\.

------
#### [ Command line ]

Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create a policy\. To create a cross\-account copy event policy, for `PolicyType`, specify `EVENT_BASED_POLICY`\.

For example, the following command creates a cross\-account copy event policy in target account `222222222222`\. The policy copies snapshots that are shared by source account `111111111111`\. The policy copies snapshots to `sa-east-1` and `eu-west-2`\. Snapshots copied to `sa-east-1` are unencrypted and they are retained for 3 days\. Snapshots copied to `eu-west-2` are encrypted using CMK `8af79514-350d-4c52-bac8-8985e84171c7` and they are retained for 1 month\. The policy uses the default IAM role\.

```
$ aws dlm create-lifecycle-policy --description "Copy policy" --state ENABLED --execution-role-arn arn:aws:iam::222222222222:role/service-role/AWSDataLifecycleManagerDefaultRole --policy-details file://policyDetails.json
```

The following shows the contents of the `policyDetails.json` file\.

```
{
    "PolicyType" : "EVENT_BASED_POLICY",
    "EventSource" : {
        "Type" : "MANAGED_CWE",
        "Parameters": {
            "EventType" : "shareSnapshot",
            "SnapshotOwner": ["111111111111"]
        }
    },
    "Actions" : [{
        "Name" :"Copy Snapshot to Sao Paulo and London",
        "CrossRegionCopy" : [{
            "Target" : "sa-east-1",
             "EncryptionConfiguration" : {
                 "Encrypted" : false
             },
             "RetainRule" : {
             "Interval" : 3,
            "IntervalUnit" : "DAYS"
            }
        },
        {
            "Target" : "eu-west-2",
            "EncryptionConfiguration" : {
                 "Encrypted" : true,
                 "CmkArn" : "arn:aws:kms:eu-west-2:222222222222:key/8af79514-350d-4c52-bac8-8985e84171c7"
            },
            "RetainRule" : {
                "Interval" : 1,
                "IntervalUnit" : "MONTHS"
            }
        }]
    }]
}
```

Upon success, the command returns the ID of the newly created policy\. The following is example output\.

```
{
    "PolicyId": "policy-9876543210abcdef0"
}
```

------

### Step 4: Allow IAM role to use the required CMKs \(*Target account*\)<a name="target_iam-role"></a>

If you are copying encrypted snapshots, you must grant the IAM role \(that you selected in the previous step\) permissions to use the customer\-managed customer master key \(CMK\) that was used to encrypt the source volume\.

**Note**  
Only perform this step if you are copying encrypted snapshots\. If you are copying unencrypted snapshots, skip this step\.

Use one of the following methods to add the required policies to the IAM role\.

------
#### [ Console ]

****

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, select **Roles**\. Search for and select the IAM role that you selected when you created the cross\-account copy event policy in the previous step\. If you chose to use the default role, the role is named **AWSDataLifecycleManagerDefaultRole**\. 

1. Choose **Add inline policy** and then select the **JSON** tab\.

1. Replace the existing policy with the following, and specify the ARNs of the CMKs\.

   ```
   {
   	    "Version": "2012-10-17",
   	    "Statement": [
   	        {
   	            "Effect": "Allow",
   	            "Action": [
   	                "kms:RevokeGrant",
   	                "kms:CreateGrant",
   	                "kms:ListGrants"
   	            ],
   	            "Resource": [
   	                "arn:aws:kms:region:source_account_id:key/shared_cmk_id",
   	                "arn:aws:kms:region:source_account_id:key/shared_cmk_id"
   	            ],
   	            "Condition": {
   	                "Bool": {
   	                    "kms:GrantIsForAWSResource": "true"
   	                }
   	            }
   	        },
   	        {
   	            "Effect": "Allow",
   	            "Action": [
   	                "kms:Encrypt",
   	                "kms:Decrypt",
   	                "kms:ReEncrypt*",
   	                "kms:GenerateDataKey*",
   	                "kms:DescribeKey"
   	            ],
   	            "Resource": [
   	                "arn:aws:kms:region:source_account_id:key/shared_cmk_id",
   	                "arn:aws:kms:region:source_account_id:key/shared_cmk_id"
   	            ]
   	        }
   	    ]
   	}
   ```

1. Choose **Review policy**

1. For **Name**, enter a descriptive name for the policy, and then choose **Create policy**\.

------
#### [ Command line ]

Using your preferred text editor, create a new JSON file named `policyDetails.json`\. Add the following policy and specify the ARNs of the CMKs that the role needs permissions to use\. In the following example, the policy grants the IAM role permission to use CMK `1234abcd-12ab-34cd-56ef-1234567890ab`, which was shared by source account `111111111111`, and CMK `4567dcba-23ab-34cd-56ef-0987654321yz`, which exists in target account `222222222222`\.

```
 {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kms:RevokeGrant",
                "kms:CreateGrant",
                "kms:ListGrants"
            ],
            "Resource": [
                "arn:aws:kms:sa-east-1:111111111111:key/1234abcd-12ab-34cd-56ef-1234567890ab",
                "arn:aws:kms:eu-west-2:222222222222:key/4567dcba-23ab-34cd-56ef-0987654321yz"
            ],
            "Condition": {
                "Bool": {
                    "kms:GrantIsForAWSResource": "true"
                }
            }
        },
        {
            "Effect": "Allow",
            "Action": [
                "kms:Encrypt",
                "kms:Decrypt",
                "kms:ReEncrypt*",
                "kms:GenerateDataKey*",
                "kms:DescribeKey"
            ],
            "Resource": [
                "arn:aws:kms:sa-east-1:111111111111:key/1234abcd-12ab-34cd-56ef-1234567890ab",
                "arn:aws:kms:eu-west-2:222222222222:key/4567dcba-23ab-34cd-56ef-0987654321yz"
            ]
        }
    ]
}
```

Save and close the file\. Then use the [ put\-role\-policy](https://docs.aws.amazon.com/cli/latest/reference/iam/put-role-policy.html) command to add the policy to the IAM role\.

For example

```
$ aws iam put-role-policy –role-name AWSDataLifecycleManagerDefaultRole --policy-name CopyPolicy --policy-document file://AdminPolicy.json
```

------

## Specifying snapshot description filters<a name="snapshot-descr-filters"></a>

When you create the snapshot copy policy in the target account, you must specify a snapshot description filter\. The snapshot description filter enables you to specify an additional level of filtering that lets you control which snapshots are copied by the policy\. This means that a snapshot is only copied by the policy if it is shared by one of the specified source accounts, and it has a snapshot description that matches the specified filter\. In other words, if a snapshot is shared by one of the specified course accounts, but it does not have a description that matches the specified filter, it is not copied by the policy\.

The snapshot filter description must be specified using a regular expression\. It is a mandatory field when creating cross\-account copy event policies using the console and the command line\. The following are example regular expressions that can be used:
+ `.*`—This filter matches all snapshot descriptions\. If you use this expression the policy will copy all snapshots that are shared by one of the specified source accounts\.
+ `Created for policy: policy-0123456789abcdef0.*`—This filter matches only snapshots that are created by a policy with an ID of `policy-0123456789abcdef0`\. If you use an expression like this, only snapshots that are shared with your account by one of the specified source accounts, and that have been created by a policy with the specified ID are copied by the policy\.
+ `.*production.*`—This filter matches any snapshot that has the word `production` anywhere in its description\. If you use this expression the policy will copy all snapshots that are shared by one of the specified source accounts and that have the specified text in their description\.
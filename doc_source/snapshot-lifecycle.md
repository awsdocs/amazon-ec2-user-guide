# Automating the Amazon EBS Snapshot Lifecycle<a name="snapshot-lifecycle"></a>

You can use Amazon Data Lifecycle Manager \(Amazon DLM\) to automate the creation, retention, and deletion of snapshots taken to back up your Amazon EBS volumes\. Automating snapshot management helps you to:
+ Protect valuable data by enforcing a regular backup schedule\.
+ Retain backups as required by auditors or internal compliance\.
+ Reduce storage costs by deleting outdated backups\.

Combined with the monitoring features of Amazon CloudWatch Events and AWS CloudTrail, Amazon DLM provides a complete backup solution for EBS volumes at no additional cost\.

**Topics**
+ [Understanding Amazon DLM](#dlm-elements)
+ [Permissions for Amazon DLM](#dlm-permissions)
+ [Permissions for IAM Users](#dlm-access-control)
+ [Limits](#dlm-limits)
+ [Working with Amazon DLM Using the Console](#snapshot-lifecycle-console)
+ [Working with Amazon DLM Using the Command Line](#snaphot-lifecycle-cli)
+ [Working with Amazon DLM Using the API](#snaphot-lifecycle-api)
+ [Working with Amazon DLM to Create and Maintain Multi\-Volume Snapshots Using the Console](#multivolume-lifecycle)
+ [Working with Amazon DLM to Create and Maintain Multi\-Volume Snapshots Using the CLI](#snaphots-lifecycle-cli)
+ [Monitoring the Snapshot Lifecycle](#dlm-monitor-lifecycle)

## Understanding Amazon DLM<a name="dlm-elements"></a>

The following are the key elements that you should understand before you get started with Amazon DLM\.

### Snapshots<a name="dlm-ebs-snapshots"></a>

Snapshots are the primary means to back up data from your EBS volumes\. To save storage costs, successive snapshots are incremental, containing only the volume data that changed since the previous snapshot\. When you delete one snapshot in a series of snapshots for a volume, only the data unique to that snapshot is removed\. The rest of the captured history of the volume is preserved\.

For more information, see [Amazon EBS Snapshots](EBSSnapshots.md)\.

### Target Resource Tags<a name="dlm-tagging-volumes"></a>

Amazon DLM uses resource tags to identify the EBS volumes to back up\. Tags are customizable metadata that you can assign to your AWS resources \(including EBS volumes and snapshots\)\. An Amazon DLM policy \(described below\) targets a volume for backup using a single tag\. Multiple tags can be assigned to a volume if you want to run multiple policies on it\.

You can't use a '\\' or '=' character in a tag key\.

For more information, see [Tagging Your Amazon EC2 Resources](Using_Tags.md)\.

### Snapshot Tags<a name="dlm-tagging-snapshots"></a>

Amazon DLM applies the following tags to all snapshots created by a policy, to distinguish them from snapshots created by any other means:
+ `aws:dlm:lifecycle-policy-id`
+ `aws:dlm:lifecycle-schedule-name`

You can also specify custom tags to be applied to snapshots on creation\.

You can't use a '\\' or '=' character in a tag key\.

The target tags that Amazon DLM uses to associate volumes with a policy can optionally be applied to snapshots created by the policy\.

### Lifecycle Policies<a name="dlm-lifecycle-policies"></a>

A lifecycle policy consists of these core settings:
+ Policy type—Defines valid target resource types and actions a policy can manage\. Defaults to `EBS_SNAPSHOT_MANAGEMENT` if not present\. 
+ Resource type—The AWS resource managed by the policy\. Supported values are EBS volumes and EC2 instances\.
+ Target tag—The tag that must be associated with an EBS volume or an EC2 instance for it to be managed by the policy\.
+ Schedule—Defines how often to create snapshots and the maximum number of snapshots to keep\. Snapshot creation starts within an hour of the specified start time\. If creating a new snapshot exceeds the maximum number of snapshots to keep for the volume, the oldest snapshot is deleted\.

The following considerations apply to lifecycle policies:
+ A policy does not begin creating snapshots until you set its activation status to *enabled*\. You can configure a policy to be enabled upon creation\.
+ Snapshots begin to be created by a policy within one hour following the specified start time\.
+ If you modify a policy by removing or changing its target tag, the EBS volumes with that tag are no longer affected by the policy\.
+ If you modify the schedule name for a policy, the snapshots created under the old schedule name are no longer affected by the policy\.
+ If you delete the resource to which a policy applies, the policy no longer manages the previously created snapshots\. You must manually delete the snapshots if they are no longer needed\.
+ You can create multiple policies to back up an EBS volume or an EC2 instance\. For example, if an EBS volume has two tags, where tag A is the target for policy A to create a snapshot every 12 hours, and tag B is the target for policy B to create a snapshot every 24 hours, Amazon DLM creates snapshots according to the schedules for both policies\.
+ When you copy a snapshot created by a policy, the retention schedule is not carried over to the copy\. This ensures that Amazon DLM does not delete snapshots that should be retained for a longer period of time\.

For example, you could create a policy that manages all EBS volumes with the tag `account=Finance`, creates snapshots every 24 hours at 0900, and retains the five most recent snapshots\. Snapshot creation could start as late as 0959\.

## Permissions for Amazon DLM<a name="dlm-permissions"></a>

Amazon DLM uses an IAM role to get the permissions that are required to manage snapshots on your behalf\. Amazon DLM creates the **AWSDataLifecycleManagerDefaultRole** role the first time that you create a lifecycle policy using the AWS Management Console\. You can also create this role using the following [create\-default\-role](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-default-role.html) command\.

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

## Permissions for IAM Users<a name="dlm-access-control"></a>

An IAM user must have the following permissions to use Amazon DLM\.

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

## Limits<a name="dlm-limits"></a>

Your AWS account has the following limits related to Amazon DLM:
+ You can create up to 100 lifecycle policies per Region\.
+ You can add up to 50 tags per resource\.
+ You can create one schedule per lifecycle policy\.

## Working with Amazon DLM Using the Console<a name="snapshot-lifecycle-console"></a>

The following examples show how to use Amazon DLM to perform typical procedures to manage the backups of your EBS volumes\.<a name="console-create-policy"></a>

**To create a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**, **Create snapshot lifecycle policy**\.

1. Provide the following information for your policy as needed:
   + **Description**—A description of the policy\.
   + **Target with tags**—The resource tags that identify the volumes or instances to back up\.
   + **Schedule Name**—A name for the backup schedule\.
   + **Create snapshots every** ***n*** **Hours**—The number of hours between policy runs\. The supported values are 2, 3, 4, 6, 8, 12, and 24\.
   + **Snapshot creation start time** ***hh***:***mm*** **UTC**—The time of day when policy runs are scheduled to start\. The policy runs start within an hour after the scheduled time\.
   + **Retention rule**—The maximum number of snapshots to retain for each volume or instance\. The supported range is 1 to 1000\. After the limit is reached, the oldest snapshot is deleted when a new one is created\.
   + **Copy tags**—Copy all user\-defined tags on a source volume to snapshots of the volume created by this policy\.
   + **Tag created snapshots**—The resource tags to apply to the snapshots that are created\. These tags are in addition to the tags applied by Amazon DLM\. You can also choose variable tags that can automatically tag all of your snapshots with the corresponding instance\-id or timestamp\.
   + **IAM role**—An IAM role that has permissions to create, delete, and describe snapshots, and to describe volumes\. AWS provides a default role, **AWSDataLifecycleManagerDefaultRole**, or you can create a custom IAM role\.
   + **Policy status after creation**—Choose **Enable policy** to start the policy runs at the next scheduled time or **Disable policy** to prevent the policy from running\.

1. Choose **Create Policy**\.<a name="console-view-policy"></a>

**To display a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\. The **Details** tab displays the following information about the policy:
   + **Policy ID**
   + **Resource type**
   + **Policy type**
   + **Date created**
   + **Date modified**
   + **Target resource tags**
   + **Rule summary**
   + **Description**
   + **Policy state**
   + **Tags added to snapshots**<a name="console-modify-policy"></a>

**To modify a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\.

1. Choose **Actions**, **Modify policy**\.

1. In an existing lifecycle policy, you can modify the following policy values:
   + **Description**—A description of the policy\.
   + **Target with tags**—The resource tags that identify the volumes or instances to back up\.
   + **Schedule Name**—A name for the backup schedule\.
   + **Create snapshots every** ***n*** **Hours**—The number of hours between policy runs\. The supported values are 2, 3, 4, 6, 8, 12, and 24\.
   + **Snapshot creation start time** ***hh***:***mm*** **UTC**—The time of day when policy runs are scheduled to start\. The policy runs start within an hour after the scheduled time\.
   + **Retention rule**—The maximum number of snapshots to retain for each volume or instance\. The supported range is 1 to 1000\. After the limit is reached, the oldest snapshot is deleted when a new one is created\.
   + **Copy tags**—Copy all user\-defined tags on a source volume to snapshots of the volume created by this policy\.
   + **Tag created snapshots**—The resource tags to apply to the snapshots that are created\. These tags are in addition to the tags applied by Amazon DLM\. You can also choose variable tags that can automatically tag all of your snapshots with the corresponding instance\-id or timestamp\.
   + **IAM role**—An IAM role that has permissions to create, delete, and describe snapshots, and to describe volumes\. AWS provides a default role, **AWSDataLifecycleManagerDefaultRole**, or you can create a custom IAM role\.
   + **Policy status after creation**—Choose **Enable policy** to start the policy runs at the next scheduled time or **Disable policy** to prevent the policy from running\.<a name="console-delete-policy"></a>

**To delete a lifecycle policy**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**, **Lifecycle Manager**\.

1. Select a lifecycle policy from the list\.

1. Choose **Actions**, **Delete policy**\.

## Working with Amazon DLM Using the Command Line<a name="snaphot-lifecycle-cli"></a>

The following examples show how to use Amazon DLM to perform typical procedures to manage the backups of your EBS volumes\.

**Example Example: Create a lifecycle policy**  <a name="create-policy-cli"></a>
Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create a lifecycle policy\. To simplify the syntax, this example references a JSON file, `policyDetails.json`, that includes the policy details\.  

```
aws dlm create-lifecycle-policy --description "My first policy" --state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole --policy-details file://policyDetails.json
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
            "Count":5
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

**Example Example: Display a lifecycle policy**  <a name="display-policy-cli"></a>
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

**Example To modify a lifecycle policy**  <a name="modify-policy-cli"></a>
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

**Example Example: Delete a lifecycle policy**  <a name="delete-policy-cli"></a>
Use the [delete\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/delete-lifecycle-policy.html) command to delete a lifecycle policy and free up the target tags specified in the policy for reuse\.  

```
aws dlm delete-lifecycle-policy --policy-id policy-0123456789abcdef0
```

## Working with Amazon DLM Using the API<a name="snaphot-lifecycle-api"></a>

The [Amazon Data Lifecycle Manager API Reference](https://docs.aws.amazon.com/dlm/latest/APIReference/) provides descriptions and syntax for each of the actions and data types for the Amazon DLM Query API\.

Alternatively, you can use one of the AWS SDKs to access an API that's tailored to the programming language or platform that you're using\. For more information, see [AWS SDKs](http://aws.amazon.com/tools/#SDKs)\.

## Working with Amazon DLM to Create and Maintain Multi\-Volume Snapshots Using the Console<a name="multivolume-lifecycle"></a>

You can create lifecycle policies to automate the creation and deletion of multi\-volume snapshots by using the AWS Management Console, from the AWS CLI, or from the Data Lifecycle Management \(DLM\) APIs\. 

**Automate multi\-volume snapshots using the console**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Elastic Block Store**\. Then choose **Lifecycle Manager** and **Create snapshot lifecycle policy**\.

1. Provide the following information for your policy, as needed: 
   + **Description**—A description of the policy\.
   + **Target with tags**—The resource tags that identify the volumes or instances to back up\.
   + **Schedule Name**—A name for the backup schedule\.
   + **Create snapshots every** ***n*** **Hours**—The number of hours between policy runs\. The supported values are 2, 3, 4, 6, 8, 12, and 24\.
   + **Snapshot creation start time** ***hh***:***mm*** **UTC**—The time of day when policy runs are scheduled to start\. The policy runs start within an hour after the scheduled time\.
   + **Retention rule**—The maximum number of snapshots to retain for each volume or instance\. The supported range is 1 to 1000\. After the limit is reached, the oldest snapshot is deleted when a new one is created\.
   + **Copy tags**—Copy all user\-defined tags on a source volume to snapshots of the volume created by this policy\.
   + **Tag created snapshots**—The resource tags to apply to the snapshots that are created\. These tags are in addition to the tags applied by Amazon DLM\. You can also choose variable tags that can automatically tag all of your snapshots with the corresponding instance\-id or timestamp\.
   + **IAM role**—An IAM role that has permissions to create, delete, and describe snapshots, and to describe volumes\. AWS provides a default role, **AWSDataLifecycleManagerDefaultRole**, or you can create a custom IAM role\.
   + **Policy status after creation**—Choose **Enable policy** to start the policy runs at the next scheduled time or **Disable policy** to prevent the policy from running\.

1. Choose **Create Policy**\.

## Working with Amazon DLM to Create and Maintain Multi\-Volume Snapshots Using the CLI<a name="snaphots-lifecycle-cli"></a>

The following examples show how to use Amazon DLM to automate creation and deletion of multi\-volume snapshots using the AWS CLI\.

**Example Example: Create a lifecycle policy**  <a name="create-policy-cli"></a>
Use the [create\-lifecycle\-policy](https://docs.aws.amazon.com/cli/latest/reference/dlm/create-lifecycle-policy.html) command to create a lifecycle policy\. To simplify the syntax, this example references a JSON file, `policyDetails.json`, which includes the policy details\.  

```
aws dlm create-lifecycle-policy --description My multi-volume snapshots policy --state ENABLED --execution-role-arn arn:aws:iam::12345678910:role/AWSDataLifecycleManagerDefaultRole --policy-details file://multi-volume-policy.json
```
The following is an example of the `multi-volume-policy.json` file\.  

```
{
   "ResourceTypes": [
      "INSTANCE"
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
               "Value": "Daily-Multi-Volume Snapshots"
            }
         ],
        "VariableTags":[
            {
                "Key": "timestamp",
                "Value": "$(timestamp)"
             },
             {
                "Key": "instance-id",
                "Value": "$(instance-id)"
              },
          ],
              "Interval": 24,
              "IntervalUnit": "HOURS",
              "Times": [
              "03:00"
             ]
         },
         "RetainRule": {
            "Count":5
         },
         "CopyTags": false 
      }
   ]
    "Parameters": {
          "ExcludeBootVolume": true
  }
}
```
Upon success, the command returns the ID of the newly created policy\. The following is example output\.  

```
{
   "PolicyId": "policy-0123456789abcdef0"
}
```

## Monitoring the Snapshot Lifecycle<a name="dlm-monitor-lifecycle"></a>

You can use the following features to monitor the lifecycle of your snapshots\.

### Console and AWS CLI<a name="monitor-console-cli"></a>

You can view your lifecycle policies using the Amazon EC2 console or the AWS CLI\. Each snapshot created by a policy has a timestamp and policy\-related tags\. You can filter snapshots using tags to verify that your backups are being created as you intend\. For information about viewing lifecycle policies using the console, see [To display a lifecycle policy](#console-view-policy)\. For information about displaying information about lifecycle policies using the CLI, see [Example: Display a lifecycle policy](#display-policy-cli)\.

### CloudWatch Events<a name="monitor-cloudwatch-events"></a>

Amazon EBS and Amazon DLM emit events related to lifecycle policy actions\. You can use AWS Lambda and Amazon CloudWatch Events to handle event notifications programmatically\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following events are available:
+ `createSnapshot`—An Amazon EBS event emitted when a `CreateSnapshot` action succeeds or fails\. For more information, see [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.
+ `DLM Policy State Change`—A Amazon DLM event emitted when a lifecycle policy enters an error state\. The event contains a description of what caused the error\. The following is an example of an event when the permissions granted by the IAM role are insufficient:

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

### AWS CloudFormation<a name="lifecycle-cloudformation"></a>

When deploying resource stacks with AWS CloudFormation, you can include Amazon DLM policies in your AWS CloudFormation templates\. For more information, see [Amazon Data Lifecycle Manager Resource Types Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_DLM.html)\.
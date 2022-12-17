# EventBridge for Amazon EBS<a name="ebs-cloud-watch-events"></a>

Amazon EBS sends events to Amazon EventBridge for actions performed on volumes and snapshots\. With EventBridge, you can establish rules that trigger programmatic actions in response to these events\. For example, you can create a rule that sends a notification to your email when a snapshot is enabled for fast snapshot restore\.

Events in EventBridge are represented as JSON objects\. The fields that are unique to the event are contained in the "detail" section of the JSON object\. The "event" field contains the event name\. The "result" field contains the completed status of the action that triggered the event\. For more information, see [Amazon EventBridge event patterns](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-event-patterns.html) in the *Amazon EventBridge User Guide*\.

For more information, see [ What Is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) in the *Amazon EventBridge User Guide*\.

**Topics**
+ [EBS volume events](#volume-events)
+ [EBS volume modification events](#volume-modification-events)
+ [EBS snapshot events](#snapshot-events)
+ [EBS Snapshots Archive events](#snapshot-archive-events)
+ [EBS fast snapshot restore events](#fast-snapshot-restore-events)
+ [Using AWS Lambda to handle EventBridge events](#using_lambda)

## EBS volume events<a name="volume-events"></a>

Amazon EBS sends events to EventBridge when the following volume events occur\.

**Topics**
+ [createVolume](#create-volume)
+ [deleteVolume](#delete-volume)
+ [attachVolume](#attach-fail-key)



### Create volume \(createVolume\)<a name="create-volume"></a>

The `createVolume` event is sent to your AWS account when an action to create a volume completes\. However it is not saved, logged, or archived\. This event can have a result of either `available` or `failed`\. Creation will fail if an invalid AWS KMS key was provided, as shown in the examples below\.

**Event data**  
The listing below is an example of a JSON object emitted by EBS for a successful `createVolume` event\. 

```
{
   "version": "0",
   "id": "01234567-0123-0123-0123-012345678901",
   "detail-type": "EBS Volume Notification",
   "source": "aws.ec2",
   "account": "012345678901",
   "time": "yyyy-mm-ddThh:mm:ssZ",
   "region": "us-east-1",
   "resources": [
      "arn:aws:ec2:us-east-1:012345678901:volume/vol-01234567"
   ],
   "detail": {
      "result": "available",
      "cause": "",
      "event": "createVolume",
      "request-id": "01234567-0123-0123-0123-0123456789ab"
   }
}
```

The listing below is an example of a JSON object emitted by EBS after a failed `createVolume` event\. The cause for the failure was a disabled KMS key\.

```
{
  "version": "0",
  "id": "01234567-0123-0123-0123-0123456789ab",
  "detail-type": "EBS Volume Notification",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "sa-east-1",
  "resources": [
    "arn:aws:ec2:sa-east-1:0123456789ab:volume/vol-01234567",
  ],
  "detail": {
    "event": "createVolume",
    "result": "failed",
    "cause": "arn:aws:kms:sa-east-1:0123456789ab:key/01234567-0123-0123-0123-0123456789ab is disabled.",
    "request-id": "01234567-0123-0123-0123-0123456789ab",
  }
}
```

The following is an example of a JSON object that is emitted by EBS after a failed `createVolume` event\. The cause for the failure was a KMS key pending import\.

```
{  
  "version": "0",  
  "id": "01234567-0123-0123-0123-0123456789ab",  
  "detail-type": "EBS Volume Notification",  
  "source": "aws.ec2",  
  "account": "012345678901",  
  "time": "yyyy-mm-ddThh:mm:ssZ",  
  "region": "sa-east-1",  
  "resources": [    
    "arn:aws:ec2:sa-east-1:0123456789ab:volume/vol-01234567",  
  ],  
  "detail": {    
    "event": "createVolume",    
    "result": "failed",    
    "cause": "arn:aws:kms:sa-east-1:0123456789ab:key/01234567-0123-0123-0123-0123456789ab is pending import.",    
    "request-id": "01234567-0123-0123-0123-0123456789ab",  
  }
}
```

### Delete volume \(deleteVolume\)<a name="delete-volume"></a>

The `deleteVolume` event is sent to your AWS account when an action to delete a volume completes\. However it is not saved, logged, or archived\. This event has the result `deleted`\. If the deletion does not complete, the event is never sent\.

**Event data**  
The listing below is an example of a JSON object emitted by EBS for a successful `deleteVolume` event\. 

```
{
   "version": "0",
   "id": "01234567-0123-0123-0123-012345678901",
   "detail-type": "EBS Volume Notification",
   "source": "aws.ec2",
   "account": "012345678901",
   "time": "yyyy-mm-ddThh:mm:ssZ",
   "region": "us-east-1",
   "resources": [
      "arn:aws:ec2:us-east-1:012345678901:volume/vol-01234567"
   ],
   "detail": {
      "result": "deleted",
      "cause": "",
      "event": "deleteVolume",
      "request-id": "01234567-0123-0123-0123-0123456789ab"
   }
}
```

### Volume attach or reattach \(attachVolume, reattachVolume\)<a name="attach-fail-key"></a>

The `attachVolume` or `reattachVolume` event is sent to your AWS account if a volume fails to attach or reattach to an instance\. However it is not saved, logged, or archived\. If you use a KMS key to encrypt an EBS volume and the KMS key becomes invalid, EBS will emit an event if that KMS key is later used to attach or reattach to an instance, as shown in the examples below\.

**Event data**  
The listing below is an example of a JSON object emitted by EBS after a failed `attachVolume` event\. The cause for the failure was a KMS key pending deletion\.

**Note**  
AWS may attempt to reattach to a volume following routine server maintenance\.

```
{
  "version": "0",    
  "id": "01234567-0123-0123-0123-0123456789ab",
  "detail-type": "EBS Volume Notification",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
  "arn:aws:ec2:us-east-1:0123456789ab:volume/vol-01234567",
  "arn:aws:kms:us-east-1:0123456789ab:key/01234567-0123-0123-0123-0123456789ab"
  ],
  "detail": {
    "event": "attachVolume",
    "result": "failed",
    "cause": "arn:aws:kms:us-east-1:0123456789ab:key/01234567-0123-0123-0123-0123456789ab is pending deletion.",
    "request-id": ""
  }
}
```

The listing below is an example of a JSON object emitted by EBS after a failed `reattachVolume` event\. The cause for the failure was a KMS key pending deletion\.

```
{
  "version": "0",    
  "id": "01234567-0123-0123-0123-0123456789ab",
  "detail-type": "EBS Volume Notification",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
  "arn:aws:ec2:us-east-1:0123456789ab:volume/vol-01234567",
  "arn:aws:kms:us-east-1:0123456789ab:key/01234567-0123-0123-0123-0123456789ab"
  ],
  "detail": {
    "event": "reattachVolume",
    "result": "failed",
    "cause": "arn:aws:kms:us-east-1:0123456789ab:key/01234567-0123-0123-0123-0123456789ab is pending deletion.",
    "request-id": ""
  }
}
```

## EBS volume modification events<a name="volume-modification-events"></a>

Amazon EBS sends `modifyVolume` events to EventBridge when a volume is modified\. However it is not saved, logged, or archived\.

```
{
   "version": "0",
   "id": "01234567-0123-0123-0123-012345678901",
   "detail-type": "EBS Volume Notification",
   "source": "aws.ec2",
   "account": "012345678901",
   "time": "yyyy-mm-ddThh:mm:ssZ",
   "region": "us-east-1",
   "resources": [
      "arn:aws:ec2:us-east-1:012345678901:volume/vol-03a55cf56513fa1b6"
   ],
   "detail": {
      "result": "optimizing",
      "cause": "",
      "event": "modifyVolume",
      "request-id": "01234567-0123-0123-0123-0123456789ab"
   }
}
```

## EBS snapshot events<a name="snapshot-events"></a>

Amazon EBS sends events to EventBridge when the following volume events occur\.

**Topics**
+ [createSnapshot](#create-snapshot-complete)
+ [createSnapshots](#create-snapshots-complete)
+ [copySnapshot](#copy-snapshot-complete)
+ [shareSnapshot](#snapshot-shared)

### Create snapshot \(createSnapshot\)<a name="create-snapshot-complete"></a>

The `createSnapshot` event is sent to your AWS account when an action to create a snapshot completes\. However it is not saved, logged, or archived\. This event can have a result of either `succeeded` or `failed`\.

**Event data**  
The listing below is an example of a JSON object emitted by EBS for a successful `createSnapshot` event\. In the `detail` section, the `source` field contains the ARN of the source volume\. The `startTime` and `endTime` fields indicate when creation of the snapshot started and completed\.

```
{
  "version": "0",
  "id": "01234567-0123-0123-0123-012345678901",
  "detail-type": "EBS Snapshot Notification",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
     "arn:aws:ec2:us-west-2::snapshot/snap-01234567"
  ],
  "detail": {
    "event": "createSnapshot",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2:us-west-2::snapshot/snap-01234567",
    "source": "arn:aws:ec2:us-west-2::volume/vol-01234567",
    "startTime": "yyyy-mm-ddThh:mm:ssZ",
    "endTime": "yyyy-mm-ddThh:mm:ssZ"  }
}
```

### Create snapshots \(createSnapshots\)<a name="create-snapshots-complete"></a>

The `createSnapshots` event is sent to your AWS account when an action to create a multi\-volume snapshot completes\. This event can have a result of either `succeeded` or `failed`\.

**Event data**  
The listing below is an example of a JSON object emitted by EBS for a successful `createSnapshots` event\. In the `detail` section, the `source` field contains the ARNs of the source volumes of the multi\-volume snapshot set\. The `startTime` and `endTime` fields indicate when creation of the snapshot started and completed\.

```
{
  "version": "0",
  "id": "01234567-0123-0123-0123-012345678901",
  "detail-type": "EBS Multi-Volume Snapshots Completion Status",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2::us-east-1:snapshot/snap-01234567",
    "arn:aws:ec2::us-east-1:snapshot/snap-012345678"
  ],
  "detail": {
    "event": "createSnapshots",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "startTime": "yyyy-mm-ddThh:mm:ssZ",
    "endTime": "yyyy-mm-ddThh:mm:ssZ",
    "snapshots": [
      {
        "snapshot_id": "arn:aws:ec2::us-east-1:snapshot/snap-01234567",
        "source": "arn:aws:ec2::us-east-1:volume/vol-01234567",
        "status": "completed"
      },
      {
        "snapshot_id": "arn:aws:ec2::us-east-1:snapshot/snap-012345678",
        "source": "arn:aws:ec2::us-east-1:volume/vol-012345678",
        "status": "completed"
      }
    ]
  }
}
```

The listing below is an example of a JSON object emitted by EBS after a failed `createSnapshots` event\. The cause for the failure was one or more snapshots for the multi\-volume snapshot set failed to complete\. The values of `snapshot_id` are the ARNs of the failed snapshots\. `startTime` and `endTime` represent when the create\-snapshots action started and ended\.

```
{
  "version": "0",
  "id": "01234567-0123-0123-0123-012345678901",
  "detail-type": "EBS Multi-Volume Snapshots Completion Status",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2::us-east-1:snapshot/snap-01234567",
    "arn:aws:ec2::us-east-1:snapshot/snap-012345678"
  ],
"detail": {
    "event": "createSnapshots",
    "result": "failed",
    "cause": "Snapshot snap-01234567 is in status error",
   "request-id": "",
    "startTime": "yyyy-mm-ddThh:mm:ssZ",
    "endTime": "yyyy-mm-ddThh:mm:ssZ",
    "snapshots": [
      {
        "snapshot_id": "arn:aws:ec2::us-east-1:snapshot/snap-01234567",
        "source": "arn:aws:ec2::us-east-1:volume/vol-01234567",
        "status": "error"
      },
      {
        "snapshot_id": "arn:aws:ec2::us-east-1:snapshot/snap-012345678",
        "source": "arn:aws:ec2::us-east-1:volume/vol-012345678",
        "status": "error"
      }
    ]
  }
}
```

### Copy snapshot \(copySnapshot\)<a name="copy-snapshot-complete"></a>

The `copySnapshot` event is sent to your AWS account when an action to copy a snapshot completes\. However it is not saved, logged, or archived\. This event can have a result of either `succeeded` or `failed`\.

**Event data**  
The listing below is an example of a JSON object emitted by EBS after a successful `copySnapshot` event\. The value of `snapshot_id` is the ARN of the newly created snapshot\. In the `detail` section, the value of `source` is the ARN of the source snapshot\. `startTime` and `endTime` represent when the copy\-snapshot action started and ended\.

```
{
  "version": "0",
  "id": "01234567-0123-0123-0123-012345678901",
  "detail-type": "EBS Snapshot Notification",
  "source": "aws.ec2",
  "account": "123456789012",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2:us-west-2::snapshot/snap-01234567"
  ],
  "detail": {
    "event": "copySnapshot",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2:us-west-2::snapshot/snap-01234567",
    "source": "arn:aws:ec2:eu-west-1::snapshot/snap-76543210",
    "startTime": "yyyy-mm-ddThh:mm:ssZ",
    "endTime": "yyyy-mm-ddThh:mm:ssZ",
    "Incremental": "true"
  }
}
```

The listing below is an example of a JSON object emitted by EBS after a failed `copySnapshot` event\. The cause for the failure was an invalid source snapshot ID\. The value of `snapshot_id` is the ARN of the failed snapshot\. In the `detail` section, the value of `source` is the ARN of the source snapshot\. `startTime` and `endTime` represent when the copy\-snapshot action started and ended\. 

```
{
  "version": "0",
  "id": "01234567-0123-0123-0123-012345678901",
  "detail-type": "EBS Snapshot Notification",
  "source": "aws.ec2",
  "account": "123456789012",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2:us-west-2::snapshot/snap-01234567"
  ],
  "detail": {
    "event": "copySnapshot",
    "result": "failed",
    "cause": "Source snapshot ID is not valid",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2:us-west-2::snapshot/snap-01234567",
    "source": "arn:aws:ec2:eu-west-1::snapshot/snap-76543210",
    "startTime": "yyyy-mm-ddThh:mm:ssZ",
    "endTime": "yyyy-mm-ddThh:mm:ssZ"
  }
}
```

### Share snapshot \(shareSnapshot\)<a name="snapshot-shared"></a>

The `shareSnapshot` event is sent to your AWS account when another account shares a snapshot with it\. However it is not saved, logged, or archived\. The result is always `succeeded`\.

**Event data**  
The following is an example of a JSON object emitted by EBS after a completed `shareSnapshot` event\. In the `detail` section, the value of `source` is the AWS account number of the user that shared the snapshot with you\. `startTime` and `endTime` represent when the share\-snapshot action started and ended\. The `shareSnapshot` event is emitted only when a private snapshot is shared with another user\. Sharing a public snapshot does not trigger the event\.

```
{
  "version": "0",
  "id": "01234567-01234-0123-0123-012345678901",
  "detail-type": "EBS Snapshot Notification",
  "source": "aws.ec2",
  "account": "012345678901",
  "time": "yyyy-mm-ddThh:mm:ssZ",
  "region": "us-east-1",
  "resources": [
    "arn:aws:ec2:us-west-2::snapshot/snap-01234567"
  ],
  "detail": {
    "event": "shareSnapshot",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2:us-west-2::snapshot/snap-01234567",
    "source": 012345678901,
    "startTime": "yyyy-mm-ddThh:mm:ssZ",
    "endTime": "yyyy-mm-ddThh:mm:ssZ"
  }
}
```

## EBS Snapshots Archive events<a name="snapshot-archive-events"></a>

Amazon EBS emits events related to snapshot archiving actions\. For more information, see [Monitor snapshot archiving](monitor-snapshot-archiving.md)\.

## EBS fast snapshot restore events<a name="fast-snapshot-restore-events"></a>

Amazon EBS sends events to EventBridge when the state of fast snapshot restore for a snapshot changes\. Events are emitted on a best effort basis\.

The following is example data for this event\.

```
{
   "version": "0",
   "id": "01234567-0123-0123-0123-012345678901",
   "detail-type": "EBS Fast Snapshot Restore State-change Notification",
   "source": "aws.ec2",
   "account": "123456789012",
   "time": "yyyy-mm-ddThh:mm:ssZ",
   "region": "us-east-1",
   "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-03a55cf56513fa1b6"
   ],
   "detail": {
      "snapshot-id": "snap-1234567890abcdef0",
      "state": "optimizing",
      "zone": "us-east-1a",
      "message": "Client.UserInitiated - Lifecycle state transition",
   }
}
```

The possible values for `state` are `enabling`, `optimizing`, `enabled`, `disabling`, and `disabled`\.

The possible values for `message` are as follows:

`Client.InvalidSnapshot.InvalidState - The requested snapshot transitioned to an invalid state (Error)`  
A request to enable fast snapshot restore failed and the state transitioned to `disabling` or `disabled`\. Fast snapshot restore cannot be enabled for this snapshot\.

`Client.UserInitiated`  
The state successfully transitioned to `enabling` or `disabling`\.

`Client.UserInitiated - Lifecycle state transition`  
The state successfully transitioned to `optimizing`, `enabled`, or `disabled`\.

`Server.InsufficientCapacity - There was insufficient capacity available to satisfy the request`  
A request to enable fast snapshot restore failed due to insufficient capacity, and the state transitioned to `disabling` or `disabled`\. Wait and then try again\.

`Server.InternalError - An internal error caused the operation to fail`  
A request to enable fast snapshot restore failed due to an internal error, and the state transitioned to `disabling` or `disabled`\. Wait and then try again\.

`Client.InvalidSnapshot.InvalidState - The requested snapshot was deleted or access permissions were revoked`  
The fast snapshot restore state for the snapshot has transitioned to `disabling` or `disabled` because the snapshot was deleted or unshared by the snapshot owner\. Fast snapshot restore cannot be enabled for a snapshot that has been deleted or is no longer shared with you\.

## Using AWS Lambda to handle EventBridge events<a name="using_lambda"></a>

You can use Amazon EBS and Amazon EventBridge to automate your data\-backup workflow\. This requires you to create an IAM policy, a AWS Lambda function to handle the event, and an EventBridge rule that matches incoming events and routes them to the Lambda function\.

The following procedure uses the `createSnapshot` event to automatically copy a completed snapshot to another Region for disaster recovery\. 

**To copy a completed snapshot to another Region**

1. Create an IAM policy, such as the one shown in the following example, to provide permissions to use the `CopySnapshot` action and write to the EventBridge log\. Assign the policy to the IAM user that will handle the EventBridge event\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "ec2:CopySnapshot"
         ],
         "Resource": "*"
       }
     ]
   }
   ```

1. Define a function in Lambda that will be available from the EventBridge console\. The sample Lambda function below, written in Node\.js, is invoked by EventBridge when a matching `createSnapshot` event is emitted by Amazon EBS \(signifying that a snapshot was completed\)\. When invoked, the function copies the snapshot from `us-east-2` to `us-east-1`\.

   ```
   // Sample Lambda function to copy an EBS snapshot to a different Region
    
   var AWS = require('aws-sdk');
   var ec2 = new AWS.EC2();
    
   // define variables
   var destinationRegion = 'us-east-1';
   var sourceRegion = 'us-east-2';
   console.log ('Loading function');
    
   //main function
   exports.handler = (event, context, callback) => {
    
       // Get the EBS snapshot ID from the event details
       var snapshotArn = event.detail.snapshot_id.split('/');
       const snapshotId = snapshotArn[1];
       const description = `Snapshot copy from ${snapshotId} in ${sourceRegion}.`;
       console.log ("snapshotId:", snapshotId);
   
       // Load EC2 class and update the configuration to use destination Region to initiate the snapshot.
       AWS.config.update({region: destinationRegion});
       var ec2 = new AWS.EC2();
   
       // Prepare variables for ec2.modifySnapshotAttribute call
       const copySnapshotParams = {
           Description: description,
           DestinationRegion: destinationRegion,
           SourceRegion: sourceRegion,
           SourceSnapshotId: snapshotId
       };
    
       // Execute the copy snapshot and log any errors
       ec2.copySnapshot(copySnapshotParams, (err, data) => {
           if (err) {
               const errorMessage = `Error copying snapshot ${snapshotId} to Region ${destinationRegion}.`;
               console.log(errorMessage);
               console.log(err);
               callback(errorMessage);
           } else {
               const successMessage = `Successfully started copy of snapshot ${snapshotId} to Region ${destinationRegion}.`;
               console.log(successMessage);
               console.log(data);
               callback(null, successMessage);
           }
       });
   };
   ```

   To ensure that your Lambda function is available from the EventBridge console, create it in the Region where the EventBridge event will occur\. For more information, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\.

1. Open the Amazon EventBridge console at [https://console\.aws\.amazon\.com/events/](https://console.aws.amazon.com/events/)\.

1. In the navigation pane, choose **Rules**, and then choose **Create rule**\.

1. For **Step 1: Define rule detail**, do the following:

   1. Enter values for **Name** and **Description**\.

   1. For **Event bus**, keep **default**\.

   1. Ensure that **Enable the rule on the selected event bus** is toggled on\.

   1. For **Event type**, select **Rule with an event pattern**\.

   1. Choose **Next**\.

1. For **Step 2: Build event pattern**, do the following:

   1. For **Event source**, select **AWS events or EventBridge partner events**\.

   1. In the **Event pattern** section, for **Event source**, ensure that **AWS service** is selected, and for **AWS service**, select **EC2**\.

   1. For **Event type**, select **EBS Snapshot Notification**, select **Specific event\(s\)**, and then choose **createSnapshot**\.

   1. Select **Specific result\(s\)** and then choose **succeeded**\.

   1. Choose **Next**\.

1. For **Step 3: Select targets**, do the following:

   1. For **Target types**, choose **AWS service**\.

   1. For **Select target**, choose **Lambda function**, and for **Function** select the function that you created earlier\.

   1. Choose **Next**

1. For **Step 4: Configure tags**, specify tags for the rule if needed, and then choose **Next**\.

1. For **Step 5: Review and create**, review the rule and then choose **Create rule**\.

Your rule should now appear on the **Rules** tab\. In the example shown, the event that you configured should be emitted by EBS the next time you copy a snapshot\.
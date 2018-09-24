# Amazon CloudWatch Events for Amazon EBS<a name="ebs-cloud-watch-events"></a>

Amazon EBS emits notifications based on Amazon CloudWatch Events for a variety of snapshot and encryption status changes\. With CloudWatch Events, you can establish rules that trigger programmatic actions in response to a change in snapshot or encryption key state\. For example, when a snapshot is created, you can trigger an AWS Lambda function to share the completed snapshot with another account or copy it to another region for disaster\-recovery purposes\.

For more information, see [Using Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch User Guide*\.

## EBS Volume Events<a name="volume-events"></a>

This section defines the supported Amazon EBS volume events and provides examples of event output for specific scenarios\. Events in CloudWatch are represented as JSON objects\. For more information about the format and content of event objects, see [Events and Event Patterns](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CloudWatchEventsandEventPatterns.html) in the *Amazon CloudWatch Events User Guide*\.

**Note**  
Additional information about EBS volumes that is not captured by Cloudwatch is available through the   
[DescribeVolumes](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeVolumes.html) API and the [describe\-volumes](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes.html) CLI command\.

The fields that are unique to EBS events are contained in the "detail" section of the JSON objects shown below\. The "event" field contains the event name\. The "result" field contains the completed status of the action that triggered the event\.

### Create Volume \(`createVolume`\)<a name="create-volume"></a>

The `createVolume` event is sent to your AWS account when an action to create a volume completes\. This event can have a result of either `available` or `failed`\. Creation will fail if an invalid KMS key was provided, as shown in the examples below\.

**Event Data**  
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

### Delete Volume \(`deleteVolume`\)<a name="delete-volume"></a>

The `deleteVolume` event is sent to your AWS account when an action to delete a volume completes\. This event has the result `deleted`\. If the deletion does not complete, the event is never sent\.

**Event Data**  
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
      "arn:aws:ec2:us-east-1: 012345678901:volume/vol-01234567"
   ],
   "detail": {
      "result": "deleted",
      "cause": "",
      "event": "deleteVolume",
      "request-id": "01234567-0123-0123-0123-0123456789ab"
   }
}
```

### Volume Attach or Reattach \(`attachVolume`, `reattachVolume`\)<a name="attach-fail-key"></a>

The `attachVolume` or `reattachVolume` event is sent to your AWS account if a volume fails to attach or reattach to an instance\. If you use a KMS key to encrypt an EBS volume and the key becomes invalid, EBS will emit an event if that key is later used to attach or reattach to an instance, as shown in the examples below\.

**Event Data**  
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

## EBS Snapshot Events<a name="snapshot-events"></a>

### Create Snapshot \(`createSnapshot`\)<a name="create-snapshot-complete"></a>

The `createSnapshot` event is sent to your AWS account when an action to create a snapshot completes\. This event can have a result of either `succeeded` or `failed`\.

**Event Data**  
The listing below is an example of a JSON object emitted by EBS for a successful `createSnapshot` event\. In the `detail` section, the `source` field contains the ARN of the source volume\. The `StartTime` and `EndTime` fields indicate when creation of the snapshot started and completed\.

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
     "arn:aws:ec2::us-west-2:snapshot/snap-01234567"
  ],
  "detail": {
    "event": "createSnapshot",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2::us-west-2:snapshot/snap-01234567",
    "source": "arn:aws:ec2::us-west-2:volume/vol-01234567",
    "StartTime": "yyyy-mm-ddThh:mm:ssZ",
    "EndTime": "yyyy-mm-ddThh:mm:ssZ"  }
}
```

### Copy Snapshot \(`copySnapshot`\)<a name="copy-snapshot-complete"></a>

The `copySnapshot` event is sent to your AWS account when an action to copy a snapshot completes\. This event can have a result of either `succeeded` or `failed`\.

**Event Data**  
The listing below is an example of a JSON object emitted by EBS after a successful `copySnapshot` event\. The value of `snapshot_id` is the ARN of the newly created snapshot\. In the `detail` section, the value of `source` is the ARN of the source snapshot\. `StartTime` and `EndTime` represent when the copy\-snapshot action started and ended\.

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
    "arn:aws:ec2::us-west-2:snapshot/snap-01234567"
  ],
  "detail": {
    "event": "copySnapshot",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2::us-west-2:snapshot/snap-01234567",
    "source": "arn:aws:ec2::eu-west-1:snapshot/snap-76543210",
    "StartTime": "yyyy-mm-ddThh:mm:ssZ",
    "EndTime": "yyyy-mm-ddThh:mm:ssZ",
    "Incremental": "True"
  }
}
```

The listing below is an example of a JSON object emitted by EBS after a failed `copySnapshot` event\. The cause for the failure was an invalid source snapshot ID\. The value of `snapshot_id` is the ARN of the failed snapshot\. In the `detail` section, the value of `source` is the ARN of the source snapshot\. `StartTime` and `EndTime` represent when the copy\-snapshot action started and ended\. 

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
    "arn:aws:ec2::us-west-2:snapshot/snap-01234567"
  ],
  "detail": {
    "event": "copySnapshot",
    "result": "failed",
    "cause": "Source snapshot ID is not valid",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2::us-west-2:snapshot/snap-01234567",
    "source": "arn:aws:ec2::eu-west-1:snapshot/snap-76543210",
    "StartTime": "yyyy-mm-ddThh:mm:ssZ",
    "EndTime": "yyyy-mm-ddThh:mm:ssZ"
  }
}
```

### Share Snapshot \(`shareSnapshot`\)<a name="snapshot-shared"></a>

The `shareSnapshot` event is sent to your AWS account when another account shares a snapshot with it\. The result is always `succeeded`\.

**Event Data**  
The listing below is an example of a JSON object emitted by EBS after a completed `shareSnapshot` event\. In the `detail` section, the value of `source` is the AWS account number of the user that shared the snapshot with you\. `StartTime` and `EndTime` represent when the share\-snapshot action started and ended\. The `shareSnapshot` event is emitted only when a private snapshot is shared with another user\. Sharing a public snapshot does not trigger the event\.

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
    "arn:aws:ec2::us-west-2:snapshot/snap-01234567"
  ],
  "detail": {
    "event": "shareSnapshot",
    "result": "succeeded",
    "cause": "",
    "request-id": "",
    "snapshot_id": "arn:aws:ec2::us-west-2:snapshot/snap-01234567",
    "source": 012345678901,
    "StartTime": "yyyy-mm-ddThh:mm:ssZ",
    "EndTime": "yyyy-mm-ddThh:mm:ssZ"
  }
}
```

## Using Amazon Lambda To Handle CloudWatch Events<a name="using_lambda"></a>

You can use Amazon EBS and CloudWatch Events to automate your data\-backup workflow\. This requires you to create an IAM policy, a AWS Lambda function to handle the event, and an Amazon CloudWatch Events rule that matches incoming events and routes them to the Lambda function\.

The following procedure uses the `createSnapshot` event to automatically copy a completed snapshot to another region for disaster recovery\. 

**To copy a completed snapshot to another region**

1. Create an IAM policy, such as the one shown in the following example, to provide permissions to execute a `CopySnapshot` action and write to the CloudWatch Events log\. Assign the policy to the IAM user that will handle the CloudWatch event\.

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

1. Define a function in Lambda that will be available from the CloudWatch console\. The sample Lambda function below, written in Node\.js, is invoked by CloudWatch when a matching `createSnapshot` event is emitted by Amazon EBS \(signifying that a snapshot was completed\)\. When invoked, the function copies the snapshot from `us-east-2` to `us-east-1`\.

   ```
   // Sample Lambda function to copy an EBS snapshot to a different region
    
   var AWS = require('aws-sdk');
   var ec2 = new AWS.EC2();
    
   // define variables
   var destinationRegion = 'us-east-1';
   var sourceRegion = 'us-east-2';
   console.log ('Loading function');
    
   //main function
   exports.handler = (event, context, callback) => {
    
       // Get the EBS snapshot ID from the CloudWatch event details
       var snapshotArn = event.detail.snapshot_id.split('/');
       const snapshotId = snapshotArn[1];
       const description = `Snapshot copy from ${snapshotId} in ${sourceRegion}.`;
       console.log ("snapshotId:", snapshotId);
   
       // Load EC2 class and update the configuration to use destination region to initiate the snapshot.
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
               const errorMessage = `Error copying snapshot ${snapshotId} to region ${destinationRegion}.`;
               console.log(errorMessage);
               console.log(err);
               callback(errorMessage);
           } else {
               const successMessage = `Successfully started copy of snapshot ${snapshotId} to region ${destinationRegion}.`;
               console.log(successMessage);
               console.log(data);
               callback(null, successMessage);
           }
       });
   };
   ```

   To ensure that your Lambda function is available from the CloudWatch console, create it in the region where the CloudWatch event will occur\. For more information, see the [AWS Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Events**, **Create rule**, **Select event source**, and **Amazon EBS Snapshots**\.

1. For **Specific Event\(s\)**, choose **createSnapshot** and for **Specific Result\(s\)**, choose **succeeded**\.

1. For **Rule target**, find and choose the sample function that you previously created\.

1. Choose **Target**, **Add Target**\.

1. For **Lambda function**, select the Lambda function that you previously created and choose **Configure details**\.

1. On the **Configure rule details** page, type values for **Name** and **Description**\. Select the **State** check box to activate the function \(setting it to **Enabled**\)\. 

1. Choose **Create rule**\.

Your rule should now appear on the **Rules** tab\. In the example shown, the event that you configured should be emitted by EBS the next time you copy a snapshot\.
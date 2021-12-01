# Monitor snapshot archiving<a name="monitor-snapshot-archiving"></a>

Amazon EBS emits events related to snapshot archiving actions\. You can use AWS Lambda and Amazon CloudWatch Events to handle event notifications programmatically\. Events are emitted on a best effort basis\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following events are available:
+ `archiveSnapshot` — Emitted when a snapshot archive action succeeds or fails\.

  The following is an example of an event that is emitted when a snapshot archive action succeeds\.

  ```
  {
     "version": "0",
     "id": "01234567-0123-0123-0123-012345678901",
     "detail-type": "EBS Snapshot Notification",
     "source": "aws.ec2",
     "account": "123456789012",
     "time": "2021-05-25T13:12:22Z",
     "region": "us-east-1",
     "resources": [
       "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
     ],
     "detail": {
       "event": "archiveSnapshot",
       "result": "succeeded",
       "cause": "",
       "request-id": "123456789",
       "snapshot_id": "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef",
       "startTime": "2021-05-25T13:12:22Z",
       "endTime": "2021-05-45T15:30:00Z",
       "recycleBinExitTime": "2021-10-45T15:30:00Z"
    }
  ```

  The following is an example of an event that is emitted when a snapshot archive action fails\.

  ```
  {
     "version": "0",
    "id": "01234567-0123-0123-0123-012345678901",
    "detail-type": "EBS Snapshot Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2021-05-25T13:12:22Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
    ],
    "detail": {
      "event": "archiveSnapshot",
      "result": "failed",
      "cause": "Source snapshot ID is not valid",
      "request-id": "1234567890",
      "snapshot_id": "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef",
      "startTime": "2021-05-25T13:12:22Z",
      "endTime": "2021-05-45T15:30:00Z",
      "recycleBinExitTime": "2021-10-45T15:30:00Z"
    }
  }
  ```
+ `restoreSnapshot` — Emitted when a snapshot restore action succeeds or fails\.

  The following is an example of an event that is emitted when a permanent restore action succeeds\.

  ```
  {
     "version": "0",
    "id": "01234567-0123-0123-0123-012345678901",
    "detail-type": "EBS Snapshot Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2021-05-25T13:12:22Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
    ],
    "detail": {
      "event": "restoreSnapshot",
      "result": "succeeded",
      "cause": "",
      "request-id": "1234567890",
      "snapshot_id": "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
      "startTime": "2021-05-25T13:12:22Z",
      "endTime": "2021-10-45T15:30:00Z"
    }
  }
  ```

  The following is an example of an event that is emitted when a permanent restore action fails\.

  ```
  {
     "version": "0",
    "id": "01234567-0123-0123-0123-012345678901",
    "detail-type": "EBS Snapshot Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2021-05-25T13:12:22Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
    ],
    "detail": {
      "event": "restoreSnapshot",
      "result": "failed",
      "cause": "Source snapshot ID is not valid",
      "request-id": "1234567890",
      "snapshot_id": "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef",
      "startTime": "2021-05-25T13:12:22Z",
      "endTime": "2021-05-45T15:30:00Z",
      "recycleBinExitTime": "2021-10-45T15:30:00Z"
    }
  }
  ```

  The following is an example of an event that is emitted when a temporary restore action succeeds\.

  ```
  {
     "version": "0",
    "id": "01234567-0123-0123-0123-012345678901",
    "detail-type": "EBS Snapshot Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2021-05-25T13:12:22Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
    ],
    "detail": {
      "event": "restoreSnapshot",
      "result": "succeeded",
      "cause": "",
      "request-id": "1234567890",
      "snapshot_id": "arn:aws:ec2:us-us-east-1::snapshot/snap-01234567890abcdef",
      "startTime": "2021-05-25T13:12:22Z",
      "endTime": "2021-05-45T15:30:00Z",    
      "restoreExpiryTime": "2021-06-45T15:30:00Z",
      "recycleBinExitTime": "2021-10-45T15:30:00Z"
    }
  }
  ```

  The following is an example of an event that is emitted when a temporary restore action fails\.

  ```
  {
     "version": "0",
    "id": "01234567-0123-0123-0123-012345678901",
    "detail-type": "EBS Snapshot Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2021-05-25T13:12:22Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
    ],
    "detail": {
      "event": "restoreSnapshot",
      "result": "failed",
      "cause": "Source snapshot ID is not valid",
      "request-id": "1234567890",
      "snapshot_id": "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef",
      "startTime": "2021-05-25T13:12:22Z",
      "endTime": "2021-05-45T15:30:00Z",
      "recycleBinExitTime": "2021-10-45T15:30:00Z"
    }
  }
  ```
+ `restoreExpiry` — Emitted when the restore period for a temporarily restored snapshot expires\.

  The following is an example\.

  ```
  {
     "version": "0",
    "id": "01234567-0123-0123-0123-012345678901",
    "detail-type": "EBS Snapshot Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2021-05-25T13:12:22Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef"
    ],
    "detail": {
      "event": "restoryExpiry",
      "result": "succeeded",
      "cause": "",
      "request-id": "1234567890",
      "snapshot_id": "arn:aws:ec2:us-east-1::snapshot/snap-01234567890abcdef",
      "startTime": "2021-05-25T13:12:22Z",
      "endTime": "2021-05-45T15:30:00Z",
      "recycleBinExitTime": "2021-10-45T15:30:00Z"
    }
  }
  ```
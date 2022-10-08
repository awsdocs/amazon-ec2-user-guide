# Monitor your policies using CloudWatch Events<a name="monitor-cloudwatch-events"></a>

Amazon EBS and Amazon Data Lifecycle Manager emit events related to lifecycle policy actions\. You can use AWS Lambda and Amazon CloudWatch Events to handle event notifications programmatically\. Events are emitted on a best effort basis\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following events are available:

**Note**  
No events are emitted for AMI lifecycle policy actions\.
+ `createSnapshot`—An Amazon EBS event emitted when a `CreateSnapshot` action succeeds or fails\. For more information, see [EventBridge for Amazon EBS](ebs-cloud-watch-events.md)\.
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
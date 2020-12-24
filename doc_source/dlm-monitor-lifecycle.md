# Monitoring<a name="dlm-monitor-lifecycle"></a>

You can use the following features to monitor the lifecycle of your snapshots and AMIs\.

**Topics**
+ [Console and AWS CLI](#monitor-console-cli)
+ [CloudWatch Events](#monitor-cloudwatch-events)
+ [AWS CloudTrail](#monitor-lifecycle-cloudtrail)

## Console and AWS CLI<a name="monitor-console-cli"></a>

You can view your lifecycle policies using the Amazon EC2 console or the AWS CLI\. Each snapshot and AMI created by a policy has a timestamp and policy\-related tags\. You can filter snapshots and AMIs using these tags to verify that your backups are being created as you intend\. For information about viewing lifecycle policies using the console, see [View lifecycyle poilcies](view-modify-delete.md#view)\.

## CloudWatch Events<a name="monitor-cloudwatch-events"></a>

Amazon EBS and Amazon Data Lifecycle Manager emit events related to lifecycle policy actions\. You can use AWS Lambda and Amazon CloudWatch Events to handle event notifications programmatically\. For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

The following events are available:

**Note**  
No events are emitted for AMI lifecycle policy actions\.
+ `createSnapshot`—An Amazon EBS event emitted when a `CreateSnapshot` action succeeds or fails\. For more information, see [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.
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

## AWS CloudTrail<a name="monitor-lifecycle-cloudtrail"></a>

With AWS CloudTrail, you can track user activity and API usage to demonstrate compliance with internal policies and regulatory standards\. For more information, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.
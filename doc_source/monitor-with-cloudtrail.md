# Logging Amazon EC2 and Amazon EBS API calls with AWS CloudTrail<a name="monitor-with-cloudtrail"></a>

Amazon EC2 and Amazon EBS are integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in Amazon EC2 and Amazon EBS\. CloudTrail captures all API calls for Amazon EC2 and Amazon EBS as events, including calls from the console and from code calls to the APIs\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Amazon EC2 and Amazon EBS\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to Amazon EC2 and Amazon EBS, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Amazon EC2 and Amazon EBS information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in Amazon EC2 and Amazon EBS, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for Amazon EC2 and Amazon EBS, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

All Amazon EC2 actions, and Amazon EBS management actions, are logged by CloudTrail and are documented in the [Amazon EC2 API Reference](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/)\. For example, calls to the [RunInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-RunInstances.html), [DescribeInstances](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeInstances.html), or [CreateImage](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-CreateImage.html) actions generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or IAM user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Understanding Amazon EC2 and Amazon EBS log file entries<a name="understanding-service-name-entries"></a>

A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files are not an ordered stack trace of the public API calls, so they do not appear in any specific order\. 

The following log file record shows that a user terminated an instance\.

```
{
   "Records":[
      {
         "eventVersion":"1.03",
         "userIdentity":{
            "type":"Root",
            "principalId":"123456789012",
            "arn":"arn:aws:iam::123456789012:root",
            "accountId":"123456789012",
            "accessKeyId":"AKIAIOSFODNN7EXAMPLE",
            "userName":"user"
         },
         "eventTime":"2016-05-20T08:27:45Z",
         "eventSource":"ec2.amazonaws.com",
         "eventName":"TerminateInstances",
         "awsRegion":"us-west-2",
         "sourceIPAddress":"198.51.100.1",
         "userAgent":"aws-cli/1.10.10 Python/2.7.9 Windows/7botocore/1.4.1",
         "requestParameters":{
            "instancesSet":{
               "items":[{
                  "instanceId":"i-1a2b3c4d"
               }]
            }
         },
         "responseElements":{
            "instancesSet":{
               "items":[{
                  "instanceId":"i-1a2b3c4d",
                  "currentState":{
                     "code":32,
                     "name":"shutting-down"
                  },
                  "previousState":{
                     "code":16,
                     "name":"running"
                  }
               }]
            }
         },
         "requestID":"be112233-1ba5-4ae0-8e2b-1c302EXAMPLE",
         "eventID":"6e12345-2a4e-417c-aa78-7594fEXAMPLE",
         "eventType":"AwsApiCall",
         "recipientAccountId":"123456789012"
     }
   ]
}
```

## Using AWS CloudTrail to audit users that connect via EC2 Instance Connect<a name="ec2-instance-connect-cloudtrail"></a>

Use AWS CloudTrail to audit the users that connect to your instances via EC2 Instance Connect\.

**To audit SSH activity via EC2 Instance Connect using the AWS CloudTrail console**

1. Open the AWS CloudTrail console at [https://console.aws.amazon.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Verify that you are in the correct Region\.

1. In the navigation pane, choose **Event history**\.

1. For **Filter**, choose **Event source**, **ec2\-instance\-connect\.amazonaws\.com**\.

1. \(Optional\) For **Time range**, select a time range\.

1. Choose the **Refresh events** icon\.

1. The page displays the events that correspond to the `[SendSSHPublicKey](https://docs.aws.amazon.com/ec2-instance-connect/latest/APIReference/API_SendSSHPublicKey.html)` API calls\. Expand an event using the arrow to view additional details, such as the user name and AWS access key that was used to make the SSH connection, and the source IP address\.

1. To display the full event information in JSON format, choose **View event**\. The **requestParameters** field contains the destination instance ID, OS user name, and public key that were used to make the SSH connection\.

   ```
   {
       "eventVersion": "1.05",
       "userIdentity": {
           "type": "IAMUser",
           "principalId": "ABCDEFGONGNOMOOCB6XYTQEXAMPLE",
           "arn": "arn:aws:iam::1234567890120:user/IAM-friendly-name",
           "accountId": "123456789012",
           "accessKeyId": "ABCDEFGUKZHNAW4OSN2AEXAMPLE",
           "userName": "IAM-friendly-name",
           "sessionContext": {
               "attributes": {
                   "mfaAuthenticated": "false",
                   "creationDate": "2018-09-21T21:37:58Z"}
           }
       },
       "eventTime": "2018-09-21T21:38:00Z",
       "eventSource": "ec2-instance-connect.amazonaws.com",
       "eventName": "SendSSHPublicKey ",
       "awsRegion": "us-west-2",
       "sourceIPAddress": "123.456.789.012",
       "userAgent": "aws-cli/1.15.61 Python/2.7.10 Darwin/16.7.0 botocore/1.10.60",
       "requestParameters": {
           "instanceId": "i-0123456789EXAMPLE",
           "osUser": "ec2-user",
           "SSHKey": {
               "publicKey": "ssh-rsa ABCDEFGHIJKLMNO01234567890EXAMPLE"
           }
        },
       "responseElements": null,
       "requestID": "1a2s3d4f-bde6-11e8-a892-f7ec64543add",
       "eventID": "1a2w3d4r5-a88f-4e28-b3bf-30161f75be34",
       "eventType": "AwsApiCall",
       "recipientAccountId": "0987654321"
   }
   ```

   If you have configured your AWS account to collect CloudTrail events in an S3 bucket, you can download and audit the information programmatically\. For more information, see [Getting and Viewing Your CloudTrail Log Files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/get-and-view-cloudtrail-log-files.html) in the *AWS CloudTrail User Guide*\.
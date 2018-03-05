# Monitoring the Progress of Volume Modifications<a name="monitoring_mods"></a>

An EBS volume being modified goes through a sequence of states\. After you issue a `ModifyVolume` directive, whether from the console, CLI, API, or SDK, the volume enters first the `Modifying` state, then the `Optimizing` state, and finally the `Complete` state\. At this point, the volume is ready to be further modified\. Rarely, a transient AWS fault can result in the `Failed` state\. If this occurs, retry the modification\. 

Size changes usually take a few seconds to complete and take effect after a volume is in the `Optimizing` state\. 

Performance \(IOPS\) changes can take from a few minutes to a few hours to complete and are dependent on the configuration change being made\. 

It may take up to 24 hours for a new configuration to take effect, and in some cases more, such as when the volume has not been fully initialized\. Typically, a fully used 1\-TiB volume takes about 6 hours to migrate to a new performance configuration\. 

While the volume is in the `optimizing` state, your volume performance is in between the source and target configuration specifications\. Transitional volume performance will be no less than the source volume performance\. If you are downgrading IOPS, transitional volume performance is no less than the target volume performance\.

You can monitor the progress of a modification by inspecting the AWS Management Console, by querying the volume's state with the Amazon EC2 API/CLI, or by accessing metrics sent to Amazon CloudWatch Events\. The following procedures demonstrate these approaches\.<a name="console_monitoring"></a>

**To monitor progress of a modification from the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Volumes**, and select the volume to inspect\. The volume's status is displayed in the **State** column\. In the example below, the modification state is **completed**\. This state information is also displayed in the **State** field of the details pane\. 

1. Open the information icon next to the **State** field to display complete before and after information about the most recent modification action, as illustrated below\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/monitor_modifications.png)

**Example To monitor progress of a modification from the command line**  <a name="api_cli_monitoring"></a>
Use [describe\-volumes\-modifications](cli-modify.md) to view the progress of the modifications\. In this example, volume `vol-11111111111111111` from above and another volume, `vol-22222222222222222`, are called\.  

```
aws ec2 describe-volumes-modifications --region us-east-1 --volume-id vol-11111111111111111 vol-22222222222222222
```
The command returns one or more `VolumesModification` objects\. The following is example output\. The first object is nearly identical to the original `modify-volume` command output shown above\. No additional modifications have been applied, however\.  

```
{
    "VolumesModifications": [
        {
            "TargetSize": 200,
            "TargetVolumeType": "io1",
            "ModificationState": "modifying",
            "VolumeId": "vol-11111111111111111",
            "TargetIops": 10000,
            "StartTime": "2017-01-19T22:21:02.959Z",
            "Progress": 0,
            "OriginalVolumeType": "gp2",
            "OriginalIops": 300,
            "OriginalSize": 100
        },
        {
            "TargetSize": 2000,
            "TargetVolumeType": "sc1",
            "ModificationState": "modifying",
            "VolumeId": "vol-22222222222222222",
            "StartTime": "2017-01-19T22:23:22.158Z",
            "Progress": 0,
            "OriginalVolumeType": "gp2",
            "OriginalIops": 300,
            "OriginalSize": 1000
        }
    ]
}
```
The next example queries for all volumes in a region with a modification state of either `optimizing` or `completed`, and then filters and formats the results to show only modifications that were initiated on or after February 1, 2017:  

```
aws ec2 describe-volumes-modifications --filters Name=modification-state,Values="optimizing","completed" --region us-east-1 --query "VolumesModifications[?StartTime>='2017-02-01'].{ID:VolumeId,STATE:ModificationState}"
```
In this case the query returns information about two volumes:  

```
[
    {
        "STATE": "optimizing",
        "ID": "vol-06397e7a0eEXAMPLE"
    },
    {
        "STATE": "completed",
        "ID": "vol-bEXAMPLE"
    }
]
```<a name="cwe_monitoring"></a>

**To monitor progress of a modification with CloudWatch Events**

With CloudWatch Events, you can create a notification rule for volume modification events to send a text message or execute a Lambda function\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Events**, **Create rule**\.

1. For **Build event pattern to match events by service**, choose **Custom event pattern**\.

1. For **Build custom event pattern**, replace the contents with the following code:

   ```
   {
     "source": [
       "aws.ec2"
     ],
     "detail-type": [
       "EBS Volume Notification"
     ],
     "detail": {
       "event": [
         "modifyVolume"
       ]
     }
   }
   ```

   Choose **Save**\.

   The typical event output should look like the following:

   ```
   Body:
   {
      "version": "0",
      "id": "1ea2ace2-7790-46ed-99ab-d07a8bd68685",
      "detail-type": "EBS Volume Notification",
      "source": "aws.ec2",
      "account": "065441870323",
      "time": "2017-01-12T21:09:07Z",
      "region": "us-east-1",
      "resources": [
         "arn:aws:ec2:us-east-1:065441870323:volume/vol-03a55cf56513fa1b6"
      ],
      "detail": {
         "result": "optimizing",
         "cause": "",
         "event": "modifyVolume",
         "request-id": "auto-58c08bad-d90b-11e6-a309-b51ed35473f8"
      }
   }
   ```

You can use your rule to generate a notification message with [Amazon SNS](http://docs.aws.amazon.com/sns/latest/dg/) or to invoke a [Lambda function](http://docs.aws.amazon.com/lambda/latest/dg/) in response to matching events\.
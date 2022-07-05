# Monitor the progress of volume modifications<a name="monitoring-volume-modifications"></a>

When you modify an EBS volume, it goes through a sequence of states\. The volume enters the `modifying` state, the `optimizing` state, and finally the `completed` state\. At this point, the volume is ready to be further modified\. 

**Note**  
Rarely, a transient AWS fault can result in a `failed` state\. This is not an indication of volume health; it merely indicates that the modification to the volume failed\. If this occurs, retry the volume modification\.

While the volume is in the `optimizing` state, your volume performance is in between the source and target configuration specifications\. Transitional volume performance will be no less than the source volume performance\. If you are downgrading IOPS, transitional volume performance is no less than the target volume performance\.

Volume modification changes take effect as follows:
+ Size changes usually take a few seconds to complete and take effect after the volume has transitioned to the `Optimizing` state\.
+ Performance \(IOPS\) changes can take from a few minutes to a few hours to complete and are dependent on the configuration change being made\.
+ In some cases, it can take more than 24 hours for a new configuration to take effect, such as when the volume has not been fully initialized\. Typically, a fully used 1\-TiB volume takes about 6 hours to migrate to a new performance configuration\. 

To monitor the progress of a volume modification, use one of the following methods\.

------
#### [ New console ]

**To monitor progress of a modification using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Select the volume\.

1. The **Volume state** column and the **Volume state** field in the **Details** tab contain information in the following format: *volume\-state* \- *modification\-state* \(*progress*%\)\.

   The possible volume states are `creating`, `available`, `in-use`, `deleting`, `deleted`, and `error`\.

   The possible code states are `modifying`, `optimizing`, and `completed`\.

------
#### [ Old console ]

**To monitor progress of a modification using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\.

1. Select the volume\.

1. The **State** column and the **State** field in the details pane contain information in the following format: *volume\-state* \- *modification\-state* \(*progress*%\)\. The possible volume states are **creating**, **available**, **in\-use**, **deleting**, **deleted**, and **error**\. The possible modification states are **modifying**, **optimizing**, and **completed**\. Shortly after the volume modification is completed, we remove the modification state and progress, leaving only the volume state\.

   In this example, the modification state of the selected volume is **optimizing**\. The modification state of the next volume is **modifying**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/monitor_modifications.png)

1. Choose the text in the **State** field in the details pane to display information about the most recent modification action, as shown in the previous step\.

------
#### [ AWS CLI ]

**To monitor progress of a modification using the AWS CLI**  
Use the [describe\-volumes\-modifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volumes-modifications.html) command to view the progress of one or more volume modifications\. The following example describes the volume modifications for two volumes\.

```
aws ec2 describe-volumes-modifications --volume-ids vol-11111111111111111 vol-22222222222222222
```

In the following example output, the volume modifications are still in the `modifying` state\. Progress is reported as a percentage\.

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

The next example describes all volumes with a modification state of either `optimizing` or `completed`, and then filters and formats the results to show only modifications that were initiated on or after February 1, 2017:

```
aws ec2 describe-volumes-modifications --filters Name=modification-state,Values="optimizing","completed" --query "VolumesModifications[?StartTime>='2017-02-01'].{ID:VolumeId,STATE:ModificationState}"
```

The following is example output with information about two volumes:

```
[
    {
        "STATE": "optimizing",
        "ID": "vol-06397e7a0eEXAMPLE"
    },
    {
        "STATE": "completed",
        "ID": "vol-ba74e18c2aEXAMPLE"
    }
]
```

------
#### [ CloudWatch Events console ]

With CloudWatch Events, you can create a notification rule for volume modification events\. You can use your rule to generate a notification message using [Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/) or to invoke a [Lambda function](https://docs.aws.amazon.com/lambda/latest/dg/) in response to matching events\. Events are emitted on a best effort basis\.

**To monitor progress of a modification using CloudWatch Events**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. Choose **Events**, **Create rule**\.

1. For **Build event pattern to match events by service**, choose **Custom event pattern**\.

1. For **Build custom event pattern**, replace the contents with the following and choose **Save**\.

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

   The following is example event data:

   ```
   {
      "version": "0",
      "id": "01234567-0123-0123-0123-012345678901",
      "detail-type": "EBS Volume Notification",
      "source": "aws.ec2",
      "account": "012345678901",
      "time": "2017-01-12T21:09:07Z",
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

------
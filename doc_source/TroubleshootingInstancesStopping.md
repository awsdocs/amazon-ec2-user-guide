# Troubleshooting Stopping Your Instance<a name="TroubleshootingInstancesStopping"></a>

If you have stopped your Amazon EBS\-backed instance and it appears "stuck" in the `stopping` state, there may be an issue with the underlying host computer\.

First, try stopping the instance again\. If you are using the [stop\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) \(AWS CLI\) command be sure to use the `--force` option\.

If you can't force the instance to stop, you can create an AMI from the instance and launch a replacement instance\.

You are not billed for any instance usage while an instance is not in the `running` state\.

**To create a replacement instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the instance\.

1. Choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** dialog box, fill in the following fields and then choose **Create Image**:

   1. Specify a name and description for the AMI\.

   1. Choose **No reboot**\.

1. Launch an instance from the AMI and verify that the instance is working\.

1. Select the stuck instance, choose **Actions**, **Instance State**, **Terminate**\. If the instance also gets stuck terminating, Amazon EC2 automatically forces it to terminate within a few hours\.

If you are unable to create an AMI from the instance as described in the previous procedure, you can set up a replacement instance as follows:

**To create a replacement instance \(if the previous procedure fails\)**

1. Select the instance, open the **Description** tab, and view the **Block devices** list\. Select each volume and write down its volume ID\. Be sure to note which volume is the root volume\.

1. In the navigation pane, choose **Volumes**\. Select each volume for the instance, and choose **Actions**, **Create Snapshot**\.

1. In the navigation pane, choose **Snapshots**\. Select the snapshot that you just created, and choose **Actions**, **Create Volume**\.

1. Launch an instance of the same type as the stuck instance \(Amazon Linux, Windows, and so on\)\. Note the volume ID and device name of its root volume\.

1. In the navigation pane, choose **Instances**, select the instance that you just launched, choose **Actions**, **Instance State**, and then choose **Stop**\.

1. In the navigation pane, choose **Volumes**, select the root volume of the stopped instance, and choose **Actions**, **Detach Volume**\.

1. Select the root volume that you created from the stuck instance, choose **Actions**, **Attach Volume**, and attach it to the new instance as its root volume \(using the device name that you wrote down\)\. Attach any additional non\-root volumes to the instance\.

1. In the navigation pane, choose **Instances** and select the replacement instance\. Choose **Actions**, **Instance State**, **Start**\. Verify that the instance is working\.

1. Select the stuck instance, choose **Actions**, **Instance State**, **Terminate**\. If the instance also gets stuck terminating, Amazon EC2 automatically forces it to terminate within a few hours\.

If you're unable to complete these procedures, you can post a request for help to the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the instance ID and describe the steps that you've already taken\.
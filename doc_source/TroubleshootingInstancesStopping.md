# Troubleshooting Stopping Your Instance<a name="TroubleshootingInstancesStopping"></a>

If you have stopped your Amazon EBS\-backed instance and it appears stuck in the `stopping` state, there may be an issue with the underlying host computer\.

There is no cost for any instance usage while an instance is not in the `running` state\.

Force the instance to stop using either the console or the AWS CLI\.
+ To force the instance to stop using the console, select the stuck instance, and choose **Actions**, **Instance State**, **Stop**, and **Yes, Forcefully Stop**\.
+ To force the instance to stop using the AWS CLI, use the [stop\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/stop-instances.html) command and the `--force` option as follows:

  ```
  aws ec2 stop-instances --instance-ids i-0123ab456c789d01e --force
  ```

If, after 10 minutes, the instance has not stopped, post a request for help in the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30)\. To help expedite a resolution, include the instance ID, and describe the steps that you've already taken\. Alternatively, if you have a support plan, create a technical support case in the [Support Center](https://console.aws.amazon.com/support/home#/)\.

## Creating a Replacement Instance<a name="Creating_Replacement_Instance"></a>

To attempt to resolve the problem while you are waiting for assistance from the [Amazon EC2 forum](https://forums.aws.amazon.com/forum.jspa?forumID=30) or the [Support Center](https://console.aws.amazon.com/support/home#/), create a replacement instance\. Create an AMI of the stuck instance, and launch a new instance using the new AMI\. 

**To create a replacement instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select the stuck instance\.

1. Choose **Actions**, **Image**, **Create Image**\.

1. In the **Create Image** dialog box, fill in the following fields, and then choose **Create Image**:

   1. Specify a name and description for the AMI\.

   1. Choose **No reboot**\.

   For more information, see [Creating a Linux AMI from an Instance](creating-an-ami-ebs.md#how-to-create-ebs-ami)\.

1. Launch a new instance from the AMI and verify that the new instance is working\.

1. Select the stuck instance, and choose **Actions**, **Instance State**, **Terminate**\. If the instance also gets stuck terminating, Amazon EC2 automatically forces it to terminate within a few hours\.

**To create a replacement instance using the CLI**

1. Create an AMI from the stuck instance using the [create\-image](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-image.html) \(AWS CLI\) command and the `--no-reboot` option as follows:\.

   ```
   aws ec2 create-image --instance-id i-0123ab456c789d01e --name "AMI" --description "AMI for replacement instance" --no-reboot
   ```

1. Launch a new instance from the AMI using the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\) command as follows:

   ```
   aws ec2 run-instances --image-id ami-1a2b3c4d --count 1 --instance-type c3.large --key-name MyKeyPair --security-groups MySecurityGroup
   ```

1. Verify that the new instance is working\.

1. Terminate the stuck instance using the [terminate\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/terminate-instances.html) \(AWS CLI\) command as follows:

   ```
   aws ec2 terminate-instances --instance-ids i-1234567890abcdef0
   ```

If you are unable to create an AMI from the instance as described in the previous procedures, you can set up a replacement instance as follows:

**\(Alternate\) To create a replacement instance using the console**

1. Select the instance and choose **Description**, **Block devices**\. Select each volume and write down its volume ID\. Be sure to note which volume is the root volume\.

1. In the navigation pane, choose **Volumes**\. Select each volume for the instance, and choose **Actions**, **Create Snapshot**\.

1. In the navigation pane, choose **Snapshots**\. Select the snapshot that you just created, and choose **Actions**, **Create Volume**\.

1. Launch an instance with the same operating system as the stuck instance\. Note the volume ID and device name of its root volume\.

1. In the navigation pane, choose **Instances**, select the instance that you just launched, choose **Actions**, **Instance State**, and then choose **Stop**\.

1. In the navigation pane, choose **Volumes**, select the root volume of the stopped instance, and choose **Actions**, **Detach Volume**\.

1. Select the root volume that you created from the stuck instance, choose **Actions**, **Attach Volume**, and attach it to the new instance as its root volume \(using the device name that you wrote down\)\. Attach any additional non\-root volumes to the instance\.

1. In the navigation pane, choose **Instances** and select the replacement instance\. Choose **Actions**, **Instance State**, **Start**\. Verify that the instance is working\.

1. Select the stuck instance, choose **Actions**, **Instance State**, **Terminate**\. If the instance also gets stuck terminating, Amazon EC2 automatically forces it to terminate within a few hours\.
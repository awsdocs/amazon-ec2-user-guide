# What to Do If an Instance Immediately Terminates<a name="Using_InstanceStraightToTerminated"></a>

After you launch an instance, we recommend that you check its status to confirm that it goes from the `pending` state to the `running` state, not the `terminated` state\.

The following are a few reasons why an instance might immediately terminate:
+ You've reached your EBS volume limit\. For information about the volume limit, see [Instance Volume Limits](volume_limits.md)\. To submit a request to increase your Amazon EBS volume limit, complete the AWS Support Center [Create Case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ebs) form\. For more information, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.
+ An EBS snapshot is corrupt\.
+ The instance store\-backed AMI you used to launch the instance is missing a required part \(an image\.part\.*xx* file\)\.

## Getting the Reason for Instance Termination<a name="Using_InstanceStraightToTerminated_Console"></a>

You can use the Amazon EC2 console, CLI, or API to get information about the reason that the instance terminated\.

**To get the reason that an instance terminated using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, and select your instance\.

1. In the **Description** tab, locate the reason next to the label **State transition reason**\. If the instance is still running, there's typically no reason listed\. If you've explicitly stopped or terminated the instance, the reason is `User initiated shutdown`\.

**To get the reason that an instance terminated using the command line**

1. Use the [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command:

   ```
   aws ec2 describe-instances --instance-id instance_id
   ```

1. In the JSON response that's displayed, locate the `StateReason` element\. It looks similar to the following example\.

   ```
   "StateReason": {
     "Message": "Client.UserInitiatedShutdown: User initiated shutdown", 
     "Code": "Client.UserInitiatedShutdown"
   },
   ```

   This example response shows the reason code that you'll see after you stop or terminate a running instance\. If the instance terminated immediately, you see `code` and `message` elements that describe the reason that the instance terminated \(for example, `VolumeLimitExceeded`\)\.
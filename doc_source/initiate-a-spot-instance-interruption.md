# Initiate a Spot Instance interruption<a name="initiate-a-spot-instance-interruption"></a>

You can select a Spot Instance in the Amazon EC2 console and initiate an interruption so that you can test how the applications on your Spot Instances handle being interrupted\. When you initiate a Spot Instance interruption, Amazon EC2 notifies you that your Spot Instance will be interrupted in two minutes, and then, after two minutes, Amazon EC2 interrupts the Spot Instance\.

The underlying service that performs the Spot Instance interruption is AWS Fault Injection Simulator \(AWS FIS\)\. For information about AWS FIS, see [AWS Fault Injection Simulator](http://aws.amazon.com/fis)\.

**Note**  
Interruption behaviors are `terminate`, `stop`, and `hibernate`\. If you set the interruption behavior to `hibernate`, when you initiate a Spot Instance interruption, the hibernation process will begin immediately\.

Initiating a Spot Instance interruption is supported in all AWS Regions except Asia Pacific \(Jakarta\), Asia Pacific \(Osaka\), China \(Beijing\), China \(Ningxia\), and Middle East \(UAE\)\.

**Topics**
+ [Initiate a Spot Instance interruption](#initiate-interruption)
+ [Verify the Spot Instance interruption](#spot-interruptions-verify-result)

## Initiate a Spot Instance interruption<a name="initiate-interruption"></a>

You can use the EC2 console to quickly initiate a Spot Instance interruption\. For more advanced experiments to test Spot Instance interruptions, you can create your own experiments using the AWS FIS console\.

**To initiate a Spot Instance interruption using the EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation pane, choose **Spot Requests**\.

1. Select a Spot Instance request, and then choose **Actions**, **Initiate interruption**\.
**Note**  
Selecting a Spot Fleet request to initiate interrupting all the Spot Instances in the fleet at once is currently not supported; you must select each Spot Instance request in the fleet separately\. You also can’t select multiple Spot Instance requests to initiate an interruption; you can only initiate an interruption for one Spot Instance at a time\.

1. In the **Initiate Spot Instance interruption** dialog box, under **Service access**, either use the default role, or choose an existing role\. To choose a role, choose **Use an existing service role**, and then, for **Service role name**, select the role to use\.

1. When you're ready to initiate the Spot Instance interruption, choose **Initiate interruption\.**

**To create more advanced experiments to test Spot Instance interruptions using the AWS FIS console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation pane, choose **Spot Requests**\.

1. Choose **Actions**, **Create advanced experiments**\.

   The AWS FIS console opens\. For more information, see [Tutorial: Test Spot Instance interruptions using AWS FIS](https://docs.aws.amazon.com/fis/latest/userguide/fis-tutorial-spot-interruptions.html) in the *AWS Fault Injection Simulator User Guide*\.

## Verify the Spot Instance interruption<a name="spot-interruptions-verify-result"></a>

After you initiate the interruption, the following occurs:
+ The Spot Instance receives an [instance rebalance recommendation](rebalance-recommendations.md)\.
+ A [Spot Instance interruption notice](spot-instance-termination-notices.md) is issued two minutes before Amazon EC2 interrupts your instance\.
+ After two minutes, the Spot Instance is interrupted\.
+ A Spot Instance that was stopped by AWS FIS remains stopped until you restart it\.

**To verify that the instance was interrupted after you initiated the interruption**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation pane, open **Spot Requests** and **Instances** in separate browser tabs or windows\.

1. For **Spot Requests**, select the Spot Instance request\. The initial status is `fulfilled`\. After the instance is interrupted, the status changes as follows, depending on the interruption behavior:
   + `terminate` – The status changes to `instance-terminated-by-experiment`\.
   + `stop` – The status changes to `marked-for-stop-by-experiment` and then `instance-stopped-by-experiment`\.

1. For **Instances**, select the Spot Instance\. The initial status is `Running`\. Two minutes after you receive the Spot Instance interruption notice, the status changes as follows, depending on the interruption behavior:
   + `stop` – The status changes to `Stopping` and then `Stopped`\.
   + `terminate` – The status changes to `Shutting-down` and then `Terminated`\.
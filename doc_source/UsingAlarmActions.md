# Create Alarms That Stop, Terminate, Reboot, or Recover an Instance<a name="UsingAlarmActions"></a>

Using Amazon CloudWatch alarm actions, you can create alarms that automatically stop, terminate, reboot, or recover your instances\. You can use the stop or terminate actions to help you save money when you no longer need an instance to be running\. You can use the reboot and recover actions to automatically reboot those instances or recover them onto new hardware if a system impairment occurs\.

Every alarm action you create uses alarm action ARNs\. One set of ARNs is more secure because it requires you to have the EC2ActionsAccess IAM role in your account\. This IAM role enables you to perform stop, terminate, or reboot actions—previously you could not execute an action if you were using an IAM role\. Existing alarms that use the previous alarm action ARNs do not require this IAM role; however, it is recommended that you change the ARN and add the role when you edit an existing alarm that uses these ARNs\.

The `EC2ActionsAccess` role enables AWS to perform alarm actions on your behalf\. When you create an alarm action for the first time using the Amazon EC2 or Amazon CloudWatch consoles, AWS automatically creates this role for you\.

There are a number of scenarios in which you might want to automatically stop or terminate your instance\. For example, you might have instances dedicated to batch payroll processing jobs or scientific computing tasks that run for a period of time and then complete their work\. Rather than letting those instances sit idle \(and accrue charges\), you can stop or terminate them, which can help you to save money\. The main difference between using the stop and the terminate alarm actions is that you can easily restart a stopped instance if you need to run it again later, and you can keep the same instance ID and root volume\. However, you cannot restart a terminated instance\. Instead, you must launch a new instance\.

You can add the stop, terminate, reboot, or recover actions to any alarm that is set on an Amazon EC2 per\-instance metric, including basic and detailed monitoring metrics provided by Amazon CloudWatch \(in the `AWS/EC2` namespace\), as well as any custom metrics that include the `InstanceId` dimension, as long as its value refers to a valid running Amazon EC2 instance\.

**Console Support**  
You can create alarms using the Amazon EC2 console or the CloudWatch console\. The procedures in this documentation use the Amazon EC2 console\. For procedures that use the CloudWatch console, see [Create Alarms That Stop, Terminate, Reboot, or Recover an Instance](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/UsingAlarmActions.html) in the *Amazon CloudWatch User Guide*\.

**Permissions**  
If you are an AWS Identity and Access Management \(IAM\) user, you must have the following permissions to create or modify an alarm:

+ `ec2:DescribeInstanceStatus` and `ec2:DescribeInstances` – For all alarms on Amazon EC2 instance status metrics

+ `ec2:StopInstances` – For alarms with stop actions

+ `ec2:TerminateInstances` – For alarms with terminate actions

+ `ec2:DescribeInstanceRecoveryAttribute`, and `ec2:RecoverInstances` – For alarms with recover actions

If you have read/write permissions for Amazon CloudWatch but not for Amazon EC2, you can still create an alarm but the stop or terminate actions won't be performed on the Amazon EC2 instance\. However, if you are later granted permission to use the associated Amazon EC2 APIs, the alarm actions you created earlier are performed\. For more information about IAM permissions, see [Permissions and Policies](http://docs.aws.amazon.com/IAM/latest/UserGuide/PermissionsAndPolicies.html) in the *IAM User Guide*\.

If you want to use an IAM role to stop, terminate, or reboot an instance using an alarm action, you can only use the EC2ActionsAccess role\. Other IAM roles are not supported\. If you are using another IAM role, you cannot stop, terminate, or reboot the instance\. However, you can still see the alarm state and perform any other actions such as Amazon SNS notifications or Amazon EC2 Auto Scaling policies\.


+ [Adding Stop Actions to Amazon CloudWatch Alarms](#AddingStopActions)
+ [Adding Terminate Actions to Amazon CloudWatch Alarms](#AddingTerminateActions)
+ [Adding Reboot Actions to Amazon CloudWatch Alarms](#AddingRebootActions)
+ [Adding Recover Actions to Amazon CloudWatch Alarms](#AddingRecoverActions)
+ [Using the Amazon CloudWatch Console to View Alarm and Action History](#ViewAlarmHistory)
+ [Amazon CloudWatch Alarm Action Scenarios](#AlarmActionScenarios)

## Adding Stop Actions to Amazon CloudWatch Alarms<a name="AddingStopActions"></a>

You can create an alarm that stops an Amazon EC2 instance when a certain threshold has been met\. For example, you may run development or test instances and occasionally forget to shut them off\. You can create an alarm that is triggered when the average CPU utilization percentage has been lower than 10 percent for 24 hours, signaling that it is idle and no longer in use\. You can adjust the threshold, duration, and period to suit your needs, plus you can add an Amazon Simple Notification Service \(Amazon SNS\) notification so that you receive an email when the alarm is triggered\.

Instances that use an Amazon EBS volume as the root device can be stopped or terminated, whereas instances that use the instance store as the root device can only be terminated\.

**To create an alarm to stop an idle instance using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, type a name for the topic, and then for **With these recipients**, type the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get notifications for this topic\.

   1. Choose **Take the action**, **Stop this instance**\.

   1. If prompted, choose **Create IAM role: EC2ActionsAccess** to automatically create an IAM role so that AWS can automatically stop the instance on your behalf when the alarm is triggered\.

   1. For **Whenever**, choose the statistic you want to use and then choose the metric\. In this example, choose **Average** and **CPU Utilization**\.

   1. For **Is**, specify the metric threshold\. In this example, type **10** percent\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, type **24** consecutive period\(s\) of **1 Hour**\.

   1. To change the name of the alarm, for **Name of alarm**, type a new name\. Alarm names must contain only ASCII characters\.

      If you don't type a name for the alarm, Amazon CloudWatch automatically creates one for you\.
**Note**  
You can adjust the alarm configuration based on your own requirements before creating the alarm, or you can edit them later\. This includes the metric, threshold, duration, action, and notification settings\. However, after you create an alarm, you cannot edit its name later\.

   1. Choose **Create Alarm**\.

## Adding Terminate Actions to Amazon CloudWatch Alarms<a name="AddingTerminateActions"></a>

You can create an alarm that terminates an EC2 instance automatically when a certain threshold has been met \(as long as termination protection is not enabled for the instance\)\. For example, you might want to terminate an instance when it has completed its work, and you don’t need the instance again\. If you might want to use the instance later, you should stop the instance instead of terminating it\. For information on enabling and disabling termination protection for an instance, see [Enabling Termination Protection for an Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Using_ChangingDisableAPITermination.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To create an alarm to terminate an idle instance using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, type a name for the topic, and then for **With these recipients**, type the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get notifications for this topic\.

   1. Choose **Take the action**, **Terminate this instance**\.

   1. If prompted, choose **Create IAM role: EC2ActionsAccess** to automatically create an IAM role so that AWS can automatically stop the instance on your behalf when the alarm is triggered\.

   1. For **Whenever**, choose a statistic and then choose the metric\. In this example, choose **Average** and **CPU Utilization**\.

   1. For **Is**, specify the metric threshold\. In this example, type **10** percent\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, type **24** consecutive period\(s\) of **1 Hour**\.

   1. To change the name of the alarm, for **Name of alarm**, type a new name\. Alarm names must contain only ASCII characters\.

      If you don't type a name for the alarm, Amazon CloudWatch automatically creates one for you\.
**Note**  
You can adjust the alarm configuration based on your own requirements before creating the alarm, or you can edit them later\. This includes the metric, threshold, duration, action, and notification settings\. However, after you create an alarm, you cannot edit its name later\.

   1. Choose **Create Alarm**\.

## Adding Reboot Actions to Amazon CloudWatch Alarms<a name="AddingRebootActions"></a>

You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance and automatically reboots the instance\. The reboot alarm action is recommended for Instance Health Check failures \(as opposed to the recover alarm action, which is suited for System Health Check failures\)\. An instance reboot is equivalent to an operating system reboot\. In most cases, it takes only a few minutes to reboot your instance\. When you reboot an instance, it remains on the same physical host, so your instance keeps its public DNS name, private IP address, and any data on its instance store volumes\.

Rebooting an instance doesn't start a new instance billing period \(with a minimum one\-minute charge\), unlike stopping and restarting your instance\. For more information, see [Reboot Your Instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-reboot.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Important**  
To avoid a race condition between the reboot and recover actions, avoid setting the same number of evaluation periods for a reboot alarm and a recover alarm\. We recommend that you set reboot alarms to three evaluation periods of one minute each\. For more information, see [Evaluating an Alarm](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#alarm-evaluation) in the *Amazon CloudWatch User Guide*\.

**To create an alarm to reboot an instance using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, type a name for the topic, and for **With these recipients**, type the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get notifications for this topic\.

   1. Select **Take the action**, **Reboot this instance**\.

   1. If prompted, select **Create IAM role: EC2ActionsAccess** to automatically create an IAM role so that AWS can automatically stop the instance on your behalf when the alarm is triggered\.

   1. For **Whenever**, choose **Status Check Failed \(Instance\)**\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, type **3** consecutive period\(s\) of **1 Minute**\.

   1. To change the name of the alarm, for **Name of alarm**, type a new name\. Alarm names must contain only ASCII characters\.

      If you don't type a name for the alarm, Amazon CloudWatch automatically creates one for you\.

   1. Choose **Create Alarm**\.

## Adding Recover Actions to Amazon CloudWatch Alarms<a name="AddingRecoverActions"></a>

You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance\. If the instance becomes impaired due to an underlying hardware failure or a problem that requires AWS involvement to repair, you can automatically recover the instance\. Terminated instances cannot be recovered\. A recovered instance is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata\.

When the `StatusCheckFailed_System` alarm is triggered, and the recover action is initiated, you are notified by the Amazon SNS topic that you chose when you created the alarm and associated the recover action\. During instance recovery, the instance is migrated during an instance reboot, and any data that is in\-memory is lost\. When the process is complete, information is published to the SNS topic you've configured for the alarm\. Anyone who is subscribed to this SNS topic receives an email notification that includes the status of the recovery attempt and any further instructions\. You notice an instance reboot on the recovered instance\.

The recover action can be used only with `StatusCheckFailed_System`, not with `StatusCheckFailed_Instance`\.

The following problems can cause system status checks to fail:

+ Loss of network connectivity

+ Loss of system power

+ Software issues on the physical host

+ Hardware issues on the physical host that impact network reachability

The recover action is supported only on instances with the following characteristics:

+ Use a C3, C4, C5, M3, M4, M5, R3, R4, T2, or X1 instance type

+ Run in a VPC \(not EC2\-Classic\)

+ Use shared tenancy \(the tenancy attribute is set to `default`\)

+ Use EBS volumes only \(do not configure instance store volumes\)\. For more information, see ['Recover this instance' is disabled](https://aws.amazon.com/premiumsupport/knowledge-center/recover-this-instance-cloudwatch-enable/)\.

If your instance has a public IP address, it retains the public IP address after recovery\.

**Important**  
To avoid a race condition between the reboot and recover actions, avoid setting the same number of evaluation periods for a reboot alarm and a recover alarm\. We recommend that you set recover alarms to two evaluation periods of one minute each\. For more information, see [Evaluating an Alarm](http://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#alarm-evaluation) in the *Amazon CloudWatch User Guide*\.

**To create an alarm to recover an instance using the Amazon EC2 console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, type a name for the topic, and for **With these recipients**, type the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get email for this topic\.

   1. Select **Take the action**, **Recover this instance**\.

   1. If prompted, select **Create IAM role: EC2ActionsAccess** to automatically create an IAM role so that AWS can automatically stop the instance on your behalf when the alarm is triggered\.

   1. For **Whenever**, choose **Status Check Failed \(System\)**\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, type **2** consecutive period\(s\) of **1 Minute**\.

   1. To change the name of the alarm, for **Name of alarm**, type a new name\. Alarm names must contain only ASCII characters\.

      If you don't type a name for the alarm, Amazon CloudWatch automatically creates one for you\.

   1. Choose **Create Alarm**\.

## Using the Amazon CloudWatch Console to View Alarm and Action History<a name="ViewAlarmHistory"></a>

You can view alarm and action history in the Amazon CloudWatch console\. Amazon CloudWatch keeps the last two weeks' worth of alarm and action history\.

**To view the history of triggered alarms and actions**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**\.

1. Select an alarm\.

1. The **Details** tab shows the most recent state transition along with the time and metric values\.

1. Choose the **History** tab to view the most recent history entries\.

## Amazon CloudWatch Alarm Action Scenarios<a name="AlarmActionScenarios"></a>

You can use the Amazon EC2 console to create alarm actions that stop or terminate an Amazon EC2 instance when certain conditions are met\. In the following screen capture of the console page where you set the alarm actions, we've numbered the settings\. We've also numbered the settings in the scenarios that follow, to help you create the appropriate actions\.

![\[Create Alarm for dialog box\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/CreateAlarmFor.png)

### Scenario 1: Stop Idle Development and Test Instances<a name="StopIdleInstance"></a>

Create an alarm that stops an instance used for software development or testing when it has been idle for at least an hour\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Stop  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Maximum  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  CPUUtilization  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  <=  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  10%  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  60 minutes  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  1  | 

### Scenario 2: Stop Idle Instances<a name="StopLowUtilizationInstance"></a>

Create an alarm that stops an instance and sends an email when the instance has been idle for 24 hours\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Stop and email  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Average  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  CPUUtilization  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  <=  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  5%  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  60 minutes  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  24  | 

### Scenario 3: Send Email About Web Servers with Unusually High Traffic<a name="StopHighWebTraffic"></a>

Create an alarm that sends email when an instance exceeds 10 GB of outbound network traffic per day\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Email  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Sum  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  NetworkOut  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  >  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  10 GB  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  1 day  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  1  | 

### Scenario 4: Stop Web Servers with Unusually High Traffic<a name="StopHighWebTraffic2"></a>

Create an alarm that stops an instance and send a text message \(SMS\) if outbound traffic exceeds 1 GB per hour\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Stop and send SMS  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Sum  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  NetworkOut  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  >  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  1 GB  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  1 hour  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  1  | 

### Scenario 5: Stop an Instance Experiencing a Memory Leak<a name="StopMemoryLeak"></a>

Create an alarm that stops an instance when memory utilization reaches or exceeds 90%, so that application logs can be retrieved for troubleshooting\.

**Note**  
The MemoryUtilization metric is a custom metric\. In order to use the MemoryUtilization metric, you must install the Perl scripts for Linux instances\. For more information, see [Monitoring Memory and Disk Metrics for Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html)\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Stop  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Maximum  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  MemoryUtilization  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  >=  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  90%  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  1 minute  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  1  | 

### Scenario 6: Stop an Impaired Instance<a name="StopImpairedInstance"></a>

Create an alarm that stops an instance that fails three consecutive status checks \(performed at 5\-minute intervals\)\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Stop  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Average  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  StatusCheckFailed\_System  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  >=  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  1  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  15 minutes  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  1  | 

### Scenario 7: Terminate Instances When Batch Processing Jobs Are Complete<a name="TerminateBatchProcesses"></a>

Create an alarm that terminates an instance that runs batch jobs when it is no longer sending results data\.


| Setting | Value | 
| --- | --- | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/1.png)  |  Terminate  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/2.png)  |  Maximum  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/3.png)  |  NetworkOut  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/4.png)  |  <=  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/5.png)  |  100,000 bytes  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/6.png)  |  5 minutes  | 
|  ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/docs-common/images/callouts/7.png)  |  1  | 
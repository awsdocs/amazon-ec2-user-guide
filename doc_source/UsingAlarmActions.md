# Create alarms that stop, terminate, reboot, or recover an instance<a name="UsingAlarmActions"></a>

Using Amazon CloudWatch alarm actions, you can create alarms that automatically stop, terminate, reboot, or recover your instances\. You can use the stop or terminate actions to help you save money when you no longer need an instance to be running\. You can use the reboot and recover actions to automatically reboot those instances or recover them onto new hardware if a system impairment occurs\.

The `AWSServiceRoleForCloudWatchEvents` service\-linked role enables AWS to perform alarm actions on your behalf\. The first time you create an alarm in the AWS Management Console, the IAM CLI, or the IAM API, CloudWatch creates the service\-linked role for you\.

There are a number of scenarios in which you might want to automatically stop or terminate your instance\. For example, you might have instances dedicated to batch payroll processing jobs or scientific computing tasks that run for a period of time and then complete their work\. Rather than letting those instances sit idle \(and accrue charges\), you can stop or terminate them, which can help you to save money\. The main difference between using the stop and the terminate alarm actions is that you can easily start a stopped instance if you need to run it again later, and you can keep the same instance ID and root volume\. However, you cannot start a terminated instance\. Instead, you must launch a new instance\.

You can add the stop, terminate, reboot, or recover actions to any alarm that is set on an Amazon EC2 per\-instance metric, including basic and detailed monitoring metrics provided by Amazon CloudWatch \(in the `AWS/EC2` namespace\), as well as any custom metrics that include the `InstanceId` dimension, as long as its value refers to a valid running Amazon EC2 instance\.

**Console support**  
You can create alarms using the Amazon EC2 console or the CloudWatch console\. The procedures in this documentation use the Amazon EC2 console\. For procedures that use the CloudWatch console, see [Create Alarms That Stop, Terminate, Reboot, or Recover an Instance](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/UsingAlarmActions.html) in the *Amazon CloudWatch User Guide*\.

**Permissions**  
If you are an AWS Identity and Access Management \(IAM\) user, you must have the `iam:CreateServiceLinkedRole` to create or modify an alarm that performs EC2 alarm actions\.

**Topics**
+ [Add stop actions to Amazon CloudWatch alarms](#AddingStopActions)
+ [Add terminate actions to Amazon CloudWatch alarms](#AddingTerminateActions)
+ [Add reboot actions to Amazon CloudWatch alarms](#AddingRebootActions)
+ [Add recover actions to Amazon CloudWatch alarms](#AddingRecoverActions)
+ [Use the Amazon CloudWatch console to view alarm and action history](#ViewAlarmHistory)
+ [Amazon CloudWatch alarm action scenarios](#AlarmActionScenarios)

## Add stop actions to Amazon CloudWatch alarms<a name="AddingStopActions"></a>

You can create an alarm that stops an Amazon EC2 instance when a certain threshold has been met\. For example, you may run development or test instances and occasionally forget to shut them off\. You can create an alarm that is triggered when the average CPU utilization percentage has been lower than 10 percent for 24 hours, signaling that it is idle and no longer in use\. You can adjust the threshold, duration, and period to suit your needs, plus you can add an Amazon Simple Notification Service \(Amazon SNS\) notification so that you receive an email when the alarm is triggered\.

Instances that use an Amazon EBS volume as the root device can be stopped or terminated, whereas instances that use the instance store as the root device can only be terminated\.

------
#### [ New console ]

**To create an alarm to stop an idle instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitor and troubleshoot**, **Manage CloudWatch alarms**\.

   Alternatively, you can choose the plus sign \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/add-plus.png) \) in the **Alarm status** column\.

1. On the **Manage CloudWatch alarms** page, do the following:

   1. Choose **Create an alarm**\.

   1. To receive an email when the alarm is triggered, for **Alarm notification**, choose an existing Amazon SNS topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

   1. Toggle on **Alarm action**, and choose **Stop**\.

   1. For **Group samples by** and **Type of data to sample**, choose a statistic and a metric\. In this example, choose **Average** and **CPU utilization**\.

   1. For **Alarm When** and **Percent**, specify the metric threshold\. In this example, specify **<=** and **10** percent\.

   1. For **Consecutive period** and **Period**, specify the evaluation period for the alarm\. In this example, specify **1** consecutive period of **5 Minutes**\.

   1. Amazon CloudWatch automatically creates an alarm name for you\. To change the name, for **Alarm name**, enter a new name\. Alarm names must contain only ASCII characters\.
**Note**  
You can adjust the alarm configuration based on your own requirements before creating the alarm, or you can edit them later\. This includes the metric, threshold, duration, action, and notification settings\. However, after you create an alarm, you cannot edit its name later\.

   1. Choose **Create**\.

------
#### [ Old console ]

**To create an alarm to stop an idle instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, enter a name for the topic, and then for **With these recipients**, enter the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get notifications for this topic\.

   1. Choose **Take the action**, **Stop this instance**\.

   1. For **Whenever**, choose the statistic you want to use and then choose the metric\. In this example, choose **Average** and **CPU Utilization**\.

   1. For **Is**, specify the metric threshold\. In this example, enter **10** percent\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, enter **24** consecutive period\(s\) of **1 Hour**\.

   1. To change the name of the alarm, for **Name of alarm**, enter a new name\. Alarm names must contain only ASCII characters\.

      If you don't enter a name for the alarm, Amazon CloudWatch automatically creates one for you\.
**Note**  
You can adjust the alarm configuration based on your own requirements before creating the alarm, or you can edit them later\. This includes the metric, threshold, duration, action, and notification settings\. However, after you create an alarm, you cannot edit its name later\.

   1. Choose **Create Alarm**\.

------

## Add terminate actions to Amazon CloudWatch alarms<a name="AddingTerminateActions"></a>

You can create an alarm that terminates an EC2 instance automatically when a certain threshold has been met \(as long as termination protection is not enabled for the instance\)\. For example, you might want to terminate an instance when it has completed its work, and you don’t need the instance again\. If you might want to use the instance later, you should stop the instance instead of terminating it\. For information on enabling and disabling termination protection for an instance, see [Enable termination protection](terminating-instances.md#Using_ChangingDisableAPITermination)\.

------
#### [ New console ]

**To create an alarm to terminate an idle instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitor and troubleshoot**, **Manage CloudWatch alarms**\.

   Alternatively, you can choose the plus sign \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/add-plus.png) \) in the **Alarm status** column\.

1. On the **Manage CloudWatch alarms** page, do the following:

   1. Choose **Create an alarm**\.

   1. To receive an email when the alarm is triggered, for **Alarm notification**, choose an existing Amazon SNS topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

   1. Toggle on **Alarm action**, and choose **Terminate**\.

   1. For **Group samples by** and **Type of data to sample**, choose a statistic and a metric\. In this example, choose **Average** and **CPU utilization**\.

   1. For **Alarm When** and **Percent**, specify the metric threshold\. In this example, specify **=>** and **10** percent\.

   1. For **Consecutive period** and **Period**, specify the evaluation period for the alarm\. In this example, specify **24** consecutive periods of **1 Hour**\.

   1. Amazon CloudWatch automatically creates an alarm name for you\. To change the name, for **Alarm name**, enter a new name\. Alarm names must contain only ASCII characters\.
**Note**  
You can adjust the alarm configuration based on your own requirements before creating the alarm, or you can edit them later\. This includes the metric, threshold, duration, action, and notification settings\. However, after you create an alarm, you cannot edit its name later\.

   1. Choose **Create**\.

------
#### [ Old console ]

**To create an alarm to terminate an idle instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, enter a name for the topic, and then for **With these recipients**, enter the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get notifications for this topic\.

   1. Choose **Take the action**, **Terminate this instance**\.

   1. For **Whenever**, choose a statistic and then choose the metric\. In this example, choose **Average** and **CPU Utilization**\.

   1. For **Is**, specify the metric threshold\. In this example, enter **10** percent\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, enter **24** consecutive period\(s\) of **1 Hour**\.

   1. To change the name of the alarm, for **Name of alarm**, enter a new name\. Alarm names must contain only ASCII characters\.

      If you don't enter a name for the alarm, Amazon CloudWatch automatically creates one for you\.
**Note**  
You can adjust the alarm configuration based on your own requirements before creating the alarm, or you can edit them later\. This includes the metric, threshold, duration, action, and notification settings\. However, after you create an alarm, you cannot edit its name later\.

   1. Choose **Create Alarm**\.

------

## Add reboot actions to Amazon CloudWatch alarms<a name="AddingRebootActions"></a>

You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance and automatically reboots the instance\. The reboot alarm action is recommended for Instance Health Check failures \(as opposed to the recover alarm action, which is suited for System Health Check failures\)\. An instance reboot is equivalent to an operating system reboot\. In most cases, it takes only a few minutes to reboot your instance\. When you reboot an instance, it remains on the same physical host, so your instance keeps its public DNS name, private IP address, and any data on its instance store volumes\.

Rebooting an instance doesn't start a new instance billing period \(with a minimum one\-minute charge\), unlike stopping and restarting your instance\. For more information, see [Reboot your instance](ec2-instance-reboot.md)\.

**Important**  
To avoid a race condition between the reboot and recover actions, avoid setting the same number of evaluation periods for a reboot alarm and a recover alarm\. We recommend that you set reboot alarms to three evaluation periods of one minute each\. For more information, see [Evaluating an alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#alarm-evaluation) in the *Amazon CloudWatch User Guide*\.

------
#### [ New console ]

**To create an alarm to reboot an instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitor and troubleshoot**, **Manage CloudWatch alarms**\.

   Alternatively, you can choose the plus sign \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/add-plus.png) \) in the **Alarm status** column\.

1. On the **Manage CloudWatch alarms** page, do the following:

   1. Choose **Create an alarm**\.

   1. To receive an email when the alarm is triggered, for **Alarm notification**, choose an existing Amazon SNS topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.

   1. Toggle on **Alarm action**, and choose **Reboot**\.

   1. For **Group samples by** and **Type of data to sample**, choose a statistic and a metric\. In this example, choose **Average** and **Status check failed: instance**\.

   1. For **Consecutive period** and **Period**, specify the evaluation period for the alarm\. In this example, enter **3** consecutive periods of **5 Minutes**\.

   1. Amazon CloudWatch automatically creates an alarm name for you\. To change the name, for **Alarm name**, enter a new name\. Alarm names must contain only ASCII characters\.

   1. Choose **Create**\.

------
#### [ Old console ]

**To create an alarm to reboot an instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, enter a name for the topic, and for **With these recipients**, enter the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get notifications for this topic\.

   1. Select **Take the action**, **Reboot this instance**\.

   1. For **Whenever**, choose **Status Check Failed \(Instance\)**\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, enter **3** consecutive period\(s\) of **5 Minutes**\.

   1. To change the name of the alarm, for **Name of alarm**, enter a new name\. Alarm names must contain only ASCII characters\.

      If you don't enter a name for the alarm, Amazon CloudWatch automatically creates one for you\.

   1. Choose **Create Alarm**\.

------

## Add recover actions to Amazon CloudWatch alarms<a name="AddingRecoverActions"></a>

You can create an Amazon CloudWatch alarm that monitors an Amazon EC2 instance\. If the instance becomes impaired due to an underlying hardware failure or a problem that requires AWS involvement to repair, you can automatically recover the instance\. Terminated instances cannot be recovered\. A recovered instance is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata\.

CloudWatch prevents you from adding a recovery action to an alarm that is on an instance which does not support recovery actions\.

When the `StatusCheckFailed_System` alarm is triggered, and the recover action is initiated, you are notified by the Amazon SNS topic that you chose when you created the alarm and associated the recover action\. During instance recovery, the instance is migrated during an instance reboot, and any data that is in\-memory is lost\. When the process is complete, information is published to the SNS topic you've configured for the alarm\. Anyone who is subscribed to this SNS topic receives an email notification that includes the status of the recovery attempt and any further instructions\. You notice an instance reboot on the recovered instance\.

**Note**  
The recover action can be used only with `StatusCheckFailed_System`, not with `StatusCheckFailed_Instance`\.

The following problems can cause system status checks to fail:
+ Loss of network connectivity
+ Loss of system power
+ Software issues on the physical host
+ Hardware issues on the physical host that impact network reachability

The recover action is supported only on instances with the following characteristics:
+ Use one of the following instance types: A1, C3, C4, C5, C5a, C5n, C6g, C6gn, Inf1, C6i, M3, M4, M5, M5a, M5n, M5zn, M6g, M6i, P3, R3, R4, R5, R5a, R5b, R5n, R6g, R6i, T2, T3, T3a, T4g, high memory \(virtualized only\), X1, X1e
+ Use `default` or `dedicated` instance tenancy
+ Use EBS volumes only \(do not configure instance store volumes\)\. For more information, see ['Recover this instance' is disabled](https://aws.amazon.com/premiumsupport/knowledge-center/recover-this-instance-cloudwatch-enable/)\.

If your instance has a public IP address, it retains the public IP address after recovery\.

**Important**  
To avoid a race condition between the reboot and recover actions, avoid setting the same number of evaluation periods for a reboot alarm and a recover alarm\. We recommend that you set recover alarms to two evaluation periods of one minute each\. For more information, see [Evaluating an Alarm](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html#alarm-evaluation) in the *Amazon CloudWatch User Guide*\.

------
#### [ New console ]

**To create an alarm to recover an instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance and choose **Actions**, **Monitor and troubleshoot**, **Manage CloudWatch alarms**\.

   Alternatively, you can choose the plus sign \( ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/add-plus.png) \) in the **Alarm status** column\.

1. On the **Manage CloudWatch alarms** page, do the following:

   1. Choose **Create an alarm**\.

   1. To receive an email when the alarm is triggered, for **Alarm notification**, choose an existing Amazon SNS topic\. You first need to create an Amazon SNS topic using the Amazon SNS console\. For more information, see [Using Amazon SNS for application\-to\-person \(A2P\) messaging](https://docs.aws.amazon.com/sns/latest/dg/sns-user-notifications.html) in the *Amazon Simple Notification Service Developer Guide*\.
**Note**  
Users must subscribe to the specified SNS topic to receive email notifications when the alarm is triggered\. The AWS account root user always receives email notifications when automatic instance recovery actions occur, even if an SNS topic is not specified or the root user is not subscribed to the specified SNS topic\.

   1. Toggle on **Alarm action**, and choose **Recover**\.

   1. For **Group samples by** and **Type of data to sample**, choose a statistic and a metric\. In this example, choose **Average** and **Status check failed: system**\.

   1. For **Consecutive period** and **Period**, specify the evaluation period for the alarm\. In this example, enter **2** consecutive periods of **5 Minutes**\.

   1. Amazon CloudWatch automatically creates an alarm name for you\. To change the name, for **Alarm name**, enter a new name\. Alarm names must contain only ASCII characters\.

   1. Choose **Create**\.

------
#### [ Old console ]

**To create an alarm to recover an instance \(Amazon EC2 console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\. On the **Monitoring** tab, choose **Create Alarm**\.

1. In the **Create Alarm** dialog box, do the following:

   1. To receive an email when the alarm is triggered, for **Send a notification to**, choose an existing Amazon SNS topic, or choose **create topic** to create a new one\.

      To create a new topic, for **Send a notification to**, enter a name for the topic, and for **With these recipients**, enter the email addresses of the recipients \(separated by commas\)\. After you create the alarm, you will receive a subscription confirmation email that you must accept before you can get email for this topic\.
**Note**  
Users must subscribe to the specified SNS topic to receive email notifications when the alarm is triggered\.
The AWS account root user always receives email notifications when automatic instance recovery actions occur, even if an SNS topic is not specified\.
The AWS account root user always receives email notifications when automatic instance recovery actions occur, even if it is not subscribed to the specified SNS topic\.

   1. Select **Take the action**, **Recover this instance**\.

   1. For **Whenever**, choose **Status Check Failed \(System\)**\.

   1. For **For at least**, specify the evaluation period for the alarm\. In this example, enter **2** consecutive period\(s\) of **5 Minutes**\.

   1. To change the name of the alarm, for **Name of alarm**, enter a new name\. Alarm names must contain only ASCII characters\.

      If you don't enter a name for the alarm, Amazon CloudWatch automatically creates one for you\.

   1. Choose **Create Alarm**\.

------

## Use the Amazon CloudWatch console to view alarm and action history<a name="ViewAlarmHistory"></a>

You can view alarm and action history in the Amazon CloudWatch console\. Amazon CloudWatch keeps the last two weeks' worth of alarm and action history\.

**To view the history of triggered alarms and actions \(CloudWatch console\)**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Alarms**\.

1. Select an alarm\.

1. The **Details** tab shows the most recent state transition along with the time and metric values\.

1. Choose the **History** tab to view the most recent history entries\.

## Amazon CloudWatch alarm action scenarios<a name="AlarmActionScenarios"></a>

You can use the Amazon EC2 console to create alarm actions that stop or terminate an Amazon EC2 instance when certain conditions are met\. In the following screen capture of the console page where you set the alarm actions, we've numbered the settings\. We've also numbered the settings in the scenarios that follow, to help you create the appropriate actions\.

------
#### [ New console ]

![\[Manage Cloudwatch alarms page\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/manage-cloudwatch-alarms.png)

------
#### [ Old console ]

![\[Create Alarm for dialog box\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/CreateAlarmFor.png)

------

### Scenario 1: Stop idle development and test instances<a name="StopIdleInstance"></a>

Create an alarm that stops an instance used for software development or testing when it has been idle for at least an hour\.


| Setting | Value | 
| --- | --- | 
|  1  |  Stop  | 
|  2  |  Maximum  | 
|  3  |  CPU Utilization  | 
|  4  |  <=  | 
|  5  |  10%  | 
|  6  |  1  | 
|  7  |  1 Hour  | 

### Scenario 2: Stop idle instances<a name="StopLowUtilizationInstance"></a>

Create an alarm that stops an instance and sends an email when the instance has been idle for 24 hours\.


| Setting | Value | 
| --- | --- | 
|  1  |  Stop and email  | 
|  2  |  Average  | 
|  3  |  CPU Utilization  | 
|  4  |  <=  | 
|  5  |  5%  | 
|  6  |  24  | 
|  7  |  1 Hour  | 

### Scenario 3: Send email about web servers with unusually high traffic<a name="StopHighWebTraffic"></a>

Create an alarm that sends email when an instance exceeds 10 GB of outbound network traffic per day\.


| Setting | Value | 
| --- | --- | 
|  1  |  Email  | 
|  2  |  Sum  | 
|  3  |  Network Out  | 
|  4  |  >  | 
|  5  |  10 GB  | 
|  6  |  24  | 
|  7  |  1 Hour  | 

### Scenario 4: Stop web servers with unusually high traffic<a name="StopHighWebTraffic2"></a>

Create an alarm that stops an instance and send a text message \(SMS\) if outbound traffic exceeds 1 GB per hour\.


| Setting | Value | 
| --- | --- | 
|  1  |  Stop and send SMS  | 
|  2  |  Sum  | 
|  3  |  Network Out  | 
|  4  |  >  | 
|  5  |  1 GB  | 
|  6  |  1  | 
|  7  |  1 Hour  | 

### Scenario 5: Stop an impaired instance<a name="StopImpairedInstance"></a>

Create an alarm that stops an instance that fails three consecutive status checks \(performed at 5\-minute intervals\)\.


| Setting | Value | 
| --- | --- | 
|  1  |  Stop  | 
|  2  |  Average  | 
|  3  |  Status Check Failed: System  | 
|  4  |  \-  | 
|  5  |  \-  | 
|  6  |  1  | 
|  7  |  15 Minutes  | 

### Scenario 6: Terminate instances when batch processing jobs are complete<a name="TerminateBatchProcesses"></a>

Create an alarm that terminates an instance that runs batch jobs when it is no longer sending results data\.


| Setting | Value | 
| --- | --- | 
|  1  |  Terminate  | 
|  2  |  Maximum  | 
|  3  |  Network Out  | 
|  4  |  <=  | 
|  5  |  100,000 bytes  | 
|  6  |  1  | 
|  7  |  5 Minutes  | 
# Instance retirement<a name="instance-retirement"></a>

An instance is scheduled to be retired when AWS detects irreparable failure of the underlying hardware that hosts the instance\. When an instance reaches its scheduled retirement date, it is stopped or terminated by AWS\. 
+ If your instance root device is an Amazon EBS volume, the instance is stopped, and you can start it again at any time\. Starting the stopped instance migrates it to new hardware\.
+ If your instance root device is an instance store volume, the instance is terminated, and cannot be used again\.

For more information about the types of instance events, see [Scheduled events for your instances](monitoring-instances-status-check_sched.md)\.

**Topics**
+ [Identify instances scheduled for retirement](#instance-retirement-identify)
+ [Actions to take for EBS\-backed instances scheduled for retirement](#instance-retirement-actions-EBS)
+ [Actions to take for instance\-store backed instances scheduled for retirement](#instance-retirement-actions-instance-store)

## Identify instances scheduled for retirement<a name="instance-retirement-identify"></a>

If your instance is scheduled for retirement, you receive an email prior to the event with the instance ID and retirement date\. You can also check for instances that are scheduled for retirement using the Amazon EC2 console or the command line\.

**Important**  
If an instance is scheduled for retirement, we recommend that you take action as soon as possible because the instance might be unreachable\. \(The email notification you receive states the following: "Due to this degradation your instance could already be unreachable\."\) For more information about the recommended action you should take, see [Check if your instance is reachable](#check-instance)\.

**Topics**
+ [Email notification](#identify-by-email)
+ [Console identification](#identify-in-console-cli)

### Email notification<a name="identify-by-email"></a>

If your instance is scheduled for retirement, you receive an email prior to the event with the instance ID and retirement date\.

The email is sent to the primary account holder and the operations contact\. For more information, see [Adding, changing, or removing alternate contacts](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/manage-account-payment.html#manage-account-payment-alternate-contacts) in the *AWS Billing User Guide*\.

### Console identification<a name="identify-in-console-cli"></a>

If you use an email account that you do not check regularly for instance retirement notifications, you can use the Amazon EC2 console or the command line to determine if any of your instances are scheduled for retirement\.<a name="identify-retiring-instances"></a>

**To identify instances scheduled for retirement using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **EC2 Dashboard**\. Under **Scheduled events**, you can see the events that are associated with your Amazon EC2 instances and volumes, organized by Region\.  
![\[Scheduled events\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/dashboard-scheduled-events.png)

1. If you have an instance with a scheduled event listed, select its link below the Region name to go to the **Events** page\.

1. The **Events** page lists all resources that have events associated with them\. To view instances that are scheduled for retirement, select **Instance resources** from the first filter list, and then **Instance stop or retirement** from the second filter list\.

1. If the filter results show that an instance is scheduled for retirement, select it, and note the date and time in the **Start time** field in the details pane\. This is your instance retirement date\.

**To identify instances scheduled for retirement using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) \(AWS CLI\)
+ [Get\-EC2InstanceStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceStatus.html) \(AWS Tools for Windows PowerShell\)

## Actions to take for EBS\-backed instances scheduled for retirement<a name="instance-retirement-actions-EBS"></a>

To preserve the data on your retiring instance, you can perform one of the following actions\. It's important that you take this action before the instance retirement date to prevent unforeseen downtime and data loss\.

If you are not sure whether your instance is backed by EBS or instance store, see [Determine the root device type of your instance](RootDeviceStorage.md#display-instance-root-device-type)\.

**Check if your instance is reachable**

When you are notified that your instance is scheduled for retirement, we recommend that you take the following action as soon as possible:
+ Check if your instance is reachable by either [connecting](AccessingInstances.md) to or pinging your instance\.
+ If your instance is reachable, you should plan to stop/start your instance at an appropriate time before the scheduled retirement date, when the impact is minimal\. For more information about stopping and starting your instance, and what to expect when your instance is stopped, such as the effect on public, private, and Elastic IP addresses that are associated with your instance, see [Stop and start your instance](Stop_Start.md)\. Note that data on instance store volumes is lost when you stop and start your instance\.
+ If your instance is unreachable, you should take immediate action and perform a [stop/start](Stop_Start.md) to recover your instance\.
+ Alternatively, if you want to [terminate](terminating-instances.md) your instance, plan to do so as soon as possible so that you stop incurring charges for the instance\.

**Create a backup of your instance**  
Create an EBS\-backed AMI from your instance so that you have a backup\. To ensure data integrity, stop the instance before you create the AMI\. You can wait for the scheduled retirement date when the instance is stopped, or stop the instance yourself before the retirement date\. You can start the instance again at any time\. For more information, see [Create an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\.

**Launch a replacement instance**  
After you create an AMI from your instance, you can use the AMI to launch a replacement instance\. From the Amazon EC2 console, select your new AMI and then choose **Actions**, **Launch**\. Follow the wizard to launch your instance\. For more information about each step in the wizard, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\.

## Actions to take for instance\-store backed instances scheduled for retirement<a name="instance-retirement-actions-instance-store"></a>

To preserve the data on your retiring instance, you can perform one of the following actions\. It's important that you take this action before the instance retirement date to prevent unforeseen downtime and data loss\.

**Warning**  
If your instance store\-backed instance passes its retirement date, it is terminated and you cannot recover the instance or any data that was stored on it\. Regardless of the root device of your instance, the data on instance store volumes is lost when the instance is retired, even if the volumes are attached to an EBS\-backed instance\.

**Check if your instance is reachable**

When you are notified that your instance is scheduled for retirement, we recommend that you take the following action as soon as possible:
+ Check if your instance is reachable by either [connecting](AccessingInstances.md) to or pinging your instance\.
+ If your instance is unreachable, there is likely very little that can be done to recover your instance\. For more information, see [Troubleshoot an unreachable instance](instance-console.md)\. AWS will terminate your instance on the scheduled retirement date, so, for an unreachable instance, you can immediately [terminate](terminating-instances.md) the instance yourself\.

**Launch a replacement instance**  
Create an instance store\-backed AMI from your instance using the AMI tools, as described in [Create an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. From the Amazon EC2 console, select your new AMI and then choose **Actions**, **Launch**\. Follow the wizard to launch your instance\. For more information about each step in the wizard, see [Launch an instance using the new launch instance wizard](ec2-launch-instance-wizard.md)\.

**Convert your instance to an EBS\-backed instance**  
Transfer your data to an EBS volume, take a snapshot of the volume, and then create AMI from the snapshot\. You can launch a replacement instance from your new AMI\. For more information, see [Convert your instance store\-backed AMI to an Amazon EBS\-backed AMI](Using_ConvertingS3toEBS.md)\.
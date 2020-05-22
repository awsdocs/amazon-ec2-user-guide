# Instance retirement<a name="instance-retirement"></a>

An instance is scheduled to be retired when AWS detects irreparable failure of the underlying hardware hosting the instance\. When an instance reaches its scheduled retirement date, it is stopped or terminated by AWS\. If your instance root device is an Amazon EBS volume, the instance is stopped, and you can start it again at any time\. Starting the stopped instance migrates it to new hardware\. If your instance root device is an instance store volume, the instance is terminated, and cannot be used again\.

**Topics**
+ [Identifying instances scheduled for retirement](#instance-retirement-identify)
+ [Working with instances scheduled for retirement](#instance-retirement-working)

For more information about types of instance events, see [Scheduled events for your instances](monitoring-instances-status-check_sched.md)\.

## Identifying instances scheduled for retirement<a name="instance-retirement-identify"></a>

If your instance is scheduled for retirement, you'll receive an email prior to the event with the instance ID and retirement date\. This email is sent to the address that's associated with your account; the same email address that you use to log in to the AWS Management Console\. If you use an email account that you do not check regularly, then you can use the Amazon EC2 console or the command line to determine if any of your instances are scheduled for retirement\. To update the contact information for your account, go to the [Account Settings](https://console.aws.amazon.com/billing/home?#/account) page\.<a name="identify-retiring-instances"></a>

**To identify instances scheduled for retirement using the console**

1. Open the Amazon EC2 console\.

1. In the navigation pane, choose **EC2 Dashboard**\. Under **Scheduled Events**, you can see the events associated with your Amazon EC2 instances and volumes, organized by Region\.  
![\[Scheduled events\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/sched-events-instance.png)

1. If you have an instance with a scheduled event listed, select its link below the Region name to go to the **Events** page\.

1. The **Events** page lists all resources with events associated with them\. To view instances that are scheduled for retirement, select **Instance resources** from the first filter list, and then **Instance stop or retirement** from the second filter list\.

1. If the filter results show that an instance is scheduled for retirement, select it, and note the date and time in the **Start time** field in the details pane\. This is your instance retirement date\.

**To identify instances scheduled for retirement using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) \(AWS CLI\)
+ [Get\-EC2InstanceStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceStatus.html) \(AWS Tools for Windows PowerShell\)

## Working with instances scheduled for retirement<a name="instance-retirement-working"></a>

There are a number of actions available to you when your instance is scheduled for retirement\. The action you take depends on whether your instance root device is an Amazon EBS volume, or an instance store volume\. If you do not know what your instance root device type is, you can find out using the Amazon EC2 console or the command line\.

### Determining your instance root device type<a name="instance-retirement-root-device"></a>

**To determine your instance root device type using the console**

1. In the navigation pane, select **Events**\. Use the filter lists to identify retiring instances, as demonstrated in the procedure above, [Identifying instances scheduled for retirement](#identify-retiring-instances)\.

1. In the **Resource Id** column, select the instance ID to go to the **Instances** page\. 

1. Select the instance and locate the **Root device type** field in the **Description** tab\. If the value is `ebs`, then your instance is EBS\-backed\. If the value is `instance-store`, then your instance is instance store\-backed\.

**To determine your instance root device type using the command line**

You can use one of the following commands\. For more information about these command line interfaces, see [Accessing Amazon EC2](concepts.md#access-ec2)\.
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

### Managing instances scheduled for retirement<a name="instance-retirement-actions"></a>

You can perform one of the actions listed below in order to preserve the data on your retiring instance\. It's important that you take this action before the instance retirement date to prevent unforeseen downtime and data loss\. 

**Warning**  
If your instance store\-backed instance passes its retirement date, it is terminated and you cannot recover the instance or any data that was stored on it\. Regardless of the root device of your instance, the data on instance store volumes is lost when the instance is retired, even if they are attached to an EBS\-backed instance\.


| Instance Root Device Type | Action | 
| --- | --- | 
| EBS | Create an EBS\-backed AMI from your instance so that you have a backup\. Wait for the scheduled retirement date \- when the instance is stopped \- or stop the instance yourself before the retirement date\. You can start the instance again at any time\. For more information about stopping and starting your instance, and what to expect when your instance is stopped, such as the effect on public, private and Elastic IP addresses associated with your instance, see [Stop and start your instance](Stop_Start.md)\. | 
| EBS | Create an EBS\-backed AMI from your instance, and launch a replacement instance\. For more information, see [Creating an Amazon EBS\-backed Linux AMI](creating-an-ami-ebs.md)\. | 
| Instance store | Create an instance store\-backed AMI from your instance using the AMI tools, and launch a replacement instance\. For more information, see [Creating an instance store\-backed Linux AMI](creating-an-ami-instance-store.md)\. | 
| Instance store | Convert your instance to an EBS\-backed instance by transferring your data to an EBS volume, taking a snapshot of the volume, and then creating an AMI from the snapshot\. You can launch a replacement instance from your new AMI\. For more information, see [Converting your instance store\-backed AMI to an Amazon EBS\-backed AMI](Using_ConvertingS3toEBS.md)\. | 
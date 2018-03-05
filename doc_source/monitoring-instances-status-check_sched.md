# Scheduled Events for Your Instances<a name="monitoring-instances-status-check_sched"></a>

AWS can schedule events for your instances, such as a reboot, stop/start, or retirement\. These events do not occur frequently\. If one of your instances will be affected by a scheduled event, AWS sends an email to the email address that's associated with your AWS account prior to the scheduled event, with details about the event, including the start and end date\. Depending on the event, you might be able to take action to control the timing of the event\.

To update the contact information for your account so that you can be sure to be notified about scheduled events, go to the [Account Settings](https://console.aws.amazon.com/billing/home?#/account) page\.


+ [Types of Scheduled Events](#types-of-scheduled-events)
+ [Viewing Scheduled Events](#viewing_scheduled_events)
+ [Working with Instances Scheduled to Stop or Retire](#schedevents_actions_retire)
+ [Working with Instances Scheduled for Reboot](#schedevents_actions_reboot)
+ [Working with Instances Scheduled for Maintenance](#schedevents_actions_maintenance)

## Types of Scheduled Events<a name="types-of-scheduled-events"></a>

Amazon EC2 supports the following types of scheduled events for your instances:

+ **Instance stop**: The instance will be stopped\. When you start it again, it's migrated to a new host computer\. Applies only to instances backed by Amazon EBS\.

+ **Instance retirement**: The instance will be stopped or terminated\.

+ **Reboot**: Either the instance will be rebooted \(instance reboot\) or the host computer for the instance will be rebooted \(system reboot\)\.

+ **System maintenance**: The instance might be temporarily affected by network maintenance or power maintenance\.

## Viewing Scheduled Events<a name="viewing_scheduled_events"></a>

In addition to receiving notification of scheduled events in email, you can check for scheduled events\.

**To view scheduled events for your instances using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, click **Events**\. Any resources with an associated event are displayed\. You can filter by resource type, or by specific event types\. You can select the resource to view details\.  
![\[Viewing events using the Events page.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/events-instance-stop.png)

1. Alternatively, in the navigation pane, choose **EC2 Dashboard**\. Any resources with an associated event are displayed under **Scheduled Events**\.  
![\[Viewing events using the dashboard.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/dashboard-scheduled-events.png)

1. Note that some events are also shown for affected resources\. For example, in the navigation pane, choose **Instances**, and then select an instance\. If the instance has an associated instance stop or instance retirement event, it is displayed in the lower pane\.  
![\[Viewing events in the instance details.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/event-instance-retirement.png)

**To view scheduled events for your instances using the command line or API**  
Use the following AWS CLI command:

```
aws ec2 describe-instance-status --instance-id i-1234567890abcdef0
```

The following is example output showing an instance retirement event:

```
{
    "InstanceStatuses": [
        {
            "InstanceStatus": {
                "Status": "ok",
                "Details": [
                    {
                        "Status": "passed",
                        "Name": "reachability"
                    }
                ]
            },
            "AvailabilityZone": "us-west-2a",
            "InstanceId": "i-1234567890abcdef0",
            "InstanceState": {
                "Code": 16,
                "Name": "running"
            },
            "SystemStatus": {
                "Status": "ok",
                "Details": [
                    {
                        "Status": "passed",
                        "Name": "reachability"
                    }
                ]
            },
            "Events": [
                {
                    "Code": "instance-stop",
                    "Description": "The instance is running on degraded hardware",
                    "NotBefore": "2015-05-23T00:00:00.000Z"
                }
            ]
        }
    ]
}
```

Alternatively, use the following commands:

+  [Get\-EC2InstanceStatus](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceStatus.html) \(AWS Tools for Windows PowerShell\) 

+  [DescribeInstanceStatus](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeInstanceStatus.html) \(Amazon EC2 Query API\)

## Working with Instances Scheduled to Stop or Retire<a name="schedevents_actions_retire"></a>

When AWS detects irreparable failure of the underlying host computer for your instance, it schedules the instance to stop or terminate, depending on the type of root device for the instance\. If the root device is an EBS volume, the instance is scheduled to stop\. If the root device is an instance store volume, the instance is scheduled to terminate\. For more information, see [Instance Retirement](instance-retirement.md)\.

**Important**  
Any data stored on instance store volumes is lost when an instance is stopped or terminated\. This includes instance store volumes that are attached to an instance that has an EBS volume as the root device\. Be sure to save data from your instance store volumes that you will need later before the instance is stopped or terminated\.

**Actions for Instances Backed by Amazon EBS**  
You can wait for the instance to stop as scheduled\. Alternatively, you can stop and start the instance yourself, which migrates it to a new host computer\. For more information about stopping your instance, as well as information about the changes to your instance configuration when it's stopped, see [Stop and Start Your Instance](Stop_Start.md)\.

**Actions for Instances Backed by Instance Store**  
We recommend that you launch a replacement instance from your most recent AMI and migrate all necessary data to the replacement instance before the instance is scheduled to terminate\. Then, you can terminate the original instance, or wait for it to terminate as scheduled\.

## Working with Instances Scheduled for Reboot<a name="schedevents_actions_reboot"></a>

When AWS needs to perform tasks such as installing updates or maintaining the underlying host computer, it can schedule an instance or the underlying host computer for the instance for a reboot\. Regardless of any existing instances that are scheduled for reboot, a new instance launch does not require a reboot, as the updates are already applied on the underlying host\.

You can determine whether the reboot event is an instance reboot or a system reboot\. 

**To view the type of scheduled reboot event using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Select **Instance resources** from the filter list, and then select your instance\.

1. In the bottom pane, locate **Event type**\. The value is either `system-reboot` or `instance-reboot`\.

**To view the type of scheduled reboot event using the AWS CLI**  
Use the following [describe\-instance\-status](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command:

```
aws ec2 describe-instance-status --instance-ids i-1234567890abcdef0
```
<a name="schedevents_actions_instancereboot"></a>
**Actions for Instance Reboot**  
You can wait for the instance reboot to occur within its scheduled maintenance window\. Alternatively, you can reboot your instance yourself at a time that is convenient for you\. For more information, see [Reboot Your Instance](ec2-instance-reboot.md)\.

After you reboot your instance, the scheduled event for the instance reboot is canceled immediately and the event's description is updated\. The pending maintenance to the underlying host computer is completed, and you can begin using your instance again after it has fully booted\. 
<a name="schedevents_actions_systemreboot"></a>
**Actions for System Reboot**  
It is not possible for you to reboot the system yourself\. We recommend that you wait for the system reboot to occur during its scheduled maintenance window\. A system reboot typically completes in a matter of minutes, the instance retains its IP address and DNS name, and any data on local instance store volumes is preserved\. After the system reboot has occurred, the scheduled event for the instance is cleared, and you can verify that the software on your instance is operating as you expect\.

Alternatively, if it is necessary to maintain the instance at a different time, you can stop and start an EBS\-backed instance, which migrates it to a new host\. However, the data on the local instance store volumes would not be preserved\. In the case of an instance store\-backed instance, you can launch a replacement instance from your most recent AMI\.

## Working with Instances Scheduled for Maintenance<a name="schedevents_actions_maintenance"></a>

When AWS needs to maintain the underlying host computer for an instance, it schedules the instance for maintenance\. There are two types of maintenance events: network maintenance and power maintenance\.

During network maintenance, scheduled instances lose network connectivity for a brief period of time\. Normal network connectivity to your instance will be restored after maintenance is complete\.

During power maintenance, scheduled instances are taken offline for a brief period, and then rebooted\. When a reboot is performed, all of your instance's configuration settings are retained\.

After your instance has rebooted \(this normally takes a few minutes\), verify that your application is working as expected\. At this point, your instance should no longer have a scheduled event associated with it, or the description of the scheduled event begins with **\[Completed\]**\. It sometimes takes up to 1 hour for this instance status to refresh\. Completed maintenance events are displayed on the Amazon EC2 console dashboard for up to a week\.

**Actions for Instances Backed by Amazon EBS**  
You can wait for the maintenance to occur as scheduled\. Alternatively, you can stop and start the instance, which migrates it to a new host computer\. For more information about stopping your instance, as well as information about the changes to your instance configuration when it's stopped, see [Stop and Start Your Instance](Stop_Start.md)\.

**Actions for Instances Backed by Instance Store**  
You can wait for the maintenance to occur as scheduled\. Alternatively, if you want to maintain normal operation during a scheduled maintenance window, you can launch a replacement instance from your most recent AMI, migrate all necessary data to the replacement instance before the scheduled maintenance window, and then terminate the original instance\.
# Scheduled Events for Your Instances<a name="monitoring-instances-status-check_sched"></a>

AWS can schedule events for your instances, such as a reboot, stop/start, or retirement\. These events do not occur frequently\. If one of your instances will be affected by a scheduled event, AWS sends an email to the email address that's associated with your AWS account prior to the scheduled event\. The email provides details about the event, including the start and end date\. Depending on the event, you might be able to take action to control the timing of the event\.

To update the contact information for your account so that you can be sure to be notified about scheduled events, go to the [Account Settings](https://console.aws.amazon.com/billing/home?#/account) page\.

**Topics**
+ [Types of Scheduled Events](#types-of-scheduled-events)
+ [Viewing Scheduled Events](#viewing_scheduled_events)
+ [Working with Instances Scheduled to Stop or Retire](#schedevents_actions_retire)
+ [Working with Instances Scheduled for Reboot](#schedevents_actions_reboot)
+ [Working with Instances Scheduled for Maintenance](#schedevents_actions_maintenance)

## Types of Scheduled Events<a name="types-of-scheduled-events"></a>

Amazon EC2 supports the following types of scheduled events for your instances:
+ **Instance stop**: At the scheduled time, the instance is stopped\. When you start it again, it's migrated to a new host\. Applies only to instances backed by Amazon EBS\.
+ **Instance retirement**: At the scheduled time, the instance is stopped if it is backed by Amazon EBS, or terminated if it is backed by instance store\.
+ **Instance reboot**: At the scheduled time, the instance is rebooted\.
+ **System reboot**: At the scheduled time, the host for the instance is rebooted\.
+ **System maintenance**: At the scheduled time, the instance might be temporarily affected by network maintenance or power maintenance\.

## Viewing Scheduled Events<a name="viewing_scheduled_events"></a>

In addition to receiving notification of scheduled events in email, you can check for scheduled events using one of the following methods\.

**To view scheduled events for your instances using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\. Any resources with an associated event are displayed\. You can filter by resource type, or by specific event types\. You can select the resource to view details\.  
![\[Viewing events using the Events page.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/events-instance-stop.png)

1. Alternatively, in the navigation pane, choose **EC2 Dashboard**\. Any resources with an associated event are displayed under **Scheduled Events**\.  
![\[Viewing events using the dashboard.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/dashboard-scheduled-events.png)

1. Some events are also shown for affected resources\. For example, in the navigation pane, choose **Instances** and select an instance\. If the instance has an associated instance stop or instance retirement event, it is displayed in the lower pane\.  
![\[Viewing events in the instance details.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/event-instance-retirement.png)

**To view scheduled events for your instances using the AWS CLI**
+ Use the following [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command\.

  ```
  aws ec2 describe-instance-status --instance-id i-1234567890abcdef0 --query "InstanceStatuses[].Events"
  ```

  The following example output shows a reboot event\.

  ```
  [
      "Events": [
          {
              "InstanceEventId": "instance-event-0d59937288b749b32",
              "Code": "system-reboot",
              "Description": "The instance is scheduled for a reboot",
              "NotAfter": "2019-03-15T22:00:00.000Z",
              "NotBefore": "2019-03-14T20:00:00.000Z",
              "NotBeforeDeadline": "2019-04-05T11:00:00.000Z"
           }
  
      ]
  ]
  ```

  The following example output shows an instance retirement event\.

  ```
  [
      "Events": [
          {
              "InstanceEventId": "instance-event-0e439355b779n26",                        
              "Code": "instance-stop",
              "Description": "The instance is running on degraded hardware",
              "NotBefore": "2015-05-23T00:00:00.000Z"
          }
      ]
  ]
  ```

**To view scheduled events for your instances using the AWS Tools for Windows PowerShell**
+ Use the following [Get\-EC2InstanceStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2InstanceStatus.html) command\.

  ```
  PS C:\> (Get-EC2InstanceStatus -InstanceId i-1234567890abcdef0).Events
  ```

  The following example output shows an instance retirement event\.

  ```
  Code         : instance-stop
  Description  : The instance is running on degraded hardware
  NotBefore    : 5/23/2015 12:00:00 AM
  ```

**To view scheduled events for your instances using instance metadata**
+ You can retrieve information about active maintenance events for your instances from the [instance metadata](ec2-instance-metadata.md), as follows\.

------
#### [ IMDSv2 ]

  ```
  [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
  && curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/events/maintenance/scheduled
  ```

------
#### [ IMDSv1 ]

  ```
  [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/events/maintenance/scheduled
  ```

------

  The following is example output with information about a scheduled system reboot event, in JSON format\.

  ```
  [ 
    {
      "NotBefore" : "21 Jan 2019 09:00:43 GMT",
      "Code" : "system-reboot",
      "Description" : "scheduled reboot",
      "EventId" : "instance-event-0d59937288b749b32",
      "NotAfter" : "21 Jan 2019 09:17:23 GMT",
      "State" : "active"
    } 
  ]
  ```<a name="viewing-event-history"></a>

**To view event history about completed or canceled events for your instances using instance metadata**
+ You can retrieve information about completed or canceled events for your instances from the [instance metadata](ec2-instance-metadata.md), as follows\.

------
#### [ IMDSv2 ]

  ```
  [ec2-user ~]$ TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
  && curl -H "X-aws-ec2-metadata-token: $TOKEN" –v http://169.254.169.254/latest/meta-data/events/maintenance/history
  ```

------
#### [ IMDSv1 ]

  ```
  [ec2-user ~]$ curl http://169.254.169.254/latest/meta-data/events/maintenance/history
  ```

------

  The following is example output with information about a system reboot event that was canceled, and a system reboot event that was completed, in JSON format\.

  ```
  [ 
    {
      "NotBefore" : "21 Jan 2019 09:00:43 GMT",
      "Code" : "system-reboot",
      "Description" : "[Canceled] scheduled reboot",
      "EventId" : "instance-event-0d59937288b749b32",
      "NotAfter" : "21 Jan 2019 09:17:23 GMT",
      "State" : "canceled"
    }, 
    {
      "NotBefore" : "29 Jan 2019 09:00:43 GMT",
      "Code" : "system-reboot",
      "Description" : "[Completed] scheduled reboot",
      "EventId" : "instance-event-0d59937288b749b32",
      "NotAfter" : "29 Jan 2019 09:17:23 GMT",
      "State" : "completed"
    }
  ]
  ```

## Working with Instances Scheduled to Stop or Retire<a name="schedevents_actions_retire"></a>

When AWS detects irreparable failure of the underlying host for your instance, it schedules the instance to stop or terminate, depending on the type of root device for the instance\. If the root device is an EBS volume, the instance is scheduled to stop\. If the root device is an instance store volume, the instance is scheduled to terminate\. For more information, see [Instance Retirement](instance-retirement.md)\.

**Important**  
Any data stored on instance store volumes is lost when an instance is stopped or terminated\. This includes instance store volumes that are attached to an instance that has an EBS volume as the root device\. Be sure to save data from your instance store volumes that you might need later before the instance is stopped or terminated\.

**Actions for Instances Backed by Amazon EBS**  
You can wait for the instance to stop as scheduled\. Alternatively, you can stop and start the instance yourself, which migrates it to a new host\. For more information about stopping your instance, in addition to information about the changes to your instance configuration when it's stopped, see [Stop and Start Your Instance](Stop_Start.md)\.

You can automate an immediate stop and start in response to a scheduled instance stop event\. For more information, see [Automating Actions for EC2 Instances](https://docs.aws.amazon.com/health/latest/ug/cloudwatch-events-health.html#automating-instance-actions) in the *AWS Health User Guide*\.

**Actions for Instances Backed by Instance Store**  
We recommend that you launch a replacement instance from your most recent AMI and migrate all necessary data to the replacement instance before the instance is scheduled to terminate\. Then, you can terminate the original instance, or wait for it to terminate as scheduled\.

## Working with Instances Scheduled for Reboot<a name="schedevents_actions_reboot"></a>

When AWS must perform tasks such as installing updates or maintaining the underlying host, it can schedule the instance or the underlying host for a reboot\. You can [reschedule most reboot events](#reschedule-reboot-event) so that your instance is rebooted at a specific date and time that suits you\.

If you stop your linked [EC2\-Classic instance](vpc-classiclink.md#classiclink-limitations), it is automatically unlinked from the VPC and the VPC security groups are no longer associated with the instance\. You can link your instance to the VPC again after you've restarted it\.

### Viewing the Reboot Event Type<a name="view-type-of-scheduled-reboot"></a>

You can view whether a reboot event is an instance reboot or a system reboot using the AWS Management Console, AWS CLI, or Amazon EC2 API\.

**To view the type of scheduled reboot event \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Instance resources** from the filter list\.

1. For each instance, view the value in the **Event Type** column\. The value is either `system-reboot` or `instance-reboot`\.

**To view the type of scheduled reboot event \(AWS CLI\)**
+ Use the following [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command\.

  ```
  aws ec2 describe-instance-status --instance-id i-1234567890abcdef0
  ```

  For scheduled reboot events, the value for `Code` is either `system-reboot` or `instance-reboot`\. The following example output shows a `system-reboot` event\.

  ```
  [
      "Events": [
                  {
                      "InstanceEventId": "instance-event-0d59937288b749b32",
                      "Code": "system-reboot",
                      "Description": "The instance is scheduled for a reboot",
                      "NotAfter": "2019-03-14T22:00:00.000Z",
                      "NotBefore": "2019-03-14T20:00:00.000Z",
                      "NotBeforeDeadline": "2019-04-05T11:00:00.000Z"
                  }
                ]
  ]
  ```
<a name="schedevents_actions_instancereboot"></a>
**Actions for Instance Reboot**  
You can wait for the instance reboot to occur within its scheduled maintenance window, [reschedule](#reschedule-reboot-event) the instance reboot to a date and time that suits you, or [reboot](ec2-instance-reboot.md) the instance yourself at a time that is convenient for you\.

After your instance is rebooted, the scheduled event is cleared and the event's description is updated\. The pending maintenance to the underlying host is completed, and you can begin using your instance again after it has fully booted\.
<a name="schedevents_actions_systemreboot"></a>
**Actions for System Reboot**  
It is not possible for you to reboot the system yourself\. You can wait for the system reboot to occur during its scheduled maintenance window, or you can [reschedule](#reschedule-reboot-event) the system reboot to a date and time that suits you\. A system reboot typically completes in a matter of minutes\. After the system reboot has occurred, the instance retains its IP address and DNS name, and any data on local instance store volumes is preserved\. After the system reboot is complete, the scheduled event for the instance is cleared, and you can verify that the software on your instance is operating as expected\.

Alternatively, if it is necessary to maintain the instance at a different time and you can't reschedule the system reboot, then you can stop and start an Amazon EBS\-backed instance, which migrates it to a new host\. However, the data on the local instance store volumes is not preserved\. You can also automate an immediate instance stop and start in response to a scheduled system reboot event\. For more information, see [Automating Actions for EC2 Instances](https://docs.aws.amazon.com/health/latest/ug/cloudwatch-events-health.html#automating-instance-actions) in the *AWS Health User Guide*\. For an instance store\-backed instance, if you can't reschedule the system reboot, then you can launch a replacement instance from your most recent AMI, migrate all necessary data to the replacement instance before the scheduled maintenance window, and then terminate the original instance\.

### Rescheduling a Reboot Event<a name="reschedule-reboot-event"></a>

You can reschedule most reboot events so that your instance is rebooted at a specific date and time that suits you\.

**To reschedule a reboot event \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Instance resources** from the filter list\.

1. Select one or more instances, and then choose **Actions**, **Schedule Event**\.

   Only events that have an event deadline date, indicated by a value for **Event Deadline**, can be rescheduled\. 

1. For **Event start time**, enter a new date and time for the reboot\. The new date and time must fall before the **Event Deadline**\.

1. Choose **Schedule Event**\.

   It might take 1\-2 minutes for the updated event start time to be reflected in the console\.

**To reschedule a reboot event \(AWS CLI\)**

1. Only events that have an event deadline date, indicated by a value for `NotBeforeDeadline`, can be rescheduled\. Use the following [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command to view the `NotBeforeDeadline` parameter value\.

   ```
   aws ec2 describe-instance-status --instance-id i-1234567890abcdef0
   ```

   The following example output shows a `system-reboot` event that can be rescheduled because `NotBeforeDeadline` contains a value\.

   ```
   [
       "Events": [
                   {
                       "InstanceEventId": "instance-event-0d59937288b749b32",
                       "Code": "system-reboot",
                       "Description": "The instance is scheduled for a reboot",
                       "NotAfter": "2019-03-14T22:00:00.000Z",
                       "NotBefore": "2019-03-14T20:00:00.000Z",
                       "NotBeforeDeadline": "2019-04-05T11:00:00.000Z"
                   }
                 ]
   ]
   ```

1. To reschedule the event, use the [modify\-instance\-event\-start\-time](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-event-start-time.html) command\. Specify the new event start time using the `not-before` parameter\. The new event start time must fall before the `NotBeforeDeadline`\.

   ```
   aws ec2 modify-instance-event-start-time --instance-id i-1234567890abcdef0 --instance-event-id instance-event-0d59937288b749b32 --not-before 2019-03-25T10:00:00.000
   ```

   It might take 1\-2 minutes before the [describe\-instance\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-status.html) command returns the updated `not-before` parameter value\.

**Limitations for Reboot Events**
+ Only reboot events with an event deadline date can be rescheduled\. The event can be rescheduled up to the event deadline date\. The **Event Deadline** column in the console and the `NotBeforeDeadline` field in the AWS CLI indicate if the event has a deadline date\.
+ Only reboot events that have not yet started can be rescheduled\. The **Start Time** column in the console and the `NotBefore` field in the AWS CLI indicate the event start time\. Reboot events that are scheduled to start in the next 5 minutes cannot be rescheduled\.
+ The new event start time must be at least 60 minutes from the current time\.
+ If you reschedule multiple events using the console, the event deadline date is determined by the event with earliest event deadline date\. 

## Working with Instances Scheduled for Maintenance<a name="schedevents_actions_maintenance"></a>

When AWS must maintain the underlying host for an instance, it schedules the instance for maintenance\. There are two types of maintenance events: network maintenance and power maintenance\.

During network maintenance, scheduled instances lose network connectivity for a brief period of time\. Normal network connectivity to your instance will be restored after maintenance is complete\.

During power maintenance, scheduled instances are taken offline for a brief period, and then rebooted\. When a reboot is performed, all of your instance's configuration settings are retained\.

After your instance has rebooted \(this normally takes a few minutes\), verify that your application is working as expected\. At this point, your instance should no longer have a scheduled event associated with it, or the description of the scheduled event begins with **\[Completed\]**\. It sometimes takes up to 1 hour for the instance status description to refresh\. Completed maintenance events are displayed on the Amazon EC2 console dashboard for up to a week\.

**Actions for Instances Backed by Amazon EBS**  
You can wait for the maintenance to occur as scheduled\. Alternatively, you can stop and start the instance, which migrates it to a new host\. For more information about stopping your instance, in addition to information about the changes to your instance configuration when it's stopped, see [Stop and Start Your Instance](Stop_Start.md)\.

You can automate an immediate stop and start in response to a scheduled maintenance event\. For more information, see [Automating Actions for EC2 Instances](https://docs.aws.amazon.com/health/latest/ug/cloudwatch-events-health.html#automating-instance-actions) in the *AWS Health User Guide*\.

**Actions for Instances Backed by Instance Store**  
You can wait for the maintenance to occur as scheduled\. Alternatively, if you want to maintain normal operation during a scheduled maintenance window, you can launch a replacement instance from your most recent AMI, migrate all necessary data to the replacement instance before the scheduled maintenance window, and then terminate the original instance\.
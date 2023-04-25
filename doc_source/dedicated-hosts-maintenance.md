# Host maintenance<a name="dedicated-hosts-maintenance"></a>

With host maintenance, your Amazon EC2 instances on a degraded Dedicated Host are automatically rebooted on a newly allocated Dedicated Host during a scheduled maintenance event\. This helps reduce application downtime and offloads the undifferentiated heavy\-lift of maintenance to AWS\. Host maintenance is also performed for planned and routine Amazon EC2 maintenance\.

Host maintenance is supported on all new Dedicated Host allocations made through Amazon EC2 console\. For any Dedicated Host in your AWS account or any new Dedicated Host allocated through [AllocateHosts](https://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_AllocateHosts.html) API, you can configure host maintenance for supported Dedicated Hosts\. For more information, see [Configuring host maintenance](#dedicated-hosts-maintenance-configuring)\.

**Topics**
+ [Host maintenance basics](#dedicated-hosts-maintenance-basics)
+ [Host maintenance versus host recovery](#dedicated-hosts-maintenance-differences)
+ [Supported instance types](#dedicated-hosts-maintenance-supported-instances)
+ [Instances on Dedicated Host](#dedicated-hosts-maintenance-instances)
+ [Configuring host maintenance](#dedicated-hosts-maintenance-configuring)
+ [Maintenance event](#dedicated-hosts-maintenance-event)
+ [Host maintenance states](#dedicated-hosts-maintenance-states)
+ [Related services](#dedicated-hosts-maintenance-related)
+ [Pricing](#dedicated-hosts-maintenance-pricing)

## Host maintenance basics<a name="dedicated-hosts-maintenance-basics"></a>

When degradation is detected on a Dedicated Host, a new Dedicated Host is allocated\. Degradation can be caused by degradation of the underlying hardware or detection of certain problematic conditions\. Your instances on the degraded Dedicated Host are scheduled to be automatically rebooted on the newly allocated Dedicated Host\.

 The replacement Dedicated Host receives a new host ID, but retains the same attributes as the original Dedicated Host\. These attributes include the following\.
+ Auto placement settings
+ Availability Zone
+ Reservation
+ Host affinity
+ Host maintenance settings
+ Host recovery settings
+ Instance type
+ Tags

Host maintenance is available in all AWS Regions for all supported Dedicated Hosts\. For more information about Dedicated Hosts where host maintenance is not supported, see [Limitations](#dedicated-hosts-maintenance-basics-limitations)\.

Your degraded Dedicated Host is released after all of your instances have been rebooted to a new Dedicated Host or stopped\. You can access your instances on the degraded Dedicated Host before the scheduled maintenance event, but launching instances on the degraded Dedicated Host is not supported\.

You can use the newly allocated Dedicated Host to launch new instances on the host before the scheduled maintenance event\. Some capacity on the new host is reserved for rebooting instances from the degraded host\. For more information, see [Instances on Dedicated Host](#dedicated-hosts-maintenance-instances)\.

### Limitations<a name="dedicated-hosts-maintenance-basics-limitations"></a>
+ Host maintenance is not supported in AWS Outposts, AWS Local Zones, and AWS Wavelength Zones\.
+ Host maintenance cannot be turned on or off for hosts already within a host resource group\. Hosts added to a host resource group retain their host maintenance setting\. For more information, see [Host resource groups](https://docs.aws.amazon.com/license-manager/latest/userguide/host-resource-groups.html)\.
+ Host maintenance is only supported on specific instance types\. For more information, see [Supported instance types](#dedicated-hosts-maintenance-supported-instances)\.

## Host maintenance versus host recovery<a name="dedicated-hosts-maintenance-differences"></a>

The following table shows the main differences between host recovery and host maintenance\.


****  

|  | Host recovery | Host maintenance | 
| --- | --- | --- | 
| Accessibility | Unreachable | Reachable | 
| State | under\-assessment | permanent\-failure | 
| Action | Recovery is immediate | Maintenance is scheduled | 
| Scheduling flexibility | Cannot be rescheduled | Can be rescheduled | 
| Host Resource Group | Supported | Not supported | 

For more information about host recovery, see [Host recovery](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-recovery.html)\.

## Supported instance types<a name="dedicated-hosts-maintenance-supported-instances"></a>

Host maintenance is supported for the following instance families: `A1`, `C4`, `C5`, `C5n`, `C6a`, `C6g`, `C6i`, `Inf1`, `G3`, `G5g`, `M4`, `M5`, `M5n`, `M5zn`, `M6a`, `M6g`, `M6i`, `P2`, `P3`, `R4`, `R5`, `R5b`, `R5n`, `R6g`, `R6i`, `T3`, `u-6tb1`, `u-9tb1`, `u-12tb1`, `u-18tb1`, `u-24tb1`, and `X2iezn`\.

## Instances on Dedicated Host<a name="dedicated-hosts-maintenance-instances"></a>

During the scheduled maintenance event, the instances on your degraded host are rebooted on a newly allocated Dedicated Host\. The instances retain the same attributes as the ones on your degraded host, including the following\.
+ Amazon EBS volume attachments
+ Elastic IP addresses
+ Instance ID
+ Instance metadata
+ Private IP address

You can **stop and start an instance** on the degraded host at any point before the scheduled maintenance event is initiated\. Doing this reboots your instance on to another host, and your instance won't undergo scheduled maintenance\. You must update your instance's host affinity to the new host where you want to reboot your instance\. For more information, see [Stop and start your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/Stop_Start.html)\.

**Note**  
The data on any local store volume is not preserved when you stop and start your instance\.

 Instances with an **instance store volume** as the root device are terminated after the specified termination date\. Any data on the instance store volumes is deleted when the instances are terminated\. Terminated instances are permanently deleted, and cannot be started again\. For instances with instance store volumes as the root device, we recommend launching replacement instances on a different Dedicated Host using the most recent Amazon Machine Image, and migrating all available data to the replacement instances before the specified termination date\. For more information, see [Instance retirement](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-retirement.html#instance-retirement-working.)\.

Instances that **cannot be rebooted** automatically are stopped after the specified date\. You can start these instances again on a different host\. Instances using an Amazon EBS volume as a root device continue to use the same Amazon EBS volume after being started on a new host\.

 You can set the **order of instance reboot** by rescheduling the start time of an instance reboot in [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

## Configuring host maintenance<a name="dedicated-hosts-maintenance-configuring"></a>

You can configure host maintenance for all supported Dedicated Hosts via AWS Management Console or AWS CLI\. See the following table for more details\.

------
#### [ AWS Management Console ]

**To enable host maintenance for your Dedicated Host using AWS Management Console\.**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host > **Actions** > **Modify host**\.

1. Select *on* in the **Host maintenance** field\.

**To disable host maintenance for your Dedicated Host using AWS Management Console\.**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host > **Actions** > **Modify host**\.

1. Select *off* in the **Host maintenance** field\.

**To view the host maintenance configuration for your Dedicated Host using AWS Management Console\.**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host, and in the **Description** tab, review the **Host maintenance** field\.

------
#### [ AWS CLI ]

**To enable or disable host maintenance for your new Dedicated Host during allocation using AWS CLI\.**  
Use the [allocate\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-hosts.html) command\.

**Enable**

```
aws ec2 allocate-hosts --region us-east-1 --quantity 1 --instance-type m3.large --availability-zone us-east-1b --host-maintenance on
```

**Disable**

```
aws ec2 allocate-hosts --region us-east-1 --quantity 1 --instance-type m3.large --availability-zone us-east-1b --host-maintenance off
```

**To enable or disable host maintenance for your existing Dedicated Host using AWS CLI\.**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) command\.

**Enable**

```
aws ec2 modify-hosts --region us-east-1 --host-maintenance on --host-ids h-0d123456bbf78910d
```

**Disable**

```
aws ec2 modify-hosts --region us-east-1 --host-maintenance off --host-ids h-0d123456bbf78910d
```

**To view the host maintenance configuration for your Dedicated Host using AWS CLI\.**  
Use the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) command\.

```
aws ec2 describe-hosts --region us-east-1 --host-ids h-0d123456bbf78910d
```

------

**Note**  
If you disable host maintenance, you receive an email notification to evict the degraded host and manually migrate your instances to another host within 28 days\. A replacement host is allocated if you have Dedicated Host reservation\. After 28 days, the instances running on the degraded host are terminated, and the host is released automatically\.

## Maintenance event<a name="dedicated-hosts-maintenance-event"></a>

On detection of degradation, a maintenance event is scheduled 14 days later, to reboot your instances on a new Dedicated Host\. You receive an email notification providing details about the degraded host, scheduled maintenance event, and maintenance time slots\. For more information, see [View scheduled events](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-instances-status-check_sched.html#viewing_scheduled_events)\.

You can reschedule the maintenance event for any day up to seven days after the date of the scheduled event\. For more information about rescheduling, see [Reschedule a scheduled event](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-instances-status-check_sched.html#reschedule-event)\.

The maintenance event usually takes a few minutes to complete\. In the rare case of unsuccessful event, you receive an email notification to evict the instances on the degraded host within a specified time frame\.

## Host maintenance states<a name="dedicated-hosts-maintenance-states"></a>

Your Dedicated Host is set to `permanent-failure` state when degradation is detected\. You cannot launch instances on a Dedicated Host in the state of `permanent-failure`\. On completion of maintenance event, the degraded host is released and put in the state of `released, permanent-failure`\.

After detecting degradation on a Dedicated Host and before scheduling a maintenance event, host maintenance automatically allocates a new Dedicated Host in your account\. This newly allocated replacement host stays in a `pending` state until a maintenance event is scheduled\. Once the maintenance event is scheduled, the new Dedicated Host moves to the `available` state\. You can launch new instances on this host at any time, even before the maintenance event\.

**Note**  
The capacity required to migrate existing instances from the degraded host to the new host is made unavailable to enable a smooth transition during the maintenance event\.

## Related services<a name="dedicated-hosts-maintenance-related"></a>

Dedicated Host integrates with **AWS License Manager**—Tracks licenses across your Amazon EC2 Dedicated Hosts \(supported only in Regions in which AWS License Manager is available\)\. For more information, see the [AWS License Manager User Guide](https://docs.aws.amazon.com/license-manager/latest/userguide/license-manager.html)\.

You must have sufficient licenses in your AWS account for your new Dedicated Host\. The licenses associated with your degraded host are released when the host is released after the completion of the scheduled maintenance event\.

## Pricing<a name="dedicated-hosts-maintenance-pricing"></a>

There are no additional charges for using host maintenance, but the usual Dedicated Host charges apply\. For more information, see [ Amazon EC2 Dedicated Hosts Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/)\.

As soon as host maintenance is initiated, you are no longer billed for the degraded Dedicated Host\. Billing for the newly allocated Dedicated Host begins only after it enters the `available` state\.

If the degraded Dedicated Host was billed using the On\-Demand rate, the newly allocated Dedicated Host is also billed using the On\-Demand rate\. If the degraded Dedicated Host had an active Dedicated Host Reservation, it is transferred to the new Dedicated Host\.
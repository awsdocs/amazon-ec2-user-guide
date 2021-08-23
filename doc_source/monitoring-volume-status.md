# Monitor the status of your volumes<a name="monitoring-volume-status"></a>

Amazon Web Services \(AWS\) automatically provides data that you can use to monitor your Amazon Elastic Block Store \(Amazon EBS\) volumes\.

**Topics**
+ [EBS volume status checks](#monitoring-volume-checks)
+ [EBS volume events](#monitoring-vol-events)
+ [Work with an impaired volume](#work_volumes_impaired)
+ [Work with the Auto\-Enabled IO volume attribute](#volumeIO)

For additional monitoring information, see [Amazon CloudWatch metrics for Amazon EBS](using_cloudwatch_ebs.md) and [Amazon CloudWatch Events for Amazon EBS](ebs-cloud-watch-events.md)\.

## EBS volume status checks<a name="monitoring-volume-checks"></a>

Volume status checks enable you to better understand, track, and manage potential inconsistencies in the data on an Amazon EBS volume\. They are designed to provide you with the information that you need to determine whether your Amazon EBS volumes are impaired, and to help you control how a potentially inconsistent volume is handled\.

Volume status checks are automated tests that run every 5 minutes and return a pass or fail status\. If all checks pass, the status of the volume is `ok`\. If a check fails, the status of the volume is `impaired`\. If the status is `insufficient-data`, the checks may still be in progress on the volume\. You can view the results of volume status checks to identify any impaired volumes and take any necessary actions\.

When Amazon EBS determines that a volume's data is potentially inconsistent, the default is that it disables I/O to the volume from any attached EC2 instances, which helps to prevent data corruption\. After I/O is disabled, the next volume status check fails, and the volume status is `impaired`\. In addition, you'll see an event that lets you know that I/O is disabled, and that you can resolve the impaired status of the volume by enabling I/O to the volume\. We wait until you enable I/O to give you the opportunity to decide whether to continue to let your instances use the volume, or to run a consistency check using a command, such as fsck, before doing so\.

**Note**  
Volume status is based on the volume status checks, and does not reflect the volume state\. Therefore, volume status does not indicate volumes in the `error` state \(for example, when a volume is incapable of accepting I/O\.\) For information about volume states, see [Volume state](ebs-describing-volumes.md#volume-state)\.

If the consistency of a particular volume is not a concern, and you'd prefer that the volume be made available immediately if it's impaired, you can override the default behavior by configuring the volume to automatically enable I/O\. If you enable the **Auto\-Enable IO** volume attribute \(`autoEnableIO` in the API\), the volume status check continues to pass\. In addition, you'll see an event that lets you know that the volume was determined to be potentially inconsistent, but that its I/O was automatically enabled\. This enables you to check the volume's consistency or replace it at a later time\.

The I/O performance status check compares actual volume performance to the expected performance of a volume\. It alerts you if the volume is performing below expectations\. This status check is available only for Provisioned IOPS SSD \(`io1` and `io2`\) volumes that are attached to an instance\. The status check is not valid for General Purpose SSD \(`gp2` and `gp3`\), Throughput Optimized HDD \(`st1`\), Cold HDD \(`sc1`\), or Magnetic\(`standard`\) volumes\. The I/O performance status check is performed once every minute, and CloudWatch collects this data every 5 minutes\. It might take up to 5 minutes from the moment that you attach an `io1` or `io2` volume to an instance for the status check to report the I/O performance status\.

**Important**  
While initializing Provisioned IOPS SSD volumes that were restored from snapshots, the performance of the volume may drop below 50 percent of its expected level, which causes the volume to display a `warning` state in the **I/O Performance** status check\. This is expected, and you can ignore the `warning` state on Provisioned IOPS SSD volumes while you are initializing them\. For more information, see [Initialize Amazon EBS volumes](ebs-initialize.md)\.

The following table lists statuses for Amazon EBS volumes\.


| Volume status | I/O enabled status | I/O performance status \(`io1` and `io2` volumes only\) | 
| --- | --- | --- | 
|  `ok`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\)  |  Normal \(Volume performance is as expected\)  | 
|  `warning`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\)  |  Degraded \(Volume performance is below expectations\) Severely Degraded \(Volume performance is well below expectations\)  | 
|  `impaired`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\) Disabled \(Volume is offline and pending recovery, or is waiting for the user to enable I/O\)  |  Stalled \(Volume performance is severely impacted\) Not Available \(Unable to determine I/O performance because I/O is disabled\)  | 
|  `insufficient-data`  |  Enabled \(I/O Enabled or I/O Auto\-Enabled\) Insufficient Data  |  Insufficient Data  | 

You can view and work with status checks using the following methods\.

------
#### [ Console ]

**To view status checks**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. The **Volume Status** column displays the operational status of each volume\.

1. To view the status details of a volume, select the volume and choose **Status Checks**\.  
![\[Viewing EBS volume status\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/AutoEnableSetting_statustab_gwt.png)

1. If you have a volume with a failed status check \(status is **impaired**\), see [Work with an impaired volume](#work_volumes_impaired)\.

Alternatively, you can choose **Events** in the navigator to view all the events for your instances and volumes\. For more information, see [EBS volume events](#monitoring-vol-events)\.

------
#### [ AWS CLI ]

**To view volume status information**

Use one of the following commands\.
+ [describe\-volume\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volume-status.html) \(AWS CLI\)
+ [Get\-EC2VolumeStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VolumeStatus.html) \(AWS Tools for Windows PowerShell\)

For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

------

## EBS volume events<a name="monitoring-vol-events"></a>

When Amazon EBS determines that a volume's data is potentially inconsistent, it disables I/O to the volume from any attached EC2 instances by default\. This causes the volume status check to fail, and creates a volume status event that indicates the cause of the failure\. 

To automatically enable I/O on a volume with potential data inconsistencies, change the setting of the **Auto\-Enabled IO** volume attribute \(`autoEnableIO` in the API\)\. For more information about changing this attribute, see [Work with an impaired volume](#work_volumes_impaired)\.

Each event includes a start time that indicates the time at which the event occurred, and a duration that indicates how long I/O for the volume was disabled\. The end time is added to the event when I/O for the volume is enabled\.

Volume status events include one of the following descriptions:

`Awaiting Action: Enable IO`  
Volume data is potentially inconsistent\. I/O is disabled for the volume until you explicitly enable it\. The event description changes to IO Enabled after you explicitly enable I/O\.

`IO Enabled`  
I/O operations were explicitly enabled for this volume\.

`IO Auto-Enabled`  
I/O operations were automatically enabled on this volume after an event occurred\. We recommend that you check for data inconsistencies before continuing to use the data\.

`Normal`  
For `io1`, `io2`, and `gp3` volumes only\. Volume performance is as expected\.

`Degraded`  
For `io1`, `io2`, and `gp3` volumes only\. Volume performance is below expectations\.

`Severely Degraded`  
For `io1`, `io2`, and `gp3` volumes only\. Volume performance is well below expectations\.

`Stalled`  
For `io1`, `io2`, and `gp3` volumes only\. Volume performance is severely impacted\.

You can view events for your volumes using the following methods\. 

------
#### [ Console ]

**To view events for your volumes**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\. All instances and volumes that have events are listed\.

1. You can filter by volume to view only volume status\. You can also filter on specific status types\.

1. Select a volume to view its specific event\.  
![\[Viewing volume events\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ViewVolEvents-gwt.png)

------
#### [ AWS CLI ]

**To view events for your volumes**

Use one of the following commands\.
+ [describe\-volume\-status](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volume-status.html) \(AWS CLI\)
+ [Get\-EC2VolumeStatus](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VolumeStatus.html) \(AWS Tools for Windows PowerShell\)

For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.

------

If you have a volume where I/O is disabled, see [Work with an impaired volume](#work_volumes_impaired)\. If you have a volume where I/O performance is below normal, this might be a temporary condition due to an action you have taken \(for example, creating a snapshot of a volume during peak usage, running the volume on an instance that cannot support the I/O bandwidth required, accessing data on the volume for the first time, etc\.\)\.

## Work with an impaired volume<a name="work_volumes_impaired"></a>

Use the following options if a volume is impaired because the volume's data is potentially inconsistent\.

**Topics**
+ [Option 1: Perform a consistency check on the volume attached to its instance](#work_volumes_impaired_option1)
+ [Option 2: Perform a consistency check on the volume using another instance](#work_volumes_impaired_option2)
+ [Option 3: Delete the volume if you no longer need it](#work_volumes_impaired_option3)

### Option 1: Perform a consistency check on the volume attached to its instance<a name="work_volumes_impaired_option1"></a>

The simplest option is to enable I/O and then perform a data consistency check on the volume while the volume is still attached to its Amazon EC2 instance\.

**To perform a consistency check on an attached volume**

1. Stop any applications from using the volume\.

1. Enable I/O on the volume\. Use one of the following methods\.

------
#### [ Console ]

   1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

   1. In the navigation pane, choose **Volumes**\.

   1. Select the volume on which to enable I/O operations\. 

   1. In the details pane, choose **Enable Volume IO**, and then choose **Yes, Enable**\.  
![\[Enable IO\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EnableIO_volumepage_gwt.png)

------
#### [ AWS CLI ]

**To enable I/O for a volume with the command line**

   You can use one of the following commands to view event information for your Amazon EBS volumes\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
   + [enable\-volume\-io](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-volume-io.html) \(AWS CLI\)
   + [Enable\-EC2VolumeIO](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VolumeIO.html) \(AWS Tools for Windows PowerShell\)

------

1. Check the data on the volume\.

   1. Run the fsck command\.

   1. \(Optional\) Review any available application or system logs for relevant error messages\.

   1. If the volume has been impaired for more than 20 minutes, you can contact the AWS Support Center\. Choose **Troubleshoot**, and then in the **Troubleshoot Status Checks** dialog box, choose **Contact Support** to submit a support case\.

### Option 2: Perform a consistency check on the volume using another instance<a name="work_volumes_impaired_option2"></a>

Use the following procedure to check the volume outside your production environment\.

**Important**  
This procedure may cause the loss of write I/Os that were suspended when volume I/O was disabled\.

**To perform a consistency check on a volume in isolation**

1. Stop any applications from using the volume\.

1. Detach the volume from the instance\. For more information, see [Detach an Amazon EBS volume from a Linux instance](ebs-detaching-volume.md)\.

1. Enable I/O on the volume\. Use one of the following methods\.

------
#### [ Console ]

   1. In the navigation pane, choose **Volumes**\. 

   1. Select the volume that you detached in the previous step\.

   1. In the details pane, choose **Enable Volume IO**, and then choose **Yes, Enable**\.   
![\[Enable IO\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/EnableIO_volumepage_gwt.png)

------
#### [ AWS CLI ]

**To enable I/O for a volume with the command line**

   You can use one of the following commands to view event information for your Amazon EBS volumes\. For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)\.
   + [enable\-volume\-io](https://docs.aws.amazon.com/cli/latest/reference/ec2/enable-volume-io.html) \(AWS CLI\)
   + [Enable\-EC2VolumeIO](https://docs.aws.amazon.com/powershell/latest/reference/items/Enable-EC2VolumeIO.html) \(AWS Tools for Windows PowerShell\)

------

1. Attach the volume to another instance\. For more information, see [Launch your instance](LaunchingAndUsingInstances.md) and [Attach an Amazon EBS volume to an instance](ebs-attaching-volume.md)\.

1. Check the data on the volume\.

   1. Run the fsck command\.

   1. \(Optional\) Review any available application or system logs for relevant error messages\.

   1. If the volume has been impaired for more than 20 minutes, you can contact the AWS Support Center\. Choose **Troubleshoot**, and then in the troubleshooting dialog box, choose **Contact Support** to submit a support case\.

### Option 3: Delete the volume if you no longer need it<a name="work_volumes_impaired_option3"></a>

If you want to remove the volume from your environment, simply delete it\. For information about deleting a volume, see [Delete an Amazon EBS volume](ebs-deleting-volume.md)\.

If you have a recent snapshot that backs up the data on the volume, you can create a new volume from the snapshot\. For more information, see [Create a volume from a snapshot](ebs-creating-volume.md#ebs-create-volume-from-snapshot)\.

## Work with the Auto\-Enabled IO volume attribute<a name="volumeIO"></a>

When Amazon EBS determines that a volume's data is potentially inconsistent, it disables I/O to the volume from any attached EC2 instances by default\. This causes the volume status check to fail, and creates a volume status event that indicates the cause of the failure\. If the consistency of a particular volume is not a concern, and you prefer that the volume be made available immediately if it's **impaired**, you can override the default behavior by configuring the volume to automatically enable I/O\. If you enable the **Auto\-Enabled IO** volume attribute \(`autoEnableIO` in the API\), I/O between the volume and the instance is automatically re\-enabled and the volume's status check will pass\. In addition, you'll see an event that lets you know that the volume was in a potentially inconsistent state, but that its I/O was automatically enabled\. When this event occurs, you should check the volume's consistency and replace it if necessary\. For more information, see [EBS volume events](#monitoring-vol-events)\.

You can view and modify the **Auto\-Enabled IO** attribute of a volume using the following methods\.

------
#### [ Console ]

**To view the Auto\-Enabled IO attribute of a volume**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select the volume and choose **Status Checks**\. **Auto\-Enabled IO** displays the current setting \(**Enabled** or **Disabled**\) for your volume\.  
![\[View Auto-Enable IO\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/AutoEnableSetting_statustab_gwt.png)

**To modify the Auto\-Enabled IO attribute of a volume**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Volumes**\. 

1. Select the volume and choose **Actions**, **Change Auto\-Enable IO Setting**\. Alternatively, choose the **Status Checks** tab, and for **Auto\-Enabled IO**, choose **Edit**\.  
![\[Change Auto-Enable IO setting\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ChangeAutoEnable-gwt.png)

1. Select the **Auto\-Enable Volume IO** check box to automatically enable I/O for an impaired volume\. To disable the feature, clear the check box\.  
![\[Modify Auto-Enable IO setting\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ModifyAutoIO-gwt.png)

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To view the autoEnableIO attribute of a volume**

Use one of the following commands\.
+ [describe\-volume\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-volume-attribute.html) \(AWS CLI\)
+ [Get\-EC2VolumeAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2VolumeAttribute.html) \(AWS Tools for Windows PowerShell\)

**To modify the `autoEnableIO` attribute of a volume**

Use one of the following commands\.
+ [modify\-volume\-attribute](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-volume-attribute.html) \(AWS CLI\)
+ [Edit\-EC2VolumeAttribute](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2VolumeAttribute.html) \(AWS Tools for Windows PowerShell\)

For more information about these command line interfaces, see [Access Amazon EC2](concepts.md#access-ec2)

------
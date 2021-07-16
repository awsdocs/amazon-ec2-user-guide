# Define event windows for scheduled events<a name="event-windows"></a>

You can define custom, weekly\-recurring event windows for scheduled events that reboot, stop, or terminate your Amazon EC2 instances\. You can associate one or more instances with an event window\. If a scheduled event for those instances is planned, AWS will schedule the events within the associated event window\.

You can use event windows to maximize workload availability by specifying event windows that occur during off\-peak periods for your workload\. You can also align the event windows with your internal maintenance schedules\.

You define an event window by specifying a set of time ranges\. Each time range must be a minimum of 2 hours\. The combined time ranges must total at least 4 hours\.

You can associate one or more instances with an event window by using either instance IDs or instance tags\. You can also associate Dedicated Hosts with an event window by using the host ID\.

**Warning**  
Event windows are applicable only for scheduled events that stop, reboot, or terminate instances\.  
Event windows are *not* applicable for:  
Expedited scheduled events and network maintenance events\. 
Unscheduled maintenance such as AutoRecovery and unplanned reboots\.

**Topics**
+ [Considerations](#event-windows-considerations)
+ [View event windows](#view-event-windows)
+ [Create event windows](#create-event-windows)
+ [Modify event windows](#modify-event-windows)
+ [Delete event windows](#delete-event-windows)
+ [Tag event windows](#tag-event-windows)

## Considerations<a name="event-windows-considerations"></a>
+ All event window times are in UTC\.
+ The minimum weekly event window duration must be 4 hours\.
+ The time ranges within an event window must each be at least 2 hours\.
+ Only one target type \(instance ID, Dedicated Host ID, or instance tag\) can be associated with an event window\.
+ A target \(instance ID, Dedicated Host ID, or instance tag\) can only be associated with one event window\.
+ A maximum of 100 instance IDs, or 50 Dedicated Host IDs, or 50 instance tags can be associated with an event window\. The instance tags can be associated with any number of instances\.
+ A maximum of 200 event windows can be created per AWS Region\.
+ Multiple instances that are associated with event windows can potentially have scheduled events occur at the same time\.
+ If AWS has already scheduled an event, modifying an event window won't change the time of the scheduled event\. If the event has a deadline date, you can [reschedule the event](monitoring-instances-status-check_sched.md#reschedule-event)\.
+ You can stop and start an instance prior to the scheduled event, which migrates the instance to a new host, and the scheduled event will no longer take place\.

## View event windows<a name="view-event-windows"></a>

You can view event windows using one of the following methods\.

------
#### [ Console ]

**To view event windows using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Actions**, **Manage event windows**\.

1. Select an event window to view its details\.

------
#### [ AWS CLI ]

**To describe all the event windows using the AWS CLI**  
Use the [describe\-instance\-event\-windows](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-event-windows.html) command\.

```
aws ec2 describe-instance-event-windows \
    --region us-east-1
```

Expected output

```
{
    "InstanceEventWindows": [
        {
            "InstanceEventWindowId": "iew-0abcdef1234567890",
            "Name": "myEventWindowName",
            "CronExpression": "* 21-23 * * 2,3",
            "AssociationTarget": {
                "InstanceIds": [
                    "i-1234567890abcdef0",
                    "i-0598c7d356eba48d7"
                ],
                "Tags": [],
                "DedicatedHostIds": []
            },
            "State": "active",
            "Tags": []
        }
        
        ...
        
    ],
    "NextToken": "9d624e0c-388b-4862-a31e-a85c64fc1d4a"
}
```

**To describe a specific event window using the AWS CLI**  
Use the [describe\-instance\-event\-windows](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-event-windows.html) command with the `--instance-event-window-id` parameter to describe a specific event window\.

```
aws ec2 describe-instance-event-windows \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890
```

**To describe event windows that match one or more filters using the AWS CLI**  
Use the [describe\-instance\-event\-windows](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-event-windows.html) command with the `--filters` parameter\. In the following example, the `instance-id` filter is used to describe all the event windows that are associated with the specified instance\.

When a filter is used, it performs a direct match\. However, the `instance-id` filter is different\. If there is no direct match to the instance ID, then it falls back to indirect associations with the event window such as the instance's tags or Dedicated Host ID \(if the instance is on a Dedicated Host\)\.

For the list of supported filters, see [describe\-instance\-event\-windows](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-event-windows.html) in the *AWS CLI Reference*\.

```
aws ec2 describe-instance-event-windows \
    --region us-east-1 \
    --filters Name=instance-id,Values=i-1234567890abcdef0 \
    --max-results 100 \
    --next-token <next-token-value>
```

Expected output

In the following example, the instance is on a Dedicated Host, which is associated with the event window\.

```
{
    "InstanceEventWindows": [
        {
            "InstanceEventWindowId": "iew-0dbc0adb66f235982",
            "TimeRanges": [
                {
                    "StartWeekDay": "sunday",
                    "StartHour": 2,
                    "EndWeekDay": "sunday",
                    "EndHour": 8
                }
            ],
            "Name": "myEventWindowName",
            "AssociationTarget": {
                "InstanceIds": [],
                "Tags": [],
                "DedicatedHostIds": [
                    "h-0140d9a7ecbd102dd"
                ]
            },
            "State": "active",
            "Tags": []
        }
    ]
}
```

------

## Create event windows<a name="create-event-windows"></a>

You can create one or more event windows\. For each event window, you specify one or more blocks of time\. For example, you can create an event window with blocks of time that occur every day at 4am for 2 hours\. Or you can create an event window with blocks of time that occur on Sundays 2am \- 4am and on Wednesdays 3am \- 5am\.

For the event window constraints, see [Considerations](#event-windows-considerations) earlier in this topic\.

Event windows recur weekly until you delete them\.

Use one of the following methods to create an event window\.

------
#### [ Console ]

**To create an event window using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Create instance event window**\.

1. For **Event window name**, enter a descriptive name for the event window\.

1. For **Event window schedule**, choose to specify the blocks of time in the event window by using the cron schedule builder or by specifying time ranges\.
   + If you choose **Cron schedule builder**, specify the following:

     1. For **Days \(UTC\)**, specify the days of the week on which the event window occurs\.

     1. For **Start time \(UTC\)**, specify the time when the event window begins\.

     1. For **Duration**, specify the duration of the blocks of time in the event window\. The minimum duration per block of time is 2 hours\. The minimum duration of the event window must equal or exceed 4 hours in total\. All times are in UTC\.
   + If you choose **Time ranges**, choose **Add new time range** and specify the start day and time and the end day and time\. Repeat for each time range\. The minimum duration per time range is 2 hours\. The minimum duration for all time ranges combined must equal or exceed 4 hours in total\.

1. \(Optional\) For **Target details**, associate one or more instances with the event window so that if the instances are scheduled for maintenance, the scheduled event will occur during the associated event window\. You can associate one or more instances with an event window using instance IDs or instance tags\. You can associate Dedicated Hosts with an event window using the host ID\.

   Note that you can create the event window without associating a target to the window\. Later, you can modify the window to associate one or more targets\.

1. \(Optional\) For **Event window tags**, choose **Add tag**, and enter the key and value for the tag\. Repeat for each tag\.

1. Choose **Create event window**\.

------
#### [ AWS CLI ]

To create an event window using the AWS CLI, you first create the event window, and then associate one or more targets with the event window\.

**Create an event window**  
You can define either a set of time ranges or a cron expression when creating the event window, but not both\.

**To create an event window with a time range using the AWS CLI**  
Use the [create\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-event-window.html) command and specify the `--time-range` parameter\. You can't also specify the `--cron-expression` parameter\.

```
aws ec2 create-instance-event-window \
    --region us-east-1 \
    --time-range StartWeekDay=monday,StartHour=2,EndWeekDay=wednesday,EndHour=8 \
    --tag-specifications "ResourceType=instance-event-window,Tags=[{Key=K1,Value=V1}]" \
    --name myEventWindowName
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "TimeRanges": [
            {
                "StartWeekDay": "monday",
                "StartHour": 2,
                "EndWeekDay": "wednesday",
                "EndHour": 8
            }
        ],
        "Name": "myEventWindowName",
        "State": "creating",
        "Tags": [
            {
                "Key": "K1",
                "Value": "V1"
            }
        ]
    }
}
```

**To create an event window with a cron expression using the AWS CLI**  
Use the [create\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-event-window.html) command and specify the `--cron-expression` parameter\. You can't also specify the `--time-range` parameter\.

```
aws ec2 create-instance-event-window \
    --region us-east-1 \
    --cron-expression "* 21-23 * * 2,3" \
    --tag-specifications "ResourceType=instance-event-window,Tags=[{Key=K1,Value=V1}]" \
    --name myEventWindowName
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "State": "creating",
        "Tags": [
            {
                "Key": "K1",
                "Value": "V1"
            }
        ]
    }
}
```

**Associate a target with an event window**  
You can associate only one type of target \(instance IDs, Dedicated Host IDs, or instance tags\) with an event window\.

**To associate instance tags with an event window using the AWS CLI**  
Use the [associate\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-instance-event-window.html) command and specify the `instance-event-window-id` parameter to specify the event window\. To associate instance tags, specify the `--association-target` parameter, and for the parameter values, specify one or more tags\.

```
aws ec2 associate-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --association-target "InstanceTags=[{Key=k2,Value=v2},{Key=k1,Value=v1}]"
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [],
            "Tags": [
                {
                    "Key": "k2",
                    "Value": "v2"
                },
                {
                    "Key": "k1",
                    "Value": "v1"
                }
            ],
            "DedicatedHostIds": []
        },
        "State": "creating"
    }
}
```

**To associate one or more instances with an event window using the AWS CLI**  
Use the [associate\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-instance-event-window.html) command and specify the `instance-event-window-id` parameter to specify the event window\. To associate instances, specify the `--association-target` parameter, and for the parameter values, specify one or more instance IDs\.

```
aws ec2 associate-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --association-target "InstanceIds=i-1234567890abcdef0,i-0598c7d356eba48d7"
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [
                "i-1234567890abcdef0",
                "i-0598c7d356eba48d7"
            ],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating"
    }
}
```

**To associate a Dedicated Host with an event window using the AWS CLI**  
Use the [associate\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/associate-instance-event-window.html) command and specify the `instance-event-window-id` parameter to specify the event window\. To associate a Dedicated Host, specify the `--association-target` parameter, and for the parameter values, specify one or more Dedicated Host IDs\.

```
aws ec2 associate-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --association-target "DedicatedHostIds=h-029fa35a02b99801d"
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [],
            "Tags": [],
            "DedicatedHostIds": [
                "h-029fa35a02b99801d"
            ]
        },
        "State": "creating"
    }
}
```

------

## Modify event windows<a name="modify-event-windows"></a>

You can modify all the fields of an event window except its ID\. For example, when daylight savings begin, you might want to modify the event window schedule\. For existing event windows, you might want to add or remove targets\.

Use one of the following methods to modify an event window\.

------
#### [ Console ]

**To modify an event window using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Actions**, **Manage event windows**\.

1. Select the event window to modify, and then choose **Actions**, **Modify instance event window**\.

1. Modify the fields in the event window, and then choose **Modify event window**\.

------
#### [ AWS CLI ]

To modify an event window using the AWS CLI, you can modify the time range or cron expression, and associate or disassociate one or more targets with the event window\. 

**Modify the event window time**  
You can modify either a time range or a cron expression when modifying the event window, but not both\.

**To modify the time range of an event window using the AWS CLI**  
Use the [modify\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-event-window.html) command and specify the event window to modify\. Specify the `--time-range` parameter to modify the time range\. You can't also specify the `--cron-expression` parameter\.

```
aws ec2 modify-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890
    --time-range StartWeekDay=monday,StartHour=2,EndWeekDay=wednesday,EndHour=8
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "TimeRanges": [
            {
                "StartWeekDay": "monday",
                "StartHour": 2,
                "EndWeekDay": "wednesday",
                "EndHour": 8
            }
        ],
        "Name": "myEventWindowName",
        "AssociationTarget": {
            "InstanceIds": [
                "i-0abcdef1234567890",
                "i-0be35f9acb8ba01f0"
            ],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating",
        "Tags": [
            {
                "Key": "K1",
                "Value": "V1"
            }
        ]
    }
}
```

**To modify a set of time ranges for an event window using the AWS CLI**  
Use the [modify\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-event-window.html) command and specify the event window to modify\. Specify the `--time-range` parameter to modify the time range\. You can't also specify the `--cron-expression` parameter in the same call\.

```
aws ec2 modify-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --time-range '[{"StartWeekDay": "monday", "StartHour": 2, "EndWeekDay": "wednesday", "EndHour": 8},
	  {"StartWeekDay": "thursday", "StartHour": 2, "EndWeekDay": "friday", "EndHour": 8}]'
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "TimeRanges": [
            {
                "StartWeekDay": "monday",
                "StartHour": 2,
                "EndWeekDay": "wednesday",
                "EndHour": 8
            },
            {
                "StartWeekDay": "thursday",
                "StartHour": 2,
                "EndWeekDay": "friday",
                "EndHour": 8
            }
        ],
        "Name": "myEventWindowName",
        "AssociationTarget": {
            "InstanceIds": [
                "i-0abcdef1234567890",
                "i-0be35f9acb8ba01f0"
            ],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating",
        "Tags": [
            {
                "Key": "K1",
                "Value": "V1"
            }
        ]
    }
}
```

**To modify the cron expression of an event window using the AWS CLI**  
Use the [modify\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-event-window.html) command and specify the event window to modify\. Specify the `--cron-expression` parameter to modify the cron expression\. You can't also specify the `--time-range` parameter\.

```
aws ec2 modify-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --cron-expression "* 21-23 * * 2,3"
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [
                "i-0abcdef1234567890",
                "i-0be35f9acb8ba01f0"
            ],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating",
        "Tags": [
            {
                "Key": "K1",
                "Value": "V1"
            }
        ]
    }
}
```

**Modify the targets associated with an event window**  
You can associate additional targets with an event window\. You can also disassociate existing targets from an event window\. However, only one type of target \(instance IDs, Dedicated Host IDs, or instance tags\) can be associated with an event window\.

**To associate additional targets with an event window**  
For the instructions on how to associate targets with an event window, see [Associate a target with an event window](#associate-target)\.

**To disassociate instance tags from an event window using the AWS CLI**  
Use the [disassociate\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-instance-event-window.html) command and specify the `instance-event-window-id` parameter to specify the event window\. To disassociate instance tags, specify the `--association-target` parameter, and for the parameter values, specify one or more tags\.

```
aws ec2 disassociate-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --association-target "InstanceTags=[{Key=k2,Value=v2},{Key=k1,Value=v1}]"
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating"
    }
}
```

**To disassociate one or more instances from an event window using the AWS CLI**  
Use the [disassociate\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-instance-event-window.html) command and specify the `instance-event-window-id` parameter to specify the event window\. To disassociate instances, specify the `--association-target` parameter, and for the parameter values, specify one or more instance IDs\.

```
aws ec2 disassociate-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --association-target "InstanceIds=i-1234567890abcdef0,i-0598c7d356eba48d7"
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating"
    }
}
```

**To disassociate a Dedicated Host from an event window using the AWS CLI**  
Use the [disassociate\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/disassociate-instance-event-window.html) command and specify the `instance-event-window-id` parameter to specify the event window\. To disassociate a Dedicated Host, specify the `--association-target` parameter, and for the parameter values, specify one or more Dedicated Host IDs\.

```
aws ec2 disassociate-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --association-target DedicatedHostIds=h-029fa35a02b99801d
```

Expected output

```
{
    "InstanceEventWindow": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "Name": "myEventWindowName",
        "CronExpression": "* 21-23 * * 2,3",
        "AssociationTarget": {
            "InstanceIds": [],
            "Tags": [],
            "DedicatedHostIds": []
        },
        "State": "creating"
    }
}
```

------

## Delete event windows<a name="delete-event-windows"></a>

You can delete one event window at a time using one of the following methods\.

------
#### [ Console ]

**To delete an event window using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Actions**, **Manage event windows**\.

1. Select the event window to delete, and then choose **Actions**, **Delete instance event window**\.

1. When prompted, enter **delete**, and then choose **Delete**\.

------
#### [ AWS CLI ]

**To delete an event window using the AWS CLI**  
Use the [delete\-instance\-event\-window](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-instance-event-window.html) command and specify the event window to delete\.

```
aws ec2 delete-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890
```

**To force delete an event window using the AWS CLI**  
Use the `--force-delete` parameter if the event window is currently associated with targets\.

```
aws ec2 delete-instance-event-window \
    --region us-east-1 \
    --instance-event-window-id iew-0abcdef1234567890 \
    --force-delete
```

Expected output

```
{
    "InstanceEventWindowState": {
        "InstanceEventWindowId": "iew-0abcdef1234567890",
        "State": "deleting"
    }
}
```

------

## Tag event windows<a name="tag-event-windows"></a>

You can tag an event window when you create it, or afterwards\.

To tag an event window when you create it, see [Create event windows](#create-event-windows)\.

Use one of the following methods to tag an event window\.

------
#### [ Console ]

**To tag an existing event window using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Events**\.

1. Choose **Actions**, **Manage event windows**\.

1. Select the event window to tag, and then choose **Actions**, **Manage instance event window tags**\.

1. Choose **Add tag** to add a tag\. Repeat for each tag\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To tag an existing event window using the AWS CLI**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) command to tag existing resources\. In the following example, the existing event window is tagged with Key=purpose and Value=test\.

```
aws ec2 create-tags \
    --resources iew-0abcdef1234567890 \
    --tags Key=purpose,Value=test
```

------
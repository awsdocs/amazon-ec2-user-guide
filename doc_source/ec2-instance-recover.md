# Recover your instance<a name="ec2-instance-recover"></a>

To automatically recover an instance, you can use the default configuration of the instance or create an Amazon CloudWatch alarm\. If an instance becomes impaired because of an underlying hardware failure or a problem that requires AWS involvement to repair, the instance is automatically recovered\.

**Note**  
Terminated instances cannot be recovered\.

A recovered instance is identical to the original instance, including the instance ID, private IP addresses, Elastic IP addresses, and all instance metadata\. If the impaired instance has a public IPv4 address, the instance retains the public IPv4 address after recovery\. If the impaired instance is in a placement group, the recovered instance runs in the placement group\. During instance recovery, the instance is migrated as part of an instance reboot, and any data that is in\-memory is lost\.

Examples of problems that require instance recovery:
+ Loss of network connectivity
+ Loss of system power
+ Software issues on the physical host
+ Hardware issues on the physical host that impact network reachability

**Topics**
+ [Simplified automatic recovery based on instance configuration](#instance-configuration-recovery)
+ [Amazon CloudWatch action based recovery](#cloudwatch-recovery)
+ [Troubleshoot instance recovery failures](#TroubleshootingInstanceRecovery)

## Simplified automatic recovery based on instance configuration<a name="instance-configuration-recovery"></a>

Instances that support simplified automatic recovery are configured by default to recover a failed instance\. Simplified automatic recovery is initiated in response to system status check failures\. Simplified automatic recovery doesn't take place during Service Health Dashboard events, or any other events that impact the underlying rack\. For more information, see [Troubleshoot instance recovery failures](#TroubleshootingInstanceRecovery)\.

### Requirements<a name="requirements-for-recovery"></a>

Simplified automatic recovery is supported by an instance if the instance has the following characteristics:
+ It uses `default` or `dedicated` instance tenancy\.
+ It does not use an Elastic Fabric Adaptor\.
+ It uses one of the following instance types:
  + General purpose: A1 \| M4 \| M5 \| M5a \| M5n \| M5zn \| M6a \| M6g \| M6i \| T1 \| T2 \| T3 \| T3a \| T4g
  + Compute optimized: C4 \| C5 \| C5a \| C5n \| C6a \| C6g \| C6gn \| C6i \| Hpc6a
  + Memory optimized: R4 \| R5 \| R5a \| R5b \| R5n \| R6a \| R6g \| R6i \| high memory \(u\-\*\), virtualized only
  + Accelerated computing: G3 \| G5g \| Inf1 \| P2 \| P3 \| VT1
+ It uses one of the following instance types, if it does not have instance store volumes:
  + General purpose: M3
  + Compute optimized: C3
  + Memory optimized: R3 \| X1 \| X1e

### Limitations<a name="limitations-simplified-recovery"></a>
+ Instances with instance store volumes and metal instance types are not supported by simplified automatic recovery\.
+ If your instance is part of an Auto Scaling group with health checks enabled, then the instance is replaced when it becomes impaired\. Auto recovery is not initiated for instances inside an Auto Scaling group\.

### Verify the recovery behavior<a name="verify-recovery-behavior"></a>

You can use the AWS Management Console or the AWS CLI to view the instance types that support simplified automatic recovery\.

------
#### [ Console ]

**To view the instance types that support simplified automatic recovery**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instance Types**\.

1. In the filter bar, enter **Auto Recovery support: true**\. Alternatively, as you enter the characters and the filter name appears, you can select it\.

   The **Instance types** table displays all the instance types that support simplified automatic recovery\.

------
#### [ AWS CLI ]

**To view the instance types that support simplified automatic recovery**  
Use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command\.

```
aws ec2 describe-instance-types --filters Name=auto-recovery-supported,Values=true 
--query "InstanceTypes[*].[InstanceType]" --output text | sort
```

------

### Set the recovery behavior<a name="set-recovery-behavior"></a>

You can set the automatic recovery behavior to `disabled` or `default` after launching the instance\. The default configuration does not enable simplified automatic recovery for an unsupported instance type\.

------
#### [ Console ]

**To disable simplified automatic recovery for a running or stopped instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and then choose **Actions**, **Instance settings**, **Change auto\-recovery behavior**\.

1. Choose **Off**, and then choose **Save**\.

**To set the automatic recovery behavior to `default` for a running or stopped instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance, and then choose **Actions**, **Instance settings**, **Change auto\-recovery behavior**\.

1. Choose **Default**, and then choose **Save**\.

------
#### [ AWS CLI ]

**To disable simplified automatic recovery at launch**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instance.html) command\.

```
aws ec2 run-instances \
--image-id ami-1a2b3c4d \
--instance-type t2.micro \
--key-name MyKeyPair \
--maintenance-options AutoRecovery=Disabled \
  [...]
```

**To disable simplified automatic recovery for a running or stopped instance**  
Use the [modify\-instance\-maintenance\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-maintenance-options.html) command\.

```
aws ec2 modify-instance-maintenance-options \
--instance-id i-0abcdef1234567890 \
--auto-recovery disabled
```

**To set the automatic recovery behavior to `default` for a running or stopped instance**  
Use the [modify\-instance\-maintenance\-options](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-maintenance-options.html) command\.

```
aws ec2 modify-instance-maintenance-options \
--instance-id i-0abcdef1234567890 \
--auto-recovery default
```

------

## Amazon CloudWatch action based recovery<a name="cloudwatch-recovery"></a>

Use Amazon CloudWatch action based recovery if you want to customize when to recover your instance\.

When the `StatusCheckFailed_System` alarm is triggered, and the recovery action is initiated, you're notified by the Amazon SNS topic that you selected when you created the alarm and associated the recovery action\. When the recovery action is complete, information is published to the Amazon SNS topic you configured for the alarm\. Anyone who is subscribed to this Amazon SNS topic receives an email notification that includes the status of the recovery attempt and any further instructions\. As a last step in the recovery action, the recovered instance reboots\.

All of the instance types supported by simplified automatic recovery are also supported by CloudWatch action based recovery\. For more information, see [Requirements](#requirements-for-recovery)\. Amazon CloudWatch action based recovery does not support instances with instance store volumes, except the following instance types\. If the instance has instance store volumes attached, the data is lost during recovery\.
+ General purpose: M3
+ Compute optimized: C3
+ Memory optimized: R3 \| X1 \| X1e \| X2idn \| X2iedn

Amazon CloudWatch action based recovery does not support recovery for instances with Amazon EC2 Dedicated Hosts tenancy and metal instances\.

For information about creating an Amazon CloudWatch alarm to recover an instance, see [Add recover actions to Amazon CloudWatch alarms](UsingAlarmActions.md#AddingRecoverActions)\.

## Troubleshoot instance recovery failures<a name="TroubleshootingInstanceRecovery"></a>

The following issues can cause the recovery of your instance to fail:
+ Service Health Dashboard events or events that impact the underlying rack\. During such events, simplified automatic recovery does not recover instances\. You will not receive recovery failure notifications for such events\. Any ongoing Service Health Dashboard events may also prevent Amazon CloudWatch action based recovery from successfully recovering an instance\. See [http://status\.aws\.amazon\.com/](http://status.aws.amazon.com/) for the latest service availability information\.
+ Temporary, insufficient capacity of replacement hardware\.
+ The instance has an attached instance store storage, which is an unsupported configuration for automatic instance recovery\.
+ The instance has reached the maximum daily allowance of three recovery attempts\.

The automatic recovery process attempts to recover your instance for up to three separate failures per day\. If the instance system status check failure persists, we recommend that you manually stop and start the instance\. For more information, see [Stop and start your instance](Stop_Start.md)\.

Your instance might subsequently be retired if automatic recovery fails and a hardware degradation is determined to be the root cause for the original system status check failure\.
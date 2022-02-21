# Host recovery<a name="dedicated-hosts-recovery"></a>

Dedicated Host auto recovery restarts your instances on to a new replacement host when certain problematic conditions are detected on your Dedicated Host\. Host recovery reduces the need for manual intervention and lowers the operational burden if there is an unexpected Dedicated Host failure concerning system power or network connectivity events\. Other Dedicated Host issues will require manual intervention to recover from\. 

**Topics**
+ [Host recovery basics](#dedicated-hosts-recovery-basics)
+ [Supported instance types](#dedicated-hosts-recovery-instances)
+ [Configure host recovery](#dedicated-hosts-recovery-working)
+ [Host recovery states](#dedicated-hosts-recovery-states)
+ [Manually recover unsupported instances](#dedicated-hosts-recovery-unsupported)
+ [Related services](#dedicated-hosts-recovery-related)
+ [Pricing](#dedicated-hosts-recovery-pricing)

## Host recovery basics<a name="dedicated-hosts-recovery-basics"></a>

Dedicated Hosts and the host resource groups recovery process use host\-level health checks to assess Dedicated Host availability and to detect underlying system failures\. The type of Dedicated Host failure determines if Dedicated Host auto recovery is possible\. Examples of problems that can cause host\-level health checks to fail include:
+ Loss of network connectivity
+ Loss of system power
+ Hardware or software issues on the physical host

### Dedicated Host auto recovery<a name="dedicated-hosts-recovery-basics-auto-recovery"></a>

When a system power or network connectivity failure is detected on your Dedicated Host, Dedicated Host auto recovery is initiated and Amazon EC2 **automatically allocates a replacement Dedicated Host**\. The replacement Dedicated Host receives a new host ID, but retains the same attributes as the original Dedicated Host, including:
+ Availability Zone
+ Instance type
+ Tags
+ Auto placement settings
+ Reservation

When the replacement Dedicated Host is allocated, the **instances are recovered on to the replacement Dedicated Host**\. The recovered instances retain the same attributes as the original instances, including:
+ Instance ID
+ Private IP addresses
+ Elastic IP addresses
+ EBS volume attachments
+ All instance metadata

Additionally, the built\-in integration with AWS License Manager automates the tracking and management of your licenses\.

**Note**  
AWS License Manager integration is supported only in Regions in which AWS License Manager is available\. 

If instances have a host affinity relationship with the impaired Dedicated Host, the recovered instances establish host affinity with the replacement Dedicated Host\.

When all of the instances have been recovered on to the replacement Dedicated Host, **the impaired Dedicated Host is released**, and the replacement Dedicated Host becomes available for use\.

When host recovery is initiated, the AWS account owner is notified by email and by an AWS Personal Health Dashboard event\. A second notification is sent after the host recovery has been successfully completed\. 

If you are using AWS License Manager to track your licenses, AWS License Manager allocates new licenses for the replacement Dedicated Host based on the license configuration limits\. If the license configuration has hard limits that will be breached as a result of the host recovery, the recovery process is not allowed and you are notified of the host recovery failure through an Amazon SNS notification \(if notification settings have been configured for AWS License Manager\)\. If the license configuration has soft limits that will be breached as a result of the host recovery, the recovery is allowed to continue and you are notified of the limit breach through an Amazon SNS notification\. For more information, see [Using License Configurations](https://docs.aws.amazon.com/license-manager/latest/userguide/license-configurations.html) and [Settings in License Manager](https://docs.aws.amazon.com/license-manager/latest/userguide/settings.html) in the *AWS License Manager User Guide*\.

### Scenarios without Dedicated Host auto recovery<a name="dedicated-hosts-recovery-basics-non-auto"></a>

**Dedicated Host auto recovery does not occur when hardware or software issues impact the physical host** and manual intervention is required\. You will receive a retirement notification in the AWS Personal Health Dashboard, an Amazon CloudWatch event, and the AWS account owner email address receives a message regarding the Dedicated Host failure\.

**Stopped instances are not recovered** on to the replacement Dedicated Host\. If you attempt to start a stopped instance that targets the impaired Dedicated Host, the instance start fails\. We recommend that you modify the stopped instance to either target a different Dedicated Host, or to launch on any available Dedicated Host with matching configurations and auto\-placement enabled\.

**Instances with instance storage are not recovered** on to the replacement Dedicated Host\. As a remedial measure, the impaired Dedicated Host is marked for retirement and you receive a retirement notification after the host recovery is complete\. Follow the remedial steps described in the retirement notification within the specified time period to manually recover the remaining instances on the impaired Dedicated Host\.

## Supported instance types<a name="dedicated-hosts-recovery-instances"></a>

Host recovery is supported for the following instance families: A1, C3, C4, C5, C5n, C6g, C6i, Inf1,  M3, M4, M5, M5n, M6g, M6i, P3, R3, R4, R5, R5n, R6g, R6i, X1, X1e, X2gd,  u\-6tb1, u\-9tb1, u\-12tb1, u\-18tb1, and u\-24tb1\.

To recover instances that are not supported, see [Manually recover unsupported instances](#dedicated-hosts-recovery-unsupported)\.

**Note**  
Dedicated Host auto recovery of supported metal [instance types](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html) will take longer to detect and recover from than non\-metal instance types\.

## Configure host recovery<a name="dedicated-hosts-recovery-working"></a>

You can configure host recovery at the time of Dedicated Host allocation, or after allocation using the Amazon EC2 console or AWS Command Line Interface \(CLI\)\.

**Topics**
+ [Enable host recovery](#dedicated-hosts-recovery-enable)
+ [Disable host recovery](#dedicated-hosts-recovery-disable)
+ [View the host recovery configuration](#dedicated-hosts-recovery-view)

### Enable host recovery<a name="dedicated-hosts-recovery-enable"></a>

You can enable host recovery at the time of Dedicated Host allocation or after allocation\.

For more information about enabling host recovery at the time of Dedicated Host allocation, see [Allocate Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-allocating)\.

**To enable host recovery after allocation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host for which to enable host recovery, and then choose **Actions**, **Modify Host Recovery**\.

1. For **Host recovery**, choose **Enable**, and then choose **Save**\.

**To enable host recovery after allocation using the AWS CLI**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) command and specify the `host-recovery` parameter\.

```
$ aws ec2 modify-hosts --host-recovery on --host-ids h-012a3456b7890cdef
```

### Disable host recovery<a name="dedicated-hosts-recovery-disable"></a>

You can disable host recovery at any time after the Dedicated Host has been allocated\.

**To disable host recovery after allocation using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host for which to disable host recovery, and then choose **Actions**, **Modify Host Recovery**\.

1. For **Host recovery**, choose **Disable**, and then choose **Save**\.

**To disable host recovery after allocation using the AWS CLI**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) command and specify the `host-recovery` parameter\.

```
$ aws ec2 modify-hosts --host-recovery off --host-ids h-012a3456b7890cdef
```

### View the host recovery configuration<a name="dedicated-hosts-recovery-view"></a>

You can view the host recovery configuration for a Dedicated Host at any time\.

**To view the host recovery configuration for a Dedicated Host using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host, and in the **Description** tab, review the **Host Recovery** field\.

**To view the host recovery configuration for a Dedicated Host using the AWS CLI**  
Use the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) command\.

```
$ aws ec2 describe-hosts --host-ids h-012a3456b7890cdef
```

The `HostRecovery` response element indicates whether host recovery is enabled or disabled\.

## Host recovery states<a name="dedicated-hosts-recovery-states"></a>

When a Dedicated Host failure is detected, the impaired Dedicated Host enters the `under-assessment` state, and all of the instances enter the `impaired` state\. You can't launch instances on to the impaired Dedicated Host while it is in the `under-assessment` state\.

After the replacement Dedicated Host is allocated, it enters the `pending` state\. It remains in this state until the host recovery process is complete\. You can't launch instances on to the replacement Dedicated Host while it is in the `pending` state\. Recovered instances on the replacement Dedicated Host remain in the `impaired` state during the recovery process\.

After the host recovery is complete, the replacement Dedicated Host enters the `available` state, and the recovered instances return to the `running` state\. You can launch instances on to the replacement Dedicated Host after it enters the `available` state\. The original impaired Dedicated Host is permanently released and it enters the `released-permanent-failure` state\.

If the impaired Dedicated Host has instances that do not support host recovery, such as instances with instance store\-backed volumes, the Dedicated Host is not released\. Instead, it is marked for retirement and enters the `permanent-failure` state\.

## Manually recover unsupported instances<a name="dedicated-hosts-recovery-unsupported"></a>

Host recovery does not support recovering instances that use instance store volumes\. Follow the instructions below to manually recover any of your instances that could not be automatically recovered\.

**Warning**  
Data on instance store volumes is lost when an instance is stopped, hibernated, or terminated\. This includes instance store volumes that are attached to an instance that has an EBS volume as the root device\. To protect data from instance store volumes, back it up to persistent storage before the instance is stopped or terminated\.

### Manually recover EBS\-backed instances<a name="dedicated-hosts-recovery-ebs"></a>

For EBS\-backed instances that could not be automatically recovered, we recommend that you manually stop and start the instances to recover them onto a new Dedicated Host\. For more information about stopping your instance, and about the changes that occur in your instance configuration when it's stopped, see [Stop and start your instance](Stop_Start.md)\.

### Manually recover instance store\-backed instances<a name="dedicated-hosts-recovery-instancestore"></a>

For instance store\-backed instances that could not be automatically recovered, we recommend that you do the following:

1. Launch a replacement instance on a new Dedicated Host from your most recent AMI\.

1. Migrate all of the necessary data to the replacement instance\.

1. Terminate the original instance on the impaired Dedicated Host\.

## Related services<a name="dedicated-hosts-recovery-related"></a>

Dedicated Host integrates with the following services:
+ **AWS License Manager**—Tracks licenses across your Amazon EC2 Dedicated Hosts \(supported only in Regions in which AWS License Manager is available\)\. For more information, see the [AWS License Manager User Guide](https://docs.aws.amazon.com/license-manager/latest/userguide/license-manager.html)\.

## Pricing<a name="dedicated-hosts-recovery-pricing"></a>

There are no additional charges for using host recovery, but the usual Dedicated Host charges apply\. For more information, see [ Amazon EC2 Dedicated Hosts Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/)\.

As soon as host recovery is initiated, you are no longer billed for the impaired Dedicated Host\. Billing for the replacement Dedicated Host begins only after it enters the `available` state\.

If the impaired Dedicated Host was billed using the On\-Demand rate, the replacement Dedicated Host is also billed using the On\-Demand rate\. If the impaired Dedicated Host had an active Dedicated Host Reservation, it is transferred to the replacement Dedicated Host\.
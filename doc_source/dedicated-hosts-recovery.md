# Host Recovery<a name="dedicated-hosts-recovery"></a>

Host recovery automatically restarts your instances on to a new replacement host if failures are detected on your Dedicated Host\. Host recovery reduces the need for manual intervention and lowers the operational burden if there is an unexpected Dedicated Host failure\.

Additionally, built\-in integration with AWS License Manager automates the tracking and management of your licenses if a host recovery occurs\. 

**Note**  
AWS License Manager integration is supported only in Regions in which AWS License Manager is available\. 

**Topics**
+ [Host Recovery Basics](#dedicated-hosts-recovery-basics)
+ [Configuring Host Recovery](#dedicated-hosts-recovery-working)
+ [Host Recovery States](#dedicated-hosts-recovery-states)
+ [Supported Instance Configurations](#dedicated-hosts-recovery-instances)
+ [Manually Recovering Unsupported Instances](#dedicated-hosts-recovery-unsupported)
+ [Related Services](#dedicated-hosts-recovery-related)
+ [Pricing](#dedicated-hosts-recovery-pricing)

## Host Recovery Basics<a name="dedicated-hosts-recovery-basics"></a>

Host recovery uses host\-level health checks to assess Dedicated Host availability and to detect underlying system failures\. Examples of problems that can cause host\-level health checks to fail include:
+ Loss of network connectivity
+ Loss of system power
+ Hardware or software issues on the physical host

When a system failure is detected on your Dedicated Host, host recovery is initiated and Amazon EC2 **automatically allocates a replacement Dedicated Host**\. The replacement Dedicated Host receives a new host ID, but retains the same attributes as the original Dedicated Host, including:
+ Availability Zone
+ Instance type
+ Tags
+ Auto placement settings

After the replacement Dedicated Host is allocated, the **instances are recovered on to the replacement Dedicated Host**\. The recovered instances retain the same attributes as the original instances, including:
+ Instance ID
+ Private IP addresses
+ Elastic IP addresses
+ EBS volume attachments
+ All instance metadata

If instances have a host affinity relationship with the impaired Dedicated Host, the recovered instances establish host affinity with the replacement Dedicated Host\.

When all of the instances have been recovered on to the replacement Dedicated Host, **the impaired Dedicated Host is released**, and the replacement Dedicated Host becomes available for use\.

When host recovery is initiated, the AWS account owner is notified by email and by an AWS Personal Health Dashboard event\. A second notification is sent after the host recovery has been successfully completed\. 

**Stopped instances are not recovered** on to the replacement Dedicated Host\. If you attempt to start a stopped instance that targets the impaired Dedicated Host, the instance start fails\. We recommend that you modify the stopped instance to either target a different Dedicated Host, or to launch on any available Dedicated Host with matching configurations and auto\-placement enabled\.

**Instances with instance storage are not recovered** on to the replacement Dedicated Host\. As a remedial measure, the impaired Dedicated Host is marked for retirement and you receive a retirement notification after the host recovery is complete\. Follow the remedial steps described in the retirement notification within the specified time period to manually recover the remaining instances on the impaired Dedicated Host\.

If you are using AWS License Manager to track your licenses, AWS License Manager allocates new licenses for the replacement Dedicated Host based on the license configuration limits\. If the license configuration has hard limits that will be breached as a result of the host recovery, the recovery process is not allowed and you are notified of the host recovery failure through an Amazon SNS notification\. If the license configuration has soft limits that will be breached as a result of the host recovery, the recovery is allowed to continue and you are notified of the limit breach through an Amazon SNS notification\. For more information, see [Using License Configurations](https://docs.aws.amazon.com/license-manager/latest/userguide/license-configurations.html) in the *AWS License Manager User Guide*\.

## Configuring Host Recovery<a name="dedicated-hosts-recovery-working"></a>

You can configure host recovery at the time of Dedicated Host allocation, or after allocation using the Amazon EC2 console or AWS Command Line Interface \(CLI\)\.

**Topics**
+ [Enabling Host Recovery](#dedicated-hosts-recovery-enable)
+ [Disabling Host Recovery](#dedicated-hosts-recovery-disable)
+ [Viewing Host Recovery Configuration](#dedicated-hosts-recovery-view)

### Enabling Host Recovery<a name="dedicated-hosts-recovery-enable"></a>

You can enable host recovery at the time of Dedicated Host allocation or after allocation\.

For more information about enabling host recovery at the time of Dedicated Host allocation, see [Allocating Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-allocating)\.

**To enable host recovery after allocation \(Console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host for which to enable host recovery, and then choose **Actions**, **Modify Host Recovery**\.

1. For **Host recovery**, choose **Enable**, and then choose **Save**\.

**To enable host recovery after allocation \(AWS CLI\)**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) command and specify the `host-recovery` parameter\.

```
$ aws ec2 modify-hosts --host-recovery on --host-ids h-012a3456b7890cdef
```

### Disabling Host Recovery<a name="dedicated-hosts-recovery-disable"></a>

You can disable host recovery at any time after the Dedicated Host has been allocated\.

**To disable host recovery after allocation \(Console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host for which to disable host recovery, and then choose **Actions**, **Modify Host Recovery**\.

1. For **Host recovery**, choose **Disable**, and then choose **Save**\.

**To disable host recovery after allocation \(AWS CLI\)**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) command and specify the `host-recovery` parameter\.

```
$ aws ec2 modify-hosts --host-recovery off --host-ids h-012a3456b7890cdef
```

### Viewing Host Recovery Configuration<a name="dedicated-hosts-recovery-view"></a>

You can view the host recovery configuration for a Dedicated Host at any time\.

**To view the host recovery configuration for a Dedicated Host \(Console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select the Dedicated Host, and in the **Description** tab, review the **Host Recovery** field\.

**To view the host recovery configuration for a Dedicated Host \(AWS CLI\)**  
Use the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) command\.

```
$ aws ec2 describe-hosts --host-ids h-012a3456b7890cdef
```

The `HostRecovery` response element indicates whether host recovery is enabled or disabled\.

## Host Recovery States<a name="dedicated-hosts-recovery-states"></a>

When a Dedicated Host failure is detected, the impaired Dedicated Host enters the `under-assessment` state, and all of the instances enter the `impaired` state\. You can't launch instances on to the impaired Dedicated Host while it is in the `under-assessment` state\.

After the replacement Dedicated Host is allocated, it enters the `pending` state\. It remains in this state until the host recovery process is complete\. You can't launch instances on to the replacement Dedicated Host while it is in the `pending` state\. Recovered instances on the replacement Dedicated Host remain in the `impaired` state during the recovery process\.

After the host recovery is complete, the replacement Dedicated Host enters the `available` state, and the recovered instances return to the `running` state\. You can launch instances on to the replacement Dedicated Host after it enters the `available` state\. The original impaired Dedicated Host is permanently released and it enters the `released-permanent-failure` state\.

If the impaired Dedicated Host has instances that do not support host recovery, such as instances with instance store\-backed volumes, the Dedicated Host is not released\. Instead, it is marked for retirement and enters the `permanent-failure` state\.

## Supported Instance Configurations<a name="dedicated-hosts-recovery-instances"></a>

Host recovery is supported only with instances with a supported configuration\. To recover instances that are not supported, see [Manually Recovering Unsupported Instances](#dedicated-hosts-recovery-unsupported)\.

The following instance configurations are not supported:
+ Instance store volumes
  + C5d, G4, I3en, M5ad, M5d, M5dn, P3dn, R5ad, R5d, R5dn, and z1d instances
  + D2, F1, HS1, I2, I3, X1, and X1e instances
+ Bare metal instances

## Manually Recovering Unsupported Instances<a name="dedicated-hosts-recovery-unsupported"></a>

Host recovery does not support recovering instances that use instance store volumes\. Follow the instructions below to manually recover any of your instances that could not be automatically recovered\.

**Warning**  
Data on instance store volumes is lost when an instance is stopped or terminated\. This includes instance store volumes that are attached to an instance that has an EBS volume as the root device\. To keep data from instance store volumes, back it up to persistent storage before the instance is stopped or terminated\.

### Manually Recovering EBS\-Backed Instances<a name="dedicated-hosts-recovery-ebs"></a>

For EBS\-backed instances that could not be automatically recovered, we recommend that you manually stop and start the instances to recover them onto a new Dedicated Host\. For more information about stopping your instance, and about the changes that occur in your instance configuration when it's stopped, see [Stop and Start Your Instance](Stop_Start.md)\.

### Manually Recovering Instance Store\-Backed Instances<a name="dedicated-hosts-recovery-instancestore"></a>

For instance store\-backed instances that could not be automatically recovered, we recommend that you do the following:

1. Launch a replacement instance on a new Dedicated Host from your most recent AMI\.

1. Migrate all of the necessary data to the replacement instance\.

1. Terminate the original instance on the impaired Dedicated Host\.

## Related Services<a name="dedicated-hosts-recovery-related"></a>

Dedicated Host integrates with the following AWS services:
+ **AWS License Manager**—Tracks licenses across your Amazon EC2 Dedicated Hosts \(supported only in Regions in which AWS License Manager is available\)\. For more information, see the [ AWS License Manager User Guide](https://docs.aws.amazon.com/license-manager/latest/userguide/license-manager.html)\.

## Pricing<a name="dedicated-hosts-recovery-pricing"></a>

There are no additional charges for using host recovery, but the usual Dedicated Host charges apply\. For more information, see [ Amazon EC2 Dedicated Hosts Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/)\.

As soon as host recovery is initiated, you are no longer billed for the impaired Dedicated Host\. Billing for the replacement Dedicated Host begins only after it enters the `available` state\.

If the impaired Dedicated Host was billed using the On\-Demand rate, the replacement Dedicated Host is also billed using the On\-Demand rate\. If the impaired Dedicated Host had an active Dedicated Host Reservation, it is transferred to the replacement Dedicated Host\.
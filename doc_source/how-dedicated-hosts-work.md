# Working with Dedicated Hosts<a name="how-dedicated-hosts-work"></a>

To use a Dedicated Host, you first allocate hosts for use in your account\. You then launch instances onto the hosts by specifying *host* tenancy for the instance\. You must select a specific host for the instance to launch on to, or you can allow it to launch on to any host that has auto\-placement enabled and matches its instance type\. When an instance is stopped and restarted, the *Host affinity* setting determines whether it's restarted on the same, or a different, host\.

If you no longer need an On\-Demand host, you can stop the instances running on the host, direct them to launch on a different host, and then *release* the host\.

Dedicated Hosts are also integrated with AWS License Manager\. With License Manager, you can create a host resource group, which is a collection of Dedicated Hosts that are managed as a single entity\. When creating a host resource group, you specify the host management preferences, such as auto\-allocate and auto\-release, for the Dedicated Hosts\. This allows you to launch instances onto Dedicated Hosts without manually allocating and managing those hosts\. For more information, see [ Host Resource Groups](https://docs.aws.amazon.com/license-manager/latest/userguide/host-resource-groups.html) in the *AWS License Manager User Guide*\.

**Topics**
+ [Allocating Dedicated Hosts](#dedicated-hosts-allocating)
+ [Launching Instances onto a Dedicated Host](#launching-dedicated-hosts-instances)
+ [Launching Instances into a Host Resource Group](#launching-hrg-instances)
+ [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)
+ [Modifying Dedicated Host Auto\-Placement](#modify-host-auto-placement)
+ [Modifying the Supported Instance Types](#modify-host-support)
+ [Modifying Instance Tenancy and Affinity](#moving-instances-dedicated-hosts)
+ [Viewing Dedicated Hosts](#dedicated-hosts-managing)
+ [Tagging Dedicated Hosts](#dedicated-hosts-tagging)
+ [Monitoring Dedicated Hosts](#dedicated-hosts-monitoring)
+ [Releasing Dedicated Hosts](#dedicated-hosts-releasing)
+ [Purchasing Dedicated Host Reservations](#purchasing-dedicated-host-reservations)
+ [Viewing Dedicated Host Reservations](#viewing-host-reservations)
+ [Tagging Dedicated Host Reservations](#tagging-host-reservations)

## Allocating Dedicated Hosts<a name="dedicated-hosts-allocating"></a>

To begin using Dedicated Hosts, you must allocate Dedicated Hosts in your account using the Amazon EC2 console or the command line tools\.

After you allocate the Dedicated Host, the Dedicated Host capacity is made available in your account immediately and you can start launching instances onto the Dedicated Host\.

You can allocate a Dedicated Host using the following methods\.

------
#### [ New console ]

**To allocate a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts** and then choose **Allocate Dedicated Host**\.

1. For **Instance family**, choose the instance family for the Dedicated Host\.

1. Specify whether the Dedicated Host supports multiple instance types within the selected instance family, or a specific instance type only\. Do one of the following\.
   + To configure the Dedicated Host to support multiple instance types in the selected instance family, for **Support multiple instance types**, choose **Enable**\. Enabling this allows you to launch different instance types from the same instance family onto the Dedicated Host\. For example, if you choose the `m5` instance family and choose this option, you can launch `m5.xlarge` and `m5.4xlarge` instances onto the Dedicated Host\. The following instance families can be configured to support multiple instance types: A1, C5, C5n, M5, M5n, R5, and R5n\.
   + To configure the Dedicated Host to support a specific instance type within the selected instance family, clear **Support multiple instance types**, and then for **Instance type**, choose the instance type to support\. This allows you to launch a single instance type on the Dedicated Host\. For example, if you choose this option and specify `m5.4xlarge` as the supported instance type, you can launch only `m5.4xlarge` instances onto the Dedicated Host\.

1. For **Availability Zone**, choose the Availability Zone in which to allocate the Dedicated Host\.

1. To allow the Dedicated Host to accept untargeted instance launches that match its instance type, for **Instance auto\-placement**, choose **Enable**\. For more information about auto\-placement, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

1. To enable host recovery for the Dedicated Host, for **Host recovery**, choose **Enable**\. For more information, see [Host Recovery](dedicated-hosts-recovery.md)\.

1. For **Quantity**, enter the number of Dedicated Hosts to allocate\.

1. \(Optional\) Choose **Add Tag** and enter a tag key and a tag value\.

1. Choose **Allocate**\.

------
#### [ Old console ]

**To allocate a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**, **Allocate Dedicated Host**\.

1. For **Instance family**, choose the instance family for the Dedicated Host\.

1. Specify whether the Dedicated Host supports multiple instance types within the selected instance family, or a specific instance type only\. Do one of the following\.
   + To configure the Dedicated Host to support multiple instance types in the selected instance family, select **Support multiple instance types**\. Enabling this allows you to launch different instance types from the same instance family onto the Dedicated Host\. For example, if you choose the `m5` instance family and choose this option, you can launch `m5.xlarge` and `m5.4xlarge` instances onto the Dedicated Host\. The following instance families can be configured to support multiple instance types: A1, C5, C5n, M5, M5n, R5, and R5n\.
   + To configure the Dedicated Host to support a specific instance type within the selected instance family, clear **Support multiple instance types**, and then for **Instance type**, choose the instance type to support\. Enabling this allows you to launch a single instance type on the Dedicated Host\. For example, if you choose this option and specify `m5.4xlarge` as the supported instance type, you can launch only `m5.4xlarge` instances onto the Dedicated Host\.

1. For **Availability Zone**, choose the Availability Zone in which to allocate the Dedicated Host\.

1. To allow the Dedicated Host to accept untargeted instance launches that match its instance type, for **Instance auto\-placement**, choose **Enable**\. For more information about auto\-placement, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

1. To enable host recovery for the Dedicated Host, for **Host recovery** choose **Enable**\. For more information, see [Host Recovery](dedicated-hosts-recovery.md)\.

1. For **Quantity**, enter the number of Dedicated Hosts to allocate\.

1. \(Optional\) Choose **Add Tag** and enter a tag key and a tag value\.

1. Choose **Allocate host**\.

------
#### [ AWS CLI ]

**To allocate a Dedicated Host**  
Use the [allocate\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-hosts.html) AWS CLI command\. The following command allocates a Dedicated Host that supports multiple instance types from the `m5` instance family in `us-east-1a` Availability Zone\. The host also has host recovery enabled and it has auto\-placement disabled\.

```
aws ec2 allocate-hosts --instance-family "m5" --availability-zone "us-east-1a" --auto-placement "off" --host-recovery "on" --quantity 1 
```

The following command allocates a Dedicated Host that supports *untargeted* `m4.large` instance launches in the `eu-west-1a` Availability Zone, enables host recovery, and applies a tag with a key of `purpose` and a value of `production`\.

```
aws ec2 allocate-hosts --instance-type "m4.large" --availability-zone "eu-west-1a" --auto-placement "on" --host-recovery "on" --quantity 1 --tag-specifications 'ResourceType=dedicated-host,Tags=[{Key=purpose,Value=production}]'
```

------
#### [ PowerShell ]

**To allocate a Dedicated Host**  
Use the [New\-EC2Host](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Host.html) AWS Tools for Windows PowerShell command\. The following command allocates a Dedicated Host that supports multiple instance types from the `m5` instance family in `us-east-1a` Availability Zone\. The host also has host recovery enabled and it has auto\-placement disabled\.

```
PS C:\> New-EC2Host -InstanceFamily m5 -AvailabilityZone us-east-1a -AutoPlacement Off -HostRecovery On -Quantity 1
```

The following commands allocate a Dedicated Host that supports *untargeted* `m4.large` instance launches in the `eu-west-1a` Availability Zone, enable host recovery, and apply a tag with a key of `purpose` and a value of `production`\.

The `TagSpecification` parameter used to tag a Dedicated Host on creation requires an object that specifies the type of resource to be tagged, the tag key, and the tag value\. The following commands create the required object\.

```
PS C:\> $tag = @{ Key="purpose"; Value="production" }
PS C:\> $tagspec = new-object Amazon.EC2.Model.TagSpecification
PS C:\> $tagspec.ResourceType = "dedicated-host"
PS C:\> $tagspec.Tags.Add($tag)
```

The following command allocates the Dedicated Host and applies the tag specified in the `$tagspec` object\.

```
PS C:\> New-EC2Host -InstanceType m4.large -AvailabilityZone eu-west-1a -AutoPlacement On -HostRecovery On -Quantity 1 -TagSpecification $tagspec
```

------

## Launching Instances onto a Dedicated Host<a name="launching-dedicated-hosts-instances"></a>

After you have allocated a Dedicated Host, you can launch instances onto it\. You can't launch instances with `host` tenancy if you do not have active Dedicated Hosts with enough available capacity for the instance type that you are launching\.

**Note**  
The instances launched onto Dedicated Hosts can only be launched in a VPC\. For more information, see [Introduction to VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Introduction.html)\.

Before you launch your instances, take note of the limitations\. For more information, see [Dedicated Hosts Restrictions](dedicated-hosts-overview.md#dedicated-hosts-limitations)\.

You can launch an instance onto a Dedicated Host using the following methods\.

------
#### [ Console ]

**To launch an instance onto a specific Dedicated Host from the Dedicated Hosts page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Dedicated Hosts** in the navigation pane\.

1. On the **Dedicated Hosts** page, select a host and choose **Actions**, **Launch Instance\(s\) onto Host**\.

1. Select an AMI from the list\. SQL Server, SUSE, and RHEL AMIs provided by Amazon EC2 can't be used with Dedicated Hosts\.

1. On the **Choose an Instance Type** page, select the instance type to launch and then choose **Next: Configure Instance Details**\. 

   If the Dedicated Host supports a single instance type only, the supported instance type is selected by default and can't be changed\.

   If the Dedicated Host supports multiple instance types, you must select an instance type within the supported instance family based on the available instance capacity of the Dedicated Host\. We recommend that you launch the larger instance sizes first, and then fill the remaining instance capacity with the smaller instance sizes as needed\.

1. On the **Configure Instance Details** page, configure the instance settings to suit your needs, and then for **Affinity**, choose one of the following options:
   + **Off**—The instance launches onto the specified host, but it is not guaranteed to restart on the same Dedicated Host if stopped\.
   + **Host**—If stopped, the instance always restarts on this specific host\.

   For more information about Affinity, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

   The **Tenancy** and **Host** options are pre\-configured based on the host that you selected\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. When prompted, select an existing key pair or create a new one, and then choose **Launch Instances**\.

**To launch an instance onto a Dedicated Host using the Launch Instance wizard**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, **Launch Instance**\.

1. Select an AMI from the list\. SQL Server, SUSE, and RHEL AMIs provided by Amazon EC2 can't be used with Dedicated Hosts\.

1. Select the type of instance to launch and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, configure the instance settings to suit your needs, and then configure the following settings, which are specific to a Dedicated Host:
   + Tenancy—Choose **Dedicated Host \- Launch this instance on a Dedicated Host**\.
   + Host—Choose either **Use auto\-placement** to launch the instance on any Dedicated Host that has auto\-placement enabled, or select a specific Dedicated Host in the list\. The list displays only Dedicated Hosts that support the selected instance type\.
   + Affinity—Choose one of the following options:
     + **Off**—The instance launches onto the specified host, but it is not guaranteed to restart on it if stopped\.
     + **Host**—If stopped, the instance always restarts on the specified host\.

   For more information, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

   If you are unable to see these settings, check that you have selected a VPC in the **Network** menu\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. When prompted, select an existing key pair or create a new one, and then choose **Launch Instances**\.

------
#### [ AWS CLI ]

**To launch an instance onto a Dedicated Host**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify the instance affinity, tenancy, and host in the `Placement` request parameter\.

------
#### [ PowerShell ]

**To launch an instance onto a Dedicated Host**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) AWS Tools for Windows PowerShell command and specify the instance affinity, tenancy, and host in the `Placement` request parameter\.

------

## Launching Instances into a Host Resource Group<a name="launching-hrg-instances"></a>

When you launch an instance into a host resource group that has a Dedicated Host with available instance capacity, Amazon EC2 launches the instance onto that host\. If the host resource group does not have a host with available instance capacity, Amazon EC2 automatically allocates a new host in the host resource group, and then launches the instance onto that host\. For more information, see [ Host Resource Groups](https://docs.aws.amazon.com/license-manager/latest/userguide/host-resource-groups.html) in the *AWS License Manager User Guide*\.

You can launch an instance into a host resource group using the following methods\. 

------
#### [ Console ]

**To launch an instance into a host resource group**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, **Launch Instance**\.

1. Select an AMI from the list\. SQL Server, SUSE, and RHEL AMIs provided by Amazon EC2 can't be used with Dedicated Hosts\.

1. Select the type of instance to launch and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, configure the instance settings to suit your needs, and then do the following:

   1. For **Tenancy**, choose **Dedicated Host**\.

   1. For **Host resource group**, choose **Launch instance into a host resource group**\.

   1. For **Host resource group name**, choose the host resource group in which to launch the instance\.

   You can't target a specific host by choosing a host ID, and you can't enable instance affinity when launching an instance into a host resource group\.

1. Choose **Review and Launch**\.

1. On the **Review Instance Launch** page, choose **Launch**\.

1. When prompted, select an existing key pair or create a new one, and then choose **Launch Instances**\.

------
#### [ AWS CLI ]

**To launch an instance into a host resource group**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command, and in the `Placement` request parameter, omit the Tenancy option and specify the host resource group ARN\.

------
#### [ PowerShell ]

**To launch an instance into a host resource group**  
Use the [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) AWS Tools for Windows PowerShell command, and in the `Placement` request parameter, omit the Tenancy option and specify the host resource group ARN\.

------

## Understanding Auto\-Placement and Affinity<a name="dedicated-hosts-understanding"></a>

Placement control for Dedicated Hosts happens on both the instance level and host level\.

### Auto\-Placement<a name="dedicated-hosts-auto-placement"></a>

Auto\-placement is configured at the host level\. It allows you to manage whether instances that you launch are launched onto a specific host, or onto any available host that has matching configurations\.

When the auto\-placement of a Dedicated Host is *disabled*, it only accepts *Host* tenancy instance launches that specify its unique host ID\. This is the default setting for new Dedicated Hosts\.

When the auto\-placement of a Dedicated Host is *enabled*, it accepts any untargeted instance launches that match its instance type configuration\.

When launching an instance, you need to configure its tenancy\. Launching an instance onto a Dedicated Host without providing a specific `HostId` enables it to launch on any Dedicated Host that has auto\-placement *enabled* and that matches its instance type\.

### Host Affinity<a name="dedicated-hosts-affinity"></a>

Host Affinity is configured at the instance level\. It establishes a launch relationship between an instance and a Dedicated Host\.

When affinity is set to `Host`, an instance launched onto a specific host always restarts on the same host if stopped\. This applies to both targeted and untargeted launches\.

When affinity is set to `Off`, and you stop and restart the instance, it can be restarted on any available host\. However, it tries to launch back onto the last Dedicated Host on which it ran \(on a best\-effort basis\)\.

## Modifying Dedicated Host Auto\-Placement<a name="modify-host-auto-placement"></a>

You can modify the auto\-placement settings of a Dedicated Host after you have allocated it to your AWS account, using one of the following methods\.

------
#### [ New console ]

**To modify the auto\-placement of a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Select a host and choose **Actions**, **Modify host**\.

1. For **Instance auto\-placement**, choose **Enable** to enable auto\-placement, or clear **Enable** to disable auto\-placement\. For more information, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

1. Choose **Save**\.

------
#### [ Old console ]

**To modify the auto\-placement of a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Dedicated Hosts** in the navigation pane\.

1. On the **Dedicated Hosts** page, select a host and choose **Actions**, **Modify Auto\-Placement**\.

1. On the Modify Auto\-placement window, for **Allow instance auto\-placement**, choose **Yes** to enable auto\-placement, or choose **No** to disable auto\-placement\. For more information, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To modify the auto\-placement of a Dedicated Host**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) AWS CLI command\. The following example enables auto\-placement for the specified Dedicated Host\.

```
aws ec2 modify-hosts --auto-placement on --host-ids h-012a3456b7890cdef
```

------
#### [ PowerShell ]

**To modify the auto\-placement of a Dedicated Host**  
Use the [Edit\-EC2Host](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2Host.html) AWS Tools for Windows PowerShell command\. The following example enables auto\-placement for the specified Dedicated Host\.

```
PS C:\> Edit-EC2Host --AutoPlacement 1 --HostId h-012a3456b7890cdef
```

------

## Modifying the Supported Instance Types<a name="modify-host-support"></a>

You can modify a Dedicated Host to change the instance types that it supports\. If it currently supports a single instance type, you can modify it to support multiple instance types within that instance family\. Similarly, if it currently supports multiple instance types, you can modify it to support a specific instance type only\.

To modify a Dedicated Host to support multiple instance types, you must first stop all running instances on the host\. The modification takes approximately 10 minutes to complete\. The Dedicated Host transitions to the `pending` state while the modification is in progress\. You can't start stopped instances or launch new instances on the Dedicated Host while it is in the `pending` state\. The following instance families can be modified to support multiple instance types: A1, C5, C5n, M5, M5n, R5, and R5n\.

To modify a Dedicated Host that supports multiple instance types to support only a specific instance type, the host must either have no running instances, or the running instances must be of the instance type that you want the host to support\. For example, to modify a host that supports multiple instance types in the `m5` instance family to support only `m5.large` instances, the Dedicated Host must either have no running instances, or it must have only `m5.large` instances running on it\.

You can modify the supported instance types using one of the following methods\.

------
#### [ New console ]

**To modify the supported instance types for a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the Navigation pane, choose **Dedicated Host**\.

1. Select the Dedicated Host to modify and choose **Actions**, **Modify host**\.

1. Do one of the following, depending on the current configuration of the Dedicated Host:
   + If the Dedicated Host currently supports a specific instance type, **Support multiple instance types** is not enabled, and **Instance type** lists the supported instance type\. To modify the host to support multiple types in the current instance family, for **Support multiple instance types**, choose **Enable**\.

     You must first stop all instances running on the host before modifying it to support multiple instance types\.
   + If the Dedicated Host currently supports multiple instance types in an instance family, **Enabled** is selected for **Support multiple instance types**\. To modify the host to support a specific instance type, for **Support multiple instance types**, clear **Enable**, and then for **Instance type**, select the specific instance type to support\.

     You can't change the instance family supported by the Dedicated Host\.

1. Choose **Save**\.

------
#### [ Old console ]

**To modify the supported instance types for a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the Navigation pane, choose **Dedicated Host**\.

1. Select the Dedicated Host to modify and choose **Actions**, **Modify Supported Instance Types**\.

1. Do one of the following, depending on the current configuration of the Dedicated Host:
   + If the Dedicated Host currently supports a specific instance type, **No** is selected for **Support multiple instance types**\. To modify the host to support multiple types in the current instance family, for **Support multiple instance types**, select **Yes**\.

     You must first stop all instances running on the host before modifying it to support multiple instance types\.
   + If the Dedicated Host currently supports multiple instance types in an instance family, **Yes** is selected for **Support multiple instance types**, and **Instance family** displays the supported instance family\. To modify the host to support a specific instance type, for **Support multiple instance types**, select **No**, and then for **Instance type**, select the specific instance type to support\.

     You can't change the instance family supported by the Dedicated Host\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To modify the supported instance types for a Dedicated Host**  
Use the [modify\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) AWS CLI command\.

The following command modifies a Dedicated Host to support multiple instance types within the `m5` instance family\.

```
aws ec2 modify-hosts --instance-family m5 --host-ids h-012a3456b7890cdef
```

The following command modifies a Dedicated Host to support `m5.xlarge` instances only\.

```
aws ec2 modify-hosts --instance-type m5.xlarge --instance-family --host-ids h-012a3456b7890cdef
```

------
#### [ PowerShell ]

**To modify the supported instance types for a Dedicated Host**  
Use the [Edit\-EC2Host](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2Host.html) AWS Tools for Windows PowerShell command\.

The following command modifies a Dedicated Host to support multiple instance types within the `m5` instance family\.

```
PS C:\> Edit-EC2Host --InstanceFamily m5 --HostId h-012a3456b7890cdef
```

The following command modifies a Dedicated Host to support `m5.xlarge` instances only\.

```
PS C:\> Edit-EC2Host --InstanceType m5.xlarge --HostId h-012a3456b7890cdef
```

------

## Modifying Instance Tenancy and Affinity<a name="moving-instances-dedicated-hosts"></a>

You can change the tenancy of an instance from `dedicated` to `host`, or from `host` to `dedicated`, after you have launched it\. You can also modify the affinity between the instance and the host\. To modify either instance tenancy or affinity, the instance must be in the `stopped` state\.

You can modify an instance's tenancy and affinity using the following methods\.

------
#### [ Console ]

**To modify instance tenancy or affinity**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances**, and select the instance to modify\.

1. Choose **Actions**, **Instance State**, and **Stop**\.

1. Open the context \(right\-click\) menu on the instance and choose **Instance Settings**, **Modify Instance Placement**\.

1. On the **Modify Instance Placement** page, configure the following:
   + **Tenancy**—Choose one of the following:
     + Run a dedicated hardware instance—Launches the instance as a Dedicated Instance\. For more information, see [Dedicated Instances](dedicated-instance.md)\.
     + Launch the instance on a Dedicated Host—Launches the instance onto a Dedicated Host with configurable affinity\.
   + **Affinity**—Choose one of the following:
     + This instance can run on any one of my hosts—The instance launches onto any available Dedicated Host in your account that supports its instance type\.
     + This instance can only run on the selected host—The instance is only able to run on the Dedicated Host selected for **Target Host**\.
   + **Target Host**—Select the Dedicated Host that the instance must run on\. If no target host is listed, you might not have available, compatible Dedicated Hosts in your account\.

   For more information, see [Understanding Auto\-Placement and Affinity](#dedicated-hosts-understanding)\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To modify instance tenancy or affinity**  
Use the [modify\-instance\-placement](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) AWS CLI command\. The following example changes the specified instance's affinity from `default` to `host`, and specifies the Dedicated Host that the instance has affinity with\.

```
aws ec2 modify-instance-placement --instance-id i-1234567890abcdef0 --affinity host --host-id h-012a3456b7890cdef
```

------
#### [ PowerShell ]

**To modify instance tenancy or affinity**  
Use the [Edit\-EC2InstancePlacement](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) AWS Tools for Windows PowerShell command\. The following example changes the specified instance's affinity from `default` to `host`, and specifies the Dedicated Host that the instance has affinity with\.

```
PS C:\> Edit-EC2InstancePlacement -InstanceId i-1234567890abcdef0 -Affinity host -HostId h-012a3456b7890cdef
```

------

## Viewing Dedicated Hosts<a name="dedicated-hosts-managing"></a>

You can view details about a Dedicated Host and the individual instances on it using the following methods\.

------
#### [ New console ]

**To view the details of a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. On the **Dedicated Hosts** page, select a host\.

1. For information about the host, choose **Details**\.

   **Available vCPUs** indicates the vCPUs that are available on the Dedicated Host for new instance launches\. For example, a Dedicated Host that supports multiple instance types within the `c5` instance family, and that has no instances running on it, has 72 available vCPUs\. This means that you can launch different combinations of instance types onto the Dedicated Host to consume the 72 available vCPUs\.

   For information about instances running on the host, choose **Running instances**\.

------
#### [ Old console ]

**To view the details of a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. On the **Dedicated Hosts** page, select a host\.

1. For information about the host, choose **Description**\. **Available vCPUs** indicates the vCPUs that are available on the Dedicated Host for new instance launches\. For example, a Dedicated Host that supports multiple instance types within the `c5` instance family, and that has no instances running on it, has 72 available vCPUs\. This means that you can launch different combinations of instance types onto the Dedicated Host to consume the 72 available vCPUs\.

   For information about instances running on the host, choose **Instances**\.

------
#### [ AWS CLI ]

**To view the capacity of a Dedicated Host**  
Use the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) AWS CLI command\.

The following example uses the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) \(AWS CLI\) command to view the available instance capacity for a Dedicated Host that supports multiple instance types within the `c5` instance family\. The Dedicated Host already has two `c5.4xlarge` instances and four `c5.2xlarge` instances running on it\.

```
$  aws ec2 describe-hosts --host-id h-012a3456b7890cdef
```

```
"AvailableInstanceCapacity": [
    { "AvailableCapacity": 2,
      "InstanceType": "c5.xlarge",
      "TotalCapacity": 18 },
    { "AvailableCapacity": 4,
      "InstanceType": "c5.large",
      "TotalCapacity": 36 }
  ],
"AvailableVCpus": 8
```

------
#### [ PowerShell ]

**To view the instance capacity of a Dedicated Host**  
Use the [Get\-EC2Host](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Host.html) AWS Tools for Windows PowerShell command\.

```
PS C:\> Get-EC2Host -HostId h-012a3456b7890cdef
```

------

## Tagging Dedicated Hosts<a name="dedicated-hosts-tagging"></a>

You can assign custom tags to your existing Dedicated Hosts to categorize them in different ways, for example, by purpose, owner, or environment\. This helps you to quickly find a specific Dedicated Host based on the custom tags that you assigned\. Dedicated Host tags can also be used for cost allocation tracking\.

You can also apply tags to Dedicated Hosts at the time of creation\. For more information, see [Allocating Dedicated Hosts](#dedicated-hosts-allocating)\.

You can tag a Dedicated Host using the following methods\.

------
#### [ New console ]

**To tag a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\. 

1. Select the Dedicated Host to tag, and then choose **Actions**, **Manage tags**\.

1. In the **Manage tags** screen, choose **Add tag**, and then specify the key and value for the tag\.

1. \(Optional\) Choose **Add tag** to add additional tags to the Dedicated Host\.

1. Choose **Save changes**\.

------
#### [ Old console ]

**To tag a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\. 

1. Select the Dedicated Host to tag, and then choose **Tags**\. 

1. Choose **Add/Edit Tags**\.

1. In the **Add/Edit Tags** dialog box, choose **Create Tag**, and then specify the key and value for the tag\.

1. \(Optional\) Choose **Create Tag** to add additional tags to the Dedicated Host\.

1. Choose **Save**\.

------
#### [ AWS CLI ]

**To tag a Dedicated Host**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\.

The following command tags the specified Dedicated Host with `Owner=TeamA`\.

```
aws ec2 create-tags --resources h-abc12345678909876 --tags Key=Owner,Value=TeamA
```

------
#### [ PowerShell ]

**To tag a Dedicated Host**  
Use the [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) AWS Tools for Windows PowerShell command\.

The `New-EC2Tag` command needs a `Tag` object, which specifies the key and value pair to be used for the Dedicated Host tag\. The following commands create a `Tag` object named `$tag`, with a key and value pair of `Owner` and `TeamA` respectively\.

```
PS C:\> $tag = New-Object Amazon.EC2.Model.Tag
PS C:\> $tag.Key = "Owner"
PS C:\> $tag.Value = "TeamA"
```

The following command tags the specified Dedicated Host with the `$tag` object\.

```
PS C:\> New-EC2Tag -Resource h-abc12345678909876 -Tag $tag
```

------

## Monitoring Dedicated Hosts<a name="dedicated-hosts-monitoring"></a>

Amazon EC2 constantly monitors the state of your Dedicated Hosts\. Updates are communicated on the Amazon EC2 console\. You can view information about a Dedicated Host using the following methods\.

------
#### [ Console ]

**To view the state of a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. Locate the Dedicated Host in the list and review the value in the **State** column\.

------
#### [ AWS CLI ]

**To view the state of a Dedicated Host**  
Use the [describe\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) AWS CLI command and then review the `state` property in the `hostSet` response element\.

```
aws ec2 describe-hosts --host-id h-012a3456b7890cdef
```

------
#### [ PowerShell ]

**To view the state of a Dedicated Host**  
Use the [Get\-EC2Host](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Host.html) AWS Tools for Windows PowerShell command and then review the `state` property in the `hostSet` response element\.

```
PS C:\> Get-EC2Host -HostId h-012a3456b7890cdef
```

------

The following table explains the possible Dedicated Host states\.


| **State** | **Description** | 
| --- | --- | 
| available | AWS hasn't detected an issue with the Dedicated Host\. No maintenance or repairs are scheduled\. Instances can be launched onto this Dedicated Host\. | 
| released | The Dedicated Host has been released\. The host ID is no longer in use\. Released hosts can't be reused\. | 
| under\-assessment | AWS is exploring a possible issue with the Dedicated Host\. If action must be taken, you are notified via the AWS Management Console or email\. Instances can't be launched onto a Dedicated Host in this state\. | 
| pending | The Dedicated Host cannot be used for new instance launches\. It is either being [ modified to support multiple instance types](#modify-host-support), or a [host recovery](dedicated-hosts-recovery.md) is in progress\. | 
| permanent\-failure | An unrecoverable failure has been detected\. You receive an eviction notice through your instances and by email\. Your instances might continue to run\. If you stop or terminate all instances on a Dedicated Host with this state, AWS retires the host\. AWS does not restart instances in this state\. Instances can't be launched onto Dedicated Hosts in this state\. | 
| released\-permanent\-failure | AWS permanently releases Dedicated Hosts that have failed and no longer have running instances on them\. The Dedicated Host ID is no longer available for use\. | 

## Releasing Dedicated Hosts<a name="dedicated-hosts-releasing"></a>

Any running instances on the Dedicated Host must be stopped before you can release the host\. These instances can be migrated to other Dedicated Hosts in your account so that you can continue to use them\. These steps apply only to On\-Demand Dedicated Hosts\.

You can release a Dedicated Host using the following methods\.

------
#### [ New console ]

**To release a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\.

1. On the **Dedicated Hosts** page, select the Dedicated Host to release\.

1. Choose **Actions**, **Release host**\.

1. To confirm, choose **Release**\.

------
#### [ Old console ]

**To release a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Dedicated Hosts** in the navigation pane\.

1. On the **Dedicated Hosts** page, select the Dedicated Host to release\.

1. Choose **Actions**, **Release Hosts**\.

1. Choose **Release** to confirm\.

------
#### [ AWS CLI ]

**To release a Dedicated Host**  
Use the [release\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/release-hosts.html) AWS CLI command\.

```
aws ec2 release-hosts --host-ids h-012a3456b7890cdef
```

------
#### [ PowerShell ]

**To release a Dedicated Host**  
Use the [Remove\-EC2Hosts](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Host.html) AWS Tools for Windows PowerShell command\.

```
PS C:\> Remove-EC2Hosts -HostId h-012a3456b7890cdef
```

------

After you release a Dedicated Host, you can't reuse the same host or host ID again, and you are no longer charged On\-Demand billing rates for it\. The state of the Dedicated Host is changed to `released`, and you are not able to launch any instances onto that host\.

**Note**  
If you have recently released Dedicated Hosts, it can take some time for them to stop counting towards your limit\. During this time, you might experience `LimitExceeded` errors when trying to allocate new Dedicated Hosts\. If this is the case, try allocating new hosts again after a few minutes\.

The instances that were stopped are still available for use and are listed on the **Instances** page\. They retain their `host` tenancy setting\.

## Purchasing Dedicated Host Reservations<a name="purchasing-dedicated-host-reservations"></a>

You can purchase reservations using the following methods:

------
#### [ Console ]

**To purchase reservations**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Dedicated Hosts**, **Dedicated Host Reservations**, **Purchase Dedicated Host Reservation**\.

1. On the **Purchase Dedicated Host Reservation** screen, you can search for available offerings using the default settings, or you can specify custom values for the following:
   + **Host instance family**—The options listed correspond with the Dedicated Hosts in your account that are not already assigned to a reservation\.
   + **Availability Zone**—The Availability Zone of the Dedicated Hosts in your account that aren't already assigned to a reservation\.
   + **Payment option**—The payment option for the offering\.
   + **Term**—The term of the reservation, which can be one or three years\.

1. Choose **Find offering** and select an offering that matches your requirements\.

1. Choose the Dedicated Hosts to associate with the reservation, and then choose **Review**\.

1. Review your order and choose **Order**\.

------
#### [ AWS CLI ]

**To purchase reservations**

1. Use the [describe\-host\-reservation\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-host-reservation-offerings.html) AWS CLI command to list the available offerings that match your needs\. The following example lists the offerings that support instances in the `m4` instance family and have a one\-year term\.
**Note**  
The term is specified in seconds\. A one\-year term includes 31,536,000 seconds, and a three\-year term includes 94,608,000 seconds\.

   ```
   aws ec2 describe-host-reservation-offerings --filter Name=instance-family,Values=m4 --max-duration 31536000
   ```

   The command returns a list of offerings that match your criteria\. Note the `offeringId` of the offering to purchase\. 

1. Use the [purchase\-host\-reservation](https://docs.aws.amazon.com/cli/latest/reference/ec2/purchase-host-reservation.html) AWS CLI command to purchase the offering and provide the `offeringId` noted in the previous step\. The following example purchases the specified reservation and associates it with a specific Dedicated Host that is already allocated in the AWS account\.

   ```
   aws ec2 purchase-host-reservation --offering-id hro-03f707bf363b6b324 --host-id-set h-013abcd2a00cbd123
   ```

------
#### [ PowerShell ]

**To purchase reservations**

1. Use the [Get\-EC2HostReservationOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2HostReservationOffering.html) AWS Tools for Windows PowerShell command to list the available offerings that match your needs\. The following examples list the offerings that support instances in the `m4` instance family and have a one\-year term\.
**Note**  
The term is specified in seconds\. A one\-year term includes 31,536,000 seconds, and a three\-year term includes 94,608,000 seconds\.

   ```
   PS C:\> $filter = @{Name="instance-family"; Value="m4"}
   ```

   ```
   PS C:\> Get-EC2HostReservationOffering -filter $filter -MaxDuration 31536000
   ```

   The command returns a list of offerings that match your criteria\. Note the `offeringId` of the offering to purchase\. 

1. Use the [New\-EC2HostReservation](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2HostReservation.html) AWS Tools for Windows PowerShell command to purchase the offering and provide the `offeringId` noted in the previous step\. The following example purchases the specified reservation and associates it with a specific Dedicated Host that is already allocated in the AWS account\.

   ```
   PS C:\> New-EC2HostReservation -OfferingId hro-03f707bf363b6b324 -HostIdSet h-013abcd2a00cbd123
   ```

------

## Viewing Dedicated Host Reservations<a name="viewing-host-reservations"></a>

You can view information about the Dedicated Hosts that are associated with your reservation, including:
+ The term of the reservation
+ The payment option
+ The start and end dates

You can view details of your Dedicated Host reservations using the following methods\.

------
#### [ Console ]

**To view the details of a Dedicated Host reservation**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Dedicated Hosts** in the navigation pane\.

1. On the **Dedicated Hosts** page, choose **Dedicated Host Reservations**, and then select the reservation from the list provided\.

1. Choose **Details** for information about the reservation\.

1. Choose **Hosts** for information about the Dedicated Hosts with which the reservation is associated\.

------
#### [ AWS CLI ]

**To view the details of a Dedicated Host reservation**  
Use the [describe\-host\-reservations](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-host-reservations.html) AWS CLI command\.

```
aws ec2 describe-host-reservations
```

------
#### [ PowerShell ]

**To view the details of a Dedicated Host reservation**  
Use the [Get\-EC2HostReservation](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2HostReservation.html) AWS Tools for Windows PowerShell command\.

```
PS C:\> Get-EC2HostReservation
```

------

## Tagging Dedicated Host Reservations<a name="tagging-host-reservations"></a>

You can assign custom tags to your Dedicated Host Reservations to categorize them in different ways, for example, by purpose, owner, or environment\. This helps you to quickly find a specific Dedicated Host Reservation based on the custom tags that you assigned\.

You can tag a Dedicated Host Reservation using the command line tools only\.

------
#### [ AWS CLI ]

**To tag a Dedicated Host Reservation**  
Use the [create\-tags](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-tags.html) AWS CLI command\.

```
aws ec2 create-tags --resources hr-1234563a4ffc669ae --tags Key=Owner,Value=TeamA
```

------
#### [ PowerShell ]

**To tag a Dedicated Host Reservation**  
Use the [New\-EC2Tag](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Tag.html) AWS Tools for Windows PowerShell command\.

The `New-EC2Tag` command needs a `Tag` parameter, which specifies the key and value pair to be used for the Dedicated Host Reservation tag\. The following commands create the `Tag` parameter\.

```
PS C:\> $tag = New-Object Amazon.EC2.Model.Tag
PS C:\> $tag.Key = "Owner"
PS C:\> $tag.Value = "TeamA"
```

```
PS C:\> New-EC2Tag -Resource hr-1234563a4ffc669ae -Tag $tag
```

------
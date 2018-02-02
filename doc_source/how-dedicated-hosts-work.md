# Using Dedicated Hosts<a name="how-dedicated-hosts-work"></a>

To use a Dedicated Host, you first *allocate* hosts for use in your account\. You then *launch* instances onto the hosts by specifying `host` tenancy for the instance\. The *instance auto\-placement* setting allows you to control whether an instance can launch onto a particular host\. When an instance is stopped and restarted, the *Host affinity* setting determines whether it's restarted on the same, or a different, host\. If you no longer need an On\-Demand host, you can stop the instances running on the host, direct them to launch on a different host, and then *release* the Dedicated Host\.


+ [Bring Your Own License](#dedicated-hosts-BYOL)
+ [Allocating Dedicated Hosts](#dedicated-hosts-allocating)
+ [Launching Instances onto Dedicated Hosts](#launching-dedicated-hosts-instances)
+ [Understanding Instance Placement and Host Affinity](dedicated-hosts-instance-placement.md)
+ [Modifying Instance Tenancies](#moving-instances-dedicated-hosts)
+ [Managing and Releasing Dedicated Hosts](#dedicated-hosts-managing)
+ [API and CLI Command Overview](#dedicated-hosts-api-cli)
+ [Tracking Configuration Changes with AWS Config](#dedicated-hosts-aws-config)

## Bring Your Own License<a name="dedicated-hosts-BYOL"></a>

You can use your own software licenses on Dedicated Hosts\. These are the general steps to follow in order to bring your own volume licensed machine image into Amazon EC2\.

1. Verify that the license terms controlling the use of your machine images \(AMIs\) allow the usage of a machine image in a virtualized cloud environment\. For more information about Microsoft Licensing, see [Amazon Web Services and Microsoft Licensing](https://aws.amazon.com/windows/faq/#licensing)\.

1. After you have verified that your machine image can be used within Amazon EC2, import your machine images using the ImportImage API operation made available by the VM Import/Export tools\. For information about restrictions and limitations, see [VM Import/Export Prerequisites](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/VMImportPrerequisites.html)\. For information about how to import your VM using ImportImage, see [ Importing a VM into Amazon EC2 Using ImportImage](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/UsingImportImage.html)\.

1. If you need a mechanism to track how your images were used in AWS, enable host recording in the AWS Config service\. You can use AWS Config to record configuration changes to a Dedicated Host and use the output as a data source for license reporting\. For more information, see [Tracking Configuration Changes with AWS Config](#dedicated-hosts-aws-config)\. 

1. After you've imported your machine image, you can launch instances from this image onto active Dedicated Hosts in your account\.

1. When you run these instances, depending on the operating system, you may be required to activate these instances against your own KMS server \(for example, Windows Server or Windows SQL Server\)\. You cannot activate your imported Windows AMI against the Amazon Windows KMS server\.

## Allocating Dedicated Hosts<a name="dedicated-hosts-allocating"></a>

To begin using Dedicated Hosts, they need to be allocated to your account\. You can use the AWS Management Console, interact directly with the API, or use the command line interface to perform these tasks\. Follow these steps every time you allocate a Dedicated Host\.

**To allocate Dedicated Hosts to your account**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dedicated Hosts** page, choose **Allocate Dedicated Host**\.

1. Configure your host using the options provided:

   1. **Instance type**—Instance type that is available on the Dedicated Host\.

   1. **Availability Zone**—The Availability Zone for the Dedicated Host\.

   1. **Allow instance auto\-placement**—The default setting is **Off**\. The Dedicated Host accepts `host` tenancy instance launches only \(provided capacity is available\)\. When instance auto\-placement is **On**, any instances with the tenancy of `host`, and matching the Dedicated Host's configuration, can be launched onto the host\. 

   1. **Quantity**—The number of hosts to allocate with these settings\.

1. Choose **Allocate host**\.

The Dedicated Host capacity is made available in your account immediately\.

If you launch instances with tenancy `host` but do not have any active Dedicated Hosts in your account, you receive an error and the instance launch fails\.

## Launching Instances onto Dedicated Hosts<a name="launching-dedicated-hosts-instances"></a>

After you have allocated a Dedicated Host, you can launch instances onto it\. Instances with the tenancy `host` can be launched onto a specific Dedicated Host or Amazon EC2 can select the appropriate Dedicated Hosts for you \(auto\-placement\)\. You cannot launch instances with the tenancy `host` if you do not have active Dedicated Hosts in your account with available capacity matching the instance type configuration of the instances you are launching\.

**Note**  
The instances launched onto Dedicated Hosts can only be launched in a VPC\. For more information, see [Introduction to VPC](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html)\.

Before you launch your instances, take note of the limitations\. For more information, see [Dedicated Hosts Limitations and Restrictions](dedicated-hosts-overview.md#dedicated-hosts-limitations)\.

**Launching instances onto a Dedicated Host from the Dedicated Hosts page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dedicated Hosts** page, select a host, choose **Actions**, **Launch Instance\(s\) onto Host**\.

1. Select the AMI to use\. If you have imported your own AMI, choose **My AMIs** on the left sidebar and select the relevant AMI\.

1. Choose the instance type for the Dedicated Host; this is the only instance type you can launch onto the host\.

1. On the **Configure Instance Details** page, the **Tenancy** and **Host** options are pre\-selected\. You can toggle the **Affinity** setting to **On** or **Off**\.

   + **On**—If stopped, the instance always restarts on that specific host\.

   + **Off**—The instance launches onto the specified Dedicated Host, but is not guaranteed to restart on it if stopped\.

1. Complete the rest of the steps and choose **Launch Instances**\.

The instance is automatically launched onto the Dedicated Host that you specified\. To view the instances on a Dedicated Host, go to the **Dedicated Hosts** page, and select the Dedicated Host that you specified when you launched the instance\.

**Launching instances onto a specific Dedicated Host from the Instances page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Instances** page, choose **Launch Instance**\.

1. Select an AMI from the list\. If you have imported your own AMI, choose **My AMIs** and select the imported image\. Not all AMIs can be used with Dedicated Hosts\.

1. Select the type of instance to launch\.

1. On the **Configure Instance Details** page, the Dedicated Host settings are:

   + **Tenancy**—**Dedicated host — Launch this instance on a Dedicated host**\. If you're not able to choose this, check whether you have selected an incompatible AMI or instance type\.

   + **Host**—Select a host\. If you are unable to select a Dedicated Host, check:

     + Whether the selected subnet is in a different Availability Zone to the host\.

     + That the instance type you've selected matches the instance type that the Dedicated Host supports\. If you don't have matching, running hosts, the only option available is **Use auto\-placement** but the instance launch fails unless there is available, matching Dedicated Host capacity in your account\.

   + **Affinity**—The default setting for this is **Off**\. The instance launches onto the specified Dedicated Host, but is not guaranteed to restart on it if stopped\.
**Note**  
If you are unable to see these settings, check that you have selected a VPC in the **Network** menu\.

1. Complete the rest of the configuration steps\. Choose **Review and Launch**\.

1. Choose **Launch**\.

1. Select an existing key pair, or create a new one\. Choose **Launch Instances**\.

**Launching instances onto any Dedicated Host from the Instances page**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Instances** page, choose **Launch Instance**\.

1. Select an AMI from the list\. If you have imported your own AMI, choose **My AMIs** and select the imported image\. Not all AMIs can be used with Dedicated Hosts\.

1. Select the type of instance to launch\.

1. On the **Configure Instance Details** page, the Dedicated Host settings are:

   + **Tenancy**—**Dedicated host — Launch this instance on a Dedicated host** If you're not able to choose this, check whether you have selected an incompatible AMI or instance type\.

   + **Host**—For this type of launch, keep the setting as **Use auto\-placement**\.

   + **Affinity**—The default setting for this is **Off**\. The instance launches onto any available Dedicated Host in your account, but is not guaranteed to restart on that host if stopped\.

   If you are unable to see these settings, check that you have selected a VPC in the **Network** menu\.

1. Complete the rest of the configuration steps\. Choose **Review and Launch**\.

1. Choose **Launch**\.

1. Select an existing key pair, or create a new one\. Choose **Launch Instances**\.

## Modifying Instance Tenancies<a name="moving-instances-dedicated-hosts"></a>

You can modify the tenancy of a Dedicated Instance from `dedicated` to `host`, and vice versa if it is not using a Windows, SUSE, or RHEL AMI provided by Amazon EC2\. You need to stop your Dedicated Instance to do this\. Instances with `shared` tenancy cannot be modified to `host` tenancy\.

**Modify instance tenancy from `dedicated` to `host`**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances**, then select the Dedicated Instances to modify\.

1. Choose **Actions**, **Instance State**, and **Stop**\.

1. Open the context \(right\-click\) menu on the instance and choose **Instance Settings**, **Modify Instance Placement**\.

1. On the **Modify Instance Placement** page, do the following:

   + **Tenancy**—Choose **Launch this instance on a Dedicated host**\.

   + **Affinity**—Choose either **This instance can run on any one of my Hosts** or **This instance can only run on the selected Host**\.

     If you choose **This instance can run on any one of my Hosts**, the instance launches onto any available, compatible Dedicated Hosts in your account\.

     If you choose **This instance can only run on the selected Host**, select a value for **Target Host**\. If no target host is listed, you may not have available, compatible Dedicated Hosts in your account\. 

1. Choose **Save**\.

1. When you restart your instance Amazon EC2 places your instance on an available Dedicated Host in your account, provided it supports the instance type that you're launching\.

## Managing and Releasing Dedicated Hosts<a name="dedicated-hosts-managing"></a>

You can use the console, interact directly with the API, or use the command line interface to view details about individual instances on a host and release an On\-Demand Dedicated Host\.

**To view details of instances on a Dedicated Host**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dedicated Hosts** page, select the host to view more information about\.

1. Choose the **Description** tab for information about the host\. Choose the **Instances** tab for information about instances running on your host\.

**To release a Dedicated Host**

Any running instances on the Dedicated Host need to be stopped before you can release the host\. These instances can be migrated to other Dedicated Hosts in your account so that you can continue to use them\. For more information, see [Modifying Instance Auto\-Placement and Host Affinity](dedicated-hosts-instance-placement.md#dedicated-hosts-instance-placement-controls)\. These steps apply only to On\-Demand Dedicated Hosts\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dedicated Hosts** page, select the Dedicated Host to release\.

1. Choose **Actions**, **Release Hosts**\.

1. Confirm your choice by choosing **Release**\.

After you release a Dedicated Host, you cannot reuse the same host or host ID again\.

When the Dedicated Host is released, you are no longer charged On\-Demand billing rates for it\. The Dedicated Host status is changed to `released` and you are not able to launch any instances onto that host\.

If you've recently released Dedicated Hosts, it may take some time for them to stop counting towards your limit\. During this time, you may experience `LimitExceeded` errors when trying to allocate new Dedicated Hosts\. If this is the case, try allocating new hosts again after a few minutes\.

The instances that were stopped are still available for use and are listed on the **Instances** page\. They retain their `host` tenancy setting\.

## API and CLI Command Overview<a name="dedicated-hosts-api-cli"></a>

You can perform the tasks described in this section using an API or the command line\.

**To allocate Dedicated Hosts to your account**

+ [allocate\-hosts](http://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-hosts.html) \(AWS CLI\)

+ [AllocateHosts](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-AllocateHosts.html) \(Amazon EC2 Query API\)

+ [New\-EC2Hosts](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Hosts.html) \(AWS Tools for Windows PowerShell\)

**To describe your Dedicated Hosts**

+ [describe\-hosts](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-hosts.html) \(AWS CLI\)

+ [DescribeHosts](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-DescribeHosts.html) \(Amazon EC2 Query API\)

+ [Get\-EC2Hosts](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Hosts.html) \(AWS Tools for Windows PowerShell\)

**To modify your Dedicated Hosts**

+ [modify\-hosts](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-hosts.html) \(AWS CLI\)

+ [ModifyHosts](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyHosts.html) \(Amazon EC2 Query API\)

+ [Edit\-EC2Hosts](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2Hosts.html) \(AWS Tools for Windows PowerShell\)

**To modify instance auto\-placement**

+ [modify\-instance\-placement](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) \(AWS CLI\)

+ [ModifyInstancePlacement](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ModifyInstancePlacement.html) \(Amazon EC2 Query API\)

+ [Edit\-EC2InstancePlacement](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) \(AWS Tools for Windows PowerShell\)

**To release your Dedicated Hosts**

+ [release\-hosts](http://docs.aws.amazon.com/cli/latest/reference/ec2/release-hosts.html) \(AWS CLI\)

+ [ReleaseHosts](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/ApiReference-query-ReleaseHosts.html) \(Amazon EC2 Query API\)

+ [Remove\-EC2Hosts](http://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Hosts.html) \(AWS Tools for Windows PowerShell\)

## Tracking Configuration Changes with AWS Config<a name="dedicated-hosts-aws-config"></a>

You can use AWS Config to record configuration changes for Dedicated Hosts, and instances that are launched, stopped, or terminated on them\. You can then use the information captured by AWS Config as a data source for license reporting\.

AWS Config records configuration information for Dedicated Hosts and instances individually and pairs this information through relationships\. There are three reporting conditions\.

+ **AWS Config recording status**—When **On**, AWS Config is recording one or more AWS resource types, which can include Dedicated Hosts and Dedicated Instances\. To capture the information required for license reporting, verify that hosts and instances are being recorded with the following fields\.

+ **Host recording status**—When **Enabled**, the configuration information for Dedicated Hosts is recorded\.

+ **Instance recording status**—When **Enabled**, the configuration information for Dedicated Instances is recorded\.

If any of these three conditions are disabled, the icon in the **Edit Config Recording** button is red\. To derive the full benefit of this tool, ensure that all three recording methods are enabled\. When all three are enabled, the icon is green\. To edit the settings, choose **Edit Config Recording**\. You are directed to the **Set up AWS Config** page in the AWS Config console, where you can set up AWS Config and start recording for your hosts, instances, and other supported resource types\. For more information, see [Setting up AWS Config using the Console](http://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html) in the *AWS Config Developer Guide*\.

**Note**  
AWS Config records your resources after it discovers them, which might take several minutes\. 

After AWS Config starts recording configuration changes to your hosts and instances, you can get the configuration history of any host that you have allocated or released and any instance that you have launched, stopped, or terminated\. For example, at any point in the configuration history of a Dedicated Host, you can look up how many instances are launched on that host alongside the number of sockets and cores on the host\. For any of those instances, you can also look up the ID of its Amazon Machine Image \(AMI\)\. You can use this information to report on licensing for your own server\-bound software that is licensed per\-socket or per\-core\.

You can view configuration histories in any of the following ways\.

+ By using the AWS Config console\. For each recorded resource, you can view a timeline page, which provides a history of configuration details\. To view this page, choose the gray icon in the **Config Timeline** column of the **Dedicated Hosts** page\. For more information, see [Viewing Configuration Details in the AWS Config Console](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-console.html) in the *AWS Config Developer Guide*\.

+ By running AWS CLI commands\. First, you can use the [list\-discovered\-resources](http://docs.aws.amazon.com/cli/latest/reference/configservice/list-discovered-resources.html) command to get a list of all hosts and instances\. Then, you can use the [get\-resource\-config\-history](http://docs.aws.amazon.com/cli/latest/reference/configservice/get-resource-config-history.html#get-resource-config-history) command to get the configuration details of a host or instance for a specific time interval\. For more information, see [View Configuration Details Using the CLI](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-cli.html) in the *AWS Config Developer Guide*\.

+ By using the AWS Config API in your applications\. First, you can use the [ListDiscoveredResources](http://docs.aws.amazon.com/config/latest/APIReference/API_ListDiscoveredResources.html) action to get a list of all hosts and instances\. Then, you can use the [GetResourceConfigHistory](http://docs.aws.amazon.com/config/latest/APIReference/API_GetResourceConfigHistory.html) action to get the configuration details of a host or instance for a specific time interval\.

For example, to get a list of all of your Dedicated Hosts from AWS Config, run a CLI command such as the following:

```
aws configservice list-discovered-resources --resource-type AWS::EC2::Host
```

To obtain the configuration history of a Dedicated Host from AWS Config, run a CLI command such as the following:

```
aws configservice get-resource-config-history --resource type AWS::EC2::Instance --resource-id i-36a47fdf
```

**To manage AWS Config settings using the AWS Management Console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the **Dedicated Hosts** page, choose **Edit Config Recording**\.

1. In the AWS Config console, follow the steps provided to turn on recording\. For more information, see [Setting up AWS Config using the Console](http://docs.aws.amazon.com/config/latest/developerguide//gs-console.html)\.

For more information, see [Viewing Configuration Details in the AWS Config Console](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-console.html)\.

 **To activate AWS Config using the command line or API**

+ Using the AWS CLI, see [Viewing Configuration Details in the AWS Config Console](http://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource-console.html) in the *AWS Config Developer Guide*\.

+ Using the Amazon EC2 API, see [GetResourceConfigHistory](http://docs.aws.amazon.com/config/latest/APIReference/API_GetResourceConfigHistory.html)\.
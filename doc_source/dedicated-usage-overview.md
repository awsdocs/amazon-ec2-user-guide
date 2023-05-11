# Work with Dedicated Instances<a name="dedicated-usage-overview"></a>

You can create a VPC with an instance tenancy of `dedicated` to ensure that all instances launched into the VPC are Dedicated Instances\. Alternatively, you can specify the tenancy of the instance during launch\. 

**Topics**
+ [Create a VPC with a dedicated instance tenancy](#creatingdedicatedvpc)
+ [Launch Dedicated Instances into a VPC](#dedicatedinstancesintovpc)
+ [Display tenancy information](#dedicated-gettingpgsinfo)
+ [Change the tenancy of an instance](#dedicated-change-tenancy)
+ [Change the tenancy of a VPC](#change-tenancy-vpc)

## Create a VPC with a dedicated instance tenancy<a name="creatingdedicatedvpc"></a>

When you create a VPC, you have the option of specifying its instance tenancy\. If you launch an instance into a VPC that has an instance tenancy of `dedicated`, your instance is automatically a Dedicated Instance, regardless of the tenancy of the instance\.

For more information about creating a VPC and choosing the tenancy options, see [Create a VPC](https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html) in the *Amazon VPC User Guide*\.

## Launch Dedicated Instances into a VPC<a name="dedicatedinstancesintovpc"></a>

You can launch a Dedicated Instance using the Amazon EC2 launch instance wizard\.

------
#### [ Console ]

**To launch a Dedicated Instance into a default tenancy VPC using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**, **Launch instance**\.

1. In the **Application and OS Images** section, select an AMI from the list\.

1. In the **Instance type** section, select the instance type to launch\.
**Note**  
Ensure that you choose an instance type that's supported as a Dedicated Instance\. For more information, see [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/)\.

1. In the **Key pair** section, select the key pair to associate with the instance\.

1. In the **Advanced details** section, for **Tenancy**, select **Dedicated**\.

1. Configure the remaining instance options as needed\. For more information, see [Launch an instance using defined parameters](ec2-launch-instance-wizard.md#liw-launch-instance-with-defined-parameters)\.

1. Choose **Launch instance**\.

------
#### [ Command line ]

**To set the tenancy option for an instance during launch using the command line**
+ [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)
+ [New\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

------

For more information about launching an instance with a tenancy of `host`, see [Launch instances onto a Dedicated Host](how-dedicated-hosts-work.md#launching-dedicated-hosts-instances)\.

## Display tenancy information<a name="dedicated-gettingpgsinfo"></a>

------
#### [ Console ]

**To display tenancy information for your VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Check the instance tenancy of your VPC in the **Tenancy** column\.

1. If the **Tenancy** column is not displayed, choose the settings icon \( ![\[Settings icon.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \) in the top\-right corner, toggle to choose **Tenancy**, and choose **Confirm**\.

**To display tenancy information for your instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Check the tenancy of your instance in the **Tenancy** column\.

1. If the **Tenancy** column is not displayed, do one of the following: 
   + Choose the settings icon \( ![\[Settings icon.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/settings-icon.png) \) in the top\-right corner, toggle to choose **Tenancy**, and choose **Confirm**\.
   + Select the instance\. On the **Details** tab near the bottom of the page, under **Host and placement group**, check the value for **Tenancy**\.

------
#### [ Command line ]

**To describe the tenancy of your VPC using the command line**
+ [describe\-vpcs](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) \(AWS CLI\)
+ [Get\-EC2Vpc](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

**To describe the tenancy of your instance using the command line**
+ [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)
+ [Get\-EC2Instance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

**To describe the tenancy value of a Reserved Instance using the command line**
+ [describe\-reserved\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) \(AWS CLI\)
+ [Get\-EC2ReservedInstance](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html) \(AWS Tools for Windows PowerShell\)

**To describe the tenancy value of a Reserved Instance offering using the command line**
+ [describe\-reserved\-instances\-offerings](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) \(AWS CLI\)
+ [Get\-EC2ReservedInstancesOffering](https://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html) \(AWS Tools for Windows PowerShell\)

------

## Change the tenancy of an instance<a name="dedicated-change-tenancy"></a>

You can change the tenancy of a stopped instance  after launch\. The changes that you make take effect the next time the instance starts\.

**Note**  
For T3 instances, you can't change the tenancy from `dedicated` to `host`, or from `host` to `dedicated`\. Attempting to make one of these unsupported tenancy changes results in the `InvalidTenancy` error code\.

------
#### [ Console ]

**To change the tenancy of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. Choose **Instance state**, **Stop instance**, **Stop**\.

1. Choose **Actions**, **Instance settings**, **Modify instance placement**\.

1. For **Tenancy**, choose whether to run your instance on dedicated hardware or on a Dedicated Host\. Choose **Save**\.

------
#### [ Command line ]

**To modify the tenancy value of an instance using the command line**
+ [ modify\-instance\-placement](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) \(AWS CLI\)
+ [ Edit\-EC2InstancePlacement](https://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) \(AWS Tools for Windows PowerShell\)

------

## Change the tenancy of a VPC<a name="change-tenancy-vpc"></a>

You can change the instance tenancy of a VPC from `dedicated` to `default` after you create it\. Modifying the instance tenancy of the VPC does not affect the tenancy of any existing instances in the VPC\. The next time you launch an instance in the VPC, it has a tenancy of `default`, unless you specify otherwise during launch\.

**Note**  
You cannot change the instance tenancy of a VPC from `default` to `dedicated` after it is created\.

You can modify the instance tenancy of a VPC using the AWS CLI, an AWS SDK, or the Amazon EC2 API only\.

------
#### [ Command line ]

**To modify the instance tenancy attribute of a VPC using the AWS CLI**  
Use the [modify\-vpc\-tenancy](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-tenancy.html) command and specify the ID of the VPC and instance tenancy value\. The only supported value is `default`\.

```
aws ec2 modify-vpc-tenancy --vpc-id vpc-1a2b3c4d --instance-tenancy default
```

------
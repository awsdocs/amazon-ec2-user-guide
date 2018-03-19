# Dedicated Instances<a name="dedicated-instance"></a>

Dedicated Instances are Amazon EC2 instances that run in a virtual private cloud \(VPC\) on hardware that's dedicated to a single customer\. Your Dedicated Instances are physically isolated at the host hardware level from instances that belong to other AWS accounts\. Dedicated Instances may share hardware with other instances from the same AWS account that are not Dedicated Instances\.

**Note**  
A *Dedicated Host* is also a physical server that's dedicated for your use\. With a Dedicated Host, you have visibility and control over how instances are placed on the server\. For more information, see [Dedicated Hosts](dedicated-hosts-overview.md)\.


+ [Dedicated Instance Basics](#dedicated-howitworks)
+ [Working with Dedicated Instances](#dedicated-usage-overview)

## Dedicated Instance Basics<a name="dedicated-howitworks"></a>

Each instance that you launch into a VPC has a tenancy attribute\. This attribute has the following values\.


| Tenancy Value | Description | 
| --- | --- | 
|  `default`  |  Your instance runs on shared hardware\.   | 
|  `dedicated`  |  Your instance runs on single\-tenant hardware\.   | 
|  `host`  | Your instance runs on a Dedicated Host, which is an isolated server with configurations that you can control\. | 

After you launch an instance, there are some limitations to changing its tenancy\.

+ You cannot change the tenancy of an instance from `default` to `dedicated` or `host` after you've launched it\.

+ You cannot change the tenancy of an instance from `dedicated` or `host` to `default` after you've launched it\.

You can change the tenancy of an instance from `dedicated` to `host`, or from `host` to `dedicated` after you've launched it\. For more information, see [Changing the Tenancy of an Instance](#dedicated-change-tenancy)\.

Each VPC has a related instance tenancy attribute\. This attribute has the following values\.


| Tenancy Value | Description | 
| --- | --- | 
|  `default`  |  An instance launched into the VPC runs on shared hardware by default, unless you explicitly specify a different tenancy during instance launch\.  | 
|  `dedicated`  |  An instance launched into the VPC is a Dedicated Instance by default, unless you explicitly specify a tenancy of `host` during instance launch\. You cannot specify a tenancy of `default` during instance launch\.  | 

You can change the instance tenancy of a VPC from `dedicated` to `default` after you create it\. You cannot change the instance tenancy of a VPC to `dedicated`\.

To create Dedicated Instances, you can do the following:

+ Create the VPC with the instance tenancy set to `dedicated` \(all instances launched into this VPC are Dedicated Instances\)\.

+ Create the VPC with the instance tenancy set to `default`, and specify a tenancy of `dedicated` for any instances when you launch them\.

### Dedicated Instances Limitations<a name="dedicated-limits"></a>

Some AWS services or their features won't work with a VPC with the instance tenancy set to `dedicated`\. Check the service's documentation to confirm if there are any limitations\.

Some instance types cannot be launched into a VPC with the instance tenancy set to `dedicated`\. For more information about supported instances types, see [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/)\.

### Amazon EBS with Dedicated Instances<a name="dedicated-instance-ebs"></a>

When you launch an Amazon EBS\-backed Dedicated Instance, the EBS volume doesn't run on single\-tenant hardware\.

### Reserved Instances with Dedicated Tenancy<a name="dedicatedreservedinstances"></a>

To guarantee that sufficient capacity is available to launch Dedicated Instances, you can purchase Dedicated Reserved Instances\. For more information, see [Reserved Instances](ec2-reserved-instances.md)\. 

When you purchase a Dedicated Reserved Instance, you are purchasing the capacity to launch a Dedicated Instance into a VPC at a much reduced usage fee; the price break in the usage charge applies only if you launch an instance with dedicated tenancy\. However, if you purchase a Reserved Instance with a default tenancy value, you won't get a Dedicated Reserved Instance if you launch an instance with `dedicated` instance tenancy\.

You can't use the modification process to change the tenancy of a Reserved Instance after you've purchased it\. However, you can exchange a Convertible Reserved Instance for a new Convertible Reserved Instance with a different tenancy\.

### Auto Scaling of Dedicated Instances<a name="dedicated-instance-autoscaling"></a>

For information about using Auto Scaling to launch Dedicated Instances, see [Auto Scaling in Amazon Virtual Private Cloud](http://docs.aws.amazon.com/autoscaling/latest/userguide/autoscalingsubnets.html) in the *Amazon EC2 Auto Scaling User Guide*\.

### Dedicated Spot Instances<a name="dedicated-instance-spot"></a>

You can run a Dedicated Spot Instance by specifying a tenancy of `dedicated` when you create a Spot Instance request\. For more information, see [Specifying a Tenancy for Your Spot Instances](spot-requests.md#spot-instance-tenancy)\.

### Pricing for Dedicated Instances<a name="dedicated-instance-pricing"></a>

Pricing for Dedicated Instances is different to pricing for On\-Demand Instances\. For more information, see the [Amazon EC2 Dedicated Instances product page](https://aws.amazon.com/dedicated-instances)\.

## Working with Dedicated Instances<a name="dedicated-usage-overview"></a>

You can create a VPC with an instance tenancy of `dedicated` to ensure that all instances launched into the VPC are Dedicated Instances\. Alternatively, you can specify the tenancy of the instance during launch\. 


+ [Creating a VPC with an Instance Tenancy of Dedicated](#creatingdedicatedvpc)
+ [Launching Dedicated Instances into a VPC](#dedicatedinstancesintovpc)
+ [Displaying Tenancy Information](#dedicated-gettingpgsinfo)
+ [Changing the Tenancy of an Instance](#dedicated-change-tenancy)
+ [Changing the Tenancy of a VPC](#change-tenancy-vpc)

### Creating a VPC with an Instance Tenancy of Dedicated<a name="creatingdedicatedvpc"></a>

When you create a VPC, you have the option of specifying its instance tenancy\. If you're using the Amazon VPC console, you can create a VPC using the VPC wizard or the **Your VPCs** page\.

**To create a VPC with an instance tenancy of dedicated \(VPC Wizard\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the dashboard, choose **Start VPC Wizard**\.

1. Select a VPC configuration, and then choose **Select**\.

1. On the next page of the wizard, choose **Dedicated** from the **Hardware tenancy** list\.

1. Choose **Create VPC**\.

**To create a VPC with an instance tenancy of dedicated \(Create VPC dialog box\)**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**, and then **Create VPC**\.

1. For **Tenancy**, choose **Dedicated**\. Specify the CIDR block, and choose **Yes, Create**\.

**To set the tenancy option when you create a VPC using the command line**

+ [create\-vpc](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-vpc.html) \(AWS CLI\)

+ [New\-EC2Vpc](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

If you launch an instance into a VPC that has an instance tenancy of `dedicated`, your instance is automatically a Dedicated Instance, regardless of the tenancy of the instance\.

### Launching Dedicated Instances into a VPC<a name="dedicatedinstancesintovpc"></a>

You can launch a Dedicated Instance using the Amazon EC2 launch instance wizard\.

**To launch a Dedicated Instance into a default tenancy VPC using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI and choose **Select**\.

1. On the **Choose an Instance Type** page, select the instance type and choose **Next: Configure Instance Details**\.
**Note**  
Ensure that you choose an instance type that's supported as a Dedicated Instance\. For more information, see [Amazon EC2 Dedicated Instances](https://aws.amazon.com/ec2/purchasing-options/dedicated-instances/)\.

1. On the **Configure Instance Details** page, select a VPC and subnet\. Choose **Dedicated \- Run a dedicated instance** from the **Tenancy** list, and then **Next: Add Storage**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch** to choose a key pair and launch the Dedicated Instance\.

For more information about launching an instance with a tenancy of `host`, see [Launching Instances onto Dedicated Hosts](how-dedicated-hosts-work.md#launching-dedicated-hosts-instances)\.

**To set the tenancy option for an instance during launch using the command line**

+ [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) \(AWS CLI\)

+ [New\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

### Displaying Tenancy Information<a name="dedicated-gettingpgsinfo"></a>

**To display tenancy information for your VPC using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\.

1. Check the instance tenancy of your VPC in the **Tenancy** column\.

1. If the **Tenancy** column is not displayed, choose **Edit Table Columns** \(the gear\-shaped icon\), **Tenancy** in the **Show/Hide Columns** dialog box, and then **Close**\.

**To display tenancy information for your instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Check the tenancy of your instance in the **Tenancy** column\.

1. If the **Tenancy** column is not displayed, do one of the following: 

   + Choose **Show/Hide Columns** \(the gear\-shaped icon\), **Tenancy** in the **Show/Hide Columns** dialog box, and then **Close**\.

   + Select the instance\. The **Description** tab in the details pane displays information about the instance, including its tenancy\.

**To describe the tenancy of your VPC using the command line**

+ [describe\-vpcs](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html) \(AWS CLI\)

+ [Get\-EC2Vpc](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Vpc.html) \(AWS Tools for Windows PowerShell\)

**To describe the tenancy of your instance using the command line**

+ [describe\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) \(AWS CLI\)

+ [Get\-EC2Instance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2Instance.html) \(AWS Tools for Windows PowerShell\)

**To describe the tenancy value of a Reserved Instance using the command line**

+ [describe\-reserved\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances.html) \(AWS CLI\)

+ [Get\-EC2ReservedInstance](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstance.html) \(AWS Tools for Windows PowerShell\)

**To describe the tenancy value of a Reserved Instance offering using the command line**

+ [describe\-reserved\-instances\-offerings](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-reserved-instances-offerings.html) \(AWS CLI\)

+ [Get\-EC2ReservedInstancesOffering](http://docs.aws.amazon.com/powershell/latest/reference/items/Get-EC2ReservedInstancesOffering.html) \(AWS Tools for Windows PowerShell\)

### Changing the Tenancy of an Instance<a name="dedicated-change-tenancy"></a>

Depending on your instance type and platform, you can change the tenancy of a stopped Dedicated Instance to `host` after launching it\. The next time the instance starts, it's started on a Dedicated Host that's allocated to your account\. For more information about allocating and working with Dedicated Hosts, and the instance types that can be used with Dedicated Hosts, see [Working with Dedicated Hosts](how-dedicated-hosts-work.md)\. Similarly, you can change the tenancy of a stopped Dedicated Host instance to `dedicated` after launching it\. The next time the instance starts, it's started on single\-tenant hardware that we control\.

**To change the tenancy of an instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances** and select your instance\.

1. Choose **Actions**, **Instance State**, **Stop**\.

1. Choose **Actions**, **Instance Settings**, **Modify Instance Placement**\.

1. In the **Tenancy** list, choose whether to run your instance on dedicated hardware or on a Dedicated Host\. Choose **Save**\.

**To modify the tenancy value of an instance using the command line**

+ [modify\-instance\-placement](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-placement.html) \(AWS CLI\)

+ [Edit\-EC2InstancePlacement](http://docs.aws.amazon.com/powershell/latest/reference/items/Edit-EC2InstancePlacement.html) \(AWS Tools for Windows PowerShell\)

### Changing the Tenancy of a VPC<a name="change-tenancy-vpc"></a>

You can change the instance tenancy attribute of a VPC from `dedicated` to `default`\. Modifying the instance tenancy of the VPC does not affect the tenancy of any existing instances in the VPC\. The next time you launch an instance in the VPC, it has a tenancy of `default`, unless you specify otherwise during launch\.

You cannot change the instance tenancy attribute of a VPC to `dedicated`\.

You can modify the instance tenancy attribute of a VPC using the AWS CLI, an AWS SDK, or the Amazon EC2 API only\.

**To modify the instance tenancy attribute of a VPC using the AWS CLI**

+ Use the [modify\-vpc\-tenancy](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-vpc-tenancy.html) command to specify the ID of the VPC and instance tenancy value\. The only supported value is `default`\.

  ```
  aws ec2 modify-vpc-tenancy --vpc-id vpc-1a2b3c4d --instance-tenancy default
  ```
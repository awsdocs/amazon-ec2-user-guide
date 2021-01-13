# Work with burstable performance instances<a name="burstable-performance-instances-how-to"></a>

The steps for launching, monitoring, and modifying these instances are similar\. The key difference is the default credit specification when they launch\. If you do not change the default credit specification, the default is that:
+ T3 and T4g instances launch as `unlimited`
+ T2 instances launch as `standard`

**Topics**
+ [Launch a burstable performance instance as Unlimited or Standard](#launch-burstable-performance-instances)
+ [Use an Auto Scaling group to launch a burstable performance instance as Unlimited](#burstable-performance-instances-auto-scaling-grp)
+ [View the credit specification of a burstable performance instance](#describe-burstable-performance-instances)
+ [Modify the credit specification of a burstable performance instance](#modify-burstable-performance-instances)
+ [Set the default credit specification for the account](#burstable-performance-instance-set-default-credit-specification-for-account)
+ [View the default credit specification](#burstable-performance-instances-get-default-credit-specification)

## Launch a burstable performance instance as Unlimited or Standard<a name="launch-burstable-performance-instances"></a>

You can launch your instances as `unlimited` or `standard` using the Amazon EC2 console, an AWS SDK, a command line tool, or with an Auto Scaling group\. For more information, see [Use an Auto Scaling group to launch a burstable performance instance as Unlimited](#burstable-performance-instances-auto-scaling-grp)\.

**Requirements**
+ You must launch your instances using an Amazon EBS volume as the root device\. For more information, see [Amazon EC2 root device volume](RootDeviceStorage.md)\.
+ For more information about AMI and driver requirements for these instances, see [Release notes](general-purpose-instances.md#general-purpose-instances-limits)\.

**To launch a burstable performance instance as Unlimited or Standard \(console\)**

1. Follow the [Launch an instance using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Choose an Instance Type** page, select an instance type, and choose **Next: Configure Instance Details**\.

1. Choose a credit specification\.

   1. To launch a T3 or T4g instance as `standard`, clear **Unlimited**\.

   1. To launch a T2 instance as `unlimited`, select **Unlimited**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\.

**To launch a burstable performance instance as Unlimited or Standard \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch your instances\. Specify the credit specification using the `--credit-specification CpuCredits=` parameter\. Valid credit specifications are `unlimited` and `standard`\.
+ For T3 and T4g, if you do not include the `--credit-specification` parameter, the instance launches as `unlimited` by default\.
+ For T2, if you do not include the `--credit-specification` parameter, the instance launches as `standard` by default\.

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t3.micro --key-name MyKeyPair --credit-specification "CpuCredits=unlimited"
```

## Use an Auto Scaling group to launch a burstable performance instance as Unlimited<a name="burstable-performance-instances-auto-scaling-grp"></a>

When burstable performance instances are launched or started, they require CPU credits for a good bootstrapping experience\. If you use an Auto Scaling group to launch your instances, we recommend that you configure your instances as `unlimited`\. If you do, the instances use surplus credits when they are automatically launched or restarted by the Auto Scaling group\. Using surplus credits prevents performance restrictions\.

### Create a launch template<a name="burstable-performance-instances-asg-launch-template"></a>

You must use a *launch template* for launching instances as `unlimited` in an Auto Scaling group\. A launch configuration does not support launching instances as `unlimited`\.

**To create a launch template that launches instances as Unlimited \(console\)**

1. Follow the [Creating a Launch Template for an Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) procedure\.

1. In **Launch template contents**, for **Instance type**, choose an instance size\.

1. To launch instances as `unlimited` in an Auto Scaling group, under **Advanced details**, for **Credit specification**, choose **Unlimited**\.

1. When you've finished defining the launch template parameters, choose **Create launch template**\. For more information, see [Creating a Launch Template for an Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create a launch template that launches instances as Unlimited \(AWS CLI\)**  
Use the [create\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) command and specify `unlimited` as the credit specification\.
+ For T3 and T4g, if you do not include the `CreditSpecification={CpuCredits=unlimited}` value, the instance launches as `unlimited` by default\.
+ For T2, if you do not include the `CreditSpecification={CpuCredits=unlimited}` value, the instance launches as `standard` by default\.

```
aws ec2 create-launch-template --launch-template-name MyLaunchTemplate --version-description FirstVersion --launch-template-data ImageId=ami-8c1be5f6,InstanceType=t3.medium,CreditSpecification={CpuCredits=unlimited}
```

### Associate an Auto Scaling group with a launch template<a name="burstable-performance-instances-create-asg-with-launch-template"></a>

To associate the launch template with an Auto Scaling group, create the Auto Scaling group using the launch template, or add the launch template to an existing Auto Scaling group\.

**To create an Auto Scaling group using a launch template \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation bar at the top of the screen, select the same Region that you used when you created the launch template\.

1. In the navigation pane, choose **Auto Scaling Groups**, **Create Auto Scaling group**\.

1. Choose **Launch Template**, select your launch template, and then choose **Next Step**\.

1. Complete the fields for the Auto Scaling group\. When you've finished reviewing your configuration settings on the **Review page**, choose **Create Auto Scaling group**\. For more information, see [Creating an Auto Scaling Group Using a Launch Template](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create an Auto Scaling group using a launch template \(AWS CLI\)**  
Use the [create\-auto\-scaling\-group](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

**To add a launch template to an existing Auto Scaling group \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation bar at the top of the screen, select the same Region that you used when you created the launch template\.

1. In the navigation pane, choose **Auto Scaling Groups**\.

1. From the Auto Scaling group list, select an Auto Scaling group, and choose **Actions**, **Edit**\.

1. On the **Details** tab, for **Launch Template**, choose a launch template, and then choose **Save**\.

**To add a launch template to an existing Auto Scaling group \(AWS CLI\)**  
Use the [update\-auto\-scaling\-group](https://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

## View the credit specification of a burstable performance instance<a name="describe-burstable-performance-instances"></a>

You can view the credit specification \(`unlimited` or `standard`\) of a running or stopped instance\.

------
#### [ New console ]

**To view the credit specification of a burstable instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance\.

1. Choose **Details** and view the **Credit specification** field\. The value is either `unlimited` or `standard`\.

------
#### [ Old console ]

**To view the credit specification of a burstable instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance\.

1. Choose **Description** and view the **T2/T3 Unlimited** field\.
   + If the value is `Enabled`, then your instance is configured as `unlimited`\.
   + If the value is `Disabled`, then your instance is configured as `standard`\.

------

**To describe the credit specification of a burstable performance instance \(AWS CLI\)**  
Use the [describe\-instance\-credit\-specifications](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-credit-specifications.html) command\. If you do not specify one or more instance IDs, all instances with the credit specification of `unlimited` are returned, as well as instances that were previously configured with the `unlimited` credit specification\. For example, if you resize a T3 instance to an M4 instance, while it is configured as `unlimited`, Amazon EC2 returns the M4 instance\.

**Example**  

```
aws ec2 describe-instance-credit-specifications --instance-id i-1234567890abcdef0
```
The following is example output:  

```
{
  "InstanceCreditSpecifications": [
    {
        "InstanceId": "i-1234567890abcdef0",
        "CpuCredits": "unlimited"
    }
  ]
}
```

## Modify the credit specification of a burstable performance instance<a name="modify-burstable-performance-instances"></a>

You can switch the credit specification of a running or stopped instance at any time between `unlimited` and `standard`\.

------
#### [ New console ]

**To modify the credit specification of a burstable performance instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance\. To modify the credit specification for several instances at one time, select all applicable instances\.

1. Choose **Actions**, **Instance settings**, **Change credit specification**\. This option is enabled only if you selected a burstable performance instance\.

1. To change the credit specification to `unlimited`, select the check box next to the instance ID\. To change the credit specification to `standard`, clear the check box next to the instance ID\.

------
#### [ Old console ]

**To modify the credit specification of a burstable performance instance**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**\.

1. Select the instance\. To modify the credit specification for several instances at one time, select all applicable instances\.

1. Choose **Actions**, **Instance Settings**, **Change T2/T3 Unlimited**\. This option is enabled only if you selected a burstable performance instance\.

1. The current credit specification appears in parentheses after the instance ID\. To change the credit specification to `unlimited`, choose **Enable**\. To change the credit specification to `standard`, choose **Disable**\.

------

**To modify the credit specification of a burstable performance instance \(AWS CLI\)**  
Use the [modify\-instance\-credit\-specification](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-credit-specification.html) command\. Specify the instance and its credit specification using the `--instance-credit-specification` parameter\. Valid credit specifications are `unlimited` and `standard`\.

**Example**  

```
aws ec2 modify-instance-credit-specification --region us-east-1 --instance-credit-specification "InstanceId=i-1234567890abcdef0,CpuCredits=unlimited"
```
The following is example output:  

```
{
  "SuccessfulInstanceCreditSpecifications": [
    {
      "InstanceId": "i- 1234567890abcdef0"
    }
  ],
  "UnsuccessfulInstanceCreditSpecifications": []
}
```

## Set the default credit specification for the account<a name="burstable-performance-instance-set-default-credit-specification-for-account"></a>

You can set the default credit specification at the account level per AWS Region\. You specify the default credit specification per instance family \(for example, T2 or T3\)\.

If you use the Launch Instance Wizard in the AWS Management Console to launch instances, the value you select for the credit specification overrides the account\-level default credit specification\. If you use the AWS CLI to launch instances, all new burstable performance instances in the account launch using the default credit option\. The credit specification for existing running or stopped instances is not affected\.

The `modify-default-credit-specification` API is an asynchronous operation, which works at an AWS Region level and modifies the credit option for each Availability Zone\. All zones in a Region are updated within five minutes\. But if instances are launched during this operation, they might not get the new credit option until the zone is updated\. To verify whether the update has occurred, you can call `get-default-credit-specification` and check the default credit specification for updates\. For more information, see [View the default credit specification](#burstable-performance-instances-get-default-credit-specification)\.

**Consideration**  
The default credit specification for an instance family can be modified only once in a rolling 5\-minute period, and up to four times in a rolling 24\-hour period\.

**To set the default credit specification at the account level \(AWS CLI\)**  
Use the [modify\-default\-credit\-specification](https://docs.aws.amazon.com/cli/latest/reference/ec2/modify-default-credit-specification.html) command\. Specify the AWS Region, instance family, and the default credit specification using the `--cpu-credits` parameter\. Valid default credit specifications are `unlimited` and `standard`\.

```
aws ec2 modify-default-credit-specification --region us-east-1 --instance-family t2 --cpu-credits unlimited
```

## View the default credit specification<a name="burstable-performance-instances-get-default-credit-specification"></a>

You can view the default credit specification of a burstable performance instance family at the account level per AWS Region\.

**To view the default credit specification at the account level \(AWS CLI\)**  
Use the [get\-default\-credit\-specification](https://docs.aws.amazon.com/cli/latest/reference/ec2/get-default-credit-specification.html) command\. Specify the AWS Region and instance family\.

```
aws ec2 get-default-credit-specification --region us-east-1 --instance-family t2
```
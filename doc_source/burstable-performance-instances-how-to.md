# Working with Burstable Performance Instances<a name="burstable-performance-instances-how-to"></a>

The steps for launching, monitoring, and modifying these instances are similar\. The key difference is the default credit specification when they launch: 
+ T3 instances launch as `unlimited` by default\.
+ T2 instances launch as `standard` by default\.

**Topics**
+ [Launching a Burstable Performance Instance as Unlimited or Standard](#launch-burstable-performance-instances)
+ [Using an Auto Scaling Group to Launch a Burstable Performance Instance as Unlimited](#burstable-performance-instances-auto-scaling-grp)
+ [Viewing the Credit Specification of a Burstable Performance Instance](#describe-burstable-performance-instances)
+ [Modifying the Credit Specification of a Burstable Performance Instance](#modify-burstable-performance-instances)

## Launching a Burstable Performance Instance as Unlimited or Standard<a name="launch-burstable-performance-instances"></a>

T3 instances launch as `unlimited` by default\. T2 instances launch as `standard` by default\.

For more information about AMI and driver requirements for these instances, see [Release Notes](general-purpose-instances.md#general-purpose-instances-limits)\.

You must launch your instances using an Amazon EBS volume as the root device\. For more information, see [Amazon EC2 Root Device Volume](RootDeviceStorage.md)\.

You can launch your instances as `unlimited` or `standard` using the Amazon EC2 console, an AWS SDK, a command line tool, or with an Auto Scaling group\. For more information, see [Using an Auto Scaling Group to Launch a Burstable Performance Instance as Unlimited](#burstable-performance-instances-auto-scaling-grp)\.

**To launch a burstable performance instance as Unlimited or Standard \(console\)**

1. Follow the [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Choose an Instance Type** page, select an instance type, and choose **Next: Configure Instance Details**\.

1. Choose a credit specification\. The default for T3 is `unlimited`, and for T2 it is `standard`\.

   1. To launch a T3 instance as `standard`, on the **Configure Instance Details** page, for **T2/T3 Unlimited**, clear **Enable**\.

   1. To launch a T2 instance as `unlimited`, on the **Configure Instance Details** page, for **T2/T3 Unlimited**, select **Enable**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To launch a burstable performance instance as Unlimited or Standard \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch your instances\. Specify the credit specification using the `--credit-specification CpuCredits=` parameter\. Valid credit specifications are `unlimited` and `standard`\.
+ For T3, if you do not include the `--credit-specification` parameter, the instance launches as `unlimited` by default\.
+ For T2, if you do not include the `--credit-specification` parameter, the instance launches as `standard` by default\.

```
aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t3.micro --key-name MyKeyPair --credit-specification "CpuCredits=unlimited"
```

## Using an Auto Scaling Group to Launch a Burstable Performance Instance as Unlimited<a name="burstable-performance-instances-auto-scaling-grp"></a>

When burstable performance instances are launched or started, they require CPU credits for a good bootstrapping experience\. If you use an Auto Scaling group to launch your instances, we recommend that you configure your instances as `unlimited`\. If you do, the instances use surplus credits when they are automatically launched or restarted by the Auto Scaling group\. Using surplus credits prevents performance restrictions\.

### Creating a Launch Template<a name="burstable-performance-instances-asg-launch-template"></a>

You must use a *launch template* for launching instances as `unlimited` in an Auto Scaling group\. A launch configuration does not support launching instances as `unlimited`\.

**To create a launch template that launches instances as Unlimited \(console\)**

1. Follow the [Creating a Launch Template for an Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) procedure\.

1. In **Launch template contents**, for **Instance type**, choose a T3 or T2 instance size\.

1. To launch instances as `unlimited` in an Auto Scaling group, in **Advanced details**, for **T2/T3 Unlimited**, choose **Enable**\.

1. When you've finished defining the launch template parameters, choose **Create launch template**\. For more information, see [Creating a Launch Template for an Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create a launch template that launches instances as Unlimited \(AWS CLI\)**  
Use the [create\-launch\-template](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) command and specify `unlimited` as the credit specification\.
+ For T3, if you do not include the `CreditSpecification={CpuCredits=unlimited}` value, the instance launches as `unlimited` by default\.
+ For T2, if you do not include the `CreditSpecification={CpuCredits=unlimited}` value, the instance launches as `standard` by default\.

```
aws ec2 create-launch-template --launch-template-name MyLaunchTemplate --version-description FirstVersion --launch-template-data ImageId=ami-8c1be5f6,InstanceType=t3.medium,CreditSpecification={CpuCredits=unlimited}
```

### Associating an Auto Scaling Group with a Launch Template<a name="burstable-performance-instances-create-asg-with-launch-template"></a>

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

## Viewing the Credit Specification of a Burstable Performance Instance<a name="describe-burstable-performance-instances"></a>

You can view the credit specification \(`unlimited` or `standard`\) of a running or stopped instance\.

**To view the credit specification of a burstable instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the instance\.

1. Choose **Description** and view the **T2/T3 Unlimited** field\.
   + If the value is `Enabled`, then your instance is configured as `unlimited`\.
   + If the value is `Disabled`, then your instance is configured as `standard` \.

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

## Modifying the Credit Specification of a Burstable Performance Instance<a name="modify-burstable-performance-instances"></a>

You can switch the credit specification of a running or stopped instance at any time between `unlimited` and `standard`\.

**To modify the credit specification of a burstable performance instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the instance\. To modify the credit specification for several instances at one time, select all applicable instances\.

1. Choose **Actions**, **Instance Settings**, **Change T2/T3 Unlimited**\.
**Note**  
The **Change T2/T3 Unlimited** option is enabled only if you select a T3 or T2 instance\.

1. To change the credit specification to `unlimited`, choose **Enable**\. To change the credit specification to `standard`, choose **Disable**\. The current credit specification for the instance appears in parentheses after the instance ID\.

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
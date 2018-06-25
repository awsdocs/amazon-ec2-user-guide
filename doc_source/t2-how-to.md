# Working With T2 Instances<a name="t2-how-to"></a>

**Topics**
+ [Launching a T2 Instance as Unlimited](#launch-t2)
+ [Using an Auto Scaling Group to Launch a T2 Unlimited Instance](#t2-auto-scaling-grp)
+ [Viewing the Credit Option for CPU Usage of a T2 Instance](#describe-t2)
+ [Modifying the Credit Option for CPU Usage of a T2 Instance](#modify-t2)

## Launching a T2 Instance as Unlimited<a name="launch-t2"></a>

When you launch a T2 instance, the instance launches as `standard` by default\. To launch a T2 instance as `unlimited`, you must specify the `unlimited` option\.

You can launch a T2 Unlimited instance using the Amazon EC2 console, an AWS SDK, a command line tool, or with an Auto Scaling group\. For more information, see [Using an Auto Scaling Group to Launch a T2 Unlimited Instance](#t2-auto-scaling-grp)\.

**To launch a T2 instance as Unlimited using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI, and choose **Select**\.

1. On the **Choose an Instance Type** page, select a T2 instance type, and choose **Next: Configure Instance Details**\.
**Note**  
T2 instance types are the only instance types that use CPU credits for CPU usage\.

1. On the **Configure Instance Details** page, for **T2 Unlimited**, choose **Enable**, and then choose **Next: Add Storage**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch** to choose a key pair and launch the T2 instance\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To launch a T2 instance as Unlimited using the AWS CLI**
+ Launch a T2 instance using the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command\. Specify the credit option using the `--credit-specification CpuCredits=` parameter\. Valid credit options are `standard` and `unlimited`\. If you do not include the `--credit-specification` parameter, the instance launches as `standard` by default\.

  ```
  aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --credit-specification CpuCredits=unlimited
  ```

## Using an Auto Scaling Group to Launch a T2 Unlimited Instance<a name="t2-auto-scaling-grp"></a>

When T2 instances are launched or started, they require CPU credits for a good bootstrapping experience\. If you use an Auto Scaling group to launch your T2 instances, we recommend that you configure the T2 instances as Unlimited so that they use surplus credits when they are automatically launched or restarted by the Auto Scaling group\. Using surplus credits prevents performance restrictions\.

You must use a launch template for launching a T2 instance as Unlimited in an Auto Scaling group; a launch configuration does not support launching a T2 instance as Unlimited\.

**To create a launch template that launches a T2 Unlimited instance using the AWS CLI**
+ To create a launch template that launches a T2 Unlimited instance, use the [create\-launch\-template](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) command and specify `unlimited` as the credit option for CPU usage\.  
**Example**  

  ```
  aws ec2 create-launch-template --launch-template-name MyLaunchTemplate --version-description FirstVersion --launch-template-data ImageId=ami-8c1be5f6,InstanceType=t2.medium,CreditSpecification={CpuCredits=unlimited} 
  ```

To associate the launch template with an Auto Scaling group, create the group with the launch template, or add the launch template to an existing group\.

**To create an Auto Scaling group with a launch template using the AWS CLI**
+ Use the [create\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

**To add a launch template to an Auto Scaling group using the AWS CLI**
+ Use the [update\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

For more information, see [Creating an Auto Scaling Group Using a Launch Template](http://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

## Viewing the Credit Option for CPU Usage of a T2 Instance<a name="describe-t2"></a>

You can view the credit option \(`standard` or `unlimited`\) of a running or stopped T2 instance\.

**To view the credit option for CPU usage using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the T2 instance\.

1. Choose **Description** and view the **T2 Unlimited** field\.
   + If the value is `Enabled`, then your instance is configured as T2 Unlimited\.
   + If the value is `Disabled`, then your instance is configured as T2 Standard\.

**To describe the credit option for CPU usage using the AWS CLI**
+ Describe the credit option for CPU usage using the [describe\-instance\-credit\-specifications](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-credit-specifications.html) command\. If you do not specify one or more instance IDs, all T2 instances with the credit option of `unlimited` are returned\.  
**Example**  

  ```
  aws ec2 describe-instance-credit-specifications --instance-id i-1234567890abcdef0
  ```

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

## Modifying the Credit Option for CPU Usage of a T2 Instance<a name="modify-t2"></a>

You can switch the credit option for CPU usage of a running or stopped T2 instance at any time from `standard` to `unlimited`, and from `unlimited` to `standard`\.

**To modify the credit option for CPU usage of a T2 instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the T2 instance\. To modify the credit option for several T2 instances at once, select all applicable instances\.

1. Choose **Actions**, **Instance Settings**, **Change T2 Unlimited**\.
**Note**  
The **Change T2 Unlimited** option is enabled only if you select a T2 instance\.

1. To change the credit option to `unlimited`, choose **Enable**\. To change the credit option to `standard`, choose **Disable**\. The current credit option for the T2 instance appears in parentheses after the instance ID\.

**To modify the credit option for CPU usage of a T2 instance using the AWS CLI**
+ Modify the credit option for CPU usage for a T2 instance using the [modify\-instance\-credit\-specification](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-credit-specification.html) command\. Specify the instance and its credit option using the `--instance-credit-specification` parameter\. Valid credit options are `standard` and `unlimited`\.

  ```
  aws ec2 modify-instance-credit-specification --region us-east-1 --instance-credit-specification '[{"InstanceId": "i-1234567890abcdef0","CpuCredits": "unlimited"}]' 
  ```

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
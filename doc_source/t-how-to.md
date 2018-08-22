# Working with T2 and T3 Instances<a name="t-how-to"></a>

The steps for launching, monitoring, and modifying T2 and T3 instances are similar\. The key difference is the default credit specification when they launch: 
+ T3 instances launch as `unlimited` by default\.
+ T2 instances launch as `standard` by default\.

**Topics**
+ [Working with T3 Instances](#t3-how-to)
+ [Working with T2 Instances](#t2-how-to)

## Working with T3 Instances<a name="t3-how-to"></a>

**Topics**
+ [Launching a T3 Instance as Standard or Unlimited](#launch-t3)
+ [Using an Auto Scaling Group to Launch a T3 Instance as Unlimited](#t3-auto-scaling-grp)
+ [Viewing the Credit Option for CPU Usage of a T3 Instance](#describe-t3)
+ [Modifying the Credit Option for CPU Usage of a T3 Instance](#modify-t3)

### Launching a T3 Instance as Standard or Unlimited<a name="launch-t3"></a>

T3 instances launch as `unlimited` by default\.

You can launch a T3 instance as `standard` or `unlimited` using the Amazon EC2 console, an AWS SDK, a command line tool, or with an Auto Scaling group\. For more information, see [Using an Auto Scaling Group to Launch a T3 Instance as Unlimited](#t3-auto-scaling-grp)\.

**To launch T3 instance as Standard or Unlimited using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI, and choose **Select**\.

1. On the **Choose an Instance Type** page, select a T3 instance type, and choose **Next: Configure Instance Details**\.

1. Choose a credit specification:

   1. To launch the instance as `standard`, on the **Configure Instance Details** page, for **T2/T3 Unlimited**, clear **Enable**, and then choose **Next: Add Storage**\.

   1. To launch the instance as `unlimited`, on the **Configure Instance Details** page, for **T2/T3 Unlimited**, select **Enable**, and then choose **Next: Add Storage**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To launch a T3 instance as Standard or Unlimited using the AWS CLI**
+ Use the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch a T3 instance\. Specify the credit option using the `--credit-specification CpuCredits=` parameter\. Valid credit options are `standard` and `unlimited`\.

  For T3, if you do not include the `--credit-specification` parameter, the instance launches as `unlimited` by default\.

  ```
  aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t3.micro --key-name MyKeyPair --credit-specification "CpuCredits=unlimited"
  ```

### Using an Auto Scaling Group to Launch a T3 Instance as Unlimited<a name="t3-auto-scaling-grp"></a>

When T3 instances are launched or started, they require CPU credits for a good bootstrapping experience\. If you use an Auto Scaling group to launch your T3 instances, we recommend that you configure your T3 instances as `unlimited` so that they use surplus credits when they are automatically launched or restarted by the Auto Scaling group\. Using surplus credits prevents performance restrictions\.

You must use a *launch template* for launching a T3 instance as `unlimited` in an Auto Scaling group\. A launch configuration does not support launching a T3 instance as `unlimited`\.

#### Creating a Launch Template<a name="t-asg-launch-template"></a>

**To create a launch template that launches a T3 instance as Unlimited using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Choose **Create launch template**, and complete the launch template fields\. For more information, see [Creating a Launch Template](ec2-launch-templates.md#create-launch-template)\.
**Note**  
If you do not specify a value for **T2/T3 Unlimited** in the launch template, the Auto Scaling group launches your T3 instances as `unlimited` by default\.

1. Choose **Create launch template**\.

**To create a launch template that launches a T3 instance as Unlimited using the AWS CLI**
+ Use the [create\-launch\-template](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) command and specify `unlimited` as the credit option for CPU usage\. 
  + For T3, if you do not include the `CreditSpecification={CpuCredits=unlimited}` value, the instance launches as `unlimited` by default\.  
**Example**  

  ```
  aws ec2 create-launch-template --launch-template-name MyLaunchTemplate --version-description FirstVersion --launch-template-data ImageId=ami-8c1be5f6,InstanceType=t3.medium,CreditSpecification={CpuCredits=unlimited}
  ```

#### Associating an Auto Scaling Group with a Launch Template<a name="t3-create-asg-with-launch-template"></a>

To associate the launch template with an Auto Scaling group, create the Auto Scaling group using the launch template, or add the launch template to an existing Auto Scaling group\.

**To create an Auto Scaling group using a launch template using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation bar at the top of the screen, select the same region that you used when you created the launch template\.

1. In the navigation pane, choose **Auto Scaling Groups**, and then choose **Create Auto Scaling group**\.

1. Choose **Launch Template**, select your launch template, and then choose **Next Step**\.

1. Complete the fields for the Auto Scaling group\. When you've finished reviewing your configuration settings on the **Review page**, choose **Create Auto Scaling group**\. For more information, see [Creating an Auto Scaling Group Using a Launch Template](http://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create an Auto Scaling group using a launch template using the AWS CLI**
+ Use the [create\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

**To add a launch template to an existing Auto Scaling group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation bar at the top of the screen, select the same region that you used when you created the launch template\.

1. In the navigation pane, choose **Auto Scaling Groups**\.

1. From the Auto Scaling group list, select an Auto Scaling group, and choose **Actions**, **Edit**\.

1. On the **Details** tab, for **Launch Template**, choose a launch template, and then choose **Save**\.

**To add a launch template to an existing Auto Scaling group using the AWS CLI**
+ Use the [update\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

### Viewing the Credit Option for CPU Usage of a T3 Instance<a name="describe-t3"></a>

You can view the credit option \(`standard` or `unlimited`\) of a running or stopped T3 instance\.

**To view the credit option for CPU usage using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the T3 instance\.

1. Choose **Description** and view the **T2/T3 Unlimited** field\.
   + If the value is `Enabled`, then your instance is configured as `unlimited`\.
   + If the value is `Disabled`, then your instance is configured as `standard` \.

**To describe the credit option for CPU usage using the AWS CLI**
+ Use the [describe\-instance\-credit\-specifications](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-credit-specifications.html) command\. If you do not specify one or more instance IDs, all T2 or T3 instances with the credit option of `unlimited` are returned\.  
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

### Modifying the Credit Option for CPU Usage of a T3 Instance<a name="modify-t3"></a>

You can switch the credit option for CPU usage of a running or stopped T3 instance at any time between `unlimited` and `standard`\.

**To modify the credit option for CPU usage of a T3 instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the T3 instance\. To modify the credit option for several T2 or T3 instances at one time, select all applicable instances\.

1. Choose **Actions**, **Instance Settings**, **Change T2/T3 Unlimited**\.
**Note**  
The **Change T2/T3 Unlimited** option is enabled only if you select a T2 or T3 instance\.

1. To change the credit option to `unlimited`, choose **Enable**\. To change the credit option to `standard`, choose **Disable**\. The current credit option for the T3 instance appears in parentheses after the instance ID\.

**To modify the credit option for CPU usage of a T3 instance using the AWS CLI**
+ Use the [modify\-instance\-credit\-specification](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-credit-specification.html) command\. Specify the instance and its credit option using the `--instance-credit-specification` parameter\. Valid credit options are `standard` and `unlimited`\.

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

## Working with T2 Instances<a name="t2-how-to"></a>

**Topics**
+ [Launching a T2 Instance as Standard or Unlimited](#launch-t2)
+ [Using an Auto Scaling Group to Launch a T2 Instance as Unlimited](#t2-auto-scaling-grp)
+ [Viewing the Credit Option for CPU Usage of a T2 Instance](#describe-t2)
+ [Modifying the Credit Option for CPU Usage of a T2 Instance](#modify-t2)

### Launching a T2 Instance as Standard or Unlimited<a name="launch-t2"></a>

T2 instances launch as `standard` by default\. To launch a T2 instance as `unlimited`, you must specify the `unlimited` option at launch\.

You can launch a T2 instance as `standard` or `unlimited` using the Amazon EC2 console, an AWS SDK, a command line tool, or with an Auto Scaling group\. For more information, see [Using an Auto Scaling Group to Launch a T2 Instance as Unlimited](#t2-auto-scaling-grp)\.

**To launch a T2 instance as Standard or Unlimited using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Launch Instance**\.

1. On the **Choose an Amazon Machine Image \(AMI\)** page, select an AMI, and choose **Select**\.

1. On the **Choose an Instance Type** page, select a T2 instance type, and choose **Next: Configure Instance Details**\.

1. Choose a credit specification:

   1. To launch the instance as `standard`, on the **Configure Instance Details** page, for **T2/T3 Unlimited**, clear **Enable**, and then choose **Next: Add Storage**\.

   1. To launch the instance as `unlimited`, on the **Configure Instance Details** page, for **T2/T3 Unlimited**, select **Enable**, and then choose **Next: Add Storage**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To launch a T2 instance as Standard or Unlimited using the AWS CLI**
+ Use the [run\-instances](http://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) command to launch a T2 instance\. Specify the credit option using the `--credit-specification CpuCredits=` parameter\. Valid credit options are `standard` and `unlimited`\.

  For T2, if you do not include the `--credit-specification` parameter, the instance launches as `standard` by default\.

  ```
  aws ec2 run-instances --image-id ami-abc12345 --count 1 --instance-type t2.micro --key-name MyKeyPair --credit-specification "CpuCredits=unlimited"
  ```

### Using an Auto Scaling Group to Launch a T2 Instance as Unlimited<a name="t2-auto-scaling-grp"></a>

When T2 instances are launched or started, they require CPU credits for a good bootstrapping experience\. If you use an Auto Scaling group to launch your T2 instances, we recommend that you configure your T2 instances as `unlimited` so that they use surplus credits when they are automatically launched or restarted by the Auto Scaling group\. Using surplus credits prevents performance restrictions\.

You must use a *launch template* for launching a T2 instance as `unlimited` in an Auto Scaling group\. A launch configuration does not support launching a T2 instance as `unlimited`\.

#### Creating a Launch Template<a name="t-asg-launch-template"></a>

**To create a launch template that launches a T2 instance as Unlimited using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Launch Templates**\.

1. Choose **Create launch template**, and complete the launch template fields\. For more information, see [Creating a Launch Template](ec2-launch-templates.md#create-launch-template)\.
**Important**  
T2 instances are launched as `standard` by default\. For **T2/T3 Unlimited** in the launch template, choose `unlimited` so that the Auto Scaling group launches your T2 instances as `unlimited` by default\.

1. Choose **Create launch template**\.

**To create a launch template that launches a T2 instance as Unlimited using the AWS CLI**
+ Use the [create\-launch\-template](http://docs.aws.amazon.com/cli/latest/reference/ec2/create-launch-template.html) command and specify `unlimited` as the credit option for CPU usage\. 

  For T2, if you do not include the `CreditSpecification={CpuCredits=unlimited}` value, the instance launches as `standard` by default\.  
**Example**  

  ```
  aws ec2 create-launch-template --launch-template-name MyLaunchTemplate --version-description FirstVersion --launch-template-data ImageId=ami-8c1be5f6,InstanceType=t2.medium,CreditSpecification={CpuCredits=unlimited}
  ```

#### Associating an Auto Scaling Group with a Launch Template<a name="t-create-asg-with-launch-template"></a>

To associate the launch template with an Auto Scaling group, create the Auto Scaling group using the launch template, or add the launch template to an existing Auto Scaling group\.

**To create an Auto Scaling group using a launch template using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation bar at the top of the screen, select the same region that you used when you created the launch template\.

1. In the navigation pane, choose **Auto Scaling Groups**, and then choose **Create Auto Scaling group**\.

1. Choose **Launch Template**, select your launch template, and then choose **Next Step**\.

1. Complete the fields for the Auto Scaling group\. When you've finished reviewing your configuration settings on the **Review page**, choose **Create Auto Scaling group**\. For more information, see [Creating an Auto Scaling Group Using a Launch Template](http://docs.aws.amazon.com/autoscaling/ec2/userguide/create-asg-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.

**To create an Auto Scaling group using a launch template using the AWS CLI**
+ Use the [create\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/create-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

**To add a launch template to an existing Auto Scaling group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation bar at the top of the screen, select the same Region that you used when you created the launch template\.

1. In the navigation pane, choose **Auto Scaling Groups**\.

1. From the Auto Scaling group list, select an Auto Scaling group, and choose **Actions**, **Edit**\.

1. On the **Details** tab, for **Launch Template**, choose a launch template, and then choose **Save**\.

**To add a launch template to an existing Auto Scaling group using the AWS CLI**
+ Use the [update\-auto\-scaling\-group](http://docs.aws.amazon.com/cli/latest/reference/autoscaling/update-auto-scaling-group.html) AWS CLI command and specify the `--launch-template` parameter\. 

### Viewing the Credit Option for CPU Usage of a T2 Instance<a name="describe-t2"></a>

You can view the credit option \(`standard` or `unlimited`\) of a running or stopped T2 instance\.

**To view the credit option for CPU usage using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the T2 instance\.

1. Choose **Description** and view the **T2/T3 Unlimited** field\.
   + If the value is `Enabled`, then your instance is configured as `unlimited`\.
   + If the value is `Disabled`, then your instance is configured as `standard` \.

**To describe the credit option for CPU usage using the AWS CLI**
+ Use the [describe\-instance\-credit\-specifications](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-credit-specifications.html) command\. If you do not specify one or more instance IDs, all T2 or T3 instances with the credit option of `unlimited` are returned\.  
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

### Modifying the Credit Option for CPU Usage of a T2 Instance<a name="modify-t2"></a>

You can switch the credit option for CPU usage of a running or stopped T2 instance at any time between `unlimited` and `standard`\.

**To modify the credit option for CPU usage of a T2 instance using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances** and select the T2 instance\. To modify the credit option for several T2 instances at one time, select all applicable instances\.

1. Choose **Actions**, **Instance Settings**, **Change T2/T3 Unlimited**\.
**Note**  
The **Change T2/T3 Unlimited** option is enabled only if you select a T2 or T3 instance\.

1. To change the credit option to `unlimited`, choose **Enable**\. To change the credit option to `standard`, choose **Disable**\. The current credit option for the T2 instance appears in parentheses after the instance ID\.

**To modify the credit option for CPU usage of a T2 instance using the AWS CLI**
+ Use the [modify\-instance\-credit\-specification](http://docs.aws.amazon.com/cli/latest/reference/ec2/modify-instance-credit-specification.html) command\. Specify the instance and its credit option using the `--instance-credit-specification` parameter\. Valid credit options are `standard` and `unlimited`\.

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
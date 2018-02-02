# T2 Unlimited<a name="t2-unlimited"></a>

A T2 Unlimited instance can sustain high CPU performance for any period of time whenever required\. The hourly T2 instance price automatically covers all interim spikes in usage if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a flat additional rate per vCPU\-hour\. For information about instance pricing, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and the T2 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**Important**  
If you use a `t2.micro` instance under the [AWS Free Tier](https://aws.amazon.com/free/) offer and configure it as Unlimited, charges may apply if your average utilization over a rolling 24\-hour period exceeds the baseline of the instance\.


+ [T2 Unlimited Concepts](#t2-unlimited-concepts)
+ [Example: Explaining Credit Use with T2 Unlimited](#t2_unlimited_example)
+ [Launching a T2 Instance as Unlimited](#launch-t2)
+ [Viewing the Credit Option for CPU Usage of a T2 Instance](#describe-t2)
+ [Modifying the Credit Option for CPU Usage of a T2 Instance](#modify-t2)
+ [Using an Auto Scaling Group to Launch a T2 Unlimited Instance](#t2-auto-scaling-grp)

## T2 Unlimited Concepts<a name="t2-unlimited-concepts"></a>

T2 Unlimited is a configuration option for T2 instances that can be set at launch, or enabled at any time for a running or stopped T2 instance\.

T2 Unlimited instances can burst above the baseline for as long as required\. This enables you to enjoy the low T2 instance hourly price for a wide variety of general\-purpose applications, and ensures that your instances are never held to the baseline performance\. ​The basic T2 hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of a T2 Unlimited instance over a rolling 24\-hour period is at or below the baseline\. For a vast majority of general\-purpose workloads, T2 Unlimited instances provide ample performance without any additional charges\. If the average CPU utilization exceeds the baseline over a 24\-hour period, there is a flat additional rate per vCPU\-hour\. For more information, see the T2 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**How T2 Unlimited Works**  
When a T2 Unlimited instance has exhausted its CPU credit balance, it can consume *surplus* credits to burst beyond the baseline\. Surplus credits are tracked by the CloudWatch metric `CPUSurplusCreditBalance`\. When an instance's CPU utilization falls below the baseline, the credits that it earns are used to pay down \(or reduce\) the surplus credits it consumed earlier\. The ability to earn and pay down credits enables Amazon EC2 to average the CPU utilization of an instance over a 24\-hour period\.

**When is a T2 Unlimited Instance Charged?**  
If the average CPU utilization of the instance is at or below the baseline, the instance incurs no additional charges\. However, if CPU utilization stays above the baseline and the instance does not pay down the surplus credits it has consumed, the surplus credits are charged at a flat additional rate per vCPU\-hour\. For more information, see the T2 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

Because an instance earns a maximum number of credits in a 24\-hour period \(for example, a `t2.micro` can earn a maximum of 144 credits\), it can consume surplus credits up to that maximum without being charged immediately\. Surplus credits that are consumed above the maximum are charged at the end of the hour\. When the instance is terminated or stopped, any remaining surplus credits that were consumed earlier are charged\.

Surplus credits that are charged at any point can be tracked by the CloudWatch metric `CPUSurplusCreditsCharged`\. For more information, see [Additional CloudWatch Metrics for T2 Instances](t2-instances-monitoring-cpu-credits.md#t2-cw-metrics)\.

**No Launch Credits for T2 Unlimited**  
T2 Unlimited instances do not receive launch credits\. A T2 Unlimited instance can burst beyond the baseline at any time with no additional charge as long as its average CPU utilization is at or below the baseline over a rolling 24\-hour window or its lifetime, whichever is shorter\. As such, T2 Unlimited instances do not require launch credits to achieve high performance immediately after launch\.

**Tracking Credit Usage Above the Baseline**  
To see if your T2 Unlimited instance is using more credits than the baseline provides, you can use CloudWatch metrics to track and set up hourly alarms to be notified of credit usage\. For more information, see [Monitoring Your CPU Credits](t2-instances-monitoring-cpu-credits.md)\.

**Enabling T2 Unlimited**  
T2 Standard is the default configuration; if you do not enable T2 Unlimited, your T2 instance launches as Standard\. You can switch from Standard to Unlimited, and from Unlimited to Standard at any time on a running or stopped instance\. For more information, see [Launching a T2 Instance as Unlimited](#launch-t2) and [Modifying the Credit Option for CPU Usage of a T2 Instance](#modify-t2)\. 

The `CPUCreditBalance` of a T2 instance remains unchanged and is carried over when switching from Standard to Unlimited, or from Unlimited to Standard\. However, the `CPUSurplusCreditBalance` is immediately charged when a T2 instance switches from Unlimited to Standard\. For more information, see [Monitoring Your CPU Credits](t2-instances-monitoring-cpu-credits.md)\.

You can view if your T2 instance is configured as Standard or Unlimited using the Amazon EC2 console or the AWS CLI\. For more information, see [Viewing the Credit Option for CPU Usage of a T2 Instance](#describe-t2)\.

## Example: Explaining Credit Use with T2 Unlimited<a name="t2_unlimited_example"></a>

In this example, you see the CPU utilization of a `t2.nano` instance launched as Unlimited, and how it uses *earned* and *surplus* credits to sustain CPU performance\.

A `t2.nano` instance earns 72 CPU credits over a rolling 24\-hour period, which it uses to redeem 72 minutes of vCPU use\. When it depletes its CPU credit balance \(represented by the CloudWatch metric `CPUCreditBalance`\), it can use *surplus* CPU credits—that it has *not yet earned*—to burst for as long as it needs\. Because a `t2.nano` earns a maximum of 72 credits in a 24\-hour period, it can consume surplus credits up to that maximum without being charged immediately\. If it uses more than 72 CPU credits, it is charged for the difference at the end of the hour\.

The intent of the example, illustrated by the following graph, is to show how an instance can burst using surplus credits even after it exhausts its `CPUCreditBalance`\. You can assume that the instance has earned the maximum credits at the start of the time line in the graph\. The following workflow references the numbered points on the graph: 

**1** – In the first 10 minutes, `CPUCreditUsage` is at 0, and the `CPUCreditBalance` remains at its maximum of 72\.

**2** – At 23:40, as CPU utilization increases, the instance uses CPU credits and the `CPUCreditBalance` decreases\.

**3** – At around 00:47, the instance depletes its entire `CPUCreditBalance`, and starts to use surplus credits to sustain high CPU performance\.

**4** – Surplus credits are used until 01:55, when the `CPUSurplusCreditBalance` reaches 72 CPU credits\. This is the maximum a `t2.nano` can earn in a 24\-hour period\. Any surplus credits used thereafter cannot be offset by earned credits within the 24\-hour period, which results in a small additional charge at the end of the hour\.

**5** – The instance continues to use surplus credits until around 02:20\. As CPU utilization falls below the baseline, the instance starts to earn credits at 3 credits per hour \(or 0\.25 credits every 5 minutes\)\. After the `CPUSurplusCreditBalance` reduces to 0, the instance starts to earn `CPUCreditBalance` at 0\.25 credits every 5 minutes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2_unlimited_graph.png)

**Calculating the Bill**  
Surplus credits cost $0\.05 per vCPU\-hour\. The instance consumed approximately 25 surplus credits between 01:55 and 02:20, which is equivalent to 0\.42 vCPU\-hours\.

Additional charges for this instance are 0\.42 vCPU\-hours x $0\.05/vCPU\-hour = $0\.021, rounded to $0\.02\.

Here is the month\-end bill for this T2 Unlimited instance:

![\[Example bill for a T2 Unlimited instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2_unlimited_bill_linux.png)

You can set billing alerts to be notified every hour of any accruing charges, and take action if required\.

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

1. In the left navigation pane, choose **Instances** and select the T2 instance\. 

1. Choose **Actions**, **Instance Settings**, **Change T2 Unlimited**, and then choose **Yes, Change T2 Unlimited**\.
**Note**  
The **Change T2 Unlimited** option is enabled only if you select a T2 instance\.

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
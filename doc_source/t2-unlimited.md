# Unlimited Mode<a name="t2-unlimited"></a>

A burstable performance instance configured as `unlimited`, such as T2 Unlimited or T3 Unlimited, can sustain high CPU performance for any period of time whenever required\. The hourly T2 or T3 instance price automatically covers all interim spikes in usage if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a flat additional rate per vCPU\-hour\. For information about instance pricing, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and the T2 and T3 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**Important**  
If you use a `t2.micro` instance under the [AWS Free Tier](https://aws.amazon.com/free/) offer and configure it as `unlimited`, charges may apply if your average utilization over a rolling 24\-hour period exceeds the baseline of the instance\.

**Topics**
+ [Unlimited Mode Concepts](#t2-unlimited-concepts)
+ [Examples: Unlimited Mode](unlimited-mode-examples.md)

## Unlimited Mode Concepts<a name="t2-unlimited-concepts"></a>

The `unlimited` mode is a credit configuration option for burstable performance instances\. It can be enabled or disabled at any time for a running or stopped instance\. 
+ T3 instances are launched as `unlimited` by default\.
+ T2 instances are launched as `standard` by default\.

A burstable performance instance configured as `unlimited` can burst above the baseline for as long as required\. This enables you to enjoy the low instance hourly price for a wide variety of general\-purpose applications, and ensures that your instances are never held to the baseline performance\. ​

The basic hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of an instance configured as `unlimited` over a rolling 24\-hour period is at or below the baseline\. For a vast majority of general\-purpose workloads, T2 Unlimited and T3 Unlimited instances provide ample performance without any additional charges\. If the average CPU utilization exceeds the baseline over a 24\-hour period, there is a flat additional rate per vCPU\-hour\. 

For more information, see the T2 and T3 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**How Unlimited Burstable Performance Instances Work**  
If a burstable performance instance configured as `unlimited` depletes its CPU credit balance, it can spend *surplus* credits to burst beyond the baseline\. When its CPU utilization falls below the baseline, it uses the CPU credits that it earns to pay down the surplus credits it spent earlier\. The ability to earn CPU credits to pay down surplus credits enables Amazon EC2 to average the CPU utilization of an instance over a 24\-hour period\. 

**Surplus Credits Can Incur Charges**  
If the average CPU utilization of an instance is at or below the baseline, the instance incurs no additional charges\. Because an instance earns a [maximum number of credits](t2-credits-baseline-concepts.md#t-instance-credit-table) in a 24\-hour period \(for example, a `t3.micro` can earn a maximum of 288 credits in a 24\-hour period\), it can spend surplus credits up to that maximum without being charged\.

However, if CPU utilization stays above the baseline, the instance cannot earn enough credits to pay down the surplus credits it has spent\. The surplus credits that are not paid down are charged at a flat additional rate per vCPU\-hour\. For more information, see the T2 and T3 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

Surplus credits that were spent earlier are charged when any of the following occurs:
+ The spent surplus credits exceed the [maximum number of credits](t2-credits-baseline-concepts.md#t-instance-credit-table) the instance can earn in a 24\-hour period\. Spent surplus credits above the maximum are charged at the end of the hour\.
+ The instance is stopped or terminated\.
+ The instance is switched from `unlimited` to `standard`\.

Spent surplus credits are tracked by the CloudWatch metric `CPUSurplusCreditBalance`\. Surplus credits that are charged are tracked by the CloudWatch metric `CPUSurplusCreditsCharged`\. For more information, see [Additional CloudWatch Metrics for T2 and T3 Instances](t2-instances-monitoring-cpu-credits.md#t2-cw-metrics)\.

**No Launch Credits for T2 Unlimited**  
T2 Standard instances receive [launch credits](t2-std.md#launch-credits), but T2 Unlimited instances do not receive launch credits\. A T2 Unlimited instance can burst beyond the baseline at any time with no additional charge as long as its average CPU utilization is at or below the baseline over a rolling 24\-hour window or its lifetime, whichever is shorter\. As such, T2 Unlimited do not require launch credits to achieve high performance immediately after launch\.

If a T2 instance is switched from `standard` to `unlimited`, any accrued launch credits are removed from the `CPUCreditBalance` before the remaining `CPUCreditBalance` is carried over\.

**Note**  
T3 Standard and T3 Unlimited instances never receive launch credits\.

**Enabling Unlimited Mode**  
T3 instances launch as `unlimited` by default\. T2 instances launch as `standard` by default unless you enable `unlimited` at launch\.

You can switch from `unlimited` to `standard`, and from `standard` to `unlimited`, at any time on a running or stopped instance\. For more information, see [Launching a T2 Instance as Standard or Unlimited](t-how-to.md#launch-t2) and [Modifying the Credit Option for CPU Usage of a T2 Instance](t-how-to.md#modify-t2)\. 

You can view if your T2 or T3 instance is configured as `unlimited` or `standard` using the Amazon EC2 console or the AWS CLI\. For more information, see [Viewing the Credit Option for CPU Usage of a T2 Instance](t-how-to.md#describe-t2)\.

**What Happens to Credits when Enabling or Disabling T2 Unlimited or T3 Unlimited**  
`CPUCreditBalance` is a CloudWatch metric that tracks the number of credits a T2 or T3 instance has accrued\. `CPUSurplusCreditBalance` is a CloudWatch metric that tracks the number of surplus credits a T2 Unlimited or T3 Unlimited instance has spent\.

When a T2 Unlimited or T3 Unlimited instance is switched to `standard`, the following occurs:
+ The `CPUCreditBalance` remains unchanged and is carried over\. 
+ The `CPUSurplusCreditBalance` is immediately charged\.

When a T2 Standard or T3 Standard instance is switched to `unlimited`, the following occurs:
+ The `CPUCreditBalance` containing accrued earned credits is carried over\.
+ For T2 Standard instances, any launch credits are removed from the `CPUCreditBalance`, and the remaining `CPUCreditBalance` containing accrued earned credits is carried over\.

**Monitoring Credit Usage**  
To see if your T2 Unlimited or T3 Unlimited instance is spending more credits than the baseline provides, you can use CloudWatch metrics to track and set up hourly alarms to be notified of credit usage\. For more information, see [Monitoring Your CPU Credits](t2-instances-monitoring-cpu-credits.md)\.
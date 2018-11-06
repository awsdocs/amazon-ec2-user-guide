# Unlimited Mode for Burstable Performance Instances<a name="burstable-performance-instances-unlimited-mode"></a>

A burstable performance instance configured as `unlimited` can sustain high CPU performance for any period of time whenever required\. The hourly instance price automatically covers all interim spikes in usage if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a flat additional rate per vCPU\-hour\. For information about instance pricing, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and the section for Unlimited pricing in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**Important**  
If you use a `t2.micro` instance under the [AWS Free Tier](https://aws.amazon.com/free/) offer and configure it as `unlimited`, charges may apply if your average utilization over a rolling 24\-hour period exceeds the baseline of the instance\.

**Topics**
+ [Unlimited Mode Concepts](#burstable-performance-instances-unlimited-mode-concepts)
+ [Examples: Unlimited Mode](unlimited-mode-examples.md)

## Unlimited Mode Concepts<a name="burstable-performance-instances-unlimited-mode-concepts"></a>

The `unlimited` mode is a credit configuration option for burstable performance instances\. It can be enabled or disabled at any time for a running or stopped instance\.
+ T3 instances are launched as `unlimited` by default\.
+ T2 instances are launched as `standard` by default\.

A burstable performance instance configured as `unlimited` can burst above the baseline for as long as required\. This enables you to enjoy the low instance hourly price for a wide variety of general\-purpose applications, and ensures that your instances are never held to the baseline performance\.

The basic hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of an instance configured as `unlimited` over a rolling 24\-hour period is at or below the baseline\. For the vast majority of general\-purpose workloads, instances configured as `unlimited` provide ample performance without any additional charges\. If the average CPU utilization exceeds the baseline over a 24\-hour period, there is a flat additional rate per vCPU\-hour\.

**How Unlimited Burstable Performance Instances Work**  
If a burstable performance instance configured as `unlimited` depletes its CPU credit balance, it can spend *surplus* credits to burst beyond the baseline\. When its CPU utilization falls below the baseline, it uses the CPU credits that it earns to pay down the surplus credits that it spent earlier\. The ability to earn CPU credits to pay down surplus credits enables Amazon EC2 to average the CPU utilization of an instance over a 24\-hour period\. 

**Surplus Credits Can Incur Charges**  
If the average CPU utilization of an instance is at or below the baseline, the instance incurs no additional charges\. Because an instance earns a [maximum number of credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) in a 24\-hour period \(for example, a `t3.micro` instance can earn a maximum of 288 credits in a 24\-hour period\), it can spend surplus credits up to that maximum without being charged\.

However, if CPU utilization stays above the baseline, the instance cannot earn enough credits to pay down the surplus credits that it has spent\. The surplus credits that are not paid down are charged at a flat additional rate per vCPU\-hour\.

Surplus credits that were spent earlier are charged when any of the following occurs:
+ The spent surplus credits exceed the [maximum number of credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) the instance can earn in a 24\-hour period\. Spent surplus credits above the maximum are charged at the end of the hour\.
+ The instance is stopped or terminated\.
+ The instance is switched from `unlimited` to `standard`\.

Spent surplus credits are tracked by the CloudWatch metric `CPUSurplusCreditBalance`\. Surplus credits that are charged are tracked by the CloudWatch metric `CPUSurplusCreditsCharged`\. For more information, see [Additional CloudWatch Metrics for Burstable Performance Instances](burstable-performance-instances-monitoring-cpu-credits.md#burstable-performance-instances-cw-metrics)\.

**No Launch Credits for T2 Unlimited**  
T2 Standard instances receive [launch credits](burstable-performance-instances-standard-mode.md#launch-credits), but T2 Unlimited instances do not\. A T2 Unlimited instance can burst beyond the baseline at any time with no additional charge, as long as its average CPU utilization is at or below the baseline over a rolling 24\-hour window or its lifetime, whichever is shorter\. As such, T2 Unlimited instances do not require launch credits to achieve high performance immediately after launch\.

If a T2 instance is switched from `standard` to `unlimited`, any accrued launch credits are removed from the `CPUCreditBalance` before the remaining `CPUCreditBalance` is carried over\.

**Note**  
T3 instances never receive launch credits\.

**Enabling Unlimited Mode**  
T3 instances launch as `unlimited` by default\. T2 instances launch as `standard` by default, but you can enable `unlimited` at launch\.

You can switch from `unlimited` to `standard`, and from `standard` to `unlimited`, at any time on a running or stopped instance\. For more information, see [Launching a Burstable Performance Instance as Unlimited or Standard](burstable-performance-instances-how-to.md#launch-burstable-performance-instances) and [Modifying the Credit Specification of a Burstable Performance Instance](burstable-performance-instances-how-to.md#modify-burstable-performance-instances)\. 

You can check whether your burstable performance instance is configured as `unlimited` or `standard` using the Amazon EC2 console or the AWS CLI\. For more information, see [Viewing the Credit Specification of a Burstable Performance Instance](burstable-performance-instances-how-to.md#describe-burstable-performance-instances)\.

**What Happens to Credits when Switching between Unlimited and Standard**  
`CPUCreditBalance` is a CloudWatch metric that tracks the number of credits accrued by an instance\. `CPUSurplusCreditBalance` is a CloudWatch metric that tracks the number of surplus credits spent by an instance\.

When you change an instance configured as `unlimited` to `standard`, the following occurs:
+ The `CPUCreditBalance` value remains unchanged and is carried over\. 
+ The `CPUSurplusCreditBalance` value is immediately charged\.

When a `standard` instance is switched to `unlimited`, the following occurs:
+ The `CPUCreditBalance` value containing accrued earned credits is carried over\.
+ For T2 Standard instances, any launch credits are removed from the `CPUCreditBalance` value, and the remaining `CPUCreditBalance` value containing accrued earned credits is carried over\.

**Monitoring Credit Usage**  
To see if your instance is spending more credits than the baseline provides, you can use CloudWatch metrics to track and set up hourly alarms to be notified of credit usage\. For more information, see [Monitoring Your CPU Credits](burstable-performance-instances-monitoring-cpu-credits.md)\.
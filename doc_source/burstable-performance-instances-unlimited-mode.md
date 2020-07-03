# Unlimited mode for burstable performance instances<a name="burstable-performance-instances-unlimited-mode"></a>

A burstable performance instance configured as `unlimited` can sustain high CPU performance for any period of time whenever required\. The hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\.

For the vast majority of general\-purpose workloads, instances configured as `unlimited` provide ample performance without any additional charges\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a [flat additional rate](https://aws.amazon.com/ec2/pricing/on-demand/#T2/T3_Unlimited_Mode_Pricing) per vCPU\-hour\. For information about instance pricing, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and the section for Unlimited pricing in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/#T2/T3_Unlimited_Mode_Pricing)\.

**Important**  
If you use a `t2.micro` instance under the [AWS Free Tier](https://aws.amazon.com/free/) offer and configure it as `unlimited`, charges may apply if your average utilization over a rolling 24\-hour period exceeds the [baseline](burstable-credits-baseline-concepts.md#baseline_performance) of the instance\.  
T3 instances launch as `unlimited` by default\. If you launch T3 Spot Instances as `unlimited` and plan to use them immediately and for a short duration, with no idle time for accruing CPU credits, you will incur charges for surplus credits\. If the average CPU usage over a 24\-hour period exceeds the baseline, you will also incur charges for surplus credits\. We recommend that you launch your T3 Spot Instances in [standard](burstable-performance-instances-standard-mode.md) mode to avoid paying higher costs\. For more information, see [Surplus credits can incur charges](burstable-performance-instances-unlimited-mode-concepts.md#unlimited-mode-surplus-credits) and [T3 Spot Instances](using-spot-limits.md#t3-spot-instances)\.

**Contents**
+ [Unlimited mode concepts](burstable-performance-instances-unlimited-mode-concepts.md)
  + [How Unlimited burstable performance instances work](burstable-performance-instances-unlimited-mode-concepts.md#how-burstable-performance-instances-unlimited-works)
  + [When to use unlimited mode versus fixed CPU](burstable-performance-instances-unlimited-mode-concepts.md#when-to-use-unlimited-mode)
  + [Surplus credits can incur charges](burstable-performance-instances-unlimited-mode-concepts.md#unlimited-mode-surplus-credits)
  + [No launch credits for T2 Unlimited instances](burstable-performance-instances-unlimited-mode-concepts.md#unlimited-mode-no-launch-credits)
  + [Enabling unlimited mode](burstable-performance-instances-unlimited-mode-concepts.md#unlimited-mode-enabling)
  + [What happens to credits when switching between Unlimited and Standard](burstable-performance-instances-unlimited-mode-concepts.md#unlimited-mode-switching-and-credits)
  + [Monitoring credit usage](burstable-performance-instances-unlimited-mode-concepts.md#unlimited-mode-monitoring-credit-usage)
+ [Unlimited mode examples](unlimited-mode-examples.md)
  + [Example 1: Explaining credit use with T3 Unlimited](unlimited-mode-examples.md#t3_unlimited_example)
  + [Example 2: Explaining credit use with T2 Unlimited](unlimited-mode-examples.md#t2_unlimited_example)
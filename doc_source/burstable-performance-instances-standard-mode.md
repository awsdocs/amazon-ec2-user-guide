# Standard mode for burstable performance instances<a name="burstable-performance-instances-standard-mode"></a>

A burstable performance instance configured as `standard` is suited to workloads with an average CPU utilization that is consistently below the baseline performance of the instance\. To burst above the baseline, the instance spends credits that it has accrued in its CPU credit balance\. If the instance is running low on accrued credits, performance is gradually lowered to the baseline performance level, so that the instance does not experience a sharp performance drop\-off when its accrued CPU credit balance is depleted\. For more information, see [CPU credits and baseline performance for burstable performance instances](burstable-credits-baseline-concepts.md)\.

**Contents**
+ [Standard mode concepts](burstable-performance-instances-standard-mode-concepts.md)
  + [How standard burstable performance instances work](burstable-performance-instances-standard-mode-concepts.md#how-burstable-performance-instances-standard-works)
  + [Launch credits](burstable-performance-instances-standard-mode-concepts.md#launch-credits)
  + [Launch credit limits](burstable-performance-instances-standard-mode-concepts.md#launch-credit-limits)
  + [Differences between launch credits and earned credits](burstable-performance-instances-standard-mode-concepts.md#burstable-performance-instances-diff-launch-earned-credits)
+ [Standard mode examples](standard-mode-examples.md)
  + [Example 1: Explaining credit use with T3 Standard](standard-mode-examples.md#t3_standard_example)
  + [Example 2: Explaining credit use with T2 Standard](standard-mode-examples.md#t2-standard-example)
    + [Period 1: 1 – 24 hours](standard-mode-examples.md#period-1)
    + [Period 2: 25 – 36 hours](standard-mode-examples.md#period-2)
    + [Period 3: 37 – 61 hours](standard-mode-examples.md#period-3)
    + [Period 4: 62 – 72 hours](standard-mode-examples.md#period-4)
    + [Period 5: 73 – 75 hours](standard-mode-examples.md#period-5)
    + [Period 6: 76 – 90 hours](standard-mode-examples.md#period-6)
    + [Period 7: 91 – 96 hours](standard-mode-examples.md#period-7)
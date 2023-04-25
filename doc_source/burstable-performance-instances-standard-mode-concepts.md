# Standard mode concepts<a name="burstable-performance-instances-standard-mode-concepts"></a>

The `standard` mode is a configuration option for burstable performance instances\. It can be enabled or disabled at any time for a running or stopped instance\. You can [set `standard` as the default credit option](burstable-performance-instances-how-to.md#burstable-performance-instance-set-default-credit-specification-for-account) at the account level per AWS Region, per burstable performance instance family, so that all new burstable performance instances in the account launch using the default credit option\.

## How standard burstable performance instances work<a name="how-burstable-performance-instances-standard-works"></a>

When a burstable performance instance configured as `standard` is in a running state, it continuously earns \(at a millisecond\-level resolution\) a set rate of earned credits per hour\. For T2 Standard, when the instance is stopped, it loses all its accrued credits, and its credit balance is reset to zero\. When it is restarted, it receives a new set of launch credits, and begins to accrue earned credits\. For T4g, T3a and T3 Standard instances, the CPU credit balance persists for seven days after the instance stops and the credits are lost thereafter\. If you start the instance within seven days, no credits are lost\.

T2 Standard instances receive two types of [CPU credits](burstable-credits-baseline-concepts.md#key-concepts): *earned credits* and *launch credits*\. When a T2 Standard instance is in a running state, it continuously earns \(at a millisecond\-level resolution\) a set rate of earned credits per hour\. At start, it has not yet earned credits for a good startup experience; therefore, to provide a good startup experience, it receives launch credits at start, which it spends first while it accrues earned credits\.

T4g, T3a and T3 instances do not receive launch credits because they support Unlimited mode\. The Unlimited mode credit configuration enables T4g, T3a and T3 instances to use as much CPU as needed to burst beyond baseline and for as long as needed\.

## Launch credits<a name="launch-credits"></a>

T2 Standard instances get 30 launch credits per vCPU at launch or start, and T1 Standard instances get 15 launch credits\. For example, a `t2.micro` instance has one vCPU and gets 30 launch credits, while a `t2.xlarge` instance has four vCPUs and gets 120 launch credits\. Launch credits are designed to provide a good startup experience to allow instances to burst immediately after launch before they have accrued earned credits\.

Launch credits are spent first, before earned credits\. Unspent launch credits are accrued in the CPU credit balance, but do not count towards the CPU credit balance limit\. For example, a `t2.micro` instance has a CPU credit balance limit of 144 earned credits\. If it is launched and remains idle for 24 hours, its CPU credit balance reaches 174 \(30 launch credits \+ 144 earned credits\), which is over the limit\. However, after the instance spends the 30 launch credits, the credit balance cannot exceed 144\. For more information about the CPU credit balance limit for each instance size, see the [credit table](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table)\.

The following table lists the initial CPU credit allocation received at launch or start, and the number of vCPUs\.


|  Instance type  |  Launch credits  |  vCPUs  | 
| --- | --- | --- | 
| t1\.micro |  15  |  1  | 
| t2\.nano |  30  |  1  | 
| t2\.micro |  30  |  1  | 
| t2\.small |  30  |  1  | 
| t2\.medium |  60  |  2  | 
| t2\.large |  60  |  2  | 
| t2\.xlarge |  120  |  4  | 
| t2\.2xlarge |  240  |  8  | 

## Launch credit limits<a name="launch-credit-limits"></a>

There is a limit to the number of times T2 Standard instances can receive launch credits\. The default limit is 100 launches or starts of all T2 Standard instances combined per account, per Region, per rolling 24\-hour period\. For example, the limit is reached when one instance is stopped and started 100 times within a 24\-hour period, or when 100 instances are launched within a 24\-hour period, or other combinations that equate to 100 starts\. New accounts may have a lower limit, which increases over time based on your usage\.

**Tip**  
To ensure that your workloads always get the performance they need, switch to [Unlimited mode for burstable performance instances](burstable-performance-instances-unlimited-mode.md) or consider using a larger instance size\.

## Differences between launch credits and earned credits<a name="burstable-performance-instances-diff-launch-earned-credits"></a>

The following table lists the differences between launch credits and earned credits\.


|    |  Launch credits  |  Earned credits  | 
| --- | --- | --- | 
|  **Credit earn rate**  |  T2 Standard instances get 30 launch credits per vCPU at launch or start\. If a T2 instance is switched from `unlimited` to `standard`, it does not get launch credits at the time of switching\.  |  Each T2 instance continuously earns \(at a millisecond\-level resolution\) a set rate of CPU credits per hour, depending on the instance size\. For more information about the number of CPU credits earned per instance size, see the [credit table](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table)\.  | 
|  **Credit earn limit**  |  The limit for receiving launch credits is 100 launches or starts of all T2 Standard instances combined per account, per Region, per rolling 24\-hour period\. New accounts may have a lower limit, which increases over time based on your usage\.  |  A T2 instance cannot accrue more credits than the CPU credit balance limit\. If the CPU credit balance has reached its limit, any credits that are earned after the limit is reached are discarded\. Launch credits do not count towards the limit\. For more information about the CPU credit balance limit for each T2 instance size, see the [credit table](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table)\.  | 
|  **Credit use**  |  Launch credits are spent first, before earned credits\.  |  Earned credits are spent only after all launch credits are spent\.  | 
|  **Credit expiration**  |  When a T2 Standard instance is running, launch credits do not expire\. When a T2 Standard instance stops or is switched to T2 Unlimited, all launch credits are lost\.  |  When a T2 instance is running, earned credits that have accrued do not expire\. When the T2 instance stops, all accrued earned credits are lost\.  | 

The number of accrued launch credits and accrued earned credits is tracked by the CloudWatch metric `CPUCreditBalance`\. For more information, see `CPUCreditBalance` in the [CloudWatch metrics table](burstable-performance-instances-monitoring-cpu-credits.md#burstable-performance-instances-CW-metrics-table)\.
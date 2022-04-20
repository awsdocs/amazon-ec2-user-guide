# Key concepts and definitions for burstable performance instances<a name="burstable-credits-baseline-concepts"></a>

Traditional Amazon EC2 instance types provide fixed CPU resources, while burstable performance instances provide a baseline level of CPU utilization with the ability to burst CPU utilization above the baseline level\. This ensures that you pay only for baseline CPU plus any additional burst CPU usage resulting in lower compute costs\. The baseline utilization and ability to burst are governed by CPU credits\. Burstable performance instances are the only instance types that use credits for CPU usage\.

Each burstable performance instance continuously earns credits when it stays below the CPU baseline, and continuously spends credits when it bursts above the baseline\. The amount of credits earned or spent depends on the CPU utilization of the instance:
+ If the CPU utilization is below baseline, then credits earned are greater than credits spent\.
+ If the CPU utilization is equal to baseline, then credits earned are equal to credits spent\.
+ If the CPU utilization is higher than baseline, then credits spent are higher than credits earned\.

When the credits earned are greater than credits spent, then the difference is called accrued credits, which can be used later to burst above baseline CPU utilization\. Similarly, when the credits spent are more than credits earned, then the instance behavior depends on the credit configuration mode—Standard mode or Unlimited mode\. 

In Standard mode, when credits spent are more than credits earned, the instance uses the accrued credits to burst above baseline CPU utilization\. If there are no accrued credits remaining, then the instance gradually comes down to baseline CPU utilization and cannot burst above baseline until it accrues more credits\. 

In Unlimited mode, if the instance bursts above baseline CPU utilization, then the instance first uses the accrued credits to burst\. If there are no accrued credits remaining, then the instance spends surplus credits to burst\. When its CPU utilization falls below the baseline, it uses the CPU credits that it earns to pay down the surplus credits that it spent earlier\. The ability to earn CPU credits to pay down surplus credits enables Amazon EC2 to average the CPU utilization of an instance over a 24\-hour period\. If the average CPU usage over a 24\-hour period exceeds the baseline, the instance is billed for the additional usage at a flat additional rate per vCPU\-hour\.

**Contents**
+ [Key concepts and definitions](#key-concepts)
+ [Earn CPU credits](#earning-CPU-credits)
+ [CPU credit earn rate](#CPU-credit-earn-rate)
+ [CPU credit accrual limit](#CPU-credit-accrual-limit)
+ [Accrued CPU credits life span](#accrued-CPU-credits-life-span)
+ [Baseline utilization](#baseline_performance)

## Key concepts and definitions<a name="key-concepts"></a>

The following key concepts and definitions are applicable to burstable performance instances\.

**CPU utilization**  
CPU utilization is the percentage of allocated EC2 compute units that are currently in use on the instance\. This metric measures the percentage of allocated CPU cycles that are being utilized on an instance\. The CPU Utilization CloudWatch metric shows CPU usage per instance and not CPU usage per core\. The baseline CPU specification of an instance is also based on the CPU usage per instance\. To measure CPU utilization using the AWS Management Console or the AWS CLI, see [Get statistics for a specific instance](US_SingleMetricPerInstance.md)\.

**CPU credit**  
A unit of vCPU\-time\.  
Examples:  
1 CPU credit = 1 vCPU \* 100% utilization \* 1 minute\.  
1 CPU credit = 1 vCPU \* 50% utilization \* 2 minutes  
1 CPU credit = 2 vCPU \* 25% utilization \* 2 minutes

**Baseline utilization**  
The baseline utilization is the level at which the CPU can be utilized for a net credit balance of zero, when the number CPU credits being earned matches the number of CPU credits being used\. Baseline utilization is also known as the baseline\. Baseline utilization is expressed as a percentage of vCPU utilization, which is calculated as follows: Baseline utilization % = \(number of credits earned/number of vCPUs\)/60 minutes

**Earned credits**  
Credits earned continuously by an instance when it is running\.  
Number of credits earned per hour = % baseline utilization \* number of vCPUs \* 60 minutes  
Example:  
A t3\.nano with 2 vCPUs and a baseline utilization of 5% earns 6 credits per hour, calculated as follows:  
2 vCPUs \* 5% baseline \* 60 minutes = 6 credits per hour

**Spent or used credits**  
Credits used continuously by an instance when it is running\.  
CPU credits spent per minute = Number of vCPUs \* CPU utilization \* 1 minute

**Accrued credits**  
Unspent CPU credits when an instance uses fewer credits than is required for baseline utilization\. In other words, accrued credits = \(Earned credits – Used credits\) below baseline\.  
Example:  
If a t3\.nano is running at 2% CPU utilization, which is below its baseline of 5% for an hour, the accrued credits is calculated as follows:  
Accrued CPU credits = \(Earned credits per hour – Used credits per hour\) = 6 – 2 vCPUs \* 2% CPU utilization \* 60 minutes = 6 – 2\.4 = 3\.6 accrued credits per hour

**Credit accrual limit**  
Depends on the instance size but in general is equal to the number of maximum credits earned in 24 hours\.  
Example:  
For t3\.nano, the credit accrual limit = 24 \* 6 = 144 credits

**Launch credits**  
Only applicable for T2 instances configured for Standard mode\. Launch credits are a limited number of CPU credits that are allocated to a new T2 instance so that, when launched in Standard mode, it can burst above the baseline\.

**Surplus credits**  
Credits that are spent by an instance after it depletes its accrued credit balance\. The surplus credits are designed for burstable instances to sustain high performance for an extended period of time, and are only used in Unlimited mode\. The surplus credits balance is used to determine how many credits were used by the instance for bursting in Unlimited mode\.

**Standard mode**  
Credit configuration mode, which allows an instance to burst above the baseline by spending credits it has accrued in its credit balance\.

**Unlimited mode**  
Credit configuration mode, which allows an instance to burst above the baseline by sustaining high CPU utilization for any period of time whenever required\. The hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a flat additional rate per vCPU\-hour\.

The following table summarizes the key credit differences between the burstable instance types\.


****  

| Type | Type of CPU credits supported | Credit configuration modes | Accrued CPU credits lifespan between instance starts and stops | 
| --- | --- | --- | --- | 
| Latest generation | 
| T4g |  Earned credits, Accrued credits, Spent credits, Surplus credits \(Unlimited mode only\)  |  Standard, Unlimited \(default\)  |  7 days \(credits persist for 7 days after an instance stops\)  | 
| T3a |  Earned credits, Accrued credits, Spent credits, Surplus credits \(Unlimited mode only\)  |  Standard, Unlimited \(default\)  |  7 days \(credits persist for 7 days after an instance stops\)  | 
| T3 |  Earned credits, Accrued credits, Spent credits, Surplus credits \(Unlimited mode only\)  |  Standard, Unlimited \(default\)  |  7 days \(credits persist for 7 days after an instance stops\)  | 
| Previous generation | 
| T2 |  Earned credits, Accrued credits, Spent credits, Launch credits \(Standard mode only\), Surplus credits \(Unlimited mode only\)  |  Standard \(default\), Unlimited  |  0 days \(credits are lost when an instance stops\)  | 

**Note**  
Unlimited mode is not supported for T3 instances that are launched on a Dedicated Host\.

## Earn CPU credits<a name="earning-CPU-credits"></a>

Each burstable performance instance continuously earns \(at a millisecond\-level resolution\) a set rate of CPU credits per hour, depending on the instance size\. The accounting process for whether credits are accrued or spent also happens at a millisecond\-level resolution, so you don't have to worry about overspending CPU credits; a short burst of CPU uses a small fraction of a CPU credit\.

If a burstable performance instance uses fewer CPU resources than is required for baseline utilization \(such as when it is idle\), the unspent CPU credits are accrued in the CPU credit balance\. If a burstable performance instance needs to burst above the baseline utilization level, it spends the accrued credits\. The more credits that a burstable performance instance has accrued, the more time it can burst beyond its baseline when more CPU utilization is needed\.

The following table lists the burstable performance instance types, the rate at which CPU credits are earned per hour, the maximum number of earned CPU credits that an instance can accrue, the number of vCPUs per instance, and the baseline utilization as a percentage of a full core \(using a single vCPU\)\.


|  Instance type  |  CPU credits earned per hour  |  Maximum earned credits that can be accrued\*  |  vCPUs\*\*\*  |  Baseline utilization per vCPU  | 
| --- | --- | --- | --- | --- | 
|  **T2**  |    |    |    |    | 
| t2\.nano |  3  |  72  |  1  |  5%  | 
| t2\.micro |  6  |  144  |  1  |  10%  | 
| t2\.small |  12  |  288  |  1  |  20%  | 
| t2\.medium |  24  |  576  |  2  |  20%\*\*  | 
| t2\.large |  36  |  864  |  2  |  30%\*\*  | 
| t2\.xlarge |  54  |  1296  |  4  |  22\.5%\*\*  | 
| t2\.2xlarge |  81\.6  |  1958\.4  |  8  |  17%\*\*  | 
|  **T3**  |    |    |    |    | 
| t3\.nano |  6  |  144  |  2  |  5%\*\*  | 
| t3\.micro |  12  |  288  |  2  |  10%\*\*  | 
| t3\.small |  24  |  576  |  2  |  20%\*\*  | 
| t3\.medium |  24  |  576  |  2  |  20%\*\*  | 
| t3\.large |  36  |  864  |  2  |  30%\*\*  | 
| t3\.xlarge |  96  |  2304  |  4  |  40%\*\*  | 
| t3\.2xlarge |  192  |  4608  |  8  |  40%\*\*  | 
|  **T3a**  |    |    |    |    | 
| t3a\.nano |  6  |  144  |  2  |  5%\*\*  | 
| t3a\.micro |  12  |  288  |  2  |  10%\*\*  | 
| t3a\.small |  24  |  576  |  2  |  20%\*\*  | 
| t3a\.medium |  24  |  576  |  2  |  20%\*\*  | 
| t3a\.large |  36  |  864  |  2  |  30%\*\*  | 
| t3a\.xlarge |  96  |  2304  |  4  |  40%\*\*  | 
| t3a\.2xlarge |  192  |  4608  |  8  |  40%\*\*  | 
| **T4g** |  |  |  |  | 
| t4g\.nano | 6 | 144 | 2 | 5%\*\* | 
| t4g\.micro | 12 | 288 | 2 | 10%\*\* | 
| t4g\.small | 24 | 576 | 2 | 20%\*\* | 
| t4g\.medium | 24 | 576 | 2 | 20%\*\* | 
| t4g\.large | 36 | 864 | 2 | 30%\*\* | 
| t4g\.xlarge | 96 | 2304 | 4 | 40%\*\* | 
| t4g\.2xlarge | 192 | 4608 | 8 | 40%\*\* | 


|  | 
| --- |
|  \* The number of credits that can be accrued is equivalent to the number of credits that can be earned in a 24\-hour period\.  | 
|  \*\* The percentage baseline utilization in the table is per vCPU\. In CloudWatch, CPU utilization is shown per vCPU\. For example, the CPU utilization for a `t3.large` instance operating at the baseline level is shown as 30% in CloudWatch CPU metrics\. For information about how to calculate the baseline utilization, see [Baseline utilization](#baseline_performance)\.  | 
|  \*\*\* Each vCPU is a thread of either an Intel Xeon core or an AMD EPYC core, except for T2 and T4g instances\.  | 

## CPU credit earn rate<a name="CPU-credit-earn-rate"></a>

The number of CPU credits earned per hour is determined by the instance size\. For example, a `t3.nano` earns six credits per hour, while a `t3.small` earns 24 credits per hour\. The preceding table lists the credit earn rate for all instances\.

## CPU credit accrual limit<a name="CPU-credit-accrual-limit"></a>

While earned credits never expire on a running instance, there is a limit to the number of earned credits that an instance can accrue\. The limit is determined by the CPU credit balance limit\. After the limit is reached, any new credits that are earned are discarded, as indicated by the following image\. The full bucket indicates the CPU credit balance limit, and the spillover indicates the newly earned credits that exceed the limit\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-t3-bucket.png)

The CPU credit balance limit differs for each instance size\. For example, a `t3.micro` instance can accrue a maximum of 288 earned CPU credits in the CPU credit balance\. The preceding table lists the maximum number of earned credits that each instance can accrue\.

T2 Standard instances also earn launch credits\. Launch credits do not count towards the CPU credit balance limit\. If a T2 instance has not spent its launch credits, and remains idle over a 24\-hour period while accruing earned credits, its CPU credit balance appears as over the limit\. For more information, see [Launch credits](burstable-performance-instances-standard-mode-concepts.md#launch-credits)\. 

T4g, T3a and T3 instances do not earn launch credits\. These instances launch as `unlimited` by default, and therefore can burst immediately upon start without any launch credits\. T3 instances launched on a Dedicated Host launch as `standard` by default; `de>unlimited` mode is not supported for T3 instances on a Dedicated Host\.

## Accrued CPU credits life span<a name="accrued-CPU-credits-life-span"></a>

CPU credits on a running instance do not expire\.

For T2, the CPU credit balance does not persist between instance stops and starts\. If you stop a T2 instance, the instance loses all its accrued credits\.

For T4g, T3a and T3, the CPU credit balance persists for seven days after an instance stops and the credits are lost thereafter\. If you start the instance within seven days, no credits are lost\.

For more information, see `CPUCreditBalance` in the [CloudWatch metrics table](burstable-performance-instances-monitoring-cpu-credits.md#burstable-performance-instances-CW-metrics-table)\.

## Baseline utilization<a name="baseline_performance"></a>

The *baseline utilization* is the level at which the CPU can be utilized for a net credit balance of zero, when the number of CPU credits being earned matches the number of CPU credits being used\. Baseline utilization is also known as *the baseline*\.

Baseline utilization is expressed as a percentage of vCPU utilization, which is calculated as follows:

`(number of credits earned/number of vCPUs)/60 minutes = % baseline utilization`

For example, a `t3.nano` instance, with 2 vCPUs, earns 6 credits per hour, resulting in a baseline utilization of 5% , which is calculated as follows:

`(6 credits earned/2 vCPUs)/60 minutes = 5% baseline utilization`

A `t3.xlarge` instance, with 4 vCPUs, earns 96 credits per hour, resulting in a baseline utilization of 40% \(`(96/4)/60`\)\.

The following graph provides an example of a t3\.large with an average CPU utilization below the baseline\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/baseline-utilization.png)
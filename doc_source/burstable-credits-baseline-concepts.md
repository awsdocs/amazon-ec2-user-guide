# CPU credits and baseline performance for burstable performance instances<a name="burstable-credits-baseline-concepts"></a>

Traditional Amazon EC2 instance types provide fixed performance, while burstable performance instances provide a baseline level of CPU performance with the ability to burst above that baseline level\. The baseline performance and ability to burst are governed by CPU credits\.

A CPU credit provides the performance of a full CPU core running at 100% utilization for one minute\. Other combinations of number of vCPUs, utilization, and time can also equate to one CPU credit\. For example, one CPU credit is equal to one vCPU running at 50% utilization for two minutes, or two vCPUs running at 25% utilization for two minutes\.

**Contents**
+ [Earning CPU credits](#earning-CPU-credits)
+ [CPU credit earn rate](#CPU-credit-earn-rate)
+ [CPU credit accrual limit](#CPU-credit-accrual-limit)
+ [Accrued CPU credits life span](#accrued-CPU-credits-life-span)
+ [Baseline performance](#baseline_performance)

## Earning CPU credits<a name="earning-CPU-credits"></a>

Each burstable performance instance continuously earns \(at a millisecond\-level resolution\) a set rate of CPU credits per hour, depending on the instance size\. The accounting process for whether credits are accrued or spent also happens at a millisecond\-level resolution, so you don't have to worry about overspending CPU credits; a short burst of CPU uses a small fraction of a CPU credit\.

If a burstable performance instance uses fewer CPU resources than is required for baseline performance \(such as when it is idle\), the unspent CPU credits are accrued in the CPU credit balance\. If a burstable performance instance needs to burst above the baseline performance level, it spends the accrued credits\. The more credits that a burstable performance instance has accrued, the more time it can burst beyond its baseline when more performance is needed\.

The following table lists the burstable performance instance types, the rate at which CPU credits are earned per hour, the maximum number of earned CPU credits that an instance can accrue, the number of vCPUs per instance, and the baseline performance level as a percentage of a full core performance \(using a single vCPU\)\.


|  Instance type  |  CPU credits earned per hour  |  Maximum earned credits that can be accrued\*  |  vCPUs  |  Baseline performance per vCPU  | 
| --- | --- | --- | --- | --- | 
|  **T2**  |   |   |   |   | 
|  `t2.nano`  |  3  |  72  |  1  |  5%  | 
|  `t2.micro`  |  6  |  144  |  1  |  10%  | 
|  `t2.small`  |  12  |  288  |  1  |  20%  | 
|  `t2.medium`  |  24  |  576  |  2  |  20%\*\*  | 
|  `t2.large`  |  36  |  864  |  2  |  30%\*\*  | 
|  `t2.xlarge`  |  54  |  1296  |  4  |  22\.5%\*\*  | 
|  `t2.2xlarge`  |  81\.6  |  1958\.4  |  8  |  17%\*\*  | 
|  **T3**  |   |   |   |   | 
|  `t3.nano`  |  6  |  144  |  2  |  5%\*\*  | 
|  `t3.micro`  |  12  |  288  |  2  |  10%\*\*  | 
|  `t3.small`  |  24  |  576  |  2  |  20%\*\*  | 
|  `t3.medium`  |  24  |  576  |  2  |  20%\*\*  | 
|  `t3.large`  |  36  |  864  |  2  |  30%\*\*  | 
|  `t3.xlarge`  |  96  |  2304  |  4  |  40%\*\*  | 
|  `t3.2xlarge`  |  192  |  4608  |  8  |  40%\*\*  | 
|  **T3a**  |   |   |   |   | 
|  `t3a.nano`  |  6  |  144  |  2  |  5%\*\*  | 
|  `t3a.micro`  |  12  |  288  |  2  |  10%\*\*  | 
|  `t3a.small`  |  24  |  576  |  2  |  20%\*\*  | 
|  `t3a.medium`  |  24  |  576  |  2  |  20%\*\*  | 
|  `t3a.large`  |  36  |  864  |  2  |  30%\*\*  | 
|  `t3a.xlarge`  |  96  |  2304  |  4  |  40%\*\*  | 
|  `t3a.2xlarge`  |  192  |  4608  |  8  |  40%\*\*  | 


|  | 
| --- |
|  \* The number of credits that can be accrued is equivalent to the number of credits that can be earned in a 24\-hour period\.  | 
|  \*\* The baseline performance in the table is per vCPU\. For instance sizes that have more than one vCPU, to calculate the baseline CPU utilization for the instance, multiply the vCPU percentage by the number of vCPUs\. For example, a `t3.large` instance has two vCPUs, which provide a baseline CPU utilization for the instance of 60% \(2 vCPUs x 30% baseline performance of one vCPU\)\. In CloudWatch, CPU utilization is shown per vCPU\. Therefore, the CPU utilization for a `t3.large` instance operating at the baseline performance is shown as 30% in CloudWatch CPU metrics\.  | 

## CPU credit earn rate<a name="CPU-credit-earn-rate"></a>

The number of CPU credits earned per hour is determined by the instance size\. For example, a `t3.nano` earns six credits per hour, while a `t3.small` earns 24 credits per hour\. The preceding table lists the credit earn rate for all instances\.

## CPU credit accrual limit<a name="CPU-credit-accrual-limit"></a>

While earned credits never expire on a running instance, there is a limit to the number of earned credits that an instance can accrue\. The limit is determined by the CPU credit balance limit\. After the limit is reached, any new credits that are earned are discarded, as indicated by the following image\. The full bucket indicates the CPU credit balance limit, and the spillover indicates the newly earned credits that exceed the limit\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-t3-bucket.png)

The CPU credit balance limit differs for each instance size\. For example, a `t3.micro` instance can accrue a maximum of 288 earned CPU credits in the CPU credit balance\. The preceding table lists the maximum number of earned credits that each instance can accrue\.

**Note**  
T2 Standard instances also earn launch credits\. Launch credits do not count towards the CPU credit balance limit\. If a T2 instance has not spent its launch credits, and remains idle over a 24\-hour period while accruing earned credits, its CPU credit balance appears as over the limit\. For more information, see [Launch credits](burstable-performance-instances-standard-mode-concepts.md#launch-credits)\.   
T3 and T3a instances do not earn launch credits\. These instances launch as `unlimited` by default, and therefore can burst immediately upon start without any launch credits\.

## Accrued CPU credits life span<a name="accrued-CPU-credits-life-span"></a>

CPU credits on a running instance do not expire\.

For T3 and T3a, the CPU credit balance persists for seven days after an instance stops and the credits are lost thereafter\. If you start the instance within seven days, no credits are lost\.

For T2, the CPU credit balance does not persist between instance stops and starts\. If you stop a T2 instance, the instance loses all its accrued credits\.

For more information, see `CPUCreditBalance` in the [CloudWatch metrics table](burstable-performance-instances-monitoring-cpu-credits.md#burstable-performance-instances-CW-metrics-table)\.

## Baseline performance<a name="baseline_performance"></a>

The number of credits that an instance earns per hour can be expressed as a percentage of CPU utilization\. It is known as the *baseline performance*, and sometimes just as *the baseline*\. For example, a `t3.nano` instance, with two vCPUs, earns six credits per hour, resulting in a baseline performance of 5% \(3/60 minutes\) per vCPU\. A `t3.xlarge` instance, with four vCPUs, earns 96 credits per hour, resulting in a baseline performance of 40% \(24/60 minutes\) per vCPU\.
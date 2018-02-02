# CPU Credits and Baseline Performance<a name="t2-credits-baseline-concepts"></a>

Traditional Amazon EC2 instance types provide fixed performance, while T2 instances provide a baseline level of CPU performance with the ability to burst above that baseline level\. The baseline performance and ability to burst are governed by CPU credits\. A CPU credit provides the performance of a full CPU core for one minute\.


+ [CPU Credits](#cpu-credits)
+ [Baseline Performance](#baseline_performance)

## CPU Credits<a name="cpu-credits"></a>

One CPU credit is equal to one vCPU running at 100% utilization for one minute\. Other combinations of number of vCPUs, utilization, and time can also equate to one CPU credit\. For example, one CPU credit is equal to one vCPU running at 50% utilization for two minutes, or two vCPUs running at 25% utilization for two minutes\.

**Earning CPU Credits**  
Each T2 instance continuously receives \(at a millisecond\-level resolution\) a set rate of CPU credits per hour, depending on the instance size\. The accounting process for whether credits are accumulated or spent also happens at a millisecond\-level resolution, so you don't have to worry about overspending CPU credits; a short burst of CPU takes a small fraction of a CPU credit\.

When a T2 instance uses fewer CPU resources than its baseline performance level allows \(such as when it is idle\), the unused CPU credits \(or the difference between what was earned and what was spent\) are stored in the credit balance, building CPU credits for bursting\. When your T2 instance requires more CPU resources than its baseline performance level allows, it uses credits from the CPU credit balance\. The more credits your T2 instance has accrued, the more time it can burst beyond its baseline performance level when more performance is needed\. 

The following table lists the rate at which CPU credits are received, the maximum earned CPU credit balance that an instance can accrue, the number of vCPUs, and the baseline performance level as a percentage of a full core performance \(using a single vCPU\)\.


|  Instance type  |  CPU credits earned per hour  |  Maximum earned CPU credit balance  |  vCPUs  |  Baseline performance \(% CPU utilization\)  | 
| --- | --- | --- | --- | --- | 
|  `t2.nano`  |  3  |  72  |  1  |  5%  | 
|  `t2.micro`  |  6  |  144  |  1  |  10%  | 
|  `t2.small`  |  12  |  288  |  1  |  20%  | 
|  `t2.medium`  |  24  |  576  |  2  |  40% \(of 200% max\)\*  | 
|  `t2.large`  |  36  |  864  | 2 |  60% \(of 200% max\)\*  | 
|  `t2.xlarge`  |  54  |  1296  | 4 |  90% \(of 400% max\)\*  | 
|  `t2.2xlarge`  |  81  |  1944  | 8 |  135% \(of 800% max\)\*  | 


|  | 
| --- |
|  \* `t2.medium` and larger instances have more than one vCPU\. The baseline performance in the table is a percentage of using a single vCPU \(you could split performance over multiple vCPUs\)\. To calculate the baseline CPU utilization for the instance, divide the combined vCPU percentages by the number of vCPUs\. For example, the baseline performance for a `t2.large` is 60% of one vCPU\. A `t2.large` instance has two vCPUs, therefore the CPU utilization for a `t2.large` instance operating at the baseline performance is shown as 30% in CloudWatch CPU metrics\.  | 

**CPU Credit Earn Rate**  
The number of CPU credits earned per hour is determined by the instance size\. For example, a `t2.nano` earns three credits per hour, while a `t2.small` earns 12 credits per hour\. The preceding table lists the credit earn rate for all T2 instances\.

While CPU credits never expire, an instance cannot accumulate more than the maximum number of credits that can be accrued\. Any credits that are earned after the maximum is reached are discarded, indicated by the image below: the full bucket indicates the maximum number of credits, and the spillover indicates newly earned credits that exceed the maximum\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-bucket.png)

The maximum number of credits differs for each T2 instance size\. For example, a `t2.micro` instance earns six CPU credits per hour, and can accumulate a maximum earned CPU credit balance of 144 CPU credits\. The preceding table lists the maximum number of credits that each T2 instance can accrue\.

**Accrued CPU Credits Are Lost When Instance Is Stopped**  
CPU credits on a running instance do not expire\. However, the CPU credit balance does not persist between instance stops and starts; if you stop an instance, the instance loses all its accrued credits\. For more information, see `CPUCreditBalance` in the CloudWatch metrics table\.

## Baseline Performance<a name="baseline_performance"></a>

Each T2 instance earns a specified number of credits per hour and can accrue a maximum number of credits, determined by the instance size\.

The number of credits earned per hour can be expressed as a percentage of CPU utilization, and is known as the *baseline performance*, and sometimes just as *the baseline*\. For example, a `t2.nano` earns three credits per hour resulting in a baseline performance of 5% \(3/60 minutes\)\. A `t2.large`, with two vCPUs, earns 36 credits per hour, resulting in a baseline performance of 30% \(18/60 minutes\) per vCPU\.
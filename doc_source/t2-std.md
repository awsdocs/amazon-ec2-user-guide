# T2 Standard<a name="t2-std"></a>

A T2 Standard instance is suited to workloads with an average CPU utilization that is consistently below the baseline performance of the instance\. The instance uses credits that it has accrued in its CPU credit balance to burst above the baseline\. If the instance is running low on accrued credits, performance is gradually lowered to the baseline performance level over a 15\-minute period, so that the instance does not experience a sharp performance drop\-off when its accrued CPU credit balance is depleted\. For more information, see [CPU Credits and Baseline Performance](t2-credits-baseline-concepts.md)\.

**Tip**  
To ensure that your workloads always get the performance they need, switch to T2 Unlimited or consider using a larger T2 instance size\. For more information, see [T2 Unlimited](t2-unlimited.md)\.

**How T2 Standard Works**  
A T2 Standard instance receives two types of CPU credits: *earned credits* and *launch credits*\. When a T2 Standard instance starts, it continuously receives \(at a millisecond\-level resolution\) a set rate of earned credits per hour\. At start, it has not yet earned credits for a good startup experience; therefore, to provide a good startup experience, it receives launch credits at start, which it uses first while it accumulates earned credits\.

When a T2 Standard instance is stopped, it loses all its accrued credits, and its credit balance is reset to zero\. When it is restarted, it receives a new set of launch credits, and begins to accumulate earned credits\. There are limits to the number of times T2 Standard instances can receive launch credits\. If an account has exceeded its limits, no more launch credits are allocated\.

**Launch Credits**  
T2 Standard instances get 30 launch credits per vCPU at launch or start\. For example, a `t2.micro` has one vCPU and gets 30 launch credits, while a `t2.xlarge` has four vCPUs and gets 120 launch credits\. Launch credits are designed to provide a good startup experience to allow instances to burst immediately after launch before they have accumulated a CPU credit balance\.

**Note**  
There is a limit on the number of times T2 Standard instances can launch or start with launch credits\. The default limit is 100 launches or starts of all T2 Standard instances combined per account, per 24\-hour period, per region\. If the limit is exceeded, launch credits are not allocated\. New accounts may have a lower limit, which increases over time based on your usage\.

Launch credits are used first and do not count towards the maximum number of earned CPU credits that can be accrued\. For example, a `t2.micro` instance that is launched and then remains idle for over 24 hours can reach a credit balance of up to 174 \(30 launch credits \+ 144 earned credits\)\. However, after the instance uses the 30 launch credits, the credit balance can never exceed 144\. For more information about the maximum earned CPU credit balance for each T2 instance size, see the T2 credit table\.

The following table lists the initial CPU credit allocation received at launch or start, and the number of vCPUs\.


|  Instance type  |  Launch credits  |  vCPUs  | 
| --- | --- | --- | 
|  `t2.nano`  |  30  |  1  | 
|  `t2.micro`  |  30  |  1  | 
|  `t2.small`  |  30  |  1  | 
|  `t2.medium`  |  60  |  2  | 
|  `t2.large`  |  60  |  2  | 
|  `t2.xlarge`  |  120  |  4  | 
|  `t2.2xlarge`  |  240  |  8  | 

**Differences Between Launch Credits and Earned Credits**  
The following table lists the differences between launch credits and earned credits\.


|   |  Launch credits  |  Earned credits  | 
| --- | --- | --- | 
|  **Credit earn rate**  | T2 Standard instances get 30 launch credits per vCPU at launch or start\. |  Each T2 instance continuously receives \(at a millisecond\-level resolution\) a set rate of CPU credits per hour, depending on the instance size\. For more information about the number of CPU credits earned per instance size, see the T2 credit table\.  | 
|  **Credit earn limit**  | The limit for receiving launch credits is 100 launches or starts of any T2 instance per account, per 24\-hour period, per region\. New accounts may have a lower limit, which increases over time based on your usage\. |  A T2 instance cannot accumulate more than the maximum number of credits that can be accrued\. Any credits that are earned after the maximum is reached are discarded\. Launch credits do not count towards the maximum\. For more information about the maximum earned CPU credit balance for each T2 instance size, see the T2 credit table\.  | 
|  **Credit use**  | Launch credits are used first, before earned credits\. |  Earned credits are used only after launch credits are used up\.  | 
|  **Credit expiration**  |  When an instance is running, launch credits do not expire\. When the instance stops, all launch credits are lost\.  |  When an instance is running, earned credits do not expire\. When the instance stops, all earned credits are lost\.  | 

The total number of launch credits and earned credits is tracked by the CloudWatch metric `CPUCreditBalance`\. For more information, see `CPUCreditBalance` in the CloudWatch metrics table\.
# T2 Standard<a name="t2-std"></a>

A T2 Standard instance is suited to workloads with an average CPU utilization that is consistently below the baseline performance of the instance\. To burst above the baseline, the instance spends credits that it has accrued in its CPU credit balance\. If the instance is running low on accrued credits, performance is gradually lowered to the baseline performance level, so that the instance does not experience a sharp performance drop\-off when its accrued CPU credit balance is depleted\. For more information, see [CPU Credits and Baseline Performance](t2-credits-baseline-concepts.md)\.

**Tip**  
To ensure that your workloads always get the performance they need, switch to [T2 Unlimited](t2-unlimited.md) or consider using a larger T2 instance size\.

**Topics**
+ [T2 Standard Concepts](#t2-standard-concepts)
+ [Example: Explaining Credit Use with T2 Standard](#t2-standard-example)

## T2 Standard Concepts<a name="t2-standard-concepts"></a>

### How T2 Standard Works<a name="how-t2-standard-works"></a>

A T2 Standard instance receives two types of CPU credits: *earned credits* and *launch credits*\. When a T2 Standard instance is in a running state, it continuously earns \(at a millisecond\-level resolution\) a set rate of earned credits per hour\. At start, it has not yet earned credits for a good startup experience; therefore, to provide a good startup experience, it receives launch credits at start, which it spends first while it accrues earned credits\.

When a T2 Standard instance is stopped, it loses all its accrued credits, and its credit balance is reset to zero\. When it is restarted, it receives a new set of launch credits, and begins to accrue earned credits\.

### Launch Credits<a name="launch-credits"></a>

T2 Standard instances get 30 launch credits per vCPU at launch or start\. For example, a `t2.micro` has one vCPU and gets 30 launch credits, while a `t2.xlarge` has four vCPUs and gets 120 launch credits\. Launch credits are designed to provide a good startup experience to allow instances to burst immediately after launch before they have accrued earned credits\.

Launch credits are spent first, before earned credits\. Unspent launch credits are accrued in the CPU credit balance, but do not count towards the CPU credit balance limit\. For example, a `t2.micro` instance has a CPU credit balance limit of 144 earned credits\. If it is launched and remains idle for 24 hours, its CPU credit balance reaches 174 \(30 launch credits \+ 144 earned credits\), which is over the limit\. However, once the instance spends the 30 launch credits, the credit balance cannot exceed 144\. For more information about the CPU credit balance limit for each T2 instance size, see the [T2 credit table](t2-credits-baseline-concepts.md#t2-credit-table)\.

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

### Launch Credit Limits<a name="launch-credit-limits"></a>

There is a limit to the number of times T2 Standard instances can receive launch credits\. The default limit is 100 launches or starts of all T2 Standard instances combined per account, per region, per rolling 24\-hour period\. For example, the limit is reached when one instance is stopped and started 100 times within a 24\-hour period, or when 100 instances are launched within a 24\-hour period, or other combinations that equate to 100 starts\. New accounts may have a lower limit, which increases over time based on your usage\.

### Differences Between Launch Credits and Earned Credits<a name="t2-diff-launch-earned-credits"></a>

The following table lists the differences between launch credits and earned credits\.


|   |  Launch credits  |  Earned credits  | 
| --- | --- | --- | 
|  **Credit earn rate**  |  T2 Standard instances get 30 launch credits per vCPU at launch or start\. If a T2 instance is switched from Unlimited to Standard, it does not get launch credits at the time of switching\.  |  Each T2 instance continuously earns \(at a millisecond\-level resolution\) a set rate of CPU credits per hour, depending on the instance size\. For more information about the number of CPU credits earned per instance size, see the [T2 credit table](t2-credits-baseline-concepts.md#t2-credit-table)\.  | 
|  **Credit earn limit**  |  The limit for receiving launch credits is 100 launches or starts of all T2 Standard instances combined per account, per region, per rolling 24\-hour period\. New accounts may have a lower limit, which increases over time based on your usage\.  |  A T2 instance cannot accrue more credits than the CPU credit balance limit\. If the CPU credit balance has reached its limit, any credits that are earned after the limit is reached are discarded\. Launch credits do not count towards the limit\. For more information about the CPU credit balance limit for each T2 instance size, see the [T2 credit table](t2-credits-baseline-concepts.md#t2-credit-table)\.  | 
|  **Credit use**  |  Launch credits are spent first, before earned credits\.  |  Earned credits are spent only after all launch credits are spent\.  | 
|  **Credit expiration**  |  When a T2 Standard instance is running, launch credits do not expire\. When a T2 Standard instance stops or is switched to T2 Unlimited, all launch credits are lost\.  |  When an instance is running, earned credits that have accrued do not expire\. When the instance stops, all accrued earned credits are lost\.  | 

The number of accrued launch credits and accrued earned credits is tracked by the CloudWatch metric `CPUCreditBalance`\. For more information, see `CPUCreditBalance` in the [CloudWatch metrics table](t2-instances-monitoring-cpu-credits.md#t2-CW-metrics-table)\.

## Example: Explaining Credit Use with T2 Standard<a name="t2-standard-example"></a>

In this example, you see how a `t2.nano` instance launched as `standard` earns, accrues, and spends *launch* and *earned* credits\. You see how the credit balance reflects not only accrued *earned* credits, but also accrued *launch* credits\.

A `t2.nano` instance gets 30 launch credits when it is launched, and earns 72 credits every 24 hours\. Its credit balance limit is 72 earned credits; launch credits do not count towards the limit\. After the limit is reached, new credits that are earned are discarded\. For more information about the number of credits that can be earned and accrued, see the [T2 credit table](t2-credits-baseline-concepts.md#t2-credit-table)\. For more information about launch credit limits, see [Launch Credit Limits](#launch-credit-limits)\.

You might launch a T2 Standard instance and use it immediately, or you might launch a T2 Standard instance and leave it idle for a few days before running applications on it\. Whether an instance is used or remains idle determines if credits are spent or accrued\. If an instance remains idle for 24 hours from the time it is launched, the credit balance appears to exceed its limit because the balance reflects both accrued earned credits and accrued launch credits\. However, after CPU is used, the launch credits are spent first\. Thereafter, the limit always reflects the maximum number of earned credits that can be accrued\. 

This example describes an instance that remains idle for 24 hours from the time it is launched, and walks you through seven periods of time over a 96\-hour period, showing the rate at which credits are earned, accrued, spent, and discarded, and the value of the credit balance at the end of each period\.

### Period 1: 1 – 24 hours<a name="period-1"></a>

At 0 hours on the graph, the T2 instance is launched as `standard` and immediately gets 30 launch credits\. It earns credits while in the running state\. The instance remains idle from the time it is launched—CPU utilization is 0%—and no credits are spent\. All unspent credits are accrued in the credit balance\. At approximately 14 hours after launch, the credit balance is 72 \(30 launch credits \+ 42 earned credits\), which is equivalent to what the instance can earn in 24 hours\. At 24 hours after launch, the credit balance exceeds 72 credits because the unspent launch credits are accrued in the credit balance—the credit balance is 102 credits: 30 launch credits \+ 72 earned credits\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph1.png)


|  |  | 
| --- |--- |
| Credit Spend Rate | 0 credits per 24 hours \(0% CPU utilization\) | 
| Credit Earn Rate | 72 credits per 24 hours | 
| Credit Discard Rate | 0 credits per 24 hours | 
| Credit Balance |  102 credits \(30 launch credits \+ 72 earned credits\)  | 

**Conclusion**  
If there is no CPU utilization after launch, the instance accrues more credits than what it can earn in 24 hours \(30 launch credits \+ 72 earned credits = 102 credits\)\.

In a real\-world scenario, an EC2 instance consumes a small number of credits while launching and running, which prevents the balance from reaching the maximum theoretical value in this example\.

### Period 2: 25 – 36 hours<a name="period-2"></a>

For the next 12 hours, the instance continues to remain idle and earn credits, but the credit balance does not increase\. It plateaus at 102 credits \(30 launch credits \+ 72 earned credits\)\. The credit balance has reached its limit of 72 accrued earned credits, so newly earned credits are discarded\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph2.png)


****  

|  |  | 
| --- |--- |
| Credit Spend Rate | 0 credits per 24 hours \(0% CPU utilization\) | 
| Credit Earn Rate | 72 credits per 24 hours \(3 credits per hour\) | 
| Credit Discard Rate | 72 credits per 24 hours \(100% of credit earn rate\) | 
| Credit Balance |  102 credits \(30 launch credits \+ 72 earned credits\)—balance is unchanged  | 

**Conclusion**  
An instance constantly earns credits, but it cannot accrue more earned credits if the credit balance has reached its limit\. After the limit is reached, newly earned credits are discarded\. Launch credits do not count towards the credit balance limit\. If the balance includes accrued launch credits, the balance appears to be over the limit\.

### Period 3: 37 – 61 hours<a name="period-3"></a>

For the next 25 hours, the instance uses 2% CPU, which requires 30 credits\. In the same period, it earns 75 credits, but the credit balance decreases\. The balance decreases because the accrued *launch* credits are spent first, while newly earned credits are discarded because the credit balance is already at its limit of 72 earned credits\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph3.png)


****  

|  |  | 
| --- |--- |
| Credit Spend Rate | 28\.8 credits per 24 hours \(1\.2 credits per hour, 2% CPU utilization, 40% of credit earn rate\)—30 credits over 25 hours | 
| Credit Earn Rate | 72 credits per 24 hours | 
| Credit Discard Rate | 72 credits per 24 hours \(100% of credit earn rate\) | 
| Credit Balance |  72 credits \(30 launch credits were spent; 72 earned credits remain unspent\)  | 

**Conclusion**  
An instance spends launch credits first, before spending earned credits\. Launch credits do not count towards the credit limit\. After the launch credits are spent, the balance can never go higher than what can be earned in 24 hours\. Furthermore, while an instance is running, it cannot get more launch credits\.

### Period 4: 62 – 72 hours<a name="period-4"></a>

For the next 11 hours, the instance uses 2% CPU, which requires 13\.2 credits\. This is the same CPU utilization as in the previous period, but the balance does not decrease\. It stays at 72 credits\.

The balance does not decrease because the credit earn rate is higher than the credit spend rate\. In the time the instance spends 13\.2 credits, it also earns 33 credits\. However, the balance limit is 72 credits, so any earned credits that exceed the limit are discarded\. The balance plateaus at 72 credits, which is different from the plateau of 102 credits during Period 2, because there are no accrued launch credits\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph4.png)


****  

|  |  | 
| --- |--- |
| Credit Spend Rate | 28\.8 credits per 24 hours \(1\.2 credits per hour, 2% CPU utilization, 40% of credit earn rate\)—13\.2 credits over 11 hours | 
| Credit Earn Rate | 72 credits per 24 hours | 
| Credit Discard Rate | 43\.2 credits per 24 hours \(60% of credit earn rate\) | 
| Credit Balance |  72 credits \(0 launch credits, 72 earned credits\)—balance is at its limit  | 

**Conclusion**  
After launch credits are spent, the credit balance limit is determined by the number of credits an instance can earn in 24 hours\. If the instance earns more credits than it spends, newly earned credits over the limit are discarded\.

### Period 5: 73 – 75 hours<a name="period-5"></a>

For the next three hours, the instance bursts at 20% CPU utilization, which requires 36 credits\. The instance earns nine credits in the same three hours, which results in a net balance decrease of 27 credits\. At the end of three hours, the credit balance is 45 accrued earned credits\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph5.png)


****  

|  |  | 
| --- |--- |
| Credit Spend Rate | 288 credits per 24 hours \(12 credits per hour, 20% CPU utilization, 400% of credit earn rate\)—36 credits over 3 hours | 
| Credit Earn Rate | 72 credits per 24 hours \(9 credits over 3 hours\) | 
| Credit Discard Rate | 0 credits per 24 hours | 
| Credit Balance |  45 credits \(previous balance \(72\) \- spent credits \(36\) \+ earned credits \(9\)\)—balance decreases at a rate of 216 credits per 24 hours \(spend rate 288/24 \+ earn rate 72/24 = balance decrease rate 216/24\)  | 

**Conclusion**  
If an instance spends more credits than it earns, its credit balance decreases\.

### Period 6: 76 – 90 hours<a name="period-6"></a>

For the next 15 hours, the instance uses 2% CPU, which requires 18 credits\. This is the same CPU utilization as in Periods 3 and 4\. However, the balance increases in this period, whereas it decreased in Period 3 and plateaued in Period 4\.

In Period 3, the accrued launch credits were spent, and any earned credits that exceeded the credit limit were discarded, resulting in a decrease in the credit balance\. In Period 4, the instance spent fewer credits than it earned, and any earned credits that exceeded the limit were discarded, so the balance plateaued at its maximum of 72 credits\.

In this period, there are no accrued launch credits, and the number of accrued earned credits in the balance is below the limit\. No earned credits are discarded\. Furthermore, the instance earns more credits than it spends, resulting in an increase in the credit balance\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph6.png)


****  

|  |  | 
| --- |--- |
| Credit Spend Rate | 28\.8 credits per 24 hours \(1\.2 credits per hour, 2% CPU utilization, 40% of credit earn rate\)—18 credits over 15 hours | 
| Credit Earn Rate | 72 credits per 24 hours \(45 credits over 15 hours\) | 
| Credit Discard Rate | 0 credits per 24 hours | 
| Credit Balance |  72 credits \(balance increases at a rate of 43\.2 credits per 24 hours—change rate = spend rate 28\.8/24 \+ earn rate 72/24\)  | 

**Conclusion**  
If an instance spends fewer credits than it earns, its credit balance increases\.

### Period 7: 91 – 96 hours<a name="period-7"></a>

For the next six hours, the instance remains idle—CPU utilization is 0%—and no credits are spent\. This is the same CPU utilization as in Period 2, but the balance does not plateau at 102 credits—it plateaus at 72 credits, which is the credit balance limit for the instance\.

In Period 2, the credit balance included 30 accrued launch credits\. The launch credits were spent in Period 3\. A running instance cannot get more launch credits\. After its credit balance limit is reached, any earned credits that exceed the limit are discarded\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2-graph7.png)


****  

|  |  | 
| --- |--- |
| Credit Spend Rate | 0 credits per 24 hours \(0% CPU utilization\) | 
| Credit Earn Rate | 72 credits per 24 hours | 
| Credit Discard Rate | 72 credits per 24 hours \(100% of credit earn rate\) | 
| Credit Balance |  72 credits \(0 launch credits, 72 earned credits\)  | 

**Conclusion**  
An instance constantly earns credits, but cannot accrue more earned credits if the credit balance limit has been reached\. After the limit is reached, newly earned credits are discarded\. The credit balance limit is determined by the number of credits an instance can earn in 24 hours\. For more information about credit balance limits, see the [T2 credit table](t2-credits-baseline-concepts.md#t2-credit-table)\. 
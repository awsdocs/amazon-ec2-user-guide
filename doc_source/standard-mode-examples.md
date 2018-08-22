# Examples: Standard Mode<a name="standard-mode-examples"></a>

The following examples explain credit use when T2 and T3 instances are configured as `standard`\.

**Topics**
+ [Example 1: Explaining Credit Use with T3 Standard](#t3_standard_example)
+ [Example 2: Explaining Credit Use with T2 Standard](#t2-standard-example)

## Example 1: Explaining Credit Use with T3 Standard<a name="t3_standard_example"></a>

In this example, you see how a `t3.nano` instance launched as `standard` earns, accrues, and spends *earned* credits\. You see how the credit balance reflects the accrued *earned* credits\.

**Note**  
T3 instances configured as `standard` do not receive launch credits\.

A running `t3.nano` instance earns 144 credits every 24 hours\. Its credit balance limit is 144 earned credits\. After the limit is reached, new credits that are earned are discarded\. For more information about the number of credits that can be earned and accrued, see the [T2 and T3 credit table](t2-credits-baseline-concepts.md#t-instance-credit-table)\.

You might launch a T2 Standard instance and use it immediately, or you might launch a T2 Standard instance and leave it idle for a few days before running applications on it\. Whether an instance is used or remains idle determines if credits are spent or accrued\. If an instance remains idle for 24 hours from the time it is launched, the credit balance reaches it limit, which is the maximum number of earned credits that can be accrued\. 

This example describes an instance that remains idle for 24 hours from the time it is launched, and walks you through seven periods of time over a 96\-hour period, showing the rate at which credits are earned, accrued, spent, and discarded, and the value of the credit balance at the end of each period\.

The following workflow references the numbered points on the graph:

**P1** – At 0 hours on the graph, the T3 instance is launched as `standard` and immediately begins to earn credits\. The instance remains idle from the time it is launched—CPU utilization is 0%—and no credits are spent\. All unspent credits are accrued in the credit balance\. For the first 24 hours, `CPUCreditUsage` is at 0, and the `CPUCreditBalance` reaches its maximum of 144\.

**P2** – For the next 12 hours, CPU utilization is at 2\.5%, which is below the 5% baseline\. The instance earns more credits than it spends, but the `CPUCreditBalance` cannot exceed its maximum of 144 credits\. Any credits that are earned in excess of the limit are discarded\.

**P3** – For the next 24 hours, CPU utilization is at 7% \(above the baseline\), which requires a spend of 57\.6 credits\. The instance spends more credits than it earns, and the `CPUCreditBalance` reduces to 86\.4 credits\.

**P4** – For the next 12 hours, CPU utilization decreases to 2\.5% \(below the baseline\), which requires a spend of 36 credits\. In the same time, the instance earns 72 credits\. The instance earns more credits than it spends, and the `CPUCreditBalance` increases to 122 credits\.

**P5** – For the next two hours, the instance bursts at 100% CPU utilization, and depletes its entire `CPUCreditBalance` of 122 credits\. At the end of this period, the `CPUCreditBalance` and the CPU utilization is forced to drop to the baseline performance level of 5%\. At the baseline, the instance earns as many credits as it spends\.

**P6** – For the next 14 hours, CPU utilization is at 5% \(the baseline\)\. The instance earns as many credits as it spends\. The `CPUCreditBalance` remains at 0\.

**P7** – For the last 24 hours in this example, the instance is idle and CPU utilization is 0%\. During this time, the instance earns 144 credits, which it accrues in its `CPUCreditBalance`\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t3_standard_graph.png)

### <a name="period-1-t3"></a>

## Example 2: Explaining Credit Use with T2 Standard<a name="t2-standard-example"></a>

In this example, you see how a `t2.nano` instance launched as `standard` earns, accrues, and spends *launch* and *earned* credits\. You see how the credit balance reflects not only accrued *earned* credits, but also accrued *launch* credits\.

A `t2.nano` instance gets 30 launch credits when it is launched, and earns 72 credits every 24 hours\. Its credit balance limit is 72 earned credits; launch credits do not count towards the limit\. After the limit is reached, new credits that are earned are discarded\. For more information about the number of credits that can be earned and accrued, see the [T2 and T3 credit table](t2-credits-baseline-concepts.md#t-instance-credit-table)\. For more information about limits, see [Launch Credit Limits](t2-std.md#launch-credit-limits)\.

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
An instance constantly earns credits, but cannot accrue more earned credits if the credit balance limit has been reached\. After the limit is reached, newly earned credits are discarded\. The credit balance limit is determined by the number of credits an instance can earn in 24 hours\. For more information about credit balance limits, see the [T2 and T3 credit table](t2-credits-baseline-concepts.md#t-instance-credit-table)\. 
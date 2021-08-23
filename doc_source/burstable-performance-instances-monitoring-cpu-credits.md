# Monitor your CPU credits<a name="burstable-performance-instances-monitoring-cpu-credits"></a>

You can see the credit balance for each instance in the Amazon EC2 per\-instance metrics of the CloudWatch console\.

**Topics**
+ [Additional CloudWatch metrics for burstable performance instances](#burstable-performance-instances-cw-metrics)
+ [Calculate CPU credit usage](#burstable-performance-instances-calculating-credit-use)

## Additional CloudWatch metrics for burstable performance instances<a name="burstable-performance-instances-cw-metrics"></a>

Burstable performance instances have these additional CloudWatch metrics, which are updated every five minutes:
+ `CPUCreditUsage` – The number of CPU credits spent during the measurement period\.
+ `CPUCreditBalance` – The number of CPU credits that an instance has accrued\. This balance is depleted when the CPU bursts and CPU credits are spent more quickly than they are earned\.
+ `CPUSurplusCreditBalance` – The number of surplus CPU credits spent to sustain CPU utilization when the `CPUCreditBalance` value is zero\.
+ `CPUSurplusCreditsCharged` – The number of surplus CPU credits exceeding the [maximum number of CPU credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) that can be earned in a 24\-hour period, and thus attracting an additional charge\.

The last two metrics apply only to instances configured as `unlimited`\.

The following table describes the CloudWatch metrics for burstable performance instances\. For more information, see [List the available CloudWatch metrics for your instances](viewing_metrics_with_cloudwatch.md)\.


| Metric | Description | 
| --- | --- | 
| CPUCreditUsage |  The number of CPU credits spent by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100% utilization for one minute or an equivalent combination of vCPUs, utilization, and time \(for example, one vCPU running at 50% utilization for two minutes or two vCPUs running at 25% utilization for two minutes\)\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\. Units: Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |  The number of earned CPU credits that an instance has accrued since it was launched or started\. For T2 Standard, the `CPUCreditBalance` also includes the number of launch credits that have been accrued\. Credits are accrued in the credit balance after they are earned, and removed from the credit balance when they are spent\. The credit balance has a maximum limit, determined by the instance size\. After the limit is reached, any new credits that are earned are discarded\. For T2 Standard, launch credits do not count towards the limit\. The credits in the `CPUCreditBalance` are available for the instance to spend to burst beyond its baseline CPU utilization\. When an instance is running, credits in the `CPUCreditBalance` do not expire\. When a T4g, T3a or T3 instance stops, the `CPUCreditBalance` value persists for seven days\. Thereafter, all accrued credits are lost\. When a T2 instance stops, the `CPUCreditBalance` value does not persist, and all accrued credits are lost\. CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)  | 
| CPUSurplusCreditBalance  |  The number of surplus credits that have been spent by an `unlimited` instance when its `CPUCreditBalance` value is zero\. The `CPUSurplusCreditBalance` value is paid down by earned CPU credits\. If the number of surplus credits exceeds the maximum number of credits that the instance can earn in a 24\-hour period, the spent surplus credits above the maximum incur an additional charge\. Units: Credits \(vCPU\-minutes\)   | 
| CPUSurplusCreditsCharged |  The number of spent surplus credits that are not paid down by earned CPU credits, and which thus incur an additional charge\. Spent surplus credits are charged when any of the following occurs:  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances-monitoring-cpu-credits.html) Units: Credits \(vCPU\-minutes\)   | 

## Calculate CPU credit usage<a name="burstable-performance-instances-calculating-credit-use"></a>

The CPU credit usage of instances is calculated using the instance CloudWatch metrics described in the preceding table\.

Amazon EC2 sends the metrics to CloudWatch every five minutes\. A reference to the *prior* value of a metric at any point in time implies the previous value of the metric, sent *five minutes ago*\.

### Calculate CPU credit usage for Standard instances<a name="burstable-performance-instances-standard-calculation"></a>
+ The CPU credit balance increases if CPU utilization is below the baseline, when the credits spent are less than the credits earned in the prior five\-minute interval\. 
+ The CPU credit balance decreases if CPU utilization is above the baseline, when the credits spent are more than the credits earned in the prior five\-minute interval\. 

Mathematically, this is captured by the following equation:

**Example**  

```
CPUCreditBalance = prior CPUCreditBalance + [Credits earned per hour * (5/60) - CPUCreditUsage]
```

The size of the instance determines the number of credits that the instance can earn per hour and the number of earned credits that it can accrue in the credit balance\. For information about the number of credits earned per hour, and the credit balance limit for each instance size, see the [credit table](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table)\.

**Example**  
This example uses a `t3.nano` instance\. To calculate the `CPUCreditBalance` value of the instance, use the preceding equation as follows:
+ `CPUCreditBalance` – The current credit balance to calculate\.
+ `prior CPUCreditBalance` – The credit balance five minutes ago\. In this example, the instance had accrued two credits\.
+ `Credits earned per hour` – A `t3.nano` instance earns six credits per hour\.
+ `5/60` – Represents the five\-minute interval between CloudWatch metric publication\. Multiply the credits earned per hour by 5/60 \(five minutes\) to get the number of credits that the instance earned in the past five minutes\. A `t3.nano` instance earns 0\.5 credits every five minutes\.
+ `CPUCreditUsage` – How many credits the instance spent in the past five minutes\. In this example, the instance spent one credit in the past five minutes\.

Using these values, you can calculate the `CPUCreditBalance` value:

**Example**  

```
CPUCreditBalance = 2 + [0.5 - 1] = 1.5
```

### Calculate CPU credit usage for Unlimited instances<a name="burstable-performance-instances-unlimited-calculation"></a>

When a burstable performance instance needs to burst above the baseline, it always spends accrued credits before spending surplus credits\. When it depletes its accrued CPU credit balance, it can spend surplus credits to burst CPU for as long as it needs\. When CPU utilization falls below the baseline, surplus credits are always paid down before the instance accrues earned credits\.

We use the term `Adjusted balance` in the following equations to reflect the activity that occurs in this five\-minute interval\. We use this value to arrive at the values for the `CPUCreditBalance` and `CPUSurplusCreditBalance` CloudWatch metrics\. 

**Example**  

```
Adjusted balance = [prior CPUCreditBalance - prior CPUSurplusCreditBalance] + [Credits earned per hour * (5/60) - CPUCreditUsage]
```

A value of `0` for `Adjusted balance` indicates that the instance spent all its earned credits for bursting, and no surplus credits were spent\. As a result, both `CPUCreditBalance` and `CPUSurplusCreditBalance` are set to `0`\.

A positive `Adjusted balance` value indicates that the instance accrued earned credits, and previous surplus credits, if any, were paid down\. As a result, the `Adjusted balance` value is assigned to `CPUCreditBalance`, and the `CPUSurplusCreditBalance` is set to `0`\. The instance size determines the [maximum number of credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) that it can accrue\.

**Example**  

```
CPUCreditBalance = min [max earned credit balance, Adjusted balance]
CPUSurplusCreditBalance = 0
```

A negative `Adjusted balance` value indicates that the instance spent all its earned credits that it accrued and, in addition, also spent surplus credits for bursting\. As a result, the `Adjusted balance` value is assigned to `CPUSurplusCreditBalance` and `CPUCreditBalance` is set to `0`\. Again, the instance size determines the [maximum number of credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) that it can accrue\.

**Example**  

```
CPUSurplusCreditBalance = min [max earned credit balance, -Adjusted balance]
CPUCreditBalance = 0
```

If the surplus credits spent exceed the maximum credits that the instance can accrue, the surplus credit balance is set to the maximum, as shown in the preceding equation\. The remaining surplus credits are charged as represented by the `CPUSurplusCreditsCharged` metric\.

**Example**  

```
CPUSurplusCreditsCharged = max [-Adjusted balance - max earned credit balance, 0]
```

Finally, when the instance terminates, any surplus credits tracked by the `CPUSurplusCreditBalance` are charged\. If the instance is switched from `unlimited` to `standard`, any remaining `CPUSurplusCreditBalance` is also charged\.
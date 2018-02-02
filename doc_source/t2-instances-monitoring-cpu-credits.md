# Monitoring Your CPU Credits<a name="t2-instances-monitoring-cpu-credits"></a>

You can see the credit balance for each T2 instance in the Amazon EC2 per\-instance metrics of the CloudWatch console\.


+ [Additional CloudWatch Metrics for T2 Instances](#t2-cw-metrics)
+ [Calculating CPU Credit Usage](#t2-calculating-credit-use)

## Additional CloudWatch Metrics for T2 Instances<a name="t2-cw-metrics"></a>

T2 instances have four additional CloudWatch metrics, which are updated every five minutes:

+ `CPUCreditUsage` – The number of CPU credits used during the measurement period\.

+ `CPUCreditBalance` – The number of unused CPU credits that a T2 instance has earned\. This balance is depleted when the CPU bursts and CPU credits are spent more quickly than they are earned\.

+ `CPUSurplusCreditBalance` – The number of surplus CPU credits used for sustaining CPU performance when the `CPUCreditBalance` is zero\.

+ `CPUSurplusCreditsCharged` – The number of surplus CPU credits that exceed the maximum number of CPU credits that can be earned in a 24\-hour period, and thus attract an additional charge\.

The last two metrics apply only to T2 instances configured as `unlimited`\. 

The following table describes the CloudWatch metrics for T2 instances\. For more information about using these metrics in CloudWatch, see [List the Available CloudWatch Metrics for Your Instances](viewing_metrics_with_cloudwatch.md)\.


| Metric | Description | 
| --- | --- | 
| CPUCreditUsage |  \[T2 instances\] The number of CPU credits used by the instance for CPU utilization\. One CPU credit equals one vCPU running at 100% utilization for one minute or an equivalent combination of vCPUs, utilization, and time \(for example, one vCPU running at 50% utilization for two minutes or two vCPUs running at 25% utilization for two minutes\)\. CPU credit metrics are available at a five\-minute frequency only\. If you specify a period greater than five minutes, use the `Sum` statistic instead of the `Average` statistic\. Units: Credits \(vCPU\-minutes\)  | 
| CPUCreditBalance |  \[T2 instances\] The number of earned CPU credits accumulated since the instance was launched, less the credits used, up to a maximum number based on the instance size\. Credits are stored in the credit balance after they are earned, and removed from the credit balance when they are used\. The credit balance has a maximum limit, determined by the instance size\. If the credit balance has reached the limit, additional earned credits are not added to the balance\. The credits in the `CPUCreditBalance` are available for the instance to use to burst beyond its baseline CPU utilization\. Credits on a running instance do not expire\. However, if you stop an instance, it loses all the credits in the credit balance\. CPU credit metrics are available at a five\-minute frequency only\. Units: Credits \(vCPU\-minutes\)  | 
| CPUSurplusCreditBalance  |  \[T2 instances\] The number of surplus credits that have been used by a T2 Unlimited instance when its `CPUCreditBalance` is zero\. The `CPUSurplusCreditBalance` is paid down by earned CPU credits\. Units: Credits \(vCPU\-minutes\)   | 
| CPUSurplusCreditsCharged |  \[T2 instances\] The number of surplus credits that have been used by a T2 Unlimited instance that are not offset by earned CPU credits\. `CPUSurplusCreditsCharged` tracks the surplus credits that incur an additional charge, and represents the difference between `CPUSurplusCreditBalance` and `CPUCreditBalance`\. Units: Credits \(vCPU\-minutes\)   | 

## Calculating CPU Credit Usage<a name="t2-calculating-credit-use"></a>

The CPU credit usage of T2 Standard and T2 Unlimited instances is calculated using the T2 instance CloudWatch metrics described in the preceding table\. 

Amazon EC2 sends the metrics to CloudWatch every five minutes\. A reference to a *prior* value of a metric at any point in time implies the previous value of the metric, sent *five minutes ago*\.

### Calculating CPU Credit Usage for T2 Standard<a name="t2-std-calculation"></a>

+ The CPU credit balance increases if CPU utilization is below the baseline, when credits used are less than credits earned in the prior five\-minute interval\. 

+ The CPU credit balance decreases if CPU utilization is above the baseline, when credits used are more than credits earned in the prior five\-minute interval\. 

Mathematically, this is captured by the following equation:

**Example**  

```
CPUCreditBalance = prior CPUCreditBalance + [Credits earned per hour * (5/60) - CPUCreditUsage]
```

The size of the instance determines the maximum credit balance that the instance can accumulate and the number of credits that it can earn per hour\. For information about the maximum credit balance for each T2 instance size, and the number of credits earned per hour, see the T2 credit table\.

**Example**  
This example uses a `t2.micro` instance\. To calculate the `CPUCreditBalance` of the instance, use the preceding equation as follows:

+ `CPUCreditBalance` – The current credit balance to calculate\.

+ `prior CPUCreditBalance` – The credit balance five minutes ago\. In this example, the instance had accrued two credits\.

+ `Credits earned per hour` – A `t2.micro` instance earns six credits per hour\.

+ `5/60` – Represents the five\-minute interval between CloudWatch metric publication\. Multiply the credits earned per hour by 5/60 \(five minutes\) to get the number of credits the instance earned in the past five minutes\. A `t2.micro` instance earns 0\.5 credits every five minutes\.

+ `CPUCreditUsage` – How many credits the instance used in the past five minutes\. In this example, the instance used one credit in the past five minutes\.

Using these values, you can calculate the `CPUCreditBalance` value:

**Example**  

```
CPUCreditBalance = 2 + [0.5 - 1] = 1.5
```

### Calculating CPU Credit Usage for T2 Unlimited<a name="t2-unlimited-calculation"></a>

When a T2 instance needs to burst above the baseline, it always uses the earned credit balance before using surplus credits\. When it exhausts its accrued CPU credit balance, it can consume surplus credits to burst for as long as it needs\. When CPU utilization falls below the baseline, surplus credits are always paid down before the instance accrues earned credits\.

We use the term `Adjusted balance` in the following equations to reflect the activity that occurs in this five\-minute interval\. We use this value to arrive at the values for the `CPUCreditBalance` and `CPUSurplusCreditBalance` CloudWatch metrics\. 

**Example**  

```
Adjusted balance = [prior CPUCreditBalance - prior CPUSurplusCreditBalance] + [Credits earned per hour * (5/60) - CPUCreditUsage]
```

A value of `0` for `Adjusted balance` indicates that the instance used all its earned credits for bursting, and no surplus credits were used\. As a result, both `CPUCreditBalance` and `CPUSurplusCreditBalance` are set to `0`\.

A positive `Adjusted balance` value indicates that the instance accrued earned credits, and previous surplus credits, if any, were paid down\. As a result, the `Adjusted balance` value is assigned to `CPUCreditBalance`, and the `CPUSurplusCreditBalance` is set to `0`\. The instance size determines the maximum credit balance it can accrue\.

**Example**  

```
CPUCreditBalance = min [max earned credit balance, Adjusted balance]
CPUSurplusCreditBalance = 0
```

A negative `Adjusted balance` value indicates that the instance used all its earned credits and, in addition, also used surplus credits for bursting\. As a result, the `Adjusted balance` value is assigned to `CPUSurplusCreditBalance` and the `CPUCreditBalance` is set to `0`\. Again, the instance size determines the maximum surplus credit balance it can accrue\.

**Example**  

```
CPUSurplusCreditBalance = min [max earned credit balance, -Adjusted balance]
CPUCreditBalance = 0
```

If the surplus credits used exceed the maximum credits the instance can accrue, the surplus credit balance is set to the maximum as shown in the preceding equation\. The remaining surplus credits are charged as represented by the `CPUSurplusCreditsCharged` metric\.

**Example**  

```
CPUSurplusCreditsCharged = max [-Adjusted balance - max earned credit balance, 0]
```

Finally, when the instance terminates, any surplus credits accumulated in the `CPUSurplusCreditBalance` are charged\. If the instance is switched from Unlimited to Standard, any remaining `CPUSurplusCreditBalance` is also charged\.
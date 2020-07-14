# Unlimited mode concepts<a name="burstable-performance-instances-unlimited-mode-concepts"></a>

The `unlimited` mode is a credit configuration option for burstable performance instances\. It can be enabled or disabled at any time for a running or stopped instance\. You can set `unlimited` as the default credit option at the account level per AWS Region, per burstable performance instance family, so that all new burstable performance instances in the account launch using the default credit option\.

**Note**  
T3 and T3a instances are launched as `unlimited` by default\. T2 instances are launched as `standard` by default\. You can change the default at the account level per AWS Region\. For more information, see [Setting the default credit specification for the account](burstable-performance-instances-how-to.md#burstable-performance-instance-set-default-credit-specification-for-account)\.

## How Unlimited burstable performance instances work<a name="how-burstable-performance-instances-unlimited-works"></a>

If a burstable performance instance configured as `unlimited` depletes its CPU credit balance, it can spend *surplus* credits to burst beyond the [baseline](burstable-credits-baseline-concepts.md#baseline_performance)\. When its CPU utilization falls below the baseline, it uses the CPU credits that it earns to pay down the surplus credits that it spent earlier\. The ability to earn CPU credits to pay down surplus credits enables Amazon EC2 to average the CPU utilization of an instance over a 24\-hour period\. If the average CPU usage over a 24\-hour period exceeds the baseline, the instance is billed for the additional usage at a flat additional rate per vCPU\-hour\.

The following graph shows the CPU usage of a `t3.large`\. The baseline CPU utilization for a `t3.large` is 30%\. If the instance runs at 30% CPU utilization or less on average over a 24\-hour period, there is no additional charge because the cost is already covered by the instance hourly price\. However, if the instance runs at 40% CPU utilization on average over a 24\-hour period, as shown in the graph, the instance is billed for the additional 10% CPU usage at a flat additional rate per vCPU\-hour\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t3-cpu-usage.png)

For more information about the baseline utilization per vCPU for each instance type and how many credits each instance type earns, see the [credit table](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table)\.

## When to use unlimited mode versus fixed CPU<a name="when-to-use-unlimited-mode"></a>

When determining whether you should use a burstable performance instance in `unlimited` mode, such as a T3, or a fixed performance instance, such as an M5, you need to determine the breakeven CPU usage\. The breakeven CPU usage for a burstable performance instance is the point at which a burstable performance instance costs the same as a fixed performance instance\. The breakeven CPU usage helps you determine the following:
+ If the average CPU usage over a 24\-hour period is at or below the breakeven CPU usage, use a burstable performance instance in `unlimited` mode so that you can benefit from the lower price of a burstable performance instance while getting the same performance as a fixed performance instance\.
+ If the average CPU usage over a 24\-hour period is above the breakeven CPU usage, the burstable performance instance will cost more than the equivalently\-sized fixed performance instance\. If a T3 instance continuously bursts at 100% CPU, you end up paying approximately 1\.5 times the price of an equivalently\-sized M5 instance\.

The following graph shows the breakeven CPU usage point where a `t3.large` costs the same as an `m5.large`\. The breakeven CPU usage point for a `t3.large` is 42\.5%\. If the average CPU usage is at 42\.5%, the cost of running the `t3.large` is the same as an `m5.large`, and is more expensive if the average CPU usage is above 42\.5%\. If the workload needs less than 42\.5% average CPU usage, you can benefit from the lower price of the `t3.large` while getting the same performance as an `m5.large`\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/T3-unltd-when-to-use.png)

The following table shows how to calculate the breakeven CPU usage threshold so that you can determine when it's less expensive to use a burstable performance instance in `unlimited` mode or a fixed performance instance\. The columns in the table are labeled A through K\.


|  Instance type  |  vCPUs  |  T3 price\*/hour  |  M5 price\*/hour  |  Price difference  |  T3 baseline utilization per vCPU \(%\)  |  Charge per vCPU hour for surplus credits  |  Charge per vCPU minute  |  Additional burst minutes available per vCPU  |  Additional CPU % available  |  Breakeven CPU %  | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
|  A  |  B  |  C  |  D   |  E = D \- C  |  F  |  G  |  H = G / 60  |  I = E / H  |  J = \(I / 60\) / B  |  K = F \+ J  | 
|  t3\.large  |  2  |  $0\.0835  |  $0\.096  |  $0\.0125  |  30%  |  $0\.05  |  $0\.000833   |  15  |  12\.5%  |  42\.5%  | 


|  | 
| --- |
| \* Price is based on us\-east\-1 and Linux OS\. | 

The table provides the following information:
+ Column A shows the instance type, `t3.large`\.
+ Column B shows the number of vCPUs for the `t3.large`\.
+ Column C shows the price of a `t3.large` per hour\.
+ Column D shows the price of an `m5.large` per hour\.
+ Column E shows the price difference between the `t3.large` and the `m5.large`\. 
+ Column F shows the baseline utilization per vCPU of the `t3.large`, which is 30%\. At the baseline, the hourly cost of the instance covers the cost of the CPU usage\.
+ Column G shows the flat additional rate per vCPU\-hour that an instance is charged if it bursts at 100% CPU after it has depleted its earned credits\.
+ Column H shows the flat additional rate per vCPU\-minute that an instance is charged if it bursts at 100% CPU after it has depleted its earned credits\.
+ Column I shows the number of additional minutes that the `t3.large` can burst per hour at 100% CPU while paying the same price per hour as an `m5.large`\.
+ Column J shows the additional CPU usage \(in %\) over baseline that the instance can burst while paying the same price per hour as an `m5.large`\.
+ Column K shows the breakeven CPU usage \(in %\) that the `t3.large` can burst without paying more than the `m5.large`\. Anything above this, and the `t3.large` costs more than the `m5.large`\.

The following table shows the breakeven CPU usage \(in %\) for T3 instance types compared to the similarly\-sized M5 instance types\.


| T3 instance type | Breakeven CPU usage \(in %\) for T3 compared to M5 | 
| --- | --- | 
| t3\.large | 42\.5% | 
| t3\.xlarge | 52\.5% | 
| t3\.2xlarge | 52\.5% | 

## Surplus credits can incur charges<a name="unlimited-mode-surplus-credits"></a>

If the average CPU utilization of an instance is at or below the baseline, the instance incurs no additional charges\. Because an instance earns a [maximum number of credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) in a 24\-hour period \(for example, a `t3.micro` instance can earn a maximum of 288 credits in a 24\-hour period\), it can spend surplus credits up to that maximum without being charged\.

However, if CPU utilization stays above the baseline, the instance cannot earn enough credits to pay down the surplus credits that it has spent\. The surplus credits that are not paid down are charged at a flat additional rate per vCPU\-hour\.

Surplus credits that were spent earlier are charged when any of the following occurs:
+ The spent surplus credits exceed the [maximum number of credits](burstable-credits-baseline-concepts.md#burstable-performance-instances-credit-table) the instance can earn in a 24\-hour period\. Spent surplus credits above the maximum are charged at the end of the hour\.
+ The instance is stopped or terminated\.
+ The instance is switched from `unlimited` to `standard`\.

Spent surplus credits are tracked by the CloudWatch metric `CPUSurplusCreditBalance`\. Surplus credits that are charged are tracked by the CloudWatch metric `CPUSurplusCreditsCharged`\. For more information, see [Additional CloudWatch metrics for burstable performance instances](burstable-performance-instances-monitoring-cpu-credits.md#burstable-performance-instances-cw-metrics)\.

## No launch credits for T2 Unlimited instances<a name="unlimited-mode-no-launch-credits"></a>

T2 Standard instances receive [launch credits](burstable-performance-instances-standard-mode-concepts.md#launch-credits), but T2 Unlimited instances do not\. A T2 Unlimited instance can burst beyond the baseline at any time with no additional charge, as long as its average CPU utilization is at or below the baseline over a rolling 24\-hour window or its lifetime, whichever is shorter\. As such, T2 Unlimited instances do not require launch credits to achieve high performance immediately after launch\.

If a T2 instance is switched from `standard` to `unlimited`, any accrued launch credits are removed from the `CPUCreditBalance` before the remaining `CPUCreditBalance` is carried over\.

**Note**  
T3 and T3a instances never receive launch credits\.

## Enabling unlimited mode<a name="unlimited-mode-enabling"></a>

T3 and T3a instances launch as `unlimited` by default\. T2 instances launch as `standard` by default, but you can enable `unlimited` at launch\.

You can switch from `unlimited` to `standard`, and from `standard` to `unlimited`, at any time on a running or stopped instance\. For more information, see [Launching a burstable performance instance as Unlimited or Standard](burstable-performance-instances-how-to.md#launch-burstable-performance-instances) and [Modifying the credit specification of a burstable performance instance](burstable-performance-instances-how-to.md#modify-burstable-performance-instances)\.

You can set `unlimited` as the default credit option at the account level per AWS Region, per burstable performance instance family, so that all new burstable performance instances in the account launch using the default credit option\. For more information, see [Setting the default credit specification for the account](burstable-performance-instances-how-to.md#burstable-performance-instance-set-default-credit-specification-for-account)\.

You can check whether your burstable performance instance is configured as `unlimited` or `standard` using the Amazon EC2 console or the AWS CLI\. For more information, see [Viewing the credit specification of a burstable performance instance](burstable-performance-instances-how-to.md#describe-burstable-performance-instances) and [Viewing the default credit specification](burstable-performance-instances-how-to.md#burstable-performance-instances-get-default-credit-specification)\.

## What happens to credits when switching between Unlimited and Standard<a name="unlimited-mode-switching-and-credits"></a>

`CPUCreditBalance` is a CloudWatch metric that tracks the number of credits accrued by an instance\. `CPUSurplusCreditBalance` is a CloudWatch metric that tracks the number of surplus credits spent by an instance\.

When you change an instance configured as `unlimited` to `standard`, the following occurs:
+ The `CPUCreditBalance` value remains unchanged and is carried over\. 
+ The `CPUSurplusCreditBalance` value is immediately charged\.

When a `standard` instance is switched to `unlimited`, the following occurs:
+ The `CPUCreditBalance` value containing accrued earned credits is carried over\.
+ For T2 Standard instances, any launch credits are removed from the `CPUCreditBalance` value, and the remaining `CPUCreditBalance` value containing accrued earned credits is carried over\.

## Monitoring credit usage<a name="unlimited-mode-monitoring-credit-usage"></a>

To see if your instance is spending more credits than the baseline provides, you can use CloudWatch metrics to track usage, and you can set up hourly alarms to be notified of credit usage\. For more information, see [Monitoring your CPU credits](burstable-performance-instances-monitoring-cpu-credits.md)\.
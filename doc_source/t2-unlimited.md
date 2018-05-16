# T2 Unlimited<a name="t2-unlimited"></a>

A T2 Unlimited instance can sustain high CPU performance for any period of time whenever required\. The hourly T2 instance price automatically covers all interim spikes in usage if the average CPU utilization of the instance is at or below the baseline over a rolling 24\-hour period or the instance lifetime, whichever is shorter\. If the instance runs at higher CPU utilization for a prolonged period, it can do so for a flat additional rate per vCPU\-hour\. For information about instance pricing, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and the T2 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**Important**  
If you use a `t2.micro` instance under the [AWS Free Tier](https://aws.amazon.com/free/) offer and configure it as Unlimited, charges may apply if your average utilization over a rolling 24\-hour period exceeds the baseline of the instance\.

**Topics**
+ [T2 Unlimited Concepts](#t2-unlimited-concepts)
+ [Example: Explaining Credit Use with T2 Unlimited](#t2_unlimited_example)

## T2 Unlimited Concepts<a name="t2-unlimited-concepts"></a>

T2 Unlimited is a configuration option for T2 instances that can be set at launch, or enabled at any time for a running or stopped T2 instance\.

T2 Unlimited instances can burst above the baseline for as long as required\. This enables you to enjoy the low T2 instance hourly price for a wide variety of general\-purpose applications, and ensures that your instances are never held to the baseline performance\. ​The basic T2 hourly instance price automatically covers all CPU usage spikes if the average CPU utilization of a T2 Unlimited instance over a rolling 24\-hour period is at or below the baseline\. For a vast majority of general\-purpose workloads, T2 Unlimited instances provide ample performance without any additional charges\. If the average CPU utilization exceeds the baseline over a 24\-hour period, there is a flat additional rate per vCPU\-hour\. For more information, see the T2 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

**How T2 Unlimited Works**  
If a T2 Unlimited instance depletes its CPU credit balance, it can spend *surplus* credits to burst beyond the baseline\. When its CPU utilization falls below the baseline, it uses the CPU credits that it earns to pay down the surplus credits it spent earlier\. The ability to earn CPU credits to pay down surplus credits enables Amazon EC2 to average the CPU utilization of an instance over a 24\-hour period\. 

**Surplus Credits Can Incur Charges**  
If the average CPU utilization of an instance is at or below the baseline, the instance incurs no additional charges\. Because an instance earns a [maximum number of credits](t2-credits-baseline-concepts.md#t2-credit-table) in a 24\-hour period \(for example, a `t2.micro` can earn a maximum of 144 credits in a 24\-hour period\), it can spend surplus credits up to that maximum without being charged\.

However, if CPU utilization stays above the baseline, the instance cannot earn enough credits to pay down the surplus credits it has spent\. The surplus credits that are not paid down are charged at a flat additional rate per vCPU\-hour\. For more information, see the T2 Unlimited Pricing section in [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/)\.

Surplus credits that were spent earlier are charged when any of the following occurs:
+ The spent surplus credits exceed the [maximum number of credits](t2-credits-baseline-concepts.md#t2-credit-table) the instance can earn in a 24\-hour period\. Spent surplus credits above the maximum are charged at the end of the hour\.
+ The instance is stopped or terminated\.
+ The instance is switched from Unlimited to Standard\.

Spent surplus credits are tracked by the CloudWatch metric `CPUSurplusCreditBalance`\. Surplus credits that are charged are tracked by the CloudWatch metric `CPUSurplusCreditsCharged`\. For more information, see [Additional CloudWatch Metrics for T2 Instances](t2-instances-monitoring-cpu-credits.md#t2-cw-metrics)\.

**No Launch Credits for T2 Unlimited**  
T2 Unlimited instances do not receive launch credits\. A T2 Unlimited instance can burst beyond the baseline at any time with no additional charge as long as its average CPU utilization is at or below the baseline over a rolling 24\-hour window or its lifetime, whichever is shorter\. As such, T2 Unlimited instances do not require launch credits to achieve high performance immediately after launch\.

If an instance is switched from Standard to Unlimited, any accrued launch credits are removed from the `CPUCreditBalance` before the remaining `CPUCreditBalance` is carried over\.

**Enabling T2 Unlimited**  
T2 Standard is the default configuration; if you do not enable T2 Unlimited, your T2 instance launches as Standard\. You can switch from Standard to Unlimited, and from Unlimited to Standard, at any time on a running or stopped instance\. For more information, see [Launching a T2 Instance as Unlimited](t2-how-to.md#launch-t2) and [Modifying the Credit Option for CPU Usage of a T2 Instance](t2-how-to.md#modify-t2)\. 

You can view if your T2 instance is configured as Standard or Unlimited using the Amazon EC2 console or the AWS CLI\. For more information, see [Viewing the Credit Option for CPU Usage of a T2 Instance](t2-how-to.md#describe-t2)\.

**What Happens to Credits when Enabling or Disabling T2 Unlimited**  
`CPUCreditBalance` is a CloudWatch metric that tracks the number of credits a T2 Standard or T2 Unlimited instance has accrued\. `CPUSurplusCreditBalance` is a CloudWatch metric that tracks the number of surplus credits a T2 Unlimited instance has spent\.

When a T2 Standard instance is switched to Unlimited, the following occurs:
+ Any launch credits are removed from the `CPUCreditBalance`, and the remaining `CPUCreditBalance` containing accrued earned credits is carried over\.

When a T2 Unlimited instance is switched to Standard, the following occurs:
+ The `CPUCreditBalance` remains unchanged and is carried over\. 
+ The `CPUSurplusCreditBalance` is immediately charged\.

**Monitoring Credit Usage**  
To see if your T2 Unlimited instance is spending more credits than the baseline provides, you can use CloudWatch metrics to track and set up hourly alarms to be notified of credit usage\. For more information, see [Monitoring Your CPU Credits](t2-instances-monitoring-cpu-credits.md)\.

## Example: Explaining Credit Use with T2 Unlimited<a name="t2_unlimited_example"></a>

In this example, you see the CPU utilization of a `t2.nano` instance launched as Unlimited, and how it spends *earned* and *surplus* credits to sustain CPU performance\.

A `t2.nano` instance earns 72 CPU credits over a rolling 24\-hour period, which it can redeem for 72 minutes of vCPU use\. When it depletes its CPU credit balance \(represented by the CloudWatch metric `CPUCreditBalance`\), it can spend *surplus* CPU credits—that it has *not yet earned*—to burst for as long as it needs\. Because a `t2.nano` earns a maximum of 72 credits in a 24\-hour period, it can spend surplus credits up to that maximum without being charged immediately\. If it spends more than 72 CPU credits, it is charged for the difference at the end of the hour\.

The intent of the example, illustrated by the following graph, is to show how an instance can burst using surplus credits even after it depletes its `CPUCreditBalance`\. You can assume that, at the start of the time line in the graph, the instance has an accrued credit balance equal to the maximum number of credits it can earn in 24 hours\. The following workflow references the numbered points on the graph: 

**1** – In the first 10 minutes, `CPUCreditUsage` is at 0, and the `CPUCreditBalance` remains at its maximum of 72\.

**2** – At 23:40, as CPU utilization increases, the instance spends CPU credits and the `CPUCreditBalance` decreases\.

**3** – At around 00:47, the instance depletes its entire `CPUCreditBalance`, and starts to spend surplus credits to sustain high CPU performance\.

**4** – Surplus credits are spent until 01:55, when the `CPUSurplusCreditBalance` reaches 72 CPU credits\. This is equal to the maximum a `t2.nano` can earn in a 24\-hour period\. Any surplus credits spent thereafter cannot be offset by earned credits within the 24\-hour period, which results in a small additional charge at the end of the hour\.

**5** – The instance continues to spend surplus credits until around 02:20\. At this time, CPU utilization falls below the baseline, and the instance starts to earn credits at 3 credits per hour \(or 0\.25 credits every 5 minutes\), which it uses to pay down the `CPUSurplusCreditBalance`\. After the `CPUSurplusCreditBalance` reduces to 0, the instance starts to accrue earned credits in its `CPUCreditBalance` at 0\.25 credits every 5 minutes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2_unlimited_graph.png)

**Calculating the Bill**  
Surplus credits cost $0\.05 per vCPU\-hour\. The instance spent approximately 25 surplus credits between 01:55 and 02:20, which is equivalent to 0\.42 vCPU\-hours\.

Additional charges for this instance are 0\.42 vCPU\-hours x $0\.05/vCPU\-hour = $0\.021, rounded to $0\.02\.

Here is the month\-end bill for this T2 Unlimited instance:

![\[Example bill for a T2 Unlimited instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2_unlimited_bill_linux.png)

You can set billing alerts to be notified every hour of any accruing charges, and take action if required\.
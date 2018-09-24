# Examples: Unlimited Mode<a name="unlimited-mode-examples"></a>

The following examples explain credit use when T3 and T2 instances are configured as `unlimited`\.

**Topics**
+ [Example 1: Explaining Credit Use with T3 Unlimited](#t3_unlimited_example)
+ [Example 2: Explaining Credit Use with T2 Unlimited](#t2_unlimited_example)

## Example 1: Explaining Credit Use with T3 Unlimited<a name="t3_unlimited_example"></a>

In this example, you see the CPU utilization of a `t3.nano` instance launched as `unlimited`, and how it spends *earned* and *surplus* credits to sustain CPU performance\.

A `t3.nano` instance earns 144 CPU credits over a rolling 24\-hour period, which it can redeem for 144 minutes of vCPU use\. When it depletes its CPU credit balance \(represented by the CloudWatch metric `CPUCreditBalance`\), it can spend *surplus* CPU credits—that it has *not yet earned*—to burst for as long as it needs\. Because a `t3.nano` instance earns a maximum of 144 credits in a 24\-hour period, it can spend surplus credits up to that maximum without being charged immediately\. If it spends more than 144 CPU credits, it is charged for the difference at the end of the hour\.

The intent of the example, illustrated by the following graph, is to show how an instance can burst using surplus credits even after it depletes its `CPUCreditBalance`\. The following workflow references the numbered points on the graph:

**P1** – At 0 hours on the graph, the T3 instance is launched as `unlimited` and immediately begins to earn credits\. The instance remains idle from the time it is launched—CPU utilization is 0%—and no credits are spent\. All unspent credits are accrued in the credit balance\. For the first 24 hours, `CPUCreditUsage` is at 0, and the `CPUCreditBalance` value reaches its maximum of 144\.

**P2** – For the next 12 hours, CPU utilization is at 2\.5%, which is below the 5% baseline\. The instance earns more credits than it spends, but the `CPUCreditBalance` value cannot exceed its maximum of 144 credits\.

**P3** – For the next 24 hours, CPU utilization is at 7% \(above the baseline\), which requires a spend of 57\.6 credits\. The instance spends more credits than it earns, and the `CPUCreditBalance` value reduces to 86\.4 credits\.

**P4** – For the next 12 hours, CPU utilization decreases to 2\.5% \(below the baseline\), which requires a spend of 36 credits\. In the same time, the instance earns 72 credits\. The instance earns more credits than it spends, and the `CPUCreditBalance` value increases to 122 credits\.

**P5** – For the next 5 hours, the instance bursts at 100% CPU utilization, and spends a total of 570 credits to sustain the burst\. About an hour into this period, the instance depletes its entire `CPUCreditBalance` of 122 credits, and starts to spend surplus credits to sustain the high CPU performance, totaling 448 surplus credits in this period \(570\-122=448\)\. When the `CPUSurplusCreditBalance` value reaches 144 CPU credits \(the maximum a `t3.nano` instance can earn in a 24\-hour period\), any surplus credits spent thereafter cannot be offset by earned credits\. The surplus credits spent thereafter amounts to 304 credits \(448\-144=304\), which results in a small additional charge at the end of the hour for 304 credits\.

**P6** – For the next 13 hours, CPU utilization is at 5% \(the baseline\)\. The instance earns as many credits as it spends, with no excess to pay down the `CPUSurplusCreditBalance`\. The `CPUSurplusCreditBalance` value remains at 144 credits\.

**P7** – For the last 24 hours in this example, the instance is idle and CPU utilization is 0%\. During this time, the instance earns 144 credits, which it uses to pay down the `CPUSurplusCreditBalance`\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t3_unlimited_graph.png)

## Example 2: Explaining Credit Use with T2 Unlimited<a name="t2_unlimited_example"></a>

In this example, you see the CPU utilization of a `t2.nano` instance launched as `unlimited`, and how it spends *earned* and *surplus* credits to sustain CPU performance\.

A `t2.nano` instance earns 72 CPU credits over a rolling 24\-hour period, which it can redeem for 72 minutes of vCPU use\. When it depletes its CPU credit balance \(represented by the CloudWatch metric `CPUCreditBalance`\), it can spend *surplus* CPU credits—that it has *not yet earned*—to burst for as long as it needs\. Because a `t2.nano` instance earns a maximum of 72 credits in a 24\-hour period, it can spend surplus credits up to that maximum without being charged immediately\. If it spends more than 72 CPU credits, it is charged for the difference at the end of the hour\.

The intent of the example, illustrated by the following graph, is to show how an instance can burst using surplus credits even after it depletes its `CPUCreditBalance`\. You can assume that, at the start of the time line in the graph, the instance has an accrued credit balance equal to the maximum number of credits it can earn in 24 hours\. The following workflow references the numbered points on the graph: 

**1** – In the first 10 minutes, `CPUCreditUsage` is at 0, and the `CPUCreditBalance` value remains at its maximum of 72\.

**2** – At 23:40, as CPU utilization increases, the instance spends CPU credits and the `CPUCreditBalance` value decreases\.

**3** – At around 00:47, the instance depletes its entire `CPUCreditBalance`, and starts to spend surplus credits to sustain high CPU performance\.

**4** – Surplus credits are spent until 01:55, when the `CPUSurplusCreditBalance` value reaches 72 CPU credits\. This is equal to the maximum a `t2.nano` instance can earn in a 24\-hour period\. Any surplus credits spent thereafter cannot be offset by earned credits within the 24\-hour period, which results in a small additional charge at the end of the hour\.

**5** – The instance continues to spend surplus credits until around 02:20\. At this time, CPU utilization falls below the baseline, and the instance starts to earn credits at 3 credits per hour \(or 0\.25 credits every 5 minutes\), which it uses to pay down the `CPUSurplusCreditBalance`\. After the `CPUSurplusCreditBalance` value reduces to 0, the instance starts to accrue earned credits in its `CPUCreditBalance` at 0\.25 credits every 5 minutes\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2_unlimited_graph.png)

**Calculating the Bill**  
Surplus credits cost $0\.05 per vCPU\-hour\. The instance spent approximately 25 surplus credits between 01:55 and 02:20, which is equivalent to 0\.42 vCPU\-hours\.

Additional charges for this instance are 0\.42 vCPU\-hours x $0\.05/vCPU\-hour = $0\.021, rounded to $0\.02\.

Here is the month\-end bill for this T2 Unlimited instance:

![\[Example bill for a T2 Unlimited instance\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/t2_unlimited_bill_linux.png)

You can set billing alerts to be notified every hour of any accruing charges, and take action if required\.
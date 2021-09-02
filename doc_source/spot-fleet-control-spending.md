# Control spending<a name="spot-fleet-control-spending"></a>

Spot Fleet stops launching instances when it has either reached the target capacity or the maximum amount you’re willing to pay\. To control the amount you pay per hour for your fleet, you can specify the `SpotMaxTotalPrice` for Spot Instances and the `OnDemandMaxTotalPrice` for On\-Demand Instances\. When the maximum total price is reached, Spot Fleet stops launching instances even if it hasn’t met the target capacity\.

The following examples show two different scenarios\. In the first, Spot Fleet stops launching instances when it has met the target capacity\. In the second, Spot Fleet stops launching instances when it has reached the maximum amount you’re willing to pay\.

**Example: Stop launching instances when target capacity is reached**

Given a request for `m4.large` On\-Demand Instances, where:
+ On\-Demand Price: $0\.10 per hour
+ `OnDemandTargetCapacity`: 10
+ `OnDemandMaxTotalPrice`: $1\.50

Spot Fleet launches 10 On\-Demand Instances because the total of $1\.00 \(10 instances x $0\.10\) does not exceed the `OnDemandMaxTotalPrice` of $1\.50\.

**Example: Stop launching instances when maximum total price is reached**

Given a request for `m4.large` On\-Demand Instances, where:
+ On\-Demand Price: $0\.10 per hour
+ `OnDemandTargetCapacity`: 10
+ `OnDemandMaxTotalPrice`: $0\.80

If Spot Fleet launches the On\-Demand target capacity \(10 On\-Demand Instances\), the total cost per hour would be $1\.00\. This is more than the amount \($0\.80\) specified for `OnDemandMaxTotalPrice`\. To prevent spending more than you're willing to pay, Spot Fleet launches only 8 On\-Demand Instances \(below the On\-Demand target capacity\) because launching more would exceed the `OnDemandMaxTotalPrice`\.
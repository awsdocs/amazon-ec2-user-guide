# Control spending<a name="ec2-fleet-control-spending"></a>

EC2 Fleet stops launching instances when it has met one of the following parameters: the `TotalTargetCapacity` or the `MaxTotalPrice` \(the maximum amount you’re willing to pay\)\. To control the amount you pay per hour for your fleet, you can specify the `MaxTotalPrice`\. When the maximum total price is reached, EC2 Fleet stops launching instances even if it hasn’t met the target capacity\.

The following examples show two different scenarios\. In the first, EC2 Fleet stops launching instances when it has met the target capacity\. In the second, EC2 Fleet stops launching instances when it has reached the maximum amount you’re willing to pay \(`MaxTotalPrice`\)\.

**Example: Stop launching instances when target capacity is reached**

Given a request for `m4.large` On\-Demand Instances, where:
+ On\-Demand Price: $0\.10 per hour
+ `OnDemandTargetCapacity`: 10
+ `MaxTotalPrice`: $1\.50

EC2 Fleet launches 10 On\-Demand Instances because the total of $1\.00 \(10 instances x $0\.10\) does not exceed the `MaxTotalPrice` of $1\.50 for On\-Demand Instances\.

**Example: Stop launching instances when maximum total price is reached**

Given a request for `m4.large` On\-Demand Instances, where:
+ On\-Demand Price: $0\.10 per hour
+ `OnDemandTargetCapacity`: 10
+ `MaxTotalPrice`: $0\.80

If EC2 Fleet launches the On\-Demand target capacity \(10 On\-Demand Instances\), the total cost per hour would be $1\.00\. This is more than the amount \($0\.80\) specified for `MaxTotalPrice` for On\-Demand Instances\. To prevent spending more than you're willing to pay, EC2 Fleet launches only 8 On\-Demand Instances \(below the On\-Demand target capacity\) because launching more would exceed the `MaxTotalPrice` for On\-Demand Instances\.
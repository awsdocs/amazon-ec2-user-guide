# Launch instances using an EC2 Fleet<a name="ec2-fleet"></a>

An *EC2 Fleet* contains the configuration information to launch a fleet—or group—of instances\. In a single API call, a fleet can launch multiple instance types across multiple Availability Zones, using the On\-Demand Instance, Reserved Instance, and Spot Instance purchasing options together\. Using EC2 Fleet, you can:
+ Define separate On\-Demand and Spot capacity targets and the maximum amount you’re willing to pay per hour
+ Specify the instance types that work best for your applications
+ Specify how Amazon EC2 should distribute your fleet capacity within each purchasing option

You can also set a maximum amount per hour that you’re willing to pay for your fleet, and EC2 Fleet launches instances until it reaches the maximum amount\. When the maximum amount you're willing to pay is reached, the fleet stops launching instances even if it hasn’t met the target capacity\.

The EC2 Fleet attempts to launch the number of instances that are required to meet the target capacity specified in your request\. If you specified a total maximum price per hour, it fulfills the capacity until it reaches the maximum amount that you’re willing to pay\. The fleet can also attempt to maintain its target Spot capacity if your Spot Instances are interrupted\. For more information, see [How Spot Instances work](how-spot-instances-work.md)\.

![\[A sample EC2 fleet\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ec2-fleet.png)

You can specify an unlimited number of instance types per EC2 Fleet\. Those instance types can be provisioned using both On\-Demand and Spot purchasing options\. You can also specify multiple Availability Zones, specify different maximum Spot prices for each instance, and choose additional Spot options for each fleet\. Amazon EC2 uses the specified options to provision capacity when the fleet launches\.

While the fleet is running, if Amazon EC2 reclaims a Spot Instance because of a price increase or instance failure, EC2 Fleet can try to replace the instances with any of the instance types that you specify\. This makes it easier to regain capacity during a spike in Spot pricing\. You can develop a flexible and elastic resourcing strategy for each fleet\. For example, within specific fleets, your primary capacity can be On\-Demand supplemented with less\-expensive Spot capacity if available\. 

If you have Reserved Instances and you specify On\-Demand Instances in your fleet, EC2 Fleet uses your Reserved Instances\. For example, if your fleet specifies an On\-Demand Instance as `c4.large`, and you have Reserved Instances for `c4.large`, you receive the Reserved Instance pricing\.

There is no additional charge for using EC2 Fleet\. You pay only for the EC2 instances that the fleet launches for you\.

**Topics**
+ [EC2 Fleet limitations](#EC2-fleet-limitations)
+ [EC2 Fleet limits](#ec2-fleet-limits)
+ [Burstable performance instances](#ec2-fleet-burstable-spot-instances)
+ [EC2 Fleet configuration strategies](ec2-fleet-configuration-strategies.md)
+ [Work with EC2 Fleets](manage-ec2-fleet.md)
+ [Use Amazon EventBridge to monitor EC2 Fleet events](ec2-fleet-eventbridge.md)

## EC2 Fleet limitations<a name="EC2-fleet-limitations"></a>

The following limitations apply to EC2 Fleet:
+ EC2 Fleet is available only through the API or AWS CLI\.
+ An EC2 Fleet request can't span AWS Regions\. You need to create a separate EC2 Fleet for each Region\.
+ An EC2 Fleet request can't span different subnets from the same Availability Zone\.

## EC2 Fleet limits<a name="ec2-fleet-limits"></a>

The usual Amazon EC2 limits apply to instances launched by an EC2 Fleet, such as Spot request price limits, instance limits, and volume limits\. In addition, the following limits apply:
+ The number of active EC2 Fleets per AWS Region: 1,000 \* †
+ The number of Spot capacity pools \(unique combination of instance type and subnet\): 300\* ‡
+ The size of the user data in a launch specification: 16 KB †
+ The target capacity per EC2 Fleet: 10,000
+ The target capacity across all EC2 Fleets in a Region: 100,000 \*
+ An EC2 Fleet request can't span Regions\.
+ An EC2 Fleet request can't span different subnets from the same Availability Zone\.

If you need more than the default limits for target capacity, complete the AWS Support Center [Create case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-fleet) form to request a limit increase\. For **Limit type**, choose **EC2 Fleet**, choose a Region, and then choose **Target Fleet Capacity per Fleet \(in units\)** or **Target Fleet Capacity per Region \(in units\)**, or both\.

\* These limits apply to both your EC2 Fleets and your Spot Fleets\.

† These are hard limits\. You cannot request a limit increase for these limits\.

‡ This limit only applies to fleets of type `request` or `maintain`\. This limit does not apply to `instant` fleets\.

## Burstable performance instances<a name="ec2-fleet-burstable-spot-instances"></a>

If you launch your Spot Instances using a [burstable performance instance type](burstable-performance-instances.md), and if you plan to use your burstable performance Spot Instances immediately and for a short duration, with no idle time for accruing CPU credits, we recommend that you launch them in [Standard mode](burstable-performance-instances-standard-mode.md) to avoid paying higher costs\. If you launch burstable performance Spot Instances in [Unlimited mode](burstable-performance-instances-unlimited-mode.md) and burst CPU immediately, you'll spend surplus credits for bursting\. If you use the instance for a short duration, the instance doesn't have time to accrue CPU credits to pay down the surplus credits, and you are charged for the surplus credits when you terminate the instance\.

Unlimited mode is suitable for burstable performance Spot Instances only if the instance runs long enough to accrue CPU credits for bursting\. Otherwise, paying for surplus credits makes burstable performance Spot Instances more expensive than using other instances\. For more information, see [When to use unlimited mode versus fixed CPU](burstable-performance-instances-unlimited-mode-concepts.md#when-to-use-unlimited-mode)\.

Launch credits are meant to provide a productive initial launch experience for T2 instances by providing sufficient compute resources to configure the instance\. Repeated launches of T2 instances to access new launch credits is not permitted\. If you require sustained CPU, you can earn credits \(by idling over some period\), use [Unlimited mode](burstable-performance-instances-unlimited-mode.md) for T2 Spot Instances, or use an instance type with dedicated CPU\.
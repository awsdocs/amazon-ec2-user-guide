# Spot Instance Limits<a name="using-spot-limits"></a>

Spot Instance requests are subject to the following limits:


+ [Spot Request Limits](#spot-limits-general)
+ [Spot Fleet Limits](#spot-fleet-limitations)

## Spot Request Limits<a name="spot-limits-general"></a>

By default, there is an account limit of 20 Spot Instances per region\. If you terminate your Spot Instance but do not cancel the request, the request counts against this limit until Amazon EC2 detects the termination and closes the request\.

Spot Instance limits are dynamic\. When your account is new, your limit might be lower than 20 to start, but increase over time\. In addition, your account might have limits on specific Spot Instance types\. If you submit a Spot Instance request and you receive the error `Max spot instance count exceeded`, you can complete the AWS Support Center [Create Case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances) form to request an Amazon EC2 instance limit increase\. For **Use Case Description**, indicate that you need an increase in your limits for Spot Instance requests\. For more information, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.

## Spot Fleet Limits<a name="spot-fleet-limitations"></a>

The usual Amazon EC2 limits apply to instances launched by a Spot Fleet, such as Spot request price limits, instance limits, and volume limits\. In addition, the following limits apply:

+ The number of active Spot Fleets per region: 1,000

+ The number of launch specifications per fleet: 50

+ The size of the user data in a launch specification: 16 KB

+ The target capacity per Spot Fleet: 3,000

+ The target capacity across all Spot Fleets in a region: 5,000

+ A Spot Fleet request can't span regions\.

+ A Spot Fleet request can't span different subnets from the same Availability Zone\.
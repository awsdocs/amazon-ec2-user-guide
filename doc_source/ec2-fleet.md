# Launching an EC2 Fleet<a name="ec2-fleet"></a>

An *EC2 Fleet* contains the configuration information to launch a fleet—or group—of instances\. In a single API call, a fleet can launch multiple instance types across multiple Availability Zones, using the On\-Demand Instance, Reserved Instance, and Spot Instance purchasing options together\. Using EC2 Fleet, you can define separate On\-Demand and Spot capacity targets, specify the instance types that work best for your applications, and specify how Amazon EC2 should distribute your fleet capacity within each purchasing option\.

The EC2 Fleet attempts to launch the number of instances that are required to meet the target capacity specified in your request\. The fleet can also attempt to maintain its target Spot capacity if your Spot Instances are interrupted due to a change in Spot prices or available capacity\. For more information, see [How Spot Instances Work](how-spot-instances-work.md)\.

![\[A sample EC2 fleet\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/ec2-fleet.png)

You can specify an unlimited number of instance types per EC2 Fleet\. Those instance types can be provisioned using both On\-Demand and Spot purchasing options\. You can also specify multiple Availability Zones, specify different maximum Spot prices for each instance, and choose additional Spot options for each fleet\. Amazon EC2 uses the specified options to provision capacity when the fleet launches\.

While the fleet is running, if Amazon EC2 reclaims a Spot Instance because of a price increase or instance failure, EC2 Fleet can try to replace the instances with any of the instance types that you specify\. This makes it easier to regain capacity during a spike in Spot pricing\. You can develop a flexible and elastic resourcing strategy for each fleet\. For example, within specific fleets, your primary capacity can be On\-Demand supplemented with less\-expensive Spot capacity if available\. 

If you have Reserved Instances and you specify On\-Demand Instances in your fleet, EC2 Fleet uses your Reserved Instances\. For example, if your fleet specifies an On\-Demand Instance as `c4.large`, and you have Reserved Instances for `c4.large`, you receive the Reserved Instance pricing\.

There is no additional charge for using EC2 Fleet\. You pay only for the EC2 instances that the fleet launches for you\.

**Topics**
+ [EC2 Fleet Limitations](#EC2-fleet-limitations)
+ [EC2 Fleet Configuration Strategies](ec2-fleet-configuration-strategies.md)
+ [Managing an EC2 Fleet](manage-ec2-fleet.md)

## EC2 Fleet Limitations<a name="EC2-fleet-limitations"></a>

The following limitations apply to EC2 Fleet:
+ EC2 Fleet is available only through the API or AWS CLI\.
+ You need to create a separate EC2 Fleet for each Region\.
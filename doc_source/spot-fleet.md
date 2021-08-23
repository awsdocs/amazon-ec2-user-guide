# Spot Fleet<a name="spot-fleet"></a>

A Spot Fleet is set of Spot Instances and optionally On\-Demand Instances that is launched based on criteria that you specify\. The Spot Fleet selects the Spot capacity pools that meet your needs and launches Spot Instances to meet the target capacity for the fleet\. By default, Spot Fleets are set to *maintain* target capacity by launching replacement instances after Spot Instances in the fleet are terminated\. You can submit a Spot Fleet as a one\-time *request*, which does not persist after the instances have been terminated\. You can include On\-Demand Instance requests in a Spot Fleet request\.

**Topics**
+ [How Spot Fleet works](how-spot-fleet-works.md)
+ [Spot Fleet requests](spot-fleet-requests.md)
+ [CloudWatch metrics for Spot Fleet](spot-fleet-cloudwatch-metrics.md)
+ [Automatic scaling for Spot Fleet](spot-fleet-automatic-scaling.md)
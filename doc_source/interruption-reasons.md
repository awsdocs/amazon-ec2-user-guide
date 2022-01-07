# Reasons for interruption<a name="interruption-reasons"></a>

The following are the possible reasons that Amazon EC2 might interrupt your Spot Instances:

**Price**  
The Spot price is greater than your maximum price\.

**Capacity**  
Amazon EC2 can interrupt your Spot Instance when it needs it back\. EC2 reclaims your instance mainly to repurpose capacity, but it can also occur for other reasons such as host maintenance or hardware decommission\.

**Constraints**  
If your Spot request includes a constraint such as a launch group or an Availability Zone group, the Spot Instances are terminated as a group when the constraint can no longer be met\.

You can see the historical interruption rates for your instance type in the [Spot Instance Advisor](http://aws.amazon.com/ec2/spot/instance-advisor/)\.
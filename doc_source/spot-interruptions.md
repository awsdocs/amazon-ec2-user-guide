# Spot Instance interruptions<a name="spot-interruptions"></a>

You can launch Spot Instances on spare EC2 capacity for steep discounts in exchange for returning them when Amazon EC2 needs the capacity back\. When Amazon EC2 reclaims a Spot Instance, we call this event a *Spot Instance interruption*\.

When Amazon EC2 interrupts a Spot Instance, it either terminates, stops, or hibernates the instance, depending on what you specified when you created the Spot request\.

Demand for Spot Instances can vary significantly from moment to moment, and the availability of Spot Instances can also vary significantly depending on how many unused EC2 instances are available\. It is always possible that your Spot Instance might be interrupted\.

An On\-Demand Instance specified in an EC2 Fleet or Spot Fleet cannot be interrupted\.

**Topics**
+ [Reasons for interruption](interruption-reasons.md)
+ [Interruption behavior](interruption-behavior.md)
+ [Stop interrupted Spot Instances](stop-spot-instances.md)
+ [Hibernate interrupted Spot Instances](hibernate-spot-instances.md)
+ [Terminate interrupted Spot Instances](terminate-interrupted-spot-instances.md)
+ [Prepare for interruptions](prepare-for-interruptions.md)
+ [Initiate a Spot Instance interruption](initiate-a-spot-instance-interruption.md)
+ [Spot Instance interruption notices](spot-instance-termination-notices.md)
+ [Find interrupted Spot Instances](finding-an-interrupted-Spot-Instance.md)
+ [Determine whether Amazon EC2 terminated a Spot Instance](BidEvictedEvent.md)
+ [Billing for interrupted Spot Instances](billing-for-interrupted-spot-instances.md)
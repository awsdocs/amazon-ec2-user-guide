# How Spot Instances work<a name="how-spot-instances-work"></a>

To launch a Spot Instance, either you create a *Spot Instance request*, or Amazon EC2 creates a Spot Instance request on your behalf\. The Spot Instance launches when the Spot Instance request is fulfilled\.

You can launch a Spot Instance using several different services\. For more information, see [Getting Started with Amazon EC2 Spot Instances](http://aws.amazon.com/ec2/spot/getting-started/)\. In this user guide, we describe the following ways to launch a Spot Instance using EC2:
+ You can create a Spot Instance request by using the [launch instance wizard](ec2-launch-instance-wizard.md) in the Amazon EC2 console or the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command\. For more information, see [Create a Spot Instance request](spot-requests.md#using-spot-instances-request)\.
+ You can create an EC2 Fleet, in which you specify the desired number of Spot Instances\. Amazon EC2 creates a Spot Instance request on your behalf for every Spot Instance that is specified in the EC2 Fleet\. For more information, see [Create an EC2 Fleet](manage-ec2-fleet.md#create-ec2-fleet)\.
+ You can create a Spot Fleet request, in which you specify the desired number of Spot Instances\. Amazon EC2 creates a Spot Instance request on your behalf for every Spot Instance that is specified in the Spot Fleet request\. For more information, see [Create a Spot Fleet request](work-with-spot-fleets.md#create-spot-fleet)\.

Your Spot Instance launches if there is available capacity\.

Your Spot Instance runs until you stop or terminate it, or until Amazon EC2 interrupts it \(known as a *Spot Instance interruption*\)\.

When you use Spot Instances, you must be prepared for interruptions\. Amazon EC2 can interrupt your Spot Instance when the demand for Spot Instances rises or when the supply of Spot Instances decreases\. When Amazon EC2 interrupts a Spot Instance, it provides a Spot Instance interruption notice, which gives the instance a two\-minute warning before Amazon EC2 interrupts it\. You can't enable termination protection for Spot Instances\. For more information, see [Spot Instance interruptions](spot-interruptions.md)\.

You can stop, start, reboot, or terminate an Amazon EBS\-backed Spot Instance\. The Spot service can stop, terminate, or hibernate a Spot Instance when it interrupts it\.

**Topics**
+ [Launch Spot Instances in a launch group](#spot-launch-group)
+ [Launch Spot Instances in an Availability Zone group](#spot-az-group)
+ [Launch Spot Instances in a VPC](#concepts-spot-instances-vpcs)

## Launch Spot Instances in a launch group<a name="spot-launch-group"></a>

Specify a launch group in your Spot Instance request to tell Amazon EC2 to launch a set of Spot Instances only if it can launch them all\. In addition, if the Spot service must terminate one of the instances in a launch group, it must terminate them all\. However, if you terminate one or more of the instances in a launch group, Amazon EC2 does not terminate the remaining instances in the launch group\.

Although this option can be useful, adding this constraint can decrease the chances that your Spot Instance request is fulfilled and increase the chances that your Spot Instances are terminated\. For example, your launch group includes instances in multiple Availability Zones\. If capacity in one of these Availability Zones decreases and is no longer available, then Amazon EC2 terminates all instances for the launch group\.

If you create another successful Spot Instance request that specifies the same \(existing\) launch group as an earlier successful request, then the new instances are added to the launch group\. Subsequently, if an instance in this launch group is terminated, all instances in the launch group are terminated, which includes instances launched by the first and second requests\.

## Launch Spot Instances in an Availability Zone group<a name="spot-az-group"></a>

Specify an Availability Zone group in your Spot Instance request to tell Amazon EC2 to launch a set of Spot Instances in the same Availability Zone\. Amazon EC2 need not interrupt all instances in an Availability Zone group at the same time\. If Amazon EC2 must interrupt one of the instances in an Availability Zone group, the others remain running\.

Although this option can be useful, adding this constraint can lower the chances that your Spot Instance request is fulfilled\.

If you specify an Availability Zone group but don't specify an Availability Zone in the Spot Instance request, the result depends on the network you specified\.

**Default VPC**  
Amazon EC2 uses the Availability Zone for the specified subnet\. If you don't specify a subnet, it selects an Availability Zone and its default subnet, but not necessarily the lowest\-priced zone\. If you deleted the default subnet for an Availability Zone, then you must specify a different subnet\.

**Nondefault VPC**  
Amazon EC2 uses the Availability Zone for the specified subnet\.

## Launch Spot Instances in a VPC<a name="concepts-spot-instances-vpcs"></a>

You specify a subnet for your Spot Instances the same way that you specify a subnet for your On\-Demand Instances\.
+ \[Default VPC\] If you want your Spot Instance launched in a specific low\-priced Availability Zone, you must specify the corresponding subnet in your Spot Instance request\. If you do not specify a subnet, Amazon EC2 selects one for you, and the Availability Zone for this subnet might not have the lowest Spot price\.
+ \[Nondefault VPC\] You must specify the subnet for your Spot Instance\.
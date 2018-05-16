# How Spot Instances Work<a name="how-spot-instances-work"></a>

To use Spot Instances, create a *Spot Instance request* or a *Spot Fleet request*\. The request can include the maximum price that you are willing to pay per hour per instance \(the default is the On\-Demand price\), and other constraints such as the instance type and Availability Zone\. If your maximum price exceeds the current Spot price for the specified instance, and capacity is available, your request is fulfilled immediately\. Otherwise, the request is fulfilled whenever the maximum price exceeds the Spot price and the capacity is available\. Spot Instances run until you terminate them or until Amazon EC2 must interrupt them \(known as a *Spot Instance interruption*\)\.

When you use Spot Instances, you must be prepared for interruptions\. Amazon EC2 can interrupt your Spot Instance when the Spot price exceeds your maximum price, when the demand for Spot Instances rises, or when the supply of Spot Instances decreases\. When Amazon EC2 interrupts a Spot Instance, it provides a Spot Instance interruption notice, which gives the instance a two\-minute warning before Amazon EC2 interrupts it\. You can't enable termination protection for Spot Instances\. For more information, see [Spot Instance Interruptions](spot-interruptions.md)\.

You can't stop and start an Amazon EBS\-backed instance if it is a Spot Instance \(only the Spot service can stop and start a Spot Instance\), but you can reboot or terminate a Spot Instance\.

**Topics**
+ [Launching Spot Instances in a Launch Group](#spot-launch-group)
+ [Launching Spot Instances in an Availability Zone Group](#spot-az-group)
+ [Launching Spot Instances in a VPC](#concepts-spot-instances-vpcs)

## Launching Spot Instances in a Launch Group<a name="spot-launch-group"></a>

Specify a launch group in your Spot Instance request to tell Amazon EC2 to launch a set of Spot Instances only if it can launch them all\. In addition, if the Spot service must terminate one of the instances in a launch group \(for example, if the Spot price exceeds your maximum price\), it must terminate them all\. However, if you terminate one or more of the instances in a launch group, Amazon EC2 does not terminate the remaining instances in the launch group\.

Although this option can be useful, adding this constraint can lower the chances that your Spot Instance request is fulfilled\. It can also increase the chance that your Spot Instances will be terminated\.

If you create another successful Spot Instance request that specifies the same \(existing\) launch group as an earlier successful request, then the new instances are added to the launch group\. Subsequently, if an instance in this launch group is terminated, all instances in the launch group are terminated, which includes instances launched by the first and second requests\.

## Launching Spot Instances in an Availability Zone Group<a name="spot-az-group"></a>

Specify an Availability Zone group in your Spot Instance request to tell the Spot service to launch a set of Spot Instances in the same Availability Zone\. Amazon EC2 need not interrupt all instances in an Availability Zone group at the same time\. If Amazon EC2 must interrupt one of the instances in an Availability Zone group, the others remain running\.

Although this option can be useful, adding this constraint can lower the chances that your Spot Instance request is fulfilled\.

If you specify an Availability Zone group but don't specify an Availability Zone in the Spot Instance request, the result depends on whether you specified the EC2\-Classic network, a default VPC, or a nondefault VPC\. For more information, see [Supported Platforms](ec2-supported-platforms.md)\.

**EC2\-Classic**  
Amazon EC2 finds the lowest\-priced Availability Zone in the region and launches your Spot Instances in that Availability Zone if the lowest price for the group is higher than the current Spot price in that Availability Zone\. Amazon EC2 waits until there is enough capacity to launch your Spot Instances together, as long as the Spot price remains lower than the lowest price for the group\.

**Default VPC**  
Amazon EC2 uses the Availability Zone for the specified subnet, or if you don't specify a subnet, it selects an Availability Zone and its default subnet, but it might not be the lowest\-priced Availability Zone\. If you deleted the default subnet for an Availability Zone, then you must specify a different subnet\.

**Nondefault VPC**  
Amazon EC2 uses the Availability Zone for the specified subnet\.

## Launching Spot Instances in a VPC<a name="concepts-spot-instances-vpcs"></a>

To take advantage of the features of EC2\-VPC when you use Spot Instances, specify in your Spot request that your Spot Instances are to be launched in a VPC\. You specify a subnet for your Spot Instances the same way that you specify a subnet for your On\-Demand Instances\.

The process for making a Spot Instance request that launches Spot Instances in a VPC is the same as the process for making a Spot Instance request that launches Spot Instances in EC2\-Classic—except for the following differences: 
+ You should use the default maximum price \(the On\-Demand price\), or base your maximum price on the Spot price history of Spot Instances in a VPC\.
+ \[Default VPC\] If you want your Spot Instance launched in a specific low\-priced Availability Zone, you must specify the corresponding subnet in your Spot Instance request\. If you do not specify a subnet, Amazon EC2 selects one for you, and the Availability Zone for this subnet might not have the lowest Spot price\.
+ \[Nondefault VPC\] You must specify the subnet for your Spot Instance\.
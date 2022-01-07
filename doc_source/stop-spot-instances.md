# Stop interrupted Spot Instances<a name="stop-spot-instances"></a>

You can specify that Amazon EC2 stops your Spot Instances when they are interrupted\. For more information, see [Specify the interruption behavior](interruption-behavior.md#specifying-spot-interruption-behavior)\.

## Considerations<a name="stopped-interrupted-spot-instances-considerations"></a>
+ Only Amazon EC2 can restart an interrupted stopped Spot Instance\.
+ For a Spot Instance launched by a `persistent` Spot Instance request: Amazon EC2 restarts the stopped instance when capacity is available in the same Availability Zone and for the same instance type as the stopped instance \(the same launch specification must be used\)\.
+ For Spot Instances launched by an EC2 Fleet or Spot Fleet of type `maintain`: After a Spot Instance is interrupted, Amazon EC2 launches a replacement instance to maintain the target capacity\. Amazon EC2 finds the best Spot capacity pools based on the specified allocation strategy \(`lowestPrice`, `diversified`, or `InstancePoolsToUseCount`\); it does not prioritize the pool with the earlier stopped instance\. Later, if the allocation strategy leads to a pool containing the earlier stopped instance, Amazon EC2 restarts the stopped instance to meet the target capacity\.

  For example, consider a Spot Fleet with the `lowestPrice` allocation strategy\. At initial launch, a `c3.large` pool meets the `lowestPrice` criteria for the launch specification\. Later, when the `c3.large` instances are interrupted, Amazon EC2 stops the instances and replenishes capacity from another pool that fits the `lowestPrice` strategy\. This time, the pool happens to be a `c4.large` pool and Amazon EC2 launches `c4.large` instances to meet the target capacity\. Similarly, Spot Fleet could move to a `c5.large` pool the next time\. In each of these transitions, Amazon EC2 does not prioritize pools with earlier stopped instances, but rather prioritizes purely on the specified allocation strategy\. The `lowestPrice` strategy can lead back to pools with earlier stopped instances\. For example, if instances are interrupted in the `c5.large` pool and the `lowestPrice` strategy leads it back to the `c3.large` or `c4.large` pools, the earlier stopped instances are restarted to fulfill target capacity\.
+ While a Spot Instance is stopped, you can modify some of its instance attributes, but not the instance type\. If you detach or delete an EBS volume, it is not attached when the Spot Instance is started\. If you detach the root volume and Amazon EC2 attempts to start the Spot Instance, the instance will fail to start and Amazon EC2 will terminate the stopped instance\.
+ You can terminate a Spot Instance while it is stopped\.
+ If you cancel a Spot Instance request, an EC2 Fleet, or a Spot Fleet, Amazon EC2 terminates any associated Spot Instances that are stopped\.
+ While a Spot Instance is stopped, you are charged only for the EBS volumes, which are preserved\. With EC2 Fleet and Spot Fleet, if you have many stopped instances, you can exceed the limit on the number of EBS volumes for your account\.
+ Make sure that you are familiar with the implications of stopping an instance\. For information about what happens when an instance is stopped, see [Differences between reboot, stop, hibernate, and terminate](ec2-instance-lifecycle.md#lifecycle-differences)\.

## Prerequisites<a name="spot-instance-stop-prerequisites"></a>

To stop an interrupted Spot Instance, the following prerequisites must be in place:

**Spot request type**  
Spot Instance request type – Must be `persistent`\. You can't specify a launch group in the Spot Instance request\.  
EC2 Fleet or Spot Fleet request type – Must be `maintain`\.

**Root volume type**  
Must be an EBS volume, not an instance store volume\.
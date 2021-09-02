# Requirements for changing the instance type<a name="resize-requirements"></a>

To resize your Amazon EC2 instance by changing its instance type, consider the following requirements:
+ The steps for changing the instance type are different depending on whether the [root device volume](RootDeviceStorage.md) for your instance is an EBS volume or an instance store volume\.
  + If the root device is an EBS volume, you can change the instance type of the original instance\. For the instructions, see [Change the instance type of an Amazon EBS–backed instance](resize-ebs-backed-instance.md)\.
  + If the root device is an instance store volume, you must migrate your application to a new instance that is configured with the instance type that you need\. For the instructions, see [Migrate an instance store\-backed instance](resize-instance-store-backed-instance.md)
+ You must select an instance type that is compatible with the configuration of the instance\. If the instance type that you want is not compatible with the instance configuration you have, then you must migrate your application to a new instance with the instance type that you need\.
+ To change the instance type, the instance must be in the `stopped` state\.
+ You cannot resize an instance if hibernation is enabled\.
# Limitations<a name="instance-hibernate-limitations"></a>
+ When you hibernate an instance, the data on any instance store volumes is lost\.
+ You can't hibernate an instance that has more than 150 GB of RAM\.
+ If you create a snapshot or AMI from an instance that is hibernated or has hibernation enabled, you might not be able to connect to the instance\.
+ You can't change the instance type or size of an instance when hibernation is enabled\.
+ You can't hibernate an instance that is in an Auto Scaling group or used by Amazon ECS\. If your instance is in an Auto Scaling group and you try to hibernate it, the Amazon EC2 Auto Scaling service marks the stopped instance as unhealthy, and might terminate it and launch a replacement instance\. For more information, see [Health Checks for Auto Scaling Instances](https://docs.aws.amazon.com/autoscaling/latest/userguide/healthcheck.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ You can't hibernate an instance that is configured to boot in UEFI mode\.
+ If you hibernate an instance that was launched into a Capacity Reservation, the Capacity Reservation does not ensure that the hibernated instance can resume after you try to start it\.
+ We do not support keeping an instance hibernated for more than 60 days\. To keep the instance for longer than 60 days, you must start the hibernated instance, stop the instance, and start it\.
+ We constantly update our platform with upgrades and security patches, which can conflict with existing hibernated instances\. We notify you about critical updates that require a start for hibernated instances so that we can perform a shutdown or a reboot to apply the necessary upgrades and security patches\.
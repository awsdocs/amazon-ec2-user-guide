# Use your Reserved Instances<a name="using-reserved-instances"></a>

Reserved Instances are automatically applied to running On\-Demand Instances provided that the specifications match\. If you have no running On\-Demand Instances that match the specifications of your Reserved Instance, the Reserved Instance is unused until you launch an instance with the required specifications\. 

If you're launching an instance to take advantage of the billing benefit of a Reserved Instance, ensure that you specify the following information during launch:
+ Platform: You must choose an Amazon Machine Image \(AMI\) that matches the platform \(product description\) of your Reserved Instance\. For example, if you specified `Linux/UNIX`, you can launch an instance from an Amazon Linux AMI or an Ubuntu AMI\.
+ Instance type: Specify the same instance type as your Reserved Instance; for example, `t2.large`\.
+ Availability Zone: If you purchased a zonal Reserved Instance for a specific Availability Zone, you must launch the instance into the same Availability Zone\. If you purchased a regional Reserved Instance, you can launch your instance into any Availability Zone\.
+ Tenancy: The tenancy of your instance must match the tenancy of the Reserved Instance; for example, `dedicated` or `shared`\. For more information, see [Dedicated Instances](dedicated-instance.md)\.

For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\. For examples of how Reserved Instances are applied to your running instances, see [How Reserved Instances are applied](apply_ri.md)\.

You can use Amazon EC2 Auto Scaling or other AWS services to launch the On\-Demand Instances that use your Reserved Instance benefits\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/WhatIsAutoScaling.html)\.
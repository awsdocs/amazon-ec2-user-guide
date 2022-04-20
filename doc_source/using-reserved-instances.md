# Use your Reserved Instances<a name="using-reserved-instances"></a>

Reserved Instances are automatically applied to running On\-Demand Instances provided that the specifications match\. If you have no running On\-Demand Instances that match the specifications of your Reserved Instance, the Reserved Instance is unused until you launch an instance with the required specifications\. 

If you're launching an On\-Demand Instance to take advantage of the billing benefit of a Reserved Instance, ensure that you specify the following information when you configure your On\-Demand Instance:

**Platform**  
You must specify an Amazon Machine Image \(AMI\) that matches the platform \(product description\) of your Reserved Instance\. For example, if you specified `Linux/UNIX` for your Reserved Instance, you can launch an instance from an Amazon Linux AMI or an Ubuntu AMI\.

**Instance type**  
If you purchased a zonal Reserved Instance, you must specify the same instance type as your Reserved Instance; for example, `t3.large`\. For more information, see [How zonal Reserved Instances are applied](apply_ri.md#apply-zonal-ri)\.  
If you purchased a regional Reserved Instance, you must specify an instance type from the same instance family as the instance type of your Reserved Instance\. For example, if you specified `t3.xlarge` for your Reserved Instance, you must launch your instance from the T3 family, but you can specify any size, for example, `t3.medium`\. For more information, see [How regional Reserved Instances are applied](apply_ri.md#apply-regional-ri)\.

**Availability Zone**  
If you purchased a zonal Reserved Instance for a specific Availability Zone, you must launch the instance into the same Availability Zone\.  
If you purchased a regional Reserved Instance, you can launch the instance into any Availability Zone in the Region that you specified for the Reserved Instance\.

**Tenancy**  
The tenancy \(`dedicated` or `shared`\) of the instance must match the tenancy of your Reserved Instance\. For more information, see [Dedicated Instances](dedicated-instance.md)\.

For examples of how Reserved Instances are applied to your running On\-Demand Instances, see [How Reserved Instances are applied](apply_ri.md)\. For more information, see [Why aren't my Amazon EC2 Reserved Instances applying to my AWS billing in the way that I expected?](http://aws.amazon.com/premiumsupport/knowledge-center/reserved-instance-applying-why/)

You can use various methods to launch the On\-Demand Instances that use your Reserved Instance discount\. For information about the different launch methods, see [Launch your instance](LaunchingAndUsingInstances.md)\. You can also use Amazon EC2 Auto Scaling to launch an instance\. For more information, see the [Amazon EC2 Auto Scaling User Guide](https://docs.aws.amazon.com/autoscaling/latest/userguide/what-is-amazon-ec2-auto-scaling.html)\.
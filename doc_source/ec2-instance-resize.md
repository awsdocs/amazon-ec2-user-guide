# Change the instance type<a name="ec2-instance-resize"></a>

As your needs change, you might find that your instance is over\-utilized \(the instance type is too small\) or under\-utilized \(the instance type is too large\)\. If this is the case, you can resize your instance by changing its instance type\. For example, if your `t2.micro` instance is too small for its workload, you can change it to another instance type that is appropriate for the workload\.

You might also want to migrate from a previous generation instance type to a current generation instance type to take advantage of some features; for example, support for IPv6\.

If you want a recommendation for an instance type that is best able to handle your existing workload, you can use AWS Compute Optimizer\. For more information, see [Get recommendations for an instance type](ec2-instance-recommendations.md)\.

**Topics**
+ [Requirements for changing the instance type](resize-requirements.md)
+ [Compatibility for resizing instances](resize-limitations.md)
+ [Resize an Amazon EBS–backed instance](resize-ebs-backed-instance.md)
+ [Migrate an instance store\-backed instance](resize-instance-store-backed-instance.md)
+ [Migrate to a new instance configuration](migrate-instance-configuration.md)
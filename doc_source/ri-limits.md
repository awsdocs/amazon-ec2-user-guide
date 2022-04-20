# Reserved Instance quotas<a name="ri-limits"></a>

There is a limit to the number of Reserved Instances that you can purchase per month, as follows:
+ For each Region, you can purchase 20 [regional](apply_ri.md#apply-regional-ri) Reserved Instances per month\.
+ Plus, for each Availability Zone, you can purchase an additional 20 [zonal](apply_ri.md#apply-zonal-ri) Reserved Instances per month\.

For example, in a Region with three Availability Zones, the limit is 80 Reserved Instances per month: 20 regional Reserved Instances for the Region plus 20 zonal Reserved Instances for each of the three Availability Zones \(20\*3=60\)\.

A regional Reserved Instance applies a discount to a running On\-Demand Instance\. The default On\-Demand Instance limit is 20\. You cannot exceed your running On\-Demand Instance limit by purchasing regional Reserved Instances\. For example, if you already have 20 running On\-Demand Instances, and you purchase 20 regional Reserved Instances, the 20 regional Reserved Instances are used to apply a discount to the 20 running On\-Demand Instances\. If you purchase more regional Reserved Instances, you will not be able to launch more instances because you have reached your On\-Demand Instance limit\.

Before purchasing regional Reserved Instances, make sure your On\-Demand Instance limit matches or exceeds the number of regional Reserved Instances you intend to own\. If required, make sure you request an increase to your On\-Demand Instance limit *before* purchasing more regional Reserved Instances\.

A zonal Reserved Instance—a Reserved Instance that is purchased for a specific Availability Zone— provides capacity reservation as well as a discount\. You *can exceed* your running On\-Demand Instance limit by purchasing zonal Reserved Instances\. For example, if you already have 20 running On\-Demand Instances, and you purchase 20 zonal Reserved Instances, you can launch a further 20 On\-Demand Instances that match the specifications of your zonal Reserved Instances, giving you a total of 40 running instances\.

The Amazon EC2 console provides quota information\. For more information, see [View your current limits](ec2-resource-limits.md#view-limits)\.
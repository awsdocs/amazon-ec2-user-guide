# Instance purchasing options<a name="instance-purchasing-options"></a>

Amazon EC2 provides the following purchasing options to enable you to optimize your costs based on your needs:
+ **On\-Demand Instances** – Pay, by the second, for the instances that you launch\.
+ **Savings Plans** – Reduce your Amazon EC2 costs by making a commitment to a consistent amount of usage, in USD per hour, for a term of 1 or 3 years\.
+ **Reserved Instances** – Reduce your Amazon EC2 costs by making a commitment to a consistent instance configuration, including instance type and Region, for a term of 1 or 3 years\.
+ **Scheduled Instances** – Purchase instances that are always available on the specified recurring schedule, for a one\-year term\.
+ **Spot Instances** – Request unused EC2 instances, which can reduce your Amazon EC2 costs significantly\.
+ **Dedicated Hosts** – Pay for a physical host that is fully dedicated to running your instances, and bring your existing per\-socket, per\-core, or per\-VM software licenses to reduce costs\.
+ **Dedicated Instances** – Pay, by the hour, for instances that run on single\-tenant hardware\.
+ **Capacity Reservations** – Reserve capacity for your EC2 instances in a specific Availability Zone for any duration\.

If you require a capacity reservation, purchase Reserved Instances or Capacity Reservations for a specific Availability Zone, or purchase Scheduled Instances\. Spot Instances are a cost\-effective choice if you can be flexible about when your applications run and if they can be interrupted\. Dedicated Hosts or Dedicated Instances can help you address compliance requirements and reduce costs by using your existing server\-bound software licenses\. For more information, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/)\.

For more information about Savings Plans, see the [AWS Savings Plans User Guide](https://docs.aws.amazon.com/savingsplans/latest/userguide/)\.

**Topics**
+ [Determining the instance lifecycle](#check-instance-lifecycle)
+ [On\-Demand Instances](ec2-on-demand-instances.md)
+ [Reserved Instances](ec2-reserved-instances.md)
+ [Scheduled Reserved Instances](ec2-scheduled-instances.md)
+ [Spot Instances](using-spot-instances.md)
+ [Dedicated Hosts](dedicated-hosts-overview.md)
+ [Dedicated Instances](dedicated-instance.md)
+ [On\-Demand Capacity Reservations](ec2-capacity-reservations.md)

## Determining the instance lifecycle<a name="check-instance-lifecycle"></a>

The lifecycle of an instance starts when it is launched and ends when it is terminated\. The purchasing option that you choose affects the lifecycle of the instance\. For example, an On\-Demand Instance runs when you launch it and ends when you terminate it\. A Spot Instance runs as long as capacity is available and your maximum price is higher than the Spot price\. You can launch a Scheduled Instance during its scheduled time period; Amazon EC2 launches the instances and then terminates them three minutes before the time period ends\.

Use the following procedure to determine the lifecycle of an instance\.

------
#### [ New console ]

**To determine the instance lifecycle using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Details** tab, under **Instance details**, find **Lifecycle**\. If the value is `spot`, the instance is a Spot Instance\. If the value is `scheduled`, the instance is a Scheduled Reserved Instance\. If the value is `normal`, the instance is either an On\-Demand Instance or a Reserved Instance\.

1. On the **Details** tab, under **Host and placement group**, find **Tenancy**\. If the value is `host`, the instance is running on a Dedicated Host\. If the value is `dedicated`, the instance is a Dedicated Instance\.

1. \(Optional\) If you have purchased a Reserved Instance and want to verify that it is being applied, you can check the usage reports for Amazon EC2\. For more information, see [Amazon EC2 usage reports](usage-reports.md)\.

------
#### [ Old console ]

**To determine the instance lifecycle using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the instance\.

1. On the **Description** tab, find **Tenancy**\. If the value is `host`, the instance is running on a Dedicated Host\. If the value is `dedicated`, the instance is a Dedicated Instance\.

1. On the **Description** tab, find **Lifecycle**\. If the value is `spot`, the instance is a Spot Instance\. If the value is `scheduled`, the instance is a Scheduled Instance\. If the value is `normal`, the instance is either an On\-Demand Instance or a Reserved Instance\.

1. \(Optional\) If you have purchased a Reserved Instance and want to verify that it is being applied, you can check the usage reports for Amazon EC2\. For more information, see [Amazon EC2 usage reports](usage-reports.md)\.

------

**To determine the instance lifecycle using the AWS CLI**  
Use the following [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command:

```
aws ec2 describe-instances --instance-ids i-1234567890abcdef0
```

If the instance is running on a Dedicated Host, the output contains the following information:

```
"Tenancy": "host"
```

If the instance is a Dedicated Instance, the output contains the following information:

```
"Tenancy": "dedicated"
```

If the instance is a Spot Instance, the output contains the following information:

```
"InstanceLifecycle": "spot"
```

If the instance is a Scheduled Instance, the output contains the following information:

```
"InstanceLifecycle": "scheduled"
```

Otherwise, the output does not contain `InstanceLifecycle`\.
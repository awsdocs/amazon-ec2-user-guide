# On\-Demand Instances<a name="ec2-on-demand-instances"></a>

An On\-Demand Instance is an instance that you use *on demand*\. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it\.

There is no long\-term commitment required when you purchase On\-Demand Instances\. You pay only for the seconds that your On\-Demand Instances are in the `running` state\. The price per second for a running On\-Demand Instance is fixed, and is listed on the [On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand) page\.

We recommend that you use On\-Demand Instances for applications with short\-term, irregular workloads that cannot be interrupted\.

For significant savings over On\-Demand Instances, use [AWS Savings Plans](http://aws.amazon.com/savingsplans/), [Spot Instances](using-spot-instances.md), or [Reserved Instances](ec2-reserved-instances.md)\.

## Working With On\-Demand Instances<a name="working-with-on-demand-instances"></a>

You can work with On\-Demand Instances in the following ways:
+ [Launch Your Instance](LaunchingAndUsingInstances.md)
+ [Connect to Your Linux Instance](AccessingInstances.md)
+ [Stop and Start Your Instance](Stop_Start.md)
+ [Hibernate Your Linux Instance](Hibernate.md)
+ [Reboot Your Instance](ec2-instance-reboot.md)
+ [Instance Retirement](instance-retirement.md)
+ [Terminate Your Instance](terminating-instances.md)
+ [Recover Your Instance](ec2-instance-recover.md)
+ [Configuring Your Amazon Linux Instance](Configure_Instance.md)
+ [Identify EC2 Linux Instances](identify_ec2_instances.md)

If you're new to Amazon EC2, see [How to Get Started with Amazon EC2](concepts.md#how-to-get-started)\.

## On\-Demand Instance Limits<a name="ec2-on-demand-instances-limits"></a>

There is a limit on the number of running On\-Demand Instances per AWS account per Region\. On\-Demand Instance limits are managed in terms of the *number of virtual central processing units \(vCPUs\)* that your running On\-Demand Instances are using, regardless of the instance type\.

**Note**  
On\-Demand Instance count\-based limits, managed in terms of the number of instances per instance type, are no longer available\. For more information, see [EC2 On\-Demand Instance limits](http://aws.amazon.com/ec2/faqs/#EC2_On-Demand_Instance_limits)\.

There are five On\-Demand Instance limits, listed in the following table\. Each limit specifies the vCPU limit for one or more instance families\. For information about the different instance families, generations, and sizes, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\.


| On\-Demand Instance limit name | Default vCPU limit | 
| --- | --- | 
|  Running On\-Demand Standard \(A, C, D, H, I, M, R, T, and Z\) instances  |  1152 vCPUs  | 
|  Running On\-Demand F instances  |  128 vCPUs  | 
|  Running On\-Demand G instances  |  128 vCPUs  | 
|  Running On\-Demand P instances  |  128 vCPUs  | 
|  Running On\-Demand X instances  |  128 vCPUs  | 

**Note**  
New AWS accounts may start with limits that are lower than the limits described here\.

With vCPU limits, you can use your limit in terms of the number of vCPUs required to launch any combination of instance types that meet your changing application needs\. For example, with a Standard instance limit of 256 vCPUs, you could launch 32 `m5.2xlarge` instances \(32 x 8 vCPUs\) or 16 `c5.4xlarge` instances \(16 x 16 vCPUs\), or a combination of any Standard instance types and sizes that total 256 vCPUs\. For more information, see [EC2 On\-Demand Instance limits](http://aws.amazon.com/ec2/faqs/#EC2_On-Demand_Instance_limits)\.

### Calculating How Many vCPUs You Need<a name="vcpu-limits-calculator"></a>

You can use the vCPU limits calculator to determine the number of vCPUs that you require for your application needs\.

The following screenshot shows the vCPU limits calculator\.

![\[The vCPU limit calculator in the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vCPU-limit-calculator.png)

You can view and use the following controls and information:
+ **Instance type** – The instance types that you add to the vCPU limits calculator\.
+ **Instance count** – The number of instances that you require for the selected instance type\.
+ **vCPU count** – The number of vCPUs that correspond to the **Instance count**\.
+ **Current limit** – Your current limit for the limit type to which the instance type belongs\. The limit applies to all instance types of the same limit type\. For example, in the preceding screenshot, the current limit for `m5.2xlarge` and `c5.4xlarge` is 1,920 vCPUs, which is the limit for all the instance types that belong to the All Standard instances limit\.
+ **New limit** – The new limit, in number of vCPUs, that is required to launch the number of instances that you specified\.
+ **X** – Choose the **X** to remove the row\.
+ **Add instance type** – Choose **Add instance type** to add another instance type to the calculator\.
+ **Limits calculation** – Displays the current limit, vCPUs needed, and new limit for the limit types\.
  + **Instance limit name** – The limit type for the instance types that you selected\.
  + **Current limit** – The current limit for the limit type\.
  + **vCPUs needed** – The number of vCPUs that correspond to the number of instances that you specified\. For the All Standard instances limit type, the vCPUs needed is calculated by adding the values for **vCPU count** for all the instance types of this limit type\.
  + **New limit** – The new limit is calculated by adding the values for **Current limit** and **vCPUs needed**\.
  + **Options** – Choose **Request limit increase** to request a limit increase for the corresponding limit type\.

**To calculate the number of required vCPUs**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\. 

1. From the navigation bar, select a Region\.

1. From the left navigator, choose **Limits**\.

1. Choose **Calculate vCPU limit**\.

1. Choose **Add instance type**, choose the required instance type, and specify the required number of instances\. To add more instance types, choose **Add instance type** again\.

1. View **Limits calculation** for the required new limit\.

1. When you've finished using the calculator, choose **Close**\.

### Requesting a Limit Increase<a name="vcpu-limits-request-increase"></a>

You can request a limit increase for each On\-Demand Instance limit type from the [Limits page](https://console.aws.amazon.com/ec2/#Limits) or the vCPU limits calculator in the Amazon EC2 console\. Complete the required fields on the AWS Support Center [limit increase form](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances) with your use case\. For **Primary Instance Type**, select the limit type that corresponds to the **Instance limit name** in the vCPU limits calculator\. For the new limit value, use the value that appears in the **New limit** column in the vCPU limits calculator\. For more information about requesting a limit increase, see [Amazon EC2 Service Limits](ec2-resource-limits.md)\.

### Monitoring On\-Demand Instance Limits and Usage<a name="monitoring-on-demand-limits"></a>

You can view and manage your On\-Demand Instance limits from the [Limits page](https://console.aws.amazon.com/ec2/#Limits) in the Amazon EC2 console, from the Amazon EC2 [Services quotas page](https://console.aws.amazon.com/servicequotas/#!/services/ec2/quotas) in the Service Quotas console, and from the [Service Limits page](https://console.aws.amazon.com/trustedadvisor/home?#/category/service-limits) in the AWS Trusted Advisor console\. For more information, see [Amazon EC2 Service Limits](ec2-resource-limits.md) in the *Amazon EC2 User Guide for Linux Instances*, [Viewing a Service Quota](https://docs.aws.amazon.com/servicequotas/latest/userguide/gs-request-quota.html) in the *Service Quotas User Guide*, and [AWS Trusted Advisor](http://aws.amazon.com/premiumsupport/technology/trusted-advisor/)\.

With Amazon CloudWatch metrics integration, you can monitor EC2 usage against limits\. You can also configure alarms to warn about approaching limits\. For more information, see [Using Amazon CloudWatch Alarms](https://docs.aws.amazon.com/servicequotas/latest/userguide/configure-cloudwatch.html) in the *Service Quotas User Guide*\.
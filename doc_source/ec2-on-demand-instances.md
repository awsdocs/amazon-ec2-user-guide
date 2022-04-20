# On\-Demand Instances<a name="ec2-on-demand-instances"></a>

With On\-Demand Instances, you pay for compute capacity by the second with no long\-term commitments\. You have full control over its lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it\.

There is no long\-term commitment required when you purchase On\-Demand Instances\. You pay only for the seconds that your On\-Demand Instances are in the `running` state, with a 60\-second minimum\. The price per second for a running On\-Demand Instance is fixed, and is listed on the [Amazon EC2 Pricing, On\-Demand Pricing page](http://aws.amazon.com/ec2/pricing/on-demand/)\.

We recommend that you use On\-Demand Instances for applications with short\-term, irregular workloads that cannot be interrupted\.

For significant savings over On\-Demand Instances, use [AWS Savings Plans](http://aws.amazon.com/savingsplans/), [Spot Instances](using-spot-instances.md), or [Reserved Instances](ec2-reserved-instances.md)\.

**Contents**
+ [Work with On\-Demand Instances](#working-with-on-demand-instances)
+ [On\-Demand Instance limits](#ec2-on-demand-instances-limits)
  + [Monitor On\-Demand Instance limits and usage](#monitoring-on-demand-limits)
  + [Calculate how many vCPUs you need](#vcpu-limits-calculator)
  + [Request a limit increase](#vcpu-limits-request-increase)
+ [Query the prices of On\-Demand Instances](#query-aws-price-list)

## Work with On\-Demand Instances<a name="working-with-on-demand-instances"></a>

You can work with On\-Demand Instances in the following ways:
+ [Launch your instance](LaunchingAndUsingInstances.md)
+ [Connect to your Linux instance](AccessingInstances.md)
+ [Stop and start your instance](Stop_Start.md)
+ [Hibernate your On\-Demand Linux instance](Hibernate.md)
+ [Reboot your instance](ec2-instance-reboot.md)
+ [Instance retirement](instance-retirement.md)
+ [Terminate your instance](terminating-instances.md)
+ [Recover your instance](ec2-instance-recover.md)
+ [Configure your Amazon Linux instance](Configure_Instance.md)
+ [Identify EC2 Linux instances](identify_ec2_instances.md)

If you're new to Amazon EC2, see [How to get started with Amazon EC2](concepts.md#how-to-get-started)\.

## On\-Demand Instance limits<a name="ec2-on-demand-instances-limits"></a>

There is a limit on the number of running On\-Demand Instances per AWS account per Region\. On\-Demand Instance limits are managed in terms of the *number of virtual central processing units \(vCPUs\)* that your running On\-Demand Instances are using, regardless of the instance type\.

There are eight On\-Demand Instance limits:
+ Running On\-Demand Standard \(A, C, D, H, I, M, R, T, Z\) instances
+ Running On\-Demand DL instances
+ Running On\-Demand F instances
+ Running On\-Demand G and VT instances
+ Running On\-Demand High Memory instances
+ Running On\-Demand Inf instances
+ Running On\-Demand P instances
+ Running On\-Demand X instances

Each limit specifies the vCPU limit for one or more instance families\. For information about the different instance families, generations, and sizes, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\.

You can launch any combination of instance types that meet your changing application needs, as long as the number of vCPUs does not exceed your account limit\. For example, with a Standard instance limit of 256 vCPUs, you could launch 32 `m5.2xlarge` instances \(32 x 8 vCPUs\) or 16 `c5.4xlarge` instances \(16 x 16 vCPUs\)\. For more information, see [EC2 On\-Demand Instance limits](http://aws.amazon.com/ec2/faqs/#EC2_On-Demand_Instance_limits)\.

**Topics**
+ [Monitor On\-Demand Instance limits and usage](#monitoring-on-demand-limits)
+ [Calculate how many vCPUs you need](#vcpu-limits-calculator)
+ [Request a limit increase](#vcpu-limits-request-increase)

### Monitor On\-Demand Instance limits and usage<a name="monitoring-on-demand-limits"></a>

You can view and manage your On\-Demand Instance limits using the following:
+ The [Limits page](https://console.aws.amazon.com/ec2/#Limits) in the Amazon EC2 console
+ The Amazon EC2 [Services quotas page](https://console.aws.amazon.com/servicequotas/#!/services/ec2/quotas) in the Service Quotas console
+ The [get\-service\-quota](https://docs.aws.amazon.com/cli/latest/reference/service-quotas/get-service-quota.html) AWS CLI
+ The [Service limits page](https://console.aws.amazon.com/trustedadvisor/home?#/category/service-limits) in the AWS Trusted Advisor console

For more information, see [Amazon EC2 service quotas](ec2-resource-limits.md) in the *Amazon EC2 User Guide*, [Viewing service quotas](https://docs.aws.amazon.com/servicequotas/latest/userguide/gs-request-quota.html) in the *Service Quotas User Guide*, and [AWS Trusted Advisor](http://aws.amazon.com/premiumsupport/technology/trusted-advisor/)\.

With Amazon CloudWatch metrics integration, you can monitor EC2 usage against limits\. You can also configure alarms to warn about approaching limits\. For more information, see [Service Quotas and Amazon CloudWatch alarms](https://docs.aws.amazon.com/servicequotas/latest/userguide/configure-cloudwatch.html) in the *Service Quotas User Guide*\.

### Calculate how many vCPUs you need<a name="vcpu-limits-calculator"></a>

You can use the vCPU limits calculator to determine the number of vCPUs that you require for your application needs\.

When using the calculator, keep the following in mind: The calculator assumes that you have reached your current limit\. The value that you enter for **Instance count** is the number of instances that you need to launch *in addition* to what is permitted by your current limit\. The calculator adds your current limit to the **Instance count** to arrive at a new limit\.

The following screenshot shows the vCPU limits calculator\.

![\[The vCPU limit calculator in the Amazon EC2 console.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/vCPU-limit-calculator.png)

You can view and use the following controls and information:
+ **Instance type** – The instance types that you add to the vCPU limits calculator\.
+ **Instance count** – The number of instances that you require for the selected instance type\.
+ **vCPU count** – The number of vCPUs that corresponds to the **Instance count**\.
+ **Current limit** – Your current limit for the limit type to which the instance type belongs\. The limit applies to all instance types of the same limit type\. For example, in the preceding screenshot, the current limit for `m5.2xlarge` and `c5.4xlarge` is 2,016 vCPUs, which is the limit for all the instance types that belong to the All Standard instances limit\.
+ **New limit** – The new limit, in number of vCPUs, which is calculated by adding **vCPU count** and **Current limit**\.
+ **X** – Choose the **X** to remove the row\.
+ **Add instance type** – Choose **Add instance type** to add another instance type to the calculator\.
+ **Limits calculation** – Displays the current limit, vCPUs needed, and new limit for the limit types\.
  + **Instance limit name** – The limit type for the instance types that you selected\.
  + **Current limit** – The current limit for the limit type\.
  + **vCPUs needed** – The number of vCPUs that corresponds to the number of instances that you specified in **Instance count**\. For the All Standard instances limit type, the vCPUs needed is calculated by adding the values for **vCPU count** for all the instance types of this limit type\.
  + **New limit** – The new limit is calculated by adding **Current limit** and **vCPUs needed**\.
  + **Options** – Choose **Request limit increase** to request a limit increase for the corresponding limit type\.

**To calculate the number of required vCPUs**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a Region\.

1. From the left navigator, choose **Limits**\.

1. Choose **Calculate vCPU limit**\.

1. Choose **Add instance type**, choose the required instance type, and specify the required number of instances\. To add more instance types, choose **Add instance type** again\.

1. View **Limits calculation** for the required new limit\.

1. When you've finished using the calculator, you can choose **Request on\-demand limit increase** or **Close**\.

### Request a limit increase<a name="vcpu-limits-request-increase"></a>

Even though Amazon EC2 automatically increases your On\-Demand Instance limits based on your usage, you can request a limit increase if necessary\. For example, if you intend to launch more instances than your current limit allows, you can request a limit increase\.

**To request an On\-Demand Instance limit increase**

1. Open the **Create case**, **Service limit increase** form in the Support Center console at [https://console\.aws\.amazon\.com/support/home\#/case/create](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-spot-instances)\.

   Alternatively, use one of the following:
   + From the **Limits Calculator**, choose one or more instance types and specify the number of instances, and then choose **Request on\-demand limit increase**\.
   + On the **Limits** page, choose a limit, and then choose **Request limit increase**\.

1. For **Limit type**, choose **EC2 Instances**\.

1. For **Region**, select the required Region\.

1. For **Primary instance type**, select the On\-Demand Instance limit for which you want to request a limit increase\.

1. For **New limit value**, enter the total number of vCPUs that you want to run concurrently\. To determine the total number of vCPUs that you need, use the value that appears in the **New limit** column in the vCPU limits calculator, or see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/) to find the number of vCPUs of each instance type\.

1. \(Conditional\) You must create a separate limit request for each On\-Demand Instance limit\. To request an increase for another On\-Demand Instance limit, choose **Add another request** and repeat steps 3 through 5 in this procedure\.

1. For **Use case description**, enter your use case, and then choose **Submit**\.

For more information about requesting a limit increase, see [Amazon EC2 service quotas](ec2-resource-limits.md)\.

## Query the prices of On\-Demand Instances<a name="query-aws-price-list"></a>

You can use the Price List Service API or the AWS Price List API to query the prices of On\-Demand Instances\. For more information, see [Using the AWS Price List API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html) in the *AWS Billing User Guide*\.
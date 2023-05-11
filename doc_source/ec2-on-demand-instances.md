# On\-Demand Instances<a name="ec2-on-demand-instances"></a>

With On\-Demand Instances, you pay for compute capacity by the second with no long\-term commitments\. You have full control over the instance's lifecycle—you decide when to launch, stop, hibernate, start, reboot, or terminate it\.

There is no long\-term commitment required when you purchase On\-Demand Instances\. You pay only for the seconds that your On\-Demand Instances are in the `running` state, with a 60\-second minimum\. The price per second for a running On\-Demand Instance is fixed, and is listed on the [Amazon EC2 Pricing, On\-Demand Pricing page](http://aws.amazon.com/ec2/pricing/on-demand/)\.

We recommend that you use On\-Demand Instances for applications with short\-term, irregular workloads that cannot be interrupted\.

For significant savings over On\-Demand Instances, use [AWS Savings Plans](http://aws.amazon.com/savingsplans/), [Spot Instances](using-spot-instances.md), or [Reserved Instances](ec2-reserved-instances.md)\.

**Contents**
+ [Work with On\-Demand Instances](#working-with-on-demand-instances)
+ [On\-Demand Instance quotas](#ec2-on-demand-instances-limits)
  + [Monitor On\-Demand Instance quotas and usage](#monitoring-on-demand-limits)
  + [Request a quota increase](#vcpu-limits-request-increase)
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

## On\-Demand Instance quotas<a name="ec2-on-demand-instances-limits"></a>

There are quotas for the number of running On\-Demand Instances per AWS account per Region\. On\-Demand Instance quotas are managed in terms of the *number of virtual central processing units \(vCPUs\)* that your running On\-Demand Instances are using, regardless of the instance type\.

We provide the following quota types for On\-Demand Instances:
+ Running On\-Demand DL instances
+ Running On\-Demand F instances
+ Running On\-Demand G and VT instances
+ Running On\-Demand High Memory instances
+ Running On\-Demand HPC instances
+ Running On\-Demand Inf instances
+ Running On\-Demand P instances
+ Running On\-Demand Standard \(A, C, D, H, I, M, R, T, Z\) instances
+ Running On\-Demand Trn instances
+ Running On\-Demand X instances

Each quota type specifies the maximum number of vCPUs for one or more instance families\. For information about the different instance families, generations, and sizes, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\.

You can launch any combination of instance types that meet your changing application needs, as long as the number of vCPUs does not exceed your account quota\. For example, with a Standard instance quota of 256 vCPUs, you could launch 32 `m5.2xlarge` instances \(32 x 8 vCPUs\) or 16 `c5.4xlarge` instances \(16 x 16 vCPUs\)\. For more information, see [EC2 On\-Demand Instance limits](http://aws.amazon.com/ec2/faqs/#EC2_On-Demand_Instance_limits)\.

**Topics**
+ [Monitor On\-Demand Instance quotas and usage](#monitoring-on-demand-limits)
+ [Request a quota increase](#vcpu-limits-request-increase)

### Monitor On\-Demand Instance quotas and usage<a name="monitoring-on-demand-limits"></a>

You can view and manage your On\-Demand Instance quotas for each Region using the following methods\.

**To view your current quotas using the Service Quotas console**

1. Open the Service Quotas console at [https://console\.aws\.amazon\.com/servicequotas/home/services/ec2/quotas/](https://console.aws.amazon.com/servicequotas/home/services/ec2/quotas/)\.

1. From the navigation bar, select a Region\.

1. In the filter field, enter **On\-Demand**\.

1. The **Applied quota value** column displays the maximum number of vCPUs for each On\-Demand Instance quota type for your account\.

**To view your current quotas using the AWS Trusted Advisor console**  
Open [Service limits page](https://console.aws.amazon.com/trustedadvisor/home?#/category/service-limits) in the AWS Trusted Advisor console\.

**To configure CloudWatch alarms**  
With Amazon CloudWatch metrics integration, you can monitor your EC2 usage against your quotas\. You can also configure alarms to warn about approaching quotas\. For more information, see [Service Quotas and Amazon CloudWatch alarms](https://docs.aws.amazon.com/servicequotas/latest/userguide/configure-cloudwatch.html) in the *Service Quotas User Guide*\.

### Request a quota increase<a name="vcpu-limits-request-increase"></a>

Even though Amazon EC2 automatically increases your On\-Demand Instance quotas based on your usage, you can request a quota increase if necessary\. For example, if you intend to launch more instances than your current quota allows, you can request a quota increase by using the Service Quotas console described in [Amazon EC2 service quotas](ec2-resource-limits.md)\.

## Query the prices of On\-Demand Instances<a name="query-aws-price-list"></a>

You can use the Price List Service API or the AWS Price List API to query the prices of On\-Demand Instances\. For more information, see [Using the AWS Price List API](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/price-changes.html) in the *AWS Billing User Guide*\.
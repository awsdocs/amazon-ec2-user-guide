# Spot Instance quotas<a name="using-spot-limits"></a>

There are quotas for the number of running and requested Spot Instances per AWS account per Region\. Spot Instance quotas are managed in terms of the *number of virtual central processing units \(vCPUs\)* that your running Spot Instances are either using or will use pending the fulfillment of open Spot Instance requests\. If you terminate your Spot Instances but do not cancel the Spot Instance requests, the requests count against your Spot Instance vCPU quota until Amazon EC2 detects the Spot Instance terminations and closes the requests\.

We provide the following quota types for Spot Instances:
+ All DL Spot Instance Requests
+ All F Spot Instance Requests
+ All G and VT Spot Instance Requests
+ All Inf Spot Instance Requests
+ All P Spot Instance Requests
+ All Standard \(A, C, D, H, I, M, R, T, Z\) Spot Instance Requests
+ All Trn Spot Instance Requests
+ All X Spot Instance Requests

Each quota type specifies the maximum number of vCPUs for one or more instance families\. For information about the different instance families, generations, and sizes, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\.

You can launch any combination of instance types that meet your changing application needs\. For example, with an All Standard Spot Instance Requests quota of 256 vCPUs, you could request 32 `m5.2xlarge` Spot Instances \(32 x 8 vCPUs\) or 16 `c5.4xlarge` Spot Instances \(16 x 16 vCPUs\)\. 

**Topics**
+ [Monitor Spot Instance quotas and usage](#monitoring-spot-limits)
+ [Request a quota increase](#spot-vcpu-limits-request-increase)

## Monitor Spot Instance quotas and usage<a name="monitoring-spot-limits"></a>

You can view and manage your Spot Instance quotas using the following:
+ The Amazon EC2 [Services quotas page](https://console.aws.amazon.com/servicequotas/#!/services/ec2/quotas) in the Service Quotas console
+ The [get\-service\-quota](https://docs.aws.amazon.com/cli/latest/reference/service-quotas/get-service-quota.html) AWS CLI

For more information, see [Amazon EC2 service quotas](ec2-resource-limits.md) in the *Amazon EC2 User Guide for Linux Instances* and [Viewing service quotas](https://docs.aws.amazon.com/servicequotas/latest/userguide/gs-request-quota.html) in the *Service Quotas User Guide*\.

With Amazon CloudWatch metrics integration, you can monitor EC2 usage against your quotas\. You can also configure alarms to warn about approaching quotas\. For more information, see [Service Quotas and Amazon CloudWatch alarms](https://docs.aws.amazon.com/servicequotas/latest/userguide/configure-cloudwatch.html) in the *Service Quotas User Guide*\.

## Request a quota increase<a name="spot-vcpu-limits-request-increase"></a>

Even though Amazon EC2 automatically increases your Spot Instance quotas based on your usage, you can request a quota increase if necessary\. For example, if you intend to launch more Spot Instances than your current quota allows, you can request a quota increase\. You can also request a quota increase if you submit a Spot Instance request and you receive the error `Max spot instance count exceeded`\. To request a quota increase, use the Service Quotas console described in [Amazon EC2 service quotas](ec2-resource-limits.md)\.
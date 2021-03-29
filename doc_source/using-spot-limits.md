# Spot Instance limits<a name="using-spot-limits"></a>

There is a limit on the number of running and requested Spot Instances per AWS account per Region\. Spot Instance limits are managed in terms of the *number of virtual central processing units \(vCPUs\)* that your running Spot Instances are either using or will use pending the fulfillment of open Spot Instance requests\. If you terminate your Spot Instances but do not cancel the Spot Instance requests, the requests count against your Spot Instance vCPU limit until Amazon EC2 detects the Spot Instance terminations and closes the requests\.

**Topics**
+ [Spot Instance limits](#spot-limits-general)
+ [Request a Spot Instance limit increase](#spot-vcpu-limits-request-increase)
+ [Monitor Spot Instance limits and usage](#monitoring-spot-limits)
+ [Spot Fleet limits](#spot-fleet-limitations)

## Spot Instance limits<a name="spot-limits-general"></a>

There are six Spot Instance limits, listed in the following table\. Each limit specifies the vCPU limit for one or more instance families\. For information about the different instance families, generations, and sizes, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/)\.


| Spot Instance limit name | Default vCPU limit | 
| --- | --- | 
|  All Standard \(A, C, D, H, I, M, R, T, Z\) Spot Instance Requests  |  1440 vCPUs  | 
|  All F Spot Instance Requests  |  11 vCPUs  | 
|  All G Spot Instance Requests  |  11 vCPUs  | 
|  All Inf Spot Instance Requests  | 64 vCPUs | 
|  All P Spot Instance Requests  |  16 vCPUs  | 
|  All X Spot Instance Requests  |  21 vCPUs  | 

**Note**  
New AWS accounts might start with limits that are lower than the limits described here\. These limits can increase over time\.

With vCPU limits, you can use your limit in terms of the number of vCPUs that are required to launch any combination of instance types that meet your changing application needs\. For example, with an All Standard Spot Instance Requests limit of 256 vCPUs, you could request 32 `m5.2xlarge` Spot Instances \(32 x 8 vCPUs\) or 16 `c5.4xlarge` Spot Instances \(16 x 16 vCPUs\), or a combination of any Standard Spot Instance types and sizes that total 256 vCPUs\. 

## Request a Spot Instance limit increase<a name="spot-vcpu-limits-request-increase"></a>

Even though Amazon EC2 automatically increases your Spot Instance limits based on your usage, you can request a limit increase if necessary\. For example, if you intend to launch more Spot Instances than your current limit allows, you can request a limit increase\. You can also request a limit increase if you submit a Spot Instance request and you receive the error `Max spot instance count exceeded`\.

**To request a Spot Instance limit increase**

1. Open the **Create case**, **Service limit increase** form in the Support Center console at [https://console\.aws\.amazon\.com/support/home\#/case/create](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-spot-instances)\.

1. For **Limit type**, choose **EC2 Spot Instances**\.

1. For **Region**, select the required Region\.

1. For **Primary instance type**, select the Spot Instance limit for which you want to request a limit increase\.

1. For **New limit value**, enter the total number of vCPUs that you want to run concurrently\. To determine the total number of vCPUs that you need, see [Amazon EC2 Instance Types](http://aws.amazon.com/ec2/instance-types/) to find the number of vCPUs of each instance type\. 

1. \(Conditional\) You must create a separate limit request for each Spot Instance limit\. To request an increase for another Spot Instance limit, choose **Add another request** and repeat steps 4 and 5 in this procedure\.

1. For **Use case description**, enter your use case, and then choose **Submit**\.

For more information about viewing limits and requesting a limit increase, see [Amazon EC2 service quotas](ec2-resource-limits.md)\.

## Monitor Spot Instance limits and usage<a name="monitoring-spot-limits"></a>

You can view and manage your Spot Instance limits using the following:
+ The [Limits page](https://console.aws.amazon.com/ec2/#Limits) in the Amazon EC2 console
+ The Amazon EC2 [Services quotas page](https://console.aws.amazon.com/servicequotas/#!/services/ec2/quotas) in the Service Quotas console
+ The [get\-service\-quota](https://docs.aws.amazon.com/cli/latest/reference/service-quotas/get-service-quota.html) AWS CLI

For more information, see [Amazon EC2 service quotas](ec2-resource-limits.md) in the *Amazon EC2 User Guide for Linux Instances* and [Viewing a Service Quota](https://docs.aws.amazon.com/servicequotas/latest/userguide/gs-request-quota.html) in the *Service Quotas User Guide*\.  

With Amazon CloudWatch metrics integration, you can monitor EC2 usage against limits\. You can also configure alarms to warn about approaching limits\. For more information, see [Using Amazon CloudWatch Alarms](https://docs.aws.amazon.com/servicequotas/latest/userguide/configure-cloudwatch.html) in the *Service Quotas User Guide*\.

## Spot Fleet limits<a name="spot-fleet-limitations"></a>

The usual Amazon EC2 limits apply to instances launched by a Spot Fleet or an EC2 Fleet, such as Spot Instance limits and volume limits\. In addition, the following limits apply:
+ The number of active Spot Fleets and EC2 Fleets per Region: 1,000\*
+ The number of Spot capacity pools \(unique combination of instance type and subnet\): 300\*
+ The size of the user data in a launch specification: 16 KB\*
+ The target capacity per Spot Fleet or EC2 Fleet: 10,000
+ The target capacity across all Spot Fleets and EC2 Fleets in a Region: 100,000
+ A Spot Fleet request or an EC2 Fleet request can't span Regions\.
+ A Spot Fleet request or an EC2 Fleet request can't span different subnets from the same Availability Zone\.

If you need more than the default limits for target capacity, complete the AWS Support Center [Create case](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-fleet) form to request a limit increase\. For **Limit type**, choose **EC2 Fleet**, choose a Region, and then choose **Target Fleet Capacity per Fleet \(in units\)** or **Target Fleet Capacity per Region \(in units\)**, or both\.

\* These are hard limits\. You cannot request a limit increase for these limits\.
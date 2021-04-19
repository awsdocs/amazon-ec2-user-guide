# Spot Instances<a name="using-spot-instances"></a>

A Spot Instance is an unused EC2 instance that is available for less than the On\-Demand price\. Because Spot Instances enable you to request unused EC2 instances at steep discounts, you can lower your Amazon EC2 costs significantly\. The hourly price for a Spot Instance is called a Spot price\. The Spot price of each instance type in each Availability Zone is set by Amazon EC2, and is adjusted gradually based on the long\-term supply of and demand for Spot Instances\. Your Spot Instance runs whenever capacity is available and the maximum price per hour for your request exceeds the Spot price\.

Spot Instances are a cost\-effective choice if you can be flexible about when your applications run and if your applications can be interrupted\. For example, Spot Instances are well\-suited for data analysis, batch jobs, background processing, and optional tasks\. For more information, see [Amazon EC2 Spot Instances](https://aws.amazon.com/ec2/spot-instances/)\.

**Topics**
+ [Concepts](#spot-features)
+ [How to get started](#spot-get-started)
+ [Related services](#spot-related-services)
+ [Pricing and savings](#spot-pricing)

## Concepts<a name="spot-features"></a>

Before you get started with Spot Instances, you should be familiar with the following concepts:
+ *Spot capacity pool* – A set of unused EC2 instances with the same instance type \(for example, `m5.large`\) and Availability Zone\.
+ *Spot price* – The current price of a Spot Instance per hour\.
+ *Spot Instance request* – Requests a Spot Instance\. The request provides the maximum price per hour that you are willing to pay for a Spot Instance\. If you don't specify a maximum price, the default maximum price is the On\-Demand price\. When the maximum price per hour for your request exceeds the Spot price, Amazon EC2 fulfills your request if capacity is available\. A Spot Instance request is either *one\-time* or *persistent*\. Amazon EC2 automatically resubmits a persistent Spot Instance request after the Spot Instance associated with the request is terminated\.
+ *EC2 instance rebalance recommendation* \- Amazon EC2 emits an instance rebalance recommendation signal to notify you that a Spot Instance is at an elevated risk of interruption\. This signal gives you the opportunity to proactively rebalance your workloads across existing or new Spot Instances without having to wait for the two\-minute Spot Instance interruption notice\.
+ *Spot Instance interruption* – Amazon EC2 terminates, stops, or hibernates your Spot Instance when capacity is no longer available or the Spot price exceeds the maximum price for your request\. Amazon EC2 provides a Spot Instance interruption notice, which gives the instance a two\-minute warning before it is interrupted\.

### Key differences between Spot Instances and On\-Demand Instances<a name="key-differences-spot-on-demand"></a>

The following table lists the key differences between Spot Instances and On\-Demand Instances\.


|  | Spot Instances | On\-Demand Instances | 
| --- | --- | --- | 
|  Launch time  |  Can only be launched immediately if the Spot request is active and capacity is available\.  |  Can only be launched immediately if you make a manual launch request and capacity is available\.  | 
|  Available capacity  |  If capacity is not available, the Spot request continues to automatically make the launch request until capacity becomes available\.  |  If capacity is not available when you make a launch request, you get an insufficient capacity error \(ICE\)\.  | 
|  Hourly price  |  The hourly price for Spot Instances varies based on demand\.  |  The hourly price for On\-Demand Instances is static\.  | 
| Rebalance recommendation | The signal that Amazon EC2 emits for a running Spot Instance when the instance is at an elevated risk of interruption\. | You determine when an On\-Demand Instance is interrupted \(stopped, hibernated, or terminated\)\. | 
|  Instance interruption  |  You can stop and start an Amazon EBS\-backed Spot Instance\. In addition, the Amazon EC2 Spot service can [interrupt](spot-interruptions.md) an individual Spot Instance if capacity is no longer available, the Spot price exceeds your maximum price, or demand for Spot Instances increases\.   |  You determine when an On\-Demand Instance is interrupted \(stopped, hibernated, or terminated\)\.  | 

### Strategies for using Spot Instances<a name="spot-strategy"></a>

One strategy is to maintain a minimum level of guaranteed compute resources for your applications by launching a core group of On\-Demand Instances, and supplementing them with Spot Instances when the opportunity arises\.

![\[Compare On-Demand and Spot Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_introduction.png)

Another strategy is to launch Spot Instances with a specified duration \(also known as Spot blocks\), which are designed not to be interrupted and will run continuously for the duration you select\. In rare situations, Spot blocks may be interrupted due to Amazon EC2 capacity needs\. In these cases, we provide a two\-minute warning before we terminate an instance, and you are not charged for the terminated instances even if you used them\. For more information, see [Define a duration for your Spot Instances](spot-requests.md#fixed-duration-spot-instances)\.

## How to get started<a name="spot-get-started"></a>

The first thing you need to do is get set up to use Amazon EC2\. It can also be helpful to have experience launching On\-Demand Instances before launching Spot Instances\.

**Get up and running**
+ [Set up to use Amazon EC2](get-set-up-for-amazon-ec2.md)
+ [Tutorial: Get started with Amazon EC2 Linux instances](EC2_GetStarted.md)

**Spot basics**
+ [How Spot Instances work](how-spot-instances-work.md) 

**Working with Spot Instances**
+ [Prepare for interruptions](spot-interruptions.md#using-spot-instances-managing-interruptions)
+ [Create a Spot Instance request](spot-requests.md#using-spot-instances-request)
+ [Get request status information](spot-request-status.md#get-spot-instance-request-status)

## Related services<a name="spot-related-services"></a>

You can provision Spot Instances directly using Amazon EC2\. You can also provision Spot Instances using other services in AWS\. For more information, see the following documentation\.

Amazon EC2 Auto Scaling and Spot Instances  
You can create launch templates or configurations with the maximum price that you are willing to pay, so that Amazon EC2 Auto Scaling can launch Spot Instances\. For more information, see [Requesting Spot Instances for fault\-tolerant and flexible applications](https://docs.aws.amazon.com/autoscaling/latest/userguide/asg-launch-spot-instances.html) and [Auto Scaling groups with multiple instance types and purchase options](https://docs.aws.amazon.com/autoscaling/latest/userguide/asg-purchase-options.html) in the *Amazon EC2 Auto Scaling User Guide*\.

Amazon EMR and Spot Instances  
There are scenarios where it can be useful to run Spot Instances in an Amazon EMR cluster\. For more information, see [Spot Instances](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-purchasing-options.html#emr-spot-instances) and [When Should You Use Spot Instances](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-instances-guidelines.html#emr-plan-spot-instances) in the *Amazon EMR Management Guide*\.

AWS CloudFormation templates  
AWS CloudFormation enables you to create and manage a collection of AWS resources using a template in JSON format\. AWS CloudFormation templates can include the maximum price you are willing to pay\. For more information, see [EC2 Spot Instance Updates \- Auto Scaling and CloudFormation Integration](https://aws.amazon.com/blogs/aws/ec2-spot-instance-updates-auto-scaling-and-cloudformation-integration-new-sample-app-1/)\.

AWS SDK for Java  
You can use the Java programming language to manage your Spot Instances\. For more information, see [Tutorial: Amazon EC2 Spot Instances](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/tutorial-spot-instances-java.html) and [Tutorial: Advanced Amazon EC2 Spot Request Management](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/tutorial-spot-adv-java.html)\.

AWS SDK for \.NET  
You can use the \.NET programming environment to manage your Spot Instances\. For more information, see [Tutorial: Amazon EC2 Spot Instances](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/tutorial-spot-instances-net.html)\.

## Pricing and savings<a name="spot-pricing"></a>

You pay the Spot price for Spot Instances, which is set by Amazon EC2 and adjusted gradually based on the long\-term supply of and demand for Spot Instances\. If the maximum price for your request exceeds the current Spot price, Amazon EC2 fulfills your request if capacity is available\. Your Spot Instances run until you terminate them, capacity is no longer available, the Spot price exceeds your maximum price, or your Amazon EC2 Auto Scaling group terminates them during [scale in](https://docs.aws.amazon.com/autoscaling/latest/userguide/AutoScalingGroupLifecycle.html#as-lifecycle-scale-in)\.

Spot Instances with a predefined duration use a fixed hourly price that remains in effect for the Spot Instance while it runs\.

If you or Amazon EC2 interrupts a running Spot Instance, you are charged for the seconds used or the full hour, or you receive no charge, depending on the operating system used and who interrupted the Spot Instance\. For more information, see [Billing for interrupted Spot Instances](spot-interruptions.md#billing-for-interrupted-spot-instances)\.

### View prices<a name="spot-pricing-view-prices"></a>

To view the current \(updated every five minutes\) lowest Spot price per AWS Region and instance type, see the [Amazon EC2 Spot Instances Pricing](https://aws.amazon.com/ec2/spot/pricing/) page\.

To view the Spot price history for the past three months, use the Amazon EC2 console or the [describe\-spot\-price\-history](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-price-history.html) command \(AWS CLI\)\. For more information, see [Spot Instance pricing history](using-spot-instances-history.md)\.

We independently map Availability Zones to codes for each AWS account\. Therefore, you can get different results for the same Availability Zone code \(for example, `us-west-2a`\) between different accounts\.

### View savings<a name="spot-pricing-view-savings"></a>

You can view the savings made from using Spot Instances for a single Spot Fleet or for all Spot Instances\. You can view the savings made in the last hour or the last three days, and you can view the average cost per vCPU hour and per memory \(GiB\) hour\. Savings are estimated and may differ from actual savings because they do not include the billing adjustments for your usage\. For more information about viewing savings information, see [Savings from purchasing Spot Instances](spot-savings.md)\.

### View billing<a name="spot-pricing-view-billing"></a>

Your bill provides details about your service usage\. For more information, see [Viewing your bill](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/getting-viewing-bill.html) in the *AWS Billing and Cost Management User Guide*\.
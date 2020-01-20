# Spot Instances<a name="using-spot-instances"></a>

A Spot Instance is an unused EC2 instance that is available for less than the On\-Demand price\. Because Spot Instances enable you to request unused EC2 instances at steep discounts, you can lower your Amazon EC2 costs significantly\. The hourly price for a Spot Instance is called a Spot price\. The Spot price of each instance type in each Availability Zone is set by Amazon EC2, and adjusted gradually based on the long\-term supply of and demand for Spot Instances\. Your Spot Instance runs whenever capacity is available and the maximum price per hour for your request exceeds the Spot price\.

Spot Instances are a cost\-effective choice if you can be flexible about when your applications run and if your applications can be interrupted\. For example, Spot Instances are well\-suited for data analysis, batch jobs, background processing, and optional tasks\. For more information, see [Amazon EC2 Spot Instances](https://aws.amazon.com/ec2/purchasing-options/spot-instances/)\.

**Topics**
+ [Concepts](#spot-features)
+ [How to Get Started](#spot-get-started)
+ [Related Services](#spot-related-services)
+ [Pricing and Savings](#spot-pricing)

## Concepts<a name="spot-features"></a>

Before you get started with Spot Instances, you should be familiar with the following concepts:
+ *Spot Instance pool* – A set of unused EC2 instances with the same instance type \(for example, `m5.large`\), operating system, Availability Zone, and network platform\.
+ *Spot price* – The current price of a Spot Instance per hour\.
+ *Spot Instance request* – Provides the maximum price per hour that you are willing to pay for a Spot Instance\. If you don't specify a maximum price, the default maximum price is the On\-Demand price\. When the maximum price per hour for your request exceeds the Spot price, Amazon EC2 fulfills your request if capacity is available\. A Spot Instance request is either *one\-time* or *persistent*\. Amazon EC2 automatically resubmits a persistent Spot request after the Spot Instance associated with the request is terminated\. Your Spot Instance request can optionally specify a duration for the Spot Instances\.
+ *Spot Fleet* – A set of Spot Instances that is launched based on criteria that you specify\. The Spot Fleet selects the Spot Instance pools that meet your needs and launches Spot Instances to meet the target capacity for the fleet\. By default, Spot Fleets are set to *maintain* target capacity by launching replacement instances after Spot Instances in the fleet are terminated\. You can submit a Spot Fleet as a one\-time *request*, which does not persist after the instances have been terminated\. You can include On\-Demand Instance requests in a Spot Fleet request\.
+ *Spot Instance interruption* – Amazon EC2 terminates, stops, or hibernates your Spot Instance when the Spot price exceeds the maximum price for your request or capacity is no longer available\. Amazon EC2 provides a Spot Instance interruption notice, which gives the instance a two\-minute warning before it is interrupted\.

### Key Differences between Spot Instances and On\-Demand Instances<a name="key-differences-spot-on-demand"></a>

The following table lists the key differences between Spot Instances and On\-Demand Instances\.


|  | Spot Instances | On\-Demand Instances | 
| --- | --- | --- | 
|  Launch time  |  Can only be launched immediately if the Spot Request is active and capacity is available\.  |  Can only be launched immediately if you make a manual launch request and capacity is available\.  | 
|  Available capacity  |  If capacity is not available, the Spot Request continues to automatically make the launch request until capacity becomes available\.  |  If capacity is not available when you make a launch request, you get an insufficient capacity error \(ICE\)\.  | 
|  Hourly price  |  The hourly price for Spot Instances varies based on demand\.  |  The hourly price for On\-Demand Instances is static\.  | 
|  Instance interruption  |  You can stop and start an Amazon EBS\-backed Spot Instance\. In addition, the Amazon EC2 Spot service can [interrupt](spot-interruptions.md) an individual Spot Instance if capacity is no longer available, the Spot price exceeds your maximum price, or demand for Spot Instances increases\.   |  You determine when an On\-Demand Instance is interrupted \(stopped, hibernated, or terminated\)\.  | 

### Strategies for Using Spot Instances<a name="spot-strategy"></a>

One strategy to maintain a minimum level of guaranteed compute resources for your applications is to launch a core group of On\-Demand Instances, and supplement them with Spot Instances when the opportunity arises\.

![\[Compare On-Demand and Spot Instances\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/spot_introduction.png)

Another strategy is to launch Spot Instances with a specified duration \(also known as Spot blocks\), which are designed not to be interrupted and will run continuously for the duration you select\. In rare situations, Spot blocks may be interrupted due to Amazon EC2 capacity needs\. In these cases, we provide a two\-minute warning before we terminate an instance, and you are not charged for the terminated instances even if you used them\. For more information, see [Defining a Duration for Your Spot Instances](spot-requests.md#fixed-duration-spot-instances)\.

## How to Get Started<a name="spot-get-started"></a>

The first thing you need to do is get set up to use Amazon EC2\. It can also be helpful to have experience launching On\-Demand Instances before launching Spot Instances\.

**Get up and running**
+ [Setting Up with Amazon EC2](get-set-up-for-amazon-ec2.md)
+ [Getting Started with Amazon EC2 Linux Instances](EC2_GetStarted.md)

**Spot basics**
+ [How Spot Instances Work](how-spot-instances-work.md) 
+ [How Spot Fleet Works](spot-fleet.md)

**Working with Spot Instances**
+ [Preparing for Interruptions](spot-interruptions.md#using-spot-instances-managing-interruptions)
+ [Creating a Spot Instance Request](spot-requests.md#using-spot-instances-request)
+ [Getting Request Status Information](spot-bid-status.md#get-spot-instance-bid-status)

**Working with Spot Fleets**
+ [Spot Fleet Prerequisites](spot-fleet-requests.md#spot-fleet-prerequisites)
+ [Creating a Spot Fleet Request](spot-fleet-requests.md#create-spot-fleet)

## Related Services<a name="spot-related-services"></a>

You can provision Spot Instances directly using Amazon EC2\. You can also provision Spot Instances using other services in AWS\. For more information, see the following documentation\.

Amazon EC2 Auto Scaling and Spot Instances  
You can create launch templates or configurations with the maximum price that you are willing to pay, so that Amazon EC2 Auto Scaling can launch Spot Instances\. For more information, see [Launching Spot Instances in Your Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/latest/userguide/asg-launch-spot-instances.html) and [Using Multiple Instance Types and Purchase Options](https://docs.aws.amazon.com/autoscaling/latest/userguide/AutoScalingGroup.html#asg-purchase-options) in the *Amazon EC2 Auto Scaling User Guide*\.

Amazon EMR and Spot Instances  
There are scenarios where it can be useful to run Spot Instances in an Amazon EMR cluster\. For more information, see [Spot Instances](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-instance-purchasing-options.html#emr-spot-instances) and [When Should You Use Spot Instances](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-plan-instances-guidelines.html#emr-plan-spot-instances) in the *Amazon EMR Management Guide*\.

AWS CloudFormation Templates  
AWS CloudFormation enables you to create and manage a collection of AWS resources using a template in JSON format\. AWS CloudFormation templates can include the maximum price you are willing to pay\. For more information, see [EC2 Spot Instance Updates \- Auto Scaling and CloudFormation Integration](https://aws.amazon.com/blogs/aws/ec2-spot-instance-updates-auto-scaling-and-cloudformation-integration-new-sample-app-1/)\.

AWS SDK for Java  
You can use the Java programming language to manage your Spot Instances\. For more information, see [Tutorial: Amazon EC2 Spot Instances](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/tutorial-spot-instances-java.html) and [Tutorial: Advanced Amazon EC2 Spot Request Management](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/tutorial-spot-adv-java.html)\.

AWS SDK for \.NET  
You can use the \.NET programming environment to manage your Spot Instances\. For more information, see [Tutorial: Amazon EC2 Spot Instances](https://docs.aws.amazon.com/sdk-for-net/latest/developer-guide/tutorial-spot-instances-net.html)\.

## Pricing and Savings<a name="spot-pricing"></a>

You pay the Spot price for Spot Instances, which is set by Amazon EC2 and adjusted gradually based on the long\-term supply of and demand for Spot Instances\. If the maximum price for your request exceeds the current Spot price, Amazon EC2 fulfills your request if capacity is available\. Your Spot Instances run until you terminate them, capacity is no longer available, the Spot price exceeds your maximum price, or your Amazon EC2 Auto Scaling group terminates them during [scale in](https://docs.aws.amazon.com/autoscaling/latest/userguide/AutoScalingGroupLifecycle.html#as-lifecycle-scale-in)\.

Spot Instances with a predefined duration use a fixed hourly price that remains in effect for the Spot Instance while it runs\.

If you or Amazon EC2 interrupts a running Spot Instance, you are charged for the seconds used or the full hour, or you receive no charge, depending on the operating system used and who interrupted the Spot Instance\. For more information, see [Billing for Interrupted Spot Instances](spot-interruptions.md#billing-for-interrupted-spot-instances)\.

### View Prices<a name="spot-pricing-view-prices"></a>

To view the current \(updated every five minutes\) lowest Spot price per AWS Region and instance type, see the [Spot Instances Pricing](https://aws.amazon.com/ec2/spot/pricing/) page\.

To view the Spot price history for the past three months, use the Amazon EC2 console or the [describe\-spot\-price\-history](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-spot-price-history.html) command \(AWS CLI\)\. For more information, see [Spot Instance Pricing History](using-spot-instances-history.md)\.

We independently map Availability Zones to codes for each AWS account\. Therefore, you can get different results for the same Availability Zone code \(for example, `us-west-2a`\) between different accounts\.

### View Savings<a name="spot-pricing-view-savings"></a>

You can view the savings made from using Spot Instances for a single Spot Fleet or for all Spot Instances\. You can view the savings made in the last hour or the last three days, and you can view the average cost per vCPU hour and per memory \(GiB\) hour\. Savings are estimated and may differ from actual savings because they do not include the billing adjustments for your usage\. For more information about viewing savings information, see [Savings From Purchasing Spot Instances](spot-savings.md)\.

### View Billing<a name="spot-pricing-view-billing"></a>

To review your bill, go to your [AWS Account Activity page](https://aws.amazon.com/account-activity)\. Your bill contains links to usage reports that provide details about your bill\. For more information, see [AWS Account Billing](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/)\.

If you have questions concerning AWS billing, accounts, and events, [contact AWS Support](https://aws.amazon.com/contact-us/)\.
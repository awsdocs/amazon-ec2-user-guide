# Dedicated Hosts on AWS Outposts<a name="dh-outposts"></a>

AWS Outposts is a fully managed service that extends AWS infrastructure, services, APIs, and tools to your premises\. By providing local access to AWS managed infrastructure, AWS Outposts enables you to build and run applications on premises using the same programming interfaces as in AWS Regions, while using local compute and storage resources for lower latency and local data processing needs\.

An Outpost is a pool of AWS compute and storage capacity deployed at a customer site\. AWS operates, monitors, and manages this capacity as part of an AWS Region\. 

You can allocate Dedicated Hosts on Outposts that you own in your account\. This makes it easier for you to bring your existing software licenses and workloads that require a dedicated physical server to AWS Outposts\.

Dedicated Hosts allow you to use your eligible software licenses on Amazon EC2, so that you get the flexibility and cost effectiveness of using your own licenses\. Other software licenses that are bound to virtual machines, sockets, or physical cores, can also be used on Dedicated Hosts, subject to their license terms\. While Outposts have always been a single\-tenant environments that are eligible for BYOL workloads, Dedicated Hosts allows you to limit the needed licenses to a single host as opposed to the entire Outpost deployment\.

Additionally, using Dedicated Hosts on an Outpost gives you greater flexibility in instance type deployment, and more granular control over instance placement\. You can target a specific host for instance launches and use host affinity to ensure that the instance always runs on that host, or you can use auto\-placement to launch an instance onto any available host that has matching configurations and available capacity\.

**Contents**
+ [Prerequisites](#dh-outpost-prereqs)
+ [Supported features](#dh-outpost-features)
+ [Considerations](#dh-outpost-considerations)
+ [Allocate and use a Dedicated Host on an Outpost](#dh-outpost-allocate)

## Prerequisites<a name="dh-outpost-prereqs"></a>

You must have an Outpost installed at your site\. For more information, see [ Create an Outpost and order Outpost capacity](https://docs.aws.amazon.com/outposts/latest/userguide/order-outpost-capacity.html) in the *AWS Outposts User Guide*\.

## Supported features<a name="dh-outpost-features"></a>
+ The following instance families are supported: `C5`, `M5`, `R5`, `C5d`, `M5d`, `R5d`, `G4dn`, and `i3en`\.
+ Dedicated Hosts on Outposts can be configured to support multiple instance sizes\. Support for multiple instance sizes is available for the following instance families: `C5`, `M5`, `R5`, `C5d`, `M5d`, and `R5d`\. For more information, see [Dedicated Host instance capacity](dedicated-hosts-overview.md#dedicated-hosts-limits)\.
+ Dedicated Hosts on Outposts support auto\-placement and targeted instance launches\. For more information, see [Understand auto\-placement and affinity](how-dedicated-hosts-work.md#dedicated-hosts-understanding)\.
+ Dedicated Hosts on Outposts support host affinity\. For more information, see [Understand auto\-placement and affinity](how-dedicated-hosts-work.md#dedicated-hosts-understanding)\.
+ Dedicated Hosts on Outposts support sharing with AWS RAM\. For more information, see [Work with shared Dedicated Hosts](dh-sharing.md)\.

## Considerations<a name="dh-outpost-considerations"></a>
+ Dedicated Host Reservations are not supported on Outposts\.
+ Host resource groups and AWS License Manager are not supported on Outposts\.
+ Dedicated Hosts on Outposts do not support burstable T3 instances\.
+ Dedicated Hosts on Outposts do not support host recovery\.

## Allocate and use a Dedicated Host on an Outpost<a name="dh-outpost-allocate"></a>

You allocate and use Dedicated Hosts on Outposts in the same way that would with Dedicated Hosts in an AWS Region\.

**Prerequisites**  
Create a subnet on the Outpost\. For more information, see [Create a subnet](https://docs.aws.amazon.com/outposts/latest/userguide/launch-instance.html#create-subnet) in the *AWS Outposts User Guide*\.

**To allocate a Dedicated Host on an Outpost, use one of the following methods:**

------
#### [ AWS Outposts console ]

1. Open the AWS Outposts console at [https://console\.aws\.amazon\.com/outposts/](https://console.aws.amazon.com/outposts/home)\.

1. In the navigation pane, choose **Outposts**\. Select the Outpost and then choose **Actions**, **Allocate Dedicated Host**\.

1. Configure the Dedicated Host as needed\. For more information, see [Allocate Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-allocating)\.
**Note**  
**Availability Zone** and **Outpost ARN** should be pre\-populated with the Availability Zone and ARN of the selected Outpost\.

1. Choose **Allocate**\.

------
#### [ Amazon EC2 console ]

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**, and then choose **Allocate Dedicated Host**\.

1. For **Availability Zone**, select the Availability Zone associated with the Outpost\.

1. For **Outpost ARN**, enter the ARN of the Outpost\.

1. Configure the remaining Dedicated Host settings as needed\. For more information, see [Allocate Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-allocating)\.

1. Choose **Allocate**\.

------
#### [ AWS CLI ]

Use the [allocate\-hosts](https://docs.aws.amazon.com/cli/latest/reference/ec2/allocate-hosts.html) AWS CLI command\. For `--availability-zone`, specify the Availability Zone associated with the Outpost\. For `--outpost-arn`, specify the ARN of the Outpost\.

```
aws ec2 allocate-hosts --availability-zone "us-east-1a" --outpost-arn "arn:aws:outposts:us-east-1a:111122223333:outpost/op-4fe3dc21baEXAMPLE" --instance-family "m5" --auto-placement "off" --quantity 1 
```

------

**To launch an instance onto a Dedicated Host on an Outpost**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Dedicated Hosts**\. Select the Dedicated Host that you allocated in the previous step and choose **Actions**, **Launch instance onto host**\.

1. Configure the instance as needed and then launch the instance\. For more information, see [Launch instances onto a Dedicated Host](how-dedicated-hosts-work.md#launching-dedicated-hosts-instances)\.
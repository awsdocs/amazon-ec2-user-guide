# Dedicated Hosts<a name="dedicated-hosts-overview"></a>

An Amazon EC2 Dedicated Host is a physical server with EC2 instance capacity fully dedicated to your use\. Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses, including Windows Server, Microsoft SQL Server, SUSE, Linux Enterprise Server, and so on\.

**Topics**
+ [Differences between Dedicated Hosts and Dedicated Instances](#dedicated-hosts-dedicated-instances)
+ [Bring Your Own License](#dedicated-hosts-BYOL)
+ [Dedicated Host Instance Capacity](#dedicated-hosts-configurations)
+ [Dedicated Hosts Limitations and Restrictions](#dedicated-hosts-limitations)
+ [Pricing and Billing](#dedicated-hosts-billing)
+ [Working with Dedicated Hosts](how-dedicated-hosts-work.md)
+ [Tracking Configuration Changes](dedicated-hosts-aws-config.md)

## Differences between Dedicated Hosts and Dedicated Instances<a name="dedicated-hosts-dedicated-instances"></a>

Dedicated Hosts and Dedicated Instances can both be used to launch Amazon EC2 instances onto physical servers that are dedicated for your use\.

There are no performance, security, or physical differences between Dedicated Instances and instances on Dedicated Hosts\. The following table highlights some of the key differences between Dedicated Hosts and Dedicated Instances:


|  | Dedicated Host | Dedicated Instance | 
| --- | --- | --- | 
| Billing | Per\-host billing | Per\-instance billing | 
| Visibility of sockets, cores, and host ID | Provides visibility of the number of sockets and physical cores | No visibility | 
| Host and instance affinity | Allows you to consistently deploy your instances to the same physical server over time | Not supported | 
| Targeted instance placement | Provides additional visibility and control over how instances are placed on a physical server | Not supported | 
| Automatic instance recovery | Not supported | Supported | 
| Bring Your Own License \(BYOL\) | Supported | Not supported | 

## Bring Your Own License<a name="dedicated-hosts-BYOL"></a>

Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses\. When you bring your own license, you are responsible for managing your own licenses, but Amazon EC2 has features that help you maintain license compliance, such as instance affinity and targeted placement\.

These are the general steps to follow in order to bring your own volume licensed machine image into Amazon EC2\.

1. Verify that the license terms controlling the use of your machine images allow usage in a virtualized cloud environment\. 

1. After you have verified that your machine image can be used within Amazon EC2, import it using VM Import/Export\. For information about how to import your machine image, see the [ VM Import/Export User Guide](https://docs.aws.amazon.com/vm-import/latest/userguide/)\.

1. After you've imported your machine image, you can launch instances from it onto active Dedicated Hosts in your account\.

1. When you run these instances, depending on the operating system, you may be required to activate these instances against your own KMS server\.

**Note**  
To track how your images are used in AWS, enable host recording in AWS Config\. You can use AWS Config to record configuration changes to a Dedicated Host and use the output as a data source for license reporting\. For more information, see [Tracking Configuration Changes](dedicated-hosts-aws-config.md)\. 

## Dedicated Host Instance Capacity<a name="dedicated-hosts-configurations"></a>

Dedicated Hosts are configured to support a single instance type and size capacity\. The number of instances you can launch onto a Dedicated Host depends on the instance type that the Dedicated Host is configured to support\. For example, if you allocated a `c3.xlarge` Dedicated Host, you'd have the right to launch up to eight `c3.xlarge` instances on the Dedicated Host\. To determine the number of instance type sizes that you can run on a particular Dedicated Host, see [Amazon EC2 Dedicated Hosts Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/)\.

## Dedicated Hosts Limitations and Restrictions<a name="dedicated-hosts-limitations"></a>

Before you allocate Dedicated Hosts, take note of the following limitations and restrictions:
+ RHEL, SUSE Linux, and Windows AMIs \(whether offered by AWS or on the AWS Marketplace\) cannot be used with Dedicated Hosts\.
+ Amazon EC2 instance recovery is not supported\.
+ Up to two On\-Demand Dedicated Hosts per instance family, per region can be allocated\. It is possible to request a limit increase: [Request to Raise Allocation Limit on Amazon EC2 Dedicated Hosts](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=)\.
+ The instances that run on a Dedicated Host can only be launched in a VPC\.
+ Host limits are independent from instance limits\. Instances that you are running on Dedicated Hosts do not count towards your instance limits\.
+ Auto Scaling groups are not supported\.
+ Amazon RDS instances are not supported\.
+ The AWS Free Usage tier is not available for Dedicated Hosts\.
+ Instance placement control refers to managing instance launches onto Dedicated Hosts\. Placement groups are not supported for Dedicated Hosts\.

## Pricing and Billing<a name="dedicated-hosts-billing"></a>

### On\-Demand Dedicated Hosts<a name="on-demand-dedicated-hosts"></a>

On\-Demand billing is automatically activated when you allocate a Dedicated Host to your account\.

The On\-Demand price for a Dedicated Host varies by instance family and region\. You are charged an hourly rate for the Dedicated Host, regardless of the quantity or the size of instances that you choose to launch on it\. In other words, you are charged for the entire Dedicated Host, and not the individual instances that you choose to run on it\. For more information about On\-Demand pricing, see [Amazon EC2 Dedicated Hosts On\-Demand Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/#on-demand)\.

You can release an On\-Demand Dedicated Host at any time to stop accruing charges for it\. For information about releasing a Dedicated Host, see [Releasing Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-releasing)\.

### Dedicated Host Reservations<a name="dedicated-host-reservations"></a>

Dedicated Host Reservations provide a billing discount compared to running On\-Demand Dedicated Hosts\. Reservations are available in three payment options:
+ **No Upfront**—No Upfront Reservations provide you with a discount on your Dedicated Host usage over a term and do not require an upfront payment\. Available for a one\-year term only\.
+ **Partial Upfront**—A portion of the reservation must be paid upfront and the remaining hours in the term are billed at a discounted rate\. Available in one\-year and three\-year terms\.
+ **All Upfront**—Provides the lowest effective price\. Available in one\-year and three\-year terms and covers the entire cost of the term upfront, with no additional future charges\.

You must have active Dedicated Hosts in your account before you can purchase reservations\. Each reservation covers a single, specific Dedicated Host in your account\. Reservations are applied to the instance family on the host, not the instance size\. If you have three Dedicated Hosts with different instances sizes \(`m4.xlarge`, `m4.medium`, and `m4.large`\) you can associate a single `m4` reservation with all those Dedicated Hosts\. The instance family and region of the reservation must match that of the Dedicated Hosts you want to associate it with\. 

When a reservation is associated with a Dedicated Host, the Dedicated Host can't be released until the reservation's term is over\.

For more information about Reservation pricing, see [Amazon EC2 Dedicated Hosts Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/#reservations)\.
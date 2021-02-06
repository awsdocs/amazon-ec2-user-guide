# Dedicated Hosts<a name="dedicated-hosts-overview"></a>

An Amazon EC2 Dedicated Host is a physical server with EC2 instance capacity fully dedicated to your use\. Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses, including Windows Server, Microsoft SQL Server, SUSE, and Linux Enterprise Server\.

For information about the configurations supported on Dedicated Hosts, see the [Dedicated Hosts Configuration Table](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#host-configuration)\.

**Topics**
+ [Differences between Dedicated Hosts and Dedicated Instances](#dedicated-hosts-dedicated-instances)
+ [Bring your own license](#dedicated-hosts-BYOL)
+ [Dedicated Host instance capacity](#dedicated-hosts-limits)
+ [Dedicated Hosts restrictions](#dedicated-hosts-limitations)
+ [Pricing and billing](#dedicated-hosts-billing)
+ [Work with Dedicated Hosts](how-dedicated-hosts-work.md)
+ [Work with shared Dedicated Hosts](dh-sharing.md)
+ [Host recovery](dedicated-hosts-recovery.md)
+ [Track configuration changes](dedicated-hosts-aws-config.md)

## Differences between Dedicated Hosts and Dedicated Instances<a name="dedicated-hosts-dedicated-instances"></a>

Dedicated Hosts and Dedicated Instances can both be used to launch Amazon EC2 instances onto physical servers that are dedicated for your use\.

There are no performance, security, or physical differences between Dedicated Instances and instances on Dedicated Hosts\. However, there are some differences between the two\. The following table highlights some of the key differences between Dedicated Hosts and Dedicated Instances:


|  | Dedicated Host | Dedicated Instance | 
| --- | --- | --- | 
| Billing | Per\-host billing | Per\-instance billing | 
| Visibility of sockets, cores, and host ID | Provides visibility of the number of sockets and physical cores | No visibility | 
| Host and instance affinity | Allows you to consistently deploy your instances to the same physical server over time | Not supported | 
| Targeted instance placement | Provides additional visibility and control over how instances are placed on a physical server | Not supported | 
| Automatic instance recovery | Supported\. For more information, see [Host recovery](dedicated-hosts-recovery.md)\. | Supported | 
| Bring Your Own License \(BYOL\) | Supported | Not supported | 

## Bring your own license<a name="dedicated-hosts-BYOL"></a>

Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses\. When you bring your own license, you are responsible for managing your own licenses\. However, Amazon EC2 has features that help you maintain license compliance, such as instance affinity and targeted placement\.

These are the general steps to follow in order to bring your own volume licensed machine image into Amazon EC2\.

1. Verify that the license terms controlling the use of your machine images allow usage in a virtualized cloud environment\. 

1. After you have verified that your machine image can be used within Amazon EC2, import it using VM Import/Export\. For information about how to import your machine image, see the [ VM Import/Export User Guide](https://docs.aws.amazon.com/vm-import/latest/userguide/)\.

1. After you import your machine image, you can launch instances from it onto active Dedicated Hosts in your account\.

1. When you run these instances, depending on the operating system, you might be required to activate these instances against your own KMS server\.

**Note**  
To track how your images are used in AWS, enable host recording in AWS Config\. You can use AWS Config to record configuration changes to a Dedicated Host and use the output as a data source for license reporting\. For more information, see [Track configuration changes](dedicated-hosts-aws-config.md)\. 

## Dedicated Host instance capacity<a name="dedicated-hosts-limits"></a>

Support for multiple instance sizes on the same Dedicated Host is available for the following instance families: `c5`, `m5`, `r5`, `c5n`, `r5n`, and `m5n`\. Other instance families support only a single instance size on the same Dedicated Host\.

For example, when you allocate an `r5` Dedicated Host, it has 2 sockets and 48 physical cores on which you can run different instance sizes, such as `r5.2xlarge` and `r5.4xlarge`, up to the core capacity associated with the host\. However, for each instance family, there is a limit on the number of instances that can be run for each instance size\. For example, an `r5` Dedicated Host supports up to 2 `r5.8xlarge` instances, which uses 32 of the physical cores\. Additional `r5` instances of another size can then be used to fill the host to core capacity\. For the supported number of instance sizes for each instance family, see the [Dedicated Hosts Configuration Table](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#host-configuration)\.

The following table shows examples of different instance size combinations that you can run on a Dedicated Host\.


| Instance family | Example instance size combinations | 
| --- | --- | 
| R5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)  | 
| C5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)  | 
| M5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)  | 

For more information about the instance families and instance size configurations supported on Dedicated Hosts, see the [Dedicated Hosts Configuration Table](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#host-configuration)\.

## Dedicated Hosts restrictions<a name="dedicated-hosts-limitations"></a>

Before you allocate Dedicated Hosts, take note of the following limitations and restrictions:
+ To run RHEL, SUSE Linux, and SQL Server on Dedicated Hosts, you must bring your own AMIs\. RHEL, SUSE Linux, and SQL Server AMIs that are offered by AWS or that are available on AWS Marketplace can't be used with Dedicated Hosts\. For more information on how to create your own AMI, see [Bring your own license](#dedicated-hosts-BYOL)\.
+ Up to two On\-Demand Dedicated Hosts per instance family, per Region can be allocated\. It is possible to request a limit increase: [Request to Raise Allocation Limit on Amazon EC2 Dedicated Hosts](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=)\.
+ The instances that run on a Dedicated Host can only be launched in a VPC\.
+ Auto Scaling groups are supported when using a launch template that specifies a host resource group\. For more information, see [ Creating a Launch Template for an Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ Amazon RDS instances are not supported\.
+ The AWS Free Usage tier is not available for Dedicated Hosts\.
+ Instance placement control refers to managing instance launches onto Dedicated Hosts\. You cannot launch Dedicated Hosts into placement groups\.

## Pricing and billing<a name="dedicated-hosts-billing"></a>

The price for a Dedicated Host varies by payment option\.

**Topics**
+ [On\-Demand Dedicated Hosts](#on-demand-dedicated-hosts)
+ [Dedicated Host Reservations](#dedicated-host-reservations)
+ [Savings Plans](#dedicated-hosts-savings-plans)
+ [Pricing for Windows Server on Dedicated Hosts](#dh-win-billing)

### On\-Demand Dedicated Hosts<a name="on-demand-dedicated-hosts"></a>



On\-Demand billing is automatically activated when you allocate a Dedicated Host to your account\.

The On\-Demand price for a Dedicated Host varies by instance family and Region\. You pay per second \(with a minimum of 60 seconds\) for active Dedicated Host, regardless of the quantity or the size of instances that you choose to launch on it\. For more information about On\-Demand pricing, see [Amazon EC2 Dedicated Hosts On\-Demand Pricing](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#on-demand)\.



You can release an On\-Demand Dedicated Host at any time to stop accruing charges for it\. For information about releasing a Dedicated Host, see [Release Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-releasing)\.

### Dedicated Host Reservations<a name="dedicated-host-reservations"></a>

Dedicated Host Reservations provide a billing discount compared to running On\-Demand Dedicated Hosts\. Reservations are available in three payment options:
+ **No Upfront**—No Upfront Reservations provide you with a discount on your Dedicated Host usage over a term and do not require an upfront payment\. Available in one\-year and three\-year terms\. Only some instance families support the three\-year term for No Upfront Reservations\.
+ **Partial Upfront**—A portion of the reservation must be paid upfront and the remaining hours in the term are billed at a discounted rate\. Available in one\-year and three\-year terms\.
+ **All Upfront**—Provides the lowest effective price\. Available in one\-year and three\-year terms and covers the entire cost of the term upfront, with no additional future charges\.

You must have active Dedicated Hosts in your account before you can purchase reservations\. Each reservation can cover one or more hosts that support the same instance family in a single Availability Zone\. Reservations are applied to the instance family on the host, not the instance size\. If you have three Dedicated Hosts with different instances sizes \(`m4.xlarge`, `m4.medium`, and `m4.large`\) you can associate a single `m4` reservation with all those Dedicated Hosts\. The instance family and Availability Zone of the reservation must match that of the Dedicated Hosts you want to associate it with\. 

When a reservation is associated with a Dedicated Host, the Dedicated Host can't be released until the reservation's term is over\.

For more information about reservation pricing, see [Amazon EC2 Dedicated Hosts Pricing](https://aws.amazon.com/ec2/dedicated-hosts/pricing/#reservations)\.

### Savings Plans<a name="dedicated-hosts-savings-plans"></a>

Savings Plans are a flexible pricing model that offers significant savings over On\-Demand Instances\. With Savings Plans, you make a commitment to a consistent amount of usage, in USD per hour, for a term of one or three years\. This provides you with the flexibility to use the Dedicated Hosts that best meet your needs and continue to save money, instead of making a commitment to a specific Dedicated Host\. For more information, see the [AWS Savings Plans User Guide](https://docs.aws.amazon.com/savingsplans/latest/userguide/)\.

### Pricing for Windows Server on Dedicated Hosts<a name="dh-win-billing"></a>

Subject to Microsoft licensing terms, you can bring your existing Windows Server and SQL Server licenses to Dedicated Hosts\. There is no additional charge for software usage if you choose to bring your own licenses\.

In addition, you can also use Windows Server AMIs provided by Amazon to run the latest versions of Windows Server on Dedicated Hosts\. This is common for scenarios where you have existing SQL Server licenses eligible to run on Dedicated Hosts, but need Windows Server to run the SQL Server workload\. Windows Server AMIs provided by Amazon are supported on [ current generation instance types](instance-types.md#AvailableInstanceTypes) only\. For more information, see [ Amazon EC2 Dedicated Hosts Pricing](http://aws.amazon.com/ec2/dedicated-hosts/pricing#windows-dh)\.
# Dedicated Hosts<a name="dedicated-hosts-overview"></a>

An Amazon EC2 Dedicated Host is a physical server with EC2 instance capacity fully dedicated to your use\. Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses, including Windows Server, Microsoft SQL Server, SUSE, and Linux Enterprise Server\.

For information about the configurations supported on Dedicated Hosts, see [Dedicated Hosts Configuration](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#host-configuration)\.

**Topics**
+ [Differences between Dedicated Hosts and Dedicated Instances](#dedicated-hosts-dedicated-instances)
+ [Bring your own license](#dedicated-hosts-BYOL)
+ [Dedicated Host instance capacity](#dedicated-hosts-limits)
+ [Burstable T3 instances on Dedicated Hosts](#burstable-t3)
+ [Dedicated Hosts restrictions](#dedicated-hosts-limitations)
+ [Pricing and billing](#dedicated-hosts-billing)
+ [Work with Dedicated Hosts](how-dedicated-hosts-work.md)
+ [Work with shared Dedicated Hosts](dh-sharing.md)
+ [Dedicated Hosts on AWS Outposts](dh-outposts.md)
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
| Capacity Reservations | Not supported | Supported | 

## Bring your own license<a name="dedicated-hosts-BYOL"></a>

Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses\. When you bring your own license, you are responsible for managing your own licenses\. However, Amazon EC2 has features that help you maintain license compliance, such as instance affinity and targeted placement\.

These are the general steps to follow in order to bring your own volume licensed machine image into Amazon EC2\.

1. Verify that the license terms controlling the use of your machine images allow usage in a virtualized cloud environment\. 

1. After you have verified that your machine image can be used within Amazon EC2, import it using VM Import/Export\. For information about how to import your machine image, see the [VM Import/Export User Guide](https://docs.aws.amazon.com/vm-import/latest/userguide/)\.

1. After you import your machine image, you can launch instances from it onto active Dedicated Hosts in your account\.

1. When you run these instances, depending on the operating system, you might be required to activate these instances against your own KMS server\.

**Note**  
To track how your images are used in AWS, enable host recording in AWS Config\. You can use AWS Config to record configuration changes to a Dedicated Host and use the output as a data source for license reporting\. For more information, see [Track configuration changes](dedicated-hosts-aws-config.md)\. 

## Dedicated Host instance capacity<a name="dedicated-hosts-limits"></a>

Support for multiple instance sizes on the same Dedicated Host is available for the following instance families: `T3`, `A1`, `C5`, `M5`, `R5`, `C5n`, `R5n`, and `M5n`\. Other instance families support only a single instance size on the same Dedicated Host\.

For example, when you allocate an `R5` Dedicated Host, it has 2 sockets and 48 physical cores on which you can run different instance sizes, such as `r5.2xlarge` and `r5.4xlarge`, up to the core capacity associated with the host\. However, for each instance family, there is a limit on the number of instances that can be run for each instance size\. For example, an `R5` Dedicated Host supports up to 2 `r5.8xlarge` instances, which uses 32 of the physical cores\. Additional `R5` instances of another size can then be used to fill the host to core capacity\. For the supported number of instance sizes for each instance family, see [Dedicated Hosts Configuration](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#host-configuration)\.

**Note**  
For Dedicated Hosts that support multiple instance sizes, we recommend that you launch the larger instance sizes first, and then fill the remaining instance capacity with the smaller instance sizes as needed\.

The following table shows examples of different instance size combinations that you can run on a Dedicated Host\.


| Instance family | Example instance size combinations | 
| --- | --- | 
| R5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)  | 
| C5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)  | 
| M5 |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html)  | 

For more information about the instance families and instance size configurations supported on Dedicated Hosts, see the [Dedicated Hosts Configuration Table](http://aws.amazon.com/ec2/dedicated-hosts/pricing/#host-configuration)\.

## Burstable T3 instances on Dedicated Hosts<a name="burstable-t3"></a>

Dedicated Hosts support burstable performance T3 instances\. T3 instances provide a cost\-efficient way of using your eligible BYOL license software on dedicated hardware\. The smaller vCPU footprint of T3 instances enables you to consolidate your workloads on fewer hosts and maximize your per\-core license utilization\.

T3 Dedicated Hosts are best suited for running BYOL software with low to moderate CPU utilization\. This includes eligible per\-socket, per\-core, or per\-VM software licenses, such as Windows Server, Windows Desktop, SQL Server, SUSE Enterprise Linux Server, Red Hat Enterprise Linux, and Oracle Database\. Examples of workloads suited for T3 Dedicated Hosts are small and medium databases, virtual desktops, development and test environments, code repositories, and product prototypes\. T3 Dedicated Hosts are not recommended for workloads with sustained high CPU utilization or for workloads that experience correlated CPU bursts simultaneously\.

T3 instances on Dedicated Hosts use the same credit model as T3 instances on shared tenancy hardware\. However, they support the `standard` credit mode only; they do not support the `unlimited` credit mode\. In `standard` mode, T3 instances on Dedicated Hosts *earn*, *spend*, and *accrue* credits in the same way as burstable instances on shared tenancy hardware\. They provide a baseline CPU performance with the ability to burst above the baseline level\. To burst above the baseline, the instance spends credits that it has accrued in its CPU credit balance\. When the accrued credits are depleted, CPU utilization is lowered to the baseline level\. For more information about `standard` mode, see [How standard burstable performance instances work](burstable-performance-instances-standard-mode-concepts.md#how-burstable-performance-instances-standard-works)\.

T3 Dedicated Hosts support all of the features offered by Amazon EC2 Dedicated Hosts, including multiple instance sizes on a single host, Host resource groups, and BYOL\.

**Supported T3 instance sizes and configurations**  


T3 Dedicated Hosts run general purpose burstable T3 instances that share CPU resources of the host by providing a baseline CPU performance and the ability to burst to a higher level when needed\. This enables T3 Dedicated Hosts, which have 48 cores, to support up to a maximum of 192 instances per host\. In order to efficiently utilize the host’s resources and to provide the best instance performance, the Amazon EC2 instance placement algorithm automatically calculates the supported number of instances and instance size combinations that can be launched on the host\.

T3 Dedicated Hosts support multiple instance types on the same host\. All T3 instance sizes are supported on Dedicated Hosts\. You can run different combinations of T3 instances up to the CPU limit of the host\.

The following table lists the supported instance types, summarizes the performance of each instance type, and indicates the maximum number of instances of each size that can be launched\.


| Instance type | vCPUs | Memory \(GiB\) | Baseline CPU utilization per vCPU | Network burst bandwidth \(Gbps\) | Amazon EBS burst bandwidth \(Mbps\) | Max number of instances per Dedicated Host | 
| --- | --- | --- | --- | --- | --- | --- | 
| t3\.nano | 2 | 0\.5 | 5% | 5 | Up to 2,085 | 192 | 
| t3\.micro | 2 | 1 | 10% | 5 | Up to 2,085 | 192 | 
| t3\.small | 2 | 2 | 20% | 5 | Up to 2,085 | 192 | 
| t3\.medium | 2 | 4 | 20% | 5 | Up to 2,085 | 192 | 
| t3\.large | 2 | 8 | 30% | 5 | 2,780 | 96 | 
| t3\.xlarge | 4 | 16 | 40% | 5 | 2,780 | 48 | 
| t3\.2xlarge | 8 | 32 | 40% | 5 | 2,780 | 24 | 

**Monitor CPU utilization for T3 Dedicated Hosts**  
You can use the `DedicatedHostCPUUtilization` Amazon CloudWatch metric to monitor the vCPU utilization of a Dedicated Host\. The metric is available in the `EC2` namespace and `Per-Host-Metrics` dimension\. For more information, see [Dedicated Host metrics](viewing_metrics_with_cloudwatch.md#dh-metrics)\.

## Dedicated Hosts restrictions<a name="dedicated-hosts-limitations"></a>

Before you allocate Dedicated Hosts, take note of the following limitations and restrictions:
+ To run RHEL, SUSE Linux, and SQL Server on Dedicated Hosts, you must bring your own AMIs\. RHEL, SUSE Linux, and SQL Server AMIs that are offered by AWS or that are available on AWS Marketplace can't be used with Dedicated Hosts\. For more information on how to create your own AMI, see [Bring your own license](#dedicated-hosts-BYOL)\.

  This restriction does not apply to hosts allocated for high memory instances \(`u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, and `u-24tb1.metal`\)\. RHEL and SUSE Linux AMIs that are offered by AWS or that are available on AWS Marketplace can be used with these hosts\.
+ Up to two On\-Demand Dedicated Hosts per instance family, per Region can be allocated\. It is possible to request a limit increase: [Request to Raise Allocation Limit on Amazon EC2 Dedicated Hosts](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=)\.
+ The instances that run on a Dedicated Host can only be launched in a VPC\.
+ Auto Scaling groups are supported when using a launch template that specifies a host resource group\. For more information, see [ Creating a Launch Template for an Auto Scaling Group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/create-launch-template.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ Amazon RDS instances are not supported\.
+ The AWS Free Usage tier is not available for Dedicated Hosts\.
+ Instance placement control refers to managing instance launches onto Dedicated Hosts\. You cannot launch Dedicated Hosts into placement groups\.
+ If you allocate a host for a virtualized instance type, you can't modify the instance type to a `.metal` instance type after the host is allocated\. For example, if you allocate a host for the `m5.large` instance type, you can't modify the instance type to `m5.metal`\.

  Similarly, if you allocate a host for a `.metal` instance type, you can't modify the instance type to a virtualized instance type after the host is allocated\. For example, if you allocate a host for the `m5.metal` instance type, you can't modify the instance type to `m5.large`\.

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

**Note**  
Savings Plans are not supported with `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, `u-18tb1.metal`, and `u-24tb1.metal` Dedicated Hosts\.

### Pricing for Windows Server on Dedicated Hosts<a name="dh-win-billing"></a>

Subject to Microsoft licensing terms, you can bring your existing Windows Server and SQL Server licenses to Dedicated Hosts\. There is no additional charge for software usage if you choose to bring your own licenses\.

In addition, you can also use Windows Server AMIs provided by Amazon to run the latest versions of Windows Server on Dedicated Hosts\. This is common for scenarios where you have existing SQL Server licenses eligible to run on Dedicated Hosts, but need Windows Server to run the SQL Server workload\. Windows Server AMIs provided by Amazon are supported on [ current generation instance types](instance-types.md#AvailableInstanceTypes) only\. For more information, see [ Amazon EC2 Dedicated Hosts Pricing](http://aws.amazon.com/ec2/dedicated-hosts/pricing#windows-dh)\.
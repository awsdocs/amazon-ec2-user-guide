# Dedicated Hosts<a name="dedicated-hosts-overview"></a>

An Amazon EC2 Dedicated Host is a physical server with EC2 instance capacity fully dedicated to your use\. Dedicated Hosts allow you to use your existing per\-socket, per\-core, or per\-VM software licenses, including Windows Server, Microsoft SQL Server, SUSE, Linux Enterprise Server, and so on\.


+ [Differences between Dedicated Hosts and Dedicated Instances](#dedicated-hosts-dedicated-instances)
+ [Pricing and Billing](#dedicated-hosts-billing)
+ [Dedicated Hosts Limitations and Restrictions](#dedicated-hosts-limitations)
+ [Dedicated Host Configurations](#dedicated-hosts-configurations)
+ [Using Dedicated Hosts](how-dedicated-hosts-work.md)
+ [Monitoring Dedicated Hosts](dedicated-hosts-monitoring.md)

## Differences between Dedicated Hosts and Dedicated Instances<a name="dedicated-hosts-dedicated-instances"></a>

Dedicated Hosts and Dedicated Instances can both be used to launch Amazon EC2 instances onto physical servers that are dedicated for your use\.

There are no performance, security, or physical differences between Dedicated Instances and instances on Dedicated Hosts\. However, Dedicated Hosts give you additional visibility and control over how instances are placed on a physical server\.

When you use Dedicated Hosts, you have control over instance placement on the host using the Host Affinity and Instance Auto\-placement settings\. With Dedicated Instances, you don't have control over which host your instance launches and runs on\. If your organization wants to use AWS, but has an existing software license with hardware compliance requirements, this allows visibility into the host's hardware so you can meet those requirements\. 

For more information about the differences between Dedicated Hosts and Dedicated Instances, see [Amazon EC2 Dedicated Hosts](https://aws.amazon.com//ec2/dedicated-hosts)\. 

For more information about working with Dedicated Hosts and Dedicated Instances, see [Modifying Instance Tenancies](how-dedicated-hosts-work.md#moving-instances-dedicated-hosts)\.

## Pricing and Billing<a name="dedicated-hosts-billing"></a>

### On\-Demand Dedicated Hosts<a name="on-demand-dedicated-hosts"></a>

On\-Demand billing is automatically activated when you allocate a Dedicated Host to your account\.

Rates vary based on the instance type that the Dedicated Host supports and the region in which the Dedicated Host is running\. The instance type size or the number of instances that are running on the Dedicated Host do not have an impact on the cost of the host\. For more information see [Amazon EC2 Dedicated Hosts Pricing](http://aws.amazon.com/ec2/dedicated-hosts/pricing/)\.

To terminate On\-Demand billing, you must first stop instances running on the Dedicated Host and then release it\. For more information, see [Managing and Releasing Dedicated Hosts](how-dedicated-hosts-work.md#dedicated-hosts-managing)\.

### Dedicated Host Reservations<a name="dedicated-host-reservations"></a>

Dedicated Host Reservations provide a billing discount compared to running On\-Demand Dedicated Hosts\. Reservations are available in three payment options:

+ **No Upfront**—No Upfront Reservations provide you with a discount on your Dedicated Host usage over a term and do not require an upfront payment\. Available for a one\-year term only\.

+ **Partial Upfront**—A portion of the reservation must be paid upfront and the remaining hours in the term are billed at a discounted rate\. Available in one\-year and three\-year terms\.

+ **All Upfront**—Provides the lowest effective price\. Available in one\-year and three\-year terms and covers the entire cost of the term upfront, with no additional charges going forward\.

You must have active Dedicated Hosts in your account before you can purchase reservations\. Each reservation covers a single, specific Dedicated Host in your account\. Reservations are applied to the instance family on the host, not the instance size\. If you have three Dedicated Hosts with different instances sizes \(`m4.xlarge`, `m4.medium`, and `m4.large`\) you can associate a single `m4` reservation with all those Dedicated Hosts\. The instance family and region of the reservation must match that of the Dedicated Hosts you want to associate it with\. 

**Note**  
When a reservation is associated with a Dedicated Host, the Dedicated Host can't be released until the reservation's term is over\.

#### Purchasing Dedicated Host Reservations<a name="purchasing-dedicated-host-reservations"></a>

You can purchase Dedicated Host Reservations using the console or the API\.

**To purchase Dedicated Host Reservations using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Dedicated Hosts**, **Dedicated Host Reservations**, **Purchase Dedicated Host Reservation**\.

1. On the **Purchase Dedicated Host Reservation** screen, you can search for offerings using the default settings or you can specify a configuration for the offering\.

   + **Host instance family**—The options listed correspond with the Dedicated Hosts in your account that are not assigned to a reservation\.

   + **Availability Zone**—The Availability Zone of the Dedicated Hosts in your account that aren't assigned to a reservation\.

   + **Payment Option**—The payment option for the offering\.

   + **Term**—The term of the reservation\. Can be one or three years\.

1. Choose **Find offering** and select an offering\.

1. Choose the Dedicated Hosts to associate with the Dedicated Host Reservation and choose **Review**\.

1. Review your order and choose **Purchase** to complete the transaction\.

#### Viewing Dedicated Host Reservations<a name="viewing-host-reservations"></a>

You can view information about the Dedicated Hosts associated with your reservation, the term of the reservation, the payment option selected, and the start and end dates of the reservation\.

**View details of Dedicated Host Reservations**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the Dedicated Hosts page, choose **Dedicated Host Reservations**\.

1. Choose the reservation from the list provided\.

1. Choose **Details** for information about the reservation\.

1. Choose **Hosts** for information about the Dedicated Hosts the reservation is associated with\.

## Dedicated Hosts Limitations and Restrictions<a name="dedicated-hosts-limitations"></a>

Before you allocate Dedicated Hosts, take note of the following limitations and restrictions\.

+ RHEL, SUSE Linux, and Windows AMIs offered by AWS or on the AWS Marketplace cannot be used with Dedicated Hosts\.

+ Amazon EC2 instance recovery is not supported\.

+ Up to two On\-Demand Dedicated Hosts per instance family, per region can be allocated\. It is possible to request a limit increase: [Request to Raise Allocation Limit on Amazon EC2 Dedicated Hosts](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=)\.

+ The instances that run on a Dedicated Host can only be launched in a VPC\.

+ Host limits are independent from instance limits\. Instances that you are running on Dedicated Hosts do not count towards your instance limits\.

+ Auto Scaling groups are not supported\.

+ Amazon RDS instances are not supported\.

+ The AWS Free Usage tier is not available for Dedicated Hosts\.

+ Instance placement control refers to managing instance launches onto Dedicated Hosts\. Placement groups are not supported for Dedicated Hosts\.

## Dedicated Host Configurations<a name="dedicated-hosts-configurations"></a>

Dedicated Hosts are configured to support a single instance type and size capacity\. The number of instances you can launch onto a Dedicated Host depends on the instance type that the Dedicated Host is configured to support\. For example, if you allocated a `c3.xlarge` Dedicated Host, you'd have the right to launch up to eight `c3.xlarge` instances on the Dedicated Host\. To determine the number of instance type sizes that you can run on a particular Dedicated Host, see [Amazon EC2 Dedicated Hosts Pricing](http://aws.amazon.com/ec2/dedicated-hosts/pricing/)\.
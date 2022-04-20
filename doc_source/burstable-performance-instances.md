# Burstable performance instances<a name="burstable-performance-instances"></a>

Many general purpose workloads are on average not busy, and do not require a high level of sustained CPU performance\. The following graph illustrates the CPU utilization for many common workloads that customers run in the AWS Cloud today\.

![\[Many common workloads look like this: the average CPU utilization is at or below the baseline, with some spikes above the baseline.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/CPU-common-workloads.png)

These low\-to\-moderate CPU utilization workloads lead to wastage of CPU cycles and, as a result, you pay for more than you use\. To overcome this, you can leverage the low\-cost burstable general purpose instances, which are the T instances\. 

The T instance family provides a baseline CPU performance with the ability to burst above the baseline at any time for as long as required\. The baseline CPU is defined to meet the needs of the majority of general purpose workloads, including large\-scale micro\-services, web servers, small and medium databases, data logging, code repositories, virtual desktops, development and test environments, and business\-critical applications\. The T instances offer a balance of compute, memory, and network resources, and provide you with the most cost\-effective way to run a broad spectrum of general purpose applications that have a low\-to\-moderate CPU usage\. They can save you up to 15% in costs when compared to M instances, and can lead to even more cost savings with smaller, more economical instance sizes, offering as low as 2 vCPUs and 0\.5 GiB of memory\. The smaller T instance sizes, such as nano, micro, small, and medium, are well suited for workloads that need a small amount of memory and do not expect high CPU usage\.

**Note**  
This topic describes burstable CPU\. For information about burstable network performance, see [Amazon EC2 instance network bandwidth](ec2-instance-network-bandwidth.md)\. 

## EC2 burstable instance types<a name="burstable-instance-types"></a>

The EC2 burstable instances consist of T4g, T3a and T3 instance types, and the previous generation T2 instance types\.

The T4g instance types are the latest generation of burstable instances\. They provide the best price for performance, and provide you with the lowest cost of all the EC2 instance types\. The T4g instance types are powered by Arm\-based [AWS Graviton2](http://aws.amazon.com/ec2/graviton) processors with extensive ecosystem support from operating systems vendors, independent software vendors, and popular AWS services and applications\.

The following table summarizes the key differences between the burstable instance types\.


****  

| Type | Description | Processor family | 
| --- | --- | --- | 
| Latest generation | 
| T4g |  Lowest cost EC2 instance type with up to 40% higher price/performance and 20% lower costs vs T3  |  AWS Graviton2 processors with Arm Neoverse N1 cores  | 
| T3a |  Lowest cost x86\-based instances with 10% lower costs vs T3 instances  |  AMD 1st gen EPYC processors  | 
| T3 |  Best peak price/performance for x86 workloads with up to 30% lower price/performance vs previous generation T2 instances  |  Intel Xeon Scalable \(Skylake, Cascade Lake processors\)  | 
| Previous generation | 
| T2 |  Previous generation burstable instances  |  Intel Xeon processors  | 

For information about instance pricing and additional specifications, see [Amazon EC2 Pricing](https://aws.amazon.com/ec2/pricing/) and [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. For information about burstable network performance, see [Amazon EC2 instance network bandwidth](ec2-instance-network-bandwidth.md)\.

If your account is less than 12 months old, you can use a `t2.micro` instance for free \(or a `t3.micro` instance in Regions where `t2.micro` is unavailable\) within certain usage limits\. For more information, see [AWS Free Tier](https://aws.amazon.com/free/)\.

**Supported purchasing options for T instances**
+ On\-Demand Instances
+ Reserved Instances
+ Dedicated Instances \(T3 only\)
+ Dedicated Hosts \(T3 only, in `standard` mode only\)
+ Spot Instances

For more information, see [Instance purchasing options](instance-purchasing-options.md)\.

**Topics**
+ [EC2 burstable instance types](#burstable-instance-types)
+ [Best practices](#burstable-performance-instances-best-practices)
+ [Key concepts and definitions for burstable performance instances](burstable-credits-baseline-concepts.md)
+ [Unlimited mode for burstable performance instances](burstable-performance-instances-unlimited-mode.md)
+ [Standard mode for burstable performance instances](burstable-performance-instances-standard-mode.md)
+ [Work with burstable performance instances](burstable-performance-instances-how-to.md)
+ [Monitor your CPU credits](burstable-performance-instances-monitoring-cpu-credits.md)

## Best practices<a name="burstable-performance-instances-best-practices"></a>

Follow these best practices to get the maximum benefit from burstable performance instances\.
+ Ensure that the instance size you choose passes the minimum memory requirements of your operating system and applications\. Operating systems with graphical user interfaces that consume significant memory and CPU resources \(for example, Windows\) might require a `t3.micro` or larger instance size for many use cases\. As the memory and CPU requirements of your workload grow over time, you have the flexibility with the T instances to scale to larger instance sizes of the same instance type, or to select another instance type\.
+ Enable [AWS Compute Optimizer](http://aws.amazon.com/compute-optimizer/getting-started/) for your account and review the Compute Optimizer recommendations for your workload\. Compute Optimizer can help assess whether instances should be upsized to improve performance or downsized for cost savings\.
+ For additional requirements, see [Release notes](general-purpose-instances.md#general-purpose-instances-release-notes)\.
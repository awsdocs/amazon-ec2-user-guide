# Elastic Fabric Adapter<a name="efa"></a>

An Elastic Fabric Adapter \(EFA\) is a network device that you can attach to your Amazon EC2 instance to accelerate High Performance Computing \(HPC\) applications\. EFA enables you to achieve the application performance of an on\-premises HPC cluster, with the scalability, flexibility, and elasticity provided by the AWS Cloud\.

EFA provides lower and more consistent latency and higher throughput than the TCP transport traditionally used in cloud\-based HPC systems\. It enhances the performance of inter\-instance communication that is critical for scaling HPC applications\. It is optimized to work on the existing AWS network infrastructure and it can scale depending on application requirements\.

EFA integrates with libfabric 1\.8\.0 and it supports both Open MPI 3\.1\.4 and Intel MPI 2019 Update 4\.

**Note**  
The OS\-bypass capabilities of EFAs are not supported on Windows instances\. If you attach an EFA to a Windows instance, the instance functions as an Elastic Network Adapter, without the added EFA capabilities\.

**Topics**
+ [EFA Basics](#efa-basics)
+ [Supported MPI Implementations](#efa-mpi)
+ [Supported Instance Types](#efa-instance-types)
+ [Supported AMIs](#efa-amis)
+ [EFA Limitations](#efa-limits)
+ [Getting Started with EFAs](efa-start.md)
+ [Working with EFA](efa-working-with.md)
+ [Monitoring an EFA](efa-working-monitor.md)

## EFA Basics<a name="efa-basics"></a>

An EFA is an Elastic Network Adapter \(ENA\) with added capabilities\. It provides all of the functionality of an ENA, with an additional OS\-bypass functionality\. OS\-bypass is an access model that allows HPC applications to communicate directly with the network interface hardware to provide low\-latency, reliable transport functionality\.

![\[Contrasting a traditional HPC software stack with one that uses an EFA.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/efa_stack.png)

Traditionally, HPC applications use the Message Passing Interface \(MPI\) to interface with the system’s network transport\. In the AWS Cloud, this has meant that applications interface with MPI, which then uses the operating system's TCP/IP stack and the ENA device driver to enable network communication between instances\.

With an EFA, HPC applications use MPI to interface with the *libfabric* API\. The libfabric API bypasses the operating system kernel and communicates directly with the EFA device to put packets on the network\. This reduces overhead and enables the HPC application to run more efficiently\.

**Note**  
Libfabric is a core component of the OpenFabrics Interfaces \(OFI\) framework, which defines and exports the user\-space API of OFI\. For more information, see the [Libfabric OpenFabrics](https://ofiwg.github.io/libfabric/) website\.

### Differences between EFAs and ENAs<a name="efa-differences"></a>

Elastic Network Adapters \(ENAs\) provide traditional IP networking features that are required to support VPC networking\. EFAs provide all of the same traditional IP networking features as ENAs, and they also support OS\-bypass capabilities\. OS\-bypass enables HPC applications to bypass the operating system kernel and to communicate directly with the EFA device\.

## Supported MPI Implementations<a name="efa-mpi"></a>

EFA supports the following Message Passing Interface \(MPI\) implementations:
+ Open MPI 3\.1\.4
+ Intel MPI 2019 Update 4

## Supported Instance Types<a name="efa-instance-types"></a>

The following instance types support EFAs: `c5n.18xlarge`, `c5n.metal`, `i3en.24xlarge`, and `p3dn.24xlarge`\.

## Supported AMIs<a name="efa-amis"></a>

The following AMIs support EFAs: Amazon Linux, Amazon Linux 2, Red Hat Enterprise Linux 7\.6, CentOS 7\.6, Ubuntu 16\.04, and Ubuntu 18\.04\.

## EFA Limitations<a name="efa-limits"></a>

EFA has the following limitations:
+ You can attach only one EFA per instance\.
+ EFA OS\-bypass traffic is limited to a single subnet\. In other words, EFA traffic cannot be sent from one subnet to another\. Normal IP traffic from the EFA can be sent from one subnet to another\.
+ EFA OS\-bypass traffic is not routable\. Normal IP traffic from the EFA remains routable\.
+ The EFA must be a member of a security group that allows all inbound and outbound traffic to and from the security group itself\.
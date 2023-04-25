# Elastic Fabric Adapter<a name="efa"></a>

An Elastic Fabric Adapter \(EFA\) is a network device that you can attach to your Amazon EC2 instance to accelerate High Performance Computing \(HPC\) and machine learning applications\. EFA enables you to achieve the application performance of an on\-premises HPC cluster, with the scalability, flexibility, and elasticity provided by the AWS Cloud\.

EFAs provide lower and more consistent latency and higher throughput than the TCP transport traditionally used in cloud\-based HPC systems\. It enhances the performance of inter\-instance communication that is critical for scaling HPC and machine learning applications\. It is optimized to work on the existing AWS network infrastructure and it can scale depending on application requirements\.

EFAs integrate with Libfabric 1\.7\.0 and later and it supports Open MPI 3\.1\.3 and later and Intel MPI 2019 Update 5 and later for HPC applications, and Nvidia Collective Communications Library \(NCCL\) for machine learning applications\.

**Note**  
The OS\-bypass capabilities of EFAs are not supported on Windows instances\. If you attach an EFA to a Windows instance, the instance functions as an Elastic Network Adapter, without the added EFA capabilities\.

**Topics**
+ [EFA basics](#efa-basics)
+ [Supported interfaces and libraries](#efa-mpi)
+ [Supported instance types](#efa-instance-types)
+ [Supported operating systems](#efa-os)
+ [EFA limitations](#efa-limits)
+ [Get started with EFA and MPI](efa-start.md)
+ [Get started with EFA and NCCL](efa-start-nccl.md)
+ [Work with EFA](efa-working-with.md)
+ [Monitor an EFA](efa-working-monitor.md)
+ [Verify the EFA installer using a checksum](efa-verify.md)

## EFA basics<a name="efa-basics"></a>

An EFA is an Elastic Network Adapter \(ENA\) with added capabilities\. It provides all of the functionality of an ENA, with an additional OS\-bypass functionality\. OS\-bypass is an access model that allows HPC and machine learning applications to communicate directly with the network interface hardware to provide low\-latency, reliable transport functionality\.

![\[Contrasting a traditional HPC software stack with one that uses an EFA.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/efa_stack.png)

Traditionally, HPC applications use the Message Passing Interface \(MPI\) to interface with the system’s network transport\. In the AWS Cloud, this has meant that applications interface with MPI, which then uses the operating system's TCP/IP stack and the ENA device driver to enable network communication between instances\.

With an EFA, HPC applications use MPI or NCCL to interface with the *Libfabric* API\. The Libfabric API bypasses the operating system kernel and communicates directly with the EFA device to put packets on the network\. This reduces overhead and enables the HPC application to run more efficiently\.

**Note**  
Libfabric is a core component of the OpenFabrics Interfaces \(OFI\) framework, which defines and exports the user\-space API of OFI\. For more information, see the [Libfabric OpenFabrics](https://ofiwg.github.io/libfabric/) website\.

### Differences between EFAs and ENAs<a name="efa-differences"></a>

Elastic Network Adapters \(ENAs\) provide traditional IP networking features that are required to support VPC networking\. EFAs provide all of the same traditional IP networking features as ENAs, and they also support OS\-bypass capabilities\. OS\-bypass enables HPC and machine learning applications to bypass the operating system kernel and to communicate directly with the EFA device\.

## Supported interfaces and libraries<a name="efa-mpi"></a>

EFAs support the following interfaces and libraries:
+ Open MPI 3\.1\.3 and later
+ Open MPI 4\.0 or newer is preferred for Graviton
+ Intel MPI 2019 Update 5 and later
+ NVIDIA Collective Communications Library \(NCCL\) 2\.4\.2 and later

## Supported instance types<a name="efa-instance-types"></a>

The following instance types support EFAs:
+ General purpose: `m5dn.24xlarge` \| `m5dn.metal` \| `m5n.24xlarge` \| `m5n.metal` \| `m5zn.12xlarge` \| `m5zn.metal` \| `m6a.48xlarge` \| `m6a.metal` \| `m6i.32xlarge` \| `m6i.metal` \| `m6id.32xlarge` \| `m6id.metal` \| `m6idn.32xlarge` \| `m6in.32xlarge` \| `m7g.16xlarge` \| `m7g.metal`
+ Compute optimized: `c5n.9xlarge` \| `c5n.18xlarge` \| `c5n.metal` \| `c6a.48xlarge` \| `c6a.metal` \| `c6gn.16xlarge` \| `c6i.32xlarge` \| `c6i.metal` \| `c6id.32xlarge` \| `c6id.metal` \| `c6in.32xlarge` \| `c7g.16xlarge` \| `c7g.metal` \| `hpc6a.48xlarge`
+ Memory optimized: `hpc6id.32xlarge` \| `r5dn.24xlarge` \| `r5dn.metal` \| `r5n.24xlarge` \| `r5n.metal` \| `r6a.48xlarge` \| `r6a.metal` \| `r6i.32xlarge` \| `r6i.metal` \| `r6idn.32xlarge` \| `r6in.32xlarge` \| `r6id.32xlarge` \| `r6id.metal` \| `r7g.16xlarge` \| `r7g.metal` \| `x2idn.32xlarge` \| `x2idn.metal` \| `x2iedn.32xlarge` \| `x2iedn.metal` \| `x2iezn.12xlarge` \| `x2iezn.metal`
+ Storage optimized: `i3en.12xlarge` \| `i3en.24xlarge` \| `i3en.metal` \| `i4i.32xlarge` \| `i4i.metal` \| `im4gn.16xlarge`
+ Accelerated computing: `dl1.24xlarge` \| `g4dn.8xlarge` \| `g4dn.12xlarge` \| `g4dn.metal` \| `g5.48xlarge` \| `inf1.24xlarge` \| `p3dn.24xlarge` \| `p4d.24xlarge` \| `p4de.24xlarge` \| `trn1.32xlarge` \| `vt1.24xlarge`

**To see the available instance types that support EFAs in a specific Region**  
The available instance types vary by Region\. To see the available instance types that support EFAs in a Region, use the [describe\-instance\-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html) command with the `--region` parameter\. Include the `--filters` parameter to scope the results to the instance types that support EFA and the `--query` parameter to scope the output to the value of `InstanceType`\.

```
aws ec2 describe-instance-types  --region us-east-1  --filters Name=network-info.efa-supported,Values=true  --query "InstanceTypes[*].[InstanceType]"  --output text | sort
```

Example output

```
c5n.18xlarge
c5n.9xlarge
c5n.metal
c6a.32xlarge
c6a.48xlarge
c6a.metal
c6gn.16xlarge
c6i.32xlarge
...
```

## Supported operating systems<a name="efa-os"></a>

The following operating systems support EFAs with Intel/AMD x86\-based instance types:
+ Amazon Linux 2
+ CentOS 7
+ RHEL 7 and 8
+ Rocky Linux 8 and 9
+ Ubuntu 18\.04, 20\.04, and 22\.04
+ SUSE Linux Enterprise 15 SP2 and later
+ OpenSUSE Leap 15\.4 and later

**Note**  
Ubuntu 20\.04 supports peer direct support when used with `dl1.24xlarge` instances\.

The following operating systems support EFAs with Arm\-based \(Graviton 2\) instance types:
+ Amazon Linux 2
+ RHEL 8 and Rocky Linux 8 and 9
+ Ubuntu 18\.04, 20\.04, and 22\.04
+ SUSE Linux Enterprise 15 SP2 and later

## EFA limitations<a name="efa-limits"></a>

EFAs have the following limitations:
+ All `p4d` instance types support NVIDIA GPUDirect Remote Direct Memory Access \(RDMA\)\.
+ [Instance types that support multiple network cards](using-eni.md#network-cards) can be configured with one EFA per network card\. All other supported instance types support only one EFA per instance\.
+ For `c7g.16xlarge`, `m7g.16xlarge` and `r7g.16xlarge` Dedicated Instances and Dedicated Hosts are not supported when an EFA is attached\.
+ EFA OS\-bypass traffic is limited to a single subnet\. In other words, EFA traffic cannot be sent from one subnet to another\. Normal IP traffic from the EFA can be sent from one subnet to another\.
+ EFA OS\-bypass traffic is not routable\. Normal IP traffic from the EFA remains routable\.
+ The EFA must be a member of a security group that allows all inbound and outbound traffic to and from the security group itself\.
+ EFA is not supported on AWS [Outposts](https://docs.aws.amazon.com/outposts/index.html) 
# Get started with EFA and NCCL<a name="efa-start-nccl"></a>

The NVIDIA Collective Communications Library \(NCCL\) is a library of standard collective communication routines for multiple GPUs across a single node or multiple nodes\. NCCL can be used together with EFA, Libfabric, and MPI to support various machine learning workloads\. For more information, see the [NCCL](https://developer.nvidia.com/nccl) website\.

**Note**  
NCCL with EFA is supported with `p3dn.24xlarge` and `p4d.24xlarge` instances only\.
Only NCCL 2\.4\.2 and later is supported with EFA\.

The following tutorials help you to launch an EFA and NCCL\-enabled instance cluster for machine learning workloads\.
+ [Use a base AMI](efa-start-nccl-base.md)
+ [Use an AWS Deep Learning AMI](efa-start-nccl-dlami.md)
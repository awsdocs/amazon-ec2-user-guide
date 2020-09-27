# Enhanced networking on Linux<a name="enhanced-networking"></a>

Enhanced networking uses single root I/O virtualization \(SR\-IOV\) to provide high\-performance networking capabilities on [supported instance types](#supported_instances)\. SR\-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces\. Enhanced networking provides higher bandwidth, higher packet per second \(PPS\) performance, and consistently lower inter\-instance latencies\. There is no additional charge for using enhanced networking\.

For information about the supported network speed for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types)\.

**Topics**
+ [Enhanced networking support](#supported_instances)
+ [Enabling enhanced networking on your instance](#enabling_enhanced_networking)
+ [Enhanced networking: ENA](enhanced-networking-ena.md)
+ [Enhanced networking: Intel 82599 VF](sriov-networking.md)
+ [Troubleshooting ENA](troubleshooting-ena.md)

## Enhanced networking support<a name="supported_instances"></a>

All [current generation](instance-types.md#current-gen-instances) instance types support enhanced networking, except for T2 instances\.

You can enable enhanced networking using one of the following mechanisms:

**Elastic Network Adapter \(ENA\)**  
The Elastic Network Adapter \(ENA\) supports network speeds of up to 100 Gbps for supported instance types\.  
The current generation instances use ENA for enhanced networking, except for C4, D2, and M4 instances smaller than `m4.16xlarge`\.

**Intel 82599 Virtual Function \(VF\) interface**  
The Intel 82599 Virtual Function interface supports network speeds of up to 10 Gbps for supported instance types\.  
The following instance types use the Intel 82599 VF interface for enhanced networking: C3, C4, D2, I2, M4 \(excluding m4\.16xlarge\), and R3\.

For a summary of the enhanced networking mechanisms by instance type, see [Summary of networking and storage features](instance-types.md#instance-type-summary-table)\.

## Enabling enhanced networking on your instance<a name="enabling_enhanced_networking"></a>

If your instance type supports the Elastic Network Adapter for enhanced networking, follow the procedures in [Enabling enhanced networking with the Elastic Network Adapter \(ENA\) on Linux instances](enhanced-networking-ena.md)\.

If your instance type supports the Intel 82599 VF interface for enhanced networking, follow the procedures in [Enabling enhanced networking with the Intel 82599 VF interface on Linux instances](sriov-networking.md)\.
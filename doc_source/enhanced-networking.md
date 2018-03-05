# Enhanced Networking on Linux<a name="enhanced-networking"></a>

Enhanced networking uses single root I/O virtualization \(SR\-IOV\) to provide high\-performance networking capabilities on [supported instance types](#supported_instances)\. SR\-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces\. Enhanced networking provides higher bandwidth, higher packet per second \(PPS\) performance, and consistently lower inter\-instance latencies\. There is no additional charge for using enhanced networking\.


+ [Enhanced Networking Types](#supported_instances)
+ [Enabling Enhanced Networking on Your Instance](#enabling_enhanced_networking)
+ [Enabling Enhanced Networking with the Intel 82599 VF Interface on Linux Instances in a VPC](sriov-networking.md)
+ [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Linux Instances in a VPC](enhanced-networking-ena.md)
+ [Troubleshooting the Elastic Network Adapter \(ENA\)](troubleshooting-ena.md)

## Enhanced Networking Types<a name="supported_instances"></a>

Depending on your instance type, enhanced networking can be enabled using one of the following mechanisms:

**Intel 82599 Virtual Function \(VF\) interface**  
The Intel 82599 Virtual Function interface supports network speeds of up to 10 Gbps for supported instance types\.  
C3, C4, D2, I2, R3, and M4 \(excluding `m4.16xlarge`\) instances use the Intel 82599 VF interface for enhanced networking\.

**Elastic Network Adapter \(ENA\)**  
The Elastic Network Adapter \(ENA\) supports network speeds of up to 25 Gbps for supported instance types\.  
C5, F1, G3, H1, I3, M5, P2, P3, R4, X1, and `m4.16xlarge` instances use the Elastic Network Adapter for enhanced networking\.

To find out which instance types support 10 or 25 Gbps network speeds, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types)\.

## Enabling Enhanced Networking on Your Instance<a name="enabling_enhanced_networking"></a>

If your instance type supports the Intel 82599 VF interface for enhanced networking, follow the procedures in [Enabling Enhanced Networking with the Intel 82599 VF Interface on Linux Instances in a VPC](sriov-networking.md)\.

If your instance type supports the Elastic Network Adapter for enhanced networking, follow the procedures in [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Linux Instances in a VPC](enhanced-networking-ena.md)\.
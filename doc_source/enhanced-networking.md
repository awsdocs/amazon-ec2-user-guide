# Enhanced Networking on Linux<a name="enhanced-networking"></a>

Enhanced networking uses single root I/O virtualization \(SR\-IOV\) to provide high\-performance networking capabilities on [supported instance types](#supported_instances)\. SR\-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization when compared to traditional virtualized network interfaces\. Enhanced networking provides higher bandwidth, higher packet per second \(PPS\) performance, and consistently lower inter\-instance latencies\. There is no additional charge for using enhanced networking\.

**Topics**
+ [Enhanced Networking Types](#supported_instances)
+ [Enabling Enhanced Networking on Your Instance](#enabling_enhanced_networking)
+ [Enhanced Networking: ENA](enhanced-networking-ena.md)
+ [Enhanced Networking: Intel 82599 VF](sriov-networking.md)
+ [Troubleshooting ENA](troubleshooting-ena.md)

## Enhanced Networking Types<a name="supported_instances"></a>

Depending on your instance type, enhanced networking can be enabled using one of the following mechanisms:

**Elastic Network Adapter \(ENA\)**  
The Elastic Network Adapter \(ENA\) supports network speeds of up to 100 Gbps for supported instance types\.  
A1, C5, C5d, C5n, F1, G3, H1, I3, `m4.16xlarge`, M5, M5a, M5ad, M5d, P2, P3, R4, R5, R5a, R5ad, R5d, T3, T3a, `u-6tb1.metal`, `u-9tb1.metal`, `u-12tb1.metal`, X1, X1e, and z1d instances use the Elastic Network Adapter for enhanced networking\.

**Intel 82599 Virtual Function \(VF\) interface**  
The Intel 82599 Virtual Function interface supports network speeds of up to 10 Gbps for supported instance types\.  
C3, C4, D2, I2, M4 \(excluding `m4.16xlarge`\), and R3 instances use the Intel 82599 VF interface for enhanced networking\.

For information about the supported network speed for each instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types)\.

## Enabling Enhanced Networking on Your Instance<a name="enabling_enhanced_networking"></a>

If your instance type supports the Elastic Network Adapter for enhanced networking, follow the procedures in [Enabling Enhanced Networking with the Elastic Network Adapter \(ENA\) on Linux Instances](enhanced-networking-ena.md)\.

If your instance type supports the Intel 82599 VF interface for enhanced networking, follow the procedures in [Enabling Enhanced Networking with the Intel 82599 VF Interface on Linux Instances](sriov-networking.md)\.
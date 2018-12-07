# Linux Accelerated Computing Instances<a name="accelerated-computing-instances"></a>

If you require high processing capability, you'll benefit from using accelerated computing instances, which provide access to hardware\-based compute accelerators such as Graphics Processing Units \(GPUs\) or Field Programmable Gate Arrays \(FPGAs\)\. Accelerated computing instances enable more parallelism for higher throughput on compute\-intensive workloads\.

GPU\-based instances provide access to NVIDIA GPUs with thousands of compute cores\. You can use GPU\-based accelerated computing instances to accelerate scientific, engineering, and rendering applications by leveraging the CUDA or Open Computing Language \(OpenCL\) parallel computing frameworks\. You can also use them for graphics applications, including game streaming, 3\-D application streaming, and other graphics workloads\.

FPGA\-based instances provide access to large FPGAs with millions of parallel system logic cells\. You can use FPGA\-based accelerated computing instances to accelerate workloads such as genomics, financial analysis, real\-time video processing, big data analysis, and security workloads by leveraging custom hardware accelerations\. You can develop these accelerations using hardware description languages such as Verilog or VHDL, or by using higher\-level languages such as OpenCL parallel computing frameworks\. You can either develop your own hardware acceleration code or purchase hardware accelerations through the [AWS Marketplace](https://aws.amazon.com/marketplace/)\.

**Important**  
FPGA\-based instances do not support Microsoft Windows\.

You can cluster accelerated computing instances into a cluster placement group\. Cluster placement groups provide low latency and high\-bandwidth connectivity between the instances within a single Availability Zone\. For more information, see [Placement Groups](placement-groups.md)\.

**Topics**
+ [Accelerated Computing Instance Families](#gpu-instance-families)
+ [Hardware Specifications](#gpu-instance-specifications)
+ [Instance Performance](#gpu-instance-performance)
+ [Network Performance](#gpu-network-performance)
+ [Instance Features](#gpu-instances-features)
+ [Release Notes](#gpu-instance-current-limitations)
+ [AMIs for GPU\-Based Accelerated Computing Instances](#gpu-operating-systems)
+ [Installing the NVIDIA Driver on Linux Instances](install-nvidia-driver.md)
+ [Activate NVIDIA GRID Virtual Applications \(G3 Instances Only\)](activate_grid.md)
+ [Optimizing GPU Settings \(P2, P3, and G3 Instances\)](optimize_gpu.md)
+ [Getting Started with FPGA Development](fpga-getting-started.md)

For information about Windows accelerated computing instances, see [Windows Accelerated Computing Instances](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/accelerated-computing-instances.html) in the *Amazon EC2 User Guide for Windows Instances*\.

## Accelerated Computing Instance Families<a name="gpu-instance-families"></a>

Accelerated computing instance families use hardware accelerators, or co\-processors, to perform some functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs\. The following accelerated computing instance families are available for you to launch in Amazon EC2\.

**F1 Instances**  
F1 instances use Xilinx UltraScale\+ VU9P FPGAs and are designed to accelerate computationally intensive algorithms, such as data\-flow or highly parallel operations not suited to general purpose CPUs\. Each FPGA in an F1 instance contains approximately 2\.5 million logic elements and approximately 6,800 Digital Signal Processing \(DSP\) engines, along with 64 GiB of local DDR ECC protected memory, connected to the instance by a dedicated PCIe Gen3 x16 connection\. F1 instances provide local NVMe SSD volumes\.

Developers can use the FPGA Developer AMI and AWS Hardware Developer Kit to create custom hardware accelerations for use on F1 instances\. The FPGA Developer AMI includes development tools for full\-cycle FPGA development in the cloud\. Using these tools, developers can create and share Amazon FPGA Images \(AFIs\) that can be loaded onto the FPGA of an F1 instance\.

For more information, see [Amazon EC2 F1 Instances](https://aws.amazon.com/ec2/instance-types/f1/)\.

**P3 Instances**  
P3 instances use NVIDIA Tesla V100 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models or through a machine learning framework\. P3 instances provide high\-bandwidth networking, powerful half, single, and double\-precision floating\-point capabilities, and up to 32 GiB of memory per GPU, which makes them ideal for deep learning, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\. Tesla V100 GPUs do not support graphics mode\. For more information, see [Amazon EC2 P3 Instances](https://aws.amazon.com/ec2/instance-types/p3)\.

P3 instances support NVIDIA NVLink peer to peer transfers\.

To view topology information about the system, run the following command:

```
nvidia-smi topo -m
```

For more information, see [NVIDIA NVLink](https://devblogs.nvidia.com/parallelforall/how-nvlink-will-enable-faster-easier-multi-gpu-computing/)\.

**P2 Instances**  
P2 instances use NVIDIA Tesla K80 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models\. P2 instances provide high\-bandwidth networking, powerful single and double precision floating\-point capabilities, and 12 GiB of memory per GPU, which makes them ideal for deep learning, graph databases, high\-performance databases, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\.

P2 instances support NVIDIA GPUDirect peer to peer transfers\.

To view topology information about the system, run the following command:

```
nvidia-smi topo -m
```

For more information, see [NVIDIA GPUDirect](https://developer.nvidia.com/gpudirect)\.

**G3 Instances**  
G3 instances use NVIDIA Tesla M60 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. G3 instances also provide NVIDIA GRID Virtual Workstation features, such as support for four monitors with resolutions up to 4096x2160, and NVIDIA GRID Virtual Applications\. G3 instances are well\-suited for applications such as 3D visualizations, graphics\-intensive remote workstations, 3D rendering, video encoding, virtual reality, and other server\-side graphics workloads requiring massively parallel processing power\. 

G3 instances support NVIDIA GRID Virtual Workstation and NVIDIA GRID Virtual Applications\. To activate either of these features, see [Activate NVIDIA GRID Virtual Applications \(G3 Instances Only\)](activate_grid.md)\.

**G2 Instances**  
G2 instances use NVIDIA GRID K520 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. NVIDIA GRID GPUs also support NVIDIA’s fast capture and encode API operations\. Example applications include video creation services, 3D visualizations, streaming graphics\-intensive applications, and other server\-side graphics workloads\.

## Hardware Specifications<a name="gpu-instance-specifications"></a>

The following is a summary of the hardware specifications for accelerated computing instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| p2\.xlarge | 4 | 61 | 
| p2\.8xlarge | 32 | 488 | 
| p2\.16xlarge | 64 | 732 | 
| p3\.2xlarge | 8 | 61 | 
| p3\.8xlarge | 32 | 244 | 
| p3\.16xlarge | 64 | 488 | 
| p3dn\.24xlarge | 96 | 768 | 
| g2\.2xlarge | 8 | 15 | 
| g2\.8xlarge | 32 | 60 | 
| g3s\.xlarge | 4 | 30\.5 | 
| g3\.4xlarge | 16 | 122 | 
| g3\.8xlarge | 32 | 244 | 
| g3\.16xlarge | 64 | 488 | 
| f1\.2xlarge | 8 | 122 | 
| f1\.4xlarge | 16 | 244 | 
| f1\.16xlarge | 64 | 976 | 

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimizing CPU Options](instance-optimize-cpu.md)\.

## Instance Performance<a name="gpu-instance-performance"></a>

There are several GPU setting optimizations that you can perform to achieve the best performance on your instances\. For more information, see [Optimizing GPU Settings \(P2, P3, and G3 Instances\)](optimize_gpu.md)\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some accelerated computing instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–Optimized Instances](EBSOptimized.md)\.

Some accelerated computing instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor State Control for Your EC2 Instance](processor_state_control.md)\.

## Network Performance<a name="gpu-network-performance"></a>

You can enable enhanced networking capabilities on supported instance types\. Enhanced networking provides significantly higher packet\-per\-second \(PPS\) performance, lower network jitter, and lower latencies\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md)\.

Instance types that use the Elastic Network Adapter \(ENA\) for enhanced networking deliver high packet per second performance with consistently low latencies\. Most applications do not consistently need a high level of network performance, but can benefit from having access to increased bandwidth when they send or receive data\. Instance sizes that use the ENA and are documented with network performance of "Up to 10 Gbps" or "Up to 25 Gbps" use a network I/O credit mechanism to allocate network bandwidth to instances based on average bandwidth utilization\. These instances accrue credits when their network bandwidth is below their baseline limits, and can use these credits when they perform network data transfers\.

The following is a summary of network performance for accelerated computing instances that support enhanced networking\.


****  

| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  `f1.2xlarge` \| `f1.4xlarge` \| `g3.4xlarge` \| `p3.2xlarge`  |  Up to 10 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `g3s.xlarge` \| `g3.8xlarge` \| `p2.8xlarge` \| `p3.8xlarge`  |  10 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
|  `f1.16xlarge` \| `g3.16.xlarge` \| `g3.16.xlarge` \| `p2.16xlarge` \| `p3.16xlarge`  |  25 Gbps  |  [ENA](enhanced-networking-ena.md)  | 
| `p3dn.24xlarge` | 100 Gbps |  [ENA](enhanced-networking-ena.md)  | 

## Instance Features<a name="gpu-instances-features"></a>

The following is a summary of features for accelerated computing instances\.


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| G2 |  |  | SSD | Yes | 
|  G3  |  Yes  |  |  |  Yes  | 
|  P2  |  Yes  |  |  |  Yes  | 
|  P3  |  `p3dn.24xlarge`: No All other sizes: Yes  |  `p3dn.24xlarge`: Yes All other sizes: No  |  `p3dn.24xlarge`: NVMe \*  |  Yes  | 
|  F1  |  |  |  NVMe \*  |  Yes  | 

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe](nvme-ebs-volumes.md)
+ [Amazon EC2 Instance Store](InstanceStorage.md)
+ [Placement Groups](placement-groups.md)

## Release Notes<a name="gpu-instance-current-limitations"></a>
+ You must launch the instance using an HVM AMI\.
+ GPU\-based instances can't access the GPU unless the NVIDIA drivers are installed\.
+ There is a limit of 100 AFIs per region\.
+ There is a limit on the number of instances that you can run\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\. To request an increase in these limits, use the following form: [Request to Increase Amazon EC2 Instance Limit](https://console.aws.amazon.com/support/home#/case/create?issueType=service-limit-increase&limitType=service-code-ec2-instances)\.

## AMIs for GPU\-Based Accelerated Computing Instances<a name="gpu-operating-systems"></a>

To help you get started, NVIDIA and others provide AMIs for GPU\-based accelerated computing instances\. These reference AMIs include the NVIDIA driver, which enables full functionality and performance of the NVIDIA GPUs\.

For a list of AMIs with the NVIDIA driver, search AWS Marketplace as follows:
+ [NVIDIA P3 AMIs](https://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=p3.2xlarge&searchTerms=NVIDIA)
+ [NVIDIA P2 AMIs](https://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=p2.xlarge&searchTerms=NVIDIA)
+ [NVIDIA GRID G3 AMIs](https://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g3.4xlarge&searchTerms=NVIDIAGRID)
+ [NVIDIA GRID G2 AMIs](https://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g2.2xlarge&searchTerms=NVIDIAGRID)

You can launch accelerated computing instances using any HVM AMI\.

**Important**  
These AMIs include drivers, software, or toolkits that are developed, owned, or provided by NVIDIA Corporation\. By using these AMIs, you agree to use these NVIDIA drivers, software, or toolkits only on Amazon EC2 instances that include NVIDIA hardware\.

You can also install the NVIDIA driver manually\. For more information, see [Installing the NVIDIA Driver on Linux Instances](install-nvidia-driver.md)\.
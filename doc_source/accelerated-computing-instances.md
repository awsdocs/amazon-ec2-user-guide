# Linux accelerated computing instances<a name="accelerated-computing-instances"></a>

Accelerated computing instances use hardware accelerators, or co\-processors, to perform some functions, such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs\. These instances enable more parallelism for higher throughput on compute\-intensive workloads\.

If you require high processing capability, you'll benefit from using accelerated computing instances, which provide access to hardware\-based compute accelerators such as Graphics Processing Units \(GPUs\), Field Programmable Gate Arrays \(FPGAs\), or AWS Inferentia\.

**Topics**
+ [GPU instances](#gpu-instances)
+ [Instances with AWS Trainium](#aws-trainium-instances)
+ [Instances with AWS Inferentia](#aws-inferentia-instances)
+ [Instances with Habana accelerators](#habana-instances)
+ [Video transcoding instances](#video-transcoding)
+ [FPGA instances](#fpga-instances)
+ [Hardware specifications](#gpu-instance-specifications)
+ [Instance performance](#gpu-instance-performance)
+ [Network performance](#gpu-network-performance)
+ [Amazon EBS I/O performance](#accelerated-ebs-perf)
+ [SSD\-based instance store volume I/O performance](#accelerated-computing-ssd-perf)
+ [Release notes](#gpu-instance-release-notes)
+ [Install NVIDIA drivers on Linux instances](install-nvidia-driver.md)
+ [Install AMD drivers on Linux instances](install-amd-driver.md)
+ [Setting up Dual 4K displays on G4ad](activate_g4ad_4k.md)
+ [Activate NVIDIA GRID Virtual Applications](activate_grid.md)
+ [Optimize GPU settings](optimize_gpu.md)

## GPU instances<a name="gpu-instances"></a>

GPU\-based instances provide access to NVIDIA GPUs with thousands of compute cores\. You can use these instances to accelerate scientific, engineering, and rendering applications by leveraging the CUDA or Open Computing Language \(OpenCL\) parallel computing frameworks\. You can also use them for graphics applications, including game streaming, 3\-D application streaming, and other graphics workloads\.

**G5 instances**  
G5 instances use NVIDIA A10G GPUs and provide high performance for graphics\-intensive applications such as remote workstations, video rendering, and cloud gaming, and deep learning models for applications such as natural language processing, computer vision, and recommendation engines\. These instances feature up to 8 NVIDIA A10G GPUs, second generation AMD EPYC processors, up to 100 Gbps of network bandwidth, and up to 7\.6 TB of local NVMe SSD storage\.

For more information, see [Amazon EC2 G5 Instances](http://aws.amazon.com/ec2/instance-types/g5/)\.

**G5g instances**  
G5g instances use NVIDIA T4G GPUs and provide high performance for graphics\-intensive applications such as game streaming and rendering that leverage industry\-standard APIs, such as OpenGL and Vulkan\. These instances are also suitable for running deep learning models for applications such as natural language processing and computer vision\. These instances feature up to 2 NVIDIA T4G Tensor Core GPUs, AWS Graviton2 processors, and up to 25 Gbps of network bandwidth\.

For more information, see [Amazon EC2 G5g Instances](http://aws.amazon.com/ec2/instance-types/g5g/)\.

<a name="g4-instances"></a>
**G4ad and G4dn instances**  
G4ad instances use AMD Radeon Pro V520 GPUs and 2nd generation AMD EPYC processors, and are well\-suited for graphics applications such as remote graphics workstations, game streaming, and rendering that leverage industry\-standard APIs such as OpenGL, DirectX, and Vulkan\. They provide up to 4 AMD Radeon Pro V520 GPUs, 64 vCPUs, 25 Gbps networking, and 2\.4 TB local NVMe\-based SSD storage\.

G4dn instances use NVIDIA Tesla GPUs and provide a cost\-effective, high\-performance platform for general purpose GPU computing using the CUDA or machine learning frameworks along with graphics applications using DirectX or OpenGL\. These instances provide high\- bandwidth networking, powerful half and single\-precision floating\-point capabilities, along with INT8 and INT4 precisions\. Each GPU has 16 GiB of GDDR6 memory, making G4dn instances well\-suited for machine learning inference, video transcoding, and graphics applications like remote graphics workstations and game streaming in the cloud\.

For more information, see [Amazon EC2 G4 Instances](http://aws.amazon.com/ec2/instance-types/g4/)\.

G4dn instances support NVIDIA GRID Virtual Workstation\. For more information, see [NVIDIA Marketplace offerings](http://aws.amazon.com/marketplace/search/results/?page=1&filters=instance_types&instance_types=g4dn.xlarge&searchTerms=NVIDIA%20GRID)\.

<a name="g3-instances"></a>
**G3 instances**  
These instances use NVIDIA Tesla M60 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. G3 instances also provide NVIDIA GRID Virtual Workstation features, such as support for four monitors with resolutions up to 4096x2160, and NVIDIA GRID Virtual Applications\. G3 instances are well\-suited for applications such as 3D visualizations, graphics\-intensive remote workstations, 3D rendering, video encoding, virtual reality, and other server\-side graphics workloads requiring massively parallel processing power\. 

For more information, see [Amazon EC2 G3 Instances](http://aws.amazon.com/ec2/instance-types/g3/)\.

G3 instances support NVIDIA GRID Virtual Workstation and NVIDIA GRID Virtual Applications\. To activate either of these features, see [Activate NVIDIA GRID Virtual Applications](activate_grid.md)\.

<a name="g2-instances"></a>
**G2 instances**  
These instances use NVIDIA GRID K520 GPUs and provide a cost\-effective, high\-performance platform for graphics applications using DirectX or OpenGL\. NVIDIA GRID GPUs also support NVIDIA’s fast capture and encode API operations\. Example applications include video creation services, 3D visualizations, streaming graphics\-intensive applications, and other server\-side graphics workloads\.

<a name="p4-instances"></a>
**P4d instances**  
These instances use NVIDIA A100 GPUs and provide a high\-performance platform for machine learning and HPC workloads\. P4d instances offer 400 Gbps of aggregate network bandwidth throughput and support, Elastic Fabric Adapter \(EFA\)\. They are the first EC2 instances to provide multiple network cards\.

For more information, see [Amazon EC2 P4d Instances](http://aws.amazon.com/ec2/instance-types/p4/)\.

P4d instances support NVIDIA NVSwitch GPU interconnect and NVIDIA GPUDirect RDMA\.

P4de instances offer NVIDIA 80GB\-A100s GPUs

<a name="p3-instances"></a>
**P3 instances**  
These instances use NVIDIA Tesla V100 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models or through a machine learning framework\. P3 instances provide high\-bandwidth networking, powerful half, single, and double\-precision floating\-point capabilities, and up to 32 GiB of memory per GPU, which makes them ideal for deep learning, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\. Tesla V100 GPUs do not support graphics mode\.

For more information, see [Amazon EC2 P3 Instances](https://aws.amazon.com/ec2/instance-types/p3)\.

P3 instances support NVIDIA NVLink peer to peer transfers\. For more information, see [NVIDIA NVLink](https://devblogs.nvidia.com/parallelforall/how-nvlink-will-enable-faster-easier-multi-gpu-computing/)\.

<a name="p2-instances"></a>
**P2 instances**  
P2 instances use NVIDIA Tesla K80 GPUs and are designed for general purpose GPU computing using the CUDA or OpenCL programming models\. P2 instances provide high\-bandwidth networking, powerful single and double precision floating\-point capabilities, and 12 GiB of memory per GPU, which makes them ideal for deep learning, graph databases, high\-performance databases, computational fluid dynamics, computational finance, seismic analysis, molecular modeling, genomics, rendering, and other server\-side GPU compute workloads\.

P2 instances support NVIDIA GPUDirect peer to peer transfers\. For more information, see [NVIDIA GPUDirect](https://developer.nvidia.com/gpudirect)\.

## Instances with AWS Trainium<a name="aws-trainium-instances"></a>

Amazon EC2 Trn1 instances, powered by [AWS Trainium](http://aws.amazon.com/machine-learning/trainium/), are purpose built for high\-performance, cost\-effective deep learning training\. You can use Trn1 instances to train natural language processing, computer vision, and recommender models used across a broad set of applications, such as speech recognition, recommendation, fraud detection, and image and video classification\. Use your existing workflows in popular ML frameworks, such as PyTorch and TensorFlow\. [AWS Neuron SDK](https://awsdocs-neuron.readthedocs-hosted.com/en/latest/) integrates seamlessly with these frameworks so that you can get started with only a few lines of code changes\.

For more information, see [Amazon EC2 Trn1 Instances](https://aws.amazon.com/ec2/instance-types/trn1/)\.

## Instances with AWS Inferentia<a name="aws-inferentia-instances"></a>

These instances are designed to accelerate machine learning using [AWS Inferentia](http://aws.amazon.com/machine-learning/inferentia/), a custom AI/ML chip from Amazon that provides high performance and low latency machine learning inference\. These instances are optimized for deploying deep learning \(DL\) models for applications, such as natural language processing, object detection and classification, content personalization and filtering, and speech recognition\.

There are a variety of ways that you can get started:
+ Use SageMaker, a fully\-managed service that is the easiest way to get started with machine learning models\. For more information, see [Get Started with SageMaker](https://docs.aws.amazon.com/sagemaker/latest/dg/gs.html) in the *Amazon SageMaker Developer Guide*\.
+ Launch an Inf1 instance using the Deep Learning AMI\. For more information, see [AWS Inferentia with DLAMI](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-inferentia.html) in the *AWS Deep Learning AMI Developer Guide*\.
+ Launch an Inf1 instance using your own AMI and install the [AWS Neuron SDK](https://github.com/aws/aws-neuron-sdk), which enables you to compile, run, and profile deep learning models for AWS Inferentia\.
+ Launch a container instance using an Inf1 instance and an Amazon ECS\-optimized AMI\. For more information, see [Amazon Linux 2 \(Inferentia\) AMIs](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ Create an Amazon EKS cluster with nodes running Inf1 instances\. For more information, see [Inferentia support](https://docs.aws.amazon.com/eks/latest/userguide/inferentia-support.html) in the **Amazon EKS User Guide**\.

For more information, see [Machine Learning on AWS](http://aws.amazon.com/machine-learning/)\.

**Inf1 instances**  
Inf1 instances use AWS Inferentia machine learning inference chips\. Inferentia was developed to enable highly cost\-effective low latency inference performance at any scale\.

For more information, see [Amazon EC2 Inf1 Instances](https://aws.amazon.com/ec2/instance-types/inf1/)\.

## Instances with Habana accelerators<a name="habana-instances"></a>

These instances are designed to accelerate deep learning model \(DL\) training workloads\. They use accelerators from Habana Labs, an Intel company\. These instances are optimized for DL models for applications such as image recognition, object detection and classification, and recommendation systems\.

For more information, see [Machine Learning on AWS](http://aws.amazon.com/machine-learning/)\.

**DL1 instances**  
DL1 instances use Habana Gaudi accelerators\. They offer up to 400 Gbps of aggregate network bandwidth, along with 32 GB of high bandwidth memory \(HBM\) per accelerator\. DL1 instances are designed to provide high performance and cost efficiency for training deep learning models\.

There are a variety of ways that you can get started:
+ Launch a DL1 instance using the [Habana Deep Learning AMI](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-habana.html)\.
+ Launch a DL1 instance using your own AMI and install the [Habana drivers and Habana SynapseAI SDK](https://github.com/HabanaAI/Setup_and_Install)\.
+ Launch a container instance using a DL1 instance and an Amazon ECS\-optimized AMI\.
+ Create an Amazon EKS cluster with nodes running DL1 instances\.

For more information, see [Amazon EC2 DL1 Instances](https://aws.amazon.com/ec2/instance-types/dl1/)\.

## Video transcoding instances<a name="video-transcoding"></a>

These instances are designed to accelerate video transcoding workloads, such as live broadcast, video conferencing, and just\-in\-time transcoding\.

<a name="vt1-instances"></a>
**VT1 instances**  
VT1 instances feature Xilinx Alveo U30 media accelerators and are designed for live video transcoding workloads\. These instances offer up to 8 Xilinx Alveo U30 acceleration cards, provide up to 192 GB of system memory, and up to 25 Gbps of network bandwidth\. VT1 instances feature H\.264/AVC and H\.265/HEVC codecs and support up to 4K UHD resolutions for multi\-stream video transcoding\.

There are a variety of ways that you can get started:
+ Launch a VT1 instance using the Xilinx U30 AMIs on AWS Marketplace\.
+ Launch a VT1 instance using your own AMI and install the [Xilinx U30 drivers and Xilinx Video SDK](https://xilinx.github.io/video-sdk/)\.
+ Launch a container instance using a VT1 instance and an Amazon ECS\-optimized AMI\.
+ Create an Amazon EKS cluster with nodes running VT1 instances\.

For more information, see [Amazon EC2 VT1 Instances](https://aws.amazon.com/ec2/instance-types/vt1/)\.

## FPGA instances<a name="fpga-instances"></a>

FPGA\-based instances provide access to large FPGAs with millions of parallel system logic cells\. You can use FPGA\-based accelerated computing instances to accelerate workloads such as genomics, financial analysis, real\-time video processing, big data analysis, and security workloads by leveraging custom hardware accelerations\. You can develop these accelerations using hardware description languages such as Verilog or VHDL, or by using higher\-level languages such as OpenCL parallel computing frameworks\. You can either develop your own hardware acceleration code or purchase hardware accelerations through the [AWS Marketplace](https://aws.amazon.com/marketplace/)\.

The [FPGA Developer AMI](https://aws.amazon.com/marketplace/pp/B06VVYBLZZ) provides the tools for developing, testing, and building AFIs\. You can use the FPGA Developer AMI on any EC2 instance with at least 32 GB of system memory \(for example, C5, M4, and R4 instances\)\.

For more information, see the documentation for the [AWS FPGA Hardware Development Kit](https://github.com/aws/aws-fpga/)\.

**F1 instances**  
F1 instances use Xilinx UltraScale\+ VU9P FPGAs and are designed to accelerate computationally intensive algorithms, such as data\-flow or highly parallel operations not suited to general purpose CPUs\. Each FPGA in an F1 instance contains approximately 2\.5 million logic elements and approximately 6,800 Digital Signal Processing \(DSP\) engines, along with 64 GiB of local DDR ECC protected memory, connected to the instance by a dedicated PCIe Gen3 x16 connection\. F1 instances provide local NVMe SSD volumes\.

Developers can use the FPGA Developer AMI and AWS Hardware Developer Kit to create custom hardware accelerations for use on F1 instances\. The FPGA Developer AMI includes development tools for full\-cycle FPGA development in the cloud\. Using these tools, developers can create and share Amazon FPGA Images \(AFIs\) that can be loaded onto the FPGA of an F1 instance\.

For more information, see [Amazon EC2 F1 Instances](https://aws.amazon.com/ec2/instance-types/f1/)\.

## Hardware specifications<a name="gpu-instance-specifications"></a>

The following is a summary of the hardware specifications for accelerated computing instances\. A virtual central processing unit \(vCPU\) represents a portion of the physical CPU assigned to a virtual machine \(VM\)\. For x86 instances, there are two vCPUs per core\. For Graviton instances, there is one vCPU per core\.


| Instance type | Default vCPUs | Memory \(GiB\) | Accelerators | 
| --- | --- | --- | --- | 
| dl1\.24xlarge | 96 | 768\.00 | 8 GPUs | 
| f1\.2xlarge | 8 | 122\.00 | 1 FPGAs | 
| f1\.4xlarge | 16 | 244\.00 | 2 FPGAs | 
| f1\.16xlarge | 64 | 976\.00 | 8 FPGAs | 
| g2\.2xlarge | 8 | 15\.00 | 1 GPUs | 
| g2\.8xlarge | 32 | 60\.00 | 4 GPUs | 
| g3\.4xlarge | 16 | 122\.00 | 1 GPUs | 
| g3\.8xlarge | 32 | 244\.00 | 2 GPUs | 
| g3\.16xlarge | 64 | 488\.00 | 4 GPUs | 
| g4ad\.xlarge | 4 | 16\.00 | 1 GPUs | 
| g4ad\.2xlarge | 8 | 32\.00 | 1 GPUs | 
| g4ad\.4xlarge | 16 | 64\.00 | 1 GPUs | 
| g4ad\.8xlarge | 32 | 128\.00 | 2 GPUs | 
| g4ad\.16xlarge | 64 | 256\.00 | 4 GPUs | 
| g4dn\.xlarge | 4 | 16\.00 | 1 GPUs | 
| g4dn\.2xlarge | 8 | 32\.00 | 1 GPUs | 
| g4dn\.4xlarge | 16 | 64\.00 | 1 GPUs | 
| g4dn\.8xlarge | 32 | 128\.00 | 1 GPUs | 
| g4dn\.12xlarge | 48 | 192\.00 | 4 GPUs | 
| g4dn\.16xlarge | 64 | 256\.00 | 1 GPUs | 
| g4dn\.metal | 96 | 384\.00 | 8 GPUs | 
| g5\.xlarge | 4 | 16\.00 | 1 GPUs | 
| g5\.2xlarge | 8 | 32\.00 | 1 GPUs | 
| g5\.4xlarge | 16 | 64\.00 | 1 GPUs | 
| g5\.8xlarge | 32 | 128\.00 | 1 GPUs | 
| g5\.12xlarge | 48 | 192\.00 | 4 GPUs | 
| g5\.16xlarge | 64 | 256\.00 | 1 GPUs | 
| g5\.24xlarge | 96 | 384\.00 | 4 GPUs | 
| g5\.48xlarge | 192 | 768\.00 | 8 GPUs | 
| g5g\.xlarge | 4 | 8\.00 | 1 GPUs | 
| g5g\.2xlarge | 8 | 16\.00 | 1 GPUs | 
| g5g\.4xlarge | 16 | 32\.00 | 1 GPUs | 
| g5g\.8xlarge | 32 | 64\.00 | 1 GPUs | 
| g5g\.16xlarge | 64 | 128\.00 | 2 GPUs | 
| g5g\.metal | 64 | 128\.00 | 2 GPUs | 
| inf1\.xlarge | 4 | 8\.00 | 1 inference accelerators | 
| inf1\.2xlarge | 8 | 16\.00 | 1 inference accelerators | 
| inf1\.6xlarge | 24 | 48\.00 | 4 inference accelerators | 
| inf1\.24xlarge | 96 | 192\.00 | 16 inference accelerators | 
| p2\.xlarge | 4 | 61\.00 | 1 GPUs | 
| p2\.8xlarge | 32 | 488\.00 | 8 GPUs | 
| p2\.16xlarge | 64 | 732\.00 | 16 GPUs | 
| p3\.2xlarge | 8 | 61\.00 | 1 GPUs | 
| p3\.8xlarge | 32 | 244\.00 | 4 GPUs | 
| p3\.16xlarge | 64 | 488\.00 | 8 GPUs | 
| p3dn\.24xlarge | 96 | 768\.00 | 8 GPUs | 
| p4d\.24xlarge | 96 | 1152\.00 | 8 GPUs | 
| p4de\.24xlarge | 96 | 1152\.00 | 8 GPUs | 
| trn1\.2xlarge | 8 | 32\.00 | 
| trn1\.32xlarge | 128 | 512\.00 | 
| vt1\.3xlarge | 12 | 24\.00 | 
| vt1\.6xlarge | 24 | 48\.00 | 
| vt1\.24xlarge | 96 | 192\.00 | 

**Note**  
**Trn1** instances feature the following number of Trainium Accelerators\.  
`trn1.2xlarge` — 1
`trn1.32xlarge` — 16
**VT1** instances feature the following number of U30 Accelerators\.  
`vt1.3xlarge` — 1
`vt1.6xlarge` — 2
`vt1.24xlarge` — 16

The accelerated computing instances use the following processors\.

**AWS Graviton processors**
+ **AWS Graviton2**: G5g

**AMD processors**
+ **2nd generation AMD EPYC processors \(AMD EPYC 7R32\)**: G4ad, G5

**Intel processors**
+ **Intel Xeon Scalable processors \(Broadwell E5\-2686 v4\)**: `F1`G3, P2, P3
+ **Intel Xeon Scalable processors \(Skylake 8175\)**: P3dn
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake P\-8275CL\)**: DL1, P4d, `P4de`
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake P\-8259CL\)**: VT1
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake P\-8259L\)**: G4dn, Inf1

For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Instance performance<a name="gpu-instance-performance"></a>

There are several GPU setting optimizations that you can perform to achieve the best performance on your instances\. For more information, see [Optimize GPU settings](optimize_gpu.md)\.

EBS\-optimized instances enable you to get consistently high performance for your EBS volumes by eliminating contention between Amazon EBS I/O and other network traffic from your instance\. Some accelerated computing instances are EBS\-optimized by default at no additional cost\. For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

Some accelerated computing instance types provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(in CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="gpu-network-performance"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for accelerated computing instances that support enhanced networking\.


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | Enhanced networking features | 
| --- | --- | --- | --- | 
| dl1\.24xlarge | 400\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| g4ad\.xlarge † | 2\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4ad\.2xlarge † | 4\.167 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4ad\.4xlarge † | 8\.333 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4ad\.8xlarge | 15\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4ad\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4dn\.xlarge † | 5\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4dn\.2xlarge † | 10\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4dn\.4xlarge † | 20\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4dn\.8xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| g4dn\.12xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| g4dn\.16xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g4dn\.metal | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| g5\.xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.2xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.4xlarge † | 10\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.8xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.12xlarge | 40\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.24xlarge | 50\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5\.48xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| g5g\.xlarge † | 1\.25 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5g\.2xlarge † | 2\.5 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5g\.4xlarge † | 5\.0 | 10\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5g\.8xlarge | 12\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5g\.16xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| g5g\.metal | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| inf1\.xlarge † | 5\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| inf1\.2xlarge † | 5\.0 | 25\.0 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| inf1\.6xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| inf1\.24xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| p3dn\.24xlarge | 100\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| p4d\.24xlarge | 400\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| p4de\.24xlarge | 400\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| trn1\.2xlarge † | 3\.125 | 12\.5 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| trn1\.32xlarge | 800\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| vt1\.3xlarge | 3\.125 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| vt1\.6xlarge | 6\.25 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| vt1\.24xlarge | 25\.0 | \- | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.

## Amazon EBS I/O performance<a name="accelerated-ebs-perf"></a>

Amazon EBS optimized instances use an optimized configuration stack and provide additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your Amazon EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## SSD\-based instance store volume I/O performance<a name="accelerated-computing-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| g4ad\.xlarge | 10,417 | 8,333 | 
| g4ad\.2xlarge | 20,833 | 16,667 | 
| g4ad\.4xlarge | 41,667 | 33,333 | 
| g4ad\.8xlarge | 83,333 | 66,667 | 
| g4ad\.16xlarge | 166,667 | 133,333 | 
| g5\.xlarge | 40,625 | 20,313 | 
| g5\.2xlarge | 40,625 | 20,313 | 
| g5\.4xlarge | 125,000 | 62,500 | 
| g5\.8xlarge | 250,000 | 125,000 | 
| g5\.12xlarge | 312,500 | 156,250 | 
| g5\.16xlarge | 250,000 | 125,000 | 
| g5\.24xlarge | 312,500 | 156,250 | 
| g5\.48xlarge | 625,000 | 312,500 | 

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Release notes<a name="gpu-instance-release-notes"></a>
+ You must launch the instance using an HVM AMI\.
+ Instances built on the [Nitro System](instance-types.md#ec2-nitro-instances) have the following requirements:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The following Linux AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2018\.03
  + Ubuntu 14\.04 or later with `linux-aws` kernel
**Note**  
AWS Graviton\-based instance types require Ubuntu 18\.04 or later with `linux-aws` kernel
  + Red Hat Enterprise Linux 7\.4 or later
  + SUSE Linux Enterprise Server 12 SP2 or later
  + CentOS 7\.4\.1708 or later
  + FreeBSD 11\.1 or later
  + Debian GNU/Linux 9 or later
+ GPU\-based instances can't access the GPU unless the NVIDIA drivers are installed\. For more information, see [Install NVIDIA drivers on Linux instances](install-nvidia-driver.md)\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ There is a limit of 100 AFIs per Region\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.

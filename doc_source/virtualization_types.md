# Linux AMI Virtualization Types<a name="virtualization_types"></a>

Linux Amazon Machine Images use one of two types of virtualization: paravirtual \(PV\) or hardware virtual machine \(HVM\)\. The main differences between PV and HVM AMIs are the way in which they boot and whether they can take advantage of special hardware extensions \(CPU, network, and storage\) for better performance\.

For the best performance, we recommend that you use current generation instance types and HVM AMIs when you launch your instances\. For more information about current generation instance types, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\. If you are using previous generation instance types and would like to upgrade, see [Upgrade Paths](https://aws.amazon.com/ec2/previous-generation/#Upgrade_Paths)\.

**HVM AMIs**  
HVM AMIs are presented with a fully virtualized set of hardware and boot by executing the master boot record of the root block device of your image\. This virtualization type provides the ability to run an operating system directly on top of a virtual machine without any modification, as if it were run on the bare\-metal hardware\. The Amazon EC2 host system emulates some or all of the underlying hardware that is presented to the guest\.

Unlike PV guests, HVM guests can take advantage of hardware extensions that provide fast access to the underlying hardware on the host system\. For more information on CPU virtualization extensions available in Amazon EC2, see [Intel Virtualization Technology](http://www.intel.com/content/www/us/en/virtualization/virtualization-technology/intel-virtualization-technology.html) on the Intel website\. HVM AMIs are required to take advantage of enhanced networking and GPU processing\. In order to pass through instructions to specialized network and GPU devices, the OS needs to be able to have access to the native hardware platform; HVM virtualization provides this access\. For more information, see [Enhanced Networking on Linux](enhanced-networking.md) and [Linux Accelerated Computing Instances](accelerated-computing-instances.md)\.

All instance types support HVM AMIs\.

To find an HVM AMI, verify that the virtualization type of the AMI is set to `hvm`, using the console or the [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

**PV AMIs**  
PV AMIs boot with a special boot loader called PV\-GRUB, which starts the boot cycle and then chain loads the kernel specified in the `menu.lst` file on your image\. Paravirtual guests can run on host hardware that does not have explicit support for virtualization, but they cannot take advantage of special hardware extensions such as enhanced networking or GPU processing\. Historically, PV guests had better performance than HVM guests in many cases, but because of enhancements in HVM virtualization and the availability of PV drivers for HVM AMIs, this is no longer true\. For more information about PV\-GRUB and its use in Amazon EC2, see [User Provided Kernels](UserProvidedKernels.md)\.

The following previous generation instance types support PV AMIs: C1, C3, HS1, M1, M3, M2, and T1\. Current generation instance types do not support PV AMIs\.

The following AWS regions support PV instances: Asia Pacific \(Tokyo\), Asia Pacific \(Singapore\), Asia Pacific \(Sydney\), EU \(Frankfurt\), EU \(Ireland\), South America \(São Paulo\), US East \(N\. Virginia\), US West \(N\. California\), and US West \(Oregon\)\.

To find a PV AMI, verify that the virtualization type of the AMI is set to `paravirtual`, using the console or the [describe\-images](http://docs.aws.amazon.com/cli/latest/reference/ec2/describe-images.html) command\.

**PV on HVM**  
Paravirtual guests traditionally performed better with storage and network operations than HVM guests because they could leverage special drivers for I/O that avoided the overhead of emulating network and disk hardware, whereas HVM guests had to translate these instructions to emulated hardware\. Now PV drivers are available for HVM guests, so operating systems that cannot be ported to run in a paravirtualized environment can still see performance advantages in storage and network I/O by using them\. With these PV on HVM drivers, HVM guests can get the same, or better, performance than paravirtual guests\.
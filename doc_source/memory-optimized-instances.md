# Memory optimized instances<a name="memory-optimized-instances"></a>

Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory\.

**R5, R5a, R5b, and R5n instances**

These instances are well suited for the following:
+ High\-performance, relational \(MySQL\) and NoSQL \(MongoDB, Cassandra\) databases\.
+ Distributed web scale cache stores that provide in\-memory caching of key\-value type data \(Memcached and Redis\)\.
+ In\-memory databases using optimized data storage formats and analytics for business intelligence \(for example, SAP HANA\)\.
+ Applications performing real\-time processing of big unstructured data \(financial services, Hadoop/Spark clusters\)\.
+ High\-performance computing \(HPC\) and Electronic Design Automation \(EDA\) applications\.

Bare metal instances, such as `r5.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 R5 Instances](https://aws.amazon.com/ec2/instance-types/r5)\.

**R6g and R6gd instances**

These instances are powered by AWS Graviton2 processors and are ideal for running memory\-intensive workloads, such as the following:
+ Open\-source databases \(for example, MySQL, MariaDB, and PostgreSQL\)
+ In\-memory caches \(for example, Memcached, Redis, and KeyDB\)

Bare metal instances, such as `r6g.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 R6g Instances](https://aws.amazon.com/ec2/instance-types/r6)\.

**R6i instances**

These instances are ideal for running memory\-intensive workloads, such as the following:
+ High\-performance databases \(relational and NoSQL\)
+ In\-memory databases, such as SAP HANA
+ Distributed web scale in\-memory caches, such as Memcached and Redis
+ Real\-time big data analytics, such as Hadoop and Spark clusters

For more information, see [Amazon EC2 R6i Instances](http://aws.amazon.com/ec2/instance-types/r6i)\.

**High memory \(u\-\*\) instances**  
These instances offer 3 TiB, 6 TiB, 9 TiB, 12 TiB, 18 TiB, and 24 TiB of memory per instance\. They are designed to run large in\-memory databases, including production deployments of the SAP HANA in\-memory database\.

For more information, see [Amazon EC2 High Memory Instances](http://aws.amazon.com/ec2/instance-types/high-memory/) and [Storage Configuration for SAP HANA](https://docs.aws.amazon.com/quickstart/latest/sap-hana/storage.html)\. For information about supported operating systems, see [Migrating SAP HANA on AWS to an EC2 High Memory Instance](https://docs.aws.amazon.com/sap/latest/sap-hana/migrating-hana-to-hm.html)\. 

**X1 instances**

These instances are well suited for the following:
+ In\-memory databases such as SAP HANA, including SAP\-certified support for Business Suite S/4HANA, Business Suite on HANA \(SoH\), Business Warehouse on HANA \(BW\), and Data Mart Solutions on HANA\. For more information, see [SAP HANA on the AWS Cloud](https://aws.amazon.com/sap/solutions/saphana/)\.
+ Big\-data processing engines such as Apache Spark or Presto\.
+ High\-performance computing \(HPC\) applications\.

For more information, see [Amazon EC2 X1 Instances](https://aws.amazon.com/ec2/instance-types/x1)\.

**X1e instances**

These instances are well suited for the following:
+ High\-performance databases\.
+ In\-memory databases such as SAP HANA\. For more information, see [SAP HANA on the AWS Cloud](https://aws.amazon.com/sap/solutions/saphana/)\.
+ Memory\-intensive enterprise applications\.

For more information, see [Amazon EC2 X1e Instances](https://aws.amazon.com/ec2/instance-types/x1e)\.

**X2gd instances**

These instances are well suited for the following:
+ In\-memory databases, such as Redis and Memcached\.
+ Relational databases, such as MySQL and PostGreSQL\.
+ Electronic design automation \(EDA\) workloads, such as physical verification and layout tools\.
+ Memory\-intensive workloads, such as real\-time analytics and real\-time caching servers\.

For more information, see [Amazon EC2 X2g Instances](https://aws.amazon.com/ec2/instance-types/x2g/)\.

**X2idn, X2iedn, and X2iezn instances**

These instances are well suited for the following:
+ In\-memory databases, such as Redis and Memcached\.
+ Relational databases, such as MySQL and PostGreSQL\.
+ Electronic design automation \(EDA\) workloads, such as physical verification and layout tools\.
+ Memory\-intensive workloads, such as real\-time analytics and real\-time caching servers\.

X2idn and X2iedn instances support `io2` Block Express volumes\. All `io2` volumes attached to X2idn and X2iedn instances, during or after launch, automatically run on EBS Block Express\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express)\.

For more information, see [Amazon EC2 X2i Instances](https://aws.amazon.com/ec2/instance-types/x2i/)\.

**z1d instances**

These instances deliver both high compute and high memory and are well\-suited for the following:
+ Electronic Design Automation \(EDA\)
+ Relational database workloads

`z1d.metal` instances provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 z1d Instances](https://aws.amazon.com/ec2/instance-types/z1d)\.

**Topics**
+ [Hardware specifications](#memory-instances-hardware)
+ [Memory performance](#memory-perf)
+ [Instance performance](#memory-compute-perf)
+ [Network performance](#memory-network-perf)
+ [SSD I/O performance](#instances-ssd-perf)
+ [Instance features](#memory-instances-features)
+ [Support for vCPUs](#high-cpu-support)
+ [Release notes](#memory-instance-limits)

## Hardware specifications<a name="memory-instances-hardware"></a>

The following is a summary of the hardware specifications for memory optimized instances\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| r4\.large | 2 | 15\.25 | 
| r4\.xlarge | 4 | 30\.5 | 
| r4\.2xlarge | 8 | 61 | 
| r4\.4xlarge | 16 | 122 | 
| r4\.8xlarge | 32 | 244 | 
| r4\.16xlarge | 64 | 488 | 
| r5\.large | 2 | 16 | 
| r5\.xlarge | 4 | 32 | 
| r5\.2xlarge | 8 | 64 | 
| r5\.4xlarge | 16 | 128 | 
| r5\.8xlarge | 32 | 256 | 
| r5\.12xlarge | 48 | 384 | 
| r5\.16xlarge | 64 | 512 | 
| r5\.24xlarge | 96 | 768 | 
| r5\.metal | 96 | 768 | 
| r5a\.large | 2 | 16 | 
| r5a\.xlarge | 4 | 32 | 
| r5a\.2xlarge | 8 | 64 | 
| r5a\.4xlarge | 16 | 128 | 
| r5a\.8xlarge | 32 | 256 | 
| r5a\.12xlarge | 48 | 384 | 
| r5a\.16xlarge | 64 | 512 | 
| r5a\.24xlarge | 96 | 768 | 
| r5ad\.large | 2 | 16 | 
| r5ad\.xlarge | 4 | 32 | 
| r5ad\.2xlarge | 8 | 64 | 
| r5ad\.4xlarge | 16 | 128 | 
| r5ad\.8xlarge | 32 | 256 | 
| r5ad\.12xlarge | 48 | 384 | 
| r5ad\.16xlarge | 64 | 512 | 
| r5ad\.24xlarge | 96 | 768 | 
| r5b\.large | 2 | 16 | 
| r5b\.xlarge | 4 | 32 | 
| r5b\.2xlarge | 8 | 64 | 
| r5b\.4xlarge | 16 | 128 | 
| r5b\.8xlarge | 32 | 256 | 
| r5b\.12xlarge | 48 | 384 | 
| r5b\.16xlarge | 64 | 512 | 
| r5b\.24xlarge | 96 | 768 | 
| r5b\.metal | 96 | 768 | 
| r5d\.large | 2 | 16 | 
| r5d\.xlarge | 4 | 32 | 
| r5d\.2xlarge | 8 | 64 | 
| r5d\.4xlarge | 16 | 128 | 
| r5d\.8xlarge | 32 | 256 | 
| r5d\.12xlarge | 48 | 384 | 
| r5d\.16xlarge | 64 | 512 | 
| r5d\.24xlarge | 96 | 768 | 
| r5d\.metal | 96 | 768 | 
| r5dn\.large | 2 | 16 | 
| r5dn\.xlarge | 4 | 32 | 
| r5dn\.2xlarge | 8 | 64 | 
| r5dn\.4xlarge | 16 | 128 | 
| r5dn\.8xlarge | 32 | 256 | 
| r5dn\.12xlarge | 48 | 384 | 
| r5dn\.16xlarge | 64 | 512 | 
| r5dn\.24xlarge | 96 | 768 | 
| r5dn\.metal | 96 | 768 | 
| r5n\.large | 2 | 16 | 
| r5n\.xlarge | 4 | 32 | 
| r5n\.2xlarge | 8 | 64 | 
| r5n\.4xlarge | 16 | 128 | 
| r5n\.8xlarge | 32 | 256 | 
| r5n\.12xlarge | 48 | 384 | 
| r5n\.16xlarge | 64 | 512 | 
| r5n\.24xlarge | 96 | 768 | 
| r5n\.metal | 96 | 768 | 
| r6g\.medium | 1 | 8 | 
| r6g\.large | 2 | 16 | 
| r6g\.xlarge | 4 | 32 | 
| r6g\.2xlarge | 8 | 64 | 
| r6g\.4xlarge | 16 | 128 | 
| r6g\.8xlarge | 32 | 256 | 
| r6g\.12xlarge | 48 | 384 | 
| r6g\.16xlarge | 64 | 512 | 
| r6gd\.medium | 1 | 8 | 
| r6gd\.large | 2 | 16 | 
| r6gd\.xlarge | 4 | 32 | 
| r6gd\.2xlarge | 8 | 64 | 
| r6gd\.4xlarge | 16 | 128 | 
| r6gd\.8xlarge | 32 | 256 | 
| r6gd\.12xlarge | 48 | 384 | 
| r6gd\.16xlarge | 64 | 512 | 
| r6i\.large | 2 | 16 | 
| r6i\.xlarge | 4 | 32 | 
| r6i\.2xlarge | 8 | 64 | 
| r6i\.4xlarge | 16 | 128 | 
| r6i\.8xlarge | 32 | 256 | 
| r6i\.12xlarge | 48 | 384 | 
| r6i\.16xlarge | 64 | 512 | 
| r6i\.24xlarge | 96 | 768 | 
| r6i\.32xlarge | 128 | 1,024 | 
| r6i\.metal | 128 | 1,024 | 
| u\-3tb1\.56xlarge | 224 | 3,072 | 
| u\-6tb1\.56xlarge | 224 | 6,144 | 
| u\-6tb1\.112xlarge | 448 | 6,144 | 
| u\-6tb1\.metal | 448 \* | 6,144 | 
| u\-9tb1\.112xlarge | 448 | 9,216 | 
| u\-9tb1\.metal | 448 \* | 9,216 | 
| u\-12tb1\.112xlarge | 448 | 12,288 | 
| u\-12tb1\.metal | 448 \* | 12,288 | 
| u\-18tb1\.metal | 448 \* | 18,432 | 
| u\-24tb1\.metal | 448 \* | 24,576 | 
| x1\.16xlarge | 64 | 976 | 
| x1\.32xlarge | 128 | 1,952 | 
| x1e\.xlarge | 4 | 122 | 
| x1e\.2xlarge | 8 | 244 | 
| x1e\.4xlarge | 16 | 488 | 
| x1e\.8xlarge | 32 | 976 | 
| x1e\.16xlarge | 64 | 1,952 | 
| x1e\.32xlarge | 128 | 3,904 | 
| x2gd\.medium | 1 | 16 | 
| x2gd\.large | 2 | 32 | 
| x2gd\.xlarge | 4 | 64 | 
| x2gd\.2xlarge | 8 | 128 | 
| x2gd\.4xlarge | 16 | 256 | 
| x2gd\.8xlarge | 32 | 512 | 
| x2gd\.12xlarge | 48 | 768 | 
| x2gd\.16xlarge | 64 | 1,024 | 
| x2gd\.metal | 64 | 1,024 | 
| x2idn\.16xlarge | 64 | 1,024 | 
| x2idn\.24xlarge | 96 | 1,536 | 
| x2idn\.32xlarge | 128 | 2,048 | 
| x2iedn\.xlarge | 4 | 128 | 
| x2iedn\.2xlarge | 8 | 256 | 
| x2iedn\.4xlarge | 16 | 512 | 
| x2iedn\.8xlarge | 32 | 1,024 | 
| x2iedn\.16xlarge | 64 | 2,048 | 
| x2iedn\.24xlarge | 96 | 3,072 | 
| x2iedn\.32xlarge | 128 | 4,096 | 
| x2iezn\.2xlarge | 8 | 256 | 
| x2iezn\.4xlarge | 16 | 512 | 
| x2iezn\.6xlarge | 24 | 768 | 
| x2iezn\.8xlarge | 32 | 1,024 | 
| x2iezn\.12xlarge | 48 | 1,536 | 
| x2iezn\.metal | 48 | 1,536 | 
| z1d\.large | 2 | 16 | 
| z1d\.xlarge | 4 | 32 | 
| z1d\.2xlarge | 8 | 64 | 
| z1d\.3xlarge | 12 | 96 | 
| z1d\.6xlarge | 24 | 192 | 
| z1d\.12xlarge | 48 | 384 | 
| z1d\.metal | 48 | 384 | 

\* Each logical processor is a hyperthread on 224 cores\.

For more information about the hardware specifications for each Amazon EC2 instance type, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

For more information about specifying CPU options, see [Optimize CPU options](instance-optimize-cpu.md)\.

## Memory performance<a name="memory-perf"></a>

X1 instances include Intel Scalable M​​emory Buffers, providing 300 GiB/s of sustainable memory\-read bandwidth and 140 GiB/s of sustainable memory\-write bandwidth\.

For more information about how much RAM can be enabled for memory optimized instances, see [Hardware specifications](#memory-instances-hardware)\.

Memory optimized instances have high memory and require 64\-bit HVM AMIs to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on memory optimized instances\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.

## Instance performance<a name="memory-compute-perf"></a>

Memory optimized instances enable increased cryptographic performance through the latest Intel AES\-NI feature, support Intel Transactional Synchronization Extensions \(TSX\) to boost the performance of in\-memory transactional data processing, and support Advanced Vector Extensions 2 \(Intel AVX2\) processor instructions to expand most integer commands to 256 bits\.

Some memory optimized instances provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(measured by CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="memory-network-perf"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for memory optimized instances that support enhanced networking\.


| Instance type | Network performance | Enhanced networking | 
| --- | --- | --- | 
|  r4\.4xlarge and smaller \| r5\.4xlarge and smaller \| r5a\.8xlarge and smaller \| r5ad\.8xlarge and smaller \| r5b\.4xlarge and smaller \| r5d\.4xlarge and smaller \| r6g\.4xlarge and smaller \|  r6gd\.4xlarge and smaller \|  x1e\.8xlarge and smaller \| x2gd\.4xlarge and smaller \|  z1d\.3xlarge and smaller  | Up to 10 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  r4\.8xlarge \| r5\.8xlarge \| r5\.12xlarge \| r5a\.12xlarge \| r5ad\.12xlarge \| r5b\.8xlarge \| r5b\.12xlarge \| r5d\.8xlarge \| r5d\.12xlarge \| x1\.16xlarge \| x1e\.16xlarge \| z1d\.6xlarge  | 10 Gbps | [ENA](enhanced-networking-ena.md) | 
|  r5a\.16xlarge \| r5ad\.16xlarge  \| r6g\.8xlarge \| r6gd\.8xlarge  \| x2gd\.8xlarge  | 12 Gbps | [ENA](enhanced-networking-ena.md) | 
|  r6i\.4xlarge and smaller  | Up to 12\.5 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  r6i\.8xlarge  | 12\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
|  r6i\.12xlarge  | 18\.75 Gbps | [ENA](enhanced-networking-ena.md) | 
|  r5\.16xlarge \| r5a\.24xlarge \| r5ad\.24xlarge \| r5b\.16xlarge \| r5d\.16xlarge  \| r6g\.12xlarge \| r6gd\.12xlarge  \| x2gd\.12xlarge  | 20 Gbps | [ENA](enhanced-networking-ena.md) | 
| r5dn\.4xlarge and smaller \| r5n\.4xlarge and smaller \| x2iedn\.4xlarge and smaller \| x2iezn\.4xlarge and smaller | Up to 25 Gbps † | [ENA](enhanced-networking-ena.md) | 
|  r4\.16xlarge \| r5\.24xlarge \| r5\.metal \| r5b\.24xlarge \| r5b\.metal \| r5d\.24xlarge \| r5d\.metal \| r5dn\.8xlarge \| r5n\.8xlarge \| r6g\.16xlarge \| r6g\.metal \|  r6gd\.16xlarge \| r6gd\.metal \|  r6i\.16xlarge \| x1\.32xlarge \| x1e\.32xlarge \| x2gd\.16xlarge \|  x2gd\.metal \|  x2iedn\.8xlarge \| z1d\.12xlarge \| z1d\.metal  | 25 Gbps | [ENA](enhanced-networking-ena.md) | 
|  r6i\.24xlarge  | 37\.5 Gbps | [ENA](enhanced-networking-ena.md) | 
| r5dn\.12xlarge \| r5n\.12xlarge \| r6i\.32xlarge \| r6i\.metal \| u\-3tb1\.56xlarge \| x2idn\.16xlarge \| x2iedn\.16xlarge \| x2iezn\.6xlarge  | 50 Gbps | [ENA](enhanced-networking-ena.md) | 
| r5dn\.16xlarge \| r5n\.16xlarge \| x2idn\.24xlarge \| x2iedn\.24xlarge \| x2iezn\.8xlarge | 75 Gbps | [ENA](enhanced-networking-ena.md) | 
|  r5dn\.24xlarge \| r5dn\.metal \| r5n\.24xlarge \| r5n\.metal \| u\-6tb1\.56xlarge \| u\-6tb1\.112xlarge \| u\-6tb1\.metal \* \| u\-9tb1\.112xlarge \| u\-9tb1\.metal \* \| u\-12tb1\.112xlarge \| u\-12tb1\.metal \* \| u\-18tb1\.metal \| u\-24tb1\.metal \| x2idn\.32xlarge \| x2iedn\.32xlarge \| x2iezn\.12xlarge \| x2iezn\.metal  | 100 Gbps | [ENA](enhanced-networking-ena.md) | 

\* Instances of this type launched after March 12, 2020 provide network performance of 100 Gbps\. Instances of this type launched before March 12, 2020 might only provide network performance of 25 Gbps\. To ensure that instances launched before March 12, 2020 have a network performance of 100 Gbps, contact your account team to upgrade your instance at no additional cost\.

† These instances have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.<a name="baseline-bandwidth"></a>


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | 
| --- | --- | --- | 
| r4\.large | \.75 | 10 | 
| r4\.xlarge | 1\.25 | 10 | 
| r4\.2xlarge | 2\.5 | 10 | 
| r4\.4xlarge | 5 | 10 | 
| r5\.large | \.75 | 10 | 
| r5\.xlarge | 1\.25 | 10 | 
| r5\.2xlarge | 2\.5 | 10 | 
| r5\.4xlarge | 5 | 10 | 
| r5a\.large | \.75 | 10 | 
| r5a\.xlarge | 1\.25 | 10 | 
| r5a\.2xlarge | 2\.5 | 10 | 
| r5a\.4xlarge | 5 | 10 | 
| r5a\.8xlarge | 7\.5 | 10 | 
| r5ad\.large | \.75 | 10 | 
| r5ad\.xlarge | 1\.25 | 10 | 
| r5ad\.2xlarge | 2\.5 | 10 | 
| r5ad\.4xlarge | 5 | 10 | 
| r5ad\.8xlarge | 7\.5 | 10 | 
| r5b\.large | \.75 | 10 | 
| r5b\.xlarge | 1\.25 | 10 | 
| r5b\.2xlarge | 2\.5 | 10 | 
| r5b\.4xlarge | 5 | 10 | 
| r5d\.large | \.75 | 10 | 
| r5d\.xlarge | 1\.25 | 10 | 
| r5d\.2xlarge | 2\.5 | 10 | 
| r5d\.4xlarge | 5 | 10 | 
| r5dn\.large | 2\.1 | 25 | 
| r5dn\.xlarge | 4\.1 | 25 | 
| r5dn\.2xlarge | 8\.125 | 25 | 
| r5dn\.4xlarge | 16\.25 | 25 | 
| r5n\.large | 2\.1 | 25 | 
| r5n\.xlarge | 4\.1 | 25 | 
| r5n\.2xlarge | 8\.125 | 25 | 
| r5n\.4xlarge | 16\.25 | 25 | 
| r6g\.medium | \.5 | 10 | 
| r6g\.large | \.75 | 10 | 
| r6g\.xlarge | 1\.25 | 10 | 
| r6g\.2xlarge | 2\.5 | 10 | 
| r6g\.4xlarge | 5 | 10 | 
| r6gd\.medium | \.5 | 10 | 
| r6gd\.large | \.75 | 10 | 
| r6gd\.xlarge | 1\.25 | 10 | 
| r6gd\.2xlarge | 2\.5 | 10 | 
| r6gd\.4xlarge | 5 | 10 | 
| r6i\.large | \.781 | 12\.5 | 
| r6i\.xlarge | 1\.562 | 12\.5 | 
| r6i\.2xlarge | 3\.125 | 12\.5 | 
| r6i\.4xlarge | 6\.25 | 12\.5 | 
| x1e\.xlarge | \.625 | 10 | 
| x1e\.2xlarge | 1\.25 | 10 | 
| x1e\.4xlarge | 2\.5 | 10 | 
| x1e\.8xlarge | 5 | 10 | 
| x2iedn\.xlarge | 3\.125 | 25 | 
| x2ed\.2xlarge | 6\.25 | 25 | 
| x2iedn\.4xlarge | 12\.5 | 25 | 
| x2gd\.medium | \.5 | 10 | 
| x2gd\.large | \.75 | 10 | 
| x2gd\.xlarge | 1\.25 | 10 | 
| x2gd\.2xlarge | 2\.5 | 10 | 
| x2gd\.4xlarge | 5 | 10 | 
| x2iezn\.2xlarge | 12\.5 | 25 | 
| x2iezn\.4xlarge | 15 | 25 | 
| z1d\.large | \.75 | 10 | 
| z1d\.xlarge | 1\.25 | 10 | 
| z1d\.2xlarge | 2\.5 | 10 | 
| z1d\.3xlarge | 5 | 10 | 

## SSD I/O performance<a name="instances-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| r5ad\.large | 30,000 | 15,000 | 
| r5ad\.xlarge | 59,000 | 29,000 | 
| r5ad\.2xlarge  | 117,000 | 57,000 | 
| r5ad\.4xlarge | 234,000 | 114,000 | 
| r5ad\.8xlarge | 466,666 | 233,333 | 
| r5ad\.12xlarge | 700,000 | 340,000 | 
| r5ad\.16xlarge | 933,333 | 466,666 | 
| r5ad\.24xlarge | 1,400,000 | 680,000 | 
| r5d\.large | 30,000 | 15,000 | 
| r5d\.xlarge | 59,000 | 29,000 | 
| r5d\.2xlarge | 117,000 | 57,000 | 
| r5d\.4xlarge | 234,000 | 114,000 | 
| r5d\.8xlarge | 466,666 | 233,333 | 
| r5d\.12xlarge | 700,000 | 340,000 | 
| r5d\.16xlarge  | 933,333 | 466,666 | 
| r5d\.24xlarge | 1,400,000 | 680,000 | 
| r5d\.metal | 1,400,000 | 680,000 | 
| r5dn\.large | 30,000 | 15,000 | 
| r5dn\.xlarge | 59,000 | 29,000 | 
| r5dn\.2xlarge | 117,000 | 57,000 | 
| r5dn\.4xlarge | 234,000 | 114,000 | 
| r5dn\.8xlarge | 466,666 | 233,333 | 
| r5dn\.12xlarge | 700,000 | 340,000 | 
| r5dn\.16xlarge | 933,333 | 466,666 | 
| r5dn\.24xlarge | 1,400,000 | 680,000 | 
| r5dn\.metal | 1,400,000 | 680,000 | 
| r6gd\.medium | 13,438 | 5,625 | 
| r6gd\.large | 26,875 | 11,250 | 
| r6gd\.xlarge | 53,750 | 22,500 | 
| r6gd\.2xlarge | 107,500 | 45,000 | 
| r6gd\.4xlarge | 215,000 | 90,000 | 
| r6gd\.8xlarge | 430,000 | 180,000 | 
| r6gd\.12xlarge | 645,000 | 270,000 | 
| r6gd\.16xlarge | 860,000 | 360,000 | 
| r6gd\.metal | 860,000 | 360,000 | 
| x2gd\.medium | 13,438 | 5,625 | 
| x2gd\.large | 26,875 | 11,250 | 
| x2gd\.xlarge | 53,750 | 22,500 | 
| x2gd\.2xlarge | 107,500 | 45,000 | 
| x2gd\.4xlarge | 215,000 | 90,000 | 
| x2gd\.8xlarge | 430,000 | 180,000 | 
| x2gd\.12xlarge | 645,000 | 270,000 | 
| x2gd\.16xlarge | 860,000 | 360,000 | 
| x2gd\.metal | 860,000 | 360,000 | 
| x2idn\.16xlarge | 430,000 | 180,000 | 
| x2idn\.24xlarge | 645,000 | 270,000 | 
| x2idn\.32xlarge | 860,000 | 360,000 | 
| x2iedn\.xlarge | 26,875 | 11,250 | 
| x2iedn\.2xlarge | 53,750 | 22,500 | 
| x2iedn\.4xlarge | 107,500 | 45,000 | 
| x2iedn\.8xlarge | 215,000 | 90,000 | 
| x2iedn\.16xlarge | 430,000 | 180,000 | 
| x2iedn\.24xlarge | 645,000 | 270,000 | 
| x2iedn\.32xlarge | 860,000 | 360,000 | 
| z1d\.large | 30,000 | 15,000 | 
| z1d\.xlarge | 59,000 | 29,000 | 
| z1d\.2xlarge | 117,000 | 57,000 | 
| z1d\.3xlarge | 175,000 | 75,000 | 
| z1d\.6xlarge | 350,000 | 170,000 | 
| z1d\.12xlarge | 700,000 | 340,000 | 
| z1d\.metal | 700,000 | 340,000 | 

As you fill the SSD\-based instance store volumes for your instance, the number of write IOPS that you can achieve decreases\. This is due to the extra work the SSD controller must do to find available space, rewrite existing data, and erase unused space so that it can be rewritten\. This process of garbage collection results in internal write amplification to the SSD, expressed as the ratio of SSD write operations to user write operations\. This decrease in performance is even larger if the write operations are not in multiples of 4,096 bytes or not aligned to a 4,096\-byte boundary\. If you write a smaller amount of bytes or bytes that are not aligned, the SSD controller must read the surrounding data and store the result in a new location\. This pattern results in significantly increased write amplification, increased latency, and dramatically reduced I/O performance\.

SSD controllers can use several strategies to reduce the impact of write amplification\. One such strategy is to reserve space in the SSD instance storage so that the controller can more efficiently manage the space available for write operations\. This is called *over\-provisioning*\. The SSD\-based instance store volumes provided to an instance don't have any space reserved for over\-provisioning\. To reduce write amplification, we recommend that you leave 10% of the volume unpartitioned so that the SSD controller can use it for over\-provisioning\. This decreases the storage that you can use, but increases performance even if the disk is close to full capacity\.

For instance store volumes that support TRIM, you can use the TRIM command to notify the SSD controller whenever you no longer need data that you've written\. This provides the controller with more free space, which can reduce write amplification and increase performance\. For more information, see [Instance store volume TRIM support](ssd-instance-store.md#InstanceStoreTrimSupport)\.

## Instance features<a name="memory-instances-features"></a>

The following is a summary of features for memory optimized instances\.


|  | EBS only | NVMe EBS | Instance store | Placement group | 
| --- | --- | --- | --- | --- | 
| R4 | Yes | No | No | Yes | 
| R5 | Yes | Yes | No | Yes | 
| R5a | Yes | Yes | No | Yes | 
| R5ad | No | Yes | NVME \* | Yes | 
| R5b | Yes \*\* | Yes | No | Yes | 
| R5d | No | Yes | NVME \* | Yes | 
| R5dn | No | Yes | NVME \* | Yes | 
| R5n | Yes | Yes | No | Yes | 
| R6g | Yes | Yes | No | Yes | 
| R6gd | No | Yes | NVMe \* | Yes | 
| R6i | Yes | Yes | No | Yes | 
| High memory | Yes | Yes | No | Virtualized: Yes Bare metal: No  | 
| X1 | No | No | SSD | Yes | 
| X2gd | No \*\* | Yes | NVME \* | Yes | 
| X2idn | No \*\* | Yes | NVME \* | Yes | 
| X2iedn | No \*\* | Yes | NVME \* | Yes | 
| X2iezn | Yes | Yes | No | Yes | 
| X1e | No | No | SSD \* | Yes | 
| z1d | No | Yes | NVME \* | Yes | 

**\*\*** All `io2` volumes attached to R5b, X2idn, and X2iedn instances, during or after launch, automatically run on EBS Block Express\. For more information, see [io2 Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express)\.

**\*** The root device volume must be an Amazon EBS volume\.

For more information, see the following:
+ [Amazon EBS and NVMe on Linux instances](nvme-ebs-volumes.md)
+ [Amazon EC2 instance store](InstanceStorage.md)
+ [Placement groups](placement-groups.md)

## Support for vCPUs<a name="high-cpu-support"></a>

Memory optimized instances provide a high number of vCPUs, which can cause launch issues with operating systems that have a lower vCPU limit\. We strongly recommend that you use the latest AMIs when you launch memory optimized instances\.

The following AMIs support launching memory optimized instances:
+ Amazon Linux 2 \(HVM\)
+ Amazon Linux AMI 2016\.03 \(HVM\) or later
+ Ubuntu Server 14\.04 LTS \(HVM\)
+ Red Hat Enterprise Linux 7\.1 \(HVM\)
+ SUSE Linux Enterprise Server 12 SP1 \(HVM\)
+ Windows Server 2019
+ Windows Server 2016
+ Windows Server 2012 R2
+ Windows Server 2012
+ Windows Server 2008 R2 64\-bit
+ Windows Server 2008 SP2 64\-bit

## Release notes<a name="memory-instance-limits"></a>
+ R4 instances feature up to 64 vCPUs and are powered by two AWS\-customized Intel XEON processors based on E5\-2686v4 that feature high\-memory bandwidth and larger L3 caches to boost the performance of in\-memory applications\.
+ R5, R5b, and R5d instances feature a 3\.1 GHz Intel Xeon Platinum 8000 series processor from either the first generation \(Skylake\-SP\) or second generation \(Cascade Lake\)\.
+ R5a and R5ad instances feature a 2\.5 GHz AMD EPYC 7000 series processor\.
+ R6g and R6gd instances feature an AWS Graviton2 processor based on 64\-bit Arm architecture\.
+ High memory instances \(`u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal`\) are the first instances to be powered by an eight\-socket platform with the latest generation Intel Xeon Platinum 8176M \(Skylake\) processors that are optimized for mission\-critical enterprise workloads\. High Memory instances with 18 TB and 24 TB of memory \(`u-18tb1.metal` and `u-24tb1.metal`\) are the first instances powered by an 8\-socket platform with 2nd Generation Intel Xeon Scalable 8280L \(Cascade Lake\) processors\.
+ X1e and X1 instances feature up to 128 vCPUs and are powered by four Intel Xeon E7\-8880 v3 processors that feature high\-memory bandwidth and larger L3 caches to boost the performance of in\-memory applications\.
+ X2idn and X2iedn instances feature Intel Xeon Scalable processors \(Ice Lake\)\. They are certified by SAP for running Business Suite on HANA, SAP S/4HANA, Data Mart Solutions on HANA, Business Warehouse on HANA, SAP BW/4HANA, and SAP NetWeaver workloads with anyDB in production environments\. For more information, see [Certified and Supported SAP HANA Hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=v:deCertified;ve:23)\.
+ X2iezn instances feature a custom Intel Xeon Scalable processor \(Cascade Lake\)\.
+ Instances built on the Nitro System have the following requirements:
  + [NVMe drivers](nvme-ebs-volumes.md) must be installed
  + [Elastic Network Adapter \(ENA\) drivers](enhanced-networking-ena.md) must be installed

  The following Linux AMIs meet these requirements:
  + Amazon Linux 2
  + Amazon Linux AMI 2018\.03
  + Ubuntu 14\.04 \(with `linux-aws` kernel\) or later
  + Red Hat Enterprise Linux 7\.4 or later
  + SUSE Linux Enterprise Server 12 SP2 or later
  + CentOS 7\.4\.1708 or later
  + FreeBSD 11\.1 or later
  + Debian GNU/Linux 9 or later
+ Instances with an AWS Graviton processors have the following requirements:
  + Use an AMI for the 64\-bit Arm architecture\.
  + Support booting through UEFI with ACPI tables and support ACPI hot\-plug of PCI devices\.

  The following AMIs meet these requirements:
  + Amazon Linux 2 \(64\-bit Arm\)
  + Ubuntu 16\.04 or later \(64\-bit Arm\)
  + Red Hat Enterprise Linux 8\.0 or later \(64\-bit Arm\)
  + SUSE Linux Enterprise Server 15 or later \(64\-bit Arm\)
  + Debian 10 or later \(64\-bit Arm\)
+ To get the best performance from your R6i instances, ensure that they have ENA driver version 2\.2\.9 or later\. Using an ENA driver earlier than version 1\.2 with these instances causes network interface attachment failures\. The following AMIs have a compatible ENA driver\.
  + Amazon Linux 2 with kernel 4\.14\.186
  + Ubuntu 20\.04 with kernel 5\.4\.0\-1025\-aws
  + Red Hat Enterprise Linux 8\.3 with kernel 4\.18\.0\-240\.1\.1\.el8\_3\.ARCH
  + SUSE Linux Enterprise Server 15 SP2 with kernel 5\.3\.18\-24\.15\.1
+ Instances built on the Nitro System instances support a maximum of 28 attachments, including network interfaces, EBS volumes, and NVMe instance store volumes\. For more information, see [Nitro System volume limits](volume_limits.md#instance-type-volume-limits)\.
+ All `io2` volumes attached to R5b, X2idn, and X2iedn instances, during or after launch, automatically run on EBS Block Express\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express)\.
+ Launching a bare metal instance boots the underlying server, which includes verifying all hardware and firmware components\. This means that it can take 20 minutes from the time the instance enters the running state until it becomes available over the network\.
+ To attach or detach EBS volumes or secondary network interfaces from a bare metal instance requires PCIe native hotplug support\. Amazon Linux 2 and the latest versions of the Amazon Linux AMI support PCIe native hotplug, but earlier versions do not\. You must enable the following Linux kernel configuration options:

  ```
  CONFIG_HOTPLUG_PCI_PCIE=y
  CONFIG_PCIEASPM=y
  ```
+ Bare metal instances use a PCI\-based serial device rather than an I/O port\-based serial device\. The upstream Linux kernel and the latest Amazon Linux AMIs support this device\. Bare metal instances also provide an ACPI SPCR table to enable the system to automatically use the PCI\-based serial device\. The latest Windows AMIs automatically use the PCI\-based serial device\.
+ You can't launch X1 instances using a Windows Server 2008 SP2 64\-bit AMI, except for `x1.16xlarge` instances\.
+ You can't launch X1e instances using a Windows Server 2008 SP2 64\-bit AMI\.
+ With earlier versions of the Windows Server 2008 R2 64\-bit AMI, you can't launch `r4.large` and `r4.4xlarge` instances\. If you experience this issue, update to the latest version of this AMI\.
+ There is a limit on the total number of instances that you can launch in a Region, and there are additional limits on some instance types\. For more information, see [How many instances can I run in Amazon EC2?](https://aws.amazon.com/ec2/faqs/#How_many_instances_can_I_run_in_Amazon_EC2) in the Amazon EC2 FAQ\.
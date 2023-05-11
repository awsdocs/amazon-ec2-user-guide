# Memory optimized instances<a name="memory-optimized-instances"></a>

Memory optimized instances are designed to deliver fast performance for workloads that process large data sets in memory\.

**R5, R5a, R5b, and R5n instances**

These instances are well suited for the following:
+ High\-performance, including relational MySQL and NoSQL, for example MongoDB and Cassandra databases\.
+ Distributed web scale cache stores that provide in\-memory caching of key\-value type data, for example Memcached and Redis
+ In\-memory databases using optimized data storage formats and analytics for business intelligence; for example, SAP HANA
+ Applications performing real\-time processing of big unstructured data, using Hadoop and Spark clusters
+ High\-performance computing \(HPC\) and Electronic Design Automation \(EDA\) applications\.

Bare metal instances, such as `r5.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 R5 Instances](https://aws.amazon.com/ec2/instance-types/r5)\.

**R6a instances**

These instances are ideal for running memory\-intensive workloads, such as the following:
+ High\-performance databases, both relational and NoSQL
+ Distributed web scale in\-memory caches, such as Memcached and Redis
+ Real\-time big data analytics, such as Hadoop and Spark clusters

**Hpc6id instances**

These instances are ideal for running high performance computing \(HPC\) workloads, such as the following:
+ Seismic and Reservoir
+ Crash Simulation
+ Finite Element Analysis

**R6g and R6gd instances**

These instances are powered by AWS Graviton2 processors and are ideal for running memory\-intensive workloads, such as the following:
+ Open\-source databases; for example, MySQL, MariaDB, and PostgreSQL
+ In\-memory caches; for example, Memcached, Redis, and KeyDB

Bare metal instances, such as `r6g.metal`, provide your applications with direct access to physical resources of the host server, such as processors and memory\.

For more information, see [Amazon EC2 R6g Instances](https://aws.amazon.com/ec2/instance-types/r6)\.

**R6i and R6id instances**

These instances are ideal for running memory\-intensive workloads, such as the following:
+ High\-performance databases, relational and NoSQL
+ In\-memory databases, for example SAP HANA
+ Distributed web scale in\-memory caches, for example Memcached and Redis
+ Real\-time big data analytics, including Hadoop and Spark clusters

**R6in and R6idn instances**

These instances are well suited for network\-intensive workloads such as the following:
+ High\-performance relational, MySQL and NoSQL\. For example, MongoDB and Cassandra databases
+ Distributed web scale cache stores that provide in\-memory caching of key\-value type data, including Memcached and Redis
+ In\-memory databases using optimized data storage formats and analytics for business intelligence, for example SAP HANA
+ Real\-time big data analytics for financial services, for example Hadoop and Spark clusters

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
+ [Amazon EBS I/O performance](#memory-ebs-perf)
+ [SSD\-based instance store volume I/O performance](#instances-ssd-perf)
+ [Support for vCPUs](#high-cpu-support)
+ [Release notes](#memory-instance-limits)

## Hardware specifications<a name="memory-instances-hardware"></a>

The following is a summary of the hardware specifications for memory optimized instances\. A virtual central processing unit \(vCPU\) represents a portion of the physical CPU assigned to a virtual machine \(VM\)\. For x86 instances, there are two vCPUs per core\. For Graviton instances, there is one vCPU per core\.


| Instance type | Default vCPUs | Memory \(GiB\) | 
| --- | --- | --- | 
| cr1\.8xlarge | 32 | 243\.76 | 
| hpc6id\.32xlarge | 64 | 1024\.00 | 
| r3\.large | 2 | 15\.00 | 
| r3\.xlarge | 4 | 30\.50 | 
| r3\.2xlarge | 8 | 61\.00 | 
| r3\.4xlarge | 16 | 122\.00 | 
| r3\.8xlarge | 32 | 244\.00 | 
| r4\.large | 2 | 15\.25 | 
| r4\.xlarge | 4 | 30\.50 | 
| r4\.2xlarge | 8 | 61\.00 | 
| r4\.4xlarge | 16 | 122\.00 | 
| r4\.8xlarge | 32 | 244\.00 | 
| r4\.16xlarge | 64 | 488\.00 | 
| r5\.large | 2 | 16\.00 | 
| r5\.xlarge | 4 | 32\.00 | 
| r5\.2xlarge | 8 | 64\.00 | 
| r5\.4xlarge | 16 | 128\.00 | 
| r5\.8xlarge | 32 | 256\.00 | 
| r5\.12xlarge | 48 | 384\.00 | 
| r5\.16xlarge | 64 | 512\.00 | 
| r5\.24xlarge | 96 | 768\.00 | 
| r5\.metal | 96 | 768\.00 | 
| r5a\.large | 2 | 16\.00 | 
| r5a\.xlarge | 4 | 32\.00 | 
| r5a\.2xlarge | 8 | 64\.00 | 
| r5a\.4xlarge | 16 | 128\.00 | 
| r5a\.8xlarge | 32 | 256\.00 | 
| r5a\.12xlarge | 48 | 384\.00 | 
| r5a\.16xlarge | 64 | 512\.00 | 
| r5a\.24xlarge | 96 | 768\.00 | 
| r5ad\.large | 2 | 16\.00 | 
| r5ad\.xlarge | 4 | 32\.00 | 
| r5ad\.2xlarge | 8 | 64\.00 | 
| r5ad\.4xlarge | 16 | 128\.00 | 
| r5ad\.8xlarge | 32 | 256\.00 | 
| r5ad\.12xlarge | 48 | 384\.00 | 
| r5ad\.16xlarge | 64 | 512\.00 | 
| r5ad\.24xlarge | 96 | 768\.00 | 
| r5b\.large | 2 | 16\.00 | 
| r5b\.xlarge | 4 | 32\.00 | 
| r5b\.2xlarge | 8 | 64\.00 | 
| r5b\.4xlarge | 16 | 128\.00 | 
| r5b\.8xlarge | 32 | 256\.00 | 
| r5b\.12xlarge | 48 | 384\.00 | 
| r5b\.16xlarge | 64 | 512\.00 | 
| r5b\.24xlarge | 96 | 768\.00 | 
| r5b\.metal | 96 | 768\.00 | 
| r5d\.large | 2 | 16\.00 | 
| r5d\.xlarge | 4 | 32\.00 | 
| r5d\.2xlarge | 8 | 64\.00 | 
| r5d\.4xlarge | 16 | 128\.00 | 
| r5d\.8xlarge | 32 | 256\.00 | 
| r5d\.12xlarge | 48 | 384\.00 | 
| r5d\.16xlarge | 64 | 512\.00 | 
| r5d\.24xlarge | 96 | 768\.00 | 
| r5d\.metal | 96 | 768\.00 | 
| r5dn\.large | 2 | 16\.00 | 
| r5dn\.xlarge | 4 | 32\.00 | 
| r5dn\.2xlarge | 8 | 64\.00 | 
| r5dn\.4xlarge | 16 | 128\.00 | 
| r5dn\.8xlarge | 32 | 256\.00 | 
| r5dn\.12xlarge | 48 | 384\.00 | 
| r5dn\.16xlarge | 64 | 512\.00 | 
| r5dn\.24xlarge | 96 | 768\.00 | 
| r5dn\.metal | 96 | 768\.00 | 
| r5n\.large | 2 | 16\.00 | 
| r5n\.xlarge | 4 | 32\.00 | 
| r5n\.2xlarge | 8 | 64\.00 | 
| r5n\.4xlarge | 16 | 128\.00 | 
| r5n\.8xlarge | 32 | 256\.00 | 
| r5n\.12xlarge | 48 | 384\.00 | 
| r5n\.16xlarge | 64 | 512\.00 | 
| r5n\.24xlarge | 96 | 768\.00 | 
| r5n\.metal | 96 | 768\.00 | 
| r6a\.large | 2 | 16\.00 | 
| r6a\.xlarge | 4 | 32\.00 | 
| r6a\.2xlarge | 8 | 64\.00 | 
| r6a\.4xlarge | 16 | 128\.00 | 
| r6a\.8xlarge | 32 | 256\.00 | 
| r6a\.12xlarge | 48 | 384\.00 | 
| r6a\.16xlarge | 64 | 512\.00 | 
| r6a\.24xlarge | 96 | 768\.00 | 
| r6a\.32xlarge | 128 | 1024\.00 | 
| r6a\.48xlarge | 192 | 1536\.00 | 
| r6a\.metal | 192 | 1536\.00 | 
| r6g\.medium | 1 | 8\.00 | 
| r6g\.large | 2 | 16\.00 | 
| r6g\.xlarge | 4 | 32\.00 | 
| r6g\.2xlarge | 8 | 64\.00 | 
| r6g\.4xlarge | 16 | 128\.00 | 
| r6g\.8xlarge | 32 | 256\.00 | 
| r6g\.12xlarge | 48 | 384\.00 | 
| r6g\.16xlarge | 64 | 512\.00 | 
| r6g\.metal | 64 | 512\.00 | 
| r6gd\.medium | 1 | 8\.00 | 
| r6gd\.large | 2 | 16\.00 | 
| r6gd\.xlarge | 4 | 32\.00 | 
| r6gd\.2xlarge | 8 | 64\.00 | 
| r6gd\.4xlarge | 16 | 128\.00 | 
| r6gd\.8xlarge | 32 | 256\.00 | 
| r6gd\.12xlarge | 48 | 384\.00 | 
| r6gd\.16xlarge | 64 | 512\.00 | 
| r6gd\.metal | 64 | 512\.00 | 
| r6i\.large | 2 | 16\.00 | 
| r6i\.xlarge | 4 | 32\.00 | 
| r6i\.2xlarge | 8 | 64\.00 | 
| r6i\.4xlarge | 16 | 128\.00 | 
| r6i\.8xlarge | 32 | 256\.00 | 
| r6i\.12xlarge | 48 | 384\.00 | 
| r6i\.16xlarge | 64 | 512\.00 | 
| r6i\.24xlarge | 96 | 768\.00 | 
| r6i\.32xlarge | 128 | 1024\.00 | 
| r6i\.metal | 128 | 1024\.00 | 
| r6idn\.large | 2 | 16\.00 | 
| r6idn\.xlarge | 4 | 32\.00 | 
| r6idn\.2xlarge | 8 | 64\.00 | 
| r6idn\.4xlarge | 16 | 128\.00 | 
| r6idn\.8xlarge | 32 | 256\.00 | 
| r6idn\.12xlarge | 48 | 384\.00 | 
| r6idn\.16xlarge | 64 | 512\.00 | 
| r6idn\.24xlarge | 96 | 768\.00 | 
| r6idn\.32xlarge | 128 | 1024\.00 | 
| r6idn\.metal | 128 | 1024\.00 | 
| r6in\.large | 2 | 16\.00 | 
| r6in\.xlarge | 4 | 32\.00 | 
| r6in\.2xlarge | 8 | 64\.00 | 
| r6in\.4xlarge | 16 | 128\.00 | 
| r6in\.8xlarge | 32 | 256\.00 | 
| r6in\.12xlarge | 48 | 384\.00 | 
| r6in\.16xlarge | 64 | 512\.00 | 
| r6in\.24xlarge | 96 | 768\.00 | 
| r6in\.32xlarge | 128 | 1024\.00 | 
| r6in\.metal | 128 | 1024\.00 | 
| r6id\.large | 2 | 16\.00 | 
| r6id\.xlarge | 4 | 32\.00 | 
| r6id\.2xlarge | 8 | 64\.00 | 
| r6id\.4xlarge | 16 | 128\.00 | 
| r6id\.8xlarge | 32 | 256\.00 | 
| r6id\.12xlarge | 48 | 384\.00 | 
| r6id\.16xlarge | 64 | 512\.00 | 
| r6id\.24xlarge | 96 | 768\.00 | 
| r6id\.32xlarge | 128 | 1024\.00 | 
| r6id\.metal | 128 | 1024\.00 | 
| r7g\.medium | 1 | 8\.00 | 
| r7g\.large | 2 | 16\.00 | 
| r7g\.xlarge | 4 | 32\.00 | 
| r7g\.2xlarge | 8 | 64\.00 | 
| r7g\.4xlarge | 16 | 128\.00 | 
| r7g\.8xlarge | 32 | 256\.00 | 
| r7g\.12xlarge | 48 | 384\.00 | 
| r7g\.16xlarge | 64 | 512\.00 | 
| r7g\.metal | 64 | 512\.00 | 
| u\-3tb1\.56xlarge | 224 | 3072\.00 | 
| u\-6tb1\.56xlarge | 224 | 6144\.00 | 
| u\-6tb1\.112xlarge | 448 | 6144\.00 | 
| u\-6tb1\.metal | 448 | 6144\.00 | 
| u\-9tb1\.112xlarge | 448 | 9216\.00 | 
| u\-9tb1\.metal | 448 | 9216\.00 | 
| u\-12tb1\.112xlarge | 448 | 12288\.00 | 
| u\-12tb1\.metal | 448 | 12288\.00 | 
| u\-18tb1\.metal | 448 | 18432\.00 | 
| u\-24tb1\.metal | 448 | 24576\.00 | 
| x1\.16xlarge | 64 | 976\.00 | 
| x1\.32xlarge | 128 | 1952\.00 | 
| x2gd\.medium | 1 | 16\.00 | 
| x2gd\.large | 2 | 32\.00 | 
| x2gd\.xlarge | 4 | 64\.00 | 
| x2gd\.2xlarge | 8 | 128\.00 | 
| x2gd\.4xlarge | 16 | 256\.00 | 
| x2gd\.8xlarge | 32 | 512\.00 | 
| x2gd\.12xlarge | 48 | 768\.00 | 
| x2gd\.16xlarge | 64 | 1024\.00 | 
| x2gd\.metal | 64 | 1024\.00 | 
| x2idn\.16xlarge | 64 | 1024\.00 | 
| x2idn\.24xlarge | 96 | 1536\.00 | 
| x2idn\.32xlarge | 128 | 2048\.00 | 
| x2idn\.metal | 128 | 2048\.00 | 
| x2iedn\.xlarge | 4 | 128\.00 | 
| x2iedn\.2xlarge | 8 | 256\.00 | 
| x2iedn\.4xlarge | 16 | 512\.00 | 
| x2iedn\.8xlarge | 32 | 1024\.00 | 
| x2iedn\.16xlarge | 64 | 2048\.00 | 
| x2iedn\.24xlarge | 96 | 3072\.00 | 
| x2iedn\.32xlarge | 128 | 4096\.00 | 
| x2iedn\.metal | 128 | 4096\.00 | 
| x2iezn\.2xlarge | 8 | 256\.00 | 
| x2iezn\.4xlarge | 16 | 512\.00 | 
| x2iezn\.6xlarge | 24 | 768\.00 | 
| x2iezn\.8xlarge | 32 | 1024\.00 | 
| x2iezn\.12xlarge | 48 | 1536\.00 | 
| x2iezn\.metal | 48 | 1536\.00 | 
| x1e\.xlarge | 4 | 122\.00 | 
| x1e\.2xlarge | 8 | 244\.00 | 
| x1e\.4xlarge | 16 | 488\.00 | 
| x1e\.8xlarge | 32 | 976\.00 | 
| x1e\.16xlarge | 64 | 1952\.00 | 
| x1e\.32xlarge | 128 | 3904\.00 | 
| z1d\.large | 2 | 16\.00 | 
| z1d\.xlarge | 4 | 32\.00 | 
| z1d\.2xlarge | 8 | 64\.00 | 
| z1d\.3xlarge | 12 | 96\.00 | 
| z1d\.6xlarge | 24 | 192\.00 | 
| z1d\.12xlarge | 48 | 384\.00 | 
| z1d\.metal | 48 | 384\.00 | 

\* Each logical processor is a hyperthread on 224 cores\.

The memory optimized instances use the following processors\.

**AWS Graviton processors**
+ **AWS Graviton2**: R6g, R6gd, X2gd

**AMD processors**
+ **AMD EPYC 7000 series processors \(AMD EPYC 7571\)**: R5a, R5ad
+ **3rd generation AMD EPYC processors \(AMD EPYC 7R13\)**: R6a

**Intel processors**
+ **Intel Xeon Scalable processors \(Haswell E7\-8880 v3\)**: X1, X1e
+ **Intel Xeon Scalable processors \(Broadwell E5\-2686 v4\)**: R4
+ **Intel Xeon Scalable processors \(Skylake 8151\)**: z1d
+ **Intel Xeon Scalable processors \(Skylake 8175M or Cascade Lake 8259CL\)**: R5, R5d
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8259CL\)**: R5b, R5n
+ **2nd generation Intel Xeon Scalable processors \(Cascade Lake 8252C\)**: X2iezn
+ **3rd generation Intel Xeon Scalable processors \(Ice Lake 8375C\)**: R6i, R6id, X2idn, X2iedn

For more information, see [Amazon EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)\.

## Memory performance<a name="memory-perf"></a>

X1 instances include Intel Scalable Memory Buffers, providing 300 GiB/s of sustainable memory\-read bandwidth and 140 GiB/s of sustainable memory\-write bandwidth\.

For more information about how much RAM can be enabled for memory optimized instances, see [Hardware specifications](#memory-instances-hardware)\.

Memory optimized instances have high memory and require 64\-bit HVM AMIs to take advantage of that capacity\. HVM AMIs provide superior performance in comparison to paravirtual \(PV\) AMIs on memory optimized instances\. For more information, see [Linux AMI virtualization types](virtualization_types.md)\.

## Instance performance<a name="memory-compute-perf"></a>

Memory optimized instances enable increased cryptographic performance through the latest Intel AES\-NI feature and support Advanced Vector Extensions 2 \(Intel AVX2\) processor instructions to expand most integer commands to 256 bits\.

Some memory optimized instances provide the ability to control processor C\-states and P\-states on Linux\. C\-states control the sleep levels that a core can enter when it is inactive, while P\-states control the desired performance \(measured by CPU frequency\) from a core\. For more information, see [Processor state control for your EC2 instance](processor_state_control.md)\.

## Network performance<a name="memory-network-perf"></a>

You can enable enhanced networking on supported instance types to provide lower latencies, lower network jitter, and higher packet\-per\-second \(PPS\) performance\. Most applications do not consistently need a high level of network performance, but can benefit from access to increased bandwidth when they send or receive data\. For more information, see [Enhanced networking on Linux](enhanced-networking.md)\.

The following is a summary of network performance for memory optimized instances that support enhanced networking\.

**Note**  
Instance types indicated with a **†** have a baseline bandwidth and can use a network I/O credit mechanism to burst beyond their baseline bandwidth on a best effort basis\. For more information, see [instance network bandwidth](ec2-instance-network-bandwidth.md)\.


| Instance type | Network performance | Enhanced networking features | 
| --- | --- | --- | 
| hpc6id\.32xlarge | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r3\.large † | Moderate | Not supported | 
| r3\.xlarge † | Moderate | Not supported | 
| r3\.2xlarge † | High | Not supported | 
| r3\.4xlarge † | High | Not supported | 
| r3\.8xlarge † | 10 Gigabit | Not supported | 
| r4\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r4\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r4\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r4\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r4\.8xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r4\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.8xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.12xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.16xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.24xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.8xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.12xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.16xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5a\.24xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.8xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.12xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.16xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5ad\.24xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.8xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.12xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.16xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.24xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5b\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.8xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.12xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.16xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.24xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5d\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.4xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.8xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.12xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.16xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5dn\.24xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r5dn\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r5n\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.4xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.8xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.12xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.16xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r5n\.24xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r5n\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6a\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.2xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.4xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.8xlarge | 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.12xlarge | 18\.75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.24xlarge | 37\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.32xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6a\.48xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6a\.metal | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6g\.medium † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.8xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.12xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6g\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.medium † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.8xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.12xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6gd\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.2xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.4xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.8xlarge | 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.12xlarge | 18\.75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.24xlarge | 37\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6i\.32xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6i\.metal | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6idn\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.xlarge † | Up to 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.2xlarge † | Up to 40 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.4xlarge † | Up to 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.8xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.12xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.16xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.24xlarge | 150 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6idn\.32xlarge | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6idn\.metal | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6in\.large † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.xlarge † | Up to 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.2xlarge † | Up to 40 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.4xlarge † | Up to 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.8xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.12xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.16xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.24xlarge | 150 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6in\.32xlarge | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6in\.metal | 200 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6id\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.2xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.4xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.8xlarge | 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.12xlarge | 18\.75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.24xlarge | 37\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r6id\.32xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r6id\.metal | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r7g\.medium † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.large † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.xlarge † | Up to 12\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.2xlarge † | Up to 15 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.4xlarge † | Up to 15 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.8xlarge | 15 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.12xlarge | 22\.5 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| r7g\.16xlarge | 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| r7g\.metal | 30 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| u\-3tb1\.56xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-6tb1\.56xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-6tb1\.112xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-6tb1\.metal | 100 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-9tb1\.112xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-9tb1\.metal | 100 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-12tb1\.112xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-12tb1\.metal | 100 | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-18tb1\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| u\-24tb1\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1\.16xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1\.32xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.medium † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.8xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.12xlarge | 20 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.16xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2gd\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2idn\.16xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2idn\.24xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2idn\.32xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| x2idn\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| x2iedn\.xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iedn\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iedn\.4xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iedn\.8xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iedn\.16xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iedn\.24xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iedn\.32xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| x2iedn\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| x2iezn\.2xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iezn\.4xlarge † | Up to 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iezn\.6xlarge | 50 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iezn\.8xlarge | 75 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x2iezn\.12xlarge | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| x2iezn\.metal | 100 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) \| [EFA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/efa.html) | 
| x1e\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1e\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1e\.4xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1e\.8xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1e\.16xlarge | 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| x1e\.32xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.large † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.2xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.3xlarge † | Up to 10 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.6xlarge | 12 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.12xlarge | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 
| z1d\.metal | 25 Gigabit | [ENA](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/enhanced-networking.html) | 

For `32xlarge` and `metal` instance types that support 200 Gbps, at least 2 ENIs, each attached to a different network card, are required on the instance to achieve 200 Gbps throughput\. Each ENI attached to a network card can achieve a max of 170 Gbps\.

`u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` instances launched after March 12, 2020 provide network performance of 100 Gbps\. `u-6tb1.metal`, `u-9tb1.metal`, and `u-12tb1.metal` instances launched before March 12, 2020 mightonly provide network performance of 25 Gbps\. To ensure that instances launched before March 12, 2020 have a network performance of 100 Gbps, contact your account team to upgrade your instance at no additional cost\.

The following table shows the baseline and burst bandwidth for instance types that use the network I/O credit mechanism to burst beyond their baseline bandwidth\.


| Instance type | Baseline bandwidth \(Gbps\) | Burst bandwidth \(Gbps\) | 
| --- | --- | --- | 
| r3\.large | 0\.5 | 1\.2 | 
| r3\.xlarge | 0\.7 | 2\.8 | 
| r3\.2xlarge | 1\.0 | 10\.0 | 
| r3\.4xlarge | 2\.0 | 10\.0 | 
| r3\.8xlarge | 5\.0 | 10\.0 | 
| r4\.large | 0\.75 | 10\.0 | 
| r4\.xlarge | 1\.25 | 10\.0 | 
| r4\.2xlarge | 2\.5 | 10\.0 | 
| r4\.4xlarge | 5\.0 | 10\.0 | 
| r5\.large | 0\.75 | 10\.0 | 
| r5\.xlarge | 1\.25 | 10\.0 | 
| r5\.2xlarge | 2\.5 | 10\.0 | 
| r5\.4xlarge | 5\.0 | 10\.0 | 
| r5a\.large | 0\.75 | 10\.0 | 
| r5a\.xlarge | 1\.25 | 10\.0 | 
| r5a\.2xlarge | 2\.5 | 10\.0 | 
| r5a\.4xlarge | 5\.0 | 10\.0 | 
| r5a\.8xlarge | 7\.5 | 10\.0 | 
| r5ad\.large | 0\.75 | 10\.0 | 
| r5ad\.xlarge | 1\.25 | 10\.0 | 
| r5ad\.2xlarge | 2\.5 | 10\.0 | 
| r5ad\.4xlarge | 5\.0 | 10\.0 | 
| r5ad\.8xlarge | 7\.5 | 10\.0 | 
| r5b\.large | 0\.75 | 10\.0 | 
| r5b\.xlarge | 1\.25 | 10\.0 | 
| r5b\.2xlarge | 2\.5 | 10\.0 | 
| r5b\.4xlarge | 5\.0 | 10\.0 | 
| r5d\.large | 0\.75 | 10\.0 | 
| r5d\.xlarge | 1\.25 | 10\.0 | 
| r5d\.2xlarge | 2\.5 | 10\.0 | 
| r5d\.4xlarge | 5\.0 | 10\.0 | 
| r5dn\.large | 2\.1 | 25\.0 | 
| r5dn\.xlarge | 4\.1 | 25\.0 | 
| r5dn\.2xlarge | 8\.125 | 25\.0 | 
| r5dn\.4xlarge | 16\.25 | 25\.0 | 
| r5n\.large | 2\.1 | 25\.0 | 
| r5n\.xlarge | 4\.1 | 25\.0 | 
| r5n\.2xlarge | 8\.125 | 25\.0 | 
| r5n\.4xlarge | 16\.25 | 25\.0 | 
| r6a\.large | 0\.781 | 12\.5 | 
| r6a\.xlarge | 1\.562 | 12\.5 | 
| r6a\.2xlarge | 3\.125 | 12\.5 | 
| r6a\.4xlarge | 6\.25 | 12\.5 | 
| r6g\.medium | 0\.5 | 10\.0 | 
| r6g\.large | 0\.75 | 10\.0 | 
| r6g\.xlarge | 1\.25 | 10\.0 | 
| r6g\.2xlarge | 2\.5 | 10\.0 | 
| r6g\.4xlarge | 5\.0 | 10\.0 | 
| r6gd\.medium | 0\.5 | 10\.0 | 
| r6gd\.large | 0\.75 | 10\.0 | 
| r6gd\.xlarge | 1\.25 | 10\.0 | 
| r6gd\.2xlarge | 2\.5 | 10\.0 | 
| r6gd\.4xlarge | 5\.0 | 10\.0 | 
| r6i\.large | 0\.781 | 12\.5 | 
| r6i\.xlarge | 1\.562 | 12\.5 | 
| r6i\.2xlarge | 3\.125 | 12\.5 | 
| r6i\.4xlarge | 6\.25 | 12\.5 | 
| r6idn\.large | 3\.125 | 25\.0 | 
| r6idn\.xlarge | 6\.25 | 30\.0 | 
| r6idn\.2xlarge | 12\.5 | 40\.0 | 
| r6idn\.4xlarge | 25\.0 | 50\.0 | 
| r6in\.large | 3\.125 | 25\.0 | 
| r6in\.xlarge | 6\.25 | 30\.0 | 
| r6in\.2xlarge | 12\.5 | 40\.0 | 
| r6in\.4xlarge | 25\.0 | 50\.0 | 
| r6id\.large | 0\.781 | 12\.5 | 
| r6id\.xlarge | 1\.562 | 12\.5 | 
| r6id\.2xlarge | 3\.125 | 12\.5 | 
| r6id\.4xlarge | 6\.25 | 12\.5 | 
| r7g\.medium | 0\.52 | 12\.5 | 
| r7g\.large | 0\.937 | 12\.5 | 
| r7g\.xlarge | 1\.876 | 12\.5 | 
| r7g\.2xlarge | 3\.75 | 15\.0 | 
| r7g\.4xlarge | 7\.5 | 15\.0 | 
| x2gd\.medium | 0\.5 | 10\.0 | 
| x2gd\.large | 0\.75 | 10\.0 | 
| x2gd\.xlarge | 1\.25 | 10\.0 | 
| x2gd\.2xlarge | 2\.5 | 10\.0 | 
| x2gd\.4xlarge | 5\.0 | 10\.0 | 
| x2iedn\.xlarge | 1\.875 | 25\.0 | 
| x2iedn\.2xlarge | 5\.0 | 25\.0 | 
| x2iedn\.4xlarge | 12\.5 | 25\.0 | 
| x2iezn\.2xlarge | 12\.5 | 25\.0 | 
| x2iezn\.4xlarge | 15\.0 | 25\.0 | 
| x1e\.xlarge | 0\.625 | 10\.0 | 
| x1e\.2xlarge | 1\.25 | 10\.0 | 
| x1e\.4xlarge | 2\.5 | 10\.0 | 
| x1e\.8xlarge | 5\.0 | 10\.0 | 
| z1d\.large | 0\.75 | 10\.0 | 
| z1d\.xlarge | 1\.25 | 10\.0 | 
| z1d\.2xlarge | 2\.5 | 10\.0 | 
| z1d\.3xlarge | 5\.0 | 10\.0 | 

## Amazon EBS I/O performance<a name="memory-ebs-perf"></a>

Amazon EBS optimized instances use an optimized configuration stack and provide additional, dedicated capacity for Amazon EBS I/O\. This optimization provides the best performance for your Amazon EBS volumes by minimizing contention between Amazon EBS I/O and other traffic from your instance\.

For more information, see [Amazon EBS–optimized instances](ebs-optimized.md)\.

## SSD\-based instance store volume I/O performance<a name="instances-ssd-perf"></a>

If you use a Linux AMI with kernel version 4\.4 or later and use all the SSD\-based instance store volumes available to your instance, you can get up to the IOPS \(4,096 byte block size\) performance listed in the following table \(at queue depth saturation\)\. Otherwise, you get lower IOPS performance\.


| Instance Size | 100% Random Read IOPS | Write IOPS | 
| --- | --- | --- | 
| hpc6id\.32xlarge | 2,146,664 | 1,073,336 | 
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
| r6id\.metal | 3,219,995 | 1,610,005 | 
| r6gd\.medium | 13,438 | 5,625 | 
| r6gd\.large | 26,875 | 11,250 | 
| r6gd\.xlarge | 53,750 | 22,500 | 
| r6gd\.2xlarge | 107,500 | 45,000 | 
| r6gd\.4xlarge | 215,000 | 90,000 | 
| r6gd\.8xlarge | 430,000 | 180,000 | 
| r6gd\.12xlarge | 645,000 | 270,000 | 
| r6gd\.16xlarge | 860,000 | 360,000 | 
| r6gd\.metal | 860,000 | 360,000 | 
| r6id\.large | 33,542 | 16,771 | 
| r6id\.xlarge | 67,083 | 33,542 | 
| r6id\.2xlarge | 134,167 | 67,084 | 
| r6id\.4xlarge | 268,333 | 134,167 | 
| r6id\.8xlarge | 536,666 | 268,334 | 
| r6id\.12xlarge | 804,999 | 402,501 | 
| r6id\.16xlarge | 1,073,332 | 536,668 | 
| r6id\.24xlarge | 1,609,998 | 805,002 | 
| r6id\.32xlarge | 2,146,664 | 1,073,336 | 
| r6id\.metal | 2,146,664 | 1,073,336 | 
| r6idn\.large | 33,542 | 16,771 | 
| r6idn\.xlarge | 67,083 | 33,542 | 
| r6idn\.2xlarge | 134,167 | 67,084 | 
| r6idn\.4xlarge | 268,333 | 134,167 | 
| r6idn\.8xlarge | 536,666 | 268,334 | 
| r6idn\.12xlarge | 804,999 | 402,501 | 
| r6idn\.16xlarge | 1,073,332 | 536,668 | 
| r6idn\.24xlarge | 1,609,998 | 805,002 | 
| r6idn\.32xlarge | 2,146,664 | 1,073,336 | 
| r6idn\.metal | 2,146,664 | 1,073,336 | 
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
| x2idn\.metal | 860,000 | 360,000 | 
| x2iedn\.xlarge | 26,875 | 11,250 | 
| x2iedn\.2xlarge | 53,750 | 22,500 | 
| x2iedn\.4xlarge | 107,500 | 45,000 | 
| x2iedn\.8xlarge | 215,000 | 90,000 | 
| x2iedn\.16xlarge | 430,000 | 180,000 | 
| x2iedn\.24xlarge | 645,000 | 270,000 | 
| x2iedn\.32xlarge | 860,000 | 360,000 | 
| x2iedn\.metal | 860,000 | 360,000 | 
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
+ Instances built on the Nitro System have the following requirements:
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
+ All `io2` volumes attached to C6a, C6in, C7g, Inf2, M6a, M6in, M6idn, M7g, R5b, R6in, R6idn, R7g, Trn1, Trn1n, X2idn, and X2iedn instances, during or after launch, automatically run on EBS Block Express\. For more information, see [`io2` Block Express volumes](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volume-types.html#io2-block-express)\.
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
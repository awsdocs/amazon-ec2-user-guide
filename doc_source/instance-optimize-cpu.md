# Optimizing CPU Options<a name="instance-optimize-cpu"></a>

Amazon EC2 instances support multithreading, which enables multiple threads to run concurrently on a single CPU core\. Each thread is represented as a virtual CPU \(vCPU\) on the instance\. An instance has a default number of CPU cores, which varies according to instance type\. For example, an `m5.xlarge` instance type has two CPU cores and two threads per core by default—four vCPUs in total\.

**Note**  
Each vCPU is a thread of a CPU core, except for T2 instances\.

In most cases, there is an Amazon EC2 instance type that has a combination of memory and number of vCPUs to suit your workloads\. However, you can specify the following CPU options to optimize your instance for specific workloads or business needs:
+ **Number of CPU cores**: You can customize the number of CPU cores for the instance\. You might do this to potentially optimize the licensing costs of your software with an instance that has sufficient amounts of RAM for memory\-intensive workloads but fewer CPU cores\.
+ **Threads per core**: You can disable multithreading by specifying a single thread per CPU core\. You might do this for certain workloads, such as high performance computing \(HPC\) workloads\.

You can specify these CPU options during instance launch\. There is no additional or reduced charge for specifying CPU options\. You're charged the same as instances that are launched with default CPU options\.

**Topics**
+ [Rules for Specifying CPU Options](#instance-cpu-options-rules)
+ [CPU Cores and Threads Per CPU Core Per Instance Type](#cpu-options-supported-instances-values)
+ [Specifying CPU Options for Your Instance](#instance-specify-cpu-options)
+ [Viewing the CPU Options for Your Instance](#view-cpu-options)

## Rules for Specifying CPU Options<a name="instance-cpu-options-rules"></a>

To specify the CPU options for your instance, be aware of the following rules:
+ CPU options are currently supported using the Amazon EC2 console, the AWS CLI, an AWS SDK, or the Amazon EC2 API\.
+ CPU options can only be specified during instance launch and cannot be modified after launch\.
+ When you launch an instance, you must specify both the number of CPU cores and threads per core in the request\. For example requests, see [Specifying CPU Options for Your Instance](#instance-specify-cpu-options)\.
+ The number of vCPUs for the instance is the number of CPU cores multiplied by the threads per core\. To specify a custom number of vCPUs, you must specify a valid number of CPU cores and threads per core for the instance type\. You cannot exceed the default number of vCPUs for the instance\. For more information, see [CPU Cores and Threads Per CPU Core Per Instance Type](#cpu-options-supported-instances-values)\.
+ To disable multithreading, specify one thread per core\.
+ When you [change the instance type](ec2-instance-resize.md) of an existing instance, the CPU options automatically change to the default CPU options for the new instance type\.
+ The specified CPU options persist after you stop, start, or reboot an instance\.

## CPU Cores and Threads Per CPU Core Per Instance Type<a name="cpu-options-supported-instances-values"></a>

The following tables list the instance types that support specifying CPU options\. For each type, the table shows the default and supported number of CPU cores and threads per core\.


**Accelerated Computing Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| f1\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| f1\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| f1\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| g3\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| g3\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| g3\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| g3s\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| p2\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| p2\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| p2\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| p3\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| p3\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| p3\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| p3dn\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 


**Compute Optimized Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| c4\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| c4\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| c4\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| c4\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| c4\.8xlarge | 36 | 18 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18 | 1, 2 | 
| c5\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| c5\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| c5\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| c5\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| c5\.9xlarge | 36 | 18 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18 | 1, 2 | 
| c5\.12xlarge | 48 | 24 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| c5\.18xlarge | 72 | 36 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36 | 1, 2 | 
| c5\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| c5d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| c5d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| c5d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| c5d\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| c5d\.9xlarge | 36 | 18 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18 | 1, 2 | 
| c5d\.18xlarge | 72 | 36 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36 | 1, 2 | 
| c5n\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| c5n\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| c5n\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| c5n\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| c5n\.9xlarge | 36 | 18 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18 | 1, 2 | 
| c5n\.18xlarge | 72 | 36 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36 | 1, 2 | 


**General Purpose Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| m5\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| m5\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| m5\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| m5\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| m5\.8xlarge | 32 | 16 | 2 | 2, 4, 6, 8, 10, 12, 14, 16 | 1, 2 | 
| m5\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| m5\.16xlarge | 64 | 32 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| m5\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| m5a\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| m5a\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| m5a\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| m5a\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| m5a\.8xlarge | 32 | 16 | 2 | 2, 4, 6, 8, 10, 12, 14, 16 | 1, 2 | 
| m5a\.12xlarge | 48 | 24 | 2 | 6, 12, 18, 24 | 1, 2 | 
| m5a\.16xlarge | 64 | 32 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| m5a\.24xlarge | 96 | 48 | 2 | 12, 18, 24, 36, 48 | 1, 2 | 
| m5ad\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| m5ad\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| m5ad\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| m5ad\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| m5ad\.12xlarge | 48 | 24 | 2 | 6, 12, 18, 24 | 1, 2 | 
| m5ad\.24xlarge | 96 | 48 | 2 | 12, 18, 24, 36, 48 | 1, 2 | 
| m5d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| m5d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| m5d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| m5d\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| m5d\.8xlarge | 32 | 16 | 2 | 2, 4, 6, 8, 10, 12, 14, 16 | 1, 2 | 
| m5d\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| m5d\.16xlarge | 64 | 32 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| m5d\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| t3\.nano | 2 | 1 | 2 | 1 | 1, 2 | 
| t3\.micro | 2 | 1 | 2 | 1 | 1, 2 | 
| t3\.small | 2 | 1 | 2 | 1 | 1, 2 | 
| t3\.medium | 2 | 1 | 2 | 1 | 1, 2 | 
| t3\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| t3\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| t3\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| t3a\.nano | 2 | 1 | 2 | 1 | 1, 2 | 
| t3a\.micro | 2 | 1 | 2 | 1 | 1, 2 | 
| t3a\.small | 2 | 1 | 2 | 1 | 1, 2 | 
| t3a\.medium | 2 | 1 | 2 | 1 | 1, 2 | 
| t3a\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| t3a\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| t3a\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 


**Memory Optimized Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| r4\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| r4\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| r4\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| r4\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| r4\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| r4\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| r5\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| r5\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| r5\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| r5\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| r5\.8xlarge | 32 | 16 | 2 | 2, 4, 6, 8, 10, 12, 14, 16 | 1, 2 | 
| r5\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| r5\.16xlarge | 64 | 32 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| r5\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| r5a\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| r5a\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| r5a\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| r5a\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| r5a\.8xlarge | 32 | 16 | 2 | 2, 4, 6, 8, 10, 12, 14, 16 | 1, 2 | 
| r5a\.12xlarge | 48 | 24 | 2 | 6, 12, 18, 24 | 1, 2 | 
| r5a\.16xlarge | 64 | 32 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| r5a\.24xlarge | 96 | 48 | 2 | 12, 18, 24, 36, 48 | 1, 2 | 
| r5ad\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| r5ad\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| r5ad\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| r5ad\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| r5ad\.12xlarge | 48 | 24 | 2 | 6, 12, 18, 24 | 1, 2 | 
| r5ad\.24xlarge | 96 | 48 | 2 | 12, 18, 24, 36, 48 | 1, 2 | 
| r5d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| r5d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| r5d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| r5d\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| r5d\.8xlarge | 32 | 16 | 2 | 2, 4, 6, 8, 10, 12, 14, 16 | 1, 2 | 
| r5d\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| r5d\.16xlarge | 64 | 32 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| r5d\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| x1\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| x1\.32xlarge | 128 | 64 | 2 | 4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64 | 1, 2 | 
| x1e\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| x1e\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| x1e\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| x1e\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| x1e\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| x1e\.32xlarge | 128 | 64 | 2 | 4, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64 | 1, 2 | 
| z1d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| z1d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| z1d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| z1d\.3xlarge | 12 | 6 | 2 | 2, 4, 6 | 1, 2 | 
| z1d\.6xlarge | 24 | 12 | 2 | 2, 4, 6, 8, 10, 12 | 1, 2 | 
| z1d\.12xlarge | 48 | 24 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 


**Storage Optimized Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| d2\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| d2\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| d2\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| d2\.8xlarge | 36 | 18 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18 | 1, 2 | 
| h1\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| h1\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| h1\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| h1\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| i3\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| i3\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| i3\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| i3\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| i3\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| i3\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| i3en\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| i3en\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| i3en\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| i3en\.3xlarge | 12 | 6 | 2 | 2, 4, 6 | 1, 2 | 
| i3en\.6xlarge | 24 | 12 | 2 | 2, 4, 6, 8, 10, 12 | 1, 2 | 
| i3en\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| i3en\.24xlarge | 96 | 48 | 2 | 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 

## Specifying CPU Options for Your Instance<a name="instance-specify-cpu-options"></a>

You can specify CPU options during instance launch\. The following examples are for an `r4.4xlarge` instance type, which has the following [default values](#cpu-options-mem-optimized):
+ Default CPU cores: 8
+ Default threads per core: 2
+ Default vCPUs: 16 \(8 \* 2\)
+ Valid number of CPU cores: 1, 2, 3, 4, 5, 6, 7, 8
+ Valid number of threads per core: 1, 2

### Disabling Multithreading<a name="cpu-options-disable-intel-hyper-threading-technology"></a>

To disable multithreading, specify one thread per core\.

**To disable multithreading during instance launch \(console\)**

1. Follow the [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Configure Instance Details** page, for **CPU options**, choose **Specify CPU options**\.

1. For **Core count**, choose the number of required CPU cores\. In this example, to specify the default CPU core count for an `r4.4xlarge` instance, choose `8`\.

1. To disable multithreading, for **Threads per core**, choose **1**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To disable multithreading during instance launch \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify a value of `1` for `ThreadsPerCore` for the `--cpu-options` parameter\. For `CoreCount`, specify the number of CPU cores\. In this example, to specify the default CPU core count for an `r4.4xlarge` instance, specify a value of `8`\.

```
aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=8,ThreadsPerCore=1" --key-name MyKeyPair
```

### Specifying a Custom Number of vCPUs<a name="cpu-options-customer-number-of-vCPUs"></a>

You can customize the number of CPU cores and threads per core for the instance\.

**To specify a custom number of vCPUs during instance launch \(console\)**

The following example launches an `r4.4xlarge` instance with six vCPUs\.

1. Follow the [Launching an Instance Using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Configure Instance Details** page, for **CPU options**, choose **Specify CPU options**\.

1. To get six vCPUs, specify three CPU cores and two threads per core, as follows:
   + For **Core count**, choose **3**\.
   + For **Threads per core**, choose **2**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launching an Instance Using the Launch Instance Wizard](launching-instance.md)\.

**To specify a custom number of vCPUs during instance launch \(AWS CLI\)**  
The following example launches an `r4.4xlarge` instance with six vCPUs\.

Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify the number of CPU cores and number of threads in the `--cpu-options` parameter\. You can specify three CPU cores and two threads per core to get six vCPUs\.

```
aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=3,ThreadsPerCore=2" --key-name MyKeyPair
```

Alternatively, specify six CPU cores and one thread per core \(disable multithreading\) to get six vCPUs:

```
aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=6,ThreadsPerCore=1" --key-name MyKeyPair
```

## Viewing the CPU Options for Your Instance<a name="view-cpu-options"></a>

You can view the CPU options for an existing instance in the Amazon EC2 console or by describing the instance using the AWS CLI\.

**To view the CPU options for an instance \(console\)**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the left navigation pane, choose **Instances**, and select the instance\.

1. Choose **Description** and view the **Number of vCPUs** field\.

1. To view the core count and threads per core, choose the **Number of vCPUs** field value\.

**To view the CPU options for an instance \(AWS CLI\)**  
Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) command\.

```
aws ec2 describe-instances --instance-ids i-123456789abcde123
```

```
...
    "Instances": [
        {
            "Monitoring": {
                "State": "disabled"
            }, 
            "PublicDnsName": "ec2-198-51-100-5.eu-central-1.compute.amazonaws.com", 
            "State": {
                "Code": 16, 
                "Name": "running"
            }, 
            "EbsOptimized": false, 
            "LaunchTime": "2018-05-08T13:40:33.000Z", 
            "PublicIpAddress": "198.51.100.5", 
            "PrivateIpAddress": "172.31.2.206", 
            "ProductCodes": [], 
            "VpcId": "vpc-1a2b3c4d", 
            "CpuOptions": {
                "CoreCount": 34, 
                "ThreadsPerCore": 1
            }, 
            "StateTransitionReason": "", 
            ...
        }
    ]
...
```

In the output that's returned, the `CoreCount` field indicates the number of cores for the instance\. The `ThreadsPerCore` field indicates the number of threads per core\.

Alternatively, connect to your instance and use a tool such as lscpu to view the CPU information for your instance\.

You can use AWS Config to record, assess, audit, and evaluate configuration changes for instances, including terminated instances\. For more information, see [Getting Started with AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/getting-started.html) in the *AWS Config Developer Guide*\.
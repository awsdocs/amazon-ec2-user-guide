# Optimize CPU options<a name="instance-optimize-cpu"></a>

Amazon EC2 instances support multithreading, which enables multiple threads to run concurrently on a single CPU core\. Each thread is represented as a virtual CPU \(vCPU\) on the instance\. An instance has a default number of CPU cores, which varies according to instance type\. For example, an `m5.xlarge` instance type has two CPU cores and two threads per core by default—four vCPUs in total\.

**Note**  
Each vCPU is a thread of a CPU core, except for T2 instances and instances powered by AWS Graviton2 processors\.

In most cases, there is an Amazon EC2 instance type that has a combination of memory and number of vCPUs to suit your workloads\. However, you can specify the following CPU options to optimize your instance for specific workloads or business needs:
+ **Number of CPU cores**: You can customize the number of CPU cores for the instance\. You might do this to potentially optimize the licensing costs of your software with an instance that has sufficient amounts of RAM for memory\-intensive workloads but fewer CPU cores\.
+ **Threads per core**: You can disable multithreading by specifying a single thread per CPU core\. You might do this for certain workloads, such as high performance computing \(HPC\) workloads\.

You can specify these CPU options during instance launch\. There is no additional or reduced charge for specifying CPU options\. You're charged the same as instances that are launched with default CPU options\.

**Topics**
+ [Rules for specifying CPU options](instance-cpu-options-rules.md)
+ [CPU cores and threads per CPU core per instance type](cpu-options-supported-instances-values.md)
+ [Specify CPU options for your instance](instance-specify-cpu-options.md)
+ [View the CPU options for your instance](view-cpu-options.md)
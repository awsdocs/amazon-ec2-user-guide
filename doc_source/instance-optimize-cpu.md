# Optimizing CPU Options<a name="instance-optimize-cpu"></a>

Amazon EC2 instances support Intel Hyper\-Threading Technology, which enables multiple threads to run concurrently on a single Intel Xeon CPU core\. Each thread is represented as a virtual CPU \(vCPU\) on the instance\. An instance has a default number of CPU cores, which varies according to instance type\. For example, an `m5.xlarge` instance type has two CPU cores and two threads per core by default—four vCPUs in total\.

**Note**  
Each vCPU is a hyperthread of an Intel Xeon CPU core, except for T2 instances\.

In most cases, there is an Amazon EC2 instance type that has a combination of memory and number of vCPUs to suit your workloads\. However, you can specify the following CPU options to optimize your instance for specific workloads or business needs:
+ **Number of CPU cores**: You can customize the number of CPU cores for the instance\. You might do this to potentially optimize the licensing costs of your software with an instance that has sufficient amounts of RAM for memory\-intensive workloads but fewer CPU cores\.
+ **Threads per core**: You can disable Intel Hyper\-Threading Technology by specifying a single thread per CPU core\. You might do this for certain workloads, such as high performance computing \(HPC\) workloads\.

You can specify these CPU options during instance launch\. There is no additional or reduced charge for specifying CPU options\. You're charged the same as instances that are launched with default CPU options\.

**Topics**
+ [Rules for Specifying CPU Options](#instance-cpu-options-rules)
+ [CPU Cores and Threads Per CPU Core Per Instance Type](#cpu-options-supported-instances-values)
+ [Specifying CPU Options for Your Instance](#instance-specify-cpu-options)
+ [Viewing the CPU Options for Your Instance](#view-cpu-options)

## Rules for Specifying CPU Options<a name="instance-cpu-options-rules"></a>

To specify the CPU options for your instance, be aware of the following rules:
+ CPU options are currently supported using the AWS CLI, an AWS SDK, or the Amazon EC2 API only\.
+ CPU options can only be specified during instance launch and cannot be modified after launch\.
+ When you launch an instance, you must specify both the number of CPU cores and threads per core in the request\. For examples, see [Specifying CPU Options for Your Instance](#instance-specify-cpu-options)\.
+ The total number of vCPUs for the instance is the number of CPU cores multiplied by the threads per core\. To specify a custom number of vCPUs, you must specify a valid number of CPU cores and threads per core for the instance type\. You cannot exceed the default number of vCPUs for the instance\. For more information, see [CPU Cores and Threads Per CPU Core Per Instance Type](#cpu-options-supported-instances-values)\.
+ To disable Intel Hyper\-Threading Technology, specify one thread per core\.
+ If you [change the instance type](ec2-instance-resize.md) of an existing instance, the CPU options automatically change to the default CPU options for the new instance type\.
+ The CPU options that you specify persist after you stop, start, or reboot an instance\.

## CPU Cores and Threads Per CPU Core Per Instance Type<a name="cpu-options-supported-instances-values"></a>

The following tables list the instance types that support specifying CPU options\. For each type, the table shows the default and supported number of CPU cores and threads per core\.


**Accelerated Computing Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| p2\.xlarge | 4 | 2 | 2 | 1, 2 | 1, 2 | 
| p2\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| p2\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| p3\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| p3\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| p3\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| g3\.4xlarge | 16 | 8 | 2 | 1, 2, 3, 4, 5, 6, 7, 8 | 1, 2 | 
| g3\.8xlarge | 32 | 16 | 2 | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16 | 1, 2 | 
| g3\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 
| f1\.2xlarge | 8 | 4 | 2 | 1, 2, 3, 4 | 1, 2 | 
| f1\.16xlarge | 64 | 32 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32 | 1, 2 | 


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
| c5\.18xlarge | 72 | 36 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36 | 1, 2 | 
| c5d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| c5d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| c5d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| c5d\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| c5d\.9xlarge | 36 | 18 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18 | 1, 2 | 
| c5d\.18xlarge | 72 | 36 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36 | 1, 2 | 


**General Purpose Instances**  

| Instance type | Default vCPUs | Default CPU cores | Default threads per core | Valid number of CPU cores | Valid number of threads per core | 
| --- | --- | --- | --- | --- | --- | 
| m5\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| m5\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| m5\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| m5\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| m5\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| m5\.24xlarge | 96 | 48 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| m5d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| m5d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| m5d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| m5d\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| m5d\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| m5d\.24xlarge | 96 | 48 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 


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
| r5\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| r5\.24xlarge | 96 | 48 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
| r5d\.large | 2 | 1 | 2 | 1 | 1, 2 | 
| r5d\.xlarge | 4 | 2 | 2 | 2 | 1, 2 | 
| r5d\.2xlarge | 8 | 4 | 2 | 2, 4 | 1, 2 | 
| r5d\.4xlarge | 16 | 8 | 2 | 2, 4, 6, 8 | 1, 2 | 
| r5d\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 
| r5d\.24xlarge | 96 | 48 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24, 26, 28, 30, 32, 34, 36, 38, 40, 42, 44, 46, 48 | 1, 2 | 
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
| z1d\.12xlarge | 48 | 24 | 2 | 2, 4, 6, 8, 10, 12, 14, 16, 18, 20, 22, 24 | 1, 2 | 


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

## Specifying CPU Options for Your Instance<a name="instance-specify-cpu-options"></a>

You can specify CPU options during instance launch\. The following examples are for an `r4.4xlarge` instance type, which has the following [default values](#cpu-options-mem-optimized):
+ Default CPU cores: 8
+ Default threads per core: 2
+ Default vCPUs: 16 \(8 \* 2\)
+ Valid number of CPU cores: 1, 2, 3, 4, 5, 6, 7, 8
+ Valid number of threads per core: 1, 2

**To disable Intel Hyper\-Threading Technology during instance launch**
+ Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify a value of `1` for `ThreadsPerCore` for the `--cpu-options` parameter\. For `CoreCount`, specify the default CPU core count for the instance type \(in this example, `8` for an `r4.4xlarge` instance\)\.

  ```
  aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=8,ThreadsPerCore=1" --key-name MyKeyPair
  ```

**To specify a custom number of vCPUs during instance launch**

The following example launches an `r4.4xlarge` instance with six vCPUs\.

1. Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify the number of CPU cores and number of threads in the `--cpu-options` parameter\. You can specify three CPU cores and two threads per core to get six vCPUs\.

   ```
   aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=3,ThreadsPerCore=2" --key-name MyKeyPair
   ```

1. Alternatively, specify six CPU cores and one thread per core \(disable hyperthreading\) to get six vCPUs:

   ```
   aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=6,ThreadsPerCore=1" --key-name MyKeyPair
   ```

## Viewing the CPU Options for Your Instance<a name="view-cpu-options"></a>

You can view the CPU options for an existing instance by describing the instance\.

**To view the CPU options for an instance using the command line**
+ Use the [describe\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html) AWS CLI command\.

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
  ```

  In the output that's returned, the `CoreCount` field indicates the number of cores for the instance\. The `ThreadsPerCore` field indicates the number of threads per core\.

Alternatively, connect to your instance and use a tool such as lscpu to view the CPU information for your instance\.

You can use AWS Config to record, assess, audit, and evaluate configuration changes for instances, including terminated instances\. For more information, see [Getting Started with AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/getting-started.html) in the *AWS Config Developer Guide*\.
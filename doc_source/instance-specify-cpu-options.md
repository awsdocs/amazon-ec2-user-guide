# Specify CPU options for your instance<a name="instance-specify-cpu-options"></a>

You can specify CPU options during instance launch\. The following examples are for an `r4.4xlarge` instance type, which has the following [default values](cpu-options-supported-instances-values.md#cpu-options-mem-optimized):
+ Default CPU cores: 8
+ Default threads per core: 2
+ Default vCPUs: 16 \(8 \* 2\)
+ Valid number of CPU cores: 1, 2, 3, 4, 5, 6, 7, 8
+ Valid number of threads per core: 1, 2

## Disable multithreading<a name="cpu-options-disable-intel-hyper-threading-technology"></a>

To disable multithreading, specify one thread per core\.

**To disable multithreading during instance launch \(console\)**

1. Follow the [Launch an instance using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Configure Instance Details** page, for **CPU options**, choose **Specify CPU options**\.

1. For **Core count**, choose the number of required CPU cores\. In this example, to specify the default CPU core count for an `r4.4xlarge` instance, choose `8`\.

1. To disable multithreading, for **Threads per core**, choose **1**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\.

**To disable multithreading during instance launch \(AWS CLI\)**  
Use the [run\-instances](https://docs.aws.amazon.com/cli/latest/reference/ec2/run-instances.html) AWS CLI command and specify a value of `1` for `ThreadsPerCore` for the `--cpu-options` parameter\. For `CoreCount`, specify the number of CPU cores\. In this example, to specify the default CPU core count for an `r4.4xlarge` instance, specify a value of `8`\.

```
aws ec2 run-instances --image-id ami-1a2b3c4d --instance-type r4.4xlarge --cpu-options "CoreCount=8,ThreadsPerCore=1" --key-name MyKeyPair
```

## Specify a custom number of vCPUs<a name="cpu-options-customer-number-of-vCPUs"></a>

You can customize the number of CPU cores and threads per core for the instance\.

**To specify a custom number of vCPUs during instance launch \(console\)**

The following example launches an `r4.4xlarge` instance with six vCPUs\.

1. Follow the [Launch an instance using the Launch Instance Wizard](launching-instance.md) procedure\.

1. On the **Configure Instance Details** page, for **CPU options**, choose **Specify CPU options**\.

1. To get six vCPUs, specify three CPU cores and two threads per core, as follows:
   + For **Core count**, choose **3**\.
   + For **Threads per core**, choose **2**\.

1. Continue as prompted by the wizard\. When you've finished reviewing your options on the **Review Instance Launch** page, choose **Launch**\. For more information, see [Launch an instance using the Launch Instance Wizard](launching-instance.md)\.

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
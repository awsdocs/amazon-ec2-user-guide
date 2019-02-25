# Amazon Elastic Inference<a name="elastic-inference"></a>

Amazon Elastic Inference \(EI\) is a resource you can attach to your Amazon EC2 instances to accelerate your deep learning \(DL\) inference workloads\. Amazon EI accelerators come in multiple sizes and are a cost\-effective method to build intelligent capabilities into applications running on Amazon EC2 instances\. 

Amazon EI accelerates operations defined by TensorFlow, Apache MXNet, and the Open Neural Network Exchange \(ONNX\) format on low\-cost, GPU\-based, DL inference accelerators\. Developers building a wide range of applications on Amazon EC2 instances with machine learning inference workloads can benefit from wider deployment through the reduction in cost that Amazon EI enables\. 

**Topics**
+ [Amazon EI Basics](#elastic-inference-basics)
+ [Working with Amazon EI](working-with-ei.md)
+ [Using CloudWatch Metrics to Monitor Amazon EI](cloudwatch-metrics-ei.md)
+ [Troubleshooting](ei-troubleshooting.md)

## Amazon EI Basics<a name="elastic-inference-basics"></a>

When you configure an Amazon EC2 instance to launch with an Amazon EI accelerator, AWS finds available accelerator capacity and establishes a network connection between your instance and the accelerator\.

Amazon EI accelerators are available to all EC2 instance types\.

The following Amazon EI accelerator types are available\. You can attach any Amazon EI accelerator type to any instance type\.


| Accelerator Type | FP32 Throughput \(TFLOPS\) | FP16 Throughput \(TFLOPS\) | Memory \(GB\) | 
| --- | --- | --- | --- | 
| eia1\.medium | 1 | 8 | 1 | 
| eia1\.large | 2 | 16 | 2 | 
| eia1\.xlarge | 4 | 32 | 4 | 

An Amazon EI accelerator is not part of the hardware that makes up your instance\. Instead, the accelerator is attached through the network using an AWS PrivateLink endpoint service\. The endpoint service routes traffic from your instance to the Amazon EI accelerator configured with your instance\.

Before you launch an instance with an Amazon EI accelerator, you must create an AWS PrivateLink endpoint service\. Each Availability Zone requires only a single endpoint service to connect instances with Amazon EI accelerators\. For more information, see [ VPC Endpoint Services \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/userguide/endpoint-service.html)\.

![\[An Amazon EI accelerator attached to an EC2 instance.\]](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/images/elastic-inference.png)

You can use Amazon Elastic Inference enabled TensorFlow, TensorFlow Serving, or Apache MXNet libraries to load models and make inference calls\. The modified versions of these libraries automatically detect the presence of Amazon EI accelerators, optimally distribute the model operations between the Amazon EI accelerator and the CPU of the instance, and securely control access to your accelerators using IAM policies\. The [AWS Deep Learning AMIs](https://aws.amazon.com//blogs/machine-learning/get-started-with-deep-learning-using-the-aws-deep-learning-ami) include the latest releases of Amazon Elastic Inference enabled TensorFlow Serving and MXNet\. If you are using custom AMIs or container images, you can download and install the required [Amazon Elastic Inference TensorFlow Serving](https://s3.console.aws.amazon.com/s3/buckets/amazonei-tensorflow/) and [Amazon Elastic Inference Apache MXNet](https://s3.console.aws.amazon.com/s3/buckets/amazonei-apachemxnet/) libraries from Amazon S3\. 

**Note**  
An Amazon EI accelerator is not visible or accessible through the device manager of your instance\.

The Amazon EI accelerator network traffic uses the HTTPS protocol \(TCP port 443\)\. Ensure that the security group for your instance and for your AWS PrivateLink endpoint service allows for this\. For more information, see [Configuring Your Security Groups for Amazon EI](setting-up-ei.md#ei-security)\. 

### Pricing for Amazon EI<a name="elastic-inference-pricing"></a>

You are charged for each second that an Amazon EI accelerator is attached to an instance in the `running` state\. You are not charged for an accelerator attached to an instance that is in the `pending`, `stopping`, `stopped`, `shutting-down`, or `terminated` state\. You are also not charged when an Amazon EI accelerator is in the `unknown` or `impaired` state\.

You do not incur AWS PrivateLink charges for VPC endpoints to the Amazon EI service when you have accelerators provisioned in the subnet\.

For more information about pricing by region for Amazon EI, see [ Amazon EI Pricing](https://aws.amazon.com//machine-learning/elastic-inference/pricing)\.

### Amazon EI Considerations<a name="elastic-inference-limitations"></a>

Before you start using Amazon EI accelerators, be aware of the following limitations:
+ You can attach one Amazon EI accelerator to an instance at a time, and only during instance launch\.
+ You cannot share an Amazon EI accelerator between instances\.
+ You cannot detach an Amazon EI accelerator from an instance or transfer it to another instance\. If you no longer require an Amazon EI accelerator, you must terminate your instance\. To change the Amazon EI accelerator type, create an AMI from your instance, terminate the instance, and launch a new instance with a different Amazon EI accelerator specification\. 
+ Currently, only the Amazon Elastic Inference enhanced MXNet and Amazon Elastic Inference enhanced TensorFlow Serving libraries can make inference calls to Amazon EI accelerators\. 
+ Amazon EI accelerators can only be attached to instances in a VPC\.
+ Pricing for Amazon EI accelerators is available at On\-Demand rates only\. You can attach an accelerator to a Reserved Instance, Scheduled Reserved Instance, or Spot Instance\. However, the On\-Demand price for the Amazon EI accelerator applies\. You cannot reserve or schedule Amazon EI accelerator capacity\. 

### Choosing an Instance and Accelerator Type for Your Model<a name="choosing-types-eia"></a>

Demands on CPU compute resources, CPU memory, GPU\-based acceleration, and GPU memory vary significantly between different types of deep learning models\. The latency and throughput requirements of the application also determine the amount of instance compute and Amazon EI acceleration you need\. Consider the following when you choose an instance and accelerator type combination for your model:
+ Before you evaluate the right combination of resources for your model or application, you should determine the target latency and throughput needs for your overall application stack, as well as any constraints you may have\. For example, if your application needs to respond within 300 milliseconds \(ms\), and data retrieval \(including any authentication\) and pre\-processing takes 200ms, you have a 100ms window to work with for the inference request\. Using this analysis, you can determine the lowest cost infrastructure combination that meets these targets\. 
+ Start with a reasonably small combination of resources\. For example, a `c5.xlarge` instance type along with an `eia1.medium` accelerator type\. This combination has been tested to work well for various computer vision workloads \(including a large version of ResNet: ResNet\-200\) and give comparable or better performance than a `p2.xlarge` instance\. You can then size up on the instance or accelerator type depending your latency targets\. 
+ Since Amazon EI accelerators are attached over the network, input/output data transfer between instance and accelerator also adds to inferencing latency\. Using a larger size for either or both instance and accelerator may reduce data transfer time, and therefore reduce overall inferencing latency\.
+ If you load multiple models to your accelerator \(or, the same model from multiple application processes on the instance\), you may need a larger accelerator size for both the compute and memory needs on the accelerator\.
+ You can convert your model to mixed precision, which utilizes the higher FP16 TFLOPS of Amazon EI \(for a given size\), to provide lower latency and higher performance\. 

### Using Amazon Elastic Inference with EC2 Auto Scaling<a name="ei-autoscaling"></a>

When you create an Auto Scaling group, you can specify the information required to configure the Amazon EC2 instances, including Amazon EI accelerators\. To configure Auto Scaling instances with Amazon EI accelerators, you can specify a launch template with your instance configuration, along with the Amazon EI accelerator type\.
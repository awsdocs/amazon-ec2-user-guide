# Getting started with AWS Inferentia development<a name="inf-getting-started"></a>

Inf1 instances are designed to accelerate machine learning using AWS Inferentia, a custom AI/ML chip from Amazon that provides high performance and low latency machine learning inference\. These instances are optimized for deploying Deep Learning \(DL\) models for applications, such as natural language processing, object detection and classification, content personalization and filtering, and speech recognition\.

**Getting started**

There are a variety of ways that you can get started\.
+ Use Amazon SageMaker, a fully\-managed service that is the easiest way to get started with machine learning models\. For more information, see [Compile and deploy a TensorFlow model on Inf1 instances](https://github.com/awslabs/amazon-sagemaker-examples/blob/master/sagemaker_neo_compilation_jobs/deploy_tensorflow_model_on_Inf1_instance/tensorflow_distributed_mnist_neo_inf1.ipynb) on github\.
+ Launch an Inf1 instance using the Deep Learning AMI\. For more information, see [AWS Inferentia with DLAMI](https://docs.aws.amazon.com/dlami/latest/devguide/tutorial-inferentia.html) in the *AWS Deep Learning AMI Developer Guide*\.
+ Launch an Inf1 instance using your own AMI and install the [AWS Neuron SDK](https://github.com/aws/aws-neuron-sdk), which enables you to compile, run, and profile deep learning models for AWS Inferentia\.
+ Launch a container instance using an Inf1 instance and an Amazon ECS\-optimized AMI\. For more information, see [Amazon Linux 2 \(Inferentia\) AMIs](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) in the *Amazon Elastic Container Service Developer Guide*\.
+ Create an Amazon EKS cluster with nodes running Inf1 instances\. For more information, see [Inferentia support](https://docs.aws.amazon.com/eks/latest/userguide/inferentia-support.html) in the **Amazon EKS User Guide**\.

**Learn more**
+ [AWS Inferentia](http://aws.amazon.com/machine-learning/inferentia/)
+ [AWS Neuron](http://aws.amazon.com/machine-learning/neuron/)
+ [Machine Learning on AWS](http://aws.amazon.com/machine-learning/)